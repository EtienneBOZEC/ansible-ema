# Installation

## Exercice 1

Démarrez la VM ubuntu depuis le répertoire atelier-01 :
```
$ vagrant up ubuntu
```

Connectez-vous à cette VM :
```
$ vagrant ssh ubuntu
```

Rafraîchissez les informations sur les paquets :
```
$ sudo apt update
```

Recherchez le paquet ansible avec les options qui vont bien :
```
$ apt search --names-only ansible
```

Installez le paquet officiel fourni par la distribution :
```
$ sudo apt install ansible -y
```

Vérifiez si l’installation s’est bien déroulée :
```
$ ansible --version
```

Notez la version d’Ansible :
```
ansible 2.10.8
  config file = None
  configured module search path = ['/home/vagrant/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/lib/python3/dist-packages/ansible
  executable location = /usr/bin/ansible
  python version = 3.10.12 (main, Mar 22 2024, 16:50:05) [GCC 11.4.0]
```
La version est assez ancienne, si on devait bosser avec il y aurait des problèmes de compatibilité.

Déconnectez-vous et supprimez la VM :
```
$ exit
$ vagrant destroy -f ubuntu
```

## Exercice 2

### Enoncé :
Répétez l’exercice précédent en configurant un dépôt PPA (Personal Package Archive) pour Ansible :
```
$ sudo apt-add-repository ppa:ansible/ansible
```
Notez la version fournie par ce dépôt tiers et comparez avec la version officielle de l’exercice précédent.

### Réponses :
Les commandes sont pratiquement les mêmes que dans l'exercice 1 :
```
$ vagrant up ubuntu
$ vagrant ssh ubuntu
$ sudo apt update
```
J'ajoute le repo PPA :
```
$ sudo apt-add-repository ppa:ansible/ansible
```

Je vérifie que le paquet est bien différent que celui des repo ubuntu :
```
$ apt search --names-only ansible
```

Enfin, j'installe ansible :
```
$ sudo apt install ansible -y
```

Check de la version installée : 
```
$ ansible --version
ansible [core 2.17.8]
  config file = /etc/ansible/ansible.cfg
  configured module search path = ['/home/vagrant/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/lib/python3/dist-packages/ansible
  ansible collection location = /home/vagrant/.ansible/collections:/usr/share/ansible/collections
  executable location = /usr/bin/ansible
  python version = 3.10.12 (main, Mar 22 2024, 16:50:05) [GCC 11.4.0] (/usr/bin/python3)
  jinja version = 3.0.3
  libyaml = True
```
On est sur une version qui est assez proche des dernières release (2.18.X).

```
$ exit
$ vagrant destroy -f ubuntu
```

## Exercice 3

### Enoncé :
Lancez une VM Rocky Linux et installez Ansible en utilisant PIP et Virtualenv.

Notez bien que contrairement à Debian, le paquet python3-venv n’est pas nécessaire ici, étant donné que Virtualenv fait partie des modules standard de Python dans cette distribution.


### Réponses :
```
$ vagrant up rocky
$ vagrant ssh rocky
```
Pour update les repo sans faire l'upgrade j'utilise la commande suivante :
```
$ sudo dnf makecache
```

Et maintenant je mets en place le venv et j'installe ansible sur celui-ci :
```
$ sudo dnf install -y python3-pip
$ python3 -m venv ~/.venv/ansible
$ source ~/.venv/ansible/bin/activate
(ansible) $ pip install --upgrade pip
(ansible) $ pip install ansible
(ansible) $ ansible --version
ansible [core 2.15.13]
  config file = /etc/ansible/ansible.cfg
  configured module search path = ['/home/ema/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /home/ema/.venv/ansible/lib64/python3.9/site-packages/ansible
  ansible collection location = /home/ema/.ansible/collections:/usr/share/ansible/collections
  executable location = /home/ema/.venv/ansible/bin/ansible
  python version = 3.9.21 (main, Dec  5 2024, 00:00:00) [GCC 11.5.0 20240719 (Red Hat 11.5.0-2)] (/home/ema/.venv/ansible/bin/python3)
  jinja version = 3.1.5
  libyaml = True
```
