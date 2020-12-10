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

