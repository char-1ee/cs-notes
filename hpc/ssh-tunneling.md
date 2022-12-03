---
description: (SSH port forwarding)
---

# SSH Tunneling

SSH tunneling creates a tunnel between 2 hosts and data from some other protocol is encapsulated in the SSH header and transferred as SSH data between these 2 hosts. Enabling secure data transfer over network

### SSH Tunnel (Local Port Forwarding)

SSH tunnel with local port forwarding forwards the data from the port on the client to the port on the server machine.

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

```bash
ssh -L 1234:localhost:5678 remoteuser@remotehost
```

In this command, “-L” refers to the local side listening. With the above command, all the data coming on “port 1234” of your machine will be encapsulated as SSH data and forwarded to “port 5678” of the remotehost. In the above example, “localhost” refers to the localhost IP of a remote machine.

```bash
ssh -L 1234:farawayhost_IP:5678 remoteuser@remotehost
```

In this kind of implementation, all the data coming on “port 1234” of your machine will be forwarded to the “port 5678” on the farawayhost machine by remotehost machine (given that farawayhost allows the remote host to send data for port 5678).

#### Use case

Local port forwarding is very useful **while accessing the services hosted on a private network.** Let’s take a scenario where your office network only allows SSH connections to the inside resources/machines, blocks all other traffic from outside networks, and you want to access the website restricted to your office network securely. In such a case, you can create an SSH tunnel to the machine inside your office network and access the restricted website:

```shell
ssh -L 8080:restricted.domain.com:80 remoteuser@remotehost
```

You can access the restricted website with the URL “**http://localhost:8080**” in your local machine browser. This is one of many use cases of local port forwarding. One other use case would be to access the database on the server securely without exposing the database ports.

### Reverse SSH Tunnel (Remote Port Forwarding)

Reverse SSH tunnel listens to the port on the remote machine and forwards all the data coming on that port to the specified port on your local machine via SSH.

<figure><img src="../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

```bash
ssh -R 1234:localhost:5678 remoteuser@remotehost
```

In this command, “-R” refers to the remote side listening and listens to the “port 1234” on the remote machine and forwards all the data from “port 1234” of the remote machine to the “port 5678” on your local machine.

```bash
ssh -R 1234:nearhost_IP:5678 remoteuser@remotehost
```

In this example, all the data from “port 1234” of the remote machine will be forwarded by your local machine to “port 5678” of the nearhost machine.

#### Use case:

Now let’s imagine a scenario where a developer is developing an application/website on its local machine and wants to let his/her client/team access that locally hosted website/app for a demo. Often, you do not have a public IP for your local machine (although you can create access to your local machine by configuring NAT rules, it doesn’t always work). You can create a reverse SSH tunnel to a publicly accessible server for “port 80” (or any custom port) and let the end-users (clients or your team members in this example) browse the website using the IP address of this public server on “port 80” (or any custom port that you define in the command) to access the website.

Before you consider implementing a reverse SSH tunnel, the parameter “GatewayPorts” should be set to “yes” in your remotehost’s SSH server configuration file “/etc/ssh/sshd\_config.”

### Dynamic SSH Tunnel (Dynamic Port Forwarding)

{% embed url="https://blogs.perficient.com/2021/08/10/how-to-understand-ssh-tunneling-and-its-use-cases/" %}

{% embed url="https://unix.stackexchange.com/questions/46235/how-does-reverse-ssh-tunneling-work" %}
