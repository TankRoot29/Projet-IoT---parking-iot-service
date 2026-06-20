```markdown
# IoT Parking Monitoring System

Une solution IoT complète de bout en bout pour la gestion et la supervision en temps réel d'un parking connecté. Ce projet simule un système de barrière automatisé via un microcontrôleur **ESP32**, achemine les métriques via le protocole **MQTT**, et centralise la supervision sur un dashboard dynamique **Prometheus / Grafana** grâce à une architecture conteneurisée avec **Docker**.

## 🚀 Architecture Globale du Système

Le projet repose sur une architecture événementielle distribuée, découplée et hautement portable :

```text
[ Capteurs HC-SR04 ] ──> (ESP32 / Wokwi) 
                               │
                       (Protocole MQTT)
                               ▼
                     [ Broker Public HiveMQ ]
                               │
                       (Internet / Cloud)
                               ▼
                    [ Traducteur Python ]  <── Docker Compose
                               │
                       (Metrics HTTP :8000)
                               ▼
                    [ Base Prometheus ]    <── Docker Compose
                               │
                       (Requêtes PromQL)
                               ▼
                     [ Dashboard Grafana ]  <── Docker Compose

```

---

## 🛠️ Technologies & Outils Utilisés

* **Matériel Embarqué (Simulation) :** ESP32, Capteurs Ultrasons HC-SR04 (Entrée/Sortie), Servomoteur (Barrière), Buzzer Piezo, LED d'état.
* **Environnement de Développement :** [Wokwi](https://wokwi.com) & Arduino IDE (C++).
* **Protocole IoT :** MQTT (Message Queuing Telemetry Transport) via le Broker public `HiveMQ`.
* **Backend & Pipeline de Données :** Python (Scripts d'écoute MQTT et exposition Prometheus).
* **Supervision & Observabilité :** Prometheus (Base de données séries temporelles) & Grafana (Visualisation).
* **Déploiement :** Docker & Docker Compose (Conteneurisation complète de l'infrastructure).

---

## 📌 Fonctionnalités Principales

* **Automatisation Locale :** Détection des véhicules à l'entrée et à la sortie (seuil réglé à `150 cm`), ouverture synchronisée de la barrière (servomoteur à `90°`), signalisation visuelle (LED Rouge/Verte) et sonore (Buzzer).
* **Capacité Adaptative :** Gestion dynamique d'un parc de `10 places` maximum avec blocage automatique à l'entrée en cas de saturation.
* **Routage Cloud Découplé :** Publication instantanée des variations du nombre de places sur un Broker MQTT distant, éliminant les contraintes de pare-feu locaux.
* **Supervision Temps Réel :** Collecte des métriques par Prometheus et affichage dynamique sur une jauge Grafana interactive avec seuils de couleur d'alerte.

---

## 📂 Structure du Répertoire

```text
├── mqtt_exporter/
│   ├── exporter.py       # Script Python (Pont entre MQTT et Prometheus)
│   └── requirements.txt  # Dépendances Python (paho-mqtt, prometheus-client)
├── prometheus/
│   └── prometheus.yml    # Configuration du scraping des métriques
├── docker-compose.yml    # Orchestration des conteneurs (Python, Prometheus, Grafana)
├── sketch.ino            # Code source C++ pour l'ESP32
└── README.md             # Documentation du projet

```

---

## ⚙️ Instructions d'Installation et Déploiement

### 1. Déploiement de l'Infrastructure Serveur (PC)

Assurez-vous d'avoir [Docker](https://www.docker.com/) installé sur votre machine.

1. Clonez ce dépôt sur votre machine locale :

```bash
   git clone [https://github.com/TankRoot29/Projet-IoT---parking-iot-service.git](https://github.com/TankRoot29/Projet-IoT---parking-iot-service.git)
   cd Projet-IoT---parking-iot-service

```

2. Lancez l'infrastructure en construisant les conteneurs :

```bash
   docker compose up --build

```

Les services suivants seront alors initialisés :

* **Script Traducteur Python :** Écoute le trafic MQTT et l'expose sur le port `8000`.
* **Prometheus :** Collecte les données et reste accessible sur `http://localhost:9090`.
* **Grafana :** Interface de supervision accessible sur `http://localhost:3000`.

### 2. Configuration du Microcontrôleur (Wokwi / Physique)

1. Ouvrez votre simulateur Wokwi et chargez le fichier `sketch.ino`.
2. Installez la bibliothèque **PubSubClient** via le gestionnaire de bibliothèques (Library Manager).
3. Connectez vos composants selon les assignations GPIO définies dans le code :
* **HC-SR04 Entrée :** Trig `5` / Echo `18`
* **HC-SR04 Sortie :** Trig `19` / Echo `23`
* **Servomoteur (Barrière) :** PWM `13`
* **Buzzer :** `12`
* **LEDs :** Verte `2` / Rouge `4`


4. Lancez la simulation. L'ESP32 se connectera au point d'accès virtuel `Wokwi-GUEST` puis au Broker HiveMQ.

### 3. Configuration finale de la Supervision (Grafana)

1. Accédez à `http://localhost:3000` (Identifiants par défaut : `admin` / `admin`).
2. Ajoutez une nouvelle **Data Source** de type **Prometheus** avec l'URL suivante : `http://prometheus:9090`. Cliquez sur *Save & Test*.
3. Créez un nouveau **Dashboard**, ajoutez un **Panel**, puis sélectionnez la métrique `parking_places_libres`.
4. Optez pour la visualisation de type **Gauge**, réglez la valeur maximale à `10` et définissez vos seuils de couleur.

---

## 🔍 Intérêt Réseau & Robustesse Architecture

Ce projet met en avant des concepts clés d'ingénierie des réseaux informatiques :

* **Légèreté applicative :** L'usage de MQTT permet de préserver la bande passante et les ressources CPU limitées de l'ESP32 par rapport à des requêtes HTTP/SQL classiques.
* **Sécurité & Découplage :** L'intégration d'un Broker public tiers fait office de zone tampon (Proxy/Relais), évitant d'exposer l'adresse IP privée de l'infrastructure de stockage à l'appareil IoT de bordure (Edge).
* **Conteneurisation & Portabilité :** L'intégralité du backend est isolée dans un réseau virtuel Docker interne, rendant la solution prête à être déployée à l'identique sur un serveur sur site ou sur un cloud privé (AWS, Azure, etc.).

```
```