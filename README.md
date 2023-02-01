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
```
