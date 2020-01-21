Dans cette partie nous nous intérressons particulièrement à la partie arduino.
C'est a dire la récupération des différentes données leur traitement ainsi que leur envoie via Nrf a BBB.
Nous avons aussi cherché a transférer les données qu'on récupère dans un fichier txt via Python.

Dans cette partie nous avons 2 codes :
le premier est sous l'environement arduino et va gérer la carte méga 2560 (initialisation pin, serial etc), le capteur flame click,le capteur air quality click, le module Nrf. 
le second code va lui permettre a notre environnement python de récupérer les données de la liaison sérial et créer un fichier txt avec notre base de données.


Tuto :
 Pour la partie capteur nous devons tout d'abord connecter les capteurs à la carte arduino. Ensuite nous devions implémenté 
 les différents codes de nos capteurs qui sont trouvable en cherchant la référence des capteur "click". Ensuite nous devons réunir 
 tous ces codes en un seul et rendre chaque fonctions non bloquantes pour permettre des exécutions en parrallèle. 
 Ensuite il faut implémenter la partie Nrf une fois cela fait on vérifie si les pipes de connexions ont bien été initialisé.
 Nous avons décidé dans un même temps de lire via l'environnement Python les données qui sortent via le port serial de notre carte
 arduino et de plus notre script va créer un fichier texte avec notre base de données a l'intérieur ( simulation de sauvegarde de back-up).
