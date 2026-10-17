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
- dialoguer en K-line avec **MS41 ECU Tool v1.1.5**, intégré : identification, défauts, lecture et écriture de la flash.

## Prérequis

- **Java 8 ou plus récent** (par exemple [Eclipse Temurin](https://adoptium.net)).
- macOS, Windows ou Linux.
- Un **full read** de calculateur MS41 (fichier de 256 Ko). Aucun n'est fourni.
- Pour l'onglet Tables : une **définition RomRaider** (fichier XML) de la calibration. Aucune n'est fournie.

## Installation et lancement

1. Télécharger ce dépôt : bouton vert **Code** en haut de la page, puis **Download ZIP**.
2. Décompresser le ZIP où l'on veut (il contient un dossier `MS41-Simulator-M52--main`).
3. Dans ce dossier, double-clic sur `MS41Simulator.jar`
   (ou dans un terminal, depuis le dossier : `java -jar MS41Simulator.jar`).

Garder les fichiers ensemble : le simulateur cherche `lib/` et `docs/` à côté de `MS41Simulator.jar`.

## Premiers pas

1. *Fichier > Charger full read…* : choisir son full read (lecture brute de la puce ou vue CPU, détectée seule).
2. Facultatif : créer le dossier `docs/definitions/` à côté du jar et y placer sa définition RomRaider (chargée
   automatiquement au chargement du full read), ou *Fichier > Charger une définition RomRaider*.
3. Onglet *Moteur virtuel* : choisir le moteur et les injecteurs, puis **Démarrer**.
4. *Aide > Guide utilisateur* (F1) : le guide illustré complet s'ouvre dans le navigateur.

## Contenu du dépôt

| Élément | Rôle |
|---|---|
| `MS41Simulator.jar` | le simulateur (avec MS41 ECU Tool v1.1.5) |
| `docs/Guide_utilisateur/` | guide utilisateur (Aide > Guide) |
| `lib/` | bibliothèques pour les manettes (JNA, SDL2) et leurs licences |

Créés à l'usage à côté du jar : `docs/definitions/` (à créer soi-même, pour sa définition RomRaider),
`data/eeprom/` (EEPROM simulées enregistrées, créé automatiquement) et `ms41_ecu_tool.log` (journal de MS41 ECU Tool).

## Remarques

- Le code source n'est pas publié : seul l'exécutable est distribué.
- Les réglages (fenêtres, thème, son, manette, capteurs) sont gardés d'un lancement à l'autre.
- Limites connues : voir le chapitre *Limites actuelles* du guide.

© Diolum.fr – [diolum.fr](https://diolum.fr)
