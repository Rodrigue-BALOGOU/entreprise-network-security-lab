# Active Directory Configuration

## Introduction

L’infrastructure Active Directory constitue le cœur de l’environnement SecureTech.

Elle assure la gestion centralisée des identités, des ressources et des politiques de sécurité.

Le domaine configuré est :

```text
corp.securetech.local
```

Le contrôleur de domaine fournit les services suivants :

- Authentification (Kerberos / NTLM)
- Service DNS interne
- Gestion des utilisateurs et des machines
- Application des stratégies de groupe (GPO)

Le DNS est entièrement géré par Active Directory, avec une redirection vers pfSense pour la résolution des noms externes.

---

## Installation et préparation du contrôleur de domaine

Avant l’installation d’Active Directory, le serveur a été renommé et configuré avec une adresse IP statique.

### Renommage du serveur

![Renommage du serveur](../screenshots/configuration/active-directory/01-server-rename-installation.png)

### Configuration IP avant promotion

![Configuration IP](../screenshots/configuration/active-directory/02-ip-config-before-ad.png)

---

## Installation d’Active Directory Domain Services

Le rôle Active Directory Domain Services (AD DS) a été installé sur Windows Server 2022.

### Installation du rôle AD DS

![Installation AD DS](../screenshots/configuration/active-directory/02-ad-ds-installation-progress.png)

### Vérification avant installation

![Résumé avant installation](../screenshots/configuration/active-directory/06-summary-before-install.png)

---

## Création du domaine

Le serveur a ensuite été promu contrôleur de domaine afin de créer le domaine SecureTech.

Nom du domaine :

```text
corp.securetech.local
```

### Promotion du contrôleur de domaine

![Promotion du contrôleur de domaine](../screenshots/configuration/active-directory/03-domain-controller-promotion.png)

### Validation de la promotion

![Promotion réussie](../screenshots/configuration/active-directory/04-domain-controller-success.png)

### Vérification du domaine

![Nom du domaine](../screenshots/configuration/active-directory/05-domaine-name-securetech.png)

---

## Configuration DNS

Le service DNS intégré à Active Directory assure la résolution des noms internes.

Les postes du domaine utilisent exclusivement ce serveur DNS.

### Gestion de la zone DNS

![Zone DNS](../screenshots/configuration/active-directory/11-dns-manager-zone.png)

### Configuration des redirecteurs DNS

Les requêtes destinées à Internet sont transférées vers pfSense via des redirecteurs DNS.

![DNS Forwarders](../screenshots/configuration/active-directory/20-dns-forwarders-configuration.png)

---

## Organisation des unités d’organisation (OU)

L’environnement est structuré à l’aide d’Unités d’Organisation permettant une administration claire et une application ciblée des GPO.

### Utilisateurs et ordinateurs Active Directory

![Utilisateurs et ordinateurs](../screenshots/configuration/active-directory/05-active-directory-users-and-computers.png)

### Création des groupes de sécurité

![Groupes de sécurité](../screenshots/configuration/active-directory/08-security-groups-created.png)

### Gestion des groupes

![Gestion des groupes](../screenshots/configuration/active-directory/09-groups-policy-management-console.png)

---

## Gestion des comptes utilisateurs

Les comptes utilisateurs sont organisés par département afin de faciliter l’administration et l’attribution des droits.

### Création des utilisateurs

![Utilisateurs créés](../screenshots/configuration/active-directory/17-users-created.png)

---

## Gestion des ordinateurs du domaine

L’intégration des postes clients au domaine repose sur un compte dédié afin de respecter le principe du moindre privilège.

### Délégation du compte de jonction au domaine

![Délégation](../screenshots/configuration/active-directory/15-admin-delegation.png)

### Limitation du quota de jonction

La limite par défaut permettant à un utilisateur standard d’ajouter dix machines au domaine a été supprimée.

Valeur configurée :

```text
ms-DS-MachineAccountQuota = 0
```

![Quota Domain Join](../screenshots/configuration/active-directory/21-domain-join-limitation-standard-users.png)

### Jonction réussie au domaine

![Domain Join](../screenshots/configuration/active-directory/13-domain-join-succes.png)

### Organisation des ordinateurs

![Ordinateurs du domaine](../screenshots/configuration/active-directory/14-domain-computers.png)

---

## Redirection automatique des objets

Afin d’éviter que les nouveaux objets soient stockés dans les conteneurs par défaut de Microsoft, une redirection automatique a été mise en place.

### Redirection des ordinateurs

![Redirection ordinateurs](../screenshots/configuration/active-directory/08-capture-redirection-computer.png)

![Default Computer Redirection](../screenshots/configuration/active-directory/24-Default-computer-redirection.png)

### Redirection des utilisateurs

![Redirection utilisateurs](../screenshots/configuration/active-directory/09-capture-redirection-user.png)

![Default User Redirection](../screenshots/configuration/active-directory/23-Default-user-redirection.png)

---

## Gestion des stratégies de groupe (GPO)

Les stratégies de groupe permettent de centraliser la configuration et le durcissement des postes de travail.

### Console de gestion des GPO

![Gestion GPO](../screenshots/configuration/active-directory/16-GPO-Management.png)

### Politique de mot de passe

![Password Policy](../screenshots/configuration/active-directory/10-password-policy.png)

### GPO de durcissement

![Hardening Policy](../screenshots/configuration/active-directory/24-gpo-hardening-polycy.png)

### GPO AppLocker

![AppLocker](../screenshots/configuration/active-directory/18-App-locker-gpo.png)

### GPO Windows Event Forwarding

![WEF GPO](../screenshots/configuration/active-directory/25-wef-gpo-configuration.png)

### Configuration RDP

![RDP](../screenshots/configuration/active-directory/26-rdp-condifyration-session.png)

---

## Conclusion

L’architecture Active Directory mise en place repose sur :

- Une organisation claire des OU
- Une séparation des rôles et des privilèges
- Une gestion des accès basée sur le modèle AGDLP
- Une centralisation des ressources via un serveur de fichiers dédié
- Une gestion centralisée des stratégies de sécurité

Cette approche permet :

- Une administration efficace
- Une meilleure sécurité des accès
- Une réduction des risques liés aux privilèges excessifs
- Une application homogène des politiques de sécurité
