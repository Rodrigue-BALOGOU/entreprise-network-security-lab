# 01 – Infrastructure Overview

## Introduction

SecureTech est une entreprise fictive conçue pour simuler une infrastructure d’entreprise réaliste orientée cybersécurité, administration système et sécurité réseau.

L’environnement est entièrement virtualisé sous VMware Workstation Pro et repose sur une architecture segmentée via pfSense afin de reproduire des mécanismes de sécurité présents en environnement professionnel.

Le laboratoire est construit autour d’une approche offensive et défensive permettant de simuler :

- Des attaques externes depuis Internet
- Des tentatives de mouvement latéral
- Des scénarios de compromission réseau
- L’application de politiques de filtrage strictes
- L’analyse et la limitation de la surface d’exposition

---

## Technical Environment

| Component | Technology |
|---|---|
| Hypervisor | VMware Workstation Pro |
| Firewall | pfSense Community Edition |
| Active Directory | Windows Server 2022 |
| DNS | Active Directory Integrated DNS |
| DHCP | pfSense |
| Domain Name | corp.securetech.local |

---

## Global Infrastructure Architecture

L’infrastructure repose sur un firewall pfSense configuré avec plusieurs interfaces réseau afin d’isoler les différentes zones de sécurité.

![Global Architecture](../../../screenshots/architecture/overview/01-global-architecture.png)

---

## pfSense Multi-Interface Configuration

Le firewall agit comme point central de contrôle et applique les politiques de filtrage interzones.

Interfaces configurées :

- WAN
- USER
- DMZ
- INTERNAL
- ADMIN

![pfSense Interfaces](../../../screenshots/architecture/overview/02-pfSense-interfaces.png)

---

## Virtual Environment Overview

L’ensemble de l’environnement est déployé dans une infrastructure virtualisée afin de faciliter les simulations d’attaque, les tests de segmentation et les scénarios de défense.

![Virtual Environment](../../../screenshots/architecture/overview/03-virtual-environment.png)

---

## Network Overview

L’architecture réseau est segmentée en plusieurs zones logiques afin de limiter les communications inutiles et réduire les risques de propagation en cas de compromission.

![Network Overview](../../../screenshots/architecture/overview/04-network-overview.png)

---

# Network Segmentation

## WAN Network

Connexion Internet externe via NAT VMware.

Cette zone représente l’environnement non fiable depuis lequel les attaques externes peuvent être simulées.

---

## Internal Servers Network — 192.168.10.0/24

Zone critique contenant les principaux services d’infrastructure :

- Domain Controller
- DNS
- File Services
- Future Monitoring Services

Cette zone applique des règles de filtrage strictes afin de limiter les communications entrantes et sortantes.

---

## User Network — 192.168.20.0/24

Zone dédiée aux postes utilisateurs joints au domaine Active Directory.

Contient :

- Windows 7 RH
- Windows 10 Direction
- Windows 11 Comptabilité
- Poste utilisateur supplémentaire de test

Restrictions appliquées :

- Aucun accès direct au réseau ADMIN
- Aucun accès direct à la DMZ
- Aucun accès direct à pfSense

---

## DMZ Network — 192.168.30.0/24

Zone isolée exposée vers Internet.

Services exposés :

- HTTP (80)
- FTP (21)

Cette zone sert de surface d’attaque principale pour les simulations offensives.

Objectif :

- Contenir une compromission éventuelle
- Empêcher les mouvements latéraux vers les zones internes

---

## Administration Network — 192.168.99.0/24

Zone hautement sécurisée réservée à l’administration.

Contient :

- Poste d’administration
- Outils de supervision
- Accès aux équipements critiques

Caractéristiques :

- Accès exclusif à l’interface pfSense
- Aucun flux entrant autorisé
- Isolation forte vis-à-vis des autres segments

---

# Security Principles

L’architecture applique plusieurs principes fondamentaux de sécurité :

- Politique par défaut : deny all
- Principe du moindre privilège
- Segmentation réseau stricte
- Isolation des zones critiques
- Réduction de la surface d’exposition
- Contrôle centralisé des flux via pfSense

---

# Inter-Zone Traffic Policy

## USER Network

### Allowed

- Active Directory authentication
- DNS requests
- HTTPS Internet access

### Denied

- Access to INTERNAL servers
- Access to ADMIN network
- Access to DMZ
- Access to pfSense management interface

---

## INTERNAL Network

### Allowed

- HTTPS outbound traffic
- DNS
- NTP synchronization

### Denied

- Direct communication to DMZ
- Non-authorized outbound traffic

---

## DMZ Network

### Allowed

- DNS outbound traffic
- HTTPS outbound updates

### Denied

- Access to INTERNAL network
- Access to USER network
- Access to ADMIN network
- Access to pfSense

---

## ADMIN Network

### Allowed

- Administrative access to all segments
- pfSense management access
- Remote administration tasks

### Denied

- Any inbound connection initiated toward ADMIN

---

# Exposure Surface

Services intentionally exposed to the WAN :

| Service | Port | Purpose |
|---|---|---|
| HTTP | 80 | Web service simulation |
| FTP | 21 | Vulnerable service simulation |

Ces services sont hébergés dans la DMZ afin de reproduire une surface d’exposition réaliste utilisée lors des scénarios d’attaque.

---

# Project Objectives

Ce laboratoire a pour objectif de :

- Simuler des attaques externes réalistes
- Tester les mouvements latéraux
- Valider la segmentation réseau
- Renforcer les politiques firewall
- Implémenter des mécanismes de supervision
- Déployer progressivement des solutions IDS/IPS
- Centraliser et analyser les journaux de sécurité

---

# Current Limitations

Limites actuelles de l’environnement :

- Un seul Domain Controller
- IDS/IPS non encore déployé
- Centralisation des logs en cours de préparation
- FTP volontairement exposé en clair dans un objectif pédagogique

---

# Conclusion

Cette infrastructure constitue une base réaliste de laboratoire de cybersécurité orienté entreprise.

Elle met en œuvre :

- Une architecture segmentée
- Une défense en profondeur
- Un contrôle strict des communications
- Une isolation des zones critiques
- Une réduction de la surface d’attaque
- Une séparation claire entre utilisateurs, services et administration

Le projet servira de base pour les futurs scénarios offensifs et défensifs du laboratoire.
