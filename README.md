🏢 Enterprise Security Lab – Conception d’une Infrastructure d’Entreprise Sécurisée et Simulation d’Attaque /defense

![Project](https://img.shields.io/badge/Project-Enterprise%20Security%20Lab-blue)
![Domain](https://img.shields.io/badge/Domain-Enterprise%20Security-darkgreen)
![Offensive](https://img.shields.io/badge/Offensive-Red%20Team-critical)
![Defensive](https://img.shields.io/badge/Defensive-Blue%20Team-blue)
![Firewall](https://img.shields.io/badge/Firewall-pfSense-orange)
![Directory](https://img.shields.io/badge/Directory-Active%20Directory-purple)
![Status](https://img.shields.io/badge/Status-In%20Progress-yellow)

📌 Présentation du projet
Ce projet consiste à concevoir, déployer et sécuriser une infrastructure réseau d’entreprise segmentée, puis à simuler des scénarios d’attaque réalistes afin d’analyser et renforcer les mécanismes de défense.
L’entreprise fictive SecureTech Solutions représente une PME disposant d’une architecture multi-zones respectant les principes modernes de cybersécurité : segmentation réseau, contrôle strict des flux, défense en profondeur et isolation des ressources critiques.
L’objectif est de reproduire un environnement professionnel réaliste permettant d’étudier les interactions entre architecture, exploitation et remédiation.

🏗 Architecture de l’infrastructure
L’architecture repose sur une segmentation en plusieurs zones logiques distinctes :
WAN : accès externe connecté via NAT (environnement de virtualisation)
DMZ : services exposés (serveur web vulnérable)
Réseau Utilisateurs : postes métiers (RH, Direction, Comptabilité)
Réseau Serveurs Internes : Active Directory et services critiques
Réseau Administration : postes et comptes à privilèges élevés isolés
Le pare-feu pfSense est positionné comme point de contrôle central et assure le filtrage des flux inter-zones selon une politique de sécurité restrictive.

🧰 Composants principaux
Pare-feu : pfSense
Contrôleur de domaine : Windows Server 2022 (AD DS + DNS)
Postes clients : Windows 10 pro / 10 pro / 11 enterprise (dont poste avec AppLocker)
Machine attaquante : Kali Linux
Serveur en DMZ : machine vulnérable
Supervision : Zabbix
IDS/IPS : Suricata

🎯 Objectifs techniques
Concevoir une architecture segmentée multi-zones
Déployer un environnement Active Directory structuré
Implémenter une isolation réseau stricte
Configurer un firewall avec règles inter-VLAN contrôlées
Simuler des scénarios d’attaque externes et internes
Analyser les logs et événements de sécurité
Appliquer des mesures de hardening et de remédiation

🧪 Scénarios d’attaque simulés
Le projet inclut des simulations structurées comprenant :
Phase de reconnaissance (scan, enumeration)
Exploitation d’un service exposé en DMZ
Tentative de pivot vers le réseau interne
Mouvement latéral
Escalade de privilèges
Analyse post-compromission

🛡 Approche défensive
Règles de filtrage strictes sur pfSense
Segmentation inter-zones contrôlée
Isolation du réseau d’administration
IDS/IPS (Suricata)
Supervision réseau (Zabbix)
Analyse des journaux de sécurité
Validation des contremesures après attaque

📂 Structure du dépôt
architecture/ → Conception logique, plan IP, segmentation, objectifs sécurité
configuration/ → Implémentation technique (pfSense, AD, clients, hardening)
attack-scenario/ → Méthodologie offensive, résultats et preuves
defense/ → Analyse des vulnérabilités et remédiation
screenshots/ → Captures organisées servant de preuves techniques

🚀 Compétences démontrées
Segmentation réseau avancée
Configuration de firewall en environnement multi-zones
Administration Active Directory
Simulation de mouvement latéral
Hardening d’infrastructure Windows
Analyse offensive et défensive
Documentation technique structurée
