 

# **Playbook** (Slide13)

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