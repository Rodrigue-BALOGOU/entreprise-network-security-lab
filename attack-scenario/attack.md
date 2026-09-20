# Défense — Détection, prévention et confinement

## Objectif

Après la compromission contrôlée de la machine VulnHub située dans la DMZ, l'objectif était d'évaluer les mécanismes de défense du laboratoire :

- détecter l'activité offensive avec Suricata IDS ;
- bloquer l'activité avec Suricata IPS ;
- vérifier la capacité de la segmentation réseau à limiter le mouvement latéral.

Le scénario a été réalisé depuis Kali Linux (`192.168.215.133`) vers la machine DMZ (`192.168.30.101`).

---

## 1. Détection avec Suricata IDS

Suricata a d'abord été utilisé en mode **IDS** afin d'observer les activités provenant de Kali Linux vers la DMZ.

Les phases de reconnaissance et d'exploitation ont généré des alertes dans Suricata.

Une alerte observée pendant les tests :

ET SCAN Suspicious inbound to mySQL port 3306

En mode IDS, Suricata permet de détecter et d'enregistrer les activités suspectes sans interrompre automatiquement le trafic.

Cela a permis de conserver une visibilité sur l'activité offensive pendant le scénario, jusqu'à la compromission de la machine DMZ.

2. Vérification de la segmentation réseau

Après l'obtention d'un shell root sur la machine DMZ, une reconnaissance a été réalisée directement depuis cette machine afin de vérifier la visibilité disponible sur les autres segments.

Les commandes utilisées avec arp-scan étaient :

arp-scan -l
arp-scan --localnet

La reconnaissance a principalement montré le réseau local de la machine compromise :

192.168.30.0/24

Nmap a ensuite été installé sur la machine compromise afin de poursuivre les vérifications.

Un scan du réseau DMZ a ensuite été réalisé.

Les résultats n'ont pas permis de découvrir d'hôtes appartenant aux autres segments internes.



## 3. Vérification du mouvement latéral

Après l'obtention d'un accès `root` sur la machine compromise, des tests de reconnaissance ont été réalisés depuis la DMZ afin de vérifier si cette compromission pouvait permettre d'atteindre ou de découvrir les autres segments du réseau.

Un test de connectivité a notamment été effectué vers le contrôleur de domaine :


192.168.10.103
Une requête ICMP a été envoyée depuis la machine compromise. Aucune réponse n'a été obtenue.

Des vérifications complémentaires ont également été réalisées avec arp-scan et Nmap. Les résultats obtenus ont principalement montré le réseau DMZ 192.168.30.0/24, sans découverte d'hôtes appartenant aux autres segments internes.

Les tests réalisés n'ont donc pas permis de démontrer un mouvement latéral réussi vers le réseau interne.

L'absence de réponse ICMP ne permet pas, à elle seule, de conclure que tous les protocoles vers le contrôleur de domaine sont bloqués. Ce résultat est donc interprété conjointement avec les autres tests de reconnaissance et la politique de filtrage appliquée sur pfSense.

Cette vérification montre que la compromission de la machine DMZ n'a pas automatiquement fourni une visibilité complète sur les autres segments de l'infrastructure.

## 4. Passage de Suricata en mode IPS

Après les tests réalisés en mode IDS, Suricata a été configuré en mode **IPS** afin d'évaluer sa capacité à bloquer l'activité offensive.

Une nouvelle reconnaissance Nmap a été effectuée depuis Kali Linux :


192.168.215.133

Suricata a détecté l'activité et a généré l'alerte suivante :
ET SCAN Suspicious inbound to mySQL port 3306

À la suite de cette détection, l'adresse IP de Kali Linux (192.168.215.133) a été bloquée.


5. Vérification du blocage

Après le blocage de Kali Linux, une nouvelle tentative de reconnaissance Nmap a été effectuée.

Contrairement aux tests précédents, la reconnaissance ne permettait plus d'obtenir les résultats observés avant le blocage.

L'activité offensive a donc été interrompue.

Cette vérification permet de mettre en évidence la différence opérationnelle entre les deux modes de Suricata :

IDS : détecte et journalise ;
IPS : détecte et peut bloquer l'activité identifiée.


## 6. Comparaison IDS / IPS

L'expérimentation a permis de comparer le comportement de Suricata en mode **IDS** (Intrusion Detection System) et en mode **IPS** (Intrusion Prevention System) dans les mêmes conditions de test.

| Fonction | IDS | IPS |
|---|---|---|
| Détection du trafic suspect | Oui | Oui |
| Génération d'alertes | Oui | Oui |
| Enregistrement des événements | Oui | Oui |
| Blocage automatique du trafic | Non | Oui |
| Blocage de l'adresse IP de Kali | Non | Oui |
| Poursuite de la reconnaissance après détection | Possible | Interrompue |

