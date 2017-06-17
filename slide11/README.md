
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
