# Loki---Promtail---Grafana

**Technologies:** AWS, Loki, Promtail, Grafana 

**What Loki is**: https://github.com/grafana/loki   https://grafana.com/docs/loki/latest/get-started/overview/


**1. Create 4 servers**
Server for Loki
Server as Node1
Server as Node2
Server for Grafana

**Loki Server**
Check ths istalation steps: https://grafana.com/docs/loki/latest/setup/install/local/?pg=oss-loki&plcmt=quick-links


```
sudo apt-get install unzip
curl -O -L "https://github.com/grafana/loki/releases/download/v2.9.4/loki-linux-amd64.zip"
unzip "loki-linux-amd64.zip"
chmod a+x "loki-linux-amd64"
wget https://raw.githubusercontent.com/grafana/loki/main/cmd/loki/loki-local-config.yaml
./loki-linux-amd64 -config.file=loki-local-config.yaml - służy do uruchomienia serwera Loki na systemach operacyjnych Linux z architekturą AMD64, korzystając z konfiguracji zawartej w pliku loki-local-config.yaml.
```

You can visit http://YorIP:3100/metrics and you should see logs from this service

## **Promtail Node1 Node2** 

To make Promail work we have to downoland binary file wihich runs protmail and the cofiguration which provides informations under what IP Loki Server is and there send data. 
<br/>First downoland the binary file Go again to release page https://github.com/grafana/loki/releases/ find iteresting  u binary file with promtail**

![image](https://github.com/jeti20/Loki---Promtail---Grafana/assets/61649661/b65fe713-e034-4894-9b78-80f83f803d41)

copy link to this file and use wget to downoland this on each node 
```
sudo apt update
sudo apt-get install unzip
wget https://github.com/grafana/loki/releases/download/v2.9.6/promtail-linux-amd64.zip
unzip promtail-linux-amd64.zip
```
Iformation
<br/>(Plik promtail-linux-amd64 to binarny plik wykonywalny programu Promtail zoptymalizowany dla systemów operacyjnych Linux z architekturą AMD64. Po uruchomieniu, Promtail rozpoczyna proces zbierania logów z określonych lokalizacji, ich przetwarzania zgodnie z konfiguracją oraz wysyłania ich do serwera Loki w celu dalszej analizy i przechowywania.)

Now lets downoland the configure file from instruction on Grafana website: https://grafana.com/docs/loki/latest/setup/install/local/?pg=oss-loki&plcmt=quick-links

![image](https://github.com/jeti20/Loki---Promtail---Grafana/assets/61649661/70e1d09e-82f1-4a28-b89f-7dfaedf11a88)


```
wget https://raw.githubusercontent.com/grafana/loki/main/clients/cmd/promtail/promtail-local-config.yaml
```

Now on each node you should have config file and unpacked binary file

![image](https://github.com/jeti20/Loki---Promtail---Grafana/assets/61649661/022972f4-8c6b-434f-9e0d-5b7ce49f645a)


Now on each node edit config file. Past there the IP of Servier with Loki.

```
vim promtail-local-config.yaml
```

![image](https://github.com/jeti20/Loki---Promtail---Grafana/assets/61649661/88b913cd-580c-4181-b9ab-8304ba8e29bb)

Information
<br/>loki-local-config.yaml push everything from folder /var/log thats ends with "log"

Remember that the server with Loki should be running the Loki binary file (last command from Loki section in Loki server).
<br/>To run protmile on each server run this command. This will run the binary file with configuration from promtail-local-config.yaml
```
./promtail-linux-amd64 -config.file=promtail-local-config.yaml
```

![image](https://github.com/jeti20/Loki---Promtail---Grafana/assets/61649661/b738843b-b0be-4ac8-87a7-705d06785e24)


**Grafana Server**
<br/>Check this repository: https://github.com/jeti20/Grafana-AWS/blob/main/README.md

In grafana add Loki as DataSource, type Loki's server IP address. Press "Save & Test" and now you should see "Data source successfully connected"

![image](https://github.com/jeti20/Loki---Promtail---Grafana/assets/61649661/f285f9af-7e1d-4676-a112-cd171e3c210b)

Go to explore and try to create dashboard. This is how LogQL looks like

![image](https://github.com/jeti20/Loki---Promtail---Grafana/assets/61649661/e4d26c28-8133-4a41-a0fd-6b45da0902bf)

![image](https://github.com/jeti20/Loki---Promtail---Grafana/assets/61649661/cfe35001-f387-4915-87a9-cdb832097d0e)

In LogQL you can search for logs with specific word. Just type words between "''" in query 

![image](https://github.com/jeti20/Loki---Promtail---Grafana/assets/61649661/dc080629-1d6c-485d-bc12-880892d9c6ec)

You can check the source of logs

![image](https://github.com/jeti20/Loki---Promtail---Grafana/assets/61649661/87ef101c-5bdf-48ad-bfdb-c59903d55a59)

See all logs from the one one specific log

![image](https://github.com/jeti20/Loki---Promtail---Grafana/assets/61649661/6777cd9c-df6e-49e2-aca8-f408ffce147e)

By chaning this symbol you search in multiple files

![image](https://github.com/jeti20/Loki---Promtail---Grafana/assets/61649661/c5a5c025-4bee-46ef-a942-20f2cda21a4d)

You can switch to "Code" and type words i query.


If you want to add file with logs to Loki which is not in the folder /var/logs defined in the promtail-local-config.yaml. You have to edit the promtail-local-config.yaml. Go into both nodes, the best option is to open them in split mode terminal for example in MobaXTerm. 
```
nano promtail-local-config.yaml
```
![image](https://github.com/jeti20/Loki---Promtail---Grafana/assets/61649661/75af20cc-7da7-4c16-ae62-ab1d422b8b3e)

You should be able to see new position in dropdown menu in grafana in jobs if you pointed valid log file

