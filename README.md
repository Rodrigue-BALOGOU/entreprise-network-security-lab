# Enterprise Security Lab – Infrastructure d’Entreprise Sécurisée et Simulation Offensive / Défensive

![Project](https://img.shields.io/badge/Project-Enterprise%20Security%20Lab-blue)
![Environment](https://img.shields.io/badge/Environment-Enterprise%20Infrastructure-darkgreen)
![Firewall](https://img.shields.io/badge/Firewall-pfSense-orange)
![Directory](https://img.shields.io/badge/Directory-Active%20Directory-purple)
![Monitoring](https://img.shields.io/badge/Monitoring-Zabbix-blue)
![IDS/IPS](https://img.shields.io/badge/IDS%2FIPS-Suricata-red)
![Status](https://img.shields.io/badge/Status-In%20Progress-yellow)

---

# Présentation du projet

Ce projet consiste à concevoir, déployer et sécuriser une infrastructure d’entreprise segmentée dans un environnement virtualisé.

L’objectif est de reproduire une architecture réaliste permettant de :

- déployer des services d’entreprise
- appliquer des mécanismes de sécurité réseau
- simuler des scénarios d’attaque
- analyser les mouvements latéraux
- mettre en place des mécanismes de détection et de remédiation

L’environnement fictif **SecureTech Solutions** représente une PME disposant d’une architecture multi-zones sécurisée reposant sur :

- la segmentation réseau
- le principe du moindre privilège
- la défense en profondeur
- l’isolation des ressources critiques

---

# Architecture de l’infrastructure

L’environnement repose sur une segmentation réseau stricte via pfSense.

## Zones réseau

| Zone | Rôle |
|---|---|
| WAN | Accès externe via NAT VMware |
| UTILISATEUR | Postes utilisateurs |
| SERVEURINTERNE | Active Directory et services critiques |
| DMZ | Services exposés |
| ADMIN | Administration sécurisée |

Le pare-feu pfSense agit comme :

- routeur inter-réseaux
- point central de filtrage
- système NAT
- serveur DHCP

---

# Services déployés

## Active Directory

- Windows Server 2022
- Domaine : `corp.securetech.local`
- DNS interne
- Gestion centralisée des utilisateurs et machines
- Structure OU hiérarchique
- Modèle AGDLP

---

## Serveur de fichiers

- Serveur dédié : `ST-FILESERVER01`
- Partages SMB
- Permissions NTFS
- Contrôle d’accès basé sur les groupes Active Directory
- Lecteurs réseau mappés via GPO

---

## WSUS

- Serveur dédié aux mises à jour Windows
- Centralisation des mises à jour système
- Contrôle du déploiement des correctifs

---

## Sécurité réseau

- Segmentation inter-zones
- Politique firewall restrictive
- Isolation de la DMZ
- Réseau d’administration isolé
- Journalisation des connexions

---

## Supervision et détection

- Supervision réseau : Zabbix (Ubuntu Server)
- IDS/IPS : Suricata
- Analyse des logs de sécurité

---

## Centralisation des logs

L’infrastructure intègre un mécanisme de collecte centralisée des événements de sécurité.

Le serveur `ST-FILESERVER01` héberge également un service **Windows Event Forwarding (WEF)** permettant de collecter les événements provenant :

- du contrôleur de domaine
- des postes clients
- des serveurs internes

Les équipements réseau sont également intégrés à la supervision :

- pfSense envoie ses journaux via Syslog
- NXLog est utilisé pour la collecte et le transfert des événements Windows

Cette approche permet :

- une meilleure visibilité sur l’infrastructure
- une centralisation des événements
- une capacité d’analyse et de corrélation des logs

# Machines principales

| Système | Rôle |
|---|---|
| pfSense | Firewall / Routeur |
| Windows Server 2022 | Contrôleur de domaine |
| ST-FILESERVER01 | Serveur de fichiers + WEF |
| Ubuntu Server | Supervision Zabbix |
| Windows Server | WSUS |
| Windows 10 Pro | Postes utilisateurs |
| Windows 11 Enterprise | Poste sécurisé / AppLocker |
| Kali Linux | Machine attaquante |
| VulnHub Machine | Service vulnérable en DMZ |

---

# Objectifs techniques

- Concevoir une infrastructure d’entreprise réaliste
- Déployer un environnement Active Directory structuré
- Implémenter une segmentation réseau sécurisée
- Configurer un firewall multi-zones
- Simuler des scénarios d’attaque internes et externes
- Étudier les mouvements latéraux
- Déployer des mécanismes de supervision et détection
- Mettre en œuvre des politiques de hardening

---

# Scénarios d’attaque simulés

Les simulations incluent notamment :

- reconnaissance réseau
- scan de services
- exploitation de services exposés
- compromission de machine DMZ
- tentative de pivot réseau
- mouvement latéral
- élévation de privilèges
- analyse post-compromission

---

# Approche défensive

L’infrastructure applique plusieurs mécanismes défensifs :

- règles firewall restrictives
- segmentation réseau stricte
- limitation des privilèges
- isolation administrative
- contrôle des accès via Active Directory
- supervision et journalisation
- validation des contre-mesures après attaque

---

# Structure du dépôt

```text
architecture/
configuration/
attack-scenario/
defense/
screenshots/

# Compétences démontrées

Architecture réseau sécurisée
Administration Active Directory
Gestion des groupes et permissions (AGDLP)
Configuration pfSense
Hardening Windows
Gestion des accès et GPO
Détection et supervision
Analyse offensive et défensive
Documentation technique structurée
