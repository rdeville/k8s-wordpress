```
# On créer le dossier mariadb
mkdir mariadb
# On va dans le dossier mariadb
cd mariadb
# On autogénère le manifest depuis un déploiement
kubectl create deployment mariadb \
    --image=mariadb:10.7.7  \
    --dry-run=client -o yaml > statefulset.yaml
# On regarde l'état du manifest et on supprime les clés qui ne serve à rien
# ON CHANGE LE KIND DE Deployment à StatefulSet
vim statefulset.yaml
# On applique le manifest
k apply -f statefulset
# Il y a un erreur, clé 'serviceName' manquante
# On regarde la documentation kubernets (mot clé "statefulset kubernetes")
# On modifie le manifest du statefulset et on ajoute la clé
vim statefulset.yaml
# On applique le manifest
k apply -f statefulset
# On remarque que le pods est en crashlookbackoff
# On regarde si ça vient du pull de l'image
k describe pod mariadb-sts
# L'image est bien pull, donc ça bien du container mariadb
k logs mariadb-sts-0
# L'erreur viens du fait qu'il manque une variable d'environment.
# On la stock dans un fichier
vim env.password
# On créer un secret a partir du fichier
k create secret generic \
  --from-env-file=./env.password \
  mariadb-secrets
k get secret/mariadb-secrets -oyaml
k get secret/mariadb-secrets -oyaml > secret.yaml
vim secret.yaml # On retire les clés qui ne serve à rien
k get secrets
k get pods # On remarque que le pods et toujours down
vim statefulset.yaml # On ajout le secret en variable d'environment
k apply -f statefulset.yaml
k get pods
# Il nous faut maintenant un service
# On va sur la documentation kubernetes
# On ajoute le port mariadb sur le statefulset
vim statefulset.yaml
# On créer le service
vim statefulset.yaml
# On applique les changements
k apply -f statefulset.yaml
k apply -f service.yaml
# On vérifie que tout est la
k get pods
k get service
k describe service
# Nous allons maintenan faire le wordpress
# On va dans le dossier wordpress
cd ../wordpress
# On va sur docker hub pour avoir la référence de l'image
# On créer le déployement
kubectl create deployment wordpress-deployment \
    --image=wordpress:php8.2  \
    --dry-run=client -o yaml > deployment.yaml
# On retire les clé qui ne serve à rien et on passe le réplica à 3
vim deployment.yaml
```
