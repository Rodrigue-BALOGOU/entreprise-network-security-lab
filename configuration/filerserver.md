# File Server – ST-FILESERVER01

## Introduction

Dans une infrastructure d’entreprise, le serveur de fichiers joue un rôle central dans la gestion des données, le contrôle des accès et la centralisation des services internes.

Dans le laboratoire **SecureTech**, un serveur dédié nommé **ST-FILESERVER01** a été déployé afin de reproduire les pratiques couramment rencontrées dans les environnements professionnels.

Le serveur assure plusieurs fonctions essentielles :

- Partage de fichiers SMB
- Hébergement des ressources départementales
- Contrôle d’accès basé sur Active Directory
- Déploiement automatique des lecteurs réseau
- Hébergement du collecteur Windows Event Forwarding (WEF/WEC)
- Centralisation des journaux pfSense via NXLog
- Gestion des quotas et du filtrage de fichiers avec FSRM
- Protection des données grâce aux Shadow Copies

---

## Situation

L’objectif était de mettre en place une infrastructure de stockage centralisée permettant :

- de limiter les données stockées localement sur les postes utilisateurs ;
- de contrôler précisément les accès aux ressources ;
- d'appliquer le principe du moindre privilège ;
- de centraliser les événements de sécurité ;
- de faciliter l'administration quotidienne.

Le serveur de fichiers constitue donc un élément clé de l’environnement SecureTech.

---

## Déploiement du serveur

Le serveur a été intégré au domaine Active Directory puis placé dans l’unité d’organisation dédiée aux serveurs de fichiers.

### Serveur membre du domaine

![Serveur membre du domaine](../screenshots/configuration/fileserver/01-file-server-domain-member.png)

### Emplacement du serveur dans Active Directory

![OU FileServers](../screenshots/configuration/fileserver/02-fileserver-ou-location.png)

---

## Structure des partages

Une arborescence de dossiers a été créée afin de séparer les données selon les besoins métiers de l’entreprise.

### Structure générale des dossiers

![Structure des dossiers](../screenshots/configuration/fileserver/03-shared-folders-structure.png)

Les principaux partages sont :

- Finance
- RH
- IT
- Direction

Chaque département dispose de son propre espace de travail avec des permissions spécifiques.

---

## Configuration des partages SMB

Les partages réseau sont publiés via SMB afin d'être accessibles aux utilisateurs du domaine.

### Propriétés du partage Finance

![Propriétés du partage Finance](../screenshots/configuration/fileserver/04-finance-share-properties.png)

### Permissions SMB du partage Finance

![Permissions SMB](../screenshots/configuration/fileserver/05-finance-share-permissions.png)

### Permissions NTFS du dossier Finance

![Permissions NTFS](../screenshots/configuration/fileserver/06-finance-ntfs-permissions.png)

### Console SMB Server

![Console SMB](../screenshots/configuration/fileserver/13-smb-share-console.png)

### Gestion des dossiers partagés

![Gestion des partages](../screenshots/configuration/fileserver/15-shared-folder-management.png)

---

## Contrôle d'accès basé sur Active Directory

### Modèle AGDLP

L’attribution des droits repose sur le modèle :

```text
Accounts → Global Groups → Domain Local Groups → Permissions
```

Cette méthode facilite l’administration et améliore la sécurité.

### Implémentation du modèle AGDLP

![AGDLP](../screenshots/configuration/fileserver/07-agdlp-implementation.png)

---

## Validation des accès

Plusieurs tests ont été réalisés afin de vérifier le respect des permissions configurées.

### Accès autorisé au partage Finance

![Accès autorisé](../screenshots/configuration/fileserver/10-user-access-finance-share.png)

### Accès refusé à une ressource non autorisée

![Accès refusé](../screenshots/configuration/fileserver/11-user-denied-other.png)

### Visibilité des lecteurs réseau

![Lecteur réseau](../screenshots/configuration/fileserver/12-network-drive-visible.png)

### Test Effective Access - Partie 1

![Effective Access 1](../screenshots/configuration/fileserver/16-effective-access-test-part1.png)

### Test Effective Access - Partie 2

![Effective Access 2](../screenshots/configuration/fileserver/17-effective-access-test-part2.png)

### Access Based Enumeration (ABE)

![ABE](screenshots/configuration/fileserver/13-access-based-enumeration.png)

L’activation d’Access Based Enumeration permet de masquer automatiquement les dossiers auxquels un utilisateur n’a pas accès.

### Tentative de création refusée

![Création refusée](../screenshots/configuration/fileserver/14-refuse-create-folder.png)

---

## Déploiement automatique des lecteurs réseau