### Résultat de l'expérimentation

En mode **IDS**, Suricata a permis de détecter et d'enregistrer l'activité offensive tout en laissant le trafic poursuivre son chemin. La reconnaissance et les tests d'exploitation ont donc pu continuer.

En mode **IPS**, Suricata a ajouté une capacité de prévention. Lors du test réalisé depuis Kali Linux (`192.168.215.133`), l'activité a été détectée puis l'adresse IP attaquante a été bloquée. Une nouvelle tentative de reconnaissance n'a alors plus permis d'obtenir les résultats observés avant le blocage.

Cette comparaison met en évidence la différence opérationnelle entre les deux modes :

- **IDS** : visibilité, détection et journalisation ;
- **IPS** : visibilité, détection, journalisation et blocage.

Dans le cadre de ce laboratoire, le passage de l'IDS à l'IPS a donc permis de vérifier expérimentalement le passage d'une logique de **détection** à une logique de **prévention**.



## 7. Résultats du scénario de défense

Les résultats sont présentés à partir des éléments effectivement observés pendant les tests. Lorsque aucune mesure chiffrée n'a été relevée, le résultat est indiqué comme **non mesuré** plutôt que d'inventer une valeur.

| Indicateur | Résultat mesuré |
|---|---:|
| Modes Suricata testés | 2 — IDS et IPS |
| Machine attaquante | 1 — Kali Linux (`192.168.215.133`) |
| Machine cible | 1 — VulnHub (`192.168.30.101`) |
| Réseaux impliqués | 2 — NAT/WAN et DMZ |
| Services exposés identifiés | 2 — HTTP (80) et FTP (2121) |
| Détection par Suricata IDS | 1 scénario détecté |
| Alerte Suricata observée | `ET SCAN Suspicious inbound to mySQL port 3306` |
| Compromission de la machine DMZ | 1 — shell `root` obtenu |
| Reconnaissance depuis la machine compromise | 1 série de vérifications |
| Réseaux internes découverts depuis la DMZ | 0 démontré |
| Mouvement latéral vers le réseau interne | 0 réussite |
| Test vers le DC (`192.168.10.103`) | 1 — aucune réponse ICMP |
| Passage IDS → IPS | 1 |
| Détection en mode IPS | 1 activité détectée |
| Adresse IP Kali bloquée par l'IPS | 1 — `192.168.215.133` |
| Nouvelle reconnaissance après blocage | 1 — interrompue |
| Durée de détection/blocage | Non mesurée |
| Nombre total d'alertes Suricata | Non mesuré précisément |
| Taux de détection | Non calculé |
| Taux de blocage | Non calculé |

### Synthèse quantitative

Sur les éléments mesurables du scénario :

- **2 modes de protection** ont été testés : IDS et IPS.
- **1 machine attaquante** a été utilisée.
- **1 machine DMZ** a été compromise.
- **2 services exposés** ont été identifiés : HTTP/80 et FTP/2121.
- **1 accès `root`** a été obtenu lors de l'exploitation.
- **0 mouvement latéral réussi** vers le réseau interne n'a été démontré.
- **1 adresse IP attaquante** a été bloquée lors du test IPS.
- Après le blocage, **1 nouvelle tentative de reconnaissance** a été interrompue.

Les métriques telles que le **temps moyen de détection (MTTD)**, le **temps de blocage**, le **nombre exact d'alertes**, le **taux de détection** ou le **taux de blocage** n'ont pas été mesurées avec un protocole de chronométrage et de comptage dédié. Elles ne sont donc pas utilisées comme indicateurs chiffrés dans ce scénario.

Cette distinction permet de conserver des résultats **mesurables et vérifiables**, sans attribuer au laboratoire des performances qui n'ont pas été effectivement mesurées.





## 8. Défense en profondeur

Le scénario met en évidence plusieurs couches de défense complémentaires :

1. **Exposition contrôlée**  
   Les services de la machine vulnérable sont exposés à travers les règles de Port Forwarding de pfSense.

2. **Segmentation réseau**  
   La machine vulnérable est placée dans une DMZ distincte des réseaux internes.

3. **Détection**  
   Suricata IDS détecte et journalise les activités suspectes.

4. **Prévention**  
   Suricata IPS ajoute une capacité de blocage lorsqu'une activité est détectée.

5. **Confinement**  
   Les tests réalisés après compromission n'ont pas permis de démontrer un mouvement latéral vers les réseaux internes.

Cette approche permet de limiter la portée d'une compromission en combinant plusieurs mécanismes de sécurité.

## Compétences démontrées

`Suricata` · `IDS/IPS` · `pfSense` · `DMZ` · `Segmentation réseau` · `Nmap` · `arp-scan` · `Linux` · `Détection réseau` · `Analyse de logs` · `Prévention` · `Network Security` · `Defense in Depth`
