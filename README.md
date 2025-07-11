# Projet DevSecOps – Orchestration Multi-Applications avec Docker, Nginx et Reverse Proxy

## Auteur : Reda ZITOUNI / E5 API

Date : 11 juillet 2025\
École : ESTIAM Paris\
Module : E5 - DevSecOps

---

## Contexte du projet

Dans le cadre du module DevSecOps, ce projet a pour but de mettre en pratique les compétences en conteneurisation, déploiement multi-services et mise en place d’un reverse proxy. L’objectif principal étant de comprendre comment déployer plusieurs applications web dans un même environnement Docker de façon efficace et sécurisée.

---

## Objectifs pédagogiques

- Isoler chaque application dans un conteneur distinct
- Utiliser un `Dockerfile` pour chaque application
- Gérer l’ensemble via `docker-compose.yml`
- Intégrer Nginx comme reverse proxy pour centraliser les accès
- Documenter l’infrastructure technique de manière claire et concise

---

## Structure du projet

Quatre applications web ont été choisies afin de démontrer la diversité des technologies utilisées :

1. **fastapi-backend** : application backend avec FastAPI
2. **vue-frontend** : interface utilisateur en Vue.js
3. **express-api** : API REST Node.js avec Express
4. **spring-taskapp** : application de gestion de tâches en Spring Boot

Fichiers ajoutés à la racine du projet :

- `docker-compose.yml`
- `nginx.conf`
- `README.md`

Structure finale :

```
projet-devsecops/
 ├ fastapi-backend/
 ├ vue-frontend/
 ├ express-api/
 ├ spring-taskapp/
 ├ docker-compose.yml
 ├ nginx.conf
 └ README.md
```

---

## Dockerisation des applications

### Exemple FastAPI :

```dockerfile
FROM python:3.11-slim
WORKDIR /app
COPY . .
RUN pip install -r requirements.txt
EXPOSE 8000
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
```

### Exemple Vue.js :

```dockerfile
FROM node:18-alpine
WORKDIR /app
COPY . .
RUN npm install && npm run build
EXPOSE 8080
CMD ["npm", "run", "dev"]
```

### Exemple Express :

```dockerfile
FROM node:18-alpine
WORKDIR /app
COPY . .
RUN npm install
EXPOSE 4000
CMD ["node", "server.js"]
```

### Exemple Spring Boot :

```dockerfile
FROM openjdk:17-jdk-slim
COPY target/taskapp.jar app.jar
EXPOSE 8081
ENTRYPOINT ["java", "-jar", "app.jar"]
```

---

## Orchestration via Docker Compose

Le fichier `docker-compose.yml` contient tous les services :

```yaml
services:
  fastapi:
    build:
      context: ./fastapi-backend
    expose:
      - "8000"

  vue:
    build:
      context: ./vue-frontend
    expose:
      - "8080"

  express:
    build:
      context: ./express-api
    expose:
      - "4000"

  spring:
    build:
      context: ./spring-taskapp
    expose:
      - "8081"

  nginx:
    image: nginx:latest
    ports:
      - "80:80"
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf
```

---

## Reverse Proxy avec Nginx

Le reverse proxy permet d’accéder aux applications via des chemins dédiés :

```nginx
server {
    listen 80;

    location /fastapi/ {
        proxy_pass http://fastapi:8000;
    }

    location /vue/ {
        proxy_pass http://vue:8080;
    }

    location /express/ {
        proxy_pass http://express:4000;
    }

    location /spring/ {
        proxy_pass http://spring:8081;
    }
}
```

---

## Lancement de l’environnement

```bash
docker-compose build
docker-compose up -d
docker ps
```

---

## Tests d’accès

- ✅ `http://localhost/fastapi/`
- ✅ `http://localhost/vue/`
- ✅ `http://localhost/express/`
- ✅ `http://localhost/spring/`

---

## Redéploiement et logs

Pour redéployer après modification :

```bash
docker-compose down
docker-compose up --build -d
```

Consulter les logs :

```bash
docker-compose logs -f
```

---

## Dépôt GitHub

```bash
git init
git remote add origin https://github.com/MONUTILISATEUR/projet-devsecops.git
git add .
git commit -m "Initial commit"
git push -u origin main
```

---

## Équipe projet

| Membre         | Rôle                             |
| -------------- | -------------------------------- |
| Reda ZITOUNI   | Chef de projet / Docker Compose  |
| Lina BOUZIANE  | Déploiement / Reverse Proxy      |
| Karim SEDDIKI  | CI/CD / Optimisations            |
| Omar EL KHADIR | Tests / Qualité / Monitoring     |
| Sarah AZZOUZ   | Documentation / Relecture finale |

---

## Schéma d’architecture

```
        [ Utilisateur ]
              |
              v
        [ Nginx Reverse Proxy ]
        /    |     |     \
 /fastapi/ /vue/ /express/ /spring/
     |       |       |        |
  FastAPI  Vue.js  Express  Spring Boot
```

---

## Résultat Final

- 4 applications déployées dans des conteneurs isolés
- Orchestration via Docker Compose
- Accès centralisé grâce à Nginx
- Projet documenté et versionné sur GitHub

---

© 2025 – Reda ZITOUNI – ESTIAM Paris

