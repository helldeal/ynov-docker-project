# Projet Final - Stack Spring Boot / Frontend JS / PostgreSQL

`Alexandre CLENET / Melvin SIMON`

## 🧩 1. Architecture Globale

Ce projet implémente une stack complète prête pour la production.
Il repose sur une architecture frontend / reverse proxy / backend / base de données entièrement orchestrée via Docker Compose.

### 🔷 Schéma d’architecture
```yml
                   ┌──────────────────────────────────┐
                   │            Utilisateur           │
                   └────────────────┬─────────────────┘
                                    │
                              HTTP / Port 80
                                    │
                         ┌──────────▼──────────┐
                         │   Reverse Proxy     │  (Nginx)
                         │  (serveur unique)   │
                         └───────┬─────────────┘
                /api/*           │           /
Backend (Spring Boot)      Frontend (React)
   port interne 8080        dist/ via Nginx
         │                       │
         └──────────┬────────────┘
                    │
             PostgreSQL Database
               port interne 5432
```
### 🔶 Rôle des services
| Service           | Rôle                                                                           |
| ----------------- | ------------------------------------------------------------------------------ |
| **reverse-proxy** | Serveur frontal unique. Sert le frontend et redirige `/api/**` vers le backend |
| **webapp**        | Frontend React buildé en production, servi par Nginx                           |
| **spring-api**    | API REST Spring Boot + accès PostgreSQL                                        |
| **db**            | Base PostgreSQL avec volume persistant                                         |

## 🧩 2. Commandes pour Builder & Lancer
### ▶️ Lancer tout le projet
```bash
docker-compose up --build
```
### 🛑 Arrêter
```bash
docker-compose down
```
### 🔥 Rebuild complet
```bash
docker-compose down -v
docker-compose up --build
```

## 🧩 3. URLs Principales
| Élément                | URL                                                                            |
| ---------------------- | ------------------------------------------------------------------------------ |
| **Frontend**           | [http://localhost](http://localhost)                                           |
| **API racine**         | [http://localhost/api](http://localhost/api)                                   |

## 🧩 4. Endpoints API
| Méthode | Endpoint          | Description        |
| ------- | ----------------- | ------------------ |
| GET     | `/api/items`      | Liste des items    |
| POST    | `/api/items`      | Ajoute un item     |
| GET     | `/api/health`     | Health Check       |

## 🧩 5. Problèmes Rencontrés & Solutions

### ⚠️ Problème 1 — Conflit de port 80 (Apache)

Symptôme :
```perl
failed to bind host port 80: address already in use
```
Cause : Apache tournait sur la machine hôte.

Solution :
```bash
docker-compose down -v
docker-compose up --build
```

### ⚠️ Problème 2 — Backend inaccessible depuis le frontend (CORS)

Symptôme :
```perl
CORS header ‘Access-Control-Allow-Origin’ missing
```
Cause : Le frontend appelait http://localhost:8080 directement → origine différente.

Solution :
Utilisation de http://localhost dans api.js

### ⚠️ Problème 3 — Backend ne se connecte pas à PostgreSQL

Symptôme :
```perl
Connection to localhost:5432 refused
```
Cause : Spring Boot cherchait la DB sur localhost au lieu du conteneur Docker.

Solution :
Correction des variables d'environnement 