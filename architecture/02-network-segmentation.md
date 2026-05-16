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

L’objectif principal est de :

- limiter les mouvements latéraux,
- réduire la surface d’attaque,
- isoler les ressources critiques,
- contrôler les communications inter-réseaux.

---

# Global Network Segmentation

L’environnement est divisé en plusieurs zones de sécurité isolées afin d’appliquer une séparation logique entre les utilisateurs, les serveurs, les services exposés et l’administration.

![Network Zones](https://raw.githubusercontent.com/Rodrigue-BALOGOU/entreprise-network-security-lab/main/screenshots/architecture/network-segmentation/02-network-overview.png)

---

# pfSense Interfaces Architecture

Le firewall pfSense constitue le point central de contrôle de l’infrastructure.

Plusieurs interfaces réseau sont configurées afin d’assurer l’isolation des différents segments de sécurité.

Interfaces configurées :

- WAN
- USER
- INTERNAL
- DMZ
- ADMIN

![pfSense Interfaces](https://raw.githubusercontent.com/Rodrigue-BALOGOU/entreprise-network-security-lab/main/screenshots/architecture/network-segmentation/01-pfSense-interfaces.png)

---

# Network Overview

L’ensemble des segments réseau communique exclusivement via le firewall pfSense selon des politiques de sécurité strictes.

Chaque réseau possède :
- son propre espace d’adressage,
- ses règles de communication,
- son niveau de confiance.

![Network Overview](https://raw.githubusercontent.com/Rodrigue-BALOGOU/entreprise-network-security-lab/main/screenshots/architecture/network-segmentation/03-network-zones.png)

---

# WAN Network

Le réseau WAN représente l’environnement externe non fiable.

Caractéristiques :

- connecté au NAT VMware,
- accès Internet simulé,
- utilisé pour les scénarios d’attaque externes,
- considéré comme zone non sécurisée.

Seuls les services explicitement publiés sont accessibles depuis cette zone.

---

# Internal Server Network — 192.168.10.0/24

Le réseau SERVEURINTERNE héberge les ressources critiques de l’infrastructure.

Services présents :

- Active Directory
- DNS
- File Server
- Services Windows internes
- Collecte d’événements centralisée

Cette zone applique des restrictions strictes afin de protéger les ressources sensibles.

---

# User Network — 192.168.20.0/24

Le réseau UTILISATEUR contient les postes clients joints au domaine Active Directory.

Machines présentes :

- Windows 11 Enterprise RH
- Windows 10 Direction
- Windows 11 Enterprise FINANCE
- Postes de test utilisateurs

Les utilisateurs peuvent :

- s’authentifier auprès du domaine,
- accéder à Internet,
- utiliser les services DNS,
- accéder aux ressources autorisées.

Les communications directes vers les segments critiques restent limitées.

---

# DMZ Network — 192.168.30.0/24

La DMZ est une zone isolée destinée aux services exposés.

Services exposés :

- HTTP (80)
- FTP (21)

Cette zone contient une machine volontairement vulnérable utilisée dans les simulations offensives du laboratoire.

Objectifs :

- isoler les services exposés,
- limiter les mouvements latéraux,
- empêcher l’accès direct au réseau interne.

---

# Administration Network — 192.168.99.0/24

Le réseau ADMIN est réservé aux opérations d’administration et de supervision.

Il permet :

- l’administration des serveurs,
- l’accès aux équipements critiques,
- la gestion de l’infrastructure,
- l’accès à l’interface pfSense.

Ce segment possède le niveau de confiance le plus élevé de l’environnement.

---

# Security Principles

L’architecture applique plusieurs principes fondamentaux de cybersécurité :

- segmentation réseau stricte,
- principe du moindre privilège,
- isolation des ressources critiques,
- contrôle des flux inter-réseaux,
- réduction de la surface d’exposition,
- séparation des zones d’administration.

Par défaut, toute communication non explicitement autorisée est bloquée.

---

# Role of pfSense

Le firewall pfSense constitue le cœur de l’infrastructure réseau.

Fonctions principales :

- routage inter-réseaux,
- filtrage des communications,
- translation NAT,
- journalisation des connexions,
- contrôle centralisé des flux,
- application des politiques réseau.

Toutes les communications transitent par ce point de contrôle.

---

# Conclusion

L’architecture réseau SecureTech repose sur une segmentation logique forte et un contrôle strict des communications.

Cette approche permet :

- de protéger les ressources critiques,
- de limiter les mouvements latéraux,
- de réduire la surface d’attaque,
- d’isoler les services exposés,
- d’améliorer la résilience globale de l’infrastructure.

Le firewall pfSense constitue l’élément central de sécurité et garantit l’application des politiques réseau dans l’ensemble du laboratoire.
