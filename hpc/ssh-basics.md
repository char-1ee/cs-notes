# SSH basics

## .ssh directory

### Client

*   `know_hosts`

    This file stores the host keys of the servers to which the client has connected previously. It is used to verify the identity of the server during future connections, preventing Man-in-the-Middle (MitM) attacks.
*   `config`

    This is the client-side configuration file where you can specify settings for SSH connections, such as hostnames, user names, port numbers, and other preferences for individual hosts or global settings.
*   `authorized_keys`

    Not typically used on the client side unless the machine also acts as a server where other clients might connect.

### Server

* `authorized_keys`:
  * This file contains the public keys that are authorized for SSH access. When a client attempts to connect to the server, the server checks if the presented key matches one of the keys listed in this file.
* `sshd_config`:
  * This is the configuration file for the SSH daemon (`sshd`). It contains settings that dictate how the SSH server behaves, including security settings, authentication methods allowed, port settings, and more.
* `ssh_host_rsa_key`, `ssh_host_dsa_key`, `ssh_host_ecdsa_key`, `ssh_host_ed25519_key`:
  * These are the server’s private host keys, each corresponding to different encryption algorithms. They are used to uniquely identify the server to clients.
* `ssh_host_rsa_key.pub`, `ssh_host_dsa_key.pub`, `ssh_host_ecdsa_key.pub`, `ssh_host_ed25519_key.pub`:
  * These are the public parts of the server's host keys. They are presented to the client during the SSH handshake process to establish a secure connection.

## ssh config

```bash
Host Server1
    Hostname 172.16.0.2
    User lixingjian
    Port 207
    ServerAliveInterval 190
    IdentityFile ~/.ssh/secret_key.pem
```

### Wildcard

If you have a bunch of nodes in a cluster:

```bash
Host Server*
    User lixingjian
    Port 207
    IdentityFile ~/.ssh/secret_key.pem
    
Host Server1
    Hostname 172.16.0.1

Host Server2
    Hostname 172.16.0.2

Host Server3
    Hostname 172.16.0.3
```

### Refer to other files

If want to include other/all config files under `~/.ssh/`

```bash
Include config-cluster-shanghai
Include config-*
```

## Passwordless authentication

```bash
# 1. generate ssh keys
ssh-keygen -t rsa -b 4096

# 2. Copy the key to the server
ssh-copy-id user@server-address

# 3. Test the server
ssh user@server-address

# 4. Disable password auth on server
sudo vim /etc/ssh/sshd_config

PasswordAuthentication no
ChallengeResponseAuthentication no
UsePAM no

# 5. Restart ssh service to apply
sudo systemctl restart sshd
```

Alternatively, you can directly copy the public key.

Log into server and append the public key to the \`\~/.ssh/authorized\_keys\`:

```bash
echo "ssh-rsa AAAAB3NzaC1yc2EAD... user@client" >> ~/.ssh/authorized_keys

# Give permission
chmod 700 ~/.ssh                 # accessible
chmod 600 ~/.ssh/authorized_keys # readable
```

## Batch operation

pssh: [https://tonydeng.github.io/2014/12/08/pssh/](https://tonydeng.github.io/2014/12/08/pssh/)

<pre class="language-bash"><code class="lang-bash"># Installation
$ python -m pip install pss

# Prepare iplist
$ vim iplist
Server1
Server2

# Batch operation
<strong>$ pssh -ih iplist echo Hello
</strong>[1] 14:27:50 [SUCCESS] Server1
Hello
[2] 14:27:50 [SUCCESS] Server2
Hello
</code></pre>
