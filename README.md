# GeoQuizz

## Prérequis

•  Maven <br>
•  Docker <br>
•  Docker-compose <br>
•  Pagekite <br>

## Installation

Dans un premier temps, verifier que l'ip de votre machine docker correspond à celle des applications 

•  Modifier la variable 'host' (par défaut : localhost) dans la partie 'data' de l'instance de Vue du fichier player.js <br>
•  Modifier la variable 'host' (par défaut : localhost) dans la partie 'data' de l'instance de Vue du fichier backoffice.html <br>

Build des deux APIs depuis la racine du projet : 
```sh
$ cd ./API_BACKOFFICE
$ mvn clean install --DskipTests
$ cd ../API_PLAYER
$ mvn clean install --DskipTests
```

Ensuite il faut lancer le docker : 
```sh
$ docker-compose up --build -d
```

Tous les containers seront lancés en même temps. 


-d : Vous permet de reprendre la main sur votre invite de commande.
--build : Va construire les container avant de les executer

Pour remplir la table d'exemple:
```sh
$ docker cp ./data.sql postgres:/docker-entrypoint-initdb.d/dump.sql
$ docker exec -u postgres postgres psql postgres postgres -f docker-entrypoint-initdb.d/dump.sql
```


Ces commandes vont copier le fichier data.sql dans le container postgres puis executer le script sql

### Mobile

Pour utiliser la partie mobile, vous devez utiliser pagekite qui permettra d'acceder à l'API via internet.
Il est nécessaire d'avoir un compte pagekite.
Pour cela, quand les containers docker sont initialisés lancez la commande : 

```sh
$ pagekite.py (IP du docker):8081 (votre adresse de compte pagekite) 
```


## Utilisation : 
### DOCUMENTATION

Vous pouvez accéder à la documentation de l'api grâce a cette adresse : 

•  {IpMachineDocker}/swagger-ui.html

### API PLAYER

| Nom | Description | Method | Nom |
| ------ | ------ | ------ | ------ |
| Créer une partie | Permet de créer une partie avec un token | Post | /partie/{serieid} |
| Envoyer le score | Permet d’envoyer le score afin que le résultat soit enregistré.  Il faut mettre le token dans le header | PUT | /partie/{id} |
| Récupérer toutes les parties | Permet de récupérer toutes les parties enregistrée | Get | /partie |
| Récupérer une partie | Permet de récupérer les informations de une partie. Il faut mettre le token dans le header | Get | /partie/{idpartie} |
| Supprimer une partie | Permet de supprimer une partie | DELETE | /partie/{id} |


### API BACKOFFICE

| Nom | Description | Method | Nom |
| ------ | ------ | ------ | ------ |
| Créer une série | Permet de créer une série | Post | /series |
| Modifier une série | Permet de modifier une série | PUT | /series/{id} |
| Supprimer une série | Permet de supprimer une série | DELETE | /series/{id} |
| Récupérer une série | Permet de prendre une série | Get | /series/{id} |
| Ajouter une photo | Permet d'ajouter une photos | Post | /photos/upload |
| Ajouter les infos d'une photo | Permet d'ajouter les informations photos comme la localisation ou la serie associée | Post | /photos/{serieid} |
