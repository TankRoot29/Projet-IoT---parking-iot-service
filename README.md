# 🚗 IoT Smart Parking & Edge Security Monitoring System

![ESP32](https://img.shields.io/badge/ESP32-IoT-blue)
![MQTT](https://img.shields.io/badge/MQTT-HiveMQ-green)
![NodeRED](https://img.shields.io/badge/Node--RED-ETL-red)
![InfluxDB](https://img.shields.io/badge/InfluxDB-TimeSeries-blue)
![Grafana](https://img.shields.io/badge/Grafana-Monitoring-orange)
![Docker](https://img.shields.io/badge/Docker-Containerized-blue)

---

## 📖 Présentation

**IoT Smart Parking & Edge Security Monitoring System** est une plateforme IoT distribuée permettant la gestion intelligente, la supervision temps réel et la sécurisation d’un parking connecté.

Le système repose sur un microcontrôleur **ESP32** chargé de gérer localement les capteurs, les barrières d’accès et les mécanismes de sécurité. Les données sont transmises via le protocole **MQTT** vers un broker cloud, puis traitées par **Node-RED** avant d’être stockées dans **InfluxDB** et visualisées dans **Grafana**.

L’architecture met également en œuvre un **système de détection d’intrusion embarqué (Edge IDS)** capable de détecter certaines tentatives de fraude ou d’accès non autorisé et de générer des alertes en temps réel.

---

# 🎯 Objectifs du Projet

* Automatiser la gestion d’un parking intelligent.
* Superviser le nombre de places disponibles en temps réel.
* Collecter des données environnementales (température et humidité).
* Détecter des comportements anormaux ou des tentatives d’intrusion.
* Mettre en œuvre une architecture IoT distribuée moderne.
* Exploiter des outils d’observabilité professionnels.
* Déployer une infrastructure entièrement conteneurisée.

---

# 🏗️ Architecture Générale

```text
┌─────────────────────────────────┐
│          ESP32 EDGE             │
│                                 │
│ • HC-SR04 Entrée                │
│ • HC-SR04 Sortie                │
│ • DHT22                         │
│ • Servo Entrée                  │
│ • Servo Sortie                  │
│ • LCD 16x2 I2C                  │
│ • LEDs                          │
│ • Buzzer                        │
│ • Edge IDS                      │
└───────────────┬─────────────────┘
                │ MQTT
                ▼
┌─────────────────────────────────┐
│       HiveMQ MQTT Broker        │
└───────────────┬─────────────────┘
                │
                ▼
┌─────────────────────────────────┐
│           Node-RED              │
│      ETL & Data Routing         │
└───────────────┬─────────────────┘
                │
                ▼
┌─────────────────────────────────┐
│            InfluxDB             │
│      Time-Series Database       │
└───────────────┬─────────────────┘
                │
                ▼
┌─────────────────────────────────┐
│            Grafana              │
│ Monitoring & Security Dashboard │
└─────────────────────────────────┘
```

---

# ✨ Fonctionnalités

## 🚘 Gestion Automatique du Parking

* Détection des véhicules à l'entrée.
* Détection des véhicules à la sortie.
* Ouverture automatique des barrières.
* Fermeture automatique après passage.
* Comptage dynamique des places disponibles.

---

## 🚦 Signalisation Intelligente

* LED Verte : places disponibles.

* LED Rouge : parking complet.

* Affichage LCD 16x2 :

  * Nombre de places libres
  * Messages système
  * Alertes

* Buzzer :

  * Confirmation d'accès
  * Signal sonore d'alerte

---

## 🌡️ Supervision Environnementale

Le système surveille également les conditions environnementales :

* Température ambiante
* Humidité relative

Les mesures sont :

* Publiées via MQTT
* Historisées dans InfluxDB
* Affichées en temps réel dans Grafana

---

## 🛡️ Edge Intrusion Detection System (Edge IDS)

Une logique de sécurité embarquée est exécutée directement sur l'ESP32.

### Détection Parking Saturé

Lorsqu'un véhicule tente d'accéder au parking alors que :

```cpp
placesLibres == 0
```

Le système :

* Refuse l'ouverture de la barrière
* Active le buzzer
* Fait clignoter la LED rouge
* Génère une alerte MQTT

---

### Détection de Passage Non Autorisé

Le système surveille :

* Les séquences de détection
* Les événements d'accès
* Les incohérences de comptage

En cas d'anomalie :

* Déclenchement d'une alarme locale
* Publication immédiate d'une alerte IDS
* Journalisation dans la base de données

---

## 📡 Communication MQTT

L'ensemble des échanges repose sur le modèle Publish/Subscribe.

Avantages :

* Faible consommation réseau
* Découplage des composants
* Communication temps réel
* Scalabilité

Broker utilisé :

```text
broker.hivemq.com
```

---

# 📊 Topics MQTT

| Catégorie          | Topic                         |
| ------------------ | ----------------------------- |
| Télémétrie Parking | `ufhb/grp_D/capteurs/parking` |
| Alertes Techniques | `ufhb/grp_D/alertes`          |
| Statut Système     | `ufhb/grp_D/statut`           |
| Événements Parking | `ufhb/grp_D/evenements`       |
| Alertes IDS        | `ufhb/grp_D/ids/alertes`      |

---

# 📈 Métriques Collectées

## Parking

* places_libres
* places_occupees
* vehicules_entres
* vehicules_sortis

## Environnement

* temperature
* humidite

## Sécurité

* intrusion_detectee
* type_intrusion
* niveau_alerte
* timestamp

---

# 🛠️ Technologies Utilisées

| Domaine          | Technologies   |
| ---------------- | -------------- |
| IoT              | ESP32          |
| Capteurs         | HC-SR04, DHT22 |
| Actionneurs      | Servomoteurs   |
| Affichage        | LCD 16x2 I2C   |
| Réseau           | MQTT           |
| Broker           | HiveMQ         |
| ETL              | Node-RED       |
| Base de données  | InfluxDB       |
| Visualisation    | Grafana        |
| Conteneurisation | Docker         |
| Orchestration    | Docker Compose |
| Simulation       | Wokwi          |
| Langage Embarqué | C++ Arduino    |
| Traitement JSON  | ArduinoJson    |

---

# 🔌 Cartographie GPIO

| Composant              | GPIO |
| ---------------------- | ---- |
| HC-SR04 Entrée Trigger | 5    |
| HC-SR04 Entrée Echo    | 18   |
| HC-SR04 Sortie Trigger | 19   |
| HC-SR04 Sortie Echo    | 23   |
| Servo Entrée           | 13   |
| Servo Sortie           | 14   |
| DHT22                  | 15   |
| Buzzer                 | 12   |
| LED Verte              | 2    |
| LED Rouge              | 4    |

---

# 📂 Structure du Projet

```text
project-root/
│
├── node_red/
│   └── flows.json
│
├── influxdb/
│   └── data/
│
├── docker-compose.yml
├── sketch.ino
├── diagram.json
└── README.md
```

---

# 🚀 Déploiement

## 1. Cloner le projet

```bash
git clone https://github.com/TankRoot29/Projet-IoT---parking-iot-service.git

cd Projet-IoT---parking-iot-service
```

---

## 2. Lancer l'infrastructure

```bash
docker compose up --build -d
```

---

## 3. Services Disponibles

| Service  | URL                   |
| -------- | --------------------- |
| Node-RED | http://localhost:1880 |
| InfluxDB | http://localhost:8086 |
| Grafana  | http://localhost:3000 |

---

# 🔀 Pipeline Node-RED

Le moteur Node-RED assure :

* Souscription MQTT
* Décodage JSON
* Validation des messages
* Séparation des flux
* Écriture dans InfluxDB

Deux pipelines principaux sont utilisés :

### Pipeline Télémétrie

```text
MQTT → JSON → InfluxDB (parking)
```

### Pipeline Sécurité

```text
MQTT IDS → JSON → InfluxDB (alertes_ids)
```

---

# 📊 Dashboard Grafana

Le tableau de bord permet de visualiser :

### Parking

* Places libres
* Places occupées
* Évolution temporelle

### Environnement

* Température
* Humidité

### Sécurité

* Nombre d'intrusions détectées
* Dernière alerte
* État du système IDS

---

# 🔍 Concepts Réseaux & Sécurité

Ce projet met en œuvre plusieurs concepts avancés :

### IoT

* Communication M2M
* Edge Computing
* Smart Monitoring

### Réseaux

* MQTT Publish/Subscribe
* Architecture distribuée
* Communication temps réel

### Sécurité

* Détection d'intrusion
* Journalisation d'événements
* Surveillance proactive

### Observabilité

* Monitoring temps réel
* Dashboards
* Historisation des données

### DevOps

* Docker
* Docker Compose
* Infrastructure as Code

---

# 🎓 Compétences Démontrées

* Internet of Things (IoT)
* Réseaux TCP/IP
* MQTT
* Edge Computing
* Détection d'intrusion
* Monitoring et observabilité
* Bases de données séries temporelles
* Node-RED
* Docker
* Architecture distribuée

---

# 🚀 Améliorations Futures

* Authentification MQTT par TLS.
* Gestion multi-parkings.
* Caméra IP avec analyse vidéo.
* Notifications Telegram/WhatsApp.
* Alertes Grafana automatiques.
* Tableau de bord Web dédié.
* Intégration d'une IA de détection d'anomalies.

---

# 📸 Démonstration

Ajouter ici :

* Capture Wokwi
* Dashboard Grafana
* Flux Node-RED
* Architecture réseau
* Détection IDS en fonctionnement

---

# 👨‍💻 Équipe Projet

### AGBENONZAN Kossivi Jacques Junior

GitHub : JunRoot29

### KONE Kpantieri

### HORO Désiré

---

## 🎓 Cadre Académique

Licence 3 Réseaux, Sécurité et Télécommunications (L3 RIST)

UFR Mathématiques et Informatique

Université Félix Houphouët-Boigny – Abidjan, Côte d'Ivoire

Sous la supervision académique de Dr. Konaté

---

## 📜 Licence

Projet académique réalisé dans le cadre du module **Création de Services Réseaux et IoT**.
