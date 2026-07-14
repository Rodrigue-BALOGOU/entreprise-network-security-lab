# 📊 Déploiement d'une plateforme de supervision d'entreprise avec Zabbix

## Présentation

La supervision est un élément essentiel d'une infrastructure informatique moderne. Elle permet de surveiller en permanence l'état des serveurs, des équipements réseau et des postes de travail afin de détecter les anomalies avant qu'elles n'affectent les utilisateurs.

Dans ce projet, j'ai déployé une plateforme complète de supervision basée sur **Zabbix 6.0 LTS** au sein de mon laboratoire d'entreprise **SecureTech**, dans le but de centraliser la surveillance des équipements critiques et de recevoir automatiquement des alertes en cas d'incident.

J'ai choisi **Zabbix** pour sa richesse fonctionnelle, son interface moderne, sa simplicité de prise en main et sa large adoption en entreprise. C'est aujourd'hui une solution Open Source incontournable que tout administrateur systèmes et réseaux devrait maîtriser.

---

# Objectifs

Les principaux objectifs de ce projet étaient :

- Déployer un serveur de supervision professionnel.
- Superviser les serveurs Windows.
- Superviser le contrôleur de domaine Active Directory.
- Superviser le pare-feu pfSense.
- Superviser les postes utilisateurs intégrés au domaine.
- Mettre en place des tableaux de bord temps réel.
- Générer des alertes automatiques.
- Recevoir les notifications par courrier électronique.
- Détecter les incidents avant les utilisateurs.

---

# Infrastructure supervisée

La plateforme surveille plusieurs composants critiques de l'infrastructure SecureTech.

| Equipement | Supervision |
|------------|-------------|
| Serveur Zabbix | ✅ |
| Contrôleur de domaine Windows Server 2022 | ✅ |
| Pare-feu pfSense | ✅ |
| Postes Windows du domaine | ✅ |

Chaque machine communique avec le serveur Zabbix grâce au **Zabbix Agent**, tandis que pfSense est supervisé à l'aide de l'agent Zabbix adapté à FreeBSD.

---

# Déploiement du serveur Zabbix

Installation du serveur Zabbix.

![](screenshots/configuration/01-install-zabbix.png)

Configuration initiale.

![](screenshots/configuration/03-config1-zabbix-installation.png)

Création des groupes d'hôtes.

![](screenshots/configuration/04-host-groups1.png)

![](screenshots/configuration/05-host-groups2.png)

---

# Déploiement des agents

Les agents Zabbix ont été installés sur l'ensemble des systèmes supervisés afin de remonter les métriques en temps réel.

Installation du service.

![](screenshots/configuration/07-zabbix-agent-service.png)

Configuration de l'agent.

![](screenshots/configuration/08-agent-configuration.png)

Déploiement sur Windows Server.

![](screenshots/configuration/34-install-agent-winserver.png)

Configuration finale.

![](screenshots/configuration/35-config-agentzabbix-winserver.png)

Intégration du contrôleur de domaine.

![](screenshots/configuration/36-dashboard-zabbix-up-with-dc-join.png)

---

# Supervision du pare-feu pfSense

Le pare-feu constitue un composant critique de l'architecture réseau. Il est supervisé afin de contrôler en permanence sa disponibilité et ses performances.

Configuration de l'hôte.

![](screenshots/configuration/06-pfsense-host-configuration.png)

Installation de l'agent.

![](screenshots/configuration/37-INSTALL-AGENT-ZABBIX-PFSENSE.png)

Validation de la connexion.

![](screenshots/configuration/38-Conf-hote-pfsense-zabbix.png)

Connexion réussie.

![](screenshots/configuration/39-confsuccessful-zabbix-dmz.png)

---

# Création des déclencheurs (Triggers)

Plusieurs déclencheurs personnalisés ont été créés afin de détecter automatiquement les incidents.

Création d'un trigger.

![](screenshots/configuration/40-create-triggers1.png)

Ajout du déclencheur.

![](screenshots/configuration/41-trigger-add-success.png)

Détection d'une indisponibilité.

![](screenshots/configuration/42-triggers-uptime.png)

