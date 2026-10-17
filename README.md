# MS41 Simulator 1.0.0

Simulateur du calculateur moteur **BMW MS41.0** (moteurs M52, processeur Siemens SAB 80C166W à 12 MHz).
Il exécute le vrai firmware lu dans un calculateur, instruction par instruction, avec les puces de la carte
(flash, RAM, décodeur d'adresse, EEPROM, contrôleur CAN, satellite), un moteur M52 virtuel branché sur ses
broches et une ligne K pour le diagnostic.

- suivre le firmware pas à pas : désassemblage, pseudocode, registres, mémoire, points d'arrêt ;
- voir en direct les cellules des tables de calibration lues par le calculateur (définition RomRaider) ;
- faire tourner un M52B20, B25 ou B28 virtuel : démarrage, ralenti, accélérations, pannes, cliquetis ;
- l'entendre (trois échappements), le passer au banc de puissance ;
- chronogrammes des broches, courbes des capteurs ;
- dialoguer en K-line avec **MS41 ECU Tool** intégré : identification, défauts, lecture et écriture de la flash.

## Prérequis

- **Java 8 ou plus récent** (par exemple [Eclipse Temurin](https://adoptium.net)).
- macOS, Windows ou Linux.
- Un **full read** de calculateur MS41 (fichier de 256 Ko). Aucun n'est fourni.

## Installation et lancement

1. Télécharger `MS41Simulator-1.0.0.zip` et le décompresser où l'on veut.
2. Lancer :
   - **macOS** : double-clic sur `Lancer MS41 Simulator.command`. La première fois, macOS peut le bloquer
     (fichier téléchargé) : clic droit > *Ouvrir*, puis *Ouvrir*.
   - **Windows** : double-clic sur `Lancer MS41 Simulator.bat`.
   - **Linux** : `./lancer.sh` dans le dossier.
   - ou double-clic sur `MS41Simulator.jar`.

## Premiers pas

1. *Fichier > Charger full read…* : choisir son full read (lecture brute de la puce ou vue CPU, détectée seule).
2. Onglet *Moteur virtuel* : choisir le moteur et les injecteurs, puis **Démarrer**.
3. *Aide > Guide utilisateur* (F1) : le guide illustré complet s'ouvre dans le navigateur.

## Contenu du ZIP

| Élément | Rôle |
|---|---|
| `MS41Simulator.jar` | le simulateur |
| `Lancer MS41 Simulator.command` / `.bat`, `lancer.sh` | lanceurs macOS, Windows, Linux |
| `docs/Guide_utilisateur/` | guide utilisateur (Aide > Guide) |
| `docs/definitions/` | définition RomRaider de la calibration ID41 (chargée automatiquement) |
| `docs/M52 physical specs/` | fiches constructeur des M52 (fenêtre Moteur) |
| `lib/` | bibliothèques pour les manettes (JNA, SDL2) et leurs licences |
| `data/` | EEPROM simulées enregistrées (créé à l'usage) |

## Remarques

- Le code source n'est pas publié : seul l'exécutable est distribué.
- Les réglages (fenêtres, thème, son, manette, capteurs) sont gardés d'un lancement à l'autre.
- Limites connues : voir le chapitre *Limites actuelles* du guide.

© Diolum.fr – [diolum.fr](https://diolum.fr)
