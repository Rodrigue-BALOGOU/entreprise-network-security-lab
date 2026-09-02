# Scénario d'attaque — Compromission contrôlée d'une machine DMZ

## 1. Situation

Dans le cadre du laboratoire SecureTech, une machine **VulnHub Basic Pentesting** a été volontairement placée dans la **DMZ** afin de simuler un serveur exposé à Internet.

La machine cible utilise l'adresse IP `192.168.30.101` et appartient au réseau `192.168.30.0/24`.

Deux services ont été exposés à travers le mécanisme de **Port Forwarding de pfSense** :

- `80/tcp` : service Web ;
- `2121/tcp` : service FTP.

Le service FTP constitue le **vecteur d'attaque principal** retenu pour ce scénario. L'énumération du service a permis d'identifier **ProFTPD 1.3.x**, dont la version pouvait être exploitée dans les conditions du laboratoire.

La machine attaquante est une instance **Kali Linux** connectée au réseau NAT de VMware avec l'adresse IP `192.168.215.133`.

Kali n'est donc pas directement connectée au réseau DMZ. L'accès aux services exposés s'effectue à travers le NAT VMware puis le pare-feu pfSense.

Les règles de Port Forwarding de pfSense permettent uniquement l'exposition des ports nécessaires vers la machine DMZ.

Cette architecture permet de reproduire un scénario réaliste dans lequel un attaquant externe tente de compromettre un service exposé tout en évaluant les mécanismes de segmentation et de détection présents dans l'infrastructure.

---

## 2. Tâche

L'objectif était de réaliser une **compromission contrôlée** de la machine DMZ afin d'évaluer :

- la surface d'exposition du serveur ;
- l'identification des services accessibles ;
- l'identification du service FTP et de sa version ;
- la possibilité d'exploiter la vulnérabilité ;
- le niveau de privilèges obtenu après exploitation ;
- les possibilités de reconnaissance depuis la machine compromise ;
- la capacité de la segmentation réseau à limiter le mouvement latéral ;
- la visibilité fournie par **Suricata IDS** ;
- la capacité de **Suricata IPS** à bloquer l'activité offensive.

Le scénario a été réalisé dans un environnement de laboratoire isolé et contrôlé.

---

## 3. Reconnaissance externe

### 3.1 Identification de la cible

La reconnaissance a été réalisée depuis Kali Linux :

text
Kali Linux
192.168.215.133


La cible recherchée se trouve dans la DMZ :

192.168.30.101

L'accès depuis Kali est rendu possible par les règles de Port Forwarding configurées sur pfSense.

La reconnaissance permet donc de tester la surface réellement exposée à l'extérieur plutôt que d'effectuer directement un scan depuis le réseau interne.

3.2 Scans Nmap

Plusieurs techniques de reconnaissance ont été utilisées avec Nmap au cours du scénario.

Les options utilisées comprennent notamment :

nmap -sS 
nmap -sV <IP_CIBLE>
nmap -A <IP_CIBLE>

Des scripts NSE ont également été utilisés afin d'obtenir davantage d'informations sur les services détectés.

Cette phase avait pour objectif de passer progressivement d'une simple découverte de la cible à l'identification précise des services disponibles.

3.3 Services exposés

La reconnaissance a permis d'identifier les services suivants :

Port	Service	État	Rôle
80/tcp	HTTP	Ouvert	Service Web
2121/tcp	FTP	Ouvert	Service FTP

Le port 2121/tcp représente le principal point d'intérêt du scénario.

4. Identification du service FTP

L'énumération du port 2121/tcp a permis d'identifier le service ProFTPD 1.3.x.

L'identification de la technologie et de sa version constitue une étape déterminante avant l'exploitation.

La démarche suivie a été :

découvrir le port ;
identifier le service ;
identifier sa version ;
rechercher une vulnérabilité correspondante ;
valider l'exploitation dans le laboratoire.

Le service FTP constitue donc le vecteur d'attaque unique retenu pour ce scénario.

Aucun autre service n'a été utilisé comme vecteur principal de compromission.

5. Recherche de vulnérabilité

Après l'identification de ProFTPD, une recherche a été effectuée avec Metasploit afin d'identifier une méthode d'exploitation correspondant au service découvert.

L'objectif était de déterminer si le service FTP pouvait être exploité depuis la position réseau de Kali.

Cette phase permet de passer de la reconnaissance à la validation d'un scénario d'exploitation.

La vulnérabilité identifiée sur le service FTP a ensuite été exploitée dans le cadre du laboratoire.

6. Exploitation du FTP
6.1 Lancement de l'exploitation

L'exploitation a été réalisée depuis Kali Linux à l'aide de Metasploit.

L'objectif était d'obtenir un accès distant à la machine VulnHub située dans la DMZ.

