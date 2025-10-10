# Document de travail — Comfy'Pack modèle low cost  

---

## 1. Objectifs

- **S1** : Objectif : embarquer un microcontrôleur dans un sac à dos pour acquérir ~10 capteurs en sortie 4–20 mA, alimenté par batterie, contrôlable par bouton, Bluetooth ou USB, avec stockage local (SD) et transmission.  
- **S2** : Contraintes principales : autonomie (durée souhaitée à préciser), sécurité batterie, facilité d'extraction des données, suivi via smartphone
- **S3** : carte de dev : Raspberry Pi (prototype en Python ou C/C++).  

---

## 2. Architecture matérielle  

- **S4** : Bloc alimentation : batterie + module de gestion-charge + convertisseurs pour générer 5V/3.3V/12V.  
- **S5** : Bloc MCU : Raspberry Pi 4 + Arduino
- **S6** : Bloc acquisition : Arduino ? + ADC multicanal ou multiplexeur + amplificateur si besoin (op-amp différentiel) + filtre 100nF ???.  
- **S7** : Bloc stockage : carte SD (SPI?) + gestion fichiers (CSV).  
- **S8** : Bloc communication : USB, Bluetooth BLE, LED, bouton(s) poussoir(s), UART.  
- **S9** : Bloc protection : fusible PTC, monitoring tension batterie et diode de protection.  

---

## 3. Choix de composants  

- **S10** : MCU envisagés : Raspberry Pi 4 (Python) — consommation élevée???  

- **S11** : ADC Arduino pour acquisition analaogique

- **S12** : Filtrage : 1 kohm + 100 nF -> fc ~ 1.6 kHz ???

- **S13** : Batterie & puissance

- **S14** : Interface physique 4–20 mA :  
  Chaque capteur : entrée 2 fils (boucle de courant) avec résistance de mesure

---

## 4. Calculs & dimensionnement  

- **S15** : Résistance (R) :  
  - R = 100 ohms -> V(20 mA) = 2.0 V ; V(4 mA) = 0.4 V ; Pmax = 0.04 W.  
  - R = 150 ohms -> V(20 mA) = 3.0 V ; V(4 mA) = 0.6 V ; Pmax = 0.06 W.  
  - R = 120 ohms -> ok.  

- **S16** : Dimensionnement batterie : pile 18650 3.7 Li-Ion.  

- **S17** : Echantillonnage : ~ 1Hz.

---

## 5. Schéma fonctionnel (description textuelle)  

- **S18** : Batterie -> protection -> régulateur -> PI4.  
- **S19** : Capteurs -> R individuel -> filtre RC -> Arduino -> PI4
- **S20** : PI4 -> SPI -> lecteur SD.  
- **S21** : PI4 -> USB / BLE pour commandes et transfert.  
- **S22** : Bouton START/STOP -> PI4 entrée GPIO + interrupt.  
- **S23** : Bouton sortie veille -> PI4 entrée GPIO + interrupt.  
- **S24** : LED états -> PI4 sorties GPIO.  

---

## 6. Logiciel — architecture et machine à états  

- **S25 — Modules logiciels** :  
  - `SystemManager` : gère les autres modules (Capteurs, Buffer, StockageSD, Configuration, BluetoothManager, CommandeManager, TransmissionManager, BatteryManager). Supervise l’état global (IDLE, PRÊT, ACQUISITION, TRANSFER, ERROR).  
  - `CommandeManager` : interprète commandes BLE
  - `TransmissionManager` : envoie données du Buffer vers BluetoothManager
  - `BluetoothManager` : gère appairage, réception et envoi des données vers smartphone.
  - `Buffer` : buffer données capteurs pour stockage SD.
  - `StockageSD` : gère écriture et lecture sessions de mesure, lecture et sauvegarde des paramètres de configuration.
  - `Configuration` : contient les paramètres du système, charge et applique coefs de calibration des capteurs, sauvegarde dans SD.
  - `Capteurs` : écriture/lecture port GPIO, échange avec Arduino/ADC, calibration et filtrage selon coefs.  
  - `BatteryManager` : monitore tension batterie, déclenche alertes si seuil critique.  

