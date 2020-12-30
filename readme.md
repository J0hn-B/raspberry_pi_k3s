# Building IoT ARM based K3s cluster

The project is based on Rasberry Pi 4 and Kubernetes.

## 1) Prepare Rasberry_Pi  [ubuntu_server_settings]

```cd ~/temp/rasberry_pi_k3s/ansible/ubuntu_server_settings```

- Create an ubuntu_pass.txt with the WiFi information:

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

then run the play: ```ansible-playbook k3s_ubuntu_server.yml -i hosts.ini --ask-pass```

## 2) Install K3s in 3 steps

```cd ~/temp/rasberry_pi_k3s/ansible/k3s-ansible```

- In ansible/k3s-ansible/inventory/my-cluster modify hosts.ini and update group_vars.

- ```ansible-playbook site.yml -i inventory/my-cluster/hosts.ini --ask-pass```

- ```scp ubuntu@master_ip:~/.kube/config ~/.kube/config```

details in: <https://github.com/k3s-io/k3s-ansible>

note: From inside Rasberry_Pi: ```sudo kubectl get nodes```

## Install Monitoring

- Go to: <https://github.com/carlosedp/cluster-monitoring> and follow the instructions

- ```cd ~/temp/rasberry_pi/k8s/cluster-monitoring```

- ```kubectl apply -f manifests/setup/```
- ```kubectl apply -f manifests/```

- Access Grafana: <https://grafana.192.168.50.38.nip.io>
