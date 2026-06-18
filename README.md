# Jenkins CI/CD Pipeline

> **FR** — Pipeline CI/CD Jenkins pour une application Java Maven : incrémentation de version sémantique automatique, build, build d'image Docker, push vers Docker Hub, et commit automatique de la version dans Git. Évolue vers l'utilisation d'une Shared Library Groovy.
>
> **EN** — Jenkins CI/CD pipeline for a Java Maven application: automatic semantic version increment, build, Docker image build, Docker Hub push, and automatic version commit to Git. Evolves to use a Groovy Shared Library.

---

## Stack

![Jenkins](https://img.shields.io/badge/Jenkins-Pipeline-red?logo=jenkins)
![Java](https://img.shields.io/badge/Java-8-blue?logo=openjdk)
![Maven](https://img.shields.io/badge/Maven-build-red?logo=apachemaven)
![Docker](https://img.shields.io/badge/Docker-Hub-blue?logo=docker)
![Groovy](https://img.shields.io/badge/Groovy-DSL-4298B8?logo=apachegroovy)

---

## FR — Description

Ce projet démontre la mise en place d'un pipeline Jenkins déclaratif complet pour automatiser le cycle de vie d'une application Java.

**Étapes du pipeline :**
1. **Increment version** — Lit la version actuelle dans `pom.xml`, incrémente le patch automatiquement, met à jour le fichier
2. **Build app** — `mvn clean package` → génère le JAR
3. **Build image** — Build l'image Docker, push vers Docker Hub avec la version comme tag
4. **Deploy** — Placeholder pour le déploiement (à étendre selon l'environnement cible)
5. **Commit version update** — Commit et push le `pom.xml` mis à jour dans le repo Git

**Concepts démontrés :**
- Utilisation de `readFile()` et regex Groovy pour extraire la version depuis le XML
- Gestion sécurisée des credentials Jenkins (`withCredentials`)
- Évitement de la boucle de trigger infinie (commit Jenkins → build → commit → ...)
- Script Groovy dans le Jenkinsfile pour la logique conditionnelle

## EN — Description

This project demonstrates setting up a complete declarative Jenkins pipeline to automate the lifecycle of a Java application.

**Pipeline stages:**
1. **Increment version** — Reads the current version from `pom.xml`, automatically increments the patch, updates the file
2. **Build app** — `mvn clean package` → generates the JAR
3. **Build image** — Builds the Docker image, pushes to Docker Hub with version as tag
4. **Deploy** — Placeholder for deployment (to extend based on target environment)
5. **Commit version update** — Commits and pushes the updated `pom.xml` back to the Git repo

**Concepts demonstrated:**
- Using `readFile()` and Groovy regex to extract version from XML
- Secure credentials management in Jenkins (`withCredentials`)
- Avoiding infinite trigger loops (Jenkins commit → build → commit → ...)
- Groovy scripting in Jenkinsfile for conditional logic

---

## FR — Évolution : Shared Library (`jenkins-shared-lib` branch)

La branche [`jenkins-shared-lib`](https://github.com/m-bengueddache/jenkins-cicd-pipeline/tree/jenkins-shared-lib) représente l'étape suivante du projet : les étapes de build et push Docker sont extraites du `Jenkinsfile` et déléguées à une [Jenkins Shared Library](https://github.com/m-bengueddache/jenkins-groovy-shared-library) externe.

**Avant (inline dans le Jenkinsfile) :**
```groovy
sh "docker build -t mb938/demo-app:${IMAGE_NAME} ."
sh 'echo $PASS | docker login -u $USER --password-stdin'
sh "docker push mb938/demo-app:${IMAGE_NAME}"
```

**Après (via la Shared Library) :**
```groovy
buildImage 'mb938/demo-app:jma-3.0'
dockerLogin()
dockerPush 'mb938/demo-app:jma-3.0'
```

La librairie est chargée directement dans le `Jenkinsfile` sans configuration Jenkins admin requise :
```groovy
library identifier: 'jenkins-groovy-shared-library@master', retriever: modernSCM(
    [$class: 'GitSCMSource',
    remote: 'https://github.com/m-bengueddache/jenkins-groovy-shared-library.git',
    credentialsId: 'git-credentials']
)
```

## EN — Evolution: Shared Library (`jenkins-shared-lib` branch)

The [`jenkins-shared-lib`](https://github.com/m-bengueddache/jenkins-cicd-pipeline/tree/jenkins-shared-lib) branch represents the next step of the project: Docker build and push steps are extracted from the `Jenkinsfile` and delegated to an external [Jenkins Shared Library](https://github.com/m-bengueddache/jenkins-groovy-shared-library).

**Before (inline in Jenkinsfile):**
```groovy
sh "docker build -t mb938/demo-app:${IMAGE_NAME} ."
sh 'echo $PASS | docker login -u $USER --password-stdin'
sh "docker push mb938/demo-app:${IMAGE_NAME}"
```

**After (via Shared Library):**
```groovy
buildImage 'mb938/demo-app:jma-3.0'
dockerLogin()
dockerPush 'mb938/demo-app:jma-3.0'
```

The library is loaded directly in the `Jenkinsfile` with no Jenkins admin configuration required:
```groovy
library identifier: 'jenkins-groovy-shared-library@master', retriever: modernSCM(
    [$class: 'GitSCMSource',
    remote: 'https://github.com/m-bengueddache/jenkins-groovy-shared-library.git',
    credentialsId: 'git-credentials']
)
```

---

## FR — Prérequis Jenkins

| Credential ID | Type | Usage |
|---|---|---|
| `dockerhub-credentials` | Username/Password | Push vers Docker Hub |
| `git-credentials` | Username/Password | Push vers GitHub |

Maven doit être configuré dans Jenkins : **Manage Jenkins → Tools → Maven installations** avec le nom `maven`.

## EN — Jenkins Prerequisites

| Credential ID | Type | Usage |
|---|---|---|
| `dockerhub-credentials` | Username/Password | Push to Docker Hub |
| `git-credentials` | Username/Password | Push to GitHub |

Maven must be configured in Jenkins: **Manage Jenkins → Tools → Maven installations** with the name `maven`.

---

## Project Structure

```
.
├── Jenkinsfile             # Declarative pipeline
├── Dockerfile              # Java app image (Amazon Corretto 8)
├── script.groovy           # Reusable Groovy functions
├── freestyle-build.sh      # Shell script for freestyle job reference
├── pom.xml                 # Maven project (Spring Boot 3)
└── src/                    # Java application source
```
