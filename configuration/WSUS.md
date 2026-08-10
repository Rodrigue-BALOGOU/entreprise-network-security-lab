# Gestion centralisée des mises à jour Microsoft avec WSUS

## Introduction

Dans une infrastructure Active Directory, la gestion individuelle des mises à jour Windows limite la visibilité de l'administrateur et réduit son contrôle sur le déploiement des correctifs.

Dans l'environnement SecureTech, les postes pouvaient initialement récupérer leurs mises à jour directement depuis Microsoft Update. Cette approche ne permettait pas de contrôler précisément les correctifs déployés ni de mettre en place une phase de validation avant leur diffusion sur les serveurs.

L'objectif a donc été de mettre en place une infrastructure **Windows Server Update Services (WSUS)** permettant de centraliser, contrôler et progressivement déployer les mises à jour Microsoft.

La solution mise en œuvre permet de transformer le processus de mise à jour en un cycle contrôlé :

> **Synchroniser → Approuver → Tester → Valider → Déployer**

---

# 1. Situation

Avant la mise en place de WSUS, les machines de l'infrastructure pouvaient récupérer directement leurs mises à jour auprès de Microsoft Update.

Cette organisation présentait plusieurs limites :

- absence de contrôle centralisé des mises à jour ;
- difficulté à suivre l'état du parc ;
- absence de groupe pilote pour tester les correctifs ;
- risque qu'une mise à jour problématique soit déployée simultanément sur plusieurs systèmes ;
- visibilité limitée sur les machines réellement à jour.

Dans une infrastructure comprenant des postes utilisateurs et plusieurs serveurs critiques, cette situation représentait un risque opérationnel.

---

# 2. Objectifs

La mise en place de WSUS avait pour objectifs de :

- centraliser les mises à jour Microsoft ;
- contrôler les correctifs disponibles ;
- approuver les mises à jour avant leur déploiement ;
- tester les correctifs sur un groupe pilote ;
- déployer progressivement les mises à jour ;
- suivre l'état des machines ;
- réduire le risque d'incident lié à un correctif problématique ;
- intégrer la gestion des mises à jour à l'environnement Active Directory.

---

# 3. Architecture

Le serveur **ST-WSUS** a été déployé sur **Windows Server 2022** et dédié au rôle WSUS.

Il récupère les mises à jour depuis Microsoft Update puis les distribue aux machines autorisées du domaine.

```text
                         INTERNET
                            │
                            ▼
                       ┌─────────┐
                       │ pfSense │
                       └────┬────┘
                            │
                            ▼
                    ┌─────────────────┐
                    │     ST-WSUS     │
                    │ Windows Server  │
                    │      2022       │
                    └────────┬────────┘
                             │
                          TCP 8530
                             │
             ┌───────────────┼───────────────┐
             ▼               ▼               ▼
        POSTES-ADMIN     UTILISATEURS      SERVEURS
             │               │               │
         PC-ADMIN          PC-IT          ST-DC01
                           PC-FINANCE     ST-FILESERVER01
                           PC-RH
                           PC-DIRECTION
```

### Composants

| Composant | Rôle |
|---|---|
| ST-WSUS | Serveur central de gestion des mises à jour |
| ST-DC01 | Active Directory et DNS |
| ST-FILESERVER01 | Serveur de fichiers |
| pfSense | Pare-feu et contrôle des flux réseau |
| PC-ADMIN | Poste pilote |
| PC-IT | Poste utilisateur |
| PC-FINANCE | Poste utilisateur |
| PC-RH | Poste utilisateur |
| PC-DIRECTION | Poste utilisateur |

Cette architecture permet de séparer le rôle de gestion des mises à jour du contrôleur de domaine tout en conservant une administration centralisée via Active Directory et les GPO.

---

# 4. Installation du serveur WSUS

## 4.1 Déploiement de Windows Server

Un serveur **Windows Server 2022 dédié** a été utilisé pour héberger WSUS.

Le choix d'un serveur dédié permet de séparer le service de gestion des mises à jour des autres rôles critiques de l'infrastructure.

