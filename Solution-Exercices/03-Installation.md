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
Les commandes sont pratiquement les mêmes que dans l'exercice 1 :
```
$ vagrant up ubuntu
$ vagrant ssh ubuntu
$ sudo apt update
```
```
$ sudo apt-add-repository ppa:ansible/ansible
$ apt search --names-only ansible
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

## Exercice 3

