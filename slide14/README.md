 
# Organizando melhor o playbook (roles) (Slide14)

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
