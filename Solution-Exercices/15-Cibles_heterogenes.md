# Cibles hétérogènes

## Enoncé
Écrivez successivement deux playbooks pour mettre en place la synchronisation NTP avec Chrony sur vos quatre Target Hosts sous Debian, Rocky Linux, SUSE Linux et Ubuntu. Il vous faudra identifier le nom du paquet, le service correspondant et le chemin vers le fichier de configuration par défaut pour chacune des distributions.

Voici la configuration qu’il faudra installer sur chacune des quatre cibles :
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

- Le premier playbook `chrony-01.yml` utilisera les modules de gestion de paquets natifs `apt`, `dnf` et `zypper` et s’inspirera de la méthode « gros sabots » utilisée plus haut dans cet article.
- Le deuxième playbook `chrony-02.yml` définira trois variables `chrony_package`, `chrony_service` et `chrony_confdir` et utilisera le module de gestion de paquets générique `package`.

## Réponses:

### chrony-01.yml
Il se trouve que le nom du packet est chrony pour les 3 familles de distibutions. De la même façon, le nom du daemon est `chronyd`.

```
---  # chrony-01.yml

- hosts: all

  tasks:
    - name: Update package information on Debian/Ubuntu
      apt:
        update_cache: true
        cache_valid_time: 3600
      when: ansible_os_family == "Debian"

    - name: Install Chrony on Debian/Ubuntu
      apt:
        name: chrony
      when: ansible_os_family == "Debian"

    - name: Install Chrony on Rocky Linux
      dnf:
        name: chrony
      when: ansible_distribution == "Rocky"

    - name: Install Chrony on SUSE Linux
      zypper:
        name: chrony
      when: ansible_distribution == "openSUSE Leap"

    - name: Enable & start Chrony
      service:
        name: chronyd
        state: started
        enabled: true

    - name: Configure chrony on debian based distros
      copy:
        dest: /etc/chrony/chrony.conf
        owner: root
        group: root
        mode: 0644
        content: |
          # /etc/chrony/chrony.conf
          server 0.fr.pool.ntp.org iburst
          server 1.fr.pool.ntp.org iburst
          server 2.fr.pool.ntp.org iburst
          server 3.fr.pool.ntp.org iburst
          driftfile /var/lib/chrony/drift
          makestep 1.0 3
          rtcsync
          logdir /var/log/chrony
      when: ansible_os_family == "Debian"
      notify: Reload chrony

    - name: Configure chrony on rocky and suse
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
      when: ansible_distribution == "Rocky" or ansible_distribution == "openSUSE Leap"
      notify: Reload chrony

  handlers:
    - name: Reload chrony
      service:
        name: chronyd
        state: restarted
...
```
Résultats du playbook:
```
[vagrant@ansible playbooks]$ ansible-playbook chrony-01.yml 

PLAY [all] ************************************************************************************************************

TASK [Gathering Facts] ************************************************************************************************
ok: [debian]
ok: [suse]
ok: [ubuntu]
ok: [rocky]

TASK [Update package information on Debian/Ubuntu] ********************************************************************
skipping: [rocky]
skipping: [suse]
ok: [debian]
ok: [ubuntu]

TASK [Install Chrony on Debian/Ubuntu] ********************************************************************************
skipping: [rocky]
skipping: [suse]
ok: [debian]
ok: [ubuntu]

TASK [Install Chrony on Rocky Linux] **********************************************************************************
skipping: [debian]
skipping: [suse]
skipping: [ubuntu]
ok: [rocky]

TASK [Install Chrony on SUSE Linux] ***********************************************************************************
skipping: [rocky]
skipping: [debian]
skipping: [ubuntu]
ok: [suse]

TASK [Enable & start Chrony] ******************************************************************************************
ok: [ubuntu]
ok: [debian]
ok: [suse]
ok: [rocky]

TASK [Configure chrony on debian based distros] ***********************************************************************
skipping: [rocky]
skipping: [suse]
ok: [ubuntu]
ok: [debian]

TASK [Configure chrony on rocky and suse] *****************************************************************************
skipping: [debian]
skipping: [ubuntu]
changed: [suse]
changed: [rocky]

RUNNING HANDLER [Reload chrony] ***************************************************************************************
changed: [suse]
changed: [rocky]

PLAY RECAP ************************************************************************************************************
debian                     : ok=5    changed=0    unreachable=0    failed=0    skipped=3    rescued=0    ignored=0   
rocky                      : ok=5    changed=2    unreachable=0    failed=0    skipped=4    rescued=0    ignored=0   
suse                       : ok=5    changed=2    unreachable=0    failed=0    skipped=4    rescued=0    ignored=0   
ubuntu                     : ok=5    changed=0    unreachable=0    failed=0    skipped=3    rescued=0    ignored=0 
```

### chrony-02.yml
Pour la définition des différentes variables, j'ai fait le choix d'utiliser les group_vars et host_vars car je préfère cette façon de travailler.
J'ai compris après coup que utiliser simultanément les group_vars et les host_vars pour une même variable est une mauvaise idée car les host_vars écrasent les group_vars.
Je vais me concentrer sur les host_vars pour cet exercice
```
---  # host_vars/debian.yml

chrony:
  package: chrony
  service: chronyd
  confdir: /etc/chrony/chrony.conf

...
```
```
---  # host_vars/ubuntu.yml

chrony:
  package: chrony
  service: chronyd
  confdir: /etc/chrony/chrony.conf

...
```
```
---  # host_vars/rocky.yml

chrony:
  package: chrony
  service: chronyd
  confdir: /etc/chrony.conf

...
```
```
---  # host_vars/suse.yml

chrony:
  package: chrony
  service: chronyd
  confdir: /etc/chrony.conf

...
```

Et voici mon playbook:
```
---  # chrony-02.yml

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
      copy:
        dest: "{{chrony.confdir}}"
        owner: root
        group: root
        mode: 0644
        content: |
          # chrony.conf
          server 0.fr.pool.ntp.org iburst
          server 1.fr.pool.ntp.org iburst
          server 2.fr.pool.ntp.org iburst
          server 3.fr.pool.ntp.org iburst
          driftfile /var/lib/chrony/drift
          makestep 1.0 3
          rtcsync
          logdir /var/log/chrony
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
[vagrant@ansible ema]$ ansible-playbook playbooks/chrony-02.yml 

PLAY [all] ************************************************************************************************************

TASK [Gathering Facts] ************************************************************************************************
ok: [debian]
ok: [ubuntu]
ok: [suse]
ok: [rocky]

TASK [Update cache on debian/ubuntu] **********************************************************************************
skipping: [rocky]
skipping: [suse]
ok: [debian]
ok: [ubuntu]

TASK [Install Chrony] *************************************************************************************************
ok: [debian]
ok: [ubuntu]
ok: [suse]
ok: [rocky]

TASK [Enable & start Chrony] ******************************************************************************************
ok: [debian]
ok: [ubuntu]
ok: [suse]
ok: [rocky]

TASK [Configure chrony] ***********************************************************************************************
ok: [ubuntu]
ok: [debian]
changed: [suse]
ok: [rocky]

RUNNING HANDLER [Reload chrony] ***************************************************************************************
changed: [suse]

PLAY RECAP ************************************************************************************************************
debian                     : ok=5    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
rocky                      : ok=4    changed=0    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0   
suse                       : ok=5    changed=2    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0   
ubuntu                     : ok=5    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```