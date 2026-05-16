# 02 – Network Segmentation

# Network Design Overview

L’infrastructure SecureTech repose sur une architecture réseau segmentée conçue pour reproduire un environnement d’entreprise réaliste orienté cybersécurité.

La segmentation est assurée par pfSense qui agit comme :

- routeur inter-réseaux,
- firewall central,
- point de contrôle de sécurité,
- système NAT,
- mécanisme de filtrage des flux.

Chaque segment possède :
- un rôle spécifique,
- un niveau de confiance différent,
- des politiques de communication dédiées.

L’objectif principal est de limiter :
- les mouvements latéraux,
- les communications inutiles,
- la propagation d’une compromission.

---

# Global Network Segmentation

L’environnement est divisé en plusieurs zones de sécurité isolées.

![Network Zones](https://raw.githubusercontent.com/Rodrigue-BALOGOU/entreprise-network-security-lab/main/screenshots/architecture/network-segmentation/01-network-zones.png)

---

# pfSense Interfaces and Network Separation

Le firewall pfSense dispose de plusieurs interfaces réseau permettant l’isolation logique des différents segments de l’infrastructure.

![pfSense Interfaces](https://raw.githubusercontent.com/Rodrigue-BALOGOU/entreprise-network-security-lab/main/screenshots/architecture/network-segmentation/02-pfsense-interfaces.png)

---

# WAN Network

Le réseau WAN représente l’environnement externe non fiable.

Caractéristiques :

- connecté au NAT VMware,
- accès Internet simulé,
- considéré comme non sécurisé,
- utilisé pour les scénarios d’attaque externes.

Seuls les services explicitement publiés peuvent être accessibles depuis cette zone.

---

# Internal Server Network

Le réseau SERVEURINTERNE héberge les ressources critiques de l’entreprise.

Services présents :

- Active Directory
- DNS
- File Server
- Services internes Windows

Cette zone applique des politiques de sécurité strictes afin de limiter les communications entrantes et sortantes.

---

# User Network

Le réseau UTILISATEUR contient les postes clients joints au domaine Active Directory.

Les utilisateurs peuvent :

- s’authentifier auprès du contrôleur de domaine,
- accéder à Internet,
- utiliser les services DNS,
- accéder aux ressources autorisées.

Les accès directs vers :
- ADMIN,
- DMZ,
- pfSense,
- certains serveurs internes,

sont bloqués par défaut.

---

# User Network Filtering Policy

Le firewall applique des règles spécifiques au réseau utilisateur afin de contrôler les communications autorisées.

![User Firewall Rules](https://raw.githubusercontent.com/Rodrigue-BALOGOU/entreprise-network-security-lab/main/screenshots/architecture/network-segmentation/03-firewall-rules-users.png)

---

# DMZ Network

La DMZ est une zone intermédiaire destinée à héberger les services exposés vers Internet.

Services exposés :

- HTTP
- FTP

Cette zone contient une machine volontairement vulnérable utilisée dans les scénarios offensifs du laboratoire.

L’objectif de la DMZ est de :

- contenir une éventuelle compromission,
- empêcher l’accès direct aux ressources critiques,
- limiter les mouvements latéraux.

---

# DMZ Filtering Policy

Les communications de la DMZ sont strictement limitées afin de protéger les réseaux internes.

![DMZ Firewall Rules](https://raw.githubusercontent.com/Rodrigue-BALOGOU/entreprise-network-security-lab/main/screenshots/architecture/network-segmentation/04-firewall-rules-dmz.png)

---

# Administration Network

Le réseau ADMIN est réservé aux opérations d’administration et de supervision.

Il permet :

- l’accès aux équipements critiques,
- l’administration des serveurs,
- la gestion de l’infrastructure,
- l’accès à l’interface pfSense.

Ce segment possède le niveau de confiance le plus élevé de l’infrastructure.

---

# Administration Filtering Policy

Le réseau ADMIN dispose de privilèges élevés mais reste isolé des autres segments afin de réduire la surface d’attaque.

![Admin Firewall Rules](https://raw.githubusercontent.com/Rodrigue-BALOGOU/entreprise-network-security-lab/main/screenshots/architecture/network-segmentation/05-firewall-rules-admin.png)

---

# Security Model

L’architecture applique plusieurs principes fondamentaux de cybersécurité :

- segmentation réseau,
- principe du moindre privilège,
- politique par défaut : deny all,
- filtrage strict des communications,
- isolation des services exposés,
- réduction de la surface d’attaque.

Toute communication inter-réseau doit être explicitement autorisée par le firewall.

---

# Role of the Firewall

Le firewall pfSense constitue le point central de sécurité de l’infrastructure.

Fonctions principales :

- routage inter-réseaux,
- filtrage des flux,
- translation NAT,
- journalisation des événements,
- contrôle des accès,
- application des politiques réseau.

Toutes les communications transitent par ce point de contrôle.

---

# Conclusion

L’architecture réseau SecureTech repose sur une segmentation stricte et un contrôle centralisé des communications.

Cette approche permet de :

- réduire la surface d’attaque,
- limiter les mouvements latéraux,
- protéger les ressources critiques,
- isoler les services exposés,
- renforcer la sécurité globale de l’infrastructure.

Le firewall pfSense garantit l’application des politiques réseau et constitue l’élément central de la sécurité de l’environnement.
