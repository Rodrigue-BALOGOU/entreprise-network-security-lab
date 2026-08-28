# Défense et réponse aux incidents

## 1. Situation

L'infrastructure SecureTech a été conçue selon une approche de défense en profondeur afin de limiter la surface d'attaque, détecter les activités suspectes et réduire l'impact potentiel d'une compromission.

Le réseau est segmenté en plusieurs zones et les communications entre ces zones sont contrôlées par le pare-feu **pfSense**.

Dans le scénario étudié, une machine située dans la DMZ expose un service FTP vers l'extérieur. Ce service constitue le vecteur d'attaque retenu dans le laboratoire.

L'exposition du service est volontaire et permet de reproduire un scénario réaliste dans lequel un attaquant tente d'exploiter un service accessible depuis Internet.

La défense ne repose cependant pas sur un seul mécanisme. Plusieurs composants interviennent à différents niveaux :

- **pfSense** pour le filtrage et le contrôle des flux réseau ;
- **Suricata** pour la détection des activités réseau suspectes ;
- **WEF** pour la centralisation des événements Windows ;
- **NXLog** pour la collecte des journaux réseau provenant de pfSense ;
- **Zabbix** pour la supervision de l'infrastructure ;
- **Active Directory et les GPO** pour le contrôle et le durcissement des systèmes Windows ;
- **la segmentation réseau** pour limiter les possibilités de déplacement latéral.

Cette architecture permet de mettre en œuvre une chaîne de défense allant de la détection jusqu'à la vérification après intervention.

---

## 2. Tâche

L'objectif de cette phase était de mettre en œuvre une démarche de défense permettant de répondre à une activité malveillante dirigée contre le service FTP exposé.

La tâche consistait à :

- surveiller les communications réseau ;
- détecter les activités suspectes ;
- identifier la machine et le service ciblés ;
- analyser les événements générés par l'infrastructure ;
- contrôler les communications avec le pare-feu ;
- limiter les possibilités de propagation ;
- conserver les journaux nécessaires à l'analyse ;
- appliquer une mesure de confinement lorsque cela est nécessaire ;
- vérifier l'efficacité de la mesure appliquée ;
- maintenir la supervision de l'infrastructure après l'incident.

La démarche adoptée suit une logique opérationnelle :

**Détecter → Analyser → Identifier → Contenir → Bloquer → Vérifier → Surveiller**

---

# 3. Action

## 3.1 Contrôle du périmètre réseau avec pfSense

Le premier niveau de défense est assuré par **pfSense**, qui constitue le point de contrôle des communications entre les différentes zones du réseau.

Les règles de filtrage appliquent une politique restrictive fondée sur le principe de moindre privilège.

Le trafic entrant est analysé par le pare-feu avant d'atteindre les ressources de l'infrastructure.

Dans le scénario étudié, seul le service FTP volontairement exposé est accessible depuis l'extérieur.

Les autres ressources internes ne sont pas directement exposées à Internet.

Cette architecture permet de réduire considérablement la surface d'attaque accessible depuis l'extérieur.

Le pare-feu permet également de conserver les traces des communications autorisées et bloquées, ce qui fournit une source importante pour l'analyse d'un incident.

---

## 3.2 Isolation du service exposé dans la DMZ

La machine hébergeant le service FTP est placée dans une **DMZ**.

Cette séparation constitue un mécanisme de protection important : une machine exposée à Internet ne doit pas disposer d'un accès direct aux ressources internes qui ne sont pas nécessaires à son fonctionnement.

La communication entre la DMZ et les réseaux internes est donc strictement contrôlée par pfSense.

L'objectif est de limiter les conséquences d'une éventuelle compromission du serveur exposé.

Même si l'attaquant parvient à compromettre le service FTP, cette compromission ne doit pas lui permettre d'accéder directement :

- au contrôleur de domaine ;
- au serveur de fichiers ;
- au serveur WSUS ;
- aux postes utilisateurs ;
- au serveur de supervision.

La segmentation constitue ainsi une première mesure de confinement.

---

## 3.3 Détection avec Suricata

Le deuxième niveau de défense repose sur **Suricata**.

Suricata est utilisé comme mécanisme de détection réseau afin d'analyser les communications et d'identifier les activités correspondant à des comportements suspects ou à des signatures connues.

Dans le cadre du scénario FTP, l'analyse du trafic permet d'obtenir une visibilité supplémentaire sur l'activité dirigée contre le service exposé.