L'exploitation a abouti à l'ouverture d'un shell sur la machine cible.

La session obtenue était une session shell et non une session Meterpreter.

Cette étape confirme qu'un attaquant disposant d'un accès au service FTP exposé pouvait dépasser le simple accès au service et obtenir une interaction directe avec le système.

6.2 Reverse shell

L'exploitation a permis d'obtenir un reverse shell sur la machine VulnHub.

Le message d'ouverture du shell dans Metasploit constitue la preuve de la réussite de cette phase.

La compromission peut alors être considérée comme effective : l'attaquant dispose désormais d'un accès interactif au système compromis.

7. Vérification des privilèges

Après l'ouverture du shell, une vérification immédiate du compte utilisé a été réalisée.

La commande utilisée était :

whoami

Le résultat obtenu était :

root

L'exploitation a donc fourni directement un accès avec les privilèges root.

Cette information est particulièrement importante dans l'analyse du scénario.

Il ne s'agit pas simplement d'une détection d'une vulnérabilité ou d'un accès limité au service FTP : l'attaquant dispose désormais de privilèges élevés sur le système compromis.

8. Vérification de la configuration réseau

Une fois le shell root obtenu, la configuration réseau de la machine compromise a été vérifiée.

La commande utilisée était :

ip a

Cette commande a permis de confirmer la présence de la machine dans le réseau DMZ.

L'adresse IP de la machine compromise est :

192.168.30.101

Cette vérification permet de confirmer que l'attaquant a quitté son environnement initial situé sur le NAT VMware pour obtenir un accès au système hébergé dans la DMZ.

9. Reconnaissance post-compromission
9.1 Objectif

Après l'obtention des privilèges root, une nouvelle phase de reconnaissance a été réalisée directement depuis la machine compromise.

L'objectif était de déterminer si la compromission de la machine DMZ permettait d'obtenir une visibilité sur d'autres machines ou sur les autres segments de l'infrastructure.

Cette étape est essentielle pour évaluer le risque de mouvement latéral.

9.2 ARP Scan

Les commandes suivantes ont été utilisées :

arp-scan -l

et :

arp-scan --localnet

Les résultats ont été analysés afin d'identifier les hôtes visibles depuis le réseau auquel appartient la machine compromise.

10. Analyse de la visibilité réseau

Les résultats de la reconnaissance ont montré que la machine compromise restait principalement limitée au réseau :

192.168.30.0/24

La reconnaissance ne permettait pas d'obtenir une visibilité équivalente sur les autres segments de l'infrastructure.

Cette observation est importante car elle permet de mesurer concrètement l'efficacité de la segmentation après une compromission.

Même avec un accès root sur la machine DMZ, l'attaquant ne dispose donc pas automatiquement d'une visibilité complète sur le réseau interne.

11. Reconnaissance Nmap depuis la machine compromise
11.1 Installation de Nmap

Afin de poursuivre l'analyse depuis la machine compromise, Nmap a été installé sur celle-ci.

Nmap n'était pas présent initialement sur le système VulnHub.

L'accès root obtenu lors de l'exploitation permettait d'effectuer cette opération sans devoir réaliser une nouvelle élévation de privilèges.

Cette situation constitue également une illustration de l'impact d'une compromission avec des privilèges élevés : l'attaquant peut modifier l'environnement du système compromis et installer les outils nécessaires à la poursuite de ses opérations.

11.2 Scan du réseau DMZ

Une reconnaissance Nmap a ensuite été effectuée depuis la machine compromise sur le réseau :

192.168.30.0/24

Les résultats ont principalement fait apparaître les éléments correspondant au réseau local observé, notamment la machine compromise et l'adresse de broadcast.

Aucun ensemble d'hôtes appartenant aux autres segments internes n'a été découvert depuis cette position.

12. Vérification du mouvement latéral
12.1 Test vers le contrôleur de domaine

Afin de vérifier explicitement la séparation entre la DMZ et le réseau interne, un test a été effectué vers le contrôleur de domaine.

Le contrôleur de domaine utilise l'adresse :

192.168.10.103

Une requête ICMP a été envoyée depuis la machine compromise vers cette adresse.

Aucune réponse n'a été obtenue.

12.2 Analyse

Ce résultat est cohérent avec les résultats précédents :

la reconnaissance ARP reste limitée au réseau DMZ ;
Nmap ne permet pas de découvrir les hôtes des autres segments ;
la communication ICMP vers le contrôleur de domaine n'aboutit pas.

La segmentation réseau limite donc les possibilités de progression depuis la machine compromise.

Il convient toutefois de préciser que l'absence de réponse ICMP ne constitue pas, à elle seule, la preuve que tous les protocoles vers 192.168.10.103 sont bloqués.

