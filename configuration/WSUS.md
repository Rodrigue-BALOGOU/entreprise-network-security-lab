# Gestion centralisée des mises à jour Microsoft dans une infrastructure Active Directory

---



Dans l'infrastructure, les postes pouvaient récupérer leurs mises à jour directement depuis Microsoft Update.

Cette approche limitait le contrôle de l'administrateur et pouvait exposer l'entreprise à une mise à jour problématique déployée simultanément sur plusieurs machines.

L'objectif était donc de centraliser la gestion des mises à jour avec **WSUS**.

---



Mettre en place un serveur WSUS intégré à Active Directory afin de :

- centraliser les mises à jour Microsoft ;
- contrôler les correctifs approuvés ;
- tester les mises à jour avant production ;
- déployer progressivement les correctifs ;
- suivre l'état des postes et serveurs.

---

## 3. Architecture

```text
                    INTERNET
                        │
                        ▼
                   ┌─────────┐
                   │ pfSense │
                   └────┬────┘
                        │
                        ▼
                ┌────────────────┐
                │    ST-WSUS     │
                │ Windows Server │
                │     2022       │
                └───────┬────────┘
                        │
                     TCP 8530
                        │
          ┌─────────────┼─────────────┐
          ▼             ▼             ▼
     POSTES-ADMIN   UTILISATEURS    SERVEURS
          │             │             │
      PC-ADMIN        PC-IT        ST-DC01
                      PC-RH        ST-FILESERVER01
                  PC-FINANCE
                  PC-DIRECTION
```

### Environnement

| Composant       | Rôle                   |
| --------------- | ---------------------- |
| ST-WSUS         | Serveur WSUS           |
| ST-DC01         | Active Directory + DNS |
| ST-FILESERVER01 | Serveur de fichiers    |
| pfSense         | Pare-feu               |
| PC-ADMIN        | Poste pilote           |
| PC-IT           | Poste utilisateur      |
| PC-FINANCE      | Poste utilisateur      |
| PC-RH           | Poste utilisateur      |
| PC-DIRECTION    | Poste utilisateur      |

---



### Installation

WSUS a été installé sur un **Windows Server 2022 dédié**.

![Installation du rôle WSUS](..WSUS.md/screenshots/configuration/wsus/role-wsus-installed.png)

*Installation du rôle WSUS sur ST-WSUS.*

Le contenu WSUS a été séparé du système :

```text
F:\WSUS
```

![Répertoire de contenu WSUS](../../screenshots/configuration/wsus/wsus-content-directory.png)

*Stockage du contenu WSUS sur le disque dédié.*

La base utilisée est :

```text
Windows Internal Database (WID)
```

Une erreur `System.UnauthorizedAccessException` est apparue pendant la post-installation.

![Erreur de post-installation](../../screenshots/configuration/wsus/installation-failed.png)

*Erreur de permissions rencontrée lors de la post-installation.*

La phase de post-installation a été finalisée avant la poursuite de l'intégration du serveur.

![Post-installation WSUS](../../screenshots/configuration/wsus/installation-post.png)

*Finalisation de la configuration post-installation.*

### Intégration Active Directory

Les clients utilisent WSUS via son FQDN :

```text
http://st-wsus.corp.securetech.local:8530
```

La configuration est distribuée par GPO :

```text
GPO - WSUS - Postes-Admin
GPO - WSUS - Utilisateurs
GPO - WSUS - Serveurs
```

![Configuration du client WSUS](../../screenshots/configuration/wsus/configuration1.png)

*Configuration du serveur WSUS utilisé par les clients.*

Le ciblage côté client permet d'associer automatiquement les machines aux groupes WSUS.

![Ciblage côté client](../../screenshots/configuration/wsus/target-client-wsus.png)

*Configuration du ciblage côté client WSUS.*

### Stratégie de déploiement

```text
Postes-Admin
     │
     ▼
Validation
     │
     ▼
Utilisateurs
     │
     ▼
Validation
     │
     ▼
Serveurs
```

Les mises à jour sont donc testées sur un poste pilote avant leur déploiement sur les utilisateurs puis les serveurs.

![Groupes d'ordinateurs WSUS](../../screenshots/configuration/wsus/computer-dashboard-groupe.png)

*Organisation des machines dans les groupes WSUS.*

### Sécurisation réseau

Les clients communiquent avec WSUS via :

```text
TCP 8530 / HTTP
```

Le flux est contrôlé par **pfSense**.

---

## 5. Résultats

La gestion des mises à jour est désormais centralisée :

```text
Microsoft Update
       ↓
   ST-WSUS
       ↓
Approbation
       ↓
Postes-Admin
       ↓
Utilisateurs
       ↓
Serveurs
```

La synchronisation avec Microsoft Update a été validée.

![Synchronisation WSUS réussie](../../screenshots/configuration/wsus/synchronises-successfull.png)

*Synchronisation réussie avec Microsoft Update.*

Les mises à jour sont ensuite approuvées progressivement.

![Approbation d'une mise à jour pour un client](../../screenshots/configuration/wsus/update-approve-client.png)

*Approbation d'une mise à jour pour le groupe pilote.*

![Approbation d'une mise à jour pour les serveurs](../../screenshots/configuration/wsus/update-approve-server.png)

*Déploiement de la mise à jour après validation du groupe pilote.*

Les tests ont permis de valider :

- l'application des GPO ;
- la communication avec WSUS ;
- le ciblage des groupes ;
- la détection des mises à jour ;
- le téléchargement ;
- l'installation ;
- la remontée des informations dans WSUS.

![Mise à jour installée](../../screenshots/configuration/wsus/update-installed.png)

*Validation de l'installation d'une mise à jour sur un client.*

Un problème de services Windows Update a également été diagnostiqué avec :

```powershell
Get-Service BITS,Wuauserv
Start-Service BITS
Start-Service Wuauserv
```

Puis une nouvelle détection a été forcée :

```cmd
UsoClient StartScan
UsoClient StartInteractiveScan
```

Le problème de ciblage et de communication rencontré pendant les tests a finalement été résolu.

![Résolution du problème WSUS](../../screenshots/configuration/wsus/problem-solved.png)

*Résolution du problème rencontré pendant les tests.*

---

## 6. Bénéfices

- **Contrôle** des mises à jour approuvées.
- **Déploiement progressif** avant les serveurs.
- **Réduction du risque** d'incident généralisé.
- **Centralisation** du suivi des machines.
- **Diagnostic facilité** grâce à la séparation des GPO.
- **Maîtrise du trafic** de téléchargement des mises à jour.

---

## 7. Compétences démontrées

`Windows Server 2022` · `Active Directory` · `GPO` · `WSUS` · `Windows Update` · `DNS` · `pfSense` · `Administration système` · `Dépannage` · `Segmentation réseau`

---

## Conclusion

Cette implémentation permet de passer d'une gestion individuelle des mises à jour à un processus maîtrisé :

> **Synchroniser → Approuver → Tester → Valider → Déployer**

L'approche réduit le risque opérationnel tout en donnant à l'administrateur une visibilité centralisée sur le parc.

---