- **S26 — Etats** :  
  - **IDLE** : faible consommation, bouton et connexion BLE activent le système.  
  - **PRET** : prêt à démarrer acquisition ou configuration.  
  - **CONFIG** : configuration des paramètres, coefficients capteurs, fréquence d’acquisition.  
  - **ACQUISITION** : acquisition de données, stockage dans Buffer et SD, lit tension batterie.  
  - **TRANSMISSION** : transfert des données acquises vers le smartphone via BLE.  
  - **ERREUR** : état d’erreur, watchdog et reset si nécessaire.  

- **S27 — Protocole de commande (texte simple)** :  
  - `START\n` -> démarre acquisition, création nouvelle session ?.  
  - `STOP\n` -> arrête l’acquisition en cours, session terminée.  
  - `GETDATA n` -> récupère les données de la session n.  
  - `SETFREQ f` -> règle la fréquence d’échantillonnage sur f Hz.  
  - `SETCOEF n a b c` -> applique coefs de calibration avec n num capteur  et  G = a.U² + b.U + c.  
  - `CONFIG\n` -> charge la configuration  
  - `STATUS\n` -> état du système (IDLE, PRÊT, ACQUISITION, TRANSMISSION, ERROR).  


- **S28** : Format de stockage :  
  - Fichiers CSV horodatés `NNNN_YYYYMMDD_HHMMSS.csv` : timestamp, canal1_mv, canal2_mv, ..., batterie_mv.  

---

## 7. Exemples de code à chercher  

- **S29** : Bouton & interrupt. 
- **S30** : Tâche acquisition (timer).  
- **S31** : Lecture/écriture GPIO  
- **S32** : Communication avec ADC/Arduino (UART/I2C/SPI)

---

## 8. Tests & validation  

- **S33** : Tests unitaires : conversion ADC->unités.  
- **S34** : Tests matériels : injection courant calibré 4/12/20 mA.  
- **S35** : Tests terrain : essais sac à dos en conditions réelles, autonomie, robustesse.  
- **S36** : Charge batterie, thermique  

---

## 9. Composants à acheter  

- **S37** : Raspberry Pi 4
- **S38** : Arduino/ADC
- **S39** : Carte microSD
- **S40** : Boutons, LEDs
- **S41** : Batterie + module charge USB
- **S42** : 3 Convertisseurs 3.3V/5V/12V

---

## 10. Plan

- **S43** : Architecture logicielle PI4
- **S44** : Implémenter commandes START/STOP via USB/BLE.  
- **S45** : Implémenter firmware arduino échange avec PI4
- **S46** : Implémenter firmware arduino acquisition avec PI4
- **S47** : Valider format fichiers SD.  
- **S48** : Schéma électrique + prototype breadboard.  
- **S49** : Tests autonomie, calibration.  


# Product Backlog — Comfy’Pack low-cost  
_Système embarqué d'acquisition de capteurs environnementaux piloté par un smartphone_

---

## Projet  
Système portable :  
- permet la mesure, l’enregistrement et la transmission de données issues de capteurs industriels,  
- doit fonctionner sur batterie,  
- est pilotable depuis une application mobile.

---

## Backlog  
4 **EPICs** :  

1. **EPIC 1 — Système d'acquisition (Raspberry Pi 4, stockage, acquisition)**  
2. **EPIC 2 — Application Smartphone (interface utilisateur + visu, communication)**  
3. **EPIC 3 — Électronique (schéma, PCB, alimentation, intégration capteurs)**  
4. **EPIC 4 — Mécanique (boîtier, fixation, ergonomie, dissipation)**  

---

## EPIC 1 — Système d'acquisition

### Objectif
Système d’acquisition, de stockage des données capteurs, gestion de l’énergie et communication Bluetooth.

---

### **US1 — Allumer / Éteindre le système**  
**En tant qu’utilisateur**, je veux pouvoir **allumer ou éteindre le système** pour démarrer ou arrêter son fonctionnement, afin de préserver la batterie.

- Détection de l’alimentation ou d’un bouton ON/OFF  
- Initialisation des GPIO, capteurs, Bluetooth  
- Passage en mode IDLE après démarrage  
- **Critère d’acceptation** : le système démarre sans erreur et signale son état (LED, log ou message BLE)

---

