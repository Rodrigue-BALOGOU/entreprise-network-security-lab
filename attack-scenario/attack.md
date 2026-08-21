# Scénario d'attaque — Compromission contrôlée d'une machine DMZ

## 1. Situation

Dans le cadre du laboratoire, une machine **VulnHub Basic Pentesting** a été volontairement placée dans la DMZ afin de simuler un serveur exposé et vulnérable.

La machine héberge notamment :

* un service **HTTP** sur le port `80` ;
* un service **FTP** sur le port `2121` ;
* une version vulnérable de **ProFTPD 1.3.x**.

L'objectif était d'évaluer la capacité de l'architecture à **détecter, contenir puis bloquer une attaque provenant du réseau de l'attaquant**.

## 2. Tâche

Le test devait permettre de vérifier :

* la visibilité des services exposés ;
* la capacité de **Suricata IDS** à détecter les activités suspectes ;
* l'exploitation contrôlée d'une vulnérabilité connue ;
* l'impact d'une compromission de la machine DMZ ;
* l'efficacité de la segmentation réseau ;
* la capacité de **Suricata IPS** à bloquer le trafic malveillant.

Le test a été réalisé dans un environnement de laboratoire isolé et contrôlé.

## 3. Actions

### 3.1 Reconnaissance

Depuis **Kali Linux**, plusieurs scans Nmap ont été réalisés :

```bash
nmap -sS <IP_CIBLE>
nmap -sV <IP_CIBLE>
nmap -A <IP_CIBLE>
```

Ces scans ont permis d'identifier les services exposés, notamment :

* HTTP `80/tcp`
* FTP `2121/tcp`
* ProFTPD `1.3.x`

Les activités de reconnaissance ont été détectées par **Suricata IDS** et remontées dans les alertes.

### 3.2 Recherche de vulnérabilité

Après identification de ProFTPD, une recherche a été effectuée dans la base de modules de **Metasploit** afin de vérifier l'existence d'une vulnérabilité connue et d'un module d'exploitation correspondant.

Cette étape a permis de confirmer que le service identifié pouvait être exploité dans le cadre du scénario.

### 3.3 Exploitation

L'exploitation contrôlée de la vulnérabilité a permis d'obtenir un **reverse shell** sur la machine située dans la DMZ.

Une vérification des privilèges a ensuite été effectuée :

```bash
whoami
```

Le résultat indiquait :

```text
root
```

La compromission permettait donc d'obtenir des privilèges élevés sur la machine vulnérable.

L'utilisation de commandes suspectes a également été remontée par Suricata.

### 3.4 Vérification du confinement

Après la compromission, une reconnaissance du réseau depuis la machine DMZ a été effectuée à l'aide d'ARP.

Le réseau directement visible était :

```text
192.168.30.0/24
```

Aucun accès permettant d'atteindre les autres segments de l'infrastructure n'a été obtenu.

Cette étape confirme que la segmentation réseau limite l'impact d'une compromission de la DMZ.

### 3.5 Passage de l'IDS à l'IPS

Dans un premier temps, le mode IDS permettait principalement de **détecter et signaler** les activités suspectes.

L'IPS a ensuite été activé afin d'ajouter une capacité de blocage.

Après activation de l'IPS, une nouvelle tentative de reconnaissance Nmap a été effectuée.

Le scan ne recevait plus de réponse de la cible et l'adresse IP de Kali Linux apparaissait comme bloquée par Suricata.

## 4. Résultats

Les tests ont permis d'obtenir les résultats suivants :

| Test                                       | Résultat    |
| ------------------------------------------ | ----------- |
| Détection des scans Nmap                   | Réussie     |
| Génération d'alertes Suricata              | Réussie     |
| Identification de ProFTPD vulnérable       | Réussie     |
| Exploitation contrôlée                     | Réussie     |
| Obtention d'un reverse shell               | Réussie     |
| Obtention des privilèges `root`            | Réussie     |
| Mouvement latéral vers les autres segments | Non réalisé |
| Confinement dans le réseau DMZ             | Vérifié     |
| Blocage du scan avec IPS                   | Réussi      |
| Blocage de l'IP de Kali                    | Vérifié     |

## 5. Analyse de sécurité

Le test met en évidence une différence importante entre les deux modes de fonctionnement.

**IDS :**

La menace est détectée et une alerte est générée, mais la détection seule ne garantit pas l'arrêt de l'attaque.

**IPS :**

La détection est associée à une action de blocage. Dans le scénario testé, l'activation de l'IPS a empêché les nouveaux scans provenant de Kali et entraîné le blocage de son adresse IP.

La segmentation apporte une seconde couche de protection : même après compromission de la machine DMZ avec des privilèges `root`, les tests réalisés n'ont pas permis d'atteindre les autres segments du réseau.

## 6. Conclusion

Ce scénario démontre l'intérêt de la défense en profondeur :

* une machine exposée peut être compromise malgré les mécanismes de détection ;
* **Suricata IDS** permet d'identifier les activités suspectes ;
* **Suricata IPS** permet ensuite de bloquer le trafic identifié comme malveillant ;
* la **segmentation réseau** limite l'impact d'une compromission de la DMZ.

Le résultat recherché n'est donc pas uniquement de détecter une attaque, mais de **réduire sa capacité de progression dans l'infrastructure**.

### Compétences démontrées

`Nmap` · `Metasploit` · `Suricata IDS/IPS` · `pfSense` · `DMZ` · `Segmentation réseau` · `Reconnaissance` · `Exploitation contrôlée` · `Analyse défensive` · `Linux`
