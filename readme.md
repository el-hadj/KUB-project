# Déploiement de mon application Kubernetes

Ce référentiel contient un manifeste de déploiement Kubernetes pour l'application "episen_sca-dev".

## Prérequis

- Un cluster Kubernetes
- L'outil en ligne de commande kubectl installé
- Docker installé pour créer et pousser l'image Docker vers un registre.
- installation d'ingress se réferer au document (04-kubernetes.md)


## Déploiement

1. Lancez le docker registry pour récuperer les images de  notre application:

   ```shell
   cd projects/docker-registry/
   docker compose up -d
   ```

1. Créez l'image Docker:

   ```shell
   docker build -t localhost:5000/myepisen-sca_dev:latest .
   docker build -t localhost:5000/myepisen-sca_cache:latest .
   ```

2. Poussez l'image Docker vers un registre:

   ```shell
   docker push localhost:5000/myepisen-sca_dev:latest
   docker push localhost:5000/myepisen-sca_cache:latest
   ```

3. création d'un token admin pour kubernetes dashbord:

   ```shell
   kubectl -n kubernetes-dashboard create token admin-user
   #exécution de kubectl proxy
   kubectl proxy
   ```

3. Déployez le manifeste Kubernetes:

   ```shell
   cd projects/Tp3-kub/
   kubectl apply -f api-manifest.yml
   ```

   Cela créera un déploiement avec trois répliques du conteneur "myapp", qui écoutera sur le port 8080.

4. Exposez le déploiement à l'aide d'un Service Kubernetes:

   ```shell
   kubectl apply -f myapp-service.yaml
   ```

   Cela créera un Service Kubernetes qui mappe le port 8080 du conteneur au port 80.

   Vous devriez maintenant être en mesure d'accéder à l'application en utilisant l'adresse IP du Service et le port 80.

## Nettoyage

Pour supprimer le déploiement et le service, exécutez les commandes suivantes:

```shell
sh api-manifest_clean.sh
cd projects/docker-registry/data/
rm -rf *
``` 