Le rôle de Suricata est complémentaire à celui du pare-feu :

- pfSense contrôle les communications ;
- Suricata analyse le trafic afin d'identifier les activités suspectes.

Cette distinction permet d'éviter de confondre filtrage réseau et détection.

Une activité peut ainsi être observée par Suricata alors que le pare-feu assure parallèlement le contrôle de la communication.

---

## 3.4 Centralisation des événements Windows avec WEF

La défense réseau est complétée par la centralisation des événements Windows grâce à **Windows Event Forwarding (WEF)**.

Les machines Windows de l'infrastructure transmettent leurs événements au serveur collecteur **ST-FILESERVER01**.

Les événements collectés permettent notamment de surveiller :

- les authentifications ;
- les événements de sécurité ;
- les erreurs système ;
- les connexions RDP ;
- les événements Windows Defender ;
- les activités des postes clients ;
- les activités des serveurs.

La centralisation permet de conserver les événements au même endroit et facilite leur analyse lorsqu'un incident est détecté.

WEF fournit donc une visibilité principalement orientée vers les systèmes Windows et Active Directory.

---

## 3.5 Centralisation des journaux pfSense avec NXLog

Les journaux générés par pfSense sont également intégrés au système de centralisation.

pfSense transmet ses événements via **Syslog**.

**NXLog**, installé sur **ST-FILESERVER01**, permet de récupérer et de traiter les journaux provenant de pfSense.

Cette architecture permet de regrouper les événements réseau avec les événements Windows.

Les journaux peuvent notamment fournir des informations concernant :

- les connexions autorisées ;
- les connexions bloquées ;
- les règles de filtrage ;
- les communications NAT ;
- les événements système ;
- les communications entre les différentes zones.

La centralisation des logs facilite ainsi l'analyse chronologique d'un incident.

---

## 3.6 Corrélation des informations

Lorsqu'une activité suspecte est détectée, l'analyse ne doit pas se limiter à une seule source.

Les différentes sources disponibles dans l'infrastructure permettent de rapprocher les informations :

| Source | Informations principales |
|---|---|
| pfSense | Flux réseau, connexions autorisées et bloquées, NAT |
| Suricata | Activités et signatures réseau suspectes |
| WEF | Événements Windows et événements de sécurité |
| NXLog | Centralisation des journaux pfSense |
| Zabbix | État et disponibilité des équipements |
| Active Directory | Identités, authentifications et événements liés au domaine |

Cette approche permet de déterminer plus précisément :

- la source de l'activité ;
- la destination ;
- le service ciblé ;
- le moment de l'activité ;
- le comportement observé ;
- l'efficacité des règles de filtrage ;
- l'existence éventuelle d'autres événements associés.

La corrélation des informations améliore donc la qualité de l'analyse par rapport à l'utilisation d'une seule source.

---

# 4. Analyse et identification de l'incident

## 4.1 Identification de la source

Lorsqu'une activité suspecte est détectée, la première étape consiste à identifier la source du trafic.

Les informations recherchées sont notamment :

- l'adresse IP source ;
- l'adresse IP de destination ;
- le port ciblé ;
- le protocole utilisé ;
- la fréquence des connexions ;
- le résultat des communications ;
- les alertes générées.

Dans le scénario étudié, l'analyse est principalement orientée vers le service FTP exposé dans la DMZ.

Cette étape permet de déterminer si le trafic observé correspond à une activité légitime ou à une tentative d'attaque.

---

## 4.2 Identification du service ciblé

Le service ciblé est identifié à partir des informations réseau disponibles.

Dans le scénario étudié, le vecteur d'attaque est le service **FTP** exposé sur la machine de la DMZ.

L'analyse porte donc en priorité sur :

- les connexions vers le service FTP ;
- les tentatives répétées ;
- les événements générés par le pare-feu ;
- les alertes Suricata ;
- les journaux de la machine cible.

Cette approche permet de concentrer l'analyse sur le point d'entrée réellement exposé au lieu de considérer l'ensemble de l'infrastructure comme directement vulnérable.

---

# 5. Containment et blocage

## 5.1 Principe de confinement

Une fois l'activité qualifiée comme malveillante, la priorité est de limiter sa capacité à poursuivre l'attaque.

Le confinement consiste à empêcher ou réduire les communications provenant de la source identifiée vers la ressource ciblée.

