*Configuration des postes clients

Cette section décrit la configuration des postes utilisateurs dans l’environnement SecureTech ainsi que leur intégration au domaine Active Directory.
Intégration au domaine

Les postes clients sont intégrés au domaine :
- corp.securetech.local
La jointure est réalisée à l’aide d’un compte dédié :
srv-join

Ce compte possède des privilèges limités lui permettant uniquement d’ajouter des machines au domaine.
Le quota par défaut autorisant les utilisateurs à joindre des machines a été défini à zéro afin de renforcer la sécurité.
Organisation des machines
Les machines ne sont pas stockées dans le conteneur par défaut Active Directory.
Une redirection a été mise en place afin que les postes soient automatiquement placés dans une Unité d’Organisation dédiée :
Workstations
Cette redirection est configurée via redircmp.
Une délégation de contrôle permet au compte srv-join de créer des objets dans cette OU.

* Configuration réseau
Les postes clients utilisent une configuration réseau centralisée.
Le serveur DNS configuré est le contrôleur de domaine :
192.168.10.103
Toutes les requêtes DNS transitent par ce serveur.
Le contrôleur de domaine utilise un mécanisme de redirection vers le pare-feu pfSense pour la résolution des requêtes externes.
Authentification des utilisateurs
Les utilisateurs se connectent aux postes via leurs comptes Active Directory.
Cela permet :
- une authentification centralisée
- une gestion des accès simplifiée
- une traçabilité des connexions

Les comptes locaux ne sont pas utilisés pour les connexions standards.
Gestion des privilèges
Les utilisateurs disposent de comptes standards.
Ils ne possèdent pas de privilèges administrateur local sur les postes.
Cette configuration permet de limiter les risques liés :
aux malwares
aux erreurs utilisateur
aux modifications système non autorisées
Accès aux ressources
Les ressources réseau sont hébergées sur un serveur de fichiers dédié :
ST-FILESERVER01
Les lecteurs réseau sont automatiquement configurés via des GPO.
Cette configuration est appliquée côté utilisateur :
User Configuration → Preferences → Drive Maps
Elle permet :
une centralisation des accès
une automatisation
une attribution basée sur les utilisateurs ou groupes
Sécurité
Les postes clients sont intégrés dans une architecture contrôlée reposant sur :
Active Directory
une gestion centralisée des identités
une limitation stricte des privilèges
Les mécanismes de durcissement avancés (GPO de sécurité, restrictions, politiques système) sont documentés dans la section dédiée au hardening.

* Conclusion
Les postes clients sont configurés de manière centralisée et sécurisée.
Ils respectent les bonnes pratiques d’intégration Active Directory et constituent une base fiable pour l’application de politiques de sécurité avancées.