Le rôle **Windows Server Update Services** a ensuite été installé sur **ST-WSUS**.

![Installation de WSUS](../screenshots/configuration/wsus/01-installation-wsus.png)

---

## 4.2 Stockage du contenu WSUS

Le contenu téléchargé par WSUS a été séparé de la partition système afin d'éviter que l'accumulation des mises à jour ne compromette l'espace disponible sur le système d'exploitation.

Le répertoire suivant a été utilisé :

```text
F:\WSUS
```

Cette organisation facilite également l'administration et la maintenance du stockage WSUS.

![Répertoire de stockage WSUS](../screenshots/configuration/wsus/02-wsus-content-folder.png)

---

## 4.3 Base de données WSUS

La solution **Windows Internal Database (WID)** a été retenue pour héberger la base de données WSUS.

Cette solution est adaptée à l'environnement du laboratoire et permet de conserver une architecture relativement simple sans introduire un serveur SQL supplémentaire.

![Configuration de la base WSUS](../screenshots/configuration/wsus/03-wsus-database.png)

---

# 5. Post-installation et résolution des erreurs

Après l'installation du rôle, une erreur est apparue lors de la phase de post-installation.

L'erreur rencontrée était notamment :

```text
System.UnauthorizedAccessException
Tentative d'exécution d'une opération non autorisée.
```

![Erreur de post-installation WSUS](../screenshots/configuration/wsus/04-wsus-permission-error.png)

L'analyse a montré que le problème concernait notamment les permissions associées au contenu et à certains éléments de configuration de WSUS.

Cette étape a nécessité une vérification des droits et de la configuration du serveur avant de poursuivre l'intégration.

Après correction, la procédure de post-installation a pu être finalisée.

![Post-installation WSUS finalisée](../screenshots/configuration/wsus/05-wsus-post-installation-success.png)

Cette phase de dépannage fait partie intégrante du déploiement : l'objectif n'était pas seulement d'installer le rôle WSUS, mais d'obtenir un service réellement opérationnel.

---

# 6. Configuration de la synchronisation

Une fois le serveur opérationnel, **ST-WSUS** a été configuré pour synchroniser les mises à jour depuis Microsoft Update.

Le serveur devient ainsi le point intermédiaire entre Microsoft et les machines du réseau interne.

```text
Microsoft Update
       │
       ▼
    ST-WSUS
       │
       ▼
 Approbation
       │
       ▼
Clients internes
```

![Configuration de la synchronisation WSUS](../screenshots/configuration/wsus/06-wsus-synchronization.png)

---

## 6.1 Sélection des produits et classifications

Les produits et classifications de mises à jour nécessaires à l'environnement ont été sélectionnés afin d'éviter de télécharger inutilement des mises à jour ne correspondant pas au parc.

Cette sélection permet notamment de maîtriser :

- le volume de données téléchargées ;
- l'espace de stockage utilisé ;
- le nombre de correctifs à administrer ;
- la pertinence des mises à jour proposées aux clients.

![Produits et classifications WSUS](../screenshots/configuration/wsus/07-wsus-classifications.png)

---

# 7. Organisation des groupes WSUS

Afin de mettre en œuvre un déploiement progressif, trois groupes principaux ont été configurés :

```text
POSTES-ADMIN
     │
     ▼
UTILISATEURS
     │
     ▼
SERVEURS
```

Le groupe **POSTES-ADMIN** constitue le groupe pilote.

Les mises à jour peuvent ainsi être validées sur un nombre limité de machines avant d'être progressivement déployées sur les postes utilisateurs puis sur les serveurs.

![Groupes WSUS](../screenshots/configuration/wsus/08-wsus-computer-groups.png)

Cette organisation réduit le risque qu'un correctif défectueux affecte simultanément l'ensemble du parc.

---

# 8. Intégration à Active Directory

## 8.1 Configuration des clients

Les machines du domaine ont été configurées pour utiliser **ST-WSUS** comme source interne de mises à jour.

Le serveur WSUS est accessible via son nom DNS :

```text
http://st-wsus.corp.securetech.local:8530
```

