# Gestion centralisée des mises à jour Microsoft dans une infrastructure Active Directory

## 1. Présentation

### 1.1 Contexte

Dans une infrastructure d'entreprise, le maintien à jour des systèmes d'exploitation constitue un élément essentiel de la politique de cybersécurité. Les correctifs publiés par Microsoft permettent de corriger des vulnérabilités, d'améliorer la stabilité des systèmes et de garantir leur conformité avec les exigences de sécurité.

Cependant, laisser chaque poste télécharger directement ses mises à jour depuis les serveurs Microsoft présente plusieurs inconvénients :

- Absence de contrôle sur les mises à jour installées.
- Risque de déployer automatiquement une mise à jour incompatible avec certaines applications ou certains pilotes.
- Consommation importante de la bande passante Internet lorsque chaque machine télécharge les mêmes fichiers.
- Difficulté à suivre l'état des mises à jour sur l'ensemble du parc informatique.

Afin de répondre à ces problématiques, une solution de gestion centralisée des mises à jour a été déployée à l'aide de **Windows Server Update Services (WSUS)** au sein d'une infrastructure **Active Directory**.

---

## 1.2 Objectif

L'objectif de cette implémentation est de mettre en place une solution centralisée permettant de gérer les mises à jour Microsoft au sein de l'entreprise.

Cette architecture permet notamment de :

- Centraliser le téléchargement des mises à jour Microsoft.
- Contrôler les mises à jour approuvées avant leur déploiement.
- Réduire les risques liés aux vulnérabilités de sécurité.
- Limiter la consommation de bande passante grâce à un téléchargement unique des correctifs.
- Déployer progressivement les mises à jour afin de réduire les risques d'incident en production.
- Disposer d'une visibilité complète sur l'état des mises à jour des postes de travail et des serveurs.

---

## 1.3 Choix de la solution

L'entreprise a retenu **Windows Server Update Services (WSUS)** afin de reprendre le contrôle du processus de gestion des mises à jour.

Sans WSUS, chaque ordinateur télécharge automatiquement les mises à jour directement depuis les serveurs Microsoft. Dans ce scénario, les administrateurs ne maîtrisent ni le contenu des mises à jour installées, ni leur calendrier de déploiement.

Une mise à jour incompatible peut provoquer des dysfonctionnements logiciels, des problèmes de pilotes ou des interruptions de service impactant la production.

Grâce à WSUS, les administrateurs disposent d'un contrôle total sur le cycle de vie des mises à jour. Ils peuvent :

- sélectionner les mises à jour à déployer ;
- approuver ou refuser chaque correctif ;
- cibler précisément les ordinateurs concernés ;
- superviser le déploiement ;
- suivre l'état de conformité de l'ensemble du parc informatique.

Cette approche permet de réduire considérablement les risques liés aux mises à jour tout en améliorant la gouvernance de l'infrastructure.

---

## 1.4 Choix de l'architecture

Afin de respecter les bonnes pratiques d'administration Microsoft, le rôle **Windows Server Update Services** a été installé sur un serveur dédié.

Cette séparation des rôles présente plusieurs avantages :

- réduction de la surface d'attaque ;
- meilleure disponibilité des services critiques ;
- simplification des opérations de maintenance ;
- dépannage facilité ;
- meilleure évolutivité de l'infrastructure.

Le contrôleur de domaine conserve ainsi exclusivement ses fonctions d'authentification, de gestion d'Active Directory et de résolution DNS, tandis que le serveur WSUS est entièrement consacré à la gestion des mises à jour.

---

## 1.5 Stratégie de déploiement

Le déploiement des mises à jour repose sur une approche progressive inspirée des bonnes pratiques utilisées en entreprise.

Les ordinateurs sont répartis dans trois groupes distincts :

- **Postes-Admin**
- **Utilisateurs**
- **Serveurs**

Les mises à jour sont d'abord déployées sur le groupe **Postes-Admin**, qui joue le rôle de groupe pilote.

Cette première phase permet de vérifier :

- la compatibilité des correctifs ;
- la stabilité du système ;
- l'absence de régression fonctionnelle.

Une fois les tests validés, les mises à jour sont progressivement déployées vers les postes utilisateurs, puis enfin vers les serveurs, qui représentent les systèmes les plus critiques de l'infrastructure.

Cette stratégie permet de limiter l'impact d'une mise à jour défectueuse tout en garantissant un déploiement maîtrisé.

---

## 1.6 Organisation des stratégies de groupe

Une stratégie de groupe (GPO) distincte a été créée pour chaque catégorie de machines :

