

# **Comandos Ad-hoc**

Quando é necessario executar um comando rápido e não precisa salvar ao “playbook”, usamos o ansible em linha de comando ou seja em ad-hoc mode.[^1]

**$ ansible <host> -m MODULE-NAME -a MODULE-ARGS**

```
#Ping da maquina local
$ ansible localhost -m ping

#Reinicia todas as maquina de 10 em 10
$ ansible all -a “/sbin/reboot” -f 10

#Instala pacote “epel”
$ ansible saopaulo -m yum -a “name=epel-release state=present”

#Remove pacote apache
$ ansible riodejaneiro -m yum -a “name=httpd state=absent”

#Inicia o serviço do apache
$ ansible saorio -m service -a “name=httpd state=started”

#Habilitando o start do serviço
$ ansible saorio -m service -a “name=httpd enabled=yes”
```

[^1]:[AD-Hoc Intro](http://docs.ansible.com/intro_adhoc.html)

---

# **Ad-hoc…**

$ ansible vagrant -m ping

```ini
192.168.60.5 | success >> {
    “changed”: false,
    “ping”: “pong”
}

192.168.60.4 | success >> {
    “changed”: false,
    “ping”: “pong”
}

192.168.60.6 | success >> {
    “changed”: false,
    “ping”: “pong”
}
```


# **Ad-hoc…**

$ ansible vagrant -m command -a “uptime”

```ini
192.168.60.5 | success | rc=0 >>
 10:27:01 up 12 min,  1 user,  load average: 0.00, 0.04, 0.07

192.168.60.4 | success | rc=0 >>
 10:27:01 up 13 min,  1 user,  load average: 0.00, 0.02, 0.05

192.168.60.6 | success | rc=0 >>
 14:27:02 up 11 min,  1 user,  load average: 0.00, 0.03, 0.05
```
