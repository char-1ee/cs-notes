# How to deploy log shipper in K8S

In normal case, we can write direct application level logic that sends logs to the logging backend, e.g. connect to Kafka cluster via gRPC. To collect logs from services deployed in K8S environment, we have alternatives including:

1. Log agent as DaemonSet
2. Log shipper in Sidecar pattern

### DaemonSet pattern

<figure><img src="../.gitbook/assets/image (1) (1) (1).png" alt=""><figcaption><p>DaemonSet logging</p></figcaption></figure>

#### Container Runtime -> Node File System

1. Container Log File (stdout/stderr):
   * When containers write to stdout or stderr, these logs are automatically captured by the container runtime (like Docker or containerd).
   * The container runtime redirects these logs to JSON files on the host node's filesystem.
   * By default, Docker stores these logs in `/var/lib/docker/containers/<container-id>/<container-id>-json.log`
   * Each container gets its own log file identified by the container ID
   * These files contain not just the raw logs but also metadata like timestamps and stream type (stdout/stderr).
2. Volume Mount:
   * When you create a volume mount in Kubernetes, it creates a bidirectional binding between: 1) A directory inside the container and 2) A corresponding directory on the host node's filesystem.
   * When the application writes log files to the mounted volume inside the container, it's actually writing directly to the node's filesystem through this mount.
   * The files appear both inside the container and on the host node simultaneously due to this mounting mechanism.

#### Node File System -> Log Agent

The Log Agent uses two main mechanisms to efficiently monitor and collect logs:

1. `tail`:
   * Similar to the Linux `tail -f` command
   * The agent keeps track of the last position it read in each file
   * It continuously reads new data appended to the end of the files
   * This ensures it doesn't re-read the entire file each time
   * Particularly useful for active log files that are constantly being written to
2. `inotify`:
   * This is a Linux kernel subsystem that provides file system event monitoring
   * The Log Agent subscribes to inotify events for the directories it's monitoring
   * It gets immediate notifications when:
     1. New log files are created
     2. Existing files are modified
     3. Files are deleted or rotated
   * This is more efficient than polling because the agent only acts when there are actual changes

The typical collection process works like this:

1. The Log Agent starts up and configures watchers for specific paths:
   * `/var/lib/docker/containers/...` for container stdout/stderr logs
   * The configured volume paths for application-specific logs
   * `/var/log/...` for system logs
2. When new data is written to any monitored file, inotify alerts the agent
3. The agent uses tail to efficiently read only the new data
4. The collected logs then go through the processing pipeline (parsing, enrichment, buffering) before being forwarded

### Sidecar pattern

* Deploy a logging container alongside the application container in the same pod.
* Read logs from a shared volume where the app write logs
* Stream stdout/stderr from the application container and forward the logs to logging backend.

<figure><img src="../.gitbook/assets/image (2).png" alt=""><figcaption><p>Sidecar pattern</p></figcaption></figure>

### Use cases

Sidecar is better when:

* Applications need specialized log processing
* You want stronger isolation between applications
* You need to ensure logs from critical applications are collected regardless of node-level issues

DaemonSet is better when:

* You want centralized logging configuration
* Resource efficiency is important (fewer total agents)
* You need to collect node-level logs as well

A hybrid design:

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption><p>Hybrid design</p></figcaption></figure>
