Conception Active Directory

Introduction

L’infrastructure Active Directory constitue le cœur de l’environnement SecureTech.

Elle assure la gestion centralisée des identités, des ressources et des politiques de sécurité.
Le domaine configuré est :
corp.securetech.local

Le contrôleur de domaine fournit les services suivants :
Authentification (Kerberos / NTLM)
Service DNS interne
Gestion des utilisateurs et des machines
Application des stratégies de groupe (GPO)
Le DNS est entièrement géré par Active Directory, avec une redirection vers des serveurs externes pour la résolution Internet.
Structure des unités d’organisation (OU)
L’organisation des objets Active Directory repose sur une structure hiérarchique permettant une administration claire et une application ciblée des politiques.
OU principales

Admin_Accounts
Groups
DomainLocal
Global
Ordinateurs
Admin Workstation
Laptops
Workstation
Servers
AppServers
FileServers
UpdateServer
UTILISATEUR
Direction
Finance
IT
RH

Cette structure permet de séparer les rôles, les ressources et les utilisateurs selon des critères métiers et techniques.
Gestion des ordinateurs (Domain Join)

L’intégration des machines au domaine est sécurisée via un compte dédié :
srv-join
Configuration appliquée :
Suppression du quota par défaut (10 machines par utilisateur)
Limitation à 0

Seul le compte srv-join est autorisé à joindre des machines au domaine
Une délégation de contrôle a été mise en place afin de permettre ce processus sans accorder de privilèges excessifs.
Les ordinateurs joints au domaine sont automatiquement redirigés vers :
OU Ordinateurs / Workstation
Gestion des comptes
L’infrastructure distingue plusieurs types de comptes :
Comptes utilisateurs standards
Comptes administrateurs
Compte dédié à l’intégration domaine (srv-join)
Comptes de support et d’administration
Cette séparation permet de limiter les privilèges et de réduire la surface d’attaque.

Serveur de fichiers

Un serveur dédié est utilisé pour la gestion des données :
Nom : ST-FILESERVER01
Localisation : OU Servers / FileServers
Rôle :
Stockage centralisé des données
Partage réseau (SMB)
Contrôle d’accès basé sur Active Directory
L’accès aux ressources est géré via des groupes de sécurité et des stratégies de groupe (GPO), notamment pour le mappage automatique des lecteurs réseau.
Gestion des groupes et contrôle d’accès
La gestion des accès repose sur le modèle AGDLP :
Accounts → Global → Domain Local → Permissions

Ce modèle permet une gestion claire, scalable et conforme aux bonnes pratiques.
Types de groupes utilisés
Groupes Globaux (GG)

Les groupes globaux contiennent les utilisateurs en fonction de leur rôle ou département.
Exemples :
GG_Direction_User
GG_Finance_User
GG_IT_User
GG_RH_User
GG_IT_Admin
GG_Server_Admin
GG_RDP_Access

Groupes Domain Local (DL)
Les groupes domain local sont utilisés pour attribuer des droits sur les ressources.
Exemples :
DL_Share_Finance_R
DL_Share_Finance_RW
DL_Share_IT_R
DL_Share_IT_RW
DL_RDP_Access
DL_Share_Admin_RW

Application du modèle AGDLP
Le modèle est appliqué de la manière suivante :
Les utilisateurs sont membres de groupes globaux (GG)
Les groupes globaux sont membres de groupes domain local (DL)
Les groupes domain local reçoivent les permissions sur les ressources

Exemple concret
Pour le service Finance :
Les utilisateurs sont membres de :
GG_Finance_User
Ce groupe est membre de :
DL_Share_Finance_R ou DL_Share_Finance_RW
Le groupe Domain Local possède les droits sur le dossier Finance situé sur :
ST-FILESERVER01
Gestion des accès aux partages
Les accès aux dossiers sont définis selon les besoins métiers :
R (Read) : accès en lecture seule
RW (Read/Write) : accès en lecture et écriture
Les permissions ne dépendent pas du type de compte (admin ou utilisateur), mais des besoins fonctionnels.

Accès administrateur
Les accès administratifs sont séparés et contrôlés :
Les groupes administrateurs (GG) sont distincts des groupes utilisateurs
Les accès RDP sont gérés via :
DL_RDP_Access
Les accès aux ressources administratives (ex : partages) sont gérés via :
DL_Share_Admin_RW
Cette approche permet de respecter le principe du moindre privilège.

Interaction réseau
Les postes clients :
Utilisent le DNS du contrôleur de domaine
S’authentifient auprès d’Active Directory
Accèdent aux ressources internes (serveur de fichiers)
Pour l’accès Internet :
Le trafic est redirigé vers pfSense
Le firewall applique les règles de filtrage et de NAT

Conclusion

L’architecture Active Directory mise en place repose sur :
Une organisation claire des OU
Une séparation des rôles et des privilèges
Une gestion des accès basée sur le modèle AGDLP
Une centralisation des ressources via un serveur de fichiers dédié
Cette approche permet :
Une administration efficace
Une meilleure sécurité des accès
Une réduction des risques liés aux privilèges excessifs