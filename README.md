# Conteneurisez votre application Web Full Stack avec Docker Compose

## Application de pile MERN Cloudinary CRUD Operations

![Appli de Pile MERN](/images/mern.png "Appliation de pile MERN Cloudinary CRUD").

Pour comprendre le but de cette application, cliquer [ICI](https://tksuryavanshi.blogspot.com/2023/10/cloudinary-crud-operations-mern-stack.html) et vous aurez la réponse à toutes vos questions.

## Mon Objectif

Mon objectif pour ce project est de pouvour Dockeriser les différentes parties de cette application.

## Stack technologique utilisée

* **MongoDB**
* **Express.js**
* **React.js**
* **Node.js**
* **VS Code**
* **Docker**
* **Docker Desktop**
* **Cloudinary**

## Partie 1: Architecture de l'application

L'architecture ci-dessous nous présente le processus de dockerisation et de déploiement d'une application Web complète composée d'une base de données **MongoDB**, d'un backend implémenté avec **NodeJs** et d'un frontend en **React** et **Express.js**.

Ce processus se fera bien évidemment à l'aide d'un manifeste **docker-compose.yml**.

![Architecture](/images/architecture.png "Architecture du processus de dockerisation").

## Que voulons-nous faire ?

Pour dockeriser une application Web full stack à l'aide de docker-compose, nous créons un « **Dockerfile** » séparé pour le **frontend** (client) et le **backend** (serveur) afin de créer une image docker et nous utilisons l'image docker officiel de **MongoDB** pour conteneuriser et définir leurs configurations dans un fichier **docker-compose.yml**. Dans ces configurations, nous connaissons des sujets de variations de Docker et de docker-compose comme l'environnement Docker, le volume, etc.

## Parie 2: 📌 Aperçu des étapes

Nous avons quelques étapes au-dessus de la solution :

1. Créez un « Dockerfile » pour le frontend (client).
2. Créez un « Dockerfile » pour le backend (serveur).
3. Créez un 'docker-compose.yml' et définissez la variable.
4. Enfin, consultez l'URL d'accès à la « démo » (localhost).

### 📘 Rubrique 1:

Créez un « **Dockerfile** » pour le **frontend (client)**.

Créez un fichier docker sur le dossier **client** .

```
# Étape 1 et spécifiez un nom 'builder'
 FROM node:latest AS builder 

# Créez un répertoire et accédez au répertoire
 WORKDIR /app 

# Copiez le fichier package.json dans mon répertoire actuel pour installer la dépendance nécessaire  
 COPY package.json . 

# Installer la dépendance
 RUN npm install 

# Copier les autres fichiers dans mon répertoire actuel
 COPY . . 

# Créer et optimiser le fichier statique
 RUN npm run build 

# Stage-2
 FROM nginx:1.25.2-alpine-slim 

# Copier le fichier statique dans mon dossier Nginx pour servir le contenu statique
 COPY --from=builder /app/build /usr/ share/nginx/html 

# Ouvrez le port pour réagir
 EXPOSE 80 

# Exécutez nginx au premier plan
 CMD [ "nginx" , "-g" , "daemon off;" ]

```

Pour écrire un Dockerfile, suivez l'étape :

1. Sélectionnez une image de base en utilisant ' **FROM** '.
2. Créez un répertoire et changez de répertoire en utilisant ' **WORKDIR** '.
3. Copiez le fichier nécessaire ou le fichier de dépendance du package comme « **package.json** ».
4. Installez le **package** à partir du fichier.
5. Copiez l'autre **code** de la **machine locale** vers l'image Docker .
6. **Générez** le package ou **démarrez** le conteneur.
7. Si nous utilisons ' **Stage-2**', copiez le fichier nécessaire de ' **Stage-1** '.
8. **Exposez** le « **port** » pour le port ouvert pour **exécuter ce conteneur sur ce port**.
9. Démarrez le serveur sur le conteneur à l'aide de la commande « **CMD** ».



### 📘 Rubrique 2:

Créez un « **Dockerfile** » pour le **backend (serveur)**.

Créez un fichier docker sur le dossier du **serveur**.

```
# Sélectionnez une image de base
 FROM node:20-alpine3.17 

# Créez un répertoire et accédez au répertoire
 WORKDIR /app 

# Copiez le fichier package.json dans mon répertoire actuel pour installer la dépendance nécessaire  
 COPY package.json . 

# Installer la dépendance
 RUN npm install 

# Copier les autres fichiers dans mon répertoire actuel
 COPY . . 


# Ouvrez le port du serveur express
 EXPOSE 5000 

# Exécutez le rhum express au premier plan
 CMD [ "npm" , "start" ]

```

### Écrivez intelligemment un fichier Docker simple

1. **Choisissons une image de base** : commencez par une image de base appropriée qui répond aux exigences de notre application. Utilisons des images officielles autant que possible, car elles sont généralement bien entretenues et sécurisées.

2. **Utilisons une image de base minimale** : optez pour une image de base minimale pour réduire la taille de votre image Docker finale. Par exemple, au lieu d'utiliser un système d'exploitation complet, envisageons d'utiliser **Alpine Linux** car il est léger.

3. **Copier uniquement les fichiers nécessaires** : copieons uniquement les fichiers nécessaires dans l'image Docker. Utilisez un fichier « **.dockerignore** » pour exclure les fichiers et répertoires inutiles.

4. **Combiner les étapes** : réduisons le nombre de couches dans votre image en combinant plusieurs commandes en une seule instruction RUN.

### 📘 Rubrique 3:

À ce stade, nos deux fichiers Docker sont prêts pour le « **frontend** et **backend** ». D'autres images officielles du docker « **MongoDB** » seront également téléchargées à partir du « **Docker Hub** ».

Maintenant, écrivez un fichier '**docker-compose.yml**' dans le répertoire racine du dossier du projet pour exécuter tous les conteneurs dans une seule commande ' **docker-compose up -d** '.

```
version: '3.8'

# Services
services:
  # Server service
  server:
    build:
      context: ./server
      dockerfile: Dockerfile
    container_name: backend
    ports:
      - "5000:5000"
    env_file: ./.env
    environment:
      - DB_HOST=mongodb_server
      - DB_USER=$MONGODB_USER
      - DB_PASSWORD=$MONGODB_PASSWORD
      - DB_NAME=$MONGODB_DATABASE
      - DB_PORT=$MONGODB_DOCKER_PORT
    depends_on:
      - mongodb

  # Client service
  client:
    build:
      context: ./client
      dockerfile: Dockerfile

    container_name: frontend
    ports:
      - "80:80"
    depends_on:
      - server

  # Database service
  mongodb:
    image: mongo:latest
    container_name: mongodb_server

    env_file: ./.env
    environment:
      - MONGO_INITDB_ROOT_USERNAME=$MONGODB_USER
      - MONGO_INITDB_ROOT_PASSWORD=$MONGODB_PASSWORD

    ports:
      - "27017:27017"

    volumes:
      - ./mydata:/data/db

# Volumes define
volumes: 
  mydata:

```

Maintenant, allez dans le **répertoire racine** du dossier du projet, ouvrez le terminal puis recommandez ' **docker-compose up -d** ' pour exécuter le manifest **docker-compose.yml** et créez trois conteneurs ' **backend**, **frontend**, **mongodb_server**'.

1. Sur le fichier ' **docker-compose.yml**', définissons les services que nous souhaitons utiliser à l'aide du mot-clé ' **services** '.

2. Ensuite, nous définissons les services que nous voulons utiliser comme ' **server** ' , ' **client** ' , ' **mongodb** '.

3. Sélectionnez ensuite l'emplacement du ' **Dockerfile** ' et le ' **nom du fichier** '.

4. Nous pouvons également définir le **nom du conteneur** à l'aide du mot-clé ' **containers_name** ' .

5. En outre, les « **ports** » sont utilisés pour la redirection de port entre le « **port de la machine hôte** » et le « **port du conteneur** ».

6. Les **services qui sont exécutés en premier** , puis **les autres services** sont définis à l'aide du mot-clé « **depend_on** ».

7. Définissons **la variable d'environnement** sur un fichier ' **.env**' et ce fichier est défini à l'aide du mot-clé ' **env_file** '.

8. Le mot-clé « **volumes** » est utilisé pour sauvegarder les données du **conteneur** vers **la machine hôte** car si le conteneur est « **mort** », nous pouvons « **sauvegarder** » nos données importantes. C'est le plus important dans les services de bases de données.

9. Enfin, définissons les « **volumes** » que nous souhaitons utiliser.

10. Nous utilisons un réseau « **par défaut** » , nous ne pouvons donc pas discuter du « **réseau Docker** ». Si vous êtes intéressé, allez [**ici**](https://docs.docker.com/network/) pour en savoir plus à ce sujet.

11. Ici ' **docker-compose up -d**' , la commande ' **-d** ' dans docker est un raccourci pour l'option ' **— detach**'. lorsque vous exécutez un conteneur avec l'indicateur « **-d** » , il démarre le conteneur en arrière-plan et vous renvoie immédiatement à l'invite de commande sans bloquer le terminal.

![](/images/result_deocker_compose.mp4)

<video width="520" height="340" controls>
  <source src="/images/result_deocker_compose.mp4" type="/video/mp4">
</video>

Nous voyons que les images de nos différentes parties de notre appli Web sont bien présentes dans la partie images de notre Docker Desktop.

![Docker Desktop](/images/dockerDesktop.png "Image buildées dans le docker desktop")

Après cela, nous voyons que le conteneur Docker Desktop est en cours d'exécution.

![Docker Desktop](/images/container_running.png "Coonteneur en cours d'exécution")

Maintenant , nous accédons à l'adresse localhost sur le navigateur en tapant le socket suivant: **127.0.0.1:80** et voyons le résultat.

![Test 1](/images/test1.png "Appl. page d'accueil")

![Test 2](/images/test2.png "Clic sur le bouton Add User")

En cas de problème avec « **ajouter un utilisateur** » , arrêtez **docker-compose** en utilisant « **docker-compose down** » , supprimez les volumes de cache, puis à nouveau « docker-compose up -d » pour exécuter l'image docker.

🌟 **Félicitations !!** 🌟 , Nous avons conteneurisé avec succès votre application Web full stack (**MongoDB**, **Express.js**, **React**, **Node.js**) avec ' **docker-compose** '.

[Référence](https://github.com/bjnandi/containerize-full-stack-app-MERN-with-docker-compose?tab=readme-ov-file#dproject-description)
