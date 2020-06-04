# CentOS IoT Server

Installation on CentOS 8 x64

1. InfluxDB
2. Grafana
3. Node-RED
4. Mosquito (future)

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
*Change file to `/lib/systemd/system/nodered.service` and `add systemctl enable`.*
```
cat <<EOF | tee /etc/systemd/system/node-red.service
** ToDo: Add file content
EOF
```

Step 6: Start service
```
systemctl start grafana-server
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