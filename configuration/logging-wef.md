
Logging centralisé et Windows Event Forwarding (WEF)

Introduction

Afin d’améliorer la visibilité et la supervision de l’infrastructure, un système de centralisation des journaux a été mis en place au sein de l’environnement SecureTech.
L’objectif de cette architecture est de :
centraliser les événements Windows,
collecter les journaux réseau,
améliorer les capacités de détection,
faciliter l’analyse des incidents,
reproduire des mécanismes de supervision utilisés en entreprise.
L’infrastructure de collecte repose principalement sur :
Windows Event Forwarding (WEF),
Windows Event Collector (WEC),
NXLog,
Syslog pfSense.

Architecture de collecte
La centralisation des événements repose sur le serveur :
ST-FILESERVER01
Ce serveur assure plusieurs rôles :
serveur de fichiers,
hébergement des partages SMB,
gestion des permissions NTFS,
serveur Windows Event Collector (WEC),
point central de collecte des journaux.
L’ensemble des événements Windows et réseau est centralisé sur cette machine.
Windows Event Forwarding (WEF)
Objectif
WEF a été déployé afin de centraliser les événements de sécurité provenant des systèmes Windows de l’infrastructure.
Les événements collectés proviennent notamment :
du contrôleur de domaine,
des postes utilisateurs,
des postes administrateurs,
des serveurs internes.
Architecture utilisée
Le mécanisme configuré repose sur :
des subscriptions Source-Initiated,
WinRM,
des stratégies de groupe (GPO),
le service Windows Event Collector.
Cette approche permet aux machines du domaine d’envoyer automatiquement leurs événements vers le serveur collecteur.
Journaux collectés
Les journaux suivants sont centralisés :
Security
System
Application
Windows Defender
événements liés au RDP
Cette collecte permet d’améliorer la visibilité sur :
les authentifications,
les connexions distantes,
les erreurs système,
les événements de sécurité,
l’activité des postes clients et serveurs.
Configuration WinRM et GPO
Le forwarding des événements est configuré via des GPO appliquées aux machines du domaine.
Les stratégies permettent notamment :
l’activation de WinRM,
la configuration des subscriptions WEF,
l’autorisation de communication avec le collecteur,
l’automatisation du forwarding des événements.
Cette approche permet une administration centralisée et scalable.
Windows Event Collector (WEC)

Le serveur ST-FILESERVER01 agit également comme serveur WEC.
Son rôle consiste à :
recevoir les événements des machines,
centraliser les journaux,
simplifier l’analyse des événements,
faciliter la corrélation des activités.
Cette centralisation permet de disposer d’une meilleure visibilité sur l’ensemble de l’infrastructure.
Collecte des logs pfSense
Le pare-feu pfSense est intégré au système de journalisation centralisé.
Configuration Syslog
pfSense est configuré pour envoyer ses événements via Syslog vers le serveur de collecte.
Les journaux transférés incluent :
firewall logs,
NAT logs,
DHCP logs,
logs système.

Cette configuration permet de superviser l’activité réseau et les communications inter-zones.
Intégration NXLog
NXLog est installé sur :
ST-FILESERVER01
Son rôle est de :
lire les fichiers Syslog générés par pfSense,
centraliser les événements réseau,
agréger les journaux au même endroit que les événements Windows.
Cette approche permet de regrouper :
événements Windows,
événements Active Directory,
événements réseau,
événements firewall,
dans une infrastructure de collecte unique.

Objectifs de sécurité

L’architecture de logging permet :
la supervision centralisée,
la détection d’activités suspectes,
l’analyse des tentatives d’intrusion,
la corrélation des événements,
l’analyse post-compromission,
la surveillance des connexions RDP,
la visibilité sur les flux réseau.
Cette architecture reproduit des mécanismes utilisés dans des environnements SOC et infrastructures d’entreprise.
Flux de collecte
Plain text
Machines Windows
    │
    ├── WinRM + WEF
    ▼
ST-FILESERVER01 (WEC)
    │
    ├── Journaux Windows
    ├── Événements sécurité
    ├── Événements RDP
    └── Windows Defender

pfSense
    │
    ├── Syslog
    ▼
NXLog
    │
    ▼
ST-FILESERVER01

Bénéfices de l’implémentation

Cette infrastructure de centralisation des logs permet :
une meilleure visibilité globale,
une supervision simplifiée,
une analyse facilitée des incidents,
une meilleure traçabilité,
une corrélation des événements système et réseau,
une préparation à une future intégration SIEM.

Conclusion

La mise en place de WEF, NXLog et Syslog permet de centraliser efficacement les événements critiques de l’infrastructure.
Cette approche améliore les capacités de supervision et constitue une base solide pour les futures phases de détection et d’analyse de sécurité.
