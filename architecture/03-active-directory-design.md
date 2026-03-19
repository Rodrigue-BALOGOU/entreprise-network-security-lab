05 – Architecture Active Directory

Cette section décrit l’architecture Active Directory mise en place dans le laboratoire ainsi que les choix de conception associés.
L’objectif est de reproduire un environnement d’entreprise réaliste permettant de centraliser la gestion des identités, des machines et des ressources réseau.
Présentation générale
L’infrastructure repose sur un contrôleur de domaine unique déployé sous Windows Server 2022.
Ce serveur assure plusieurs rôles essentiels :
Active Directory Domain Services (AD DS)
DNS interne
Authentification des utilisateurs et des machines
Serveur de fichiers
Le domaine configuré est :

* corp.securetech.local
  
Les postes utilisateurs sont intégrés à ce domaine afin de bénéficier d’une gestion centralisée.
Rôle du contrôleur de domaine
Le contrôleur de domaine constitue le cœur de l’infrastructure.
Il permet :

* l’authentification centralisée via Kerberos
* la gestion des comptes utilisateurs et groupes
* la gestion des ordinateurs du domaine
* la résolution DNS interne

Le service DNS intégré à Active Directory est utilisé pour :
résoudre les noms internes
permettre la localisation des services AD (enregistrements SRV)
effectuer des requêtes externes via un mécanisme de redirection (forwarder) configuré vers le pare-feu pfSense

Le pare-feu pfSense agit ainsi comme point de relais pour la résolution DNS externe, permettant un contrôle centralisé du trafic sortant.
Serveur de fichiers
Le serveur joue également le rôle de serveur de fichiers.
Il permet de centraliser le stockage des données utilisateurs et des ressources partagées.
Les objectifs sont :
centraliser les données
contrôler les accès via les permissions NTFS
faciliter la gestion des droits utilisateurs
Les accès aux partages sont contrôlés à l’aide :
des groupes Active Directory
des permissions NTFS
Cette approche permet de respecter le principe du moindre privilège.
Organisation logique (Unités d’Organisation)

L’environnement est structuré à l’aide d’Unités d’Organisation (OU).
Elles permettent :
d’organiser les utilisateurs et les machines
d’appliquer des stratégies de groupe (GPO)
de déléguer certaines tâches d’administration
Une structure logique typique comprend :
OU_Utilisateurs
OU_Ordinateurs
OU_Admin
Cette organisation améliore la lisibilité et la gestion du domaine.
Comptes et rôles
Plusieurs types de comptes sont utilisés :
Administrateur du domaine
Compte disposant de privilèges complets sur l’infrastructure.
Il est utilisé uniquement pour les opérations critiques.
Compte administrateur
Compte dédié aux tâches d’administration quotidiennes.
Comptes utilisateurs
Comptes standards utilisés par les employés.
Cette séparation permet de limiter les risques liés à l’utilisation de comptes à privilèges élevés.
Intégration réseau

Le contrôleur de domaine est situé dans le réseau SERVEURINTERNE (192.168.10.0/24).
Les flux nécessaires depuis le réseau UTILISATEUR vers le contrôleur de domaine sont strictement autorisés, notamment :
Kerberos (88)
LDAP (389)
DNS (53)
RPC (135 + ports dynamiques)
SMB (445)
Global Catalog (3268)
Kerberos password change (464)

Ces communications sont contrôlées par le pare-feu pfSense afin de limiter les accès au strict nécessaire.
Sécurité
Plusieurs principes de sécurité sont appliqués :
isolation du contrôleur de domaine dans un réseau dédié
contrôle strict des flux réseau
séparation des comptes administratifs et utilisateurs
utilisation du DNS Active Directory pour toutes les machines du domaine
L’accès au contrôleur de domaine est restreint afin de protéger les services critiques de l’infrastructure.

* Limites actuelles

L’architecture présente certaines limites liées à son caractère pédagogique :
un seul contrôleur de domaine (pas de redondance)
absence de haute disponibilité
serveur de fichiers hébergé sur le même serveur que le contrôleur de domaine (non recommandé en production)
Ces éléments devraient être améliorés dans un environnement réel.

Conclusion

L’architecture Active Directory mise en place permet de centraliser :
l’authentification
la gestion des utilisateurs
la gestion des ressources (fichiers)
Elle constitue une base solide pour l’implémentation de mécanismes de sécurité avancés tels que les stratégies de groupe (GPO) et le contrôle d’accès.