Le résultat doit être interprété avec les autres tests de reconnaissance et avec la politique de filtrage mise en place sur pfSense.

13. Impact de la compromission

La vulnérabilité du service FTP a permis d'obtenir un shell root sur la machine DMZ.

L'attaquant dispose donc d'un niveau de contrôle élevé sur le système compromis.

Dans les conditions du laboratoire, cet accès permettait notamment :

d'exécuter des commandes avec des privilèges élevés ;
d'installer des outils ;
d'effectuer une reconnaissance supplémentaire ;
de modifier l'environnement local ;
d'interagir avec les services présents sur la machine ;
d'interrompre les services hébergés.

Dans le cadre du scénario, la machine compromise a finalement été arrêtée à distance.

Cette action permet de démontrer l'un des impacts possibles d'une compromission : la perte de disponibilité des services hébergés sur la machine compromise.

L'arrêt de la machine ne constitue cependant pas une compromission des autres systèmes de l'infrastructure.

Les tests réalisés n'ont pas démontré de mouvement latéral réussi vers les réseaux internes.

14. Détection avec Suricata IDS

Pendant les phases de reconnaissance et d'exploitation, Suricata était configuré en mode IDS.

Les activités provenant de Kali Linux vers la DMZ étaient observées au niveau de l'interface correspondant au trafic entre Internet et la DMZ.

Les différents tests ont généré des alertes Suricata.

L'IDS a donc permis de conserver une visibilité sur l'activité de l'attaquant pendant le scénario.

Cette phase est importante car elle permet d'observer une situation dans laquelle l'attaque est détectée mais n'est pas automatiquement interrompue.

L'attaquant a ainsi pu poursuivre le scénario jusqu'à l'obtention du reverse shell.

15. Passage de l'IDS à l'IPS

Après avoir réalisé les différents tests en mode IDS, Suricata a été configuré en mode IPS.

L'objectif était de reproduire une nouvelle phase de reconnaissance depuis Kali Linux et de comparer le comportement du système avec celui observé précédemment.

La même source d'attaque a été utilisée :

Kali Linux
192.168.215.133

La différence principale réside donc dans le mode de fonctionnement de Suricata.

En mode IDS, le trafic suspect est détecté et enregistré.

En mode IPS, la détection peut être accompagnée d'une action de blocage.

16. Nouvelle reconnaissance en mode IPS

Après activation de l'IPS, une nouvelle reconnaissance Nmap a été effectuée depuis Kali Linux.

Suricata a alors identifié le trafic comme suspect.

L'alerte observée était :

ET SCAN Suspicious inbound to mySQL port 3306

À la suite de cette détection, l'adresse IP de Kali Linux :

192.168.215.133

a été bloquée par Suricata IPS.

17. Vérification du blocage

Après le blocage de l'adresse IP de Kali, une nouvelle tentative de reconnaissance Nmap a été effectuée.

Cette fois, Nmap ne retrouvait plus la cible et le scan ne permettait plus d'obtenir les résultats observés précédemment.

L'activité offensive a donc été interrompue.

Cette vérification permet de confirmer expérimentalement la différence entre le fonctionnement IDS et le fonctionnement IPS dans le laboratoire.

18. Comparaison IDS / IPS
Élément	IDS	IPS
Détection du trafic suspect	Oui	Oui
Génération d'alertes	Oui	Oui
Enregistrement des événements	Oui	Oui
Blocage du trafic	Non	Oui
Blocage de l'IP de Kali	Non	Oui
Poursuite du scan	Possible	Interrompue
Fonction principale	Détection	Détection + prévention

L'expérimentation permet donc de mettre en évidence la différence opérationnelle entre les deux modes.

Avec l'IDS, l'activité malveillante est visible et enregistrée.

Avec l'IPS, la détection est associée à une capacité de réaction permettant de bloquer l'activité identifiée.

19. Résultats du scénario
Étape	Résultat
Reconnaissance externe	Réussie
Identification du port 80/tcp	Réussie
Identification du port 2121/tcp	Réussie
Identification de ProFTPD	Réussie
Recherche de vulnérabilité	Réalisée
Exploitation du FTP	Réussie
Reverse shell	Obtenu
Ouverture du shell	Réussie
whoami	root
Vérification avec ip a	DMZ confirmée
ARP Scan	Réalisé
Reconnaissance Nmap depuis la DMZ	Réalisée
Découverte des réseaux internes	Non réalisée
Test ICMP vers 192.168.10.103	Aucune réponse
Mouvement latéral	Non réalisé
Arrêt de la machine DMZ	Réalisé
Détection par Suricata IDS	Réussie
Passage en mode IPS	Réalisé
Détection en mode IPS	Réussie
Blocage de Kali	Réussi
Nouvelle reconnaissance après blocage	Interrompue
20. Analyse de sécurité
20.1 Le service FTP représente le point d'entrée

