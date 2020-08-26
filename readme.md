# CentOS IoT Server

Installation on CentOS 8 x64    

1. [InfluxDB as time series database backend](#influxdb)
2. [Grafana](#grafana)
3. [Node-RED](#node-red)
4. [Mosquitto MQTT broker](#mosquitto)

**Optional steps**

5. [Nginx as reverse proxy for Grafana and Node-RED](#nginx)
6. [Monitor server resources using Telegraf and InfluxDB](monitoring.md)


## Preparation and prerequisites
*NTP*


## InfluxDB
Compiled steps from https://docs.influxdata.com/influxdb/v1.8/introduction/install/ and 
from https://www.systemmen.com/database/influxdb/how-to-install-influxdb-in-centos-7-460.html.

Step 0: Sudo


Step 1: Create repo for yum installation
```
cat <<EOF | tee /etc/yum.repos.d/influxdb.repo
[influxdb]
name = InfluxDB Repository
baseurl = https://repos.influxdata.com/rhel/8/amd64/stable
enabled = 1
gpgcheck = 1
gpgkey = https://repos.influxdata.com/influxdb.key
EOF
```

Step 2: Installation
```
yum install influxdb
```

Step 3: Enable service
```
systemctl enable influxdb
```

Step 4: Start service
```
systemctl start influxdb
```

### Open firewall port for InfluxDB
InfluxDB uses 2 ports 8086 and 8088.

```
firewall-cmd --permanent --add-port=8086/tcp
```

```
firewall-cmd --permanent --add-port=8088/tcp
```

```
firewall-cmd --reload
```

### Create database
Start `influx` command line interface and use following commands

```
CREATE DATABASE sensor
```

```
SHOW DATABASES
```

```
CREATE USER admin WITH PASSWORD '<password>' WITH ALL PRIVILEGES
```

```
CREATE USER demo WITH PASSWORD 'demo'
```

```
GRANT ALL ON sensor TO demo
```

```
INSERT sensor,location=Empiria,room=1154,stand=* temperature=18.24
INSERT sensor,location=Empiria,room=1148,stand=1 temperature=18.25
INSERT sensor,location=Empiria,room=*,stand=* temperature=18.24
```


## Grafana
From https://grafana.com/docs/grafana/latest/installation/rpm/ and 
from https://www.howtoforge.com/tutorial/how-to-install-grafana-on-linux-servers/#step-install-grafana-on-centos- 

Step 0: Sudo


Step 1: Create repo for yum installation

```
cat <<EOF | tee /etc/yum.repos.d/grafana.repo
[grafana]
name = Grafana OSS  Repository
baseurl=https://packages.grafana.com/oss/rpm
repo_gpgcheck=1
enabled=1
gpgcheck=1
gpgkey=https://packages.grafana.com/gpg.key
sslverify=1
sslcacert=/etc/pki/tls/certs/ca-bundle.crt
EOF
```

Step 2: Installation
```
yum install grafana
```

Step 3: Enable service
```
systemctl enable grafana-server
```

Step 4: Start service
```
systemctl start grafana-server
```

### Open firewall port for Grafana
Grafana uses port 3000.

```
firewall-cmd --permanent --add-port=3000/tcp
```

```
firewall-cmd --reload
```

### Log in for the first time
1. Open your web browser and go to http://localhost:3000/. 3000 is the default HTTP port that Grafana listens to if you haven’t configured a different port.
2. On the login page, type `admin` for the username and password.
3. Change your password.

### Plugins

```
grafana-cli plugins install grafana-clock-panel
```


## Node-RED
Created from https://ketandesai.co.uk/os/install-node-red-on-centos-7/ and
from http://www.steves-internet-guide.com/installing-node-red/ 
and https://nodered.org/docs/getting-started/local


Step 0: Sudo

Step 1: Install Extra Repositories
```
yum install epel-release
```

Step 2: Install Node.JS and npm
```
yum install nodejs
```

Step 3: Install Node-Red
```
npm install -g node-red
```

Step 4: Add nodered service user
```
useradd -r --comment "Node-RED" --shell /bin/false nodered
```

Step 5: Create service description file
```
cat <<EOF | tee /lib/systemd/system/node-red.service
[Unit]
Description=Node-RED

[Service]
User=nodered
Group=nodered
ExecStart=/usr/bin/node $NODE_OPTIONS /lib/node_modules/node-red/red.js --userDir /home/nodered $NODE_RED_OPTIONS
WorkingDirectory=/home/nodered
KillMode=control-group
Restart=on-failure

[Install]
WantedBy=multi-user.target
Alias=node-red.service
EOF
```

Step 6: Enable service
```
systemctl enable node-red
```

Step 7: Start service
```
systemctl start node-red
```

### Open firewall port for Node-RED
Node-RED uses port 1880. 

```
firewall-cmd --permanent --add-port=1880/tcp
```

```
firewall-cmd --reload
```

### Securing Node-RED

https://nodered.org/docs/user-guide/runtime/securing-node-red#editor--admin-api-security

**Username/password based authentication**
To enable user authentication on the Editor and Admin API, uncomment the adminAuth property in your settings file:

```
adminAuth: {
    type: "credentials",
    users: [
        {
            username: "admin",
            password: "$2a$08$zZWtXTja0fB1pzD4sHCMyOCMYz2Z6dNbM6tl8sJogENOMcxWV9DN.",
            permissions: "*"
        },
        {
            username: "george",
            password: "$2b$08$wuAqPiKJlVN27eF5qJp.RuQYuy6ZYONW7a/UWYxDTtwKFCdB8F19y",
            permissions: "read"
        }
    ]
}
```

User `admin` who has permission to do everything within the editor and has a password of `password`. User `george` has read-only permission.

**Generating the password hash**
To generate a suitable password hash, you can use the node-red-admin command-line tool. 

Step1: Installation of the command-line tool.
```
npm install -g --unsafe-perm node-red-admin
```

Step 2: Generate password hash
```
node-red-admin hash-pw
```

### Node-RED plugins
For installation of Node-RED plugins it must use trick to impersonate
`npm` execution.

```
su
cd /home/nodered/
sudo -u nodered npm install <plugin name>
service node-red restart
```

## Mosquitto

Step 0: Sudo


Step 1: Install mosquitto

```
yum install mosquitto
```

Step 2: Create the directory where persistence db files will be stored, change owner to mosquitto:
```
mkdir /var/lib/mosquitto/
chown mosquitto:mosquitto /var/lib/mosquitto/ -R
```
Step 3: Create mosquitto user with password
```
mosquitto_passwd -c /etc/mosquitto/pwfile demo
```

Step 4: Edit config file:
```
nano /etc/mosquitto/mosquitto.conf
```

Edit or add following lines
```
persistence true
persistence_location /var/lib/mosquitto/
persistence_file mosquitto.db

allow_anonymous false
password_file /etc/mosquitto/pwfile
```

Step 5: Enable service
```
systemctl enable mosquitto.service
```

Step 6: Start service
```
systemctl start mosquitto.service
```

### Open firewall port for Mosquitto
Mosquitto deafult port 1883.

```
firewall-cmd --permanent --add-port=1883/tcp
```

```
firewall-cmd --reload
```

## Nginx
Install nginx as reverse proxy, so grafana and node-red should not require opened ports


Step 0: Sudo


Step 1: Install Nginx

```
yum install nginx
```

Step 2: Edit config file, add reverse proxies for services

```
nano /etc/nginx/nginx.conf
```
Add or edit the following lines
```
server {
    listen       80 default_server;
    listen       [::]:80 default_server;
    server_name  _;
    root         /usr/share/nginx/html;

    location /static/ {
        alias   /usr/share/nginx/html/;
    }

    location /nodered/ {
        proxy_pass              http://127.0.0.1:1880/;
        proxy_set_header        Host $http_host;
        proxy_set_header        X-Real-IP $remote_addr;
        proxy_set_header        X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header        X-Forwarded-Proto $scheme;
        proxy_http_version      1.1;
        proxy_set_header        Upgrade $http_upgrade;
        proxy_set_header        Connection "upgrade";
    }  

    location / {
        proxy_pass      http://127.0.0.1:3000/;
    }
```

Step 3: Enable service
```
systemctl enable nginx.service
```

Step 4: Start service
```
systemctl start nginx.service
```

### Remove "permission denied" for reverse proxy
If SELinux is enabled, change boolean setting (solves 502 gateway error):
```
setsebool -P httpd_can_network_connect 1
```

### Open firewall port for nginx
HTTP port 80.

```
firewall-cmd --permanent --add-port=80/tcp
```

```
firewall-cmd --reload
```

## Other

Remove cockpit (or not)
```
sudo systemctl stop cockpit
sudo systemctl disable cockpit
sudo yum remove cockpit
sudo firewall-cmd --permanent --remove-service=cockpit
sudo firewall-cmd --reload
```
