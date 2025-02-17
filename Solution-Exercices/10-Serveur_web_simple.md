# Un serveur web simple

## Enoncé :

### Écrivez trois playbooks :

- Un premier playbook apache-debian.yml qui installe Apache sur l’hôte debian avec une page personnalisée Apache web server running on Debian Linux.
- Un deuxième playbook apache-rocky.yml qui installe Apache sur l’hôte rocky avec une page personnalisée Apache web server running on Rocky Linux.
- Un troisième playbook apache-suse.yml qui installe Apache sur l’hôte suse avec une page personnalisée Apache web server running on SUSE Linux.

### Voici quelques pistes de réflexion :

- Pas la peine de rafraîchir le cache de paquets sous Rocky Linux et SUSE.
- Apache n’a pas forcément le même nom sur ces distributions.
- La même chose vaut pour le service correspondant.
- Rocky Linux utilise le gestionnaire de paquets dnf.
- SUSE Linux utilise le gestionnaire de paquets zypper.
- Les modules de gestion de paquets correspondants portent le même nom.
- L’emplacement de la page web par défaut (directive DocumentRoot) peut varier.
- J’ai supprimé le pare-feu FirewallD installé par défaut sous Rocky Linux pour ne pas trop vous embrouiller.

## Réponses :
Lors du vagrant up, je suis resté bloqué pendant 15min sur :
```
...
ansible: Resolving objects.githubusercontent.com (objects.githubusercontent.com)... 185.199.109.133, 185.199.110.133, 185.199.108.133, ...
    ansible: Connecting to objects.githubusercontent.com (objects.githubusercontent.com)|185.199.109.133|:443... connected.
```
Il s'agissait de l'install de direnv, je vais travailler depuis le répertoire ema pour cet atelier.

### apache-debian.yml
```
[vagrant@ansible ema]$ vim playbooks/apache-debian.yml
```
Ensuite j'écris le playbook en le lançant plusieurs fois en cours d'écriture :
```
---  # apache-debian.yml

- hosts: debian
  tasks:
    - name: Update package information
      apt:
        update_cache: true
        cache_valid_time: 3600

    - name: Install Apache
      apt:
        name: apache2
        state: present

    - name: Start & Enable Apache
      service:
        name: apache2
        state: started
        enabled: true

    - name: Install custom web page
      copy:
        dest: /var/www/html/index.html
        mode: 0644
        content: |
          <!doctype html>
          <html>
            <head>
              <meta charset="utf-8">
              <title>Test</title>
            </head>
            <body>
              <h1>My first Ansible-managed website</h1>
            </body>
          </html>
...
```
Résultats :
```
[vagrant@ansible ema]$ ansible-playbook playbooks/apache-debian.yml 

PLAY [debian] *********************************************************************************************************

TASK [Gathering Facts] ************************************************************************************************
ok: [debian]

TASK [Update package information] *************************************************************************************
ok: [debian]

TASK [Install Apache] *************************************************************************************************
ok: [debian]

TASK [Start & Enable Apache] ******************************************************************************************
ok: [debian]

TASK [Install custom web page] ****************************************************************************************
changed: [debian]

PLAY RECAP ************************************************************************************************************
debian                     : ok=5    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```
```
[vagrant@ansible ema]$ curl 192.168.56.30
<!doctype html>
<html>
  <head>
    <meta charset="utf-8">
    <title>Test</title>
  </head>
  <body>
    <h1>My first Ansible-managed website</h1>
  </body>
</html>
```

### apache-rocky.yml
```
---  # apache-rocky.yml

- hosts: rocky
  tasks:
    - name: Install Apache
      dnf:
        name: httpd
        state: present

    - name: Start & Enable Apache
      service:
        name: httpd
        state: started
        enabled: true

    - name: Install custom web page
      copy:
        dest: /var/www/html/index.html
        mode: 0644
        content: |
          <!doctype html>
          <html>
            <head>
              <meta charset="utf-8">
              <title>Test</title>
            </head>
            <body>
              <h1>My first Ansible-managed website</h1>
            </body>
          </html>
...
```

Résultats:
```
[vagrant@ansible ema]$ ansible-playbook playbooks/apache-rocky.yml 

PLAY [rocky] **********************************************************************************************************

TASK [Gathering Facts] ************************************************************************************************
ok: [rocky]

TASK [Install Apache] *************************************************************************************************
ok: [rocky]

TASK [Start & Enable Apache] ******************************************************************************************
changed: [rocky]

TASK [Install custom web page] ****************************************************************************************
changed: [rocky]

PLAY RECAP ************************************************************************************************************
rocky                      : ok=4    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```
```
[vagrant@ansible ema]$ curl 192.168.56.20
<!doctype html>
<html>
  <head>
    <meta charset="utf-8">
    <title>Test</title>
  </head>
  <body>
    <h1>My first Ansible-managed website</h1>
  </body>
</html>
```

### apache-suse.yml
```
---  # apache-suse.yml

- hosts: suse
  tasks:
    - name: Install Apache
      zypper:
        name: apache2

    - name: Start & Enable Apache
      service:
        name: apache2
        state: started
        enabled: true

    - name: Install custom web page
      copy:
        dest: /srv/www/htdocs/index.html
        mode: 0644
        content: |
          <!doctype html>
          <html>
            <head>
              <meta charset="utf-8">
              <title>Test</title>
            </head>
            <body>
              <h1>My first Ansible-managed website</h1>
            </body>
          </html>
...
```

Résultats:
```
[vagrant@ansible ema]$ ansible-playbook playbooks/apache-suse.yml

PLAY [suse] ***********************************************************************************************************

TASK [Gathering Facts] ************************************************************************************************
ok: [suse]

TASK [Install Apache] *************************************************************************************************
ok: [suse]

TASK [Start & Enable Apache] ******************************************************************************************
ok: [suse]

TASK [Install custom web page] ****************************************************************************************
ok: [suse]

PLAY RECAP ************************************************************************************************************
suse                       : ok=4    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```
```
[vagrant@ansible ema]$ curl suse
<!doctype html>
<html>
  <head>
    <meta charset="utf-8">
    <title>Test</title>
  </head>
  <body>
    <h1>My first Ansible-managed website</h1>
  </body>
</html>
```
