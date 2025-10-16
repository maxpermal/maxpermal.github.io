# Document de travail — Comfy'Pack modèle low cost

---

## 1. Objectifs

- **S01** : Concevoir un système embarqué dans un sac à dos permettant d’acquérir des capteurs analogiques, alimenté par batterie, contrôlable par bouton, Bluetooth ou USB, avec stockage local (SD) et transmission vers smartphone.
- **S02** : Facilité d’extraction des données, suivi via application mobile.
- **S03** : Utiliser Raspberry Pi 4 pour le contrôle et l’enregistrement, Arduino pour l’acquisition analogique.

---

## 2. Architecture matérielle

- **S04** : Bloc alimentation : batterie Li-Ion + module de charge + convertisseurs DC/DC 5V/3.3V/12V.
- **S05** : Bloc MCU : Raspberry Pi 4 (comme unité centrale) + Arduino (comme ADC externe).
- **S06** : Connecter les capteurs analogiques à l’Arduino, utilisation éventuelle de résistances de mesure et filtres RC.
- **S07** : Bloc stockage : carte SD + gestion de fichiers (CSV).
- **S08** : Bloc communication : USB, Bluetooth BLE, LEDs et boutons.
- **S09** : Bloc protection : fusible PTC + diodes.

---

## 3. Choix de composants

- **S10** : Raspberry Pi 4 + Arduino Uno/Nano pour acquisition analogique (ADC 10 bits).
- **S11** : Batteries 18650 - Li-Ion.
- **S12** : TP4056 — chargeur de batterie USB.
- **S13** : MT3608 — convertisseurs DC/DC 2–24V/3–28V, 2A.
- **S14** : Filtrage RC (1 kΩ + 100 nF, fc ≈ 1.6 kHz) sur les entrées.<br>
            R = 220 Ω → 4 mA = 0.88 V ; 20 mA = 4.4 V ; P = 0.088 W.

---

## 4. Schéma fonctionnel

- **S15** : Batterie → protection → régulateur → Raspberry Pi.
- **S16** : Capteurs → résistance de mesure → filtre RC → Arduino → UART → Raspberry Pi.
- **S17** : Raspberry Pi → USB/BLE pour transfert de données et commandes.
- **S18** : Bouton START/STOP → GPIO Raspberry Pi.
- **S19** : LED d’état système → GPIO Raspberry Pi.

---

## 5. Logiciel — architecture et machine à états

- **S20** — Modules logiciels :
  - `SystemManager` : gérer les autres modules (Capteurs, Buffer, StockageSD, Configuration, BluetoothManager, CommandeManager, TransmissionManager, BatteryManager). Superviser l’état global (`IDLE`, `PRET`, `CONFIG`, `ACQUISITION`, `TRANSMISSION`, `ERREUR`).
  - `CommandeManager` : interpréter commandes USB/BLE.
  - `TransmissionManager` : envoyer données du Buffer vers BluetoothManager.
  - `BluetoothManager` : gérer appairage BLE et transfert.
  - `Buffer` : stocker données en mémoire avant écriture SD.
  - `StockageSD` : gérer fichiers de mesure (sessions) et fichier de configuration.
  - `Configuration` : gérer paramètres système et calibration capteurs.
  - `Capteurs` : lire données Arduino via UART, appliquer calibration.
  - `BatteryManager` : surveiller tension batterie et alerter en cas critique.

- **S21** — États système :
  - `IDLE` : veille, attente activation bouton ou Bluetooth.
  - `PRET` : prêt pour acquisition, communication ou configuration.
  - `CONFIG` : configurer capteurs, fréquence et coefficients.
  - `ACQUISITION` : acquérir et enregistrer données, lire tension batterie.
  - `TRANSMISSION` : transférer données vers smartphone.
  - `ERREUR` : gérer erreurs et relancer système si nécessaire.

- **S22** — Protocole de commande :
  - `CONFIG\n\r` : charger configuration.
  - `START\n\r` : démarrer acquisition et créer nouvelle session.
  - `STOP\n\r` : arrêter acquisition en cours et terminer session.
  - `GETDATA -1\n\r` : retourner les infos batterie.
  - `GETDATA 0\n\r` : retourner les données de la dernière acquisition.
  - `GETDATA n\n\r` : retourner les données de la session n (n>0).
  - `STATUS\n\r` : renvoyer état système (`IDLE`, `PRET`, `CONFIG`, `ACQUISITION`, `TRANSMISSION`, `ERREUR`).
  - `SETFREQ f\n\r` : définir fréquence d’acquisition.
  - `SETCOEF n a b c\n\r` : appliquer coefficients de calibration (G = a·U² + b·U + c).
  - `SETTHRESH x` : définit les seuils de batterie.

