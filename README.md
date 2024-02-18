# DevOps-CI-CD-Docker
## Contexte du projet

Pour ce projet nous avons choisi de dockeriser et de mettre en place la CI-CD d'une solution réalisée au cours de notre Master 1 à l'ESGI de Lyon. \
Il s'agit d'un projet ciblé front-end réalisé en Vue.js.

---

## Récupérer le projet avec Git

Cloner le projet en local :
```sh
git clone https://github.com/YannickBOYER/DevOps-CI-CD-Docker.git
```

## Docker

### 1) Mise en oeuvre du projet en local sur son poste de travail

Au préalable : avoir récupéré le projet en local, et s'être positionné à la racine du projet.

#### Lancement du projet :
```bash
docker-compose up -d
```

Remarque : on rajoute l'option `-d` pour être en exécution détachée et ainsi pouvoir utiliser le terminal. \
On l'enlevera si l'on souhaite voir l'intégralité des logs lors de l'exécution.

Pour tester l'application, il suffira d'ouvrir un navigateur et de se rendre sur l'URL : [localhost:4173](http://localhost:4173/)

Le port utilisé `4173` à été choisi arbitrairement. Il est cependant possible d'en utiliser un autre en modifiant le fichier `docker-compose.yaml` (situé à la racine du projet) de manière suivante : 

```yaml
###
ports:
    - PORT_CUSTOM:4173
###
```

#### Pour stopper et supprimer le container lancé :
```bash
docker-compose down
```
Nous avons choisi ici une orchestration avec docker-compose pour simplifier l'utilisation locale.

---
### 2) Le fonctionnement sur la C.I.

Remarque : le nom des composants (image, container...) ont été choisi arbitrairement pour correspondre au projet.
Tous peuvent être adaptés sur le fichier `.github\workflows\CICD.yml` : 
```yml
env:
    IMAGE_NAME: "web-project-image"
    CONTAINER_NAME: "web-project-container"
    CONTAINER_PORT: "4173"
```

#### Construction de l'image dockerisée du projet :
```sh
docker image build --tag=web-project-image ./web-project/
```

#### Lancement du container :
```sh
docker container run -d -p 4173:4173 --name web-project-container web-project-image
```

#### Test de l'application avec CURL : 
```sh
curl http://127.0.0.1:4173/
```
Comme précisé plutôt, le port exposé est également personnalisable dans le fichier de configuration de la CI.

#### Arrêt et suppression du container : 
```sh
docker container stop web-project-container
docker container rm web-project-container
```
