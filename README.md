# ansible-centos7-kubespray

Ansible playbook to configure prerequisites for Kubespray on CentOS7

```
$ ansible-playbook -i hosts.ini -u centos -k playbook.yml --extra-vars "ansible_sudo_pass=supersecret user=centos group=centos pubkeypath=~/.ssh/id_rsa.pub"
```
