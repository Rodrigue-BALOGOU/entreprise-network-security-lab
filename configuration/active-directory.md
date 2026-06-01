Active Directory Configuration

Introduction

L'infrastructure Active Directory constitue le cœur de l'environnement SecureTech.

Elle assure la gestion centralisée des identités, des ressources et des politiques de sécurité au sein du domaine :

corp.securetech.local

Le contrôleur de domaine, déployé sous Windows Server 2022, fournit les services suivants :

- Active Directory Domain Services (AD DS)
- Authentification Kerberos et NTLM
- Service DNS interne
- Gestion centralisée des utilisateurs et des ordinateurs
- Application des stratégies de groupe (GPO)

Le DNS est entièrement géré par Active Directory. Les requêtes externes sont transférées vers pfSense via une configuration de DNS Forwarders.

"Server Rename" (../screenshots/configuration/active-directory/01-server-rename-installation.png)

"AD DS Installation" (../screenshots/configuration/active-directory/02-ad-ds-installation-progress.png)

"IP Configuration Before AD" (../screenshots/configuration/active-directory/02-ip-config-before-ad.png)

"Domain Controller Promotion" (../screenshots/configuration/active-directory/03-domain-controller-promotion.png)

"Domain Controller Success" (../screenshots/configuration/active-directory/04-domain-controller-success.png)

---

Domain Configuration

Le domaine Active Directory a été créé afin de centraliser l'administration des ressources de l'entreprise.

Nom du domaine :

corp.securetech.local

"Domain Name" (../screenshots/configuration/active-directory/05-domain-name-securetech.png)

"Installation Summary" (../screenshots/configuration/active-directory/06-summary-before-install.png)

---

Organizational Unit Structure

L'organisation logique repose sur une structure hiérarchique d'Unités d'Organisation (OU) permettant une administration claire et une application ciblée des politiques de sécurité.

Administrative Structure

Admin_Accounts
Groups
 ├── DomainLocal
 └── Global

Computer Structure

Ordinateurs
 ├── Admin Workstation
 ├── Laptops
 ├── Workstation
 └── Servers
      ├── AppServers
      ├── FileServers
      └── UpdateServer

User Structure

UTILISATEUR
 ├── Direction
 ├── Finance
 ├── IT
 └── RH

Cette organisation facilite :

- l'application des GPO,
- la délégation de contrôle,
- l'administration quotidienne,
- la séparation des rôles.

"Active Directory Structure" (../screenshots/configuration/active-directory/05-active-directory-users-and-computers.png)

---

User and Computer Management

L'intégration des machines au domaine est sécurisée à l'aide d'un compte dédié :

srv-join

Mesures mises en œuvre :

- suppression du quota par défaut de 10 machines par utilisateur,
- quota défini à 0,
- délégation spécifique accordée au compte srv-join,
- redirection automatique des nouveaux ordinateurs vers l'OU Workstation.

Cette approche limite les privilèges et réduit les risques d'ajout non autorisé de machines au domaine.

"Computer Redirection" (../screenshots/configuration/active-directory/08-capture-redirection-computer.png)

"User Redirection" (../screenshots/configuration/active-directory/09-capture-redirection-user.png)

"Domain Join Limitation" (../screenshots/configuration/active-directory/21-domain-join-limitation-standards-users.png)

"Default User Redirection" (../screenshots/configuration/active-directory/23-default-user-redirection.png)

"Default Computer Redirection" (../screenshots/configuration/active-directory/24-default-computer-redirection.png)

"Domain Join Success" (../screenshots/configuration/active-directory/13-domain-join-succes.png)

"Domain Computers" (../screenshots/configuration/active-directory/14-domain-computers.png)

---

User Accounts and Security Groups

L'infrastructure distingue plusieurs catégories de comptes :

- comptes utilisateurs standards,
- comptes administrateurs,
- compte d'intégration domaine (srv-join),
- comptes support et administration.

La gestion des accès repose sur des groupes de sécurité Active Directory.

Exemples :

GG_Direction_User
GG_Finance_User
GG_IT_User
GG_RH_User
GG_IT_Admin
GG_Server_Admin
GG_RDP_Access

"Security Groups" (../screenshots/configuration/active-directory/08-security-groups-created.png)

"Users Created" (../screenshots/configuration/active-directory/17-users-created.png)

---

AGDLP Access Management Model

Les permissions sont attribuées selon le modèle Microsoft AGDLP :

Accounts
   ↓
Global Groups
   ↓
Domain Local Groups
   ↓
Permissions

Cette approche permet :

- une administration simplifiée,
- une meilleure évolutivité,
- une gestion cohérente des droits.

Exemple :

GG_Finance_User
        ↓
DL_Share_Finance_RW
        ↓
Finance Share

"Group Policy Management" (../screenshots/configuration/active-directory/09-groups-policy-management-console.png)

---

File Server Integration

Le serveur de fichiers centralisé est intégré à Active Directory.

Informations :

Nom : ST-FILESERVER01

Fonctions :

- stockage centralisé,
- partage SMB,
- gestion des droits via groupes AD,
- mappage automatique des lecteurs réseau via GPO.

Les autorisations sont attribuées selon les besoins métiers :

R  = Read
RW = Read / Write

---

Group Policy Management

Les stratégies de groupe sont utilisées pour centraliser la configuration et le durcissement des systèmes.

Fonctions mises en œuvre :

- application des paramètres de sécurité,
- déploiement des restrictions,
- configuration des postes clients,
- gestion du logging centralisé.

"GPO Management" (../screenshots/configuration/active-directory/16-GPO-Management.png)

"Password Policy" (../screenshots/configuration/active-directory/10-password-policy.png)

"Hardening Policy" (../screenshots/configuration/active-directory/24-gpo-hardening-policy.png)

"WEF Configuration" (../screenshots/configuration/active-directory/25-wef-gpo-configuration.png)

"AppLocker GPO" (../screenshots/configuration/active-directory/18-App-locker-gpo.png)

---

DNS Configuration

Active Directory assure la résolution DNS interne du domaine.

Fonctions :

- résolution des noms internes,
- localisation des services AD,
- résolution externe via Forwarders.

Les requêtes Internet sont transférées vers pfSense afin de centraliser le contrôle du trafic DNS.

"DNS Zone" (../screenshots/configuration/active-directory/11-dns-manager-zone.png)

"DNS Forwarders" (../screenshots/configuration/active-directory/20-dns-forwarders-configuration.png)

---

Delegation and Administrative Control

Les privilèges administratifs sont séparés des comptes utilisateurs standards.

Une délégation spécifique est mise en place afin de permettre certaines tâches d'administration sans accorder de privilèges Domain Admin.

Exemples :

- gestion des comptes utilisateurs,
- réinitialisation des mots de passe,
- intégration des postes au domaine.

"Admin Delegation" (../screenshots/configuration/active-directory/15-admin-delegation.png)

"RDP Access Configuration" (../screenshots/configuration/active-directory/26-rdp-condition-configuration-session.png)

---

Conclusion

L'architecture Active Directory de SecureTech repose sur :

- une structure d'OU organisée,
- une séparation stricte des rôles,
- une gestion centralisée des utilisateurs et des ordinateurs,
- l'application du modèle AGDLP,
- une administration sécurisée des ressources,
- l'intégration d'un serveur de fichiers centralisé,
- l'application de politiques de sécurité via GPO.

Cette configuration constitue une base solide pour une infrastructure d'entreprise sécurisée et facilite l'implémentation de mécanismes avancés de supervision, de durcissement et de contrôle d'accès.