### **US2 — Sortie du mode veille**  
**En tant qu’utilisateur**, je veux **sortir le système du mode veille** en appuyant sur un bouton, afin de préparer une session d’acquisition.

- Réveil matériel du Raspberry (GPIO interrupt)  
- Réinitialisation de l’état du `SystemManager` à “PRÊT”  
- Relance du Bluetooth en mode appairage  

---

### **US3 — Appairage Bluetooth**  
**En tant que smartphone**, je veux pouvoir **m’appairer au système via BLE**, afin d’établir une connexion sécurisée pour les commandes et la transmission des données.

- `BluetoothManager` : annonce, appairage, gestion de session BLE  
- Gestion du timeout d’appairage (30 s)  
- **Critère d’acceptation** : le smartphone reçoit la confirmation d’appairage  

---

### **US4 — Configuration**  
**En tant qu’utilisateur**, je veux **configurer les capteurs et la fréquence d’acquisition** avant le démarrage, afin d’adapter le système à mes besoins de mesure.

- Paramètres configurables : fréquence, coefficients capteurs, seuils batterie  
- Commandes supportées :
  - `SETCOEF n a b c` : définit les coefficients de calibration du capteur *n*  
  - `SETFREQ f` : définit la fréquence d’échantillonnage  
  - `SETTHRESH x` : définit les seuils de batterie  
- Lecture/écriture sur carte SD via `Configuration`  
- **Critère d’acceptation** : la configuration est sauvegardée et relue correctement après redémarrage  

---

### **US5 — Acquisition de données capteurs**  
**En tant qu’utilisateur**, je veux **lancer une acquisition**, afin d’enregistrer les signaux analogiques convertis en valeurs physiques.

- Lecture continue via les objets `Capteur` (ADC interne Raspberry)  
- Application des coefficients de calibration  
- Stockage dans le `Buffer` puis sur la `carte SD`  
- Surveillance batterie pendant l’acquisition  
- Boucle d’acquisition maintenue tant que commande `START` active
- Critère d’acceptation : fichier session valide et cohérent sur la carte SD

---

### **US6 — Gestion de session**  
**En tant qu’utilisateur**, je veux pouvoir **ouvrir une nouvelle session d’acquisition** ou **relire une session existante** depuis la carte SD.

- `SystemManager.demarrerSession()`  
- `SystemManager.terminerSession()`  
- `StockageSD.lireSession(id)`  
- **Critère d’acceptation** : les sessions sont identifiables, horodatées et relisibles  

---

### **US7 — Stockage sur carte SD**  
**En tant qu’utilisateur**, je veux que les données soient **stockées sur la carte SD**, afin de pouvoir les relire ou les transférer plus tard.

- Sauvegarde périodique (si buffer plein)  
- Écriture de blocs atomiques  
- Lecture et export par session  
- **Critère d’acceptation** : aucune perte de données pendant une acquisition prolongée  

---

### **US8 — Surveillance batterie**  
**En tant qu’utilisateur**, je veux que le système **surveille le niveau de batterie** et m’alerte en cas de batterie basse ou critique.

- `BatteryManager.mesurerNiveau()`  
- Notification via LED, log ou Bluetooth (alerte)  
- Mise en veille automatique si batterie critique  
- **Critère d’acceptation** : alertes cohérentes et autonomie respectée  

---

### **US9 — Transmission des données**  
**En tant que smartphone**, je veux pouvoir **recevoir les données mesurées** par le système via Bluetooth, pour les visualiser et les stocker localement.

- `TransmissionManager` : envoi bloc ou flux  
- Commande `ENVOI`  
- Transmission session par session  
- **Critère d’acceptation** : transfert complet, vérifié par checksum ou taille  

---

### **US10 — Arrêt du système**  
**En tant qu’utilisateur**, je veux **stopper l’acquisition et éteindre le système**, pour économiser l’énergie et clore proprement les fichiers.

- Commande `STOP`  
- Fermeture de session, flush du buffer  
- Mise en mode IDLE ou extinction complète  

---

## EPIC 2 — Application Smartphone

### Objectif
Offrir une interface utilisateur simple pour le pilotage, la configuration et la récupération des données depuis le module embarqué.

---

### **US11 — Appairer avec le module**  
**En tant qu’utilisateur**, je veux pouvoir **appairer mon smartphone** avec le module Comfy’Pack pour établir la communication BLE.

