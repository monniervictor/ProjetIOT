Pour la creation du cloud nous avons choisi la solution azure grâce aux 100$ offert pour les étudiants. 

# Readme Cloud :  
L'intégralité des fichiers de configurations seront upload dans ce git.
Afin d'éviter les services IOT tout fait, nous avons décidé de tout faire à partir d'un serveur Ubuntu 18.04.
Sur ce serveur nous avons installé :
  - la base de données Elasticsearch (port d'entré 9200)
  - Kibana pour l'interpretation graphique (et mise en place de dashboard etc...) avec comme port d'entré 5601
  - Logstash comme outil pour recevoir, traiter et écrire les données sur la base de données Elasticsearch (port d'entré 5044)
  - Nginx en tant que reverse proxy afin de gérer les redirection uri/port (et donc nous éviter de devoir taper les ports d'entrés
  pour accéder aux différents outils utilisés
  - UFW comme firewall afin d'authoriser qu'un certains type d'échange à être envoyé sur notre serveur.

---

## Installation des outils nécéssaires : 
  Nous avons donc commencé par mettre à jour notre serveur ubuntu afin d'éviter tout problème de précaritées : 
  > apt-get update et apt-get upgrade
  
  Installation de java qui sera nécessaire pour les différents outils cités précédemment : 
  > apt-get install default-java 
  
  Définition de la variable d'environnement JAVA_HOME sur le path du dossier d'installation de java
 
---
 
### Installation et configuration Elasticsearch :
  Importation de la clef GPG Elasticsearch qui nous servira à la validation des paquets Elasticsearch :
  > wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
    
  Ajout d'une source de téléchargements pour les requetes APT : 
  > echo "deb https://artifacts.elastic.co/packages/6.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-6.x.list
    
  Mettez à jour votre liste APT avec la nouvelle source :
  > apt update
    
  Installer Elasticsearch :
  > apt-get install elasticsearch
    
  Allez dans le fichier de configuration d'elasticsearch (/etc/elasticsearch/elasticsearch.yml) les champs intéréssants à 
  modifier sont les suivants : path.logs: /var/log/elasticsearch qui définit votre dossier de fichiers de log et 
  network.host: localhost qui définit votre réseau (localhost permet ici de contacter elasticsearch uniquement en interne 
  par le serveur lui meme ce qui nous donnera par la suite qu'un seul point d'accès à nos données (NGINX)
  il vous reste aussi des parametre interessant tel que le port de connexion que vous voulez utiliser mais attention celui ci 
  sera à modifier dans les autres outils qui sont préconfigurer pour se connecter à la base en utilisant le port 9200    
  
  Démarrez le service elasticsearch :
  > systemctl start elasticsearch
  
  Si vous souhaitez que votre service se lance à chaque démarrage de votre machine tapez : 
  > systemctl enable elasticsearch
  
  Enfin, testez que votre BDD répond avec les commandes suivantes : 
  > systemctl status elasticsearch 
  
  (et verifier qu'il n'y ai pas d'érreur et que ce soit marqué running)
  
  > curl -X GET "localhost:9200" 
  
  afin d'avoir une réponse des informations de base de votre bdd
 
---
 
### Installation et configuration Kibana : 

  Installation du package kibana :
  > apt-get install kibana
    
  Modifier votre fichier de configuration kibana (/etc/kiabana/kibana.yml) comme vous le souhaitez. Dans notre cas nous avons souhaité 
  modifier "server.host: "localhost"" afin d'authoriser les requêtes d'accès ou de modification de la bdd uniquement par notre serveur
  modifier "server.basePath: "/kibana_view"" (qui semble être un bug quand nous lions nginx à kibana) afin de devoir taper
  l'url suivant : @IPouDNS/kibana_view pour accéder à kibana
  
  Nous allons maintenant créer un utilisateur que nous devrons renseigner pour nous connecter à l'interface graphique de kibana : 
  > echo "kibanaadmin:`openssl passwd -apr1`" | sudo tee -a /etc/nginx/htpasswd.users

---

### Installation et configuration NGINX : 
  Installation du package NGINX : 
  > apt-get install nginx
  
  Activer le lancement du service NGINX au démarrage du serveur et activer le service NGINX : 
  > systemctl enable nginx
  
  > systemctl start nginx
  
  Vous allez maintenant créer un fichier de configuration nginx. Dans notre cas nous allons utiliser le nom kibana : 
  > nano /etc/nginx/sites-available/kibana et rentrer comme le fichier présent dans le git possédant le meme nom. 
  
  Il faut savoir que je n'ai pas réussi à mettre en place le HTTPS alors que je me suis basé sur des configurations
  que j'avais misent en place dans mon entreprise. Je soupçonne un bug lié à la liaison NGINX/Kibana.
  Les explications du fichier de configuration se trouvent dans le fichier lui meme avec des commentaires.
  Une fois le fichier configuré il faut supprimé le précédent lien symbolique suivant : /etc/nginx/site-enabled/default
  une fois celui ci supprimé faite un lien avec votre précédent fichier de configuration : 
  > ln -s /etc/nginx/sites-available/kibana /etc/nginx/sites-enabled/kibana
  
  Testez la configuration avec la commande suivante : 
  > nginx -t
  
  Redémarrer le service afin qu'il prenne en compte les modifications : 
  > systemctl reload nginx

---

### Installation et configuration Logstash :

  Installation du package logstash : 
  > apt-get install logstash
  
  Préparer un fichier de configuration pour gérer ce qui est réception/filtre/envoie dans la BDD dans cet exemple 
  nous utilisons /etc/logstash/conf.d/logstash-test.conf. Ici aussi le fichier sera expliqué par des commentaires dans 
  le git.
  Il faut maintenant dire à votre logstash quel fichier de configuration prendre en compte. Pour cela il vous faut modifier
  le fichier /etc/logstash/logstash.yml. Pour cela modifier la ligne suivante avec le nom de votre fichier de config précédemment
  créé : 
  > path.config: /etc/logstash/conf.d/logstash-test.conf
  
  Il ne vous reste plus qu'a tester la configuration de logstash avec la commande suivante : 
  > /usr/share/logstash/bin/logstash --path.settings /etc/logstash -t
  
  Une fois que la commande à terminée si votre configuration est bonne vous aurez marqué : "Configuration OK"
  
  Activer le service et faites en sorte que celui ci se lance automatiquement au démarrage du serveur : 
  > systemctl start logstash
  
  > systemctl enable logastash
