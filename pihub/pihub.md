# Hardwario HUB for IoT Kit TOWER

1. Download [hardwario hio-raspbian-buster-lite](https://github.com/hardwario/bc-raspbian/releases) or use [Custom Setup on Raspberry Pi](https://tower.hardwario.com/en/latest/tutorials/custom-setup-on-raspberry-pi/)
2. Write img file using [balenaEtcher](https://www.balena.io/etcher/) or [Raspberry Pi Imager](https://www.raspberrypi.org/software/)
3. Boot raspberry (user: `pi`, password: `raspberry`)
4. Set static IP address in `/etc/dhcpcd.conf`
5. *Recommended:* Change default password
6. *Optional:* [Add SSH key-based authentication](ssh.md)

## Update/upgrade OS

1. Update
    ```
    sudo apt update
    ```
2. Upgrade 
    ```
    sudo apt upgrade
    ```

## Connect Mosquitto and InfluxDBB
From [Connect Mosquitto and InfluxDB](https://tower.hardwario.com/en/latest/integrations/grafana-for-visualization/#connect-mosquitto-and-influxdb)

1. Install MQTT to InfluxDB service
    ```
    sudo pip3 install --upgrade mqtt2influxdb
    ```
2. Create the `hardwario` directory
    ```
    sudo mkdir /etc/hardwario
    ```
3. Create the configuration file
    ```
    sudo nano /etc/hardwario/mqtt2influxdb.yml
    ```
4. Paste [this real snippet](mqtt2influxdb.yml) to the configuration file and replace influxdb host and database `demo` to CentOS IoT server address and database.
   
   > Example on https://tower.hardwario.com/en/latest/tutorials/mqtt-to-influxdb/ is not working. Snippet uses file from GitHub project https://github.com/hardwario/bch-mqtt2influxdb

5. Test the configuration file
    ```
    mqtt2influxdb -c /etc/hardwario/mqtt2influxdb.yml --test
    ```
6. Start the MQTT to InfluxDB service
    ```
    pm2 start `which python3` --name "mqtt2influxdb" -- `which mqtt2influxdb` -c /etc/hardwario/mqtt2influxdb.yml
    ```
7. Save the PM2 state (so it will start after reboot)
    ```
    pm2 save
    ```
