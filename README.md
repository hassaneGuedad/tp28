# TP28 – Analyse de projet Java Maven avec SonarQube

## Objectif
Mettre en place SonarQube en local (via Docker), créer un projet, générer un token, analyser un projet Java Maven, puis interpréter les résultats (Quality Gate, bugs, code smells, vulnérabilités, couverture, etc.).

## Prérequis
- Docker Desktop (ou Docker Engine)
- Navigateur Web
- JDK installé
- Maven (ou Maven Wrapper `mvnw`)
- Projet Java Maven (présence de `pom.xml`)

## Étapes

### 1. Démarrer SonarQube en local (Docker)
1. **Créer les volumes Docker** (persistance) :
   ```sh
   docker volume create sonarqube_data
   docker volume create sonarqube_logs
   docker volume create sonarqube_extensions
   ```
2. **Lancer SonarQube** :
   ```sh
   docker run -d --name sonarqube -p 9000:9000 \
     -v sonarqube_data:/opt/sonarqube/data \
     -v sonarqube_logs:/opt/sonarqube/logs \
     -v sonarqube_extensions:/opt/sonarqube/extensions \
     sonarqube:lts-community
   ```
   - Si le port 9000 est occupé, utiliser `-p 9001:9000` et ouvrir http://localhost:9001.
3. **Accéder à SonarQube** :
   - Ouvrir http://localhost:9000
   - Identifiants par défaut : `admin` / `admin` (changement du mot de passe demandé)

### 2. Comprendre l’écran “Overview” et le Quality Gate
- Le Quality Gate indique si le projet respecte les critères minimaux (bugs, vulnérabilités, coverage, duplications, etc.).
- Un projet peut être "Failed" (trop de bugs, faible couverture) ou "Passed" (toutes les conditions remplies).
- Prioriser la correction des bugs et vulnérabilités, puis améliorer la maintenabilité et la couverture.

### 3. Créer un projet et générer un token
1. Créer un projet manuellement dans SonarQube.
2. Choisir "Analyser localement".
3. Générer un token d’analyse (garder le token secret !).

### 4. Lancer l’analyse Maven
1. SonarQube propose une commande Maven adaptée :
   ```sh
   mvn clean verify sonar:sonar \
     -Dsonar.projectKey=VOTRE_PROJECT_KEY \
     -Dsonar.host.url=http://localhost:9000 \
     -Dsonar.login=VOTRE_TOKEN
   ```
   - Adapter le port si besoin.
2. Se placer dans le dossier du projet Maven (contenant `pom.xml`).
3. Exécuter la commande dans un terminal.
4. Attendre le message "ANALYSIS SUCCESSFUL".

### 5. Consulter les résultats dans SonarQube
- Ouvrir le projet dans SonarQube :
  - **Overview** : résumé + Quality Gate
  - **Issues** : liste détaillée (Bugs, Code Smells…)
  - **Security Hotspots** : points à valider
  - **Measures** : métriques (duplication, complexité…)
  - **Code** : code annoté
  - **Activity** : historique des analyses
- Corriger en priorité :
  1. Bugs
  2. Vulnérabilités / Hotspots
  3. Code Smells
  4. Coverage (ajout de tests)

## Dépannage (problèmes fréquents)
- **Erreur 401 / Unauthorized** : token invalide → régénérer et relancer.
- **Connection refused** : SonarQube non démarré / mauvaise URL.
- **Projet introuvable** : `sonar.projectKey` différent de celui créé.
- **Analyse lente** : attendre que SonarQube soit "Ready".

## Mini-récap
- SonarQube tourne en local via Docker sur localhost:9000
- Projet créé manuellement
- Token généré et utilisé dans Maven
- Analyse déclenchée via `mvn clean verify sonar:sonar`
- Lecture du Quality Gate et des issues (priorité : bugs + sécurité)

---
© 2026 MLIAEdu Platform. L'éducation est la clé pour démocratiser l'IA et le génie logiciel.
