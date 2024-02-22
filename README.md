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

## Lancer le projet en local (Docker)

Au préalable : avoir récupéré le projet en local, et s'être positionné à la racine du projet.

#### Lancement du projet :
```bash
DevOps-CI-CD-Docker> docker-compose up -d
```

Remarque : on rajoute l'option `-d` pour être en exécution détachée et ainsi pouvoir utiliser le terminal. \
On l'enlevera si l'on souhaite voir l'intégralité des logs lors de l'exécution.

Par défaut, pour tester l'application, il suffira d'ouvrir un navigateur et de se rendre sur l'URL : [localhost:4173](http://localhost:4173/)

Le port utilisé est le `4173`, il a été choisi arbitrairement. Il est cependant possible d'en utiliser un autre en modifiant le fichier `docker-compose.yaml` (situé à la racine du projet) de manière suivante : 

```yaml
###
ports:
    - [CUSTOM_PORT]:4173
###
```
Ce changement de port peut aussi être effectué dans la `CI` de cette façon :
```yaml
###
    CONTAINER_PORT: "[CUSTOM_PORT]"
###
```
#### Pour stopper et supprimer le container lancé :
```bash
DevOps-CI-CD-Docker> docker-compose down
```
Nous avons choisi ici une orchestration avec docker-compose pour simplifier l'utilisation locale.

---
## Collaboration

La collaboration sur ce projet est structurée de manière à ce que le code soit validé avant d'être fusionné dans la branche `main`.\
Ainsi, toute modification directe sur la branche principale du dépôt est impossible. \
Pour apporter des modifications, il est nécessaire de créer une nouvelle branche et d'initier une `Pull Request`.\
Durant cette PR, le code est soumis à divers tests, et si l'un d'eux échoue, la PR ne peut être complétée. \
De plus, l'approbation d'un utilisateur du dépôt est requise pour que la fusion soit autorisée.

## Fonctionnement de la C.I.

Remarque : Les noms des composants (image, container...) ont été choisis arbitrairement pour correspondre au projet. \
Tous peuvent être adaptés dans le fichier `.github\workflows\CICD.yml` : 
```yml
env:
    IMAGE_NAME: "web-project-image"
    CONTAINER_NAME: "web-project-container"
    CONTAINER_PORT: "4173"
```

Plusieurs étapes sont définies dans la CI, chacune d'entre elles doit se dérouler sans erreur pour que la CI soit validée:

### Analyse du code

La CI mise en place analyse le code hébergé dans le dépôt GitHub, assurant ainsi sa qualité et sa conformité.\
Nous utilisons Hadolint pour vérifier la syntaxe du Dockerfile, assurant ainsi la stabilité de l'environnement de conteneurisation. \
De plus, nous avons mis en place une vérification du code Vue.js à l'aide de l'outil `ESLint`. \
Ce linter est exécuté automatiquement grâce à un script nommé `lint`, activé par la commande `npm run lint`.

### Construction de l'image Docker :
```bash
docker image build --tag=web-project-image ./web-project/
```

### Lancement du container :
```sh
docker container run -d -p 4173:4173 --name web-project-container web-project-image
```

### Test de l'application avec CURL : 
```sh
curl http://127.0.0.1:4173/
```
Comme précisé plus tôt, le port exposé est également personnalisable dans le fichier de configuration de la CI.

### Arrêt et suppression du container : 
```sh
docker container stop web-project-container
docker container rm web-project-container
```
## Fonctionnement de la C.D.

### Continuous Deployment

Un déploiement continu est implémenté dans ce projet.

Ce processus intervient uniquement lors d'un `push` sur la branche principale, donc lors de la validation d'une PR. \
Lors de cette étape, l'image générée avec le `Dockerfile` est poussée sur `DockerHub`, un service permettant de stocker et partager des images Docker. \
L'image sera alors marquée avec le tag `main`.

### Continuous Delivery

Nous avons aussi intégré un processus de livraison continue à ce projet.

Ce processus est déclenché à la suite de la création d'un tag sur le repository. \
Important : Ce tag correspond à une version de l'image, il doit respecter une convention de nommage `v*` (commencer par la lettre v). \
Dès lors que ce tag est créé, l'image sera poussée automatiquement sur `DockerHub` marquée avec le tag choisi.

## Contributeurs

- [Robin Peignet](https://github.com/rpeignet)
- [Yannick Boyer](https://github.com/YannickBOYER)