L'utilisation du FQDN permet de conserver une configuration cohérente avec l'infrastructure DNS du domaine.

![Configuration WSUS côté client](../screenshots/configuration/wsus/09-wsus-client-fqdn.png)

---

## 8.2 Déploiement avec les GPO

La configuration des clients est réalisée de manière centralisée grâce aux stratégies de groupe Active Directory.

Trois GPO ont été utilisées afin de séparer les politiques selon les catégories de machines :

```text
GPO - WSUS - Postes-Admin
GPO - WSUS - Utilisateurs
GPO - WSUS - Serveurs
```

Cette séparation permet de contrôler précisément le comportement des différentes catégories de systèmes.

Elle facilite également le diagnostic lorsqu'une machine ne communique pas correctement avec WSUS.

![GPO WSUS](../screenshots/configuration/wsus/10-wsus-gpo.png)

---

# 9. Déploiement progressif des mises à jour

La stratégie de déploiement repose sur une approche progressive.

Le processus est le suivant :

```text
Microsoft Update
       ↓
    ST-WSUS
       ↓
   Approbation
       ↓
 POSTES-ADMIN
       ↓
    Validation
       ↓
  UTILISATEURS
       ↓
    Validation
       ↓
    SERVEURS
```

Le groupe pilote permet de détecter les éventuels problèmes avant d'exposer les utilisateurs et les serveurs aux mêmes correctifs.

Cette approche réduit le risque opérationnel tout en conservant un processus de mise à jour régulier.

![Déploiement progressif WSUS](../screenshots/configuration/wsus/11-wsus-approval-deployment.png)

---

# 10. Gestion des mises à jour automatiques

Les postes clients recherchent automatiquement les mises à jour approuvées auprès du serveur **ST-WSUS**.

Le fonctionnement est alors le suivant :

```text
Poste client
     │
     ▼
GPO Active Directory
     │
     ▼
ST-WSUS
     │
     ▼
Mise à jour approuvée
     │
     ▼
Téléchargement
     │
     ▼
Installation
```

![Configuration Windows Update](../screenshots/configuration/wsus/12-windows-update-policy.png)

Cette configuration permet de supprimer la dépendance directe des postes vis-à-vis de Microsoft Update pour la gestion quotidienne des correctifs.

---

# 11. Validation du fonctionnement

Une fois les GPO appliquées, les machines clientes ont été vérifiées depuis l'interface WSUS.

La présence des postes dans les groupes correspondants permet de confirmer que la communication entre les clients et le serveur fonctionne correctement.

![Machines détectées dans WSUS](../screenshots/configuration/wsus/13-wsus-client-reporting.png)

Les informations remontées par les clients permettent à l'administrateur de suivre leur état et d'identifier les systèmes nécessitant une intervention.

---

# 12. Nettoyage de WSUS

Une opération de nettoyage a été réalisée afin de supprimer les données devenues inutiles et de limiter l'utilisation de l'espace disque.

Le nettoyage permet notamment de retirer les éléments obsolètes et de maintenir une base WSUS exploitable dans le temps.

![Nettoyage WSUS](../screenshots/configuration/wsus/14-wsus-cleanup.png)

Cette opération doit être intégrée aux tâches d'administration régulières du serveur afin d'éviter une croissance inutile du stockage.

---

# 13. Dépannage des clients WSUS

Lors des tests, certains postes ne remontaient pas correctement dans les groupes WSUS.

Le diagnostic a porté sur plusieurs composants :

- application des GPO ;
- ciblage des groupes WSUS ;
- services Windows Update ;
- service BITS ;
- communication réseau avec ST-WSUS ;
- résolution DNS.

Les services nécessaires ont d'abord été vérifiés :

```powershell
Get-Service BITS,Wuauserv
```

Lorsque cela était nécessaire, les services ont été démarrés :

```powershell
Start-Service BITS
Start-Service Wuauserv
```

![Vérification des services Windows Update](../screenshots/configuration/wsus/15-windows-update-services.png)

Une nouvelle recherche de mises à jour a ensuite été forcée côté client :

