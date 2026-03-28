# 🐳 TP 15 — Conteneurisation d'une Application Spring Boot avec Docker

> **Cours** : Développement JakartaEE / Spring Boot  
> **Objectif** : Déployer une application Spring Boot avec une base de données MySQL dans des conteneurs Docker orchestrés par Docker Compose.

---

## 🛠️ Technologies utilisées

| Technologie | Version | Rôle |
|---|---|---|
| Java | 17 | Langage de l'application |
| Spring Boot | 3.5.x | Framework backend |
| Maven | 3.x | Gestionnaire de build |
| Docker | Desktop | Conteneurisation |
| MySQL | 8.0 | Base de données |
| Docker Compose | 3.8 | Orchestration multi-conteneurs |

---

## 📁 Structure du projet

```
springdocker/
├── src/
│   └── main/
│       └── resources/
│           └── application.properties
├── target/
│   └── springdocker-0.0.1-SNAPSHOT.jar
├── Dockerfile
├── docker-compose.yml
└── pom.xml
```

---

## ⚙️ Configuration — `application.properties`

```properties
spring.datasource.url=jdbc:mysql://mysql-db:3306/demo_db?createDatabaseIfNotExist=true
spring.datasource.username=root
spring.datasource.password=1234
spring.jpa.hibernate.ddl-auto=update
server.port=8080
```

> 💡 `mysql-db` est le nom du conteneur MySQL — les conteneurs Docker communiquent entre eux via leur nom de service.

---

## 🐋 Dockerfile

```dockerfile
# Image de base Java 17
FROM eclipse-temurin:17-jdk-jammy

# Répertoire de travail dans le conteneur
WORKDIR /app

# Copier le JAR généré dans le conteneur
COPY target/springdocker-0.0.1-SNAPSHOT.jar app.jar

# Port exposé par l'application
EXPOSE 8080

# Commande de démarrage
ENTRYPOINT ["java", "-jar", "app.jar"]
```

---

## 🔧 Compilation du projet

```bash
./mvnw clean package -DskipTests
```

---

## 🏗️ Construction de l'image Docker

```bash
docker build -t ens/springdocker:1.0 .
```

Vérifier l'image créée :

```bash
docker images
```

---

## 📦 docker-compose.yml

```yaml
version: '3.8'

services:

  mysql-db:
    image: mysql:8.0
    container_name: mysql-db
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: 1234
      MYSQL_DATABASE: demo_db
    ports:
      - "3307:3306"
    volumes:
      - mysql-data:/var/lib/mysql

  spring-app:
    image: ens/springdocker:1.0
    container_name: spring-app
    depends_on:
      - mysql-db
    environment:
      - SPRING_DATASOURCE_URL=jdbc:mysql://mysql-db:3306/demo_db?createDatabaseIfNotExist=true
      - SPRING_DATASOURCE_USERNAME=root
      - SPRING_DATASOURCE_PASSWORD=1234
      - SPRING_JPA_HIBERNATE_DDL_AUTO=update
    ports:
      - "8090:8080"

volumes:
  mysql-data:
```

---

## 🚀 Démarrage de l'environnement

```bash
# Démarrer tous les conteneurs
docker-compose up -d

# Vérifier les conteneurs actifs
docker ps

# Afficher les logs en temps réel
docker-compose logs -f

# Arrêter l'environnement
docker-compose down
```

---

## ✅ Résultat final

### Conteneurs actifs dans Docker Desktop

<img width="1920" height="1080" alt="Screenshot (1228)" src="https://github.com/user-attachments/assets/edb638fa-af24-4af3-9a3a-09d93a5ddef5" />


### Images Docker disponibles

<img width="1920" height="1080" alt="Screenshot (1229)" src="https://github.com/user-attachments/assets/75cbeed9-ed23-46f7-8c75-0b16cab94c48" />


> - 🟢 **mysql** `8.0` — 1.08 GB
> - 🟢 **ens/springdocker** `1.0` — 732.51 MB

### Application accessible

```
http://localhost:8090
```

---

## 🧠 Concepts clés appris

| Concept | Description |
|---|---|
| **Image Docker** | Modèle figé contenant l'application et ses dépendances |
| **Conteneur** | Instance en cours d'exécution d'une image |
| **Dockerfile** | Script de construction d'une image |
| **Docker Compose** | Outil d'orchestration de plusieurs conteneurs |
| **Volume** | Persistance des données MySQL entre redémarrages |
| **Réseau Docker** | Communication entre conteneurs via leur nom de service |

---

## 📌 Commandes essentielles

```bash
# Construire une image
docker build -t nom:tag .

# Lancer un conteneur
docker run -d -p 8090:8080 --name mon-app nom:tag

# Voir les conteneurs actifs
docker ps

# Voir les logs
docker logs -f mon-app

# Arrêter et supprimer un conteneur
docker stop mon-app && docker rm mon-app

# Docker Compose — démarrer
docker-compose up -d

# Docker Compose — arrêter
docker-compose down
```

---

## 👤 Auteur

AIT LECHGUEUR BASMA
