File Server – ST-FILESERVER01

Introduction

L’infrastructure SecureTech intègre un serveur de fichiers dédié permettant la centralisation des données internes, le contrôle des accès et la gestion des ressources partagées.
Le serveur déployé est :
Plain text
ST-FILESERVER01

Ce serveur joue plusieurs rôles importants dans l’environnement :
serveur de fichiers SMB,
hébergement des partages départementaux,
stockage des ressources communes,
gestion centralisée des permissions,
hébergement du collecteur WEF/WEC,
collecte des logs pfSense via NXLog.

L’objectif est de reproduire une architecture de partage de fichiers réaliste en environnement d’entreprise.
Rôles du serveur
Le serveur assure plusieurs fonctions critiques :
centralisation des fichiers internes,
contrôle d’accès basé sur Active Directory,
gestion des lecteurs réseau,
collecte des événements Windows,
centralisation des logs firewall.

Cette approche permet de limiter la dispersion des données et d’améliorer l’administration des ressources.
Structure des partages
Partages départementaux
Des dossiers dédiés ont été créés pour les différents départements de l’entreprise :
Finance
RH
IT
Direction
Chaque département dispose de ressources isolées avec des permissions spécifiques.
Ressources communes

Un espace partagé commun est également disponible afin de permettre l’échange de fichiers entre plusieurs équipes selon les besoins métiers.
Les accès sont contrôlés via des groupes Active Directory.
Gestion des permissions
Modèle AGDLP
Le contrôle des accès repose sur le modèle :
Plain text
Accounts → Global → Domain Local → Permissions
Cette approche permet :
une administration simplifiée,
une séparation claire des rôles,
une gestion centralisée des permissions,
une meilleure évolutivité.
Permissions NTFS et SMB
Le serveur utilise :
permissions NTFS,
permissions de partage SMB.

Les droits sont attribués via des groupes Active Directory afin d’éviter les permissions directes sur les utilisateurs.
Types d’accès
Les permissions sont organisées selon les besoins métiers :
R (Read)
RW (Read / Write)
Les autorisations sont attribuées via des groupes Domain Local associés aux groupes Globaux des différents départements.
Lecteurs réseau

Les lecteurs réseau sont déployés automatiquement via :
Group Policy Preferences (Drive Maps)
Cette méthode permet :
une configuration centralisée,
une administration simplifiée,
un déploiement automatique des partages utilisateurs.
Les lecteurs réseau sont attribués dynamiquement selon les groupes Active Directory des utilisateurs.
Intégration Windows Event Forwarding (WEF)
Le serveur ST-FILESERVER01 héberge également le rôle :
Windows Event Collector (WEC)
Les événements Windows collectés via WEF proviennent :
du contrôleur de domaine,
des postes clients,
des serveurs internes,
des postes administrateurs.
Les événements restent centralisés dans l’Event Viewer du serveur collecteur.
Intégration NXLog et Syslog pfSense
Le serveur intègre également :
NXLog,
collecte Syslog pfSense.
Le pare-feu pfSense transmet ses journaux via Syslog vers le serveur.
NXLog est ensuite utilisé pour :
lire les fichiers Syslog,
centraliser les événements réseau,
stocker les logs firewall dans des fichiers dédiés.
Les journaux collectés incluent :
firewall logs,
NAT logs,
DHCP logs,
logs système.

Les fichiers de logs sont stockés dans des dossiers internes non partagés.
Gestion du stockage
File Server Resource Manager (FSRM)
Le serveur utilise :
Plain text
FSRM (File Server Resource Manager)
afin de contrôler l’utilisation du stockage.
Quotas
Des quotas de stockage sont appliqués afin de :
limiter l’utilisation excessive de l’espace disque,
contrôler la consommation des ressources,
améliorer la gestion des données.

Filtrage de fichiers
Un filtrage d’extensions a été configuré afin de limiter certains types de fichiers potentiellement dangereux.
Les extensions bloquées incluent notamment :
.bat
.ps1
scripts

Cette approche permet de réduire certains risques liés à l’exécution de scripts malveillants ou non autorisés.
Sécurité
Le serveur applique plusieurs principes de sécurité :
séparation des accès,
gestion centralisée des permissions,
moindre privilège,
segmentation réseau,
isolation administrative.

Les accès aux ressources sont exclusivement contrôlés via Active Directory et les groupes de sécurité.
Bénéfices de l’implémentation
L’implémentation du serveur de fichiers permet :
une centralisation des données,
une gestion simplifiée des accès,
une meilleure visibilité sur les événements,
une administration centralisée,
une réduction des privilèges excessifs,
une meilleure supervision des activités internes.

Conclusion

Le serveur ST-FILESERVER01 constitue un composant central de l’infrastructure SecureTech.
Au-delà du simple partage de fichiers, il participe également :
à la centralisation des événements Windows,
à la collecte des logs réseau,
à la gestion des permissions,
à l’application des politiques de sécurité.
Cette approche permet de reproduire une architecture de services internes proche d’un environnement d’entreprise réel.