```cmd
UsoClient StartScan
UsoClient StartInteractiveScan
```

![Recherche forcée des mises à jour](../screenshots/configuration/wsus/16-windows-update-scan.png)

Cette démarche a permis de distinguer les problèmes liés à la stratégie de groupe de ceux liés au service Windows Update ou à la communication avec le serveur.

---

# 14. Communication réseau

Les clients communiquent avec **ST-WSUS** via le port :

```text
TCP 8530 / HTTP
```

Le flux suit le chemin suivant :

```text
Poste client
     │
     │ TCP 8530
     ▼
  ST-WSUS
```

Le trafic est contrôlé par **pfSense**, qui assure la segmentation et le filtrage des communications entre les différentes zones de l'infrastructure.

![Communication client vers WSUS](../screenshots/configuration/wsus/17-wsus-network-communication.png)

Cette architecture permet de conserver une maîtrise des flux nécessaires au fonctionnement du service.

---

# 15. Résultats obtenus

La mise en place de WSUS a permis de transformer la gestion des mises à jour en un processus centralisé et contrôlé.

Les tests réalisés ont permis de valider :

- l'installation du rôle WSUS ;
- la configuration du stockage ;
- la synchronisation avec Microsoft Update ;
- la sélection des produits et classifications ;
- la création des groupes WSUS ;
- l'intégration avec Active Directory ;
- l'application des GPO ;
- la communication entre les clients et ST-WSUS ;
- la détection des mises à jour ;
- le téléchargement des correctifs ;
- l'installation des mises à jour approuvées ;
- la remontée des informations dans la console WSUS.

Le processus final est désormais :

```text
Microsoft Update
       │
       ▼
   ST-WSUS
       │
       ▼
 Approbation
       │
       ▼
 POSTES-ADMIN
       │
       ▼
 Validation
       │
       ▼
 UTILISATEURS
       │
       ▼
 Validation
       │
       ▼
 SERVEURS
```

---

# 16. Bénéfices pour l'infrastructure

La centralisation des mises à jour apporte plusieurs bénéfices opérationnels.

### Contrôle

L'administrateur décide quelles mises à jour peuvent être déployées sur les différentes catégories de machines.

### Déploiement progressif

Les correctifs sont d'abord testés sur un groupe pilote avant leur déploiement plus large.

### Réduction du risque

Une mise à jour problématique peut être identifiée avant d'atteindre les serveurs critiques.

### Visibilité

La console WSUS fournit une vue centralisée de l'état des machines et des mises à jour.

### Administration centralisée

Les paramètres Windows Update sont distribués via Active Directory et les GPO.

### Maîtrise du réseau

Les postes utilisent le serveur interne WSUS plutôt que de gérer individuellement leur processus de mise à jour depuis Internet.

---

# 17. Compétences mises en œuvre

Cette implémentation a permis de mettre en pratique plusieurs compétences d'administration système et réseau :

`Windows Server 2022` · `WSUS` · `Active Directory` · `GPO` · `Windows Update` · `DNS` · `pfSense` · `TCP/IP` · `Administration système` · `Dépannage` · `Gestion des correctifs`

---

# Conclusion

La mise en place de **WSUS** permet désormais à l'infrastructure SecureTech de gérer les mises à jour Microsoft selon un processus centralisé, contrôlé et progressif.

L'intégration avec **Active Directory** et les **GPO** permet d'appliquer automatiquement les paramètres aux différentes catégories de machines, tandis que l'organisation en groupes WSUS permet de tester les correctifs avant leur déploiement sur les serveurs.

Le projet ne s'est pas limité à l'installation du rôle WSUS. Les problèmes de permissions rencontrés lors de la post-installation ainsi que les difficultés de remontée de certains clients ont nécessité des phases de diagnostic et de correction.

L'architecture finale permet ainsi de conserver la maîtrise du cycle de mise à jour :

> **Synchroniser → Approuver → Tester → Valider → Déployer**

Cette approche réduit le risque opérationnel lié aux mises à jour tout en améliorant la visibilité et le contrôle de l'administrateur sur l'ensemble du parc.
