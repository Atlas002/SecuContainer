# Sécurité des Containers

Ce cours explore les concepts fondamentaux et avancés de la sécurité des containers, en mettant l'accent sur les pratiques de sécurité essentielles pour protéger les environnements conteneurisés.

## Introduction aux Containers et à la Sécurité

### Objectifs
- Comprendre le fonctionnement des containers et leurs différences avec les machines virtuelles.
- Identifier les enjeux de sécurité liés aux containers.

### Définition d'un Container
Un container est une unité d'exécution légère qui encapsule une application et ses dépendances dans un environnement isolé. Contrairement aux machines virtuelles, les containers partagent le noyau du système d'exploitation de l'hôte tout en maintenant une isolation entre les applications
.

### Différences entre Containers et Machines Virtuelles
| Aspect | Containers | Machines Virtuelles |
|--------|------------|---------------------|
| Isolation | Partage le noyau de l’hôte | Isolation complète |
| Légèreté | Rapide à démarrer, faible empreinte mémoire | Démarrage plus lent, plus gourmand |
| Portabilité | Facile à déplacer d’un environnement à un autre | Moins portable |

### Enjeux de Sécurité des Containers
1. **Partage du noyau de l’hôte** : Les containers partagent le même noyau, ce qui signifie qu’une faille dans le noyau pourrait compromettre tous les containers en cours d’exécution.
2. **Images non sécurisées** : Beaucoup d’images disponibles sur des registres publics contiennent des failles de sécurité.
3. **Gestion des privilèges** : Par défaut, certains containers s’exécutent avec des privilèges élevés, augmentant les risques d’attaques
.

## Sécurité des Orchestrateurs

### Objectifs
- Comprendre le fonctionnement de Kubernetes et son architecture.
- Identifier les principaux composants de Kubernetes.

### Architecture de Kubernetes
Kubernetes est un orchestrateur de containers qui permet de gérer le déploiement, la mise à l’échelle et l’exploitation d’applications conteneurisées. Son architecture repose sur plusieurs composants principaux :
1. **Master Node** : Inclut l'API Server, le Scheduler, le Controller Manager, et etcd.
2. **Worker Nodes** : Inclut le Kubelet, le Kube Proxy, et le Container Runtime
.

### Enjeux de Sécurité des Orchestrateurs
1. **Exposition des API Kubernetes** : Une mauvaise configuration des accès peut permettre à des attaquants d’exécuter des commandes malveillantes sur le cluster.
2. **Exécution de Pods non sécurisés** : Par défaut, Kubernetes permet l’exécution de pods avec des privilèges élevés.
3. **Réseau et communication inter-pods** : Par défaut, tous les pods peuvent communiquer entre eux sans restriction
.

## Outils de CI/CD

### Objectifs
- Comprendre l’importance de l’intégrité des images.
- Découvrir les bonnes pratiques pour sécuriser un pipeline.

### Signature d'images avec COSIGN
Cosign permet de signer les images avec une clé cryptographique, garantissant l'authenticité et l'origine vérifiable des conteneurs. Il vérifie que les images n'ont pas été altérées après leur création, protégeant contre les modifications malveillantes
.

### Sécurité dans les Pipelines CI/CD
1. **Fuites de secrets** : Les clés API et mots de passe ne doivent jamais être stockés en clair dans le code source.
2. **Exécution de code non vérifié** : Un pipeline peut exécuter du code malveillant s'il n'est pas correctement sécurisé.
3. **Déploiement d’images non scannées** : Utiliser des scanners comme Trivy ou Grype pour détecter les vulnérabilités dans les images
.

## Projet Étudiant : "Minecraft Secure Cluster"

### Objectif
Déployer un serveur Minecraft sécurisé dans Kubernetes avec une CI/CD contrôlée par RBAC.

### Livrables Attendus
1. Dépôt GitLab avec code fonctionnel.
2. Documentation des choix de sécurité.
3. Capture d'écran du serveur Minecraft accessible.
4. Exemple de rejet de pipeline pour CVE critique
.

### Architecture Globale
```
[GitLab CI] -> [Build/Lint/Sign/Scan] -> [Registry] -> [Kubernetes (Kind)] -> Minecraft Server
```

### Étapes du Projet
1. **Exemple d'infrastructure Kubernetes locale (Kind)** : Configuration d'un cluster Kubernetes local avec Kind.
2. **GitLab Runner dans le Cluster** : Installation et configuration d'un GitLab Runner dans le cluster.
3. **RBAC Pour la CI** : Création de rôles et de bindings RBAC pour sécuriser les déploiements.
4. **Dockerfile sécurisé** : Création d'un Dockerfile pour lancer un serveur Minecraft.
5. **Déploiement Kubernetes** : Création d'un déploiement Kubernetes pour le serveur Minecraft.
6. **Pipeline GitLab CI** : Configuration d'un pipeline CI/CD pour le déploiement du serveur Minecraft
.