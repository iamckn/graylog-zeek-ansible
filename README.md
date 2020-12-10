Have zeek set up (locally or on a remote server) - https://github.com/iamckn/zeek_pfring_ansible. Make sure filebeat is configured to ship the logs to the graylog server on port 5044.


# Prepare the server

Set the timezone

```bash
sudo dpkg-reconfigure tzdata
```

Set a static IP

```bash
sudo nano /etc/netplan/01-netcfg.yaml 
```

Example network config

```bash
network:
  version: 2
  renderer: networkd
  ethernets:
    eth0:
      dhcp4: no
      addresses: [192.168.8.180/24]
      gateway4: 192.168.8.1
      nameservers:
        addresses: [8.8.8.8,8.8.4.4]
```

Apply the network config

```bash
sudo netplan apply
```

# On the client

```bash
git clone https://github.com/iamckn/elk_ansible
cd elk_ansible
```

Edit the hosts file in that folder and fill in the IP field with the graylog server IP

# Install Prerequisites

```bash
ansible-playbook graylog_zeek.yml -u c -k -K -i hosts -t prerequisites
```

# Install Mongodb

```bash
ansible-playbook graylog_zeek.yml -u c -k -K -i hosts -t mongodb
```

# Install Elasticsearch

```bash
ansible-playbook graylog_zeek.yml -u c -k -K -i hosts -t elasticsearch
```


# Install Graylog

```bash
ansible-playbook graylog_zeek.yml -u c -k -K -i hosts -t graylog
```

# Install Everything

```bash
ansible-playbook graylog_zeek.yml -u c -k -K -i hosts
```

# Restore backup configs (zeek)

This expects zeek json logs shipped by filebeat on port 5044

```bash
mongo
show dbs
use graylog
show collections
exit

mongorestore --db graylog --collection inputs graylog/inputs.bson
mongorestore --db graylog --collection streams graylog/streams.bson
mongorestore --db graylog --collection streamrules graylog/streamrules.bson
mongorestore --db graylog --collection dashboards graylog/dashboards.bson
```

Set stream indexes to default index and restart graylog

```bash
sudo systemctl restart graylog-server
```

# Database backup

```bash
mongodump --db graylog --out graylog_backup
```