Surveillance mémoire.

![](screenshots/configuration/43-trigger-memory-condition.png)

Détection d'une consommation mémoire élevée.

![](screenshots/configuration/44-trigger-memory-utilisation.png)

Surveillance des services Windows.

![](screenshots/configuration/45-trigger-server-restart.png)

Exemple de trigger personnalisé.

![](screenshots/configuration/46-template-exemple-create.png)

Détection automatique d'un service arrêté.

![](screenshots/configuration/47-trigger-active-directory-stop-running.png)

---

# Tableaux de bord

Les tableaux de bord permettent d'obtenir une vision globale de l'état de l'infrastructure.

Vue générale.

![](screenshots/configuration/10-probleme-dashboard1.png)

Tableau de bord avancé.

![](screenshots/configuration/11-probleme-dashboard2.png)

Nouveau Dashboard.

![](screenshots/configuration/32-dashboard-zabbix-new.png)

---

# Graphiques de supervision

Les métriques sont collectées en temps réel.

Utilisation CPU.

![](screenshots/configuration/13-cpu-usage-graph.png)

Mémoire.

![](screenshots/configuration/13-memory-utilisation.png)

Trafic réseau.

![](screenshots/configuration/14-network-traffic-graph.png)

Occupation disque.

![](screenshots/configuration/15-disk-usage-graph.png)

---

# Notifications par courrier électronique

Afin d'assurer une détection proactive des incidents, une notification automatique a été configurée via Gmail SMTP.

Configuration SMTP.

![](screenshots/configuration/48-config-alerte-smtp.png)

Configuration des utilisateurs.

![](screenshots/configuration/49-user-config-alerte.png)

Association du média.

![](screenshots/configuration/50-config-okay-alerte-user.png)

Test du message.

![](screenshots/configuration/51-test-message.png)

Configuration utilisateur.

![](screenshots/configuration/52-config-user.png)

Action de notification.

![](screenshots/configuration/56-config-action-trigger.png)

Configuration du média Gmail.

![](screenshots/configuration/56-config-ssmtp.png)

Validation.

![](screenshots/configuration/57-connected-okay.png)

Notification reçue.

![](screenshots/configuration/58-config-success-alerte-gmail.png)

---

# Difficultés rencontrées

Durant le projet, plusieurs difficultés techniques ont été rencontrées :

- Blocage des connexions SMTP par Suricata lors des tests.
- Port TCP 587 initialement bloqué sur pfSense.
- Configuration des mots de passe d'application Gmail.
- Association du média de notification au compte administrateur.
- Validation de l'ensemble de la chaîne Trigger → Action → Notification.

Ces problèmes ont été diagnostiqués puis corrigés jusqu'à obtenir un système de notification entièrement fonctionnel.

---

# Résultats obtenus

À l'issue du projet, la plateforme permet désormais :

- Une supervision centralisée de l'infrastructure.
- Une visibilité temps réel des équipements.
- Une détection proactive des incidents.
- La surveillance des performances des serveurs.
- Le suivi des ressources système.
- Des tableaux de bord dynamiques.
- L'envoi automatique d'alertes par courrier électronique.
- Une réduction du temps de détection des pannes.

---

# Compétences acquises

- Déploiement de Zabbix 6 LTS
- Administration Linux
- Supervision Windows
- Supervision pfSense
- Déploiement des agents Zabbix
- Création de Templates
- Création de Triggers
- Création d'Actions
- Configuration SMTP
- Dépannage réseau
- Analyse des incidents
- Supervision d'une infrastructure Active Directory

---

## Conclusion

Ce projet m'a permis de mettre en œuvre une plateforme de supervision complète, proche d'un environnement d'entreprise. Au-delà du déploiement technique, il m'a conduit à résoudre plusieurs problématiques réelles liées à la supervision, à la configuration des notifications et au diagnostic d'incidents.

La plateforme obtenue offre une surveillance centralisée, une détection proactive des anomalies et une capacité d'alerte automatique, contribuant ainsi à améliorer la disponibilité et la fiabilité de l'infrastructure informatique.