- Découverte BLE et sélection du module  
- Authentification simple  
- Sauvegarde du profil d’appairage  

---

### **US12 — Configurer le module**  
**En tant qu’utilisateur**, je veux pouvoir **envoyer des paramètres de configuration** depuis mon smartphone vers le Raspberry Pi.

- Écran de configuration capteurs (coefficients, fréquence, seuils)  
- Commandes : `SETCOEF`, `SETFREQ`, `SETTHRESH`  
- Confirmation de sauvegarde reçue  

---

### **US13 — Démarrer / arrêter une acquisition**  
**En tant qu’utilisateur**, je veux **piloter à distance** le démarrage et l’arrêt des acquisitions via l’application.

- Commandes `START` et `STOP`  
- Retour d’état en temps réel  
- Affichage du statut courant du système  

---

### **US14 — Visualiser les données**  
**En tant qu’utilisateur**, je veux **télécharger et visualiser une session** de mesure pour analyser mes données.

- Commande `GETDATA n`  
- Sélection de session depuis une liste (n = id session)  
- Export CSV ou affichage graphique  

---

### **US15 — Recevoir alertes batterie**  
**En tant qu’utilisateur**, je veux **être alerté en cas de batterie faible ou critique**, pour éviter une coupure de session.

- Notification visuelle (popup)  
- Indicateur dans l’interface  
- Enregistrement des alertes dans le log local  

---

## EPIC 3 — Électronique

### Objectif  
Concevoir et valider le schéma électronique complet du Comfy’Pack incluant l’alimentation, la gestion batterie, la connectique capteurs et la communication avec le Raspberry Pi.

---

### **US16 — Conception du schéma électronique**  
**En tant qu’ingénieur**, je veux **concevoir le schéma électronique global**, afin d’assurer une interconnexion correcte entre les sous-systèmes (capteurs, batterie, communication, Raspberry).  

- Dessin du schéma sous KiCad  
- Intégration des interfaces capteurs (GPIO/ADC internes, I²C, SPI)  
- Vérification des niveaux logiques (3.3 V, 5 V)  

---

### **US17 — Gestion de l’alimentation**  
**En tant qu’ingénieur**, je veux **concevoir un circuit d’alimentation stable**, pour garantir le bon fonctionnement du Raspberry et des capteurs.

- Alimentation via batterie Li-ion + module de charge  
- Protection surtension/sous-tension  
- Convertisseurs DC-DC step-up/step-down adaptés  
- Intégration du suivi batterie (mesure tension/courant)  

---

### **US18 — Conception du PCB**  
**En tant qu’ingénieur**, je veux **concevoir et router un PCB** compact intégrant la logique d’alimentation, la connectique capteurs et les signaux du Raspberry.  

- PCB 2 couches  
- Connecteurs détachables pour capteurs et batterie  
- Test de continuité et validation DRC sous KiCad  

---

### **US19 — Intégration et tests électroniques**  
**En tant qu’ingénieur**, je veux **assembler et tester les cartes**, afin de valider la continuité, la consommation et le bon fonctionnement général.  

- Tests de tension, courant, et continuité  
- Validation communication GPIO/I²C/SPI  
- Test de consommation à vide et en charge  

---

## EPIC 4 — Mécanique

### Objectif  
Concevoir un boîtier compact et robuste intégrant les cartes électroniques, la batterie et les capteurs, adapté à une utilisation nomade.

---

### **US20 — Conception du boîtier**  
**En tant que concepteur mécanique**, je veux **concevoir le boîtier du système**, afin de protéger les composants électroniques tout en assurant une bonne ergonomie.

- DAO sous FreeCAD ou SolidWorks  
- Accès facile aux ports et boutons  
- Emplacement pour LED et connecteurs  

---

### **US21 — Fixation des capteurs et du Raspberry**  
**En tant que concepteur**, je veux **intégrer des fixations internes** pour les cartes et capteurs, afin d’assurer la stabilité mécanique et thermique.

- Supports internes vissés ou clipsés  
- Espace prévu pour câblage propre  
- Gestion thermique (ouvertures, dissipateur si nécessaire)  

---