- **S23** — Format de stockage :
  - Fichiers CSV horodatés `NNNN_YYYYMMDD_HHMMSS.csv` : timestamp, canal1_mv, canal2_mv, ..., batterie_mv.

---

## 6. Tests & validation

- **S24** : Vérifier conversion ADC → tension.
- **S25** : Injecter courant 4–20 mA avec générateur étalon.
- **S26** : Vérifier acquisition terrain et autonomie batterie.
- **S27** : Tester charge et température batterie.

---

## 7. Composants à acheter

- **S28** : Raspberry Pi 4.
- **S29** : Arduino Nano ou Uno.
- **S30** : Carte microSD.
- **S31** : 1× Bouton poussoir.
- **S32** : 4× LEDs (états système et charge).
- **S33** : 1× Interrupteur ON/OFF.
- **S34** : 3× Batteries 18650.
- **S35** : 2× Modules charge TP4056.
- **S36** : 3× Convertisseurs DC/DC MT3608 (3.3V, 5V, 12V).
- **S37** : 3× Boîtiers pour batteries 18650.

---

## 8. Plan de travail — logiciel, électronique, mécanique, application mobile

### 8.1 Logiciel PI4
- **S38** : Dev machine à états système.
- **S39** : Dev protocole commande BLE/USB.
- **S40** : Dev échange série Arduino → Pi.
- **S41** : Dev enregistrement CSV.

### 8.2 Électronique
- **S42** : Réaliser schéma d’alimentation complet.
- **S43** : Réaliser schéma acquisition capteurs 4–20 mA.
- **S44** : Réaliser schéma bouton/interrupteur/LEDs.
- **S45** : Réaliser prototype sur support.
- **S46** : Mesurer autonomie batterie et consommation.

### 8.3 Mécanique
- **S47** : Concevoir support interne pour cartes et batteries (référence modèle performant).
- **S48** : Intégrer capteurs dans le sac.

### 8.4 Application mobile
- **S49** : Dev architecture logiciel
- **S50** : Dev IHM
- **S51** : Dev protocole authentification/commande/pilotage BLE
- **S52** : Dev protocole de commande PI4, `CONFIG`, `START`, `STOP`, `GETDATA`, `STATUS`, `SETFREQ`, `SETCOEF`, `SETTHRESH`
- **S53** : Dev visu données

---

## UML

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

@startuml 0_Context_diagram
!define RECTANGLE class

' Définition des acteurs
actor Utilisateur

' Définition des systèmes
rectangle "Application mobile:\n- IHM\n- Communication BLE avec PI4\n" as App
rectangle "Système embarqué:\n- PI4/Arduino\n- Liaison BLE\n- Batterie + conv DC/DC\n- Capteurs T°, H°, ...\n" as Module

' Connexions entre acteurs et systèmes
Utilisateur --> App : Paramétrage / Visualisation / Alertes
App --> Module : Commandes / Requête de données (BLE)
Module --> App : Mesures capteurs (T°, H°, ...)

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
  component (Application mobile) as C_App
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
    :État = PRET;
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
      :Vérifier batterie;
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
        :État = PRET;
      elseif (Commande GETDATA ?) then (oui)
        :Trigger transmission;
        :État = TRANSMISSION (si pas en mode ACQUISITION);
      elseif (Commande CONFIG ?) then (oui)
        :Trigger config;
      endif
    repeat while (État != IDLE)
  }
fork again
  partition "Thread Transmission" {
    repeat
      if (Transmission demandée ?) then (oui)
        if (Acquisition terminée?) then (oui)
          :Envoyer données en bloc au Smartphone;
          :Confirmer transmission;
        else (non)
          :Envoyer qq données (config);
        endif
      endif
    repeat while (État != IDLE)
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
    repeat while (État != IDLE)
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

class CapteurHum
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
Capteur <|-- CapteurHum
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
  SM -> SM : état = PRET
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
Smartphone -> BLE : commande GETDATA
activate BLE
BLE -> CMD : traiterCommande(GETDATA)
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
SM -> SM : état = PRET ou IDLE
deactivate CMD
deactivate BLE
deactivate SM

@enduml
````
