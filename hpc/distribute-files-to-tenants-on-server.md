# Distribute files to tenants on server

Case: on a jump server node, users can access production k8s cluster via kubectl and customized cli tool tcectl. For new users, when they ssh into the jump server and download kubectl and tcectl via

```bash
wget http://127.0.0.1:8000/tcectl
wget http://127.0.0.1:8000/kubectl
```

This actually is to access services (file downloading) via a local HTTP server.&#x20;

We can do so by starting a HTTP server

<pre class="language-bash"><code class="lang-bash"><strong>mkdir shared_files
</strong><strong>cd shared_files # where put shared files
</strong><strong>
</strong><strong># Starting the http server
</strong><strong>python3 -m http.server 8000
</strong>
# Or using node.js
npx http-server -p 8000

# Others can access using 
wget http://127.0.0.1:8000/filename
</code></pre>

If want to run the http server running consistently, we can use `screeen` or `tmux`, or using `nohup`&#x20;

```bash
nohup python3 -m http.server 8000 &
```

best way is to use systemd, for automatically starts on system boot and can handle crashes by restarting the service:

```bash
sudo vim /etc/systemd/system/file-server/service

[Unit]
Description=File Server
After=network.target

[Service]
Type=simple
User=your_username
WorkingDirectory=/path/to/files_to_share
ExecStart=/usr/bin/python3 -m http.server 8000
Restart=always

[Install]
WantedBy=multi-user.target

# Enable and start the service
sudo systemctl enable file-server
sudo systemctl start file-server

# Check status
sudo systemctl status file-server
```
