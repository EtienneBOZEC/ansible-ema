# Handler

## Enoncé:
Écrivez un playbook chrony.yml qui assure la synchronisation NTP de tous vos Target Hosts :

- Installez le paquet chrony.
- Activez et démarrez le service chronyd correspondant.
- Jetez un œil sur le fichier de configuration /etc/chrony.conf fourni par défaut.
- Installez une configuration personnalisée (cf. ci-dessous).
- Prenez en compte cette nouvelle configuration.
- Vérifiez la syntaxe correcte de votre playbook chrony.yml.
- Vérifiez l’idempotence de votre playbook.
- Voici la configuration à installer :

```
# /etc/chrony.conf
server 0.fr.pool.ntp.org iburst
server 1.fr.pool.ntp.org iburst
server 2.fr.pool.ntp.org iburst
server 3.fr.pool.ntp.org iburst
driftfile /var/lib/chrony/drift
makestep 1.0 3
rtcsync
logdir /var/log/chrony
```

## Réponses:
Mon playbook :
```
---  # chrony.yml

- hosts: redhat

  tasks:
    - name: Install Chrony
      dnf:
        name: chrony

    - name: Enable & Start Chrony
      service:
        name: chronyd
        state: started
        enabled: true

    - name: Configure Chrony
      copy:
        dest: /etc/chrony.conf
        owner: root
        group: root
        mode: 0644
        content: |
          # /etc/chrony.conf
          server 0.fr.pool.ntp.org iburst
          server 1.fr.pool.ntp.org iburst
          server 2.fr.pool.ntp.org iburst
          server 3.fr.pool.ntp.org iburst
          driftfile /var/lib/chrony/drift
          makestep 1.0 3
          rtcsync
          logdir /var/log/chrony
      notify:

  handlers:
    - name: Reload chrony
      service:
        name: chronyd
        state: restarted

...
```

Vérification de la syntaxe:
```
[vagrant@control playbooks]$ yamllint chrony.yml
```

Lancement du playbook:
```
[vagrant@control playbooks]$ ansible-playbook chrony.yml 

PLAY [redhat] *********************************************************************************************************

TASK [Gathering Facts] ************************************************************************************************
ok: [target02]
ok: [target01]
ok: [target03]

TASK [Install Chrony] *************************************************************************************************
ok: [target02]
ok: [target03]
ok: [target01]

TASK [Enable & Start Chrony] ******************************************************************************************
ok: [target01]
ok: [target02]
ok: [target03]

TASK [Configure Chrony] ***********************************************************************************************
changed: [target03]
changed: [target02]
changed: [target01]

PLAY RECAP ************************************************************************************************************
target01                   : ok=4    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
target02                   : ok=4    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
target03                   : ok=4    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

Vérification de l'idempotence:
```
[vagrant@control playbooks]$ ansible-playbook chrony.yml 

PLAY [redhat] *********************************************************************************************************

TASK [Gathering Facts] ************************************************************************************************
ok: [target03]
ok: [target02]
ok: [target01]

TASK [Install Chrony] *************************************************************************************************
ok: [target02]
ok: [target01]
ok: [target03]

TASK [Enable & Start Chrony] ******************************************************************************************
ok: [target02]
ok: [target01]
ok: [target03]

TASK [Configure Chrony] ***********************************************************************************************
ok: [target01]
ok: [target02]
ok: [target03]

PLAY RECAP ************************************************************************************************************
target01                   : ok=4    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
target02                   : ok=4    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
target03                   : ok=4    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0 
```
On peut voir que mes tâches sont bien idempotentes.