# Configuration de base

### Éditez /etc/hosts de manière à ce que les Target Hosts soient joignables par leur nom d’hôte simple :
```
vagrant@control:~$ cat /etc/hosts
127.0.0.1 localhost
127.0.1.1 vagrant

# The following lines are desirable for IPv6 capable hosts
::1     ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
127.0.2.1 control control

192.168.56.10 control
192.168.56.20 target01
192.168.56.30 target02
192.168.56.40 target03
```

### Configurez l’authentification par clé SSH avec les trois Target Hosts.
```
vagrant@control:~$ ssh-keygen
vagrant@control:~$ ssh-copy-id vagrant@target01
vagrant@control:~$ ssh-copy-id vagrant@target02
vagrant@control:~$ ssh-copy-id vagrant@target03
```

### Installez Ansible.
```
vagrant@control:~$ apt search --names-only ansible
Sorting... Done
Full Text Search... Done
ansible/jammy 2.10.7+merged+base+2.10.8+dfsg-1 all
  Configuration management, deployment, and task execution system
```
Je trouve la version trop ancienne, je vais l'installer avec pip dans un venv.
```
vagrant@control:~$ sudo apt update
vagrant@control:~$ sudo apt install python3-pip python3-venv -y
vagrant@control:~$ python3 -m venv ~/.venv/ansible
vagrant@control:~$ source .venv/ansible/bin/activate
(ansible) vagrant@control:~$ pip install ansible
```


Envoyez un premier ping Ansible sans configuration.
```

```

Créez un répertoire de projet ~/monprojet.
```

```

Créez un fichier vide ansible.cfg dans ce répertoire.
Vérifiez si ce fichier est bien pris en compte par Ansible.
Spécifiez un inventaire nommé hosts.
Activez la journalisation dans ~/journal/ansible.log.
Testez la journalisation.
Créez un groupe [testlab] avec vos trois Target Hosts.
Définissez explicitement l’utilisateur vagrant pour la connexion à vos cibles.
Envoyez un ping Ansible vers le groupe de machines [all].
Définissez l’élévation des droits pour l’utilisateur vagrant sur les Target Hosts.
Affichez la première ligne du fichier /etc/shadow sur tous les Target Hosts.
Quittez le Control Host et supprimez toutes les VM de l’atelier.