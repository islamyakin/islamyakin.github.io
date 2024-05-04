---
layout: post
title: Build Freenginx on Ubuntu/Debian-Based Systems from Source Code
---

# Build Freenginx on Ubuntu/Debian-Based Systems from Source Code


Hi everyone, this is a tutorial on building Freenginx on Ubuntu-based systems. Before proceeding, you'll need to install some additional packages.
Prerequisites :
```bash
sudo apt install build-essential libpcre3-dev libssl-dev zlib1g-dev libgd-dev 
```


## Download and Extract Freenginx Source Code :
1. Visit the Freenginx download page: https://freenginx.org/en/download.html
2. Download the latest stable version (currently freenginx-1.26.0.tar.gz).
3. Extract the downloaded archive:

```bash cd /opt/
sudo wget https://freenginx.org/download/freenginx-1.26.0.tar.gz
sudo tar -xzvf freenginx-1.26.0.tar.gz
```
Configure Freenginx with Modules :
1. Navigate to the extracted directory:
```bash
cd /opt/freenginx-1.26.0/
```
2. Run the configure script, specifying installation paths and enabling desired modules:
```bash sudo ./configure \
  --prefix=/var/www/html \
  --sbin-path=/usr/sbin/nginx \
  --conf-path=/etc/nginx/nginx.conf \
  --http-log-path=/var/log/nginx/access.log \
  --error-log-path=/var/log/nginx/error.log \
  --with-pcre \
  --lock-path=/var/lock/nginx.lock  \
  --pid-path=/var/run/nginx.pid \
  --with-http_ssl_module \
  --with-http_image_filter_module=dynamic \
  --modules-path=/etc/nginx/modules \
  --with-http_v2_module \
  --with-stream=dynamic \
  --with-stream \
  --with-http_addition_module \
  --with-http_mp4_module \
  --with-stream_ssl_module \
  --with-stream_ssl_preread_module \
  --with-http_realip_module \
  --with-compat \
  --with-http_stub_status_module
```

- You can customize the installation paths using the provided options.
- Refer to the Freenginx documentation for details on available modules: [https://freenginx.org/en/docs/]

## Compile and Install Freenginx :
1. Compile the source code
```bash
sudo make
```
2. Install freenginx
```bash
sudo make install
```
Output:
The compilation and installation process will generate output on your terminal. You can review it for any errors or warnings.
Congratulations! You have successfully installed Freenginx.

Verify Installation:
Check the version using one of the following commands:
```bash
sudo nginx -v
sudo nginx -V
```
## Create a Systemd Service for Freenginx :
1. Create a new systemd service file using a text editor like nano:
```bash
sudo nano /lib/systemd/system/freenginx.service
```
2. Paste the following content into the file, replacing paths if needed:
```bash
[Unit]
Description=The Freenginx HTTP and reverse proxy server
After=syslog.target network-online.target remote-fs.target nss-lookup.target
Wants=network-online.target
        
[Service]
Type=forking
PIDFile=/var/run/nginx.pid
ExecStartPre=/usr/sbin/nginx -t
ExecStart=/usr/sbin/nginx
ExecReload=/usr/sbin/nginx -s reload
ExecStop=/bin/kill -s QUIT $MAINPID
PrivateTmp=true
        
[Install]
WantedBy=multi-user.target
This service file defines how systemd manages Freenginx.
ExecStartPre ensures the configuration is valid before starting.
```
3. Save and close the file (Ctrl+X, then Ctrl+Y).

## Enable and Start Freenginx :
Reload systemd to recognize the new service:
```bash
sudo systemctl daemon-reload
```
2. Enable Freenginx to start automatically at boot :
```bash
sudo systemctl enable freenginx.service
```
3. Start the Freenginx service :
```bash
sudo systemctl start freenginx.service
```
4. Check the status of Freenginx :
```bash
sudo systemctl status freenginx.service
```
Output :
This should show that Freenginx is running and active.Additional Notes:
You can configure Freenginx by editing the main configuration file:

```bash
sudo nano /etc/nginx/nginx.conf
```
I hope this continuation helps you complete the Freenginx installation process!

references :
https://freenginx.org/en/docs/

Cheers!
