# Robot Zumo : évitement d'obstacles et contrôle Bluetooth

Projet du module **Robotique** (Master ISOC) : un robot mobile basé sur **Arduino Uno** et **Zumo Shield**, capable de deux comportements :

- **Mode autonome** : il avance et évite les obstacles grâce à un capteur ultrason HC-SR04.
- **Mode manuel** : il se pilote à distance depuis un smartphone via un module Bluetooth HC-05.

> Les deux modes sont fournis ici sous forme de deux programmes séparés.

## Fonctionnement

### 1. Évitement d'obstacles (`evitement_obstacles`)
Le robot mesure en continu la distance devant lui :
- **Plus de 20 cm** : il avance à vitesse constante.
- **Moins de 20 cm** : il s'arrête, pivote sur place vers la gauche (400 ms), puis repart.
- Si le capteur ne reçoit pas d'écho (aucun obstacle), la distance est considérée comme très grande (999 cm).
- La distance mesurée s'affiche dans le moniteur série (9600 bauds).

### 2. Contrôle Bluetooth (`controle_bluetooth`)
Le robot reçoit des caractères envoyés par une application Bluetooth pour Arduino (liaison série avec le HC-05) :

| Caractère | Action |
|:---:|---|
| `F` | Avancer |
| `B` | Reculer |
| `L` | Tourner à gauche |
| `R` | Tourner à droite |
| `S` | Stop |

La dernière commande reçue reste active jusqu'à la suivante. Par sécurité, la commande `F` est **bloquée si un obstacle est détecté à moins de 15 cm**.

## Matériel

- Arduino Uno + Zumo Shield (châssis Zumo, double pont en H, moteurs DC)
- Capteur ultrason HC-SR04
- Module Bluetooth HC-05
- 4 piles AA
- Smartphone Android avec une application de contrôle Bluetooth pour Arduino

## Branchements

| Élément | Broche Arduino |
|---|---|
| HC-SR04 `TRIG` | A1 |
| HC-SR04 `ECHO` | A0 |
| HC-05 `TXD` | D4 (réception Arduino) |
| HC-05 `RXD` | D5 (émission Arduino) |
| HC-SR04 / HC-05 `VCC` et `GND` | 5 V et GND |

Les moteurs sont pilotés par le Zumo Shield via la bibliothèque `ZumoMotors`. Le HC-05 fonctionne en 3,3 V sur sa broche `RXD` : un petit diviseur de tension (par exemple 1 kΩ et 2 kΩ) entre D5 et `RXD` protège le module.

## Installation

1. Installer l'[Arduino IDE](https://www.arduino.cc/en/software).
2. Installer la bibliothèque **ZumoShield** de Pololu (`Croquis > Inclure une bibliothèque > Gérer les bibliothèques`, chercher *ZumoShield*). Elle contient `ZumoMotors`. `SoftwareSerial` est déjà incluse.
3. Ouvrir `evitement_obstacles/evitement_obstacles.ino` ou `controle_bluetooth/controle_bluetooth.ino`.
4. Choisir la carte **Arduino Uno** et téléverser.

## Utilisation

**Évitement d'obstacles** : poser le robot au sol et l'allumer. Il avance seul et contourne ce qu'il rencontre.

**Contrôle Bluetooth** :
1. Allumer le robot, puis appairer le smartphone avec le module **HC-05** (code PIN par défaut : `1234`).
2. Dans l'application, régler les boutons pour envoyer `F`, `B`, `L`, `R` et `S`.
3. Piloter le robot avec les boutons directionnels.

## Résultats (mesurés pendant les tests du rapport)

| Mesure | Valeur |
|---|---|
| Latence Bluetooth | 80 à 120 ms |
| Portée Bluetooth (intérieur) | jusqu'à 8 m |
| Évitement réussi | 95 % (19 essais sur 20) |
| Autonomie | environ 45 minutes |

## Structure du dépôt

```
robot-zumo/
├── evitement_obstacles/evitement_obstacles.ino
├── controle_bluetooth/controle_bluetooth.ino
├── .gitignore
└── README.md
```

## Pistes d'amélioration

- Fusionner les deux programmes avec un basculement automatique / manuel.
- Ajouter la gestion de la vitesse (commandes `0` à `9`) et du klaxon.
- Ajouter des capteurs infrarouges latéraux ou un servomoteur pour balayer le capteur ultrason.
- Remplacer les `delay()` par une gestion non bloquante.
- Créer une application mobile sur mesure qui affiche la distance en temps réel.

## Auteurs

- Kawthar Derouich
- Aymane Ait Arab

Encadrants : M. Mohammed Guerbaoui et M. Zakaria Bakziz.