- GPO - WSUS - Serveurs
- GPO - WSUS - Postes-Admin
- GPO - WSUS - Utilisateurs

Cette séparation permet :

- d'isoler les configurations propres à chaque catégorie de machines ;
- de faciliter le dépannage en identifiant rapidement la stratégie concernée en cas d'incident ;
- de réduire les risques de conflits entre différentes stratégies ;
- d'offrir une meilleure flexibilité lors des évolutions de l'infrastructure.

Cette organisation simplifie l'administration quotidienne et améliore la maintenabilité de l'environnement.

# 2. Architecture de la solution

## 2.1 Vue d'ensemble

L'infrastructure de gestion des mises à jour repose sur une architecture Active Directory segmentée et administrée à l'aide de Windows Server Update Services (WSUS).

Le serveur WSUS centralise le téléchargement des mises à jour Microsoft puis les distribue aux différents postes et serveurs du domaine selon des stratégies de groupe (GPO) spécifiques.

Cette architecture permet de contrôler le déploiement des correctifs tout en limitant les risques opérationnels.

---

## 2.2 Composants de l'infrastructure

| Élément | Rôle |
|----------|------|
| pfSense | Pare-feu et routage entre les différents réseaux |
| ST-DC01 | Contrôleur de domaine Active Directory et serveur DNS |
| ST-WSUS | Gestion centralisée des mises à jour Microsoft |
| ST-FILESERVER01 | Serveur de fichiers |
| PC-ADMIN | Poste pilote utilisé pour valider les mises à jour |
| PC-IT | Poste utilisateur |
| PC-FINANCE | Poste utilisateur |

---

## 2.3 Architecture de déploiement

Les mises à jour suivent le processus suivant :

1. Synchronisation entre Microsoft Update et le serveur WSUS.
2. Validation des mises à jour par l'administrateur.
3. Déploiement sur le groupe **Postes-Admin**.
4. Déploiement sur le groupe **Utilisateurs**.
5. Déploiement sur le groupe **Serveurs**.

Cette stratégie permet de détecter rapidement un éventuel problème avant qu'il n'affecte les systèmes critiques.

---

## 2.4 Communication

Les clients Windows communiquent avec le serveur WSUS via le port **8530 (HTTP)**.

Les paramètres de connexion sont distribués automatiquement grâce aux stratégies de groupe (GPO), garantissant une configuration homogène sur l'ensemble des machines du domaine.


# 3. Prérequis

Avant le déploiement de WSUS, plusieurs prérequis ont été vérifiés afin de garantir son intégration correcte à l'infrastructure Active Directory.

## 3.1 Prérequis système et réseau

| Élément | Configuration |
|---|---|
| Serveur WSUS | Windows Server 2022 |
| Nom du serveur | ST-WSUS |
| Domaine | `corp.securetech.local` |
| Adresse IP | Statique |
| DNS | ST-DC01 |
| Accès Internet | Requis pour la synchronisation Microsoft Update |
| Communication client/WSUS | TCP 8530 |
| Protocole | HTTP |
| Adresse WSUS | FQDN du serveur |
| IIS | Installé automatiquement par l'assistant WSUS |

## 3.2 Intégration Active Directory

ST-WSUS est membre du domaine `corp.securetech.local`.

L'utilisation de **ST-DC01 comme serveur DNS** permet au serveur WSUS et aux clients de résoudre correctement les ressources du domaine et le nom du serveur WSUS.

Les paramètres WSUS sont ensuite distribués aux machines clientes à l'aide de stratégies de groupe (GPO).

## 3.3 Connectivité

ST-WSUS dispose d'un accès Internet afin de récupérer les mises à jour depuis Microsoft Update.

Le port **TCP 8530** est autorisé entre le serveur WSUS et les réseaux des postes utilisateurs et administrateurs afin de permettre :

- la détection des mises à jour ;
- le téléchargement des mises à jour ;
- la remontée des informations d'état vers WSUS.

## 3.4 Configuration du client WSUS

Les postes clients sont configurés via GPO pour utiliser le serveur WSUS à travers son **FQDN** :

```text
http://st-wsus.corp.securetech.local:8530
```

L'utilisation du FQDN évite de dépendre directement de l'adresse IP du serveur et facilite une éventuelle évolution de l'infrastructure réseau.

## 3.5 Composants nécessaires

Les composants nécessaires au fonctionnement de WSUS, notamment **IIS**, ont été installés et configurés automatiquement par l'assistant d'installation du rôle WSUS.

Cette approche permet de conserver une installation cohérente avec les dépendances requises par le rôle.
