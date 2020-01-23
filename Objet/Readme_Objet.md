# Readme Objet

Dans cette partie nous nous intérressons particulièrement à la partie arduino.
C'est à dire la récupération des différentes données leur traitement ainsi que leur envoie via Nrf a BBB.
Nous avons aussi cherché a transférer les données qu'on récupère dans un fichier txt via Python.

Dans cette partie nous avons 2 codes :
- le premier est sous l'environement arduino et va gérer la carte méga 2560 (initialisation pin, serial etc), le capteur flame click,le capteur air quality click, le module Nrf. 
 - le second code va lui permettre a notre environnement python de récupérer les données de la liaison sérial et créer un fichier txt avec notre base de données.


## Tuto :
 Pour la partie capteur :
 - Connecter les capteurs à la carte arduino
 - Implémenter les différents codes de nos capteurs qui sont trouvable en cherchant la référence des capteur "click"
 - Concaténer tous ces codes en un seul et rendre chaque fonctions non bloquantes pour permettre des exécutions en parrallèle
 - Implémenter la partie nRF
 - Vérifier si les pipes de connexions ont bien été initialisé
 
 Nous avons décidé dans un même temps de lire via l'environnement Python les données qui sortent via le port serial de notre carte
 arduino. En parallèle, notre script permettra la création d'un fichier texte comportant la base de données à l'intérieur (simulation de sauvegarde de back-up).
