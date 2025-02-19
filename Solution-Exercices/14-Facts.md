# Facts et variables implicites

## Enoncé:
Écrivez trois playbooks pour afficher des informations sur chacun des Target Hosts :

- `pkg-info.yml` pour afficher le gestionnaire de paquets utilisé
- `python-info.yml` pour afficher la version de Python in`stallée
- `dns-info.yml` pour afficher le(s) serveur(s) DNS utilisé(s)

## Réponses:
### pkg-info.yml
```
---  # pkg-info.yml

- hosts: all

  tasks:
    - name: Display distribution package manager information
      debug:
        msg: >-
          Host [{{inventory_hostname}}] uses packet manager {{ansible_pkg_mgr}}.

...
```

Résultats du playbook
```
[vagrant@ansible ema]$ ansible-playbook playbooks/pkg-info.yml 

PLAY [all] ************************************************************************************************************

TASK [Gathering Facts] ************************************************************************************************
ok: [debian]
ok: [suse]
ok: [rocky]

TASK [Display distribution package manager information] ***************************************************************
ok: [rocky] => {
    "msg": "Host [rocky] uses packet manager dnf."
}
ok: [debian] => {
    "msg": "Host [debian] uses packet manager apt."
}
ok: [suse] => {
    "msg": "Host [suse] uses packet manager zypper."
}

PLAY RECAP ************************************************************************************************************
debian                     : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
rocky                      : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
suse                       : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

### python-info.yml
```
---  # python-info.yml

- hosts: all

  tasks:
    - debug:
        var: ansible_python.version_info

...
```
Résultats du playbook
```
...
TASK [debug] **********************************************************************************************************
ok: [rocky] => {
    "ansible_python.version_info": [
        3,
        9,
        18,
        "final",
        0
    ]
}
ok: [debian] => {
    "ansible_python.version_info": [
        3,
        11,
        2,
        "final",
        0
    ]
}
ok: [suse] => {
    "ansible_python.version_info": [
        3,
        6,
        15,
        "final",
        0
    ]
}
...
```

### dns-info.yml
```
---  # dns-info.yml

- hosts: all

  tasks:
    - debug:
        var: ansible_dns

...
```
Résultats du playbook
```
TASK [debug] **********************************************************************************************************
ok: [rocky] => {
    "ansible_dns": {
        "nameservers": [
            "10.0.2.3"
        ],
        "options": {
            "single-request-reopen": true
        }
    }
}
ok: [debian] => {
    "ansible_dns": {
        "nameservers": [
            "4.2.2.1",
            "4.2.2.2",
            "208.67.220.220"
        ]
    }
}
ok: [suse] => {
    "ansible_dns": {
        "nameservers": [
            "10.0.2.3"
        ]
    }
}
```