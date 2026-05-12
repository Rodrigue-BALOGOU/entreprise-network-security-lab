* Configuration du pare-feu pfSense

Cette section décrit la configuration du pare-feu pfSense ainsi que son rôle central dans l’architecture réseau de l’infrastructure SecureTech.
Rôle du pare-feu

Le pare-feu pfSense joue un rôle central dans l’infrastructure.
Il assure plusieurs fonctions essentielles :
*routage inter-réseaux
*filtrage des communications
*translation d’adresses (NAT)
*attribution des adresses IP (DHCP)

Le DNS interne est géré par Active Directory, tandis que pfSense intervient indirectement dans la résolution externe via le mécanisme de redirection DNS.
Architecture des interfaces

Le pare-feu est configuré avec cinq interfaces réseau :
*WAN : connexion externe via le réseau NAT de VMware
*UTILISATEUR (LAN) : réseau des postes clients (192.168.20.0/24)
* SERVEURINTERNE (OPT1) : réseau des serveurs (192.168.10.0/24)
* DMZ (OPT2) : réseau des services exposés (192.168.30.0/24)
* ADMIN (OPT3) : réseau d’administration (192.168.99.0/24)

Le pare-feu agit comme point central de contrôle entre ces différents segments.
Politique de filtrage

La configuration du firewall repose sur une politique stricte :
blocage par défaut de tout le trafic inter-réseaux
autorisation uniquement des flux nécessaires
Cette approche respecte le principe du moindre privilège.

Aucune règle de type “allow any” n’est utilisée, ce qui permet de garantir un contrôle précis des communications.
Configuration NAT
Le NAT est configuré en mode automatique.
Ce mode permet à pfSense de générer dynamiquement les règles nécessaires pour permettre aux réseaux internes d’accéder à Internet.
Bien que ce mode soit fonctionnel, il offre moins de contrôle qu’une configuration manuelle ou hybride.
Publication des services (Port Forwarding)

Certains services hébergés dans la DMZ sont exposés vers Internet via des règles de redirection de ports (port forwarding).
Les services publiés sont :
HTTP (port 80) → 192.168.30.102
FTP (port 2121) → 192.168.30.102
Ces règles permettent d’accéder aux services depuis le réseau externe tout en maintenant une isolation entre la DMZ et les réseaux internes.
Sécurité

Plusieurs mécanismes de sécurité sont appliqués :
isolation stricte des réseaux
filtrage précis des communications
absence de règles permissives globales
séparation des zones de confiance
La DMZ est totalement isolée des réseaux internes afin d’empêcher tout mouvement latéral en cas de compromission.
Journalisation
La journalisation est activée sur les règles critiques, notamment sur l’interface WAN.
Cela permet :
de surveiller les connexions entrantes
d’analyser les tentatives d’accès
de détecter des comportements suspects
Limites actuelles

Certaines améliorations peuvent être envisagées :
utilisation du NAT manuel ou hybride pour un meilleur contrôle
restriction des accès WAN à certaines adresses IP sources
mise en place d’un IDS/IPS

Conclusion

Le pare-feu pfSense constitue le point central de sécurité de l’infrastructure.
Il permet de contrôler l’ensemble des communications entre les réseaux et d’appliquer des politiques de sécurité strictes.
Cette configuration offre une base solide pour la mise en place de mécanismes de détection et de protection avancés.