### **US22 — Validation mécanique**  
**En tant que concepteur**, je veux **valider l’ergonomie et la robustesse** du boîtier, pour garantir la résistance mécanique en usage réel.  

- Impression 3D du prototype  
- Test d’assemblage et d’ergonomie  
- Validation du maintien et de la dissipation  

---

## Annexes  
- **Docs :** Diagrammes UML
- **Phase 1 :** Développement sur Raspberry Pi 4  
- **Phase 2 :** Développement sur Android  
- **Phase 3 :** Etude électronique
- **Phase 4 :** Etude mécanique  
- **Livrables intermédiaires :**
  - Code source embarqué (phase 1)  
  - Application mobile BLE (phase 2)  
  - Schéma électronique, PCB, et dossier mécanique  
  - Documentation système et tests unitaires  

# UML diagrams

````plantuml
'
'Diagrammes pour le projet Comfy'Pack - Raspberry Pi 4
'

'  ██████╗ ██████╗ ███╗   ██╗████████╗███████╗██╗  ██╗████████╗███████╗
' ██╔════╝██╔═══██╗████╗  ██║╚══██╔══╝██╔════╝╚██╗██╔╝╚══██╔══╝██╔════╝
' ██║     ██║   ██║██╔██╗ ██║   ██║   █████╗   ╚███╔╝    ██║   █████╗  
' ██║     ██║   ██║██║╚██╗██║   ██║   ██╔══╝   ██╔██╗    ██║   ██╔══╝  
' ╚██████╗╚██████╔╝██║ ╚████║   ██║   ███████╗██╔╝ ██╗   ██║   ███████╗
'  ╚═════╝ ╚═════╝ ╚═╝  ╚═══╝   ╚═╝   ╚══════╝╚═╝  ╚═╝   ╚═╝   ╚══════╝
                                                                     
@startuml 0_Context_dagram
actor Utilisateur
actor Smartphone

node "Comfy'Pack" {
    component "Raspberry Pi 4" as Pi
    component "Capteurs" as Sensors
    component "Batterie/Alim." as Power
}

Utilisateur --> Pi : Interaction/Commandes
Smartphone --> Pi : BLE Commandes / Apparaiement
Smartphone <-- Pi : Transfert données
Pi --> Sensors : Lit données
Pi --> Power : Monitoring
@enduml
````
````plantuml
' ██████╗ ███████╗██████╗ ██╗      ██████╗ ██╗███████╗███╗   ███╗███████╗███╗   ██╗████████╗
' ██╔══██╗██╔════╝██╔══██╗██║     ██╔═══██╗██║██╔════╝████╗ ████║██╔════╝████╗  ██║╚══██╔══╝
' ██║  ██║█████╗  ██████╔╝██║     ██║   ██║██║█████╗  ██╔████╔██║█████╗  ██╔██╗ ██║   ██║   
' ██║  ██║██╔══╝  ██╔═══╝ ██║     ██║   ██║██║██╔══╝  ██║╚██╔╝██║██╔══╝  ██║╚██╗██║   ██║   
' ██████╔╝███████╗██║     ███████╗╚██████╔╝██║███████╗██║ ╚═╝ ██║███████╗██║ ╚████║   ██║   
' ╚═════╝ ╚══════╝╚═╝     ╚══════╝ ╚═════╝ ╚═╝╚══════╝╚═╝     ╚═╝╚══════╝╚═╝  ╚═══╝   ╚═╝   
                                                                                          
 @startuml 1_Deployment_diagram
node "Sac à dos" {
  node "Raspberry Pi 4" {
    component (Programme MCU) as C_MCU
    component (Module Bluetooth) as C_BLE
    component (Capteurs)  as C_SENS
    component (Stockage SD) as C_SD
    component (Moniteur batterie) as C_BM
  }
  artifact (Batterie Li-Ion) as B_Liion
}

node "Smartphone" {
  component (App Compagnon) as C_App
}

C_MCU --> C_BLE : Gestion BLE
C_MCU --> C_SENS : Acquisition (n voies)
C_MCU --> C_SD : Session & configuration
C_MCU --> C_BM : Monitoring
C_BM --> B_Liion  : Mesure niveau
C_BLE--> C_App :  Commandes, Envoi/réception de données
@enduml

````
````plantuml

