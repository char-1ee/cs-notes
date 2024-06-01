# SSH ProxyJump

Access server C directly from client A through jump server B using SSH, which involves setting up an SSH tunnel or using the ProxyJump feature. Here are the steps for both methods:

#### Method 1: Using ProxyJump

The `ProxyJump` directive (introduced in OpenSSH 7.3) simplifies the process of using a jump host. You can use the `-J` option directly in your SSH command:

```bash
ssh -J userB@B userC@C
```

This command tells SSH to first connect to user `userB` on jump server B and then from there to connect to user `userC` on host server C.

For more persistent settings, you can configure this in your `~/.ssh/config` file:

```plaintext
Host C
  HostName C
  User userC
  ProxyJump userB@B
```

After adding these configurations, you can connect to server C simply by running:

```bash
ssh C
```

#### Method 2: Using an SSH Tunnel

If you're using an older version of SSH that does not support `ProxyJump`, you can use an SSH tunnel instead. You would set up your SSH config file (`~/.ssh/config`) like this:

```plaintext
Host B
  HostName B
  User userB

Host C
  HostName C
  User userC
  ProxyCommand ssh -q -W %h:%p B
```

This configuration uses `ProxyCommand` to create a tunnel through B to reach C. You would simply use the command `ssh C` to connect directly to C, tunneling through B.

#### Keeping the Connection Alive

To prevent the SSH session from timing out, you can add these settings in your SSH config file under the respective host entries:

```plaintext
ServerAliveInterval 60
ServerAliveCountMax 2
```

These settings help keep the SSH connection alive by sending a packet from the client to the server every 60 seconds, and it will stop if it fails twice consecutively.

#### Security Considerations

When setting up SSH access through jump servers, ensure your keys and credentials are secure, and consider using key-based authentication with passphrase-protected private keys for added security. Additionally, keep your SSH client updated to benefit from security patches and newer features like `ProxyJump`.
