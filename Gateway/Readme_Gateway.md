# Beagle Bone et module nRF

---

## Contexte
Vous trouverez ici les informations concernant la connexion de deux BBB à travers des modules nRF

Version OS : BBB to BBB

Communication : Module nRF24L01


## BeagleBone Black
Nous avons relié la BBB à notre PC via USB. Après le démarrage nous nous sommes connecté via l'interface WEB : 192.168.7.2:3000 


## Configuration IP

Pour ne pas avoir de problème avec les priorités de gestionaires réseaux, nous avons supprimer le module connman
> sudo apt remove connman

Dans /etc/network/interfaces Nous nous sommes ensuite directement connecté sur le réseau de CPE en utilisant une @ disponible : 
> inet iface eth0 static
> address 134.114.51.125/23
> gateway 134.114.49.1

Configuration du DNS dans /etc/resolv.conf
> nameserver 134.114.49.16
> nameserver 134.114.49.19

Nous relançons ensuite le service networking:
> service networking reload
> service networking restart


## Configuration des Pins

Les bus 2.0 et 2.1 sont présents sur les slots 1 2 et 4 du microBUS.
Dans le cas où les bus ne sont pas configurés, il faut vérifier dans le fichier /boot/uEnv.txt que les BUS Spi sont bien activés.

Vous devez avoir ces lignes :
```
uboot_overlay_options:[uboot_overlay_addr4=/lib/firmware/BB-SPIDEV0-00A0.dtbo]
uboot_overlay_options:[uboot_overlay_addr4=/lib/firmware/BB-SPIDEV1-00A0.dtbo]
```

Ensuite, si vous avez des conflits entre le Spi et le HDMI, vous pouvez les désactiver par précaution :
```
uboot_overlay_options:[disable_uboot_overlay_video=1]
uboot_overlay_options:[disable_uboot_overlay_audio=1]
uboot_overlay_options:[enable_uboot_overlays=1]
```

Suivant la version que vous possédez, vous aurez différents moyens de vérifier que les BUS sont actifs.

En version debian 8.6, kernel 4.4.X, vous pouvez afficher le fichier slots de la manière suivante :
>  cat /sys/devices/platform/bone_capemgr/slots

(Sur d'autres version, vous pouvez pleurer)

En effet, le fichier slots n'est plus géré sur les versions supérieures à 4.4.X.

Vous pouvez donc uniquement vérifier les BUS Spi actifs avec ls /dev/spi* mais vous aurez moins d'infos.
Normalement il y en a 4 : le 1.0, 1.1, 2.0, 2.1

Ensuite, suivant le slot que vous allez utiliser pour le module nRF, vous aurez à exporter et activer les pins suivant les versions :

En version 8.6 "Jessie", vous devrez effectuer des commandes similaires à :
> echo XXXX > /sys/class/gpio/export

(XXXX sera le numéro absolu du port GPIO vu par le processeur)

Pour le connaître, vous pouvez vous référer au deux images de ce site --> http://exploringbeaglebone.com/chapter6
Chacune correspond à un slot de ports, le 8 et le 9.
Le numéro est grosso modo calculé selon la position du pin sur 1 des 4 BUS de bits :

EX : Si je cherche le pin 9_28, il est situé sur le 17ème pin du BUS 3, comme chaque BUS est de 32 bits, on fait le calcul suivant :

3*32+17 = 113, pour l'exporter nous ferons donc echo 113 > /sys/class/gpio/export
Durant la seconde étape, vous devrez moder les pins, càd leur fixer un mode de fonctionnement.
Chaque pin possède plusieurs mode utilisable et est par conséquent multiplexé. Vous devrez donc vous rendre le dossier propre de ce pin, pour y modifier les fichiers direction, value etc de façon à leur donner le mode que vous désirer

En debian 9.9, vous devrez utiliser l'utilitaire config-pin qui ne fonctionne pas sur l'autre version mentionné au dessus.
Cet utilitaire vous permet d'exporter, moder, et voir le statut de vos pins.

Voir les mode asscoiés :
> config-pin -l PX_XX 

Voir le mode et l'état en cours sur le pin :
> config-pin -q PX_XX 

Associer un mode :
> config-pin PX_XX YYY

(avec YYY un mode tel que spi, uart, spi_cs, spi_sclk)

Pour d'autres infos, vous référer au man de cet utilitaire.



## Librairie RF24

Télécharger le fichier install.sh de http://tmrh20.github.io/RF24Installer/RPi/install.sh
> wget http://tmrh20.github.io/RF24Installer/RPi/install.sh 

Modifier les droits pour l'utiliser
> chmod +x install.sh 

Lancer l'installation
> ./install.sh 



## Communication

Nous avons créé un code simplifié pour s'assurer de la bonne communication entre les deux modules nRF

(Voir le code XXXX.cpp dans rf24libs/RF24/examples_linux)
> cd rf24libs/RF24/examples_linux  

Configurer les pins comme vu dans la partie $Configuration des Pins
> radio(60,20)

Compiler et lancer les codes
> make
> sudo ./XXXX

