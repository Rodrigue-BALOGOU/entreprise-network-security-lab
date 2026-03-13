04 – Segmentation réseau et politique de pare-feu

Cette section décrit la segmentation réseau mise en place dans le laboratoire ainsi que les règles de filtrage configurées sur le pare-feu pfSense.
L’objectif est de reproduire une architecture d’entreprise réaliste dans laquelle les différents segments du réseau sont isolés et où seules les communications nécessaires sont autorisées.
La segmentation réseau est un principe fondamental de sécurité. Elle permet de réduire la surface d’attaque et de limiter les déplacements latéraux en cas de compromission d’une machine.
Dans ce laboratoire, l’infrastructure est divisée en plusieurs zones réseau.

* Réseau ADMIN
Le réseau ADMIN est dédié aux administrateurs systèmes.
Les postes d’administration sont placés dans ce segment afin d’effectuer les opérations de gestion sur l’infrastructure.
Depuis ce réseau, les administrateurs peuvent accéder aux ressources critiques telles que :
le contrôleur de domaine
les postes utilisateurs
l’interface d’administration du pare-feu pfSense
L’accès à ce réseau est strictement réservé aux opérations d’administration.

* Réseau SERVEURINTERNE
Le réseau SERVEURINTERNE héberge les serveurs internes de l’infrastructure.
Dans ce laboratoire, il contient principalement :
le contrôleur de domaine Active Directory
le service DNS interne
Ce réseau est considéré comme un segment critique car il héberge les services centraux de l’environnement.
L’accès vers ce réseau est donc limité afin de protéger les services essentiels de l’infrastructure.

* Réseau UTILISATEUR
Le réseau UTILISATEUR représente les postes de travail des employés.
Les machines présentes dans ce segment sont jointes au domaine Active Directory et utilisent le serveur DNS interne fourni par le contrôleur de domaine.
Les postes utilisateurs peuvent accéder aux services nécessaires à leur fonctionnement mais ne peuvent pas communiquer directement avec les réseaux sensibles tels que le réseau ADMIN ou certains services internes.

* Réseau DMZ
La DMZ (zone démilitarisée) est utilisée pour isoler les machines qui présentent un niveau de risque plus élevé ou qui pourraient être exposées à des accès externes.
L’objectif de cette zone est de protéger le réseau interne contre une éventuelle compromission.
Les règles du pare-feu empêchent les machines de la DMZ d’initier des connexions vers :
le réseau des serveurs internes
le réseau d’administration
le réseau des utilisateurs

Cette isolation permet de protéger les composants critiques comme Active Directory.
Principes de la politique de pare-feu
La configuration du pare-feu repose sur plusieurs principes de sécurité.

Principe du moindre privilège
Seules les communications nécessaires au fonctionnement du système sont autorisées.
Tout le reste du trafic est bloqué par défaut.
Isolation des réseaux

Les différents segments du réseau sont isolés afin de limiter la propagation d’une attaque.
Accès d’administration contrôlé
Les opérations d’administration sont réalisées uniquement depuis le réseau ADMIN.
Communications sortantes limitées
Les serveurs internes et les machines de la DMZ disposent uniquement des accès sortants nécessaires, notamment pour les mises à jour ou la résolution DNS.
Cette architecture permet de simuler une infrastructure d’entreprise où les différentes zones réseau sont contrôlées par des politiques de sécurité strictes.
