# Jinja et Templates

## Enoncé

Dans notre précédent exercice, nous avons mis en place la synchronisation NTP avec Chrony sur nos Target Hosts, en installant le fichier de configuration ci-dessous sur chacune des quatre cibles :
```
# chrony.conf
server 0.fr.pool.ntp.org iburst
server 1.fr.pool.ntp.org iburst
server 2.fr.pool.ntp.org iburst
server 3.fr.pool.ntp.org iburst
driftfile /var/lib/chrony/drift
makestep 1.0 3
rtcsync
logdir /var/log/chrony
```
Écrivez un playbook `chrony.yml` qui installe un fichier de configuration personnalisé sur vos cibles. La première ligne de commentaire devra indiquer le chemin complet vers le fichier :

- Dans certains cas ce sera `/etc/chrony/chrony.conf`.
- Dans d’autres cas ce sera simplement `/etc/chrony.conf`.

## Réponse
Les fichiers du répertoire host_vars sont les mêmes que dans le lab précédent.

Mon playbook:
```
---  # chrony.yml

- hosts: all
  tasks:
    - name: Update cache on debian/ubuntu
      apt:
        update_cache: true
        cache_valid_time: 3600
      when: ansible_os_family == "Debian"

    - name: Install Chrony
      package:
        name: "{{chrony.package}}"
        state: present

    - name: Enable & start Chrony
      service:
        name: "{{chrony.service}}"
        state: started
        enabled: true

    - name: Configure chrony
      template:
        dest: "{{chrony.confdir}}"
        mode: 0644
        owner: root
        group: root
        src: chrony.conf.j2
      notify: Reload chrony

  handlers:
    - name: Reload chrony
      service:
        name: "{{chrony.service}}"
        state: restarted
...
```

Résultats du playbook :
```
[vagrant@ansible playbooks]$ ansible-playbook chrony.yml 

PLAY [all] ************************************************************************************************************

TASK [Gathering Facts] ************************************************************************************************
ok: [debian]
ok: [ubuntu]
ok: [suse]
ok: [rocky]

TASK [Update cache on debian/ubuntu] **********************************************************************************
skipping: [rocky]
skipping: [suse]
changed: [debian]
ok: [ubuntu]

TASK [Install Chrony] *************************************************************************************************
ok: [rocky]
changed: [debian]
changed: [ubuntu]
changed: [suse]

TASK [Enable & start Chrony] ******************************************************************************************
ok: [ubuntu]
ok: [debian]
ok: [rocky]
changed: [suse]

TASK [Configure chrony] ***********************************************************************************************
changed: [ubuntu]
changed: [debian]
changed: [suse]
changed: [rocky]

RUNNING HANDLER [Reload chrony] ***************************************************************************************
changed: [debian]
changed: [ubuntu]
changed: [suse]
changed: [rocky]

PLAY RECAP ************************************************************************************************************
debian                     : ok=6    changed=4    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
rocky                      : ok=5    changed=2    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0   
suse                       : ok=5    changed=4    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0   
ubuntu                     : ok=6    changed=3    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

Pour vérifier que les modifications ont bien été effectuées:
```
$ exit
[ema@localhost:atelier-18] $ vagrant ssh rocky
[vagrant@rocky ~]$ cat /etc/chrony.conf
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
> On peut voir que tout est OK.

Travail terminé !
