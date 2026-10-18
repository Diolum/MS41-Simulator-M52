---
name: ms41-simulator
description: Piloter et analyser le simulateur du calculateur moteur BMW MS41 (MS41 Simulator) par son serveur MCP intégré : état du firmware et du moteur virtuel, adresses en cours, mémoire, tables de calibration, courbes, pannes simulées, banc de puissance, modification et enregistrement du binaire. À utiliser dès qu'on parle du simulateur MS41, de son moteur virtuel M52/S52 ou de ses outils MCP (sim_status, engine_state, set_control...).
---

# MS41 Simulator : piloter le simulateur par MCP

MS41 Simulator est une **émulation matérielle** du calculateur moteur BMW MS41 (M52 / S52) : processeur Siemens C166
à 12 MHz, flash, RAM, décodeur d'adresse PLCC, EEPROM, CAN, satellite, et un moteur virtuel branché sur les broches.
Il exécute le **vrai binaire** chargé par l'utilisateur (full read de son calculateur). Aucun code ni document BMW
n'est fourni : les labels et la définition RomRaider, s'il y en a, viennent aussi de l'utilisateur.

Le serveur MCP est **expérimental**. Il tourne dans le simulateur (Settings > Serveur MCP), sur `127.0.0.1` seulement.

## Avant de commencer

1. Appeler `sim_status`. Vérifier :
   - `firmware_loaded` : un full read est chargé (sinon, demander à l'utilisateur de l'ouvrir dans le simulateur) ;
   - `control_allowed` : si `false`, seuls les outils de lecture marchent (l'utilisateur coche « Autoriser le
     pilotage » dans Settings > Serveur MCP) ;
   - `running` : l'exécution du firmware tourne-t-elle ? (`sim_control` action `run` sinon) ;
   - `ui_mode` : en mode `SIMPLE` ou `EXTRA_SIMPLE`, la vitesse reste en temps réel.
2. Le nom du fichier (`file_name`) contient souvent un numéro de châssis (VIN) : ne pas le recopier dans un document
   public ou partagé sans l'accord de l'utilisateur.
3. Chaque action de pilotage est notée au Journal du simulateur : l'utilisateur voit ce qui a été fait.

## Les outils

**Lecture** (sans effet sur la simulation) :
- `sim_status` : fichier (nom, format, référence, indice logiciel, empreinte du programme, flash modifiée),
  définition et labels chargés, exécution, temps simulé, vitesse, moteur, mode d'interface.
- `engine_state` : physique du moteur virtuel (régime, papillon, air par course, lambda, températures, batterie,
  couple, VANOS, mode) et régime lu par le firmware (`firmware_rpm_word`).
- `engine_panel` : toutes les valeurs du panneau Moteur virtuel, dont **Défauts actifs (FD30)** et **Diagnostic**.
- `cpu_state` : PC, fonction en cours (si labels), instruction, registres, SP, CP, DPP.
- `bus_activity` : adresses de données lues / écrites pendant un moment (exécution en cours), avec l'instruction et
  la fonction qui accèdent. Filtres : `region` (FLASH, XRAM, IRAM, SFR, CAN), `rw`, `from` / `to`.
