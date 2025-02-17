# Un serveur web simple

## Enoncé :

### Écrivez trois playbooks :

Un premier playbook apache-debian.yml qui installe Apache sur l’hôte debian avec une page personnalisée Apache web server running on Debian Linux.
Un deuxième playbook apache-rocky.yml qui installe Apache sur l’hôte rocky avec une page personnalisée Apache web server running on Rocky Linux.
Un troisième playbook apache-suse.yml qui installe Apache sur l’hôte suse avec une page personnalisée Apache web server running on SUSE Linux.

### Voici quelques pistes de réflexion :

Pas la peine de rafraîchir le cache de paquets sous Rocky Linux et SUSE.
Apache n’a pas forcément le même nom sur ces distributions.
La même chose vaut pour le service correspondant.
Rocky Linux utilise le gestionnaire de paquets dnf.
SUSE Linux utilise le gestionnaire de paquets zypper.
Les modules de gestion de paquets correspondants portent le même nom.
L’emplacement de la page web par défaut (directive DocumentRoot) peut varier.
J’ai supprimé le pare-feu FirewallD installé par défaut sous Rocky Linux pour ne pas trop vous embrouiller.

## Réponses :

### apache-debian.yml

### apache-rocky.yml

### apache-suse.yml


