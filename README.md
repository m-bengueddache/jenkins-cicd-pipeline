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

- Using `readFile()` and Groovy regex to extract version from XML
- Secure credentials management in Jenkins (`withCredentials`)
- Avoiding infinite trigger loops (Jenkins commit → build → commit → ...)
- Groovy scripting in Jenkinsfile for conditional logic

---

## FR — Évolution : Shared Library (`jenkins-shared-lib` branch)

La branche [`jenkins-shared-lib`](https://github.com/m-bengueddache/jenkins-cicd-pipeline/tree/jenkins-shared-lib) extrait les étapes de build et push Docker du `Jenkinsfile` vers une [Jenkins Shared Library](https://github.com/m-bengueddache/jenkins-groovy-shared-library) externe. Les appels `buildImage()`, `dockerLogin()`, `dockerPush()` remplacent les commandes shell inline — la librairie est chargée directement dans le `Jenkinsfile` sans configuration Jenkins admin requise.

## EN — Evolution: Shared Library (`jenkins-shared-lib` branch)

The [`jenkins-shared-lib`](https://github.com/m-bengueddache/jenkins-cicd-pipeline/tree/jenkins-shared-lib) branch extracts Docker build and push steps from the `Jenkinsfile` into an external [Jenkins Shared Library](https://github.com/m-bengueddache/jenkins-groovy-shared-library). Calls to `buildImage()`, `dockerLogin()`, `dockerPush()` replace inline shell commands — the library is loaded directly in the `Jenkinsfile` with no Jenkins admin configuration required.

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
