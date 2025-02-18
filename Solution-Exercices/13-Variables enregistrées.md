# Variables enregistrées

## Enoncé:
- Écrivez un playbook `kernel.yml` qui affiche les infos détaillées du noyau sur tous vos Target Hosts. Utilisez la commande `uname -a` et le module `debug` avec le paramètre `msg`.
- Essayez d’obtenir le même résultat en utilisant le paramètre `var` du module `debug`.
- Écrivez un playbook `packages.yml` qui affiche le nombre total de paquets RPM installés sur les hôtes rocky et suse (`rpm -qa | wc -l`).

## Réponses