Le scénario démontre qu'un service exposé à Internet peut constituer un point d'entrée critique lorsqu'une vulnérabilité exploitable est présente.

Dans ce laboratoire, le port 2121/tcp était volontairement exposé vers la machine DMZ.

L'identification de ProFTPD a permis de poursuivre l'analyse jusqu'à l'exploitation.

Le FTP représente donc le vecteur d'attaque unique du scénario.

20.2 La détection ne suffit pas à elle seule

Suricata IDS a permis de détecter et d'enregistrer les activités suspectes.

Cependant, en mode IDS, la détection n'a pas empêché la poursuite du scénario.

L'attaquant a donc pu atteindre l'objectif d'exploitation et obtenir un shell root.

Cette situation démontre qu'un mécanisme de détection doit être intégré dans une stratégie de défense plus large.

20.3 La segmentation limite la portée de la compromission

Après l'obtention du shell root, une reconnaissance supplémentaire a été réalisée depuis la machine compromise.

Les tests effectués avec arp-scan, Nmap et le test ICMP vers le contrôleur de domaine n'ont pas démontré de mouvement latéral vers les réseaux internes.

La segmentation constitue donc une seconde couche de défense.

Même après la compromission du serveur DMZ, l'attaquant ne dispose pas automatiquement d'un accès au reste de l'infrastructure.

20.4 L'IPS permet une réponse automatique

Le passage en mode IPS a apporté une différence concrète.

Lors d'une nouvelle reconnaissance, Suricata a généré l'alerte :

ET SCAN Suspicious inbound to mySQL port 3306

L'adresse IP de Kali 192.168.215.133 a ensuite été bloquée.

Une nouvelle tentative de reconnaissance n'a plus permis de retrouver la cible.

L'IPS a donc ajouté une capacité de prévention au mécanisme de détection.

21. Défense en profondeur

Le scénario permet de mettre en évidence plusieurs couches de sécurité complémentaires.

Couche 1 — Exposition contrôlée

Seuls les ports nécessaires au scénario sont exposés :

HTTP 80/tcp ;
FTP 2121/tcp.
Couche 2 — Segmentation

La machine vulnérable est isolée dans la DMZ.

La compromission de cette machine ne donne pas automatiquement accès aux autres réseaux internes.

Couche 3 — Détection

Suricata IDS permet de détecter et d'enregistrer les activités suspectes.

Couche 4 — Prévention

Suricata IPS ajoute une capacité de blocage lorsqu'une activité suspecte est détectée.

Couche 5 — Confinement

Même après obtention de privilèges root, les tests de reconnaissance n'ont pas permis de démontrer un mouvement latéral vers les autres segments.

L'objectif de la défense en profondeur n'est donc pas de supposer qu'une seule mesure empêchera toute compromission.

L'objectif est de faire en sorte que l'échec d'une couche ne permette pas automatiquement à l'attaquant de compromettre toute l'infrastructure.

22. Limites du scénario

Plusieurs limites doivent être prises en compte dans l'interprétation des résultats.

Le test de communication vers le contrôleur de domaine a été effectué avec ICMP. Une absence de réponse au ping ne permet pas, à elle seule, de conclure que tous les protocoles vers le contrôleur de domaine sont bloqués.

De même, aucun mouvement latéral réussi vers un serveur interne n'a été réalisé.

Les conclusions de ce scénario sont donc limitées aux éléments effectivement vérifiés :

compromission de la machine DMZ ;
obtention d'un shell root ;
reconnaissance post-compromission ;
visibilité principalement limitée au réseau DMZ ;
absence de réponse ICMP du contrôleur de domaine ;
détection des activités par Suricata IDS ;
blocage de Kali par Suricata IPS ;
interruption de la reconnaissance après le blocage.

Cette précision permet de conserver une documentation fidèle aux tests réellement effectués.


23. Compétences démontrées

Nmap · NSE · Metasploit · ProFTPD · Reverse Shell · Linux · Suricata IDS/IPS · pfSense · NAT · Port Forwarding · DMZ · Segmentation réseau · Reconnaissance réseau · Post-exploitation · Analyse de sécurité · Red Team · Blue Team

Conclusion

Ce scénario démontre concrètement comment un service exposé à Internet peut devenir un point d'entrée dans une infrastructure lorsqu'il présente une vulnérabilité exploitable.

L'attaque a suivi une progression structurée :

Reconnaissance → Identification du service → Identification de ProFTPD → Recherche de vulnérabilité → Exploitation → Reverse shell → Accès root → Reconnaissance post-compromission → Vérification du mouvement latéral

