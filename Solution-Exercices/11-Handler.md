# Handler

## Enoncé:
Écrivez un playbook chrony.yml qui assure la synchronisation NTP de tous vos Target Hosts :

- Installez le paquet chrony.
- Activez et démarrez le service chronyd correspondant.
- Jetez un œil sur le fichier de configuration /etc/chrony.conf fourni par défaut.
- Installez une configuration personnalisée (cf. ci-dessous).
- Prenez en compte cette nouvelle configuration.
- Vérifiez la syntaxe correcte de votre playbook chrony.yml.
- Vérifiez l’idempotence de votre playbook.
- Voici la configuration à installer :

```
# /etc/chrony.conf
server 0.fr.pool.ntp.org iburst
server 1.fr.pool.ntp.org iburst
server 2.fr.pool.ntp.org iburst
server 3.fr.pool.ntp.org iburst
driftfile /var/lib/chrony/drift
makestep 1.0 3
rtcsync
logdir /var/log/chrony
```

## Réponses:

