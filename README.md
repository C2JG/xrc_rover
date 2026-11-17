# XRC Rover – Logiciel de contrôle ROS 2

Stack logicielle d'un **rover à 6 roues avec bras robotisé**, développée en équipe dans le cadre d'**EPFL Xplore**. Le projet tourne sous **ROS 2 Humble** dans Docker. Il couvre la chaîne complète : la manette, la machine à états, la cinématique, la communication série avec les microcontrôleurs, la vision et la localisation.

 # Mon rôle :
   - integration de la manette avec le repo ui
   - cinematique des roues et cinematique inverse du bras
   - Données capteurs : publication des mesures des capteurs ultrasons et du gyroscope sur des topics ROS 2, pour que l’interface ou n’importe quel autre module    puisse s’y abonner et les utiliser.
   - pris part à la conception du fichier rover.urdf.xacro, qui décrit la structure du rover (châssis, roues, bras, capteurs) et les liaisons entre ses pièces, pour la visualisation et la navigation.

## Équipe

- [@AquIce](https://github.com/AquIce)
- [@C2JG](https://github.com/C2JG)

## Fonctionnalités

- **Pilotage manuel à la manette** : les commandes de la manette sont converties en ordres pour les roues et pour le bras.
- **Machine à états (FSM)** avec les états `BOOTING`, `MANUAL`, `AUTOMATIC` et `FAIL`, plus un arrêt d'urgence.
- **Cinématique des roues** : direction de type Ackermann sur 6 roues (vitesse de chaque roue et angle des 4 roues directrices).
- **Bras robotisé à 2 segments** : cinématique inverse, rotation de la base et commande de la pince.
- **Communication série en C++** avec les cartes électroniques : moteurs DC, servomoteurs et capteurs.
- **Vision** :
  - flux vidéo caméra en GStreamer/H.264 (mode navigation ou autonomie) ;
  - estimation de profondeur avec **MiDaS** et détection d'objets avec **YOLOv8**, pour générer un nuage de points ;
  - détection de **marqueurs ArUco** pour se repérer.
- **Localisation** : odométrie des roues, puis un **filtre de Kalman étendu (EKF)** qui fusionne roues, IMU et vision.
- **Sécurité** : alerte d'obstacle à partir de capteurs ultrasons.
- **Navigation** : modèle URDF du rover, fichiers de lancement et configuration **Nav2**.

## Architecture

```
Manette ──► gamepad_publisher ──► fsm_node ──► wheels_manager ──► serial_link (C++) ──► moteurs / servos
                         └──────► arm_manager ──────────────────────┘
Caméra ──► camera_publisher ──► vision / aruco_detector ──┐
Roues ──► odometry_manager ───────────────────────────────┼──► ekf_node ──► /odom + TF
IMU ──────────────────────────────────────────────────────┘
Ultrasons ──► ultrasonic_node ──► alerte FSM
```

## Structure du dépôt

| Dossier | Contenu |
|---|---|
| `rover_commands/` | Package ROS 2 principal en Python : nœuds, cinématique, EKF, URDF, launch files, config Nav2 |
| `serial_link/` | Nœuds C++ qui communiquent en série avec les microcontrôleurs (DC, servos, capteurs) |
| `custom_msg/` | Messages et services ROS personnalisés (`MoveData`, `DCSpeed`, `MPUData`, `GamepadData`…) |
| `docker_humble_desktop/` | Image Docker ROS 2 Humble et scripts de build et de lancement (Linux, Mac, Windows) |
| `docs/` | Guides d'installation et commandes utiles |

## Technologies

ROS 2 Humble · Python · C++ · Docker · OpenCV · PyTorch (MiDaS, YOLOv8) · GStreamer · Nav2 · URDF/Xacro