L'exploitation du service FTP a permis d'obtenir un shell root sur la machine DMZ 192.168.30.101.

La compromission a ensuite été utilisée pour évaluer la capacité de l'attaquant à progresser dans l'infrastructure.

Les tests réalisés depuis la machine compromise n'ont pas permis de démontrer un mouvement latéral vers les autres segments internes.

La segmentation réseau a ainsi limité la portée de la compromission.La cible recherchée se trouve dans la DMZ :

192.168.30.101

L'accès depuis Kali est rendu possible par les règles de Port Forwarding configurées sur pfSense.

La reconnaissance permet donc de tester la surface réellement exposée à l'extérieur plutôt que d'effectuer directement un scan depuis le réseau interne.
3.2 Scans Nmap

Plusieurs techniques de reconnaissance ont été utilisées avec Nmap au cours du scénario.

Les options utilisées comprennent notamment :

nmap -sS <IP_CIBLE>
nmap -sV <IP_CIBLE>
nmap -A <IP_CIBLE>

Des scripts NSE ont également été utilisés afin d'obtenir davantage d'informations sur les services détectés.

Cette phase avait pour objectif de passer progressivement d'une simple découverte de la cible à l'identification précise des services disponibles.

3.3 Services exposés

La reconnaissance a permis d'identifier les services suivants :
Port	Service	État	Rôle
80/tcp	HTTP	Ouvert	Service Web
2121/tcp	FTP	Ouvert	Service FTP

Le port 2121/tcp représente le principal point d'intérêt du scénario.

4. Identification du service FTP

L'énumération du port 2121/tcp a permis d'identifier le service ProFTPD 1.3.x.

L'identification de la technologie et de sa version constitue une étape déterminante avant l'exploitation.

La démarche suivie a été :

    découvrir le port ;

    identifier le service ;

    identifier sa version ;

    rechercher une vulnérabilité correspondante ;

    valider l'exploitation dans le laboratoire.

Le service FTP constitue donc le vecteur d'attaque unique retenu pour ce scénario.

Aucun autre service n'a été utilisé comme vecteur principal de compromission.

5. Recherche de vulnérabilité

Après l'identification de ProFTPD, une recherche a été effectuée avec Metasploit afin d'identifier une méthode d'exploitation correspondant au service découvert.

L'objectif était de déterminer si le service FTP pouvait être exploité depuis la position réseau de Kali.

Cette phase permet de passer de la reconnaissance à la validation d'un scénario d'exploitation.

La vulnérabilité identifiée sur le service FTP a ensuite été exploitée dans le cadre du laboratoire.

6. Exploitation du FTP
6.1 Lancement de l'exploitation

L'exploitation a été réalisée depuis Kali Linux à l'aide de Metasploit.

L'objectif était d'obtenir un accès distant à la machine VulnHub située dans la DMZ.

L'exploitation a abouti à l'ouverture d'un shell sur la machine cible.

La session obtenue était une session shell et non une session Meterpreter.

Cette étape confirme qu'un attaquant disposant d'un accès au service FTP exposé pouvait dépasser le simple accès au service et obtenir une interaction directe avec le système.

6.2 Reverse shell

L'exploitation a permis d'obtenir un reverse shell sur la machine VulnHub.

Le message d'ouverture du shell dans Metasploit constitue la preuve de la réussite de cette phase.

La compromission peut alors être considérée comme effective : l'attaquant dispose désormais d'un accès interactif au système compromis.


7. Vérification des privilèges

Après l'ouverture du shell, une vérification immédiate du compte utilisé a été réalisée.

La commande utilisée était :

whoami

Le résultat obtenu était :

root

L'exploitation a donc fourni directement un accès avec les privilèges root.

Cette information est particulièrement importante dans l'analyse du scénario.

Il ne s'agit pas simplement d'une détection d'une vulnérabilité ou d'un accès limité au service FTP : l'attaquant dispose désormais de privilèges élevés sur le système compromis.

8. Vérification de la configuration réseau

Une fois le shell root obtenu, la configuration réseau de la machine compromise a été vérifiée.

La commande utilisée était :

ip a

Cette commande a permis de confirmer la présence de la machine dans le réseau DMZ.

L'adresse IP de la machine compromise est :

192.168.30.101

Cette vérification permet de confirmer que l'attaquant a quitté son environnement initial situé sur le NAT VMware pour obtenir un accès au système hébergé dans la DMZ.

9. Reconnaissance post-compromission
9.1 Objectif

Après l'obtention des privilèges root, une nouvelle phase de reconnaissance a été réalisée directement depuis la machine compromise.

L'objectif était de déterminer si la compromission de la machine DMZ permettait d'obtenir une visibilité sur d'autres machines ou sur les autres segments de l'infrastructure.

