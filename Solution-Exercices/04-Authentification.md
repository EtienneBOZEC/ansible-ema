# Authentification

### Enoncé :
Placez-vous dans le répertoire du troisième atelier pratique :
```
$ cd ~/formation-ansible/atelier-03
```
Voici les quatre machines virtuelles Ubuntu 22.04 de cet atelier :
```
Machine virtuelle	Adresse IP
control	192.168.56.10
target01	192.168.56.20
target02	192.168.56.30
target03	192.168.56.40
```
Démarrez les VM :
```
$ vagrant up
```
Connectez-vous au Control Host :
```
$ vagrant ssh control
```
Ansible est déjà installé sur cette machine :
```
$ type ansible
ansible is /usr/bin/ansible
```
Faites le nécessaire pour réussir un ping Ansible comme ceci :
```
$ ansible all -i target01,target02,target03 -m ping
target03 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
target02 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
target01 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
```

### Réponses :

Après avoir mis en place les VMs, et repris les quelques commandes de l'énoncé, je modifie le fichier host de la VM ansible :
```
[vagrant@ansible ~]$ sudo nano /etc/hosts
[vagrant@ansible ~]$ cat /etc/hosts
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6

127.0.0.1 rocky9.localdomain

127.0.1.1 ansible ansible
192.168.56.10  control.sandbox.lan      control
192.168.56.20  target01.sandbox.lan    target01
192.168.56.30  target02.sandbox.lan    target02
192.168.56.40  target03.sandbox.lan    target03
```

Ensuite, comme dans la pratique je vérifie que je peux joindre toutes les VM:
```
[vagrant@ansible ~]$ for HOST in target01 target02 target03; do ping -c 1 -q $HOST; done
PING target01.sandbox.lan (192.168.56.20) 56(84) bytes of data.

--- target01.sandbox.lan ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 2.444/2.444/2.444/0.000 ms
PING target02.sandbox.lan (192.168.56.30) 56(84) bytes of data.

--- target02.sandbox.lan ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 2.047/2.047/2.047/0.000 ms
PING target03.sandbox.lan (192.168.56.40) 56(84) bytes of data.

--- target03.sandbox.lan ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 1.699/1.699/1.699/0.000 ms
```

J'utilise la commande suivante pour ajouter les clés publiques de chaque VM au fichier `know_hosts` :
```
[vagrant@ansible ~]$ ssh-keyscan -t rsa target01 target02 target03 >> .ssh/known_hosts
# target03:22 SSH-2.0-OpenSSH_8.4
# target01:22 SSH-2.0-OpenSSH_8.7
# target02:22 SSH-2.0-OpenSSH_9.2p1 Debian-2+deb12u2
```

Je vais maintenant générer une paire de clés publique/privée pour me connecter à ces VM sans utiliser l'authentification par mot de passe :
```
[vagrant@ansible ~]$ ssh-keygen
```

Je copie ma clé publique sur les VM debian et suse afin de pouvoir me connecter dessus proprement :
```
[vagrant@ansible ~]$ ssh-copy-id vagrant@target02
[vagrant@ansible ~]$ ssh-copy-id vagrant@target03
```

Pour la VM Rocky il faut que j'authorise la connexion par mot de passe avant de pouvoir lui passer ma clé privée :
```
[ema@localhost:atelier-02] $ vagrant ssh rocky
[vagrant@rocky ~]$ sudo vim /etc/ssh/sshd_config
```

Je modifie la ligne suivante :
```
PasswordAuthentication yes
```
J'enregistre et je restart le service sshd :
```
[vagrant@rocky ~]$ sudo systemctl restart sshd
[vagrant@rocky ~]$ systemctl status sshd
[vagrant@rocky ~]$ exit
```

Je peux maintenant importer la clé publique sur la VM Rocky :
```
[ema@localhost:atelier-02] $ vagrant ssh ansible
[vagrant@ansible ~]$ ssh-copy-id vagrant@target01
```

Je dois maintenant être capable de lancer un ping vers chacun de mes VM en utilisant ansible :
```
[vagrant@ansible ~]$ ansible all -i target02,target03,target01 -m ping
target02 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
[WARNING]: Platform linux on host target03 is using the discovered Python interpreter at /usr/bin/python3.6, but future
installation of another Python interpreter could change the meaning of that path. See
https://docs.ansible.com/ansible-core/2.14/reference_appendices/interpreter_discovery.html for more information.
target03 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3.6"
    },
    "changed": false,
    "ping": "pong"
}
target01 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
```

