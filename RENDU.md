# Rendu — Atelier Kubernetes

> Complétez ce fichier au fil de l'atelier, puis faites un commit et un push (voir la section « Rendu » du README).

**Nom / Prénom :** Maryam GEORGI
**Groupe :** RS2

---

## Exercice 1 — Cluster

1. Combien de nœuds affiche `kubectl get nodes` ? Quel est le rôle de chacun ?
   >kubectl get nodes -o wide : Affiche les 3 nœuds de votre cluster (k3d-lab-agent-0, k3d-lab-agent-1 et k3d-lab-server-0), leur état (Ready), leur rôle (les agents ont le rôle <none> et le serveur a le rôle control-plane), leur âge, leur version Kubernetes (v1.35.5+k3s1), leurs adresses IP internes sur le réseau Docker, ainsi que le système d'exploitation et le runtime de conteneurs (containerd).

kubectl cluster-info : Confirme que le plan de contrôle Kubernetes (control plane) est bien en cours d'exécution, de même que les services essentiels comme CoreDNS et Metrics-server.
2. Que montre `docker ps` ? Qu'est-ce qu'un « nœud » dans k3d, concrètement ?
   >. On y voit la liste des conteneurs Docker créés pour simuler le cluster k3d :
>   k3d-lab-server-0 : Le conteneur exécutant l'image rancher/k3s, qui héberge le nœud maître (control-plane).
>   k3d-lab-agent-0 et k3d-lab-agent-1 : Les deux conteneurs exécutant également l'image rancher/k3s, qui jouent le rôle des nœuds de travail (workers).
>   k3d-lab-serverlb : Un conteneur supplémentaire (k3d-proxy) qui sert de répartiteur de charge (load balancer) pour acheminer le trafic vers le cluster.
Chaque « nœud » de Kubernetes dans k3d correspond à un conteneur Docker isolé qui exécute l'environnement k3s. La commande docker ps permet de visualiser l'infrastructure sous-jacente qui fait tourner vos machines virtuelles légères sur votre machine hôte (ou Codespace).

## Exercice 2 — Premier déploiement

3. Quels objets Kubernetes ont été créés par `kubectl create deployment` ? (indice : `kubectl get all`)
   >Un Deployment (nommé web) : Il sert de ressource de haut niveau pour gérer l'état souhaité de l'application. Un ReplicaSet (généré automatiquement par le Deployment) : Il garantit qu'un nombre défini de répods (par défaut 1) tourne en permanence. Un Pod (géré par le ReplicaSet, dont le nom commence par web-...) : C'est le conteneur proprement dit qui exécute l'image Nginx.Un Service (créé par la commande kubectl expose, nommé web) : Il expose l'application en interne sur le port 80 pour permettre aux autres composants (ou au tunnel port-forward) d'y accéder.
4. Vous avez supprimé le pod `web-...` : que s'est-il passé, et pourquoi ?
   >En observant le terminal avec kubectl get pods -w, on remarque que dès que le pod est supprimé, il disparaît brièvement, mais un nouveau pod est immédiatement recréé (avec un suffixe aléatoire légèrement différent). Le nom du pod initial a donc changé. Cela est arrivé grâce à la boucle de réconciliation de Kubernetes, l'état réel du cluster est constamment comparé à l'état désiré. Comme le Deployment exige qu'un pod (replicas: 1) tourne en permanence pour l'application web, la disparition soudaine du pod a déclenché une correction automatique : le ReplicaSet en a instantanément généré un nouveau pour respecter la consigne.   

## Exercice 3 — Modifier un conteneur en direct

5. Après suppression du pod, votre message « Bienvenue... » a-t-il survécu ? Pourquoi ?
   >Non, le message personnalisé a disparu et la page d'accueil par défaut de Nginx est revenue.
   Le système de fichiers d'un conteneur est éphémère. Lorsque le pod a été supprimé, le ReplicaSet en a recréé un nouveau entièrement neuf à partir de l'image      nginx d'origine. Toutes les modifications manuelles et directes apportées à l'intérieur du conteneur (via kubectl exec) sont donc perdues lors de son             redémarrage ou de son remplacement.   
6. Pourquoi le `port-forward` a-t-il cessé de fonctionner après la suppression du pod ?
   >Un tunnel port-forward vers un Service Kubernetes s'attache en réalité à la connexion réseau d'un pod spécifique en arrière-plan. Lorsque ce pod a été            supprimé, le tunnel a perdu sa cible initiale, ce qui a provoqué son arrêt (d'où l'erreur et le besoin de le relancer).

## Exercice 4 — Déclaratif

7. Quelle différence entre `kubectl create` et `kubectl apply` ?
   >
8. À quoi sert le `selector` d'un Service ? Que se passe-t-il s'il ne correspond à aucun pod ?
   >

## Exercice 5 — Scaling & mises à jour

9. Sur quels nœuds se trouvent vos 3 réplicas ? Collez la sortie de `kubectl get pods -l app=site -o wide`.
   ```

   ```
10. Avec l'image `nginx:9.99`, quel statut avait le pod ? Quelle commande vous a donné la cause ?
    >
11. Pendant la panne, le site restait-il accessible ? Pourquoi ?
    >

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