Dans l'infrastructure, pfSense constitue le principal mécanisme permettant d'appliquer ce confinement.

Une règle de filtrage peut être utilisée pour empêcher une source identifiée comme malveillante de poursuivre ses communications avec le service concerné.

Cette intervention permet de réduire immédiatement la surface d'exposition sans modifier inutilement les autres composants du réseau.

---

## 5.2 Blocage au niveau du pare-feu

Le blocage au niveau de pfSense permet de contrôler précisément le trafic concerné.

La règle de blocage peut notamment être basée sur :

- l'adresse IP source ;
- l'adresse IP destination ;
- le protocole ;
- le port ;
- l'interface concernée.

L'objectif est d'appliquer une mesure ciblée plutôt qu'un blocage global susceptible d'interrompre les communications légitimes.

Cette approche respecte le principe de moindre privilège appliqué au filtrage réseau.

---

## 5.3 Protection des réseaux internes

La segmentation limite également les conséquences potentielles d'une compromission du serveur FTP.

Le serveur exposé se trouve dans la DMZ et les communications vers les réseaux internes sont contrôlées.

Les ressources internes restent ainsi protégées par plusieurs niveaux :

- filtrage pfSense ;
- séparation des zones réseau ;
- restrictions inter-interfaces ;
- politiques par défaut restrictives ;
- absence de communication inutile depuis la DMZ vers les réseaux internes.

La compromission potentielle d'un service exposé ne doit donc pas entraîner automatiquement une compromission du reste de l'infrastructure.

---

# 6. Vérification après intervention

## 6.1 Vérification du blocage

Après l'application d'une mesure de blocage, une nouvelle observation doit être effectuée.

Cette étape permet de vérifier que la mesure produit réellement le résultat attendu.

Les éléments à contrôler sont notamment :

- la règle pfSense est active ;
- le trafic de la source ciblée est refusé ;
- le service concerné n'est plus accessible depuis la source bloquée ;
- les événements de blocage sont enregistrés ;
- aucune communication non autorisée n'est observée.

Une mesure de sécurité n'est considérée comme réellement efficace qu'après vérification.

---

## 6.2 Vérification de l'absence de propagation

Après le confinement, l'analyse doit également vérifier qu'aucune activité suspecte n'a atteint les réseaux internes.

Une attention particulière doit être portée aux ressources critiques :

- contrôleur de domaine ;
- serveur de fichiers ;
- serveur WSUS ;
- postes clients ;
- serveur Zabbix.

Les journaux Windows, les logs réseau et les informations de supervision peuvent être utilisés pour rechercher d'éventuelles anomalies.

Cette étape permet de déterminer si l'incident est resté limité au périmètre initial ou s'il a eu un impact plus large.

---

# 7. Supervision avec Zabbix

Zabbix constitue un niveau supplémentaire de défense en assurant la supervision de l'infrastructure.

Les machines surveillées sont notamment :

- pfSense ;
- le serveur de fichiers ;
- le serveur WSUS ;
- le serveur Zabbix ;
- les postes clients RH, IT et Finance ;
- le contrôleur de domaine.

La supervision permet de surveiller la disponibilité et l'état général des équipements.

Elle permet notamment d'identifier :

- une indisponibilité ;
- une dégradation d'un service ;
- une anomalie de fonctionnement ;
- une consommation inhabituelle de ressources ;
- un problème apparu après une intervention.

Zabbix ne remplace donc pas le système de détection réseau ou la centralisation des logs. Il apporte une visibilité complémentaire sur l'état opérationnel de l'infrastructure.

---

# 8. Défense en profondeur

L'architecture SecureTech repose sur plusieurs mécanismes de protection complémentaires.

Chaque mécanisme répond à un objectif différent :

| Mécanisme | Rôle défensif |
|---|---|
| pfSense | Filtrage et contrôle des communications |
| DMZ | Isolation du service exposé |
| Suricata | Détection des activités réseau suspectes |
| WEF | Centralisation des événements Windows |
| NXLog | Centralisation des journaux pfSense |
| Zabbix | Supervision de l'infrastructure |
| Active Directory | Gestion centralisée des identités |
| GPO | Application des politiques de sécurité |
| Segmentation réseau | Limitation des communications entre zones |

Cette combinaison permet de réduire la dépendance à un seul mécanisme de sécurité.

Si une mesure ne suffit pas à empêcher une activité malveillante, les autres niveaux peuvent encore permettre de la détecter, de la contenir ou d'en limiter les conséquences.

