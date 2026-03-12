02 - Network Architecture

Network Design Overview

L’infrastructure SecureTech repose sur une architecture réseau segmentée.
La segmentation est assurée par le pare-feu pfSense qui agit comme point central de routage et de filtrage.
Chaque segment possède un rôle spécifique et un niveau de confiance différent.
Cette approche permet de limiter les mouvements latéraux en cas de compromission d’un système.
Le firewall contrôle toutes les communications inter-réseaux selon des règles strictes.
Firewall Architecture
Le firewall pfSense dispose de cinq interfaces réseau :

Interface   Rôle

WAN         Connexion externe vers Internet (NAT VMware)
LAN         Réseau utilisateurs
OPT1        Réseau serveurs internes
OPT2        DMZ
OPT3        Réseau administration

Le firewall agit comme :
routeur inter-VLAN
point de contrôle de sécurité
système de translation NAT pour les services exposés
Network Segmentation
L’architecture est divisée en plusieurs zones de sécurité.

* WAN
Le WAN représente la connexion externe.
Il est relié au réseau NAT fourni par VMware Workstation.
Ce segment est considéré comme non fiable.
Seuls les services explicitement publiés sont accessibles depuis Internet.

* Internal Server Network
Le réseau SERVEURINTERNE contient les ressources critiques de l’infrastructure.
Il héberge notamment :
le contrôleur de domaine
les services d’authentification
le service DNS
le serveur de fichier
Ce réseau est fortement protégé et n’accepte que les flux nécessaires au fonctionnement d’Active Directory.

* User Network
Le réseau UTILISATEUR contient les postes clients de l’entreprise.
Les machines de ce réseau sont jointes au domaine Active Directory.
Les utilisateurs peuvent :
s’authentifier auprès du contrôleur de domaine
accéder à Internet via HTTPS
utiliser les services DNS
Toute communication vers les autres segments est bloquée sauf si nécessaire.

* DMZ
La DMZ est une zone intermédiaire destinée à héberger les services exposés.
Dans cette infrastructure, elle contient une machine vulnérable utilisée pour simuler des scénarios d’attaque.
Les services exposés incluent :
-HTTP
-FTP
La DMZ est isolée du réseau interne afin d’empêcher tout accès direct aux ressources critiques.

* Administration Network
Le réseau ADMIN est dédié aux opérations d’administration.
Il permet :
l’accès à l’interface pfSense
l’administration des serveurs
la gestion de l’infrastructure
Ce réseau possède un niveau de confiance élevé et n’est accessible par aucun autre segment.
Security Model

L’architecture réseau applique plusieurs principes de sécurité :
segmentation réseau
principe du moindre privilège
filtrage strict des communications
isolation des services exposés
Chaque communication inter-réseau doit être explicitement autorisée par le firewall.
Par défaut, toute communication est bloquée.

Role of the Firewall
Le firewall pfSense joue un rôle central dans l’infrastructure.
Il assure :
- le routage entre les segments
- le filtrage des flux
- la translation NAT
- la journalisation des connexions
Toutes les communications passent par ce point de contrôle.

Conclusion

L’architecture réseau de SecureTech repose sur une segmentation claire des ressources et un contrôle strict des communications.
Cette approche permet de :
- réduire la surface d’attaque
- limiter les mouvements latéraux
- protéger les ressources critiques
Le firewall constitue le point central de sécurité et garantit l’application des politiques réseau.
