Auteur: Ruben Rouvière
## Créer un compte Redhat

## Créer un compte Redhat Developper

https://developers.redhat.com/
-> Descendre jusqu'à l'option de création de compte

## Attacher le compte à l'OS

Note: si le compte n'a pas de subscription active, l'installation échoue avec un message peu claire "pas de source de paquets".
Une réinstallation semble nécessaire même après contournement avec un support hors-ligne (le système SCA ne semble pas comprendre de fonction permettant de recréer les repos).

## Vérifier que la subscription est active

https://access.redhat.com/management/subscriptions

subscription-manager list --available

## Mettre à jour le système

