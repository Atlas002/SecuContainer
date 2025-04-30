# Session 2 : Bonnes pratiques pour sécuriser les containers

## Activités Pratiques

## 1 - Sécurisation du réseau

Utilisation de dockers isolés : 

![0](img/0.png)



Récupération de la dernière image nginx :

![1-1](img/1-1.png)

Lancement d'un container avec une restrictions sur les ports utilisés :

![1-2](img/1-2.png)

Vérification de l'exposition des ports avec `netstat` : 

![1-3](img/1-3.png)


## 2 - Restreindre les permissions d’accès aux fichiers sensibles
Démarrage d'une image docker Alpine en interactif :

![2-1](img/2-1.png)

`cat` de `/mnt/passwd` :

![2-2](img/2-2.png)

Impossible d'éditer le fichier car il est en read-only :

![2-3](img/2-3.png)

## 3 - Auditer la configuration d’un container avec Docker Bench

Téléchargement de Docker Bench for Security :

![3-1](img/3-1.png)

Lancement du script d'évaluation :

![3-2](img/3-2.png)

Résultat de l'évaluation : score de 6 

![3-3](img/3-3.png)

Évaluation du container `vulnerables/web-dvwa`

![3-4](img/3-4.png)

Résultat de l'évaluation : score de 2 

![3-5](img/3-5.png)


## 4 - Stocker et Utiliser des Secrets

Lancement du container `Vault` :

![4-1](img/4-1.png)

Accès à `localhost:8200` :

![4-2](img/4-2.png)

Création du root token (split en 1/1 pour la simlplicité du lab) :

![4-3](img/4-3.png)

Récupération du root token et de la clé associée : 

![4-4](img/4-4.png)

Unseal de la vault à l'aide de la clé :

![4-5](img/4-5.png)

Connection à la vault à l'aide du root token :

![4-6](img/4-6.png)

Ajout de la méthode d'authentification par combo login/mot de passe :

![4-7](img/4-7.png)

Ajout d'un utilisateur : 

![4-8](img/4-8.png)

Création réussie d'un utilisateur root : 

![4-9](img/4-9.png)

![4-11](img/4-11.png)


Création de la politique ACL permettant de lire le secret à l'emplacement `containers/mon-secret` :

![4-10](img/4-10.png)

Création du 2e utilisateur :

![4-12](img/4-12.png)

Attribution de la politique ACL au nouvel utilisateur : 

![4-13](img/4-13.png)

![4-14](img/4-14.png)

Création du secret dans le chemin `containers/mon-secret` :

![4-15](img/4-15.png)

Récupération du token utilisateur depuis un container Alpine :

![4-16](img/4-16.png)

Récupération du secret à l'aide du token client :

![4-17](img/4-17.png)

## 5 - Trouver la clé

Récupération de l'image Docker : 

![5-1](img/5-1.png)

Scan de l'image avec la fonction `docker history`, on récupère la clé API ``U-never-will-saw-that``

![5-2](img/5-2.png)

Pour éviter d’exposer la clé dans l’image et dans l’historique, il aurait fallu ne jamais l’inscrire directement dans un ``RUN`` du Dockerfile, mais la passer soit au runtime via une variable d’environnement (``docker run -e API_KEY="$API_KEY" …``), soit au build uniquement avec BuildKit (``RUN --mount=type=secret,id=api_key curl -H "API-Key: $(cat /run/secrets/api_key)" …``), ce qui permet d’utiliser la clé sans qu’elle soit stockée dans une couche et donc visible par un simple ``docker history``.

## 6 - Rootless mode

Installation de Docker en mode rootless : 

![6-1](img/6-1.png)

Lancement d'un container nginx : 

![6-2](img/6-2.png)

Navigation vers la page web par défaut de nginx réussie :

![6-3](img/6-3.png)

Résultat du ``docker-bench-security`` : en rootless, Docker Bench exécute moins de contrôles (105 vs. 117) car de nombreux tests liés aux privilèges système et à la configuration du noyau (iptables, auditd, cgroups, AppArmor…) ne sont tout simplement plus applicables, et le score global descend légèrement (5 vs. 6) puisque certains contrôles passent en WARN/FAIL faute d’accès root. Cela reflète bien la réduction de surface d’attaque offerte par le mode rootless, au prix d’une couverture partielle des vérifications de sécurité.

![6-4](img/6-4.png)

## Conclusion

Cette session a permis d'explorer et d'implémenter un ensemble complet de bonnes pratiques pour sécuriser les conteneurs Docker. À travers nos six exercices pratiques, nous avons couvert les aspects fondamentaux de la défense en profondeur pour les environnements conteneurisés :

1. **Isolation réseau** : La restriction des ports exposés réduit considérablement la surface d'attaque.

2. **Restriction des accès aux fichiers** : Le montage en lecture seule des fichiers sensibles empêche leur modification malveillante.

3. **Audit de sécurité** : L'utilisation de Docker Bench permet d'évaluer objectivement la conformité aux bonnes pratiques et d'identifier les vulnérabilités.

4. **Gestion sécurisée des secrets** : HashiCorp Vault offre une solution robuste pour stocker et accéder aux secrets sans les exposer dans les images ou variables d'environnement.

5. **Protection contre les fuites de données sensibles** : L'analyse de l'historique des images révèle l'importance d'éviter d'inclure directement des secrets dans les Dockerfiles.

6. **Mode rootless** : L'exécution de Docker sans privilèges root constitue une protection efficace contre les attaques par élévation de privilèges.

La combinaison de ces différentes techniques forme un modèle de sécurité multi-couches essentiel pour les déploiements en production. Cette approche défensive complète répond aux principales menaces qui pèsent sur les conteneurs : vulnérabilités des images, élévation de privilèges, accès non autorisés aux données sensibles et attaques réseau.

La sécurité des conteneurs n'est pas un état final mais un processus continu qui nécessite une veille technologique, des audits réguliers et l'adaptation aux nouvelles menaces. En intégrant ces pratiques dans vos workflows DevOps et en automatisant les contrôles de sécurité, vous pouvez construire des environnements conteneurisés à la fois agiles et résilients.

**FIN**