- `read_memory` : octets à une adresse CPU (hexa, ex. `FAE4`, ou label).
- `list_tables`, `read_table` : tables de calibration ; `read_table` donne les valeurs, les cellules lues
  récemment et `firmware_position` : la **position exacte** du firmware sur les axes (valeur que le CPU compare aux
  points de l'axe) et la valeur interpolée. Il faut que l'onglet Tables soit affiché pour le suivi des lectures
  (`sim_control` action `show_tab`, tab `Tables`).
- `list_curve_channels`, `read_curves` : historique des courbes (toutes les 10 ms, 10 minutes, enregistré même
  onglet caché).
- `disassemble`, `list_controls`, `dyno_status`, `read_gal` (équations du PLCC), `read_sensors`, `list_breakpoints`.

**Pilotage** (si autorisé) :
- `set_control` : n'importe quel curseur, case ou bouton du panneau Moteur virtuel (voir `list_controls`) :
  papillon, températures, batterie, vitesse, charge, **pannes simulées** (débitmètre débranché, sonde coupée,
  vilebrequin coupé...), cliquetis provoqué, Démarrer, Couper le contact, démarreur maintenu (`hold_ms`).
  Curseur : `value` nombre ; case : `value` true/false ; bouton : sans `value`. L'interface suit.
- `engine_setting` : mode du moteur (STOP, CRANK, FIXED, FREE), octane, cylindre du cliquetis, moteur simulé
  (B20, B25, B28, S52B32), `auto_start`.
- `sim_control` : `run`, `pause`, `reset`, `step` (count), `run_to` (address), `speed` (REAL, MAX...), `ui_mode`,
  `show_tab`.
- `write_memory` : RAM et SFR comme une écriture du CPU ; flash : contenu de la puce (persistant pour la session).
- `write_table` : une cellule de table (valeur convertie ou brute) ; la valeur brute la plus proche est écrite.
- `program_gal` : équations du décodeur d'adresse (4 sorties) ou `measured: true` pour celles d'origine.
- `set_sensor` : courbes ECT / IAT / MAF, loi et tensions du papillon, couronne, impulsions de vitesse, RESET.
- `dyno` : banc de puissance : `hold` (rpm), `sweep` (paliers : start, end, step), `pull` (tir), `stop` ;
  résultats avec `dyno_status`.
- `breakpoint` : points d'arrêt de code ou mémoire.
- `export_curves` : tout l'historique des courbes en CSV (dossier `logs` du simulateur).
- `save_firmware` : enregistre le binaire modifié à côté de l'original (`_modifie`), au format du fichier chargé ;
  jamais d'écrasement ; refuse si rien n'est modifié (sauf `force`).

## Recettes

**Démarrer le moteur et le laisser chauffer**
1. `sim_control` `run` (si `running` est faux).
2. `engine_setting` `auto_start: true`, attendre 5 à 8 s, puis `engine_state` : régime au ralenti (~800 tr/min).
3. Températures : `set_control` sur le curseur de température moteur, ou la case de montée en température automatique.

**Provoquer une panne et lire la réaction du calculateur**
1. `list_controls` avec `group: "Pannes simulées"` pour les libellés exacts.
2. `set_control` la case voulue à `true`.
3. Attendre quelques secondes (le firmware confirme un défaut après un délai), puis `engine_panel` : regarder
   « Défauts actifs (FD30) », « Diagnostic », « Voyant moteur ». Courbes utiles avec `read_curves`.
4. Remettre la case à `false` à la fin.

**Passer au banc**
1. Moteur démarré (voir plus haut), `sim_control` `speed` `MAX` pour aller plus vite (mode normal ou avancé).
2. `dyno` `sweep` (ex. start 1000, end 6000, step 250) ou `pull`.
3. Interroger `dyno_status` jusqu'à `state: DONE` ; présenter couple et puissance par régime.

**Modifier une table et garder le binaire**
1. `read_table` pour voir les valeurs et la position du firmware.
2. `write_table` (name, x, y, value) ; relire avec `read_table`, observer l'effet (`engine_state`, `read_curves`).
3. `save_firmware` pour enregistrer le binaire modifié ; donner le chemin à l'utilisateur.

**Savoir où en est le firmware**
`cpu_state` (fonction en cours), puis `bus_activity` sur 300 ms (adresses les plus utilisées, qui y accède) ;
`disassemble` autour du PC ; `read_memory` sur les variables.

## Précautions

- Le simulateur suit le matériel ; ne pas présenter une valeur lue comme une donnée constructeur. Distinguer les
  valeurs « firmware » (mémoire simulée) des valeurs « moteur » (physique du moteur virtuel).
- Les écritures en flash et les tables modifiées changent le comportement jusqu'au rechargement du full read :
  le dire à l'utilisateur et proposer `save_firmware` s'il veut garder le résultat.
- Reprogrammer le PLCC avec de mauvaises équations peut empêcher le CPU de lire sa flash : remettre `measured: true`.
- Ne jamais écraser ni supprimer les fichiers de l'utilisateur.
