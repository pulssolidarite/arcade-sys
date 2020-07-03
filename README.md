# PULS Sys V2

> A complete system to manage Arcade terminal capable of running retro games with donations management.

#### Build Setup

```bash
1 - Flash Raspbian
2 - Build Retroarch 1.7.7 --> https://gist.github.com/AlexMax/32e5d038a66ce57253e740ea75736805 
3 - Lancer Retroarch pour test, puis aller à droite dans Réglages > Entrées > Utilisateur 1
4 - Vérifier le type de périphérique et le numéro du périphérique
5 - Assigner toutes les touches pour l'utilisateur
6 - Refaire les mêmes étapes (depuis l'étape 3) pour l'utilisateur 2
7 - Télécharger la dernière release (le fichier .AppImage) et la mettre sur le Bureau --> https://github.com/hilaliMoncef/arcade-sys/releases
8 - cd /home/pi/Desktop
9 - chmod a+x LeNomDuFichier.AppImage
10 - Add Env Variables --> sudo nano /etc/profile et ajouter à la fin "export PULS_LOGIN=LeLoginChoisi" et ensuite "export PULS_MDP=LeMDPChoisi", puis faire CTRL+X (et appuyer sur Yes pour valider puis appuyer sur Entrer)
11 - Faire cd /home/pi et puis faire un "git clone https://github.com/hilaliMoncef/arcade-sys-games.git". Un nouveau dossier contenant les jeux et les cores sera créé. Il faudra rajouter les jeux dessus plus tard et ensuite programmer un "git pull" automatique.
12 - Il faut taper dans une commandline "retroarch", configurez les inputs du gamepad de l'utilisateur 1 (menu à droite) et configurez toutes les touches pour les deux utilisateurs.
13 - Il faut ensuite "Sauvegarder la configuration automatique"
14 - Reboot
15 - Lancer l'appImage
```

#### Prerequisites

- RetroArch 1.7.7 installed and configured
- Node.js with Electron JS
- Payter Terminal
- Linux armv7l Environnement for building