Cette étape est essentielle pour évaluer le risque de mouvement latéral.
9.2 ARP Scan

Les commandes suivantes ont été utilisées :

arp-scan -l

et :

arp-scan --localnet

Les résultats ont été analysés afin d'identifier les hôtes visibles depuis le réseau auquel appartient la machine compromise.

10. Analyse de la visibilité réseau

Les résultats de la reconnaissance ont montré que la machine compromise restait principalement limitée au réseau :

192.168.30.0/24

La reconnaissance ne permettait pas d'obtenir une visibilité équivalente sur les autres segments de l'infrastructure.

Cette observation est importante car elle permet de mesurer concrètement l'efficacité de la segmentation après une compromission.

Même avec un accès root sur la machine DMZ, l'attaquant ne dispose donc pas automatiquement d'une visibilité complète sur le réseau interne.

11. Reconnaissance Nmap depuis la machine compromise
11.1 Installation de Nmap

Afin de poursuivre l'analyse depuis la machine compromise, Nmap a été installé sur celle-ci.

Nmap n'était pas présent initialement sur le système VulnHub.

L'accès root obtenu lors de l'exploitation permettait d'effectuer cette opération sans devoir réaliser une nouvelle élévation de privilèges.

Cette situation constitue également une illustration de l'impact d'une compromission avec des privilèges élevés : l'attaquant peut modifier l'environnement du système compromis et installer les outils nécessaires à la poursuite de ses opérations.
11.2 Scan du réseau DMZ

Une reconnaissance Nmap a ensuite été effectuée depuis la machine compromise sur le réseau :

192.168.30.0/24

Les résultats ont principalement fait apparaître les éléments correspondant au réseau local observé, notamment la machine compromise et l'adresse de broadcast.

Aucun ensemble d'hôtes appartenant aux autres segments internes n'a été découvert depuis cette position.


12. Vérification du mouvement latéral
12.1 Test vers le contrôleur de domaine

Afin de vérifier explicitement la séparation entre la DMZ et le réseau interne, un test a été effectué vers le contrôleur de domaine.

Le contrôleur de domaine utilise l'adresse :

192.168.10.103

Une requête ICMP a été envoyée depuis la machine compromise vers cette adresse.

Aucune réponse n'a été obtenue.


12.2 Analyse

Ce résultat est cohérent avec les résultats précédents :

    la reconnaissance ARP reste limitée au réseau DMZ ;

    Nmap ne permet pas de découvrir les hôtes des autres segments ;

    la communication ICMP vers le contrôleur de domaine n'aboutit pas.

La segmentation réseau limite donc les possibilités de progression depuis la machine compromise.

Il convient toutefois de préciser que l'absence de réponse ICMP ne constitue pas, à elle seule, la preuve que tous les protocoles vers 192.168.10.103 sont bloqués.

Le résultat doit être interprété avec les autres tests de reconnaissance et avec la politique de filtrage mise en place sur pfSense.

13. Impact de la compromission

La vulnérabilité du service FTP a permis d'obtenir un shell root sur la machine DMZ.

L'attaquant dispose donc d'un niveau de contrôle élevé sur le système compromis.

Dans les conditions du laboratoire, cet accès permettait notamment :

    d'exécuter des commandes avec des privilèges élevés ;

    d'installer des outils ;

    d'effectuer une reconnaissance supplémentaire ;

    de modifier l'environnement local ;

    d'interagir avec les services présents sur la machine ;

    d'interrompre les services hébergés.

Dans le cadre du scénario, la machine compromise a finalement été arrêtée à distance.

Cette action permet de démontrer l'un des impacts possibles d'une compromission : la perte de disponibilité des services hébergés sur la machine compromise.

L'arrêt de la machine ne constitue cependant pas une compromission des autres systèmes de l'infrastructure.

Les tests réalisés n'ont pas démontré de mouvement latéral réussi vers les réseaux internes.

14. Détection avec Suricata IDS

Pendant les phases de reconnaissance et d'exploitation, Suricata était configuré en mode IDS.

Les activités provenant de Kali Linux vers la DMZ étaient observées au niveau de l'interface correspondant au trafic entre Internet et la DMZ.

Les différents tests ont généré des alertes Suricata.

L'IDS a donc permis de conserver une visibilité sur l'activité de l'attaquant pendant le scénario.

Cette phase est importante car elle permet d'observer une situation dans laquelle l'attaque est détectée mais n'est pas automatiquement interrompue.

L'attaquant a ainsi pu poursuivre le scénario jusqu'à l'obtention du reverse shell.


15. Passage de l'IDS à l'IPS

Après avoir réalisé les différents tests en mode IDS, Suricata a été configuré en mode IPS.

