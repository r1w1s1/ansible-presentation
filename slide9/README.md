
# Tipos de Inventários

```ini
Simples INI (/etc/ansible/hosts)
   [production]
    www.myapp.com
    www2.myapp.com

Dinâmico (script)
   ec2.py (aws)
   DigitalOcean
  Google Compute Engine
  Linode
  OpenShift
  OpenStack Nova
```

# /etc/ansible/hosts

```ini

mysql-master.myapp.com ansible_ssh_port=3522
redis.myapp.com ansible_python_interpreter=/usr/local/bin/python

[production]
www.myapp.com
www2.myapp.com

[production:vars]
http_port=80

[webservers]
www[01:50].myapp.com

[saopaulo]
www.myapp.com
www1.myapp.com

[riodejaneiro]
www2.myapp.com
www3.myapp.com

[saorio:children]
saopaulo
riodejaneiro

[saorio:vars]
foo=bar
http_port=80
ansible_ssh_user=apache

```

# Inventário Dinamico (ec2.py)

```ini

$ ansible -i ec2.py -m ping tag_Ansible_Slave
$ ansible -i ec2.py -u ubuntu us-east-1d -m ping

```
