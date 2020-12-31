# Build ARM based K3s cluster

The cluster is based on Rasberry Pi 4 and k3s.

- Cluster: Rasberry Pi 4 2GB as master node, Rasberry Pi 4 4GB as agent node
- OS: Ubuntu Server 20.04 LTS (RPi 3/4) 64bit

1) Use Rasberry Pi Imager to prepare your sd card

2) Connect Raspberry Pi with ethernet cable and boot

3) Find Rasberry Pi IP. You can use nmap or fing: > <https://www.fing.com/products/fing-desktop>

4) Each node must have a unique **hostname**

## 1) Prepare Rasberry_Pi Wi-Fi

```git clone https://github.com/J0hn-B/raspberry_pi_k3s.git```

```cd rasberry_pi_k3s/ansible/ubuntu_server_settings/templates```

- Create an wifi_pass.txt with the WiFi information:

```touch wifi_pass.txt```

- copy paste the code inside wifi_pass.txt and update the wifi SSID_NAME and SSID_PASSWORD with your values
- A wifi_file.sample is provided for standard indentation.

```bash
## This file is generated from information provided by the datasource.  Changes
## to it will not persist across an instance reboot.  To disable cloud-init's
## network configuration capabilities, write a file
## /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg with the following:
## network: {config: disabled}

network:
  version: 2
  renderer: networkd
  ethernets:
    eth0:
      dhcp4: yes
  wifis:
    wlan0:
      dhcp4: true
      dhcp6: true
      optional: true
      access-points: 
        "SSID_NAME":
          password: "SSID_PASSWORD"

```

- update the ip in the hosts.ini file with your **ethernet ip**

- Run the play: ```ansible-playbook k3s_ubuntu_server.yml -i hosts.ini --ask-pass```

- wait for play to finish and remove the ethernet cable. Pi's will reboot and return the Wi-FI ip (wlan0)

- update the ip in the hosts.ini file with your **Wi-FI ip** and re-run the play *(for confirmation only)*


## 2) Install K3s 

```cd ../k3s-ansible/inventory/my-cluster```

- In ansible/k3s-ansible/inventory/my-cluster update ip's in hosts.ini and update group_vars.

- ```cd ../..```

- ```ansible-playbook site.yml -i inventory/my-cluster/hosts.ini --ask-pass```

- ```scp ubuntu@your_master_ip:~/.kube/config ~/.kube/config```

details in: <https://github.com/k3s-io/k3s-ansible>

note: From inside master node: ```sudo kubectl get nodes```

## Install cluster monitoring

- ```cd ../../k8s/cluster-monitoring/```
- update the vars.jsonnet values as described here <https://github.com/carlosedp/cluster-monitoring#quickstart-for-k3s>
- ```make vendor```
- ```make```
- ```kubectl apply -f manifests/setup/```
- ```kubectl apply -f manifests/```

- Access Grafana: <https://your_master_ip.nip.io>
- username: admin
- password: admin
- Go to Dashboards --> Manage --> Default and check --> Kubernetes cluster monitoring (via Prometheus)
