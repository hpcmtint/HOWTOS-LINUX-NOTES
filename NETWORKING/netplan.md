
'''bash

cd  /etc/netplan/
vi 00-netplan-config-eth0.yaml

network:
  renderer: networkd
  ethernets:
    enp0s3:
      addresses:
        - 192.168.128.197/24
      nameservers:
        addresses: [1.1.1.1,8.8.8.8]
      routes:
        - to: default
          via: 192.168.128.1
  version: 2
  
netplan apply
'''