---

# 9. Démarche de réponse à l'incident

La réponse appliquée au scénario suit une démarche structurée.

## 9.1 Détection

L'activité suspecte est détectée grâce aux mécanismes de surveillance disponibles dans l'infrastructure.

Les principales sources sont :

- Suricata ;
- pfSense ;
- les journaux centralisés ;
- Zabbix lorsque l'activité entraîne une anomalie observable.

## 9.2 Analyse

Les informations disponibles sont ensuite analysées afin de comprendre l'activité.

L'analyse porte notamment sur :

- la source ;
- la destination ;
- le service ciblé ;
- la chronologie ;
- les événements associés.

## 9.3 Identification

L'objectif est de déterminer précisément le périmètre de l'incident.

Dans le scénario étudié, le service FTP de la DMZ constitue le point d'entrée identifié.

## 9.4 Containment

La communication avec la source malveillante est limitée au niveau du pare-feu.

La segmentation empêche également l'accès direct aux ressources internes non autorisées.

## 9.5 Vérification

Après intervention, les journaux et le trafic réseau sont à nouveau observés afin de confirmer l'efficacité de la mesure.

## 9.6 Surveillance

La supervision est maintenue afin de détecter toute nouvelle anomalie après le traitement de l'incident.

---

# 10. Résultat

La mise en œuvre de cette architecture permet d'obtenir une chaîne de défense cohérente face au scénario d'attaque FTP.

La défense permet de :

- contrôler l'exposition du service ;
- isoler le serveur dans la DMZ ;
- surveiller le trafic réseau ;
- détecter les activités suspectes ;
- centraliser les événements ;
- analyser les communications ;
- limiter les communications malveillantes ;
- protéger les réseaux internes ;
- vérifier les effets du confinement ;
- maintenir la supervision après l'incident.

Le scénario démontre ainsi qu'une défense efficace ne repose pas uniquement sur le blocage d'une connexion.

Elle repose sur plusieurs étapes complémentaires :

**Détecter → Analyser → Identifier → Contenir → Bloquer → Vérifier → Surveiller**

---

# 11. Limites et améliorations futures

L'architecture actuelle constitue une base cohérente pour la mise en œuvre d'une démarche Blue Team dans le laboratoire.

Elle peut néanmoins être améliorée.

Une première évolution serait l'intégration d'une solution **SIEM** permettant de centraliser et corréler automatiquement :

- les événements Windows ;
- les événements Active Directory ;
- les logs pfSense ;
- les alertes Suricata ;
- les événements provenant des serveurs.

Une deuxième évolution consiste à renforcer l'audit de l'environnement Active Directory.

Un audit avec **PingCastle** pourra notamment être réalisé depuis une machine interne du laboratoire afin d'identifier les mauvaises pratiques ou faiblesses potentielles de la configuration Active Directory.

Les résultats de cet audit pourront ensuite être utilisés pour définir de nouvelles mesures de remédiation.

Le projet pourra ainsi suivre un cycle d'amélioration continue :

**Configuration → Hardening → Attaque → Détection → Défense → Remédiation → Audit → Amélioration**

---

# 12. Compétences démontrées

`pfSense` · `Suricata` · `WEF` · `NXLog` · `Zabbix` · `Active Directory` · `GPO` · `Segmentation réseau` · `Firewall` · `IDS/IPS` · `Centralisation des logs` · `Analyse d'incident` · `Blue Team` · `Défense en profondeur`

---

# Conclusion

La défense de l'infrastructure SecureTech repose sur une stratégie de **défense en profondeur** combinant filtrage réseau, segmentation, détection, centralisation des journaux et supervision.

Le service FTP constitue le seul vecteur d'attaque exposé dans le scénario. Son placement dans la DMZ permet de limiter son interaction avec les ressources internes.

En cas d'activité suspecte, plusieurs sources peuvent être utilisées pour identifier et analyser l'événement : pfSense, Suricata, WEF, NXLog et Zabbix.

La réponse repose ensuite sur le confinement de l'activité, le contrôle des communications et la vérification du résultat.

Cette démarche permet de reproduire un processus de défense proche d'une approche **Blue Team / SOC**, tout en démontrant que la sécurité de l'infrastructure repose sur plusieurs couches complémentaires plutôt que sur un mécanisme unique.

> **Détecter → Analyser → Identifier → Contenir → Bloquer → Vérifier → Surveiller**
