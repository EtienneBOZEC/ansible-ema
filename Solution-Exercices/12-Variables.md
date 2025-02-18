# Variables

### Écrivez un playbook `myvars1.yml` qui affiche respectivement votre voiture et votre moto préférée en utilisant le module `debug` et deux variables `mycar` et `mybike` définies en tant que play vars.
```
---  # myvars1.yml

- hosts: all
  gather_facts: false

  vars:
    mycar: "BMW E28 528i"
    mybike: "Triumph Trident 660"

  tasks:
    - debug:
        msg: "My car: {{mycar}}, \nMy bike: {{mybike}}"

...
```
Résultats du playbook:
```
[vagrant@control ema]$ ansible-playbook playbooks/myvars1.yml 

PLAY [all] ************************************************************************************************************

TASK [debug] **********************************************************************************************************
ok: [target01] => {
    "msg": "My car: BMW E28 528i, \nMy bike: Triumph Trident 660"
}
ok: [target02] => {
    "msg": "My car: BMW E28 528i, \nMy bike: Triumph Trident 660"
}
ok: [target03] => {
    "msg": "My car: BMW E28 528i, \nMy bike: Triumph Trident 660"
}
...
```

### En utilisant les extra vars, remplacez successivement l’une et l’autre marque – puis les deux à la fois – avant d’exécuter le play.
```
[vagrant@control ema]$ ansible-playbook playbooks/myvars1.yml -e mycar="Honda"

PLAY [all] ************************************************************************************************************

TASK [debug] **********************************************************************************************************
ok: [target01] => {
    "msg": "My car: Honda, \nMy bike: Triumph Trident 660"
}
```
```
[vagrant@control ema]$ ansible-playbook playbooks/myvars1.yml -e mybike="Yamaha"

PLAY [all] ************************************************************************************************************

TASK [debug] **********************************************************************************************************
ok: [target01] => {
    "msg": "My car: BMW E28 528i, \nMy bike: Yamaha"
}
```
```
[vagrant@control ema]$ ansible-playbook playbooks/myvars1.yml -e mybike="Yamaha" -e mycar="Honda"

PLAY [all] ************************************************************************************************************

TASK [debug] **********************************************************************************************************
ok: [target01] => {
    "msg": "My car: Honda, \nMy bike: Yamaha"
}
```

### Écrivez un playbook `myvars2.yml` qui fait essentiellement la même chose que `myvars1.yml`, mais en utilisant une tâche avec `set_fact` pour définir les deux variables.
```
---  # myvars2.yml

- hosts: all
  gather_facts: false

  tasks:
    - name: Define variables
      set_fact:
        mycar: "BMW E28 528i"
        mybike: "Triumph Trident 660"

    - debug:
        msg: "My car: {{mycar}}, My bike: {{mybike}}"

...
```
Résultats du playbook:
```
[vagrant@control ema]$ ansible-playbook playbooks/myvars2.yml 

PLAY [all] ************************************************************************************************************

TASK [Define variables] ***********************************************************************************************
ok: [target01]
ok: [target02]
ok: [target03]

TASK [debug] **********************************************************************************************************
ok: [target01] => {
    "msg": "My car: BMW E28 528i, My bike: Triumph Trident 660"
}
ok: [target02] => {
    "msg": "My car: BMW E28 528i, My bike: Triumph Trident 660"
}
ok: [target03] => {
    "msg": "My car: BMW E28 528i, My bike: Triumph Trident 660"
}
...
```

### Là aussi, essayez de remplacer les deux variables en utilisant des extra vars avant l’exécution du play.
```
[vagrant@control ema]$ ansible-playbook playbooks/myvars2.yml -e mybike="Yamaha" -e mycar="Honda"

PLAY [all] ************************************************************************************************************

TASK [Define variables] ***********************************************************************************************
ok: [target01]
ok: [target02]
ok: [target03]

TASK [debug] **********************************************************************************************************
ok: [target01] => {
    "msg": "My car: Honda, My bike: Yamaha"
}
ok: [target02] => {
    "msg": "My car: Honda, My bike: Yamaha"
}
ok: [target03] => {
    "msg": "My car: Honda, My bike: Yamaha"
}
...
```
> Ca fonctionne, ce qui est normal puisque les extra vars priment sur toutes les autres variables.

### Écrivez un playbook `myvars3.yml` qui affiche le contenu des deux variables `mycar` et `mybike` mais sans les définir. Avant d’exécuter le playbook, définissez `VW` et `BMW` comme valeurs par défaut pour `mycar` et `mybike` pour tous les hôtes, en utilisant l’endroit approprié.
```
[vagrant@control ema]$ mkdir group_vars && cd group_vars
```
```
---  # group_vars/all.yml

mycar: VW
mybike: BMW

...
```
Résultats du playbook :
```
[vagrant@control ema]$ ansible-playbook playbooks/myvars3.yml 

PLAY [all] ************************************************************************************************************

TASK [debug] **********************************************************************************************************
ok: [target01] => {
    "msg": "My car: VW, My bike: BMW"
}
ok: [target03] => {
    "msg": "My car: VW, My bike: BMW"
}
ok: [target02] => {
    "msg": "My car: VW, My bike: BMW"
}
...
```

### Effectuez le nécessaire pour remplacer `VW` et `BMW` par `Mercedes` et `Honda` sur l’hôte target02.
```
---  # host_vars/target02.yml

mycar: Mercedes
mybike: Honda
  
...
```
Résultats du playbook:
```
[vagrant@control ema]$ ansible-playbook playbooks/myvars3.yml 

PLAY [all] ************************************************************************************************************

TASK [debug] **********************************************************************************************************
ok: [target01] => {
    "msg": "My car: VW, My bike: BMW"
}
ok: [target02] => {
    "msg": "My car: Mercedes, My bike: Honda"
}
ok: [target03] => {
    "msg": "My car: VW, My bike: BMW"
}
...
```

### Écrivez un playbook `display_user.yml` qui affiche un utilisateur et son mot de passe correspondant à l’aide des variables `user` et `password`. Ces deux variables devront être saisies de manière interactive pendant l’exécution du playbook. Les valeurs par défaut seront `microlinux` pour `user` et `yatahongaga` pour `password`. Le mot de passe ne devra pas s’afficher pendant la saisie.
```
---  # display_user.yml

- hosts: localhost
  gather_facts: false

  vars_prompt:
    - name: user
      prompt: Please enter your username
      default: microlinux
      private: false

    - name: password
      prompt: Please enter your password
      default: yatahongaga
      private: true

  tasks:
    - debug:
        msg: "Username: {{user}}, Password: {{password}}"
...
```
Résultats du playbook:
```
[vagrant@control ema]$ ansible-playbook playbooks/display_user.yml
Please enter your username [microlinux]: test
Please enter your password [yatahongaga]: 

PLAY [localhost] ******************************************************************************************************

TASK [debug] **********************************************************************************************************
ok: [localhost] => {
    "msg": "Username: test, Password: mzoufigsifyugsdfidgv"
}

PLAY RECAP ************************************************************************************************************
localhost                  : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0  
```
> Les * n'apparaissent pas non plus lors de la frappe du mot de passe, c'est normal j'ai fait en sorte de les cacher pour tout mot de passe tapé dans le terminal.