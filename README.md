# Loki---Promtail---Grafana

**Technologies:** AWS, Loki, Promtail, Grafana 

**What Loki is**: https://github.com/grafana/loki   https://grafana.com/docs/loki/latest/get-started/overview/

**Architecture:**


**Requirmets:** 
- EC2 Ubuntu


## **Loki**
**1. Create Grafana servers with open ports 3100, 3000, 22 and 9096. Go to https://github.com/grafana/loki and**

**2. Check ths istalation steps: https://grafana.com/docs/loki/latest/setup/install/local/?pg=oss-loki&plcmt=quick-links**

**3. Go to**
<br/>![image](https://github.com/jeti20/Loki---Promtail---Grafana/assets/61649661/63f7c54a-14cc-4a65-8a4b-ade2fbaca4a3)
<br/>![image](https://github.com/jeti20/Loki---Promtail---Grafana/assets/61649661/123570e2-bfec-4e13-8716-b01321550905)

```
sudo apt-get install unzip
curl -O -L "https://github.com/grafana/loki/releases/download/v2.9.4/loki-linux-amd64.zip"
# extract the binary
unzip "loki-linux-amd64.zip"
# make sure it is executable
$ chmod a+x "loki-linux-amd64"
```

You can visit http://YorIP:3100/metrics and you should see logs from this service

## **Promtail**

**4. Go again to release page  https://github.com/grafana/loki/releases/ find interestign u binary file with promtail**

![image](https://github.com/jeti20/Loki---Promtail---Grafana/assets/61649661/b65fe713-e034-4894-9b78-80f83f803d41)

copy link to this file and use wget to downoland this on each node 
```
sudo apt-get install unzip
wget https://github.com/grafana/loki/releases/download/v2.9.6/promtail-linux-amd64.zip
unzip promtail-linux-amd64.zip
```

Also from wget previous link https://raw.githubusercontent.com/grafana/loki/main/clients/cmd/promtail/promtail-local-config.yaml Use this command to downoland promtail config file

![image](https://github.com/jeti20/Loki---Promtail---Grafana/assets/61649661/d02b2c1c-901f-422d-a0da-33f616e6297b)

```
wget https://raw.githubusercontent.com/grafana/loki/main/clients/cmd/promtail/promtail-local-config.yaml
```

After all operetion this is how it should look liek ater ls -l
>br/>Node1 
![image](https://github.com/jeti20/Loki---Promtail---Grafana/assets/61649661/887fa4f2-e739-4dbc-bbd4-3b02631a6ed4)

<br/>Node2
![image](https://github.com/jeti20/Loki---Promtail---Grafana/assets/61649661/83bfeedb-abe0-45bf-b167-45979488aedd)

Go to node one and edit config file for promtail, change localhost to node ip 

```
vim promtail-local-config.yaml
```

![image](https://github.com/jeti20/Loki---Promtail---Grafana/assets/61649661/88b913cd-580c-4181-b9ab-8304ba8e29bb)

loki-local-config.yaml push everything from folder /var/log thats ends with "log"

go to node 2 and do the same thingvim 






.
.

..

.
.
.
.
.

..

..

.
.
.
.
.
.
.

.





















```
cd /usr/local/bin
```

downoland Loki, install unzip tool, unzip pacakge, make sure all file are executeable, create new file config-loki.yml
```
curl -O -L "https://github.com/grafana/loki/releases/download/v2.4.1/loki-linux-amd64.zip"
apt install unzip
unzip "loki-linux-amd64.zip"
chmod a+x "loki-linux-amd64"
sudo nano config-loki.yml
```

past it into config-loki.yml. Chceck if there are any changes since last updaet https://raw.githubusercontent.com/grafana/loki/master/cmd/loki/loki-local-config.yaml
```
auth_enabled: false

server:
  http_listen_port: 3100
  grpc_listen_port: 9096

common:
  path_prefix: /tmp/loki
  storage:
    filesystem:
      chunks_directory: /tmp/loki/chunks
      rules_directory: /tmp/loki/rules
  replication_factor: 1
  ring:
    kvstore:
      store: inmemory

schema_config:
  configs:
    - from: 2020-10-24
      store: boltdb-shipper
      object_store: filesystem
      schema: v11
      index:
        prefix: index_
        period: 24h

ruler:
  alertmanager_url: http://localhost:9093
```

Create sysetm user to run this 24/7 in background as a user
```
sudo useradd --system loki
```
Create a file called loki.service
```
sudo nano /etc/systemd/system/loki.service
```
Paste it into this file 
```
[Unit]
Description=Loki service. It will allows to run it as a serviec in background base on loki-config.yaml. It is uising user Loki which we created before
After=network.target

[Service]
Type=simple
User=loki
ExecStart=/usr/local/bin/loki-linux-amd64 -config.file /usr/local/bin/config-loki.yml

[Install]
WantedBy=multi-user.target
```