' ██╗   ██╗███████╗███████╗ ██████╗ █████╗ ███████╗███████╗
' ██║   ██║██╔════╝██╔════╝██╔════╝██╔══██╗██╔════╝██╔════╝
' ██║   ██║███████╗█████╗  ██║     ███████║███████╗█████╗  
' ██║   ██║╚════██║██╔══╝  ██║     ██╔══██║╚════██║██╔══╝  
' ╚██████╔╝███████║███████╗╚██████╗██║  ██║███████║███████╗
'  ╚═════╝ ╚══════╝╚══════╝ ╚═════╝╚═╝  ╚═╝╚══════╝╚══════╝
                                                         
@startuml 2_UseCase_diagram
actor Utilisateur
actor Smartphone

rectangle "Raspberry Pi 4" {
    usecase (Allumer/Éteindre le système) as UC_AllumerEteindre
    usecase (Appuyer sur bouton sortie mode veille) as UC_SortieVeille
    usecase (Appairer Bluetooth) as UC_Bluetooth
    usecase (Gérer Commandes) as UC_Commandes
    usecase (Acquérir données capteurs) as UC_Acquerir
    usecase (Envoyer données vers Smartphone) as UC_Envoyer
    usecase (Stockage sur SD) as UC_Stockage
    usecase (Configurer paramètres) as UC_Configurer
    usecase (Surveiller état batterie) as UC_Monitoring
}

Utilisateur --> UC_AllumerEteindre
Utilisateur --> UC_SortieVeille
Smartphone --> UC_Bluetooth
Smartphone --> UC_Commandes

UC_Commandes --> UC_Acquerir : <<include>>
UC_Commandes --> UC_Configurer : <<include>>
UC_Commandes --> UC_Envoyer : <<include>>

UC_Acquerir --> UC_Stockage : <<include>>
UC_Configurer --> UC_Stockage : <<include>>

Smartphone <-- UC_Envoyer
UC_Monitoring --> Smartphone : Alertes/Batterie
UC_Monitoring --> Utilisateur : Alertes/Batterie

UC_Commandes ..> UC_Monitoring : <<extend>> : Bloquer/Modifier commandes si batterie critique

note right of UC_Configurer
  La configuration doit être effectuée
  avant le démarrage d'une acquisition
end note
@enduml

````
````plantuml

'  █████╗  ██████╗████████╗██╗██╗   ██╗██╗████████╗███████╗███████╗
' ██╔══██╗██╔════╝╚══██╔══╝██║██║   ██║██║╚══██╔══╝██╔════╝██╔════╝
' ███████║██║        ██║   ██║██║   ██║██║   ██║   █████╗  ███████╗
' ██╔══██║██║        ██║   ██║╚██╗ ██╔╝██║   ██║   ██╔══╝  ╚════██║
' ██║  ██║╚██████╗   ██║   ██║ ╚████╔╝ ██║   ██║   ███████╗███████║
' ╚═╝  ╚═╝ ╚═════╝   ╚═╝   ╚═╝  ╚═══╝  ╚═╝   ╚═╝   ╚══════╝╚══════╝
                                                                 
@startuml 3_Activity_diagram
start
:Allumage du Raspberry Pi;
:Initialisation GPIO / Capteurs / Bluetooth;
:État = IDLE (veille basse consommation);

partition InputUtilisateur {
    :Attente appui bouton pour sortie veille;
}

partition Bluetooth {
    :Annonce / Mode Appairage;
    :Attente d'appairage avec Smartphone;
    if (Appairage réussi ?) then (oui)
        :État = PRÊT;
    else (non après 30s)
        :Retour à l'état IDLE;
        stop
    endif
}

fork
  partition "Thread Acquisition" {
    repeat
        :Lire tous les capteurs (n voies);
        :Filtrer et convertir en unités physiques;
        :Empiler données dans le buffer;
        if (Buffer plein ?) then (oui)
          :Écriture sur carte SD;
        endif
        :Vérifier seuils et alarmes;
    repeat while (État == ACQUISITION)
    :Fin d'acquisition;
  }
