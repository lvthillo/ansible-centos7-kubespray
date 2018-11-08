# ansible-centos7-kubespray

Ansible playbook to configure prerequisites for Kubespray on CentOS7

Prerequisites for the CentOS7 machines:
* SSH access using password with a user who has root permissions (centos in example)
* A public key generated on your local machine

This playbook will:
* Update packages
* Install network tools
* Install and configure ntpd
* disable firewall
* disable swap
* enable passwordless sudo
* enable passwordless SSH
* set hostname

Add the IP's of your CentOS7 servers in the `hosts.ini`.

```
[all]
node1 ansible_host=192.168.140.101
node2 ansible_host=192.168.140.102
node3 ansible_host=192.168.140.103
```

Start the playbook. Define variables like root password, user, group and path to the public key.

```
$ ansible-playbook -i hosts.ini -u centos -k playbook.yml --extra-vars "ansible_sudo_pass=supersecret  user=centos group=centos pubkeypath=~/.ssh/id_rsa.pub"
```

After running the playbook you can start with [Kubespray](https://github.com/kubernetes-incubator/kubespray):
```
$ git clone https://github.com/kubernetes-incubator/kubespray
$ cd kubespray
$ git checkout v2.7.0
```

Follow the instructions described in the README.md

```
# Install dependencies from ``requirements.txt``
$ sudo pip install -r requirements.txt

# Copy inventory/sample as inventory/mycluster
$ cp -rfp inventory/sample inventory/mycluster

# Update Ansible inventory file with inventory builder
$ declare -a IPS=(192.168.140.101 192.168.140.102 192.168.140.103)
$ CONFIG_FILE=inventory/mycluster/hosts.ini python3 contrib/inventory_builder/inventory.py ${IPS[@]}

# Review and change parameters under inventory/mycluster/group_var
$ cat inventory/mycluster/group_vars/all.yml
$ cat inventory/mycluster/group_vars/k8s-cluster.yml
# Optional: modify the hosts.ini file to your needs.
$ vi inventory/mycluster/hosts.ini
[all]
node1   ansible_host=192.168.140.101 ip=192.168.140.101
node2   ansible_host=192.168.140.102 ip=192.168.140.102
node3   ansible_host=192.168.140.103 ip=192.168.140.103
# possible to add additional masters
[kube-master]
node1
[kube-node]
node2
node3
# possible to add additional etcd's
[etcd]
node1
[k8s-cluster:children]
kube-node
kube-master
[calico-rr]
[vault]
node1
node2
node3
```

Deploy Kubespray with Ansible Playbook.
```
$ ansible-playbook -u centos -b -i inventory/mycluster/hosts.ini cluster.yml
```

More details and configuration can be found [here](https://medium.com/@lvthillo/install-kubernetes-on-bare-metal-centos7-fba40e9bb3de).
