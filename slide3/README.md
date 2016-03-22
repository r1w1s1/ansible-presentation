 
# Modulos… (Slide3)

- Centenas de modulos para tarefas triviais ou complexas
- Se comunicam através de JSON
- Podem ser desenvolvidos nas principais linguagens. (Ruby, Python, etc)



# Não necessita de agente… (slide4)

- Utiliza SSH
- Não necessita “deploy”

  
# Seguro (slide5)

- Utiliza SSH
- Não instala “daemons” vulneráveis nos Servidores/Nodes
- Não necessita abertura “extra” de portas.

 

# Idempotência… (slide6)

- Podemos rodar o playbook multiplas vezes sem alterar o resultado .




# Poderoso (slide7)

- Deploy Aplicação 
- Gerenciamento de Configuração
- Provisionamento
- Orquestração 
- Alternativa a Capistrano/Fabric


# Instalando Ansible (slide8)

```
$ yum install ansible
$ pip install ansible

$ brew install ansible

```

# Tipos de Inventários (slide9)

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


# Módulos

- **apt/yum/portge/zypper (S.O) =>** Instalação de pacotes.

- **pip/npm/cpanm (Linguagens) =>** Instalação de Lib/pacotes.

- **command/shell =>** Execução de comandos shell/scripts.

- **copy =>** Copia de arquivos (origem/destino).

- **file =>** Criação de diretórios, link simbólico, troca de permissões.

- **service =>** Liga/Desliga/Habilitar serviços.

- **docker_image =>** gerenciamento de imagens docker

- **docker =>** gerenciamento de containers docker



# **Fatos**

```json
$ ansible localhost -m setup

localhost | success >> {
    "ansible_facts": {
        "ansible_all_ipv4_addresses": [
            "10.0.2.15"
    ],
    "ansible_distribution": "CentOS",
    "ansible_distribution_major_version": "7",
    "ansible_distribution_release": "Core",
    "ansible_distribution_version": "7.1.1503",
    "ansible_domain": "localdomain",
    }
}
```

```json
$ ansible localhost -m setup -a ‘filter=ansible_distribution’

localhost | success >> {
    "ansible_facts": {
        "ansible_distribution": "CentOS"
    },
    "changed": false
}
```

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

# **Playbook**

- Playbook utiliza o formato YAML simplificado a idéia é não se tornar linguagem de programação e sim uma sintaxe **simples** de configuração[^2].

- Playbook é composto por um ou mais “plays”


[^2]:[Playbooks Intro](http://docs.ansible.com/)


# Exemplo de playbook simples


```
- - -
- hosts: app1 
  gather_facts: yes
  sudo: true
  vars:
    packages_base:
        - vim
        - telnet
        - git 
        - epel-release
  tasks:
  - name: Install Packages
    yum: name={{ item }} state=latest
    with_items:
       - “{{ packages_base }}”
    when: ansible_os_family == ‘RedHat’

  - name: Upgrade all packages
    yum: name=* state=latest
    when: ansible_os_family == ‘RedHat’
    tags: pkg_upgrade

  - name: Disable SELinux
    selinux: state=disabled
    when: ansible_os_family == ‘RedHat’
    tags: selinux
```


# Organizando melhor o playbook (roles)

  Para melhor organização dos nossos playbooks criamos o que chamamos de **roles**[^3] que seria basicamente a estrutura abaixo.

```
   roles/
     init/
       tasks/
         main.yml
       vars/
          main.yml
```

[^3]:[Playbooks Roles](https://docs.ansible.com/playbooks_roles.html) | [Ansible Roles](https://www.digitalocean.com/community/tutorials/how-to-use-ansible-roles-to-abstract-your-infrastructure-environment)



cat roles/init/tasks/main.yml

```
---
- name: Install Packages
    yum: name={{ item }} state=latest
    with_items:
       - “{{ packages_base }}”
    when: ansible_os_family == ‘RedHat’ #(Condicional)

  - name: Upgrade all packages
    yum: name=* state=latest
    when: ansible_os_family == ‘RedHat’ 
    tags: pkg_upgrade

  - name: Disable SELinux
    selinux: state=disabled
    when: ansible_os_family == ‘RedHat’ 
    tags: selinux
```


cat roles/init/vars/main.yml


```
---
packages_base:
    - vim
    - telnet
    - git 
    - epel-release
```


# **Playbook - Tags**

$ ansible-playbook site.yml --tags pkg_upgrade

