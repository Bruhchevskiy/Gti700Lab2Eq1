# Gti700Lab2Eq1
Labo 2 pour GTI700
lab2.md
L'objectif du laboratoire 2 est d'utiliser le paradigme publish-subscribe (topic-based, avec les protocoles MQTT
et/ou MQTT-WebSocket) pour récolter les données de différents capteurs IoT, les publier et les consommer par
l'entremise d'une application web "front-end" afin de les afficher.
Dans le laboratoire 1, vous avez écrit une application dorsale (back-end) en JavaScript pour récolter les
données d'un capteur de température connecté à un Raspberry Pi, et les afficher de manière textuelle et
graphique sur une page web simple (servie par votre application frontale).
Dans ce laboratoire, vous allez d'abord écrire un module "back-end" Node.JS qui récolte les données de 2
capteurs différents à intervalles réguliers, et qui les envoie vers un courtier (broker) MQTT local. Vous allez
également rédiger un court programme qui permet de faire afficher la DEL d'une couleur donnée, transmise
par un service d'alerte via le courtier MQTT public. Enfin, vous allez concevoir une application web "front-end"
rudimentaire qui va:
obtenir les données météo du serveur MQTT local, et les afficher dans la page
obtenir des données (couleurs RGB) du serveur MQTT public, et les afficher pour chaque équipe sur la
page.
L'annexe 1 présente une vue d'ensemble de l'architecture globale du projet.
Sur votre Raspberry Pi, vous devez installer le courtier MQTT Mosquitto. Sur Ubuntu:
sudo apt-get install mosquitto
Vous devrez ensuite configurer ce dernier afin qu'il puisse accepter les connexions au moyen de MQTT over
WebSocket en éditant le fichier de configuration suivant (en root):
sudo nano /etc/mosquitto/mosquitto.conf
Ajouter les lignes suivantes avant la fin du fichier:
port 1883 # numéro de port à utiliser pour MQTT
listener 9001 # numéro de port à utiliser pour MQTT over WebSocket
protocol websockets # activer le protocole websockets
Relancez ensuite le service pour appliquer les changements à la configuration:
sudo service mosquitto restart
Laboratoire 2 (GTI700 - volet applicatif)
Échange de données IoT au moyen du protocole MQTT
1- Courtiers et librairie MQTT
1.1 Déploiement d'un courtier MQTT local
Vous devrez démontrer à la chargée de lab le bon fonctionnement du courtier MQTT.
Pour une partie de ce TP, nous utiliserons le serveur public MQTT HiveMQ. Les informations pour l'utiliser sont
disponibles à l'adresse: https://www.hivemq.com/public-mqtt-broker/. Attention, ce serveur est public! Vous
ne serez pas les seuls utilisateurs à l'utiliser!
Pour vous connecter au moyen d'un client MQTT, vous pouvez utiliser le port TCP (1883). Pour vous connecter
au moyen d'un client MQTT-WebSocket, vous pouvez utiliser le port WebSocket (8000).
L'entreprise HiveMQ a conçu l'application web front-end HiveMQ MQTT Web Client, accessible à l'adresse
http://www.hivemq.com/demos/websocket-client/. Vous pouvez utiliser cet outil pour vous connecter au
courtier public MQTT (1.2) ainsi qu'à votre courtier local (1.1), au moyen du protocole MQTT over WebSocket.
Vous pourrez déboguer vos souscriptions et publications avec cet outil. Cet outil est open-source -- vous
pouvez regarder le code source à https://github.com/hivemq/hivemq-mqtt-web-client (l'outil utilise d'ailleurs
la librairie Eclipse Paho).
Vous pouvez utiliser la librairie MQTT de votre choix en JavaScript (il y en a plusieurs). Pour la partie "frontend", votre librairie devrait prendre en charge MQTT sur WebSocket, car il ne sera pas possible d'utiliser le
protocole MQTT directement dans le "sandbox " du navigateur pour des raisons de sécurité. Un bon choix est
la librairie Eclipse Paho (https://www.eclipse.org/paho/clients/js/), qui fonctionnera autant dans votre "backend" (application sur Raspberry Pi qui publie les données -- tâche 1) que dans votre "front-end" (application
web de visualisation -- tâche 2). Elle est très simple d'utilisation. Il y a également d'autres options.
Vous devrez utiliser simultanément des capteurs permettant d'obtenir les données suivantes, sur votre
Raspberry Pi:
température
humidité
Notes:
Il est possible qu'un même capteur puisse retourner différentes données en même temps. Nous vous
laissons le libre choix de la combinaison des capteurs physiques à utiliser, tant que les mesures
demandées peuvent être obtenues. L'annexe 2 décrit le branchement du capteur "humiture".
Comme au laboratoire précédent, les scripts d'obtention des données des capteurs sont en Python. Vous
devrez transférer les données récoltées à votre programme JS. Encore une fois, vous pouvez:
choisir de rediriger la sortie standard des scripts Python vers votre code JavaScript (par exemple, en
suivant la technique décrite dans l'annexe A du laboratoire précédent), ou
réimplémenter les scripts écrits en Python en JavaScript au moyen d'une librairie appropriée.
Par la suite, vous devez publier les données des 2 capteurs vers le courtier MQTT hébergé sur votre Pi (1.1),
sur les canaux MQTT suivants (le nom exact est important), aux 3 secondes (remplacez XX par votre numéro
d'équipe Moodle, ex. 01, 02, ..., 10, 11, 12). Le format est simplement la valeur lue telle quelle avec deux
décimales et un point comme séparateur (ex., publish("21.123") ).
1.2 Utilisation du courtier public MQTT HiveMQ
1.3 Outil de test MQTT (dans le navigateur)
1.4 Librairie MQTT
2- Obtention des données des capteurs
GTI700/Data/E2025/XX/temperature
GTI700/Data/E2025/XX/humidite
Vous devez tout d'abord connecter la LED tel que vu aux laboratoires de la première partie du cours (regardez
également le fichier intelligent_measur.py afin de savoir comment intéragir avec la DEL).
Un système d'alerte global infonuagique a été mis sur pied: ce système publie régulièrement une valeur de
couleur RGB sur le canal suivant, au moyen du courtier public MQTT HiveMQ (1.2):
GTI700/Alerts/E2025/XX
Le message contient 3 valeurs numériques entre 0 et 255, séparées par un point-virgule (R;G;B -- p. ex.,
255;0;0 serait la couleur rouge). Vous devez souscrire à ce canal sur votre Raspberry Pi. Vous devez ensuite
définir la couleur de la DEL à la valeur reçue pour votre équipe, lorsqu'une nouvelle publication est émise sur
ce canal.
Les publications seront émises par un service que nous mettrons sur pied, vous n'avez pas à gérer cet aspect.
Conseil: en vous inspirant de intelligent_measur.py , vous pouvez écrire un court programme Python qui
acceptera la valeur RGB à utiliser pour la LED, et vous pouvez simplement invoquer ce programme à partir de
votre code Node.JS. Vous n'aurez probablement pas besoin de capturer la sortie standard.
Vous devez réaliser une page HTML simple. On lancera le "front-end" simplement en ouvrant le fichier HTML
dans le navigateur -- il n'y a donc pas nécessité d'avoir un "back-end" ici puisque le front-end sera autonome.
Votre front-end devra se connecter:
à votre courtier MQTT hébergé sur le Raspberry Pi, afin d'obtenir les données météo publiées, via
WebSocket.
au courtier HiveMQ public (également via WebSocket) afin d'obtenir les données du système d'alerte
(couleur RGB) publiées par le service.
Vous devez souscrire aux topics appropriés sur les deux serveurs MQTT. La page devra suivre le modèle
suivant et contenir les sections suivantes.
Pour les valeurs de température et d'humidité, affichez les 15 dernières valeurs.
Pour le système d'alerte, vous devez colorier la case dans la deuxième colonne ( background-color ) selon la
dernière couleur reçue du système d'alerte chaque équipe. Cette couleur sera la même que sur les DELs
connectées aux Raspberry Pi. N'incluez pas le mot "couleur".
Heure Température Humidité
11:00:01 4.4 C 45%
11:00:04 4.2 C 47%
3- Obtention des données du système d'alerte
4- Front-end: affichage des données des capteurs et de la couleur du
système d'alerte
Mes capteurs
Heure Température Humidité
11:00:07 4.5 C 49%
11:00:10 4.4 C 51%
Équipe Couleur
1 "couleur"
2 "couleur"
3 "couleur"
4 "couleur"
5 "couleur"
... ...
Notes:
La chargée de laboratoire vous communiquera le nombre d'équipes dans la classe.
Pour les souscriptions aux données de la classe, vous devriez utiliser les "wildcards" (jetons) MQTT
appropriés tels que vus en classe.
Un rapport est demandé. Vous devez discuter des points suivants (maximum 6 pages). Une pénalité sera
appliquée pour les fautes de français (voir le barème) et une mise en page incorrecte ou un manque de
rigueur dans la présentation. Une courte introduction et conclusion sont demandées.
Votre rapport doit discuter des éléments suivants, et en respectant l'ordre suivant:
1. Entités, topics, publishers et subscribers [10 pts]:
De manière similaire à l'exercice que nous avons fait en classe, identifiez les entités (acteurs) du
système qui publient et/ou qui souscrivent à des topics MQTT. Pour chaque entité, identifiez les topics
sur lesquels cette entité publie ainsi que les topics auxquels cette entité souscrit, ainsi que les
courtiers utilisés pour chaque topic. Pour répondre à cette question, remplissez le tableau "GTI700 -
Rapport lab 2 - conception MQTT.docx" et insérez-le dans votre rapport.
2. Performance [4 pts]:
Jugez-vous que la performance de votre système de bout-en-bout est adéquate? Justifiez votre
réponse, et suggérez des pistes pour améliorer la performance globale du système s'il y a lieu.
3. Gestion des erreurs [5 pts]:
Avez-vous implémenté une gestion des erreurs dans votre code? Par exemple, que se produit-il si la
connexion à l'un des courtiers MQTT est interrompue?
Quelle stratégie pourrions-nous mettre en place pour pallier à une perte de messages MQTT?
4. Travail d'équipe [5 pts]:
Comment avez-vous configuré votre environnement pour permettre le travail en équipe à distance,
considérant qu'une seule personne a accès au PI?
Système d'alerte
Rapport
Si vous travaillez toujours en présentiel (par exemple, à l'ÉTS), veuillez plutôt indiquer quelle
serait votre stratégie si vous souhaitiez à distance.
Comment avez-vous réparti les tâches dans le travail d'équipe?
5. Défis et difficultés rencontrées [6 pts]:
Quelles sont les défis/difficultés et particularités rencontrées durant la réalisation de ce projet, et
quelles stratégies avez-vous utilisés pour y pallier?
Note: si vous avez fait quelconque utilisation d'un SIAG, vous décrire exactement quelle utilisation vous en
avez fait dans votre rapport. Veuillez lire la section correspondante dans le module 0 (introduction/plan de
cours).
À noter que la structure et la présentation doivent être professionnelles.
Tâche Description
Points
max.
1.1 Déploiement et fonctionnement du courtier MQTT sur le Pi 3
2.1 Module JS permet d'obtenir les données d'humidité et de température 4
2.2
La publication des données de température et pression est effectuée
correctement et avec les bons formats, et au bon rythme
6
3.1 Obtention des données d'alerte du serveur MQTT HiveMQ 4
3.2 Définition de la couleur de la DEL selon la couleur obtenue 7
4.1
Affichage du tableau de température et d'humidité de manière adéquate,
rafraîchissement et expiration des valeurs désuètes
8
4.2 Affichage et rafraîchissement du tableau des couleurs de la DEL 8
Pénalité Non respect du déploiement -25%
Pénalité Mauvais "topics" ou utilisation du mauvais courtier, ou mauvaise utilisation des
"wildcards"
-25%
Pénalité Le format de la page ne respecte pas le modèle proposé (front-end) -25%
Pénalité Autres pénalités, au besoin
Démo Professionnalisme/préparation adéquate (modulation de la note globale) <5>
Rapport (Voir critères) 30
Total Max. 70
Notes:
Les pourcentages de pénalités sont des bornes supérieures. Si une pénalité s'applique, la chargée de
laboratoire déterminera la note qui s'imposera.
Grille de correction
Il est essentiel que la démonstration au chargé soit préparée de manière professionnelle. Une note sur 5
sera attribuée à ce critère, et cette note modulera votre note pour la partie démo du labo: (5 multipliera
votre note par 100%, 4 la multipliera par 90%, 3 la multipliera par 80%, 2 la multipliera par 70%, 1 la
multipliera par 60% et 0 la multipliera par 50%).
La remise du code source se fera sur Moodle/ENA. La date limite de remise et la correction en laboratoire
(présence obligatoire des membres de l'équipe, sauf autorisation spéciale de votre chargée de laboratoire)
sont indiqués sur Moodle.
Bien que cela ne soit pas obligatoire, nous vous suggérons d'utiliser un entrepôt Git (GitHub ou GitLab
disponible à l'ÉTS) pour votre projet. Tous les membres de l'équipe devraient "pousser" du code pour étayer la
contribution de tous et chacun. Advenant le cas où nous recevions une plainte concernant un membre de
l'équipe ne fournissant pas sa juste part de travail, nous pourrions vous demander de nous donner accès à
votre entrepôt ou de nous fournir une copie des "logs" (bien entendu, nous espérons fortement que cela ne
se sera pas nécessaire!).
La figure suivante décrit l'architecture globale du projet (figure produite par Amna Snene).
Remise
Utilisation d'un entrepôt Git
Annexes
Annexe 1: Architecture globale du projet
Pour utiliser le capteur "humiture", effectuer le branchement tel que décrit dans la figure, et exécuter le script
"Python" humid.py avec les privilèges du superutilisateur: sudo python humid.py (note: le fichier humid.py est
sur la page ENA à la section du laboratoire).
Note: le schéma est adapté de l'énoncé du laboratoire 6 du cours GTI780 - A2019 (rédigé par Aris Leivadeas).
Annexe 2: Utilisation du capteur "humiture"