fork again
  partition "Thread Commandes" {
    repeat
        :Écouter commandes BLE/USB;
        if (Commande START ?) then (oui)
            :Trigger acquisition;
            :État = ACQUISITION;
        elseif (Commande STOP ?) then (oui)
            :Arrêter acquisition;
            :État = PRÊT;
        elseif (Commande ENVOI ?) then (oui)
            :Trigger transmission;
            :État = TRANSMISSION (si pas en mode ACQUISITION);
        elseif (Commande CONFIG ?) then (oui)
            :Trigger config;
        endif
    repeat while (État != IDLE)
  }
fork again
  partition "Thread Transmission" {
    while (Transmission demandée ?)
        if (Acquisition terminée?) then (oui)
            :Envoyer données en bloc au Smartphone;
            :Confirmer transmission;
        else (non)
            :Envoyer qq données (config);
        endif
    endwhile
  }
fork again
  partition "Thread Batterie" {
    repeat
        :Lire niveau batterie;
        if (Batterie critique ?) then (oui)
            :Arrêter acquisition si nécessaire;
            :Notifier Utilisateur/Smartphone;
        elseif (Batterie basse ?) then (oui)
            :Notifier utilisateur/Smartphone;
        endif
    repeat while (Système actif)
  }
end fork

:Retour à l'état IDLE;
stop
@enduml
 
````
````plantuml

'  ██████╗██╗      █████╗ ███████╗███████╗███████╗███████╗
' ██╔════╝██║     ██╔══██╗██╔════╝██╔════╝██╔════╝██╔════╝
' ██║     ██║     ███████║███████╗███████╗█████╗  ███████╗
' ██║     ██║     ██╔══██║╚════██║╚════██║██╔══╝  ╚════██║
' ╚██████╗███████╗██║  ██║███████║███████║███████╗███████║
'  ╚═════╝╚══════╝╚═╝  ╚═╝╚══════╝╚══════╝╚══════╝╚══════╝
                                                        
@startuml 4_Class_diagram

class SystemManager {
  - etat : string
  - ble : BluetoothManager
  - cmdMgr : CommandeManager
  - txMgr : TransmissionManager
  - storage : StockageSD
  - buffer : Buffer
  - config : Configuration
  - capteurs : list<Capteur>
  - battery : BatteryManager
  + start() : void
  + stop() : void
  + idle() : void
  + triggerAcquisition() : void
  + triggerTransmission() : void
  + appliquerConfig() : void
  + demarrerSession() : void
  + terminerSession() : void
  + chargerSession(id : string) : Session
}

class Capteur {
  - coefficients[]
  + appliquerCalibration(coefficients[]) : void
  + lireDonnee() : Data
  + filtrerConvertir(d : Data) : Data
}

class CapteurGPS
class CapteurAcc
class CapteurGyro
class CapteurTemp
class CapteurPression

class Buffer {
  - data[]
  + push(d : Data) : void
  + isFull() : bool
  + flushToSD(sd : StockageSD) : bool
}

class StockageSD {
  + ecrire(d : Data) : bool
  + lireData() : Data
  + ecrireSession(s : Session) : bool
  + lireSession(id : string) : Session
  + lireConfig() : ConfigParams
  + ecrireConfig(c : ConfigParams) : bool
}

class Session {
  - id : string
  - dateDebut : datetime
  - dateFin : datetime
  - donnees : list<Data>
}

class BluetoothManager {
  + appairer() : bool
  + envoyer(d : Data) : bool
  + recevoir() : Data
}

class CommandeManager {
  + traiterCommande(cmd : Commande) : Etat
}

class TransmissionManager {
  + envoyerSession(d : Data, ble : BluetoothManager) : bool
  + envoyerBlock(d : Data, ble : BluetoothManager) : bool
  + confirmerTransmission() : void
}

class Configuration {
  - params[]
  + appliquer() : void
  + charger(sd : StockageSD) : bool
  + sauvegarder(sd : StockageSD) : bool
}

class BatteryManager {
  - niveau : float
  - seuilAlerte : float
  + mesurerNiveau() : float
  + alerter() : void
}

' ===== HÉRITAGES =====
Capteur <|-- CapteurGPS
Capteur <|-- CapteurAcc
Capteur <|-- CapteurGyro
Capteur <|-- CapteurTemp
Capteur <|-- CapteurPression

Session *-- Data

