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

### Configurez l’authentification par clé SSH avec les trois Target Hosts :
```
vagrant@control:~$ ssh-keygen
vagrant@control:~$ ssh-copy-id vagrant@target01
vagrant@control:~$ ssh-copy-id vagrant@target02
vagrant@control:~$ ssh-copy-id vagrant@target03
```

### Installez Ansible :
```
vagrant@control:~$ apt search --names-only ansible
Sorting... Done
Full Text Search... Done
ansible/jammy 2.10.7+merged+base+2.10.8+dfsg-1 all
  Configuration management, deployment, and task execution system
```
> Je trouve la version trop ancienne, je vais l'installer avec pip dans un venv.
```
vagrant@control:~$ sudo apt update
vagrant@control:~$ sudo apt install python3-pip python3-venv -y
vagrant@control:~$ python3 -m venv ~/.venv/ansible
vagrant@control:~$ source .venv/ansible/bin/activate
(ansible) vagrant@control:~$ pip install ansible
(ansible) vagrant@control:~$ ansible --version
ansible [core 2.17.8]
```

### Envoyez un premier ping Ansible sans configuration :
```
(ansible) vagrant@control:~$ ansible all -i "target01,target02,target03" -m ping
[WARNING]: Platform linux on host target01 is using the discovered Python interpreter at /usr/bin/python3.10, but
future installation of another Python interpreter could change the meaning of that path. See
https://docs.ansible.com/ansible-core/2.17/reference_appendices/interpreter_discovery.html for more information.
target01 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3.10"
    },
    "changed": false,
    "ping": "pong"
}
[WARNING]: Platform linux on host target02 is using the discovered Python interpreter at /usr/bin/python3.10, but
future installation of another Python interpreter could change the meaning of that path. See
https://docs.ansible.com/ansible-core/2.17/reference_appendices/interpreter_discovery.html for more information.
target02 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3.10"
    },
    "changed": false,
    "ping": "pong"
}
[WARNING]: Platform linux on host target03 is using the discovered Python interpreter at /usr/bin/python3.10, but
future installation of another Python interpreter could change the meaning of that path. See
https://docs.ansible.com/ansible-core/2.17/reference_appendices/interpreter_discovery.html for more information.
target03 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3.10"
    },
    "changed": false,
    "ping": "pong"
}
```

### Créez un répertoire de projet ~/monprojet :
```
(ansible) vagrant@control:~$ mkdir ~/monprojet
(ansible) vagrant@control:~$ cd monprojet/
```

### Créez un fichier vide ansible.cfg dans ce répertoire :
```
(ansible) vagrant@control:~/monprojet$ touch ansible.cfg
```

### Vérifiez si ce fichier est bien pris en compte par Ansible :
```
(ansible) vagrant@control:~/monprojet$ ansible --version | head -n 2
ansible [core 2.17.8]
  config file = /home/vagrant/monprojet/ansible.cfg
```
Il est bien pris en compte.

### Spécifiez un inventaire nommé hosts + Activez la journalisation dans ~/journal/ansible.log :
Je modifie ansible.cfg comme ceci :
```
[defaults]
inventory = ./hosts
log_path = ./ansible.log
```

### Testez la journalisation :
```
(ansible) vagrant@control:~/monprojet$ ansible all -i "target01,target02,target03" -m ping
(ansible) vagrant@control:~/monprojet$ cat ansible.log
2025-02-12 09:44:19,052 p=4590 u=vagrant n=ansible | [WARNING]: Platform linux on host target01 is using the discovered Python interpreter at /usr/bin/python3.10, but
future installation of another Python interpreter could change the meaning of that path. See
https://docs.ansible.com/ansible-core/2.17/reference_appendices/interpreter_discovery.html for more information.

2025-02-12 09:44:19,055 p=4590 u=vagrant n=ansible | target01 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3.10"
    },
    "changed": false,
    "ping": "pong"
}
...
```

### Créez un groupe [testlab] avec vos trois Target Hosts :
Je modifie le fichier inventaire "hosts" comme ceci :
```
[testlab]
target01
target02
target03
```

### Définissez explicitement l’utilisateur vagrant pour la connexion à vos cibles :
J'ajoute les lignes suivantes au fichier "hosts" :
```
[testlab:vars]
ansible_python_interpreter=/usr/bin/python3
ansible_user=vagrant
```
> J'en profite aussi pour ajouer la ligne ansible_python_interpreter pour ne plus avoir tous les warnings.

### Envoyez un ping Ansible vers le groupe de machines [all] :
```
(ansible) vagrant@control:~/monprojet$ ansible all -m ping
target02 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
target01 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
target03 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

### Définissez l’élévation des droits pour l’utilisateur vagrant sur les Target Hosts :
J'ajoute `ansible_become=yes` à la fin du fichier hosts.


### Affichez la première ligne du fichier /etc/shadow sur tous les Target Hosts :
```
(ansible) vagrant@control:~/monprojet$ ansible testlab -m command -a "head -n 1 /etc/shadow"
target01 | CHANGED | rc=0 >>
root:*:19769:0:99999:7:::
target02 | CHANGED | rc=0 >>
root:*:19769:0:99999:7:::
target03 | CHANGED | rc=0 >>
root:*:19769:0:99999:7:::
```

### Quittez le Control Host et supprimez toutes les VM de l’atelier :
```
$ exit
$ vagrant destroy -f
```