Les lecteurs réseau sont déployés via Group Policy Preferences.

### Configuration du Drive Mapping

![Drive Mapping](../screenshots/configuration/fileserver/09-Drive-map-Finance.png)

### Application de la stratégie après GPUpdate

![GPUpdate](../screenshots/configuration/fileserver/18-gpupdate-drive-mapping.png)

Cette méthode garantit un déploiement automatique des ressources en fonction de l'appartenance aux groupes Active Directory.

---

## Gestion des groupes locaux du serveur

Le serveur utilise également des groupes locaux afin de déléguer certaines tâches administratives spécifiques.

![Groupes locaux](../screenshots/configuration/fileserver/19-file-server-local-group.png)

---

## Gestion du stockage avec FSRM

Afin de contrôler l’utilisation du stockage, le rôle **File Server Resource Manager (FSRM)** a été déployé.

### Tableau de bord FSRM

![FSRM Dashboard](../screenshots/configuration/fileserver/25-fsrm-dashboard.png)

### Configuration des modèles de quotas

![Quota Templates](../screenshots/configuration/fileserver/20-quota-templates-configuration.png)

### Détails d'un modèle de quota

![Quota Details](../screenshots/configuration/fileserver/26-quota-template-details.png)

### Application d'un quota

![Quota Applied](../screenshots/configuration/fileserver/21-folder-quota-applied.png)

### Vérification de l'application

![Quota Verification](../screenshots/configuration/fileserver/23-file-screen-applied.png)

---

## Filtrage de fichiers

Le filtrage de fichiers permet d'empêcher le stockage de certains types de fichiers à risque.

### Modèles de filtrage

![File Screen Templates](../screenshots/configuration/fileserver/22-file-screen-templates.png)

### Modèle utilisé

![Template Details](../screenshots/configuration/fileserver/28-file-screen-template-detail.png)

### Configuration du filtrage

![File Screen Template](../screenshots/configuration/fileserver/27-file-screen-template.png)

### Application du filtrage

![File Screen Applied](../screenshots/configuration/fileserver/29-file-screen-template.png)

### Test de blocage

![Test de blocage](../screenshots/configuration/fileserver/24-File-screen-test-denied.png)

Les extensions à risque telles que :

- .bat
- .cmd
- .ps1
- scripts administratifs

sont bloquées afin de réduire la surface d’attaque.

---

## Protection des données avec Shadow Copies

Afin de permettre la restauration rapide de fichiers supprimés ou modifiés, les Shadow Copies ont été activées.

### Activation des Shadow Copies

![Activation Shadow Copy](../screenshots/configuration/fileserver/31-Activation-copy-shadow.png)

### Configuration de la planification

![Planification](../screenshots/configuration/fileserver/32-Config-planification-shadow-copy.png)

### Instantanés configurés

![Snapshots](../screenshots/configuration/fileserver/33-shadow-copy-snaps.png)

### Fichier avant modification

![Avant modification](../screenshots/configuration/fileserver/34-File-before-modification-shadow-copy.png)

### Versions précédentes disponibles

![Versions précédentes](../screenshots/configuration/fileserver/35-File-before-aftershadow-copy.png)

### Restauration depuis une Shadow Copy

![Restauration](../screenshots/configuration/fileserver/36-list-shadow-copy-restaure.png)

Cette fonctionnalité améliore significativement la résilience face aux suppressions accidentelles.

---

## Collecte des événements Windows (WEF)

Le serveur héberge également le rôle Windows Event Collector.

### Collecteur WEF

![WEF Collector](../screenshots/configuration/fileserver/37-wef-collector.png)

Les événements provenant :

- du contrôleur de domaine ;
- des postes utilisateurs ;
- des serveurs ;
- des postes administrateurs ;

sont centralisés afin de faciliter les investigations de sécurité.

---

## Résultat

L’implémentation de ST-FILESERVER01 a permis :

- la centralisation des données ;
- l'application du modèle AGDLP ;
- le déploiement automatisé des lecteurs réseau ;
- le contrôle des quotas de stockage ;
- le filtrage des fichiers sensibles ;
- la restauration rapide grâce aux Shadow Copies ;
- la centralisation des événements Windows ;
- la préparation de l’infrastructure aux futures phases de monitoring et de détection.

---

## Conclusion

Le serveur **ST-FILESERVER01** constitue un composant essentiel de l’infrastructure SecureTech.

Au-delà du simple partage de fichiers, il participe à la sécurisation des accès, à la gouvernance des données, à la supervision des événements et à l’application des bonnes pratiques d’administration système.

Cette implémentation reproduit fidèlement les mécanismes utilisés dans de nombreuses infrastructures d’entreprise modernes.
