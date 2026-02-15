🏢 Conception d’une Architecture Réseau d’Entreprise Sécurisée et Simulation d’Attaque

📌 Contexte du projet
Ce projet consiste à concevoir et déployer une infrastructure réseau d’entreprise segmentée, puis à simuler un scénario d’attaque réaliste afin d’analyser les mécanismes de défense mis en place.
L’entreprise fictive SecuTech Solutions représente une PME disposant :
d’une zone DMZ exposée,
d’un réseau interne sous Active Directory,
d’une segmentation VLAN,
d’un pare-feu centralisé,
d’outils de supervision et de détection d’intrusion.
L’objectif est de reproduire un environnement professionnel réaliste afin d’étudier les interactions entre attaque et défense.

🎯 Objectifs
Concevoir une architecture réseau segmentée
Déployer un environnement Active Directory
Mettre en place une isolation par VLAN
Configurer un pare-feu (pfSense)
Simuler une attaque externe multi-étapes
Détecter et analyser l’activité malveillante
Proposer des mesures de remédiation
🏗 Architecture de l’infrastructure
🔹 Segmentation réseau
VLAN 10 – DMZ (services exposés)
VLAN 20 – Réseau interne (LAN)
VLAN 30 – Réseau attaquant (simulation externe)
🔹 Composants principaux
Pare-feu : pfSense
Contrôleur de domaine : Windows Server (AD + DNS)
Poste client joint au domaine
Serveur Web en DMZ
Machine attaquante (Kali Linux)
Serveur de supervision (Zabbix)
IDS : Suricata
🔐 Principes de sécurité appliqués
L’architecture repose sur :
Le principe du moindre privilège
La segmentation réseau
La réduction de la surface d’attaque
Une approche défense en profondeur
Les communications inter-VLAN sont strictement contrôlées par des règles de filtrage.

🧪 Scénario d’attaque simulé
Le projet inclut une simulation d’attaque structurée :
Phase de reconnaissance
Exploitation d’un service exposé
Pivot vers le réseau interne
Mouvement latéral
Escalade de privilèges
Détection et analyse des journaux

🛡 Mécanismes de défense
Règles de filtrage pare-feu
Système de détection d’intrusion (IDS)
Supervision réseau (Zabbix)
Analyse des logs
Validation de la segmentation

📊 Démarche d’analyse des risques
Identification des services exposés
Modélisation des menaces
Analyse des chemins d’attaque
Évaluation de l’impact
Recommandations de sécurisation

🧰 Technologies utilisées
VMware
pfSense
Windows Server
Kali Linux
Zabbix
VLAN
IDS/IPS

🎓 Objectif pédagogique
Ce projet vise à démontrer des compétences pratiques en :
Architecture réseau sécurisée
Administration système
Tests d’intrusion encadrés
Mise en place de mécanismes défensifs
Analyse de sécurité en environnement d’entreprise
