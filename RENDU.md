# Rendu — Atelier Kubernetes

> Complétez ce fichier au fil de l'atelier, puis faites un commit et un push (voir la section « Rendu » du README).

**Nom / Prénom :** KAMAGATE Marthe
**Groupe :** INGE2 - APP - RS2

---

## Exercice 1 — Cluster

1. Combien de nœuds affiche `kubectl get nodes` ? Quel est le rôle de chacun ?
   > 3 nœuds : 1 'k3d-lab-server-0' et 2 workers 'k3d-lab-agent-0' et 'k3d-lab-agent-1'
2. Que montre `docker ps` ? Qu'est-ce qu'un « nœud » dans k3d, concrètement ?
   > 'docker ps' montre un conteneur par nœud, dans k3d un nœud c'est un conteneur Docker qui fait tourner k3s : il simule une machine du cluster.


## Exercice 2 — Premier déploiement

3. Quels objets Kubernetes ont été créés par `kubectl create deployment` ? (indice : `kubectl get all`)
   > Un Deployment 'web', qui a créé un ReplicaSet 'web-xxxx', qui a lui-même créé un Pod 'web-xxxx-yyyy'. Le Service 'web' vient de la commande 'kubectl expose', pas de 'create deployment'.

4. Vous avez supprimé le pod `web-...` : que s'est-il passé, et pourquoi ?
   >  Un nouveau pod a été recréé immédiatement, avec un nom différent. Le ReplicaSet doit maintenir 1 réplica, c'est la boucle de reconciliation

## Exercice 3 — Modifier un conteneur en direct

5. Après suppression du pod, votre message « Bienvenue... » a-t-il survécu ? Pourquoi ?
   > Non car le système de fichiers d'un conteneur est éphémère : le nouveau pod repart de l'image 'nginx' d'origine, donc la modification faite avec 'exec' est perdue.
6. Pourquoi le `port-forward` a-t-il cessé de fonctionner après la suppression du pod ?
   > Un 'port-forward' vers un Service s'attache en réalité à un seul pod précis. Quand ce pod est supprimé, le tunnel n'a plus de cible et il s'arrête : l'erreur apparaît dans '/tmp/web.log'.

## Exercice 4 — Déclaratif

7. Quelle différence entre `kubectl create` et `kubectl apply` ?
   > 'create' est impératif : il crée une ressource et échoue si elle existe déjà. 'apply' est déclaratif : on décrit l'état désiré dans un fichier YAML, et Kubernetes crée la ressource ou la met à jour pour atteindre cet état. On peut le relancer autant de fois qu'on veut.
8. À quoi sert le `selector` d'un Service ? Que se passe-t-il s'il ne correspond à aucun pod ?
   > Il désigne les pods qui reçoivent le trafic, par leurs labels. S'il ne correspond à aucun pod, le Service n'a aucun endpoint et les requêtes échouent.

## Exercice 5 — Scaling & mises à jour

9. Sur quels nœuds se trouvent vos 3 réplicas ? Collez la sortie de `kubectl get pods -l app=site -o wide`.
   ```
NODE   READINESS GATES
site-845b89d549-854d6   1/1     Running   0          5m22s   10.42.0.9   k3d-lab-agent-0    <none>           <none>
site-845b89d549-dgfgt   1/1     Running   0          1m15s     10.42.1.5   k3d-lab-agent-1    <none>           <none>
site-845b89d549-mc6mt   1/1     Running   0          15s     10.42.2.6   k3d-lab-server-0   <none>
   ```
10. Avec l'image `nginx:9.99`, quel statut avait le pod ? Quelle commande vous a donné la cause ?
    > Statut 'ErrImagePull' puis 'ImagePullBackOff'. C'est 'kubectl describe pod -l app=site' qui donne la cause, dans la section Events : le tag 'nginx:9.99' n'existe pas sur Docker Hub.
11. Pendant la panne, le site restait-il accessible ? Pourquoi ?
    > Oui. Pendant un rolling update, Kubernetes ne supprime les anciens pods que lorsque les nouveaux sont prêts. Le nouveau pod ne démarrant jamais, les anciens pods en 'nginx:1.28' ont continué à servir le site.

## Exercice 6 — Portainer & Mario

12. URL publique de votre Mario :
    >
13. Retrouvez le Deployment `mario` en ligne de commande. Portainer et `kubectl` parlent-ils au même composant ? Lequel ?
    >

## Bonus (facultatif)

14. Bonus réalisés et remarques :
    >

---

**Score obtenu avec `./scripts/check.sh` :** ... / ...
