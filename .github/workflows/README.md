# Explication du workflow GitHub Actions (`main.yml`)

Ce dépôt utilise un pipeline CI/CD automatisé grâce à GitHub Actions, défini dans le fichier `.github/workflows/main.yml`.

## Que fait ce workflow ?

Le workflow se déclenche automatiquement à chaque push sur les branches `main` et `develop`. Il est composé de deux jobs principaux :

---

### 1. **test-backend**

Ce job réalise les étapes suivantes :

- **Checkout du code**  
  Récupère le code source du dépôt.

- **Installation de Java 21**  
  Configure l’environnement avec le JDK 21 (distribution Temurin).

- **Build et tests Maven**  
  Se place dans le dossier `simple-api` et exécute `mvn clean verify` pour compiler le projet et lancer les tests unitaires.

- **Analyse SonarCloud**  
  Lance une analyse de qualité de code avec SonarCloud sur le projet `simple-api` grâce à la commande Maven `sonar:sonar`.  
  Les paramètres de connexion à SonarCloud (clé de projet, organisation, token) sont fournis via des secrets GitHub.

---

### 2. **build-and-push-docker-image**

Ce job dépend du succès du job précédent. Il effectue :

- **Login Docker Hub**  
  Se connecte à Docker Hub avec les identifiants stockés dans les secrets GitHub.

- **Build et push des images Docker**  
  - **Backend** : Construit et pousse l’image Docker du backend (`simple-api`) si la branche est `main`.
  - **HTTP Server** : Construit et pousse l’image Docker du serveur HTTP (`http-server`) si la branche est `main`.
  - **Database** : Construit et pousse l’image Docker de la base de données (`database`) si la branche est `main`.

Chaque image est taguée avec le nom d’utilisateur Docker Hub et le tag `latest`.

---

## Configuration requise

Pour que le workflow fonctionne, il faut ajouter les secrets suivants dans les paramètres du dépôt GitHub :

- `SONAR_TOKEN` : Token d’accès SonarCloud
- `DOCKER_USERNAME` : Nom d’utilisateur Docker Hub
- `DOCKER_PASSWORD` : Mot de passe Docker Hub

---