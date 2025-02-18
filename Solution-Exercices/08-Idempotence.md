# Idempotence

## Enoncé :
Pour vous familiariser avec la notion d’idempotence, exécutez une série de tâches administratives sur toutes les machines cibles. Pour ce faire, servez-vous des commandes ad hoc que nous avons vues dans le précédent article. Prenez soin d’exécuter chaque commande deux fois et observez ce qui se passe dans l’affichage du résultat.

## Réponses :

### Installez successivement les paquets tree, git et nmap sur toutes les cibles :
```
[vagrant@ansible ema]$ ansible testing -m package -a "name=tree,git,nmap state=present"
```
Les résultats sont très verbeux donc je vais éviter de les inclure. Les résultats sont tous en `"changed":true`.
Lorsque je relance la même commande, tout est en `"changed":false`.
```
debian | SUCCESS => {
    "cache_update_time": 1704860215,
    "cache_updated": false,
    "changed": false
}
suse | SUCCESS => {
    "changed": false,
    "name": [
        "tree",
        "git",
        "nmap"
    ],
    "rc": 0,
    "state": "present",
    "update_cache": false
}
rocky | SUCCESS => {
    "changed": false,
    "msg": "Nothing to do",
    "rc": 0,
    "results": []
}
```

### Désinstallez successivement ces trois paquets en utilisant le paramètre supplémentaire state=absent :
```
[vagrant@ansible ema]$ ansible testing -m package -a "name=tree,git,nmap state=absent"
```
Au deuxième run de la commande :
```
debian | SUCCESS => {
    "changed": false
}
suse | SUCCESS => {
    "changed": false,
    "name": [
        "tree",
        "git",
        "nmap"
    ],
    "rc": 0,
    "state": "absent",
    "update_cache": false
}
rocky | SUCCESS => {
    "changed": false,
    "msg": "Nothing to do",
    "rc": 0,
    "results": []
}
```

### Copier le fichier /etc/fstab du Control Host vers tous les Target Hosts sous forme d’un fichier /tmp/test3.txt :
```
[vagrant@ansible ema]$ ansible testing -m copy -a "src=/etc/fstab dest=/tmp/test3.txt"
debian | CHANGED => {
    "changed": true,
    "checksum": "d39263691e31170df199aae3d93f7c556fbb3446",
    "dest": "/tmp/test3.txt",
    "gid": 0,
    "group": "root",
    "md5sum": "b6f1fe0d790a8d2f9b74b95df1c889dc",
    "mode": "0644",
    "owner": "root",
    "size": 743,
    "src": "/home/vagrant/.ansible/tmp/ansible-tmp-1739357560.0802925-5995-281351321058547/source",
    "state": "file",
    "uid": 0
}
suse | CHANGED => {
    "changed": true,
    "checksum": "d39263691e31170df199aae3d93f7c556fbb3446",
    "dest": "/tmp/test3.txt",
    "gid": 0,
    "group": "root",
    "md5sum": "b6f1fe0d790a8d2f9b74b95df1c889dc",
    "mode": "0644",
    "owner": "root",
    "size": 743,
    "src": "/home/vagrant/.ansible/tmp/ansible-tmp-1739357560.0480947-5996-23771059330919/source",
    "state": "file",
    "uid": 0
}
rocky | CHANGED => {
    "changed": true,
    "checksum": "d39263691e31170df199aae3d93f7c556fbb3446",
    "dest": "/tmp/test3.txt",
    "gid": 0,
    "group": "root",
    "md5sum": "b6f1fe0d790a8d2f9b74b95df1c889dc",
    "mode": "0644",
    "owner": "root",
    "secontext": "unconfined_u:object_r:user_home_t:s0",
    "size": 743,
    "src": "/home/vagrant/.ansible/tmp/ansible-tmp-1739357563.5483568-5994-141272711498905/source",
    "state": "file",
    "uid": 0
}
```
> Encore une fois, sur le second run de la commande aucune modification n'est effectuée. 

### Supprimez le fichier /tmp/test3.txt sur les Target Hosts en utilisant le module file avec le paramètre state=absent :
```
[vagrant@ansible ema]$ ansible testing -m file -a "dest=/tmp/test3.txt state=absent"
debian | CHANGED => {
    "changed": true,
    "path": "/tmp/test3.txt",
    "state": "absent"
}
suse | CHANGED => {
    "changed": true,
    "path": "/tmp/test3.txt",
    "state": "absent"
}
rocky | CHANGED => {
    "changed": true,
    "path": "/tmp/test3.txt",
    "state": "absent"
}
```
> Idem, plus rien à faire.

### Enfin, affichez l’espace utilisé par la partition principale sur tous les Target Hosts. Que remarquez-vous ?
```
[vagrant@ansible ema]$ ansible testing -m shell -a "df -h /"
debian | CHANGED | rc=0 >>
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda3       124G  2.3G  115G   2% /
suse | CHANGED | rc=0 >>
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda3       124G  2.8G  118G   3% /
rocky | CHANGED | rc=0 >>
Filesystem                  Size  Used Avail Use% Mounted on
/dev/mapper/rl_rocky9-root   70G  2.4G   68G   4% /
```
Dans le cas de cette commande, peut importe le nombre de fois que l'on execute l'instruction elle nous retourne `CHANGED`. En effet cette commande ne modifie rien sur le système cible, elle retourne simplement une information. Il y a toujours quelque chose à faire.