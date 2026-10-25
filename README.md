Mini-Projet Systèmes Embarqués : Box Domotique (SEM2205T)
Ce dépôt contient les livrables pour la création d'une distribution Linux embarquée personnalisée (Yocto/Poky) pour la carte STM32MP135F-DK.

Le système transforme la carte en box domotique capable de relever des données capteurs et de se mettre à jour à distance.

Contenu du Dépôt
core-image-ubs-homeautomation-stm32mp13-disco.wic.gz : L'image disque binaire prête à être flashée (contient Bootloader + Kernel + Rootfs).
Documentation : Procédures d'installation et de compilation ci-dessous.
Sources du Projet (Environnement de Compilation)
En raison de la taille de l'environnement Yocto (plusieurs Go), les sources complètes (Layer meta-ubs, configuration local.conf, et recettes) sont hébergées externement.

TÉLÉCHARGER L'ARCHIVE DU PROJET COMPLET (.tar.gz)

Guide de Flashage Rapide (Sans recompiler)
Si vous souhaitez uniquement tester le fonctionnement sur la carte sans recompiler le projet :

Récupérez l'image .wic.gz présente dans ce dépôt GitLab.
Décompressez l'archive :
gunzip core-image-ubs-homeautomation-stm32mp13-disco.wic.gz
Flashez sur la carte SD (Attention à bien identifier votre lecteur, ex: /dev/mmcblk0 ou /dev/sdX) :
sudo dd if=core-image-ubs-homeautomation-stm32mp13-disco.wic of=/dev/mmcblk0 bs=8M status=progress conv=fdatasync
Insérez la carte SD et bootez la STM32MP1.
Guide de Re-Compilation (Développeur)
Pour modifier le projet ou régénérer l'image depuis les sources :

Téléchargez l'archive complète via le lien Google Drive ci-dessus.
Extrayez l'archive :
tar -xzvf sauvegarde_yocto_complet.tar.gz
cd my-yocto-project
Initialisez l'environnement :
cd poky
source oe-init-build-env
Lancez la compilation :
bitbake core-image-ubs-homeautomation
Fonctionnalités Validées
Fonctionnalité	Détails Technique
Boot Linux		Yocto Poky (Kirkstone) sur STM32MP135F-DK
Layer Personnalisée		Création de meta-ubs-homeautomation
Capteur I2C		Driver et lecture du capteur Si7021 (Temp/Hum)
Service Auto		Application sensor-app lancée via SystemD au boot
Mise à jour OTA		Gestionnaire OPKG configuré et fonctionnel (HTTP)
SDK		SDK généré pour le cross-développement
