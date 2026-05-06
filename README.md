Fichier README.md

# The Bee Guardian

## 1. Présentation du projet
The Bee Guardian" est une application API-First robuste dédiée à la surveillance et à la gestion de flux de données. Le projet met en œuvre une architecture organisée en couches, intégrant API Platform pour l'exposition des ressources, Docker pour l'orchestration de services et une suite de tests automatisés (PHPUnit) garantissant une haute fiabilité logicielle.

## 2. Architecture Technique
- **Backend :** PHP 8.x / Symfony 7
- **Frontend :** Twig / JavaScript (ES6)
- **Base de données :** MySQL 8.0
- **Conteneurisation :** Docker / Docker Compose

## 3. Installation et Déploiement (Docker)
L'application est entièrement conteneurisée pour garantir la parité des environnements.

### Prérequis
- Docker Desktop
- Git

### Procédure
1. Cloner le dépôt : `git clone https://github.com/renaulaury/the-bee-guardian.git`
2. Configurer les variables d'environnement : `cp .env .env.local`
3. Construire et démarrer les conteneurs : `docker-compose up -d --build`
4. Installer les dépendances : `docker-compose exec php composer install`
5. Initialiser la base de données : 
   `docker-compose exec php bin/console doctrine:migrations:migrate --no-interaction`

## 4. Qualité et Tests
L'intégrité des composants métier est vérifiée via PHPUnit :
`docker-compose exec php bin/phpunit`

Configuration Docker (compose.yaml)

services:
  db:
    image: mysql:8.0
    container_name: bee_guardian_db
    restart: always
    environment:
      MYSQL_DATABASE: bee_guardian
      MYSQL_ROOT_PASSWORD: ${MYSQL_ROOT_PASSWORD}
    volumes:
      - db_data:/var/lib/mysql

  php:
    build:
      context: .
      dockerfile: Dockerfile
    container_name: bee_guardian_php
    volumes:
      - .:/var/www/html
    environment:
      DATABASE_URL: "mysql://root:${MYSQL_ROOT_PASSWORD}@db:3306/bee_guardian?serverVersion=8.0"
    depends_on:
      - db

  nginx:
    image: nginx:alpine
    container_name: bee_guardian_nginx
    ports:
      - "8080:80"
    volumes:
      - .:/var/www/html
      - ./docker/nginx/default.conf:/etc/nginx/conf.d/default.conf
    depends_on:
      - php

volumes:
  db_data:

