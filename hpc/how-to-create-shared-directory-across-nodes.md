# How to create shared directory across nodes

Given 2 H20 nodes (Ubuntu), 10.10.50.54 and 10.10.50.55, creating a shared folder between 2 nodes.

### 1. Install NFS

Make one of nodes the NFS server, which hosts the shared directory physically.

```bash
# Install NFS server on chosen host node
sudo apt update 
sudo apt install nfs-kernel-server

# Install NFS client on all other nodes
sudo apt update
sudo apt isntall nfs-common
```

### 2. Create and Configure the shared directory

```bash
# On server node
sudo mkdir /mnt/share

sudo chown -R user:group /mnt/share
sudo chmod -R 755 /mnt/share
```

### 3. Export the shared directory

```bash
sudo vim /etc/exports
```

Add the lines to grant access to the shared directory from all client nodes

* `rw`: Allows read and write permissions.
* `sync`: Ensures changes are written to disk before they are acknowledged.
* `no_subtree_check`: Improves reliability by eliminating subtree checking.

```bash
/mnt/share 10.10.50.0/24(rw,sync,no_subtree_check)
```

After editing, export the shared directory&#x20;

```bash
sudo exportfs -a
sudo systemctl restart nfs-kernel-server
```

### 4.  Mount the shared directory on client nodes

On each client node, create a mount point and mount the shared directory, replace `server_ip` with the IP address of your NFS server.

```sh
sudo mkdir /path/to/local_mount_point
sudo mount server_ip:/path/to/shared_directory /path/to/local_mount_point
```

### 5. Automate the mount on boot

To ensure the shared directory is mounted automatically at boot, add it to the `/etc/fstab` file on each client:

```sh
server_ip:/path/to/shared_directory /path/to/local_mount_point nfs defaults 0 0
```