L'objectif était de reproduire une nouvelle phase de reconnaissance depuis Kali Linux et de comparer le comportement du système avec celui observé précédemment.

La même source d'attaque a été utilisée :

Kali Linux
192.168.215.133

La différence principale réside donc dans le mode de fonctionnement de Suricata.

En mode IDS, le trafic suspect est détecté et enregistré.

En mode IPS, la détection peut être accompagnée d'une action de blocage.

16. Nouvelle reconnaissance en mode IPS

Après activation de l'IPS, une nouvelle reconnaissance Nmap a été effectuée depuis Kali Linux.

Suricata a alors identifié le trafic comme suspect.

L'alerte observée était :

ET SCAN Suspicious inbound to mySQL port 3306

À la suite de cette détection, l'adresse IP de Kali Linux :

192.168.215.133

a été bloquée par Suricata IPS.

17. Vérification du blocage

Après le blocage de l'adresse IP de Kali, une nouvelle tentative de reconnaissance Nmap a été effectuée.

Cette fois, Nmap ne retrouvait plus la cible et le scan ne permettait plus d'obtenir les résultats observés précédemment.

L'activité offensive a donc été interrompue.

Cette vérification permet de confirmer expérimentalement la différence entre le fonctionnement IDS et le fonctionnement IPS dans le laboratoire.

18. Comparaison IDS / IPS
Élément	IDS	IPS
Détection du trafic suspect	Oui	Oui
Génération d'alertes	Oui	Oui
Enregistrement des événements	Oui	Oui
Blocage du trafic	Non	Oui
Blocage de l'IP de Kali	Non	Oui
Poursuite du scan	Possible	Interrompue
Fonction principale	Détection	Détection + prévention

L'expérimentation permet donc de mettre en évidence la différence opérationnelle entre les deux modes.

Avec l'IDS, l'activité malveillante est visible et enregistrée.

Avec l'IPS, la détection est associée à une capacité de réaction permettant de bloquer l'activité identifiée.

19. Résultats du scénario
Étape	Résultat
Reconnaissance externe	Réussie
Identification du port 80/tcp	Réussie
Identification du port 2121/tcp	Réussie
Identification de ProFTPD	Réussie
Recherche de vulnérabilité	Réalisée
Exploitation du FTP	Réussie
Reverse shell	Obtenu
Ouverture du shell	Réussie
whoami	root
Vérification avec ip a	DMZ confirmée
ARP Scan	Réalisé
Reconnaissance Nmap depuis la DMZ	Réalisée
Découverte des réseaux internes	Non réalisée
Test ICMP vers 192.168.10.103	Aucune réponse
Mouvement latéral	Non réalisé
Arrêt de la machine DMZ	Réalisé
Détection par Suricata IDS	Réussie
Passage en mode IPS	Réalisé
Détection en mode IPS	Réussie
Blocage de Kali	Réussi
Nouvelle reconnaissance après blocage	Interrompue
20. Analyse de sécurité

20.1 Le service FTP représente le point d'entrée

Le scénario démontre qu'un service exposé à Internet peut constituer un point d'entrée critique lorsqu'une vulnérabilité exploitable est présente.

Dans ce laboratoire, le port 2121/tcp était volontairement exposé vers la machine DMZ.

L'identification de ProFTPD a permis de poursuivre l'analyse jusqu'à l'exploitation.

Le FTP représente donc le vecteur d'attaque unique du scénario.

20.2 La détection ne suffit pas à elle seule

Suricata IDS a permis de détecter et d'enregistrer les activités suspectes.

Cependant, en mode IDS, la détection n'a pas empêché la poursuite du scénario.

L'attaquant a donc pu atteindre l'objectif d'exploitation et obtenir un shell root.

Cette situation démontre qu'un mécanisme de détection doit être intégré dans une stratégie de défense plus large.
20.3 La segmentation limite la portée de la compromission

Après l'obtention du shell root, une reconnaissance supplémentaire a été réalisée depuis la machine compromise.

Les tests effectués avec arp-scan, Nmap et le test ICMP vers le contrôleur de domaine n'ont pas démontré de mouvement latéral vers les réseaux internes.

La segmentation constitue donc une seconde couche de défense.

Même après la compromission du serveur DMZ, l'attaquant ne dispose pas automatiquement d'un accès au reste de l'infrastructure.
20.4 L'IPS permet une réponse automatique

Le passage en mode IPS a apporté une différence concrète.

Lors d'une nouvelle reconnaissance, Suricata a généré l'alerte :

ET SCAN Suspicious inbound to mySQL port 3306

L'adresse IP de Kali 192.168.215.133 a ensuite été bloquée.

Une nouvelle tentative de reconnaissance n'a plus permis de retrouver la cible.

L'IPS a donc ajouté une capacité de prévention au mécanisme de détection.
21. Défense en profondeur

