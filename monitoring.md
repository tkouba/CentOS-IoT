# Monitor server resources using Telegraf and InfluxDB

## Prepare InfluxDB database
First we need to create a database for storing Telegraf data.

Step 1: Start InfluxDB command line interface
```
influx
```

Step 1: Create a database, user and set privileges
```
CREATE DATABASE telegraf
CREATE USER teleuser WITH PASSWORD 'TeleP0d'
GRANT WRITE ON telegraf TO teleuser
```

Step 3: And then you can run this to show databases or users

```
SHOW DATABASES
SHOW USERS
SHOW GRANTS FOR teleuser
```

Step 4: Then just type `exit` to leave influx.
```
exit
```

That’s it InfluxDB is installed and ready to go your database is ready to go.

## Install Telegraf
Step 1: Install telegraf from package
```
sudo yum install telegraf
```
Telegraf service is enabledbut not started.

Step 2: Edit Telegraf configuration

```
sudo nano /etc/telegraf/telegraf.conf
```

Change `outputs.influxdb` to created InfluxDB database: 
```
[[outputs.influxdb]]
urls = ["http://127.0.0.1:8086"]
database = "telegraf"
skip_database_creation = true
## HTTP Basic Auth
username = "telegraf"
password = "TeleP0d"
```

Check or update required `inputs`.
```
###############################################################################
#                            INPUT PLUGINS                                    #
###############################################################################

# Read metrics about cpu usage
[[inputs.cpu]]
  ## Whether to report per-cpu stats or not
  percpu = true 
  ## Whether to report total system cpu stats or not
  totalcpu = true
  ## If true, collect raw CPU time metrics.
  collect_cpu_time = false
  ## If true, compute and report the sum of all non-idle CPU states.
  report_active = false

# Read metrics about disk usage by mount point
[[inputs.disk]]
  ## By default stats will be gathered for all mount points.
  ## Set mount_points will restrict the stats to only the specified mount points.
  # mount_points = ["/"]

  ## Ignore mount points by filesystem type.
  ignore_fs = ["tmpfs", "devtmpfs", "devfs", "iso9660", "overlay", "aufs", "squashfs"]


# # Read InfluxDB-formatted JSON metrics from one or more HTTP endpoints
[[inputs.influxdb]]
#   ## Works with InfluxDB debug endpoints out of the box,
#   ## but other services can use this format too.
#   ## See the influxdb plugin's README for more details.
#
#   ## Multiple URLs from which to read InfluxDB-formatted JSON
#   ## Default is "http://localhost:8086/debug/vars".
    urls = ["http://localhost:8086/debug/vars"]

#   ## http request & header timeout
    timeout = "5s"
```



## Grafana




## Links
 * [Guide for Setting up Telegraf to Monitor InfluxDB](https://tracyboggiano.com/archive/2018/04/guide-for-setting-up-telegraf-to-monitor-influxdb/) 
 * [Guide for Set Up of Telegraf for Monitoring Windows](https://tracyboggiano.com/archive/2018/04/guide-for-set-up-of-telegraf-for-monitoring-windows/)
 * [Setting up Telegraf to Startup on Windows](https://tracyboggiano.com/archive/2018/03/setting-up-telegraf-to-startup-on-windows/)
 * [Metrics For Free: SQL Server Monitoring With Telegraf](https://36chambers.wordpress.com/2019/02/05/metrics-for-free-sql-server-monitoring-with-telegraf/)
 * [Guide for Set Up of Telegraf for Monitoring SQL Server xPlat](https://tracyboggiano.com/archive/2018/02/setup-of-telegraf/)
 * [https://github.com/tboggiano/grafana](https://github.com/tboggiano/grafana)