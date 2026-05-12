01 – Overview

 Introduction
 
SecureTech est une entreprise IT fictive conçue pour simuler une infrastructure professionnelle sécurisée.
L’environnement est déployé sous VMware Workstation Pro et repose sur une segmentation réseau stricte via pfSense.
Ce laboratoire est orienté attaque / défense, permettant de simuler :
-Des attaques externes
-Des tentatives de mouvement latéral
-La mise en œuvre de mécanismes de protection réseau

 *Environnement Technique
-Hyperviseur : VMware Workstation Pro
-Firewall : pfSense (5 interfaces configurées)
-Annuaire : Active Directory (1 Domain Controller)
-DNS : géré par Active Directory
-DHCP : géré par pfSense
-Nom de domaine : corp.securetech.local

Architecture Réseau
L’infrastructure est segmentée en cinq zones logiques :

* WAN
Connexion externe via NAT VMware (DHCP)

* Serveurs Internes – 192.168.10.0/24
Contient le Domain Controller
Zone critique

* Utilisateurs – 192.168.20.0/24
4 postes Windows joints au domaine
  
* DMZ – 192.168.30.0/24
Machine vulnérable
HTTP (80) exposé
FTP (21) exposé

* Administration – 192.168.99.0/24
Poste dédié à l’administration
Poste join au domaine 
Accès exclusif à pfSense
Le firewall pfSense agit comme point de contrôle central et filtre tous les flux interzones.

Principes de Sécurité
L’architecture applique :
Politique interzones par défaut : deny
Principe du moindre privilège
Segmentation stricte
Protection forte du réseau d’administration
Isolation complète de la DMZ

Politique de Flux par Zone

* UTILISATEUR
Autorisé :
-Authentification Active Directory
-DNS vers firewall
-HTTPS vers Internet
Interdit :
-Accès SERVEURINTERNE
-Accès ADMIN
-Accès DMZ
-Accès interface pfSense

* SERVEURINTERNE
Autorisé :
-HTTPS sortant (mises à jour)
-NTP (123)
-DNS
Interdit :
-Communication vers DMZ
-Tout flux non explicitement autorisé

* DMZ
Autorisé :
-DNS sortant
-HTTPS sortant (mises à jour)
Interdit :
-Accès réseau interne
-Accès ADMIN
-Accès UTILISATEUR
-Accès firewall

* ADMIN
Autorisé :
-Accès à tous les segments pour supervision
-Accès exclusif à l’interface pfSense
-Aucun accès entrant autorisé vers ADMIN.

 Surface d’Exposition
Services exposés vers le WAN :
-HTTP (80)
-FTP (21)
Ces services sont hébergés en DMZ et servent de point d’entrée pour les scénarios d’attaque.

 Objectifs du Projet
- Simuler des attaques externes
- Tester les mouvements latéraux
- Valider la segmentation réseau
- Implémenter progressivement IDS/IPS
- Analyser les journaux firewall

⚠ Limites Actuelles
Un seul Domain Controller
Pas encore d’IDS/IPS actif
Centralisation des logs non déployée
FTP exposé en clair (choix pédagogique)

 Conclusion
L’architecture met en œuvre :
Une  Attaque controlé
Défense en profondeur
Segmentation réseau avancée
Contrôle strict des flux
Protection de la surface d’administration
Elle constitue une base réaliste pour des scénarios de cybersécurité offensifs et défensifs.
