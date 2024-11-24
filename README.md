# Filebrowser for cloud

### How to install
```
$ wget https://github.com/filebrowser/filebrowser/releases/download/v2.23.0/linux-amd64-filebrowser.tar.gz
$ tar xvfz linux-amd64-filebrowser.tar.gz
$ sudo mv filebrowser /usr/local/bin/
$ sudo mkdir -p /etc/filebrowser
$ sudo chown -R <username> /etc/filebrowser
$ sudo nano /etc/filebrowser/filebrowser.json
{
  "port": 8080,
  "baseURL": "",
  "address": "127.0.0.1",
  "log": "stdout",
  "database": "/etc/filebrowser/filebrowser.db",
  "root": "/path/to/your/cloud/storage",
  "auth": {
    "method": "basicauth",
    "header": "Authorization",
    "username": "your_username",
    "password": "your_password"
  }
}
$ sudo nano /etc/nginx/sites-available/filebrowser
server {
    listen 80;
    server_name your_domain.com;

    location / {
        proxy_pass http://127.0.0.1:8080;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
$ sudo ln -s /etc/nginx/sites-available/filebrowser /etc/nginx/sites-enabled/
$ sudo nginx -t
$ sudo systemctl restart nginx
$ sudo nano /etc/systemd/system/filebrowser.service
[Unit]
Description=FileBrowser
After=network.target

[Service]
ExecStart=/usr/local/bin/filebrowser -c /etc/filebrowser/filebrowser.json
Restart=on-failure
User=root

[Install]
WantedBy=multi-user.target
$ sudo systemctl daemon-reload
$ sudo systemctl start filebrowser
$ sudo systemctl enable filebrowser
$ sudo ufw allow 8181
$ sudo systemctl restart nginx

$ sudo nano /etc/nginx/sites-available/domain
server {
    listen 80;
    listen [::]:80;
    server_name cloud.solusi-rnd.tech;

    # Konfigurasi untuk mengizinkan upload folder
    # client_max_body_size 1000m;
    client_body_temp_path /var/www/tmp;
    proxy_request_buffering off;
    proxy_buffering off;

    # Konfigurasi untuk membatasi ukuran file maksimum
    client_body_timeout 120s;
    client_header_timeout 120s;
    send_timeout 120s;
    client_max_body_size 1000m;

    location / {
        proxy_pass http://159.223.61.133:8181;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
    }
}
```
