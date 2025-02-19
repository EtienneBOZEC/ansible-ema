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


### chrony-02.yml