' ===== RELATIONS PRINCIPALES =====
SystemManager *-- Configuration
SystemManager *-- Buffer
SystemManager *-- CommandeManager
SystemManager *-- TransmissionManager
SystemManager *-- BluetoothManager
SystemManager *-- StockageSD
SystemManager *-- Capteur
SystemManager *-- BatteryManager

' Relations d’utilisation
Buffer --> StockageSD : flushToSD() <<use>>
TransmissionManager --> BluetoothManager : <<use>>
Configuration --> StockageSD : charger()/sauvegarder() <<use>>
SystemManager --> StockageSD : ecrireSession()/lireSession() <<use>>

@enduml

````
````plantuml

' ███████╗███████╗ ██████╗ ██╗   ██╗███████╗███╗   ██╗████████╗██╗███████╗██╗     
' ██╔════╝██╔════╝██╔═══██╗██║   ██║██╔════╝████╗  ██║╚══██╔══╝██║██╔════╝██║     
' ███████╗█████╗  ██║   ██║██║   ██║█████╗  ██╔██╗ ██║   ██║   ██║█████╗  ██║     
' ╚════██║██╔══╝  ██║▄▄ ██║██║   ██║██╔══╝  ██║╚██╗██║   ██║   ██║██╔══╝  ██║     
' ███████║███████╗╚██████╔╝╚██████╔╝███████╗██║ ╚████║   ██║   ██║███████╗███████╗
' ╚══════╝╚══════╝ ╚══▀▀═╝  ╚═════╝ ╚══════╝╚═╝  ╚═══╝   ╚═╝   ╚═╝╚══════╝╚══════╝
                                                                                
@startuml 5_Sequence_diagram
actor Utilisateur
actor Smartphone

participant "SystemManager" as SM
participant "CommandeManager" as CMD
participant "TransmissionManager" as TX
participant "BluetoothManager" as BLE
participant "Buffer" as BUF
participant "StockageSD" as SD
participant "Configuration" as CFG
participant "BatteryManager" as BAT
participant "Capteurs" as SENS

note right of SM
  SystemManager reste actif tout au long de la session,
  de la sortie du mode veille jusqu'au retour à IDLE
end note

== Allumage et configuration ==
Utilisateur -> SM : allumer()
activate SM
SM -> CFG : charger(SD)
activate CFG
CFG -> SD : lireConfig()
'deactivate CFG
activate SD
SD --> CFG : config
deactivate SD
deactivate CFG
SM -> BAT : mesurerNiveau()
SM -> SM : état = IDLE
deactivate SM

== Sortie de veille ==
Utilisateur -> SM : bouton sortie veille
activate SM
SM -> BLE : annonce/appairage
activate BLE
BLE -> Smartphone : pairing
alt Appairage réussi
    SM -> SM : état = PRÊT
else Appairage échoué (30s)
    SM -> SM : état = IDLE
end
deactivate BLE

== Acquisition ==
Smartphone -> BLE : commande START
activate BLE
BLE -> CMD : traiterCommande(START)
activate CMD
CMD -> SM : triggerAcquisition()
deactivate CMD
deactivate BLE

loop Acquisition continue (tant que START actif)
    SM -> SENS : lireDonnee()
    activate SENS
    SENS -> BUF : push(data)
    deactivate SENS
    activate BUF
    opt Buffer plein
        BUF -> SD : flushToSD()
        activate SD
        SD -> SD : ecrire(data)  
        SD --> BUF : ack
        deactivate SD
    end
    deactivate BUF
    SM -> BAT : mesurerNiveau()
end

== Transmission ==
Smartphone -> BLE : commande ENVOI
activate BLE
BLE -> CMD : traiterCommande(ENVOI)
activate CMD
CMD -> SM : triggerTransmission()
deactivate CMD

SM -> TX : envoyerBloc()/envoyerFlux()
activate TX
TX -> BLE : envoyer(data)
deactivate TX
BLE -> Smartphone : réception données
deactivate BLE

== Stop ==
Smartphone -> BLE : commande STOP
activate BLE
BLE -> CMD : traiterCommande(STOP)
activate CMD
CMD -> SM : stop()
SM -> SM : état = PRÊT ou IDLE
deactivate CMD
deactivate BLE
deactivate SM

@enduml
````
