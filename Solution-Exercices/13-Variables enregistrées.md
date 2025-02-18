# Variables enregistrées

## Enoncé:
- Écrivez un playbook `kernel.yml` qui affiche les infos détaillées du noyau sur tous vos Target Hosts. Utilisez la commande `uname -a` et le module `debug` avec le paramètre `msg`.
- Essayez d’obtenir le même résultat en utilisant le paramètre `var` du module `debug`.
- Écrivez un playbook `packages.yml` qui affiche le nombre total de paquets RPM installés sur les hôtes rocky et suse (`rpm -qa | wc -l`).

## Réponses

### Playbook kernel.yml :
```
---  # kernel.yml

- hosts: all
  gather_facts: false

  tasks:
    - name: Show detailled kernel information
      command: uname -a
      changed_when: false
      register: uname_cmd

    - debug:
        msg: "{{uname_cmd}}"
...
```
Résultats du playbook (tronqués):
```
...
ok: [rocky] => {
    "msg": {
        "changed": false,
        "cmd": [
            "uname",
            "-a"
        ],
...
        "stdout": "Linux rocky 5.14.0-362.13.1.el9_3.x86_64 #1 SMP PREEMPT_DYNAMIC Wed Dec 13 14:07:45 UTC 2023 x86_64 x86_64 x86_64 GNU/Linux",
        "stdout_lines": [
            "Linux rocky 5.14.0-362.13.1.el9_3.x86_64 #1 SMP PREEMPT_DYNAMIC Wed Dec 13 14:07:45 UTC 2023 x86_64 x86_64 x86_64 GNU/Linux"
        ]
    }
}
ok: [debian] => {
    "msg": {
        "changed": false,
        "cmd": [
            "uname",
            "-a"
        ],
...
        "stdout": "Linux debian 6.1.0-17-amd64 #1 SMP PREEMPT_DYNAMIC Debian 6.1.69-1 (2023-12-30) x86_64 GNU/Linux",
        "stdout_lines": [
            "Linux debian 6.1.0-17-amd64 #1 SMP PREEMPT_DYNAMIC Debian 6.1.69-1 (2023-12-30) x86_64 GNU/Linux"
        ]
    }
}
ok: [suse] => {
    "msg": {
        "changed": false,
        "cmd": [
            "uname",
            "-a"
        ],
...
        "stdout": "Linux suse 5.14.21-150500.55.39-default #1 SMP PREEMPT_DYNAMIC Tue Dec 5 10:06:35 UTC 2023 (2e4092e) x86_64 x86_64 x86_64 GNU/Linux",
        "stdout_lines": [
            "Linux suse 5.14.21-150500.55.39-default #1 SMP PREEMPT_DYNAMIC Tue Dec 5 10:06:35 UTC 2023 (2e4092e) x86_64 x86_64 x86_64 GNU/Linux"
        ]
    }
}
...
```

### Playbook kernet.yml avec debug -> var
```
---  # kernel.yml

- hosts: all
  gather_facts: false

  tasks:
    - name: Show detailled kernel information
      command: uname -a
      changed_when: false
      register: uname_cmd

    - debug:
        var: uname_cmd.stdout_lines
...
```

Résultats du playbook :
```
[vagrant@ansible playbooks]$ ansible-playbook kernel.yml 

PLAY [all] ************************************************************************************************************

TASK [Show detailled kernel information] ******************************************************************************
ok: [debian]
ok: [suse]
ok: [rocky]

TASK [debug] **********************************************************************************************************
ok: [rocky] => {
    "uname_cmd.stdout_lines": [
        "Linux rocky 5.14.0-362.13.1.el9_3.x86_64 #1 SMP PREEMPT_DYNAMIC Wed Dec 13 14:07:45 UTC 2023 x86_64 x86_64 x86_64 GNU/Linux"
    ]
}
ok: [debian] => {
    "uname_cmd.stdout_lines": [
        "Linux debian 6.1.0-17-amd64 #1 SMP PREEMPT_DYNAMIC Debian 6.1.69-1 (2023-12-30) x86_64 GNU/Linux"
    ]
}
ok: [suse] => {
    "uname_cmd.stdout_lines": [
        "Linux suse 5.14.21-150500.55.39-default #1 SMP PREEMPT_DYNAMIC Tue Dec 5 10:06:35 UTC 2023 (2e4092e) x86_64 x86_64 x86_64 GNU/Linux"
    ]
}

PLAY RECAP ************************************************************************************************************
debian                     : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
rocky                      : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
suse                       : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```
> On peut voir que le résultat est bien plus concis, toutes les informations superflues ont été ignorées.

### Playbook packages.yml
```
---  # packages.yml

- hosts: rocky suse
  gather_facts: false

  tasks:
    - name: Show number of installed rpm packets
      shell: "rpm -qa | wc -l"
      changed_when: false
      register: rpm_cmd

    - debug:
        var: rpm_cmd.stdout_lines
...
```
> Pour ce playbook j'ai du utiliser le module shell car les pipes ne sont pas pris en compte par le module command.

Résultats du playbook:
```
[vagrant@ansible playbooks]$ ansible-playbook packages.yml 

PLAY [rocky suse] *****************************************************************************************************

TASK [Show number of installed rpm packets] ***************************************************************************
ok: [suse]
ok: [rocky]

TASK [debug] **********************************************************************************************************
ok: [rocky] => {
    "rpm_cmd.stdout_lines": [
        "671"
    ]
}
ok: [suse] => {
    "rpm_cmd.stdout_lines": [
        "917"
    ]
}

PLAY RECAP ************************************************************************************************************
rocky                      : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
suse                       : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0 
```