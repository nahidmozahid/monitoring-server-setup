Install Prometheus and Grafana on Ubuntu 22.04 LTS:

>> What is Prometheus ?
 Prometheus is a open source Linux Server Monitoring tool mainly used for metrics monitoring, event monitoring, alert management, etc.

>> What is Grafana ?
Grafana is a free and open source visualization tool mostly used with Prometheus to which monitor metrics.

>> What is Node Exporter ?
Node exporter is one of the  Prometheus exporters which is used to expose servers or system OS metrics.


##Prerequisites##

>> Ubuntu with 22.04 Version

>> Root user account with sudo  privilege

>>  Prometheus system user and group

>> Ports Required- 9090 (Prometheus), 3000 (Grafana), 9100 (Node Exporter)


##Let’s do some hands-on practice##

@@@Prometheus Installation@@@

1.First, update the system repository index
>> sudo apt update

2.Create a system user for Prometheus using below commnds:
>> sudo useradd --no-create-home --shell /bin/false prometheus

3. Create the directories which we will be storing our configuration files and libraries:
>> sudo mkdir /etc/prometheus
   sudo mkdir /var/lib/prometheus

4. Set the ownership of the /var/lib/prometheus directory:
>> sudo chown prometheus:prometheus /var/lib/prometheus

go to tmp directory >> cd /tmp/

5. Download the Prometheus setup using wget:
>> wget https://github.com/prometheus/prometheus/releases/download/v2.46.0/prometheus-2.46.0.linux-amd64.tar.gz

6. Extract the files using tar :
>> tar -xvf prometheus-2.46.0.linux-amd64.tar.gz

7. Move the configuration file and set the owner to the prometheus user:
>> cd prometheus-2.46.0.linux-amd64
   sudo mv console* /etc/prometheus
   sudo mv prometheus.yml /etc/prometheus
   sudo chown -R prometheus:prometheus /etc/prometheus

8. Move the binaries and set the owner:
>> sudo mv prometheus /usr/local/bin/
   sudo chown prometheus:prometheus /usr/local/bin/prometheus

9. You can find prometheus configurations file in below directory:
>> cd /etc/prometheus/prometheus.yml

10. Create prometheus service file using below command:
>>  sudo nano /etc/systemd/system/prometheus.service
add below lines:

>>

[Unit]

Description=Prometheus

Wants=network-online.target

After=network-online.target

[Service]

User=prometheus

Group=prometheus

Type=simple

ExecStart=/usr/local/bin/prometheus \
    --config.file /etc/prometheus/prometheus.yml \
    --storage.tsdb.path /var/lib/prometheus/ \
    --web.console.templates=/etc/prometheus/consoles \
    --web.console.libraries=/etc/prometheus/console_libraries

[Install]

WantedBy=multi-user.target

11. Reload systemd:
>> sudo systemctl daemon-reload

12. Start, Enable, Status Prometheus service:
>> sudo systemctl start prometheus
   sudo systemctl enable prometheus
   sudo systemctl status prometheus

13. Use below command to enable prometheus service in firewall:
>>  ufw allow 9090/tcp

14. Now Prometheus service is ready to run and we can access it from any web browser:
>> http://server-IP-or-Hostname:9090.

@@@Download Node Exporter@@@

1. cd /tmp/

2. run the below URL with wget command:
>> wget https://github.com/prometheus/node_exporter/releases/download/v1.6.1/node_exporter-1.6.1.linux-amd64.tar.gz

3. Unzip the downloaded the file:
   >> sudo tar xvfz node_exporter-*.*-amd64.tar.gz

4. Move the binary file of node exporter to /usr/local/bin location
   >> sudo mv node_exporter-*.*-amd64/node_exporter /usr/local/bin/

5. Create a node_exporter user to run the node exporter service:
   >> sudo useradd -rs /bin/false node_exporter

6. Create a node_exporter service file in the /etc/systemd/system directory:
   >> sudo nano /etc/systemd/system/node_exporter.service

7. Add below lines:
   >>
   [Unit]
   Description=Node Exporter
   After=network.target

   [Service]
   User=node_exporter
   Group=node_exporter
   Type=simple
   ExecStart=/usr/local/bin/node_exporter

   [Install]
   WantedBy=multi-user.target

8. start and enable the node_exporter service:
   >> sudo systemctl daemon-reload
      sudo systemctl start node_exporter
      sudo systemctl enable node_exporter
      sudo systemctl status node_exporter

9. Now Configure the Node Exporter as a Prometheus target

10. go to etc/prometheus and open prometheus.yml:
    >> sudo nano /etc/prometheus/prometheus.yml
11. Add this in buttom of the lines:
    >>
    - job_name: 'Node_Exporter'

    scrape_interval: 5s

    static_configs:

      - targets: ['<Server_IP>:9100', '<Server_IP>:9100', as much as we want]

12. restart the Prometheus service:
    >> sudo systemctl restart prometheus

13. Now Hit the URL in web browser to check weather target is successfully scraped by Prometheus or not


@@@Install Grafana@@@

1. Add the Grafana GPG key:
   >> wget -q -O - https://packages.grafana.com/gpg.key | sudo apt-key add -
2. add the Grafana repository to the APT sources and then install grafana, start, enable grafana:
   >> sudo add-apt-repository "deb https://packages.grafana.com/oss/deb stable main"
   >> sudo apt install grafana
   >> sudo systemctl start grafana-server
   >> sudo systemctl status grafana-server
   >> sudo systemctl enable grafana-server

3. to access grafana dashboard:
   >> http://your_ip:3000

4. Username – admin || Password – admin

5. Configure  Prometheus as Grafana DataSource