Le scénario permet de mettre en évidence plusieurs couches de sécurité complémentaires.
Couche 1 — Exposition contrôlée

Seuls les ports nécessaires au scénario sont exposés :

    HTTP 80/tcp ;

    FTP 2121/tcp.

Couche 2 — Segmentation

La machine vulnérable est isolée dans la DMZ.

La compromission de cette machine ne donne pas automatiquement accès aux autres réseaux internes.
Couche 3 — Détection

Suricata IDS permet de détecter et d'enregistrer les activités suspectes.
Couche 4 — Prévention

Suricata IPS ajoute une capacité de blocage lorsqu'une activité suspecte est détectée.
Couche 5 — Confinement

Même après obtention de privilèges root, les tests de reconnaissance n'ont pas permis de démontrer un mouvement latéral vers les autres segments.

L'objectif de la défense en profondeur n'est donc pas de supposer qu'une seule mesure empêchera toute compromission.

L'objectif est de faire en sorte que l'échec d'une couche ne permette pas automatiquement à l'attaquant de compromettre toute l'infrastructure.
22. Limites du scénario

Plusieurs limites doivent être prises en compte dans l'interprétation des résultats.

Le test de communication vers le contrôleur de domaine a été effectué avec ICMP. Une absence de réponse au ping ne permet pas, à elle seule, de conclure que tous les protocoles vers le contrôleur de domaine sont bloqués.

De même, aucun mouvement latéral réussi vers un serveur interne n'a été réalisé.

Les conclusions de ce scénario sont donc limitées aux éléments effectivement vérifiés :

    compromission de la machine DMZ ;

    obtention d'un shell root ;

    reconnaissance post-compromission ;

    visibilité principalement limitée au réseau DMZ ;

    absence de réponse ICMP du contrôleur de domaine ;

    détection des activités par Suricata IDS ;

    blocage de Kali par Suricata IPS ;

    interruption de la reconnaissance après le blocage.

Cette précision permet de conserver une documentation fidèle aux tests réellement effectués.
23. Compétences démontrées

Nmap · NSE · Metasploit · ProFTPD · Reverse Shell · Linux · Suricata IDS/IPS · pfSense · NAT · Port Forwarding · DMZ · Segmentation réseau · Reconnaissance réseau · Post-exploitation · Analyse de sécurité · Red Team · Blue Team
Conclusion

Ce scénario démontre concrètement comment un service exposé à Internet peut devenir un point d'entrée dans une infrastructure lorsqu'il présente une vulnérabilité exploitable.

L'attaque a suivi une progression structurée :

Reconnaissance → Identification du service → Identification de ProFTPD → Recherche de vulnérabilité → Exploitation → Reverse shell → Accès root → Reconnaissance post-compromission → Vérification du mouvement latéral

L'exploitation du service FTP a permis d'obtenir un shell root sur la machine DMZ 192.168.30.101.

La compromission a ensuite été utilisée pour évaluer la capacité de l'attaquant à progresser dans l'infrastructure.

Les tests réalisés depuis la machine compromise n'ont pas permis de démontrer un mouvement latéral vers les autres segments internes.

La segmentation réseau a ainsi limité la portée de la compromission.

Le scénario a également permis de comparer expérimentalement les deux modes de fonctionnement de Suricata.

En IDS, les activités suspectes étaient détectées et enregistrées.

En IPS, une nouvelle reconnaissance a déclenché l'alerte :

ET SCAN Suspicious inbound to mySQL port 3306

L'adresse IP de Kali 192.168.215.133 a ensuite été bloquée et les nouvelles tentatives de reconnaissance n'ont plus permis de retrouver la cible.

L'expérimentation démontre ainsi l'intérêt d'une architecture reposant sur plusieurs couches de défense :

Exposition contrôlée → Segmentation → Détection → Blocage → Confinement

La compromission d'un point d'entrée ne conduit donc pas automatiquement à la compromission de l'ensemble de l'infrastructure.



Le scénario a également permis de comparer expérimentalement les deux modes de fonctionnement de Suricata.

En IDS, les activités suspectes étaient détectées et enregistrées.

En IPS, une nouvelle reconnaissance a déclenché l'alerte :

ET SCAN Suspicious inbound to mySQL port 3306

L'adresse IP de Kali 192.168.215.133 a ensuite été bloquée et les nouvelles tentatives de reconnaissance n'ont plus permis de retrouver la cible.

L'expérimentation démontre ainsi l'intérêt d'une architecture reposant sur plusieurs couches de défense :

Exposition contrôlée → Segmentation → Détection → Blocage → Confinement

La compromission d'un point d'entrée ne conduit donc pas automatiquement à la compromission de l'ensemble de l'infrastructure.
