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

