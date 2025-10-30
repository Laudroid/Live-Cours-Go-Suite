# Cours Langage Go Avance

## Objectifs de la formation

- Comprendre la Persistance des données et bases de données en Go : stocker et récupérer des données dans une base relationnelle, utiliser un ORM ou la bibliothèque SQL standard.
- Comprendre la concurrence et le Web temps réel : maîtriser goroutines et channels, découvrir la gestion du Web temps réel avec Go.
- Comprendre la Sécurité, structuration avancée et bonnes pratiques Web : notions essentielles de sécurité, appliquer des patterns de structuration robustes.
## Séance 1 : Persistance des données et bases de données en Go

**Durée :** 3 heures

### Objectifs pédagogiques

- Comprendre comment stocker et récupérer des données dans une base relationnelle avec Go.
- Utiliser un ORM simple ou la bibliothèque SQL standard.
### Contenus

#### Introduction à la persistance des données

- Présentation des bases de données relationnelles et non relationnelles.
- Pourquoi et comment stocker les données en Go.
#### Utilisation de la bibliothèque SQL standard

- Connexion à une base de données relationnelle (ex : PostgreSQL).
- Executer des requêtes SQL (SELECT, INSERT, UPDATE, DELETE).
- Gestion des erreurs et sécurité dans les requêtes.
#### Utilisation d'un ORM simple en Go

- Présentation d'un ORM Go (ex : GORM).
- Mise en place et configuration d'un ORM.
- Création et manipulation des entités avec l'ORM.
## Séance 2 : Concurrence et Web temps réel

**Durée :** 3 heures

### Objectifs pédagogiques

- Comprendre la puissance des goroutines et channels pour la gestion d’opérations simultanées.
- Découvrir comment Go peut gérer du Web temps réel (ex : notifications ou chat simple).
### Contenus

#### Introduction à la concurrence en Go

- Principe des goroutines et channels.
- Synchronisation et communication entre goroutines.
#### Gestion des opérations simultanées

- Utiliser les goroutines pour lancer des tâches.
- Communication sécurisée entre goroutines avec channels.
#### Go pour le Web temps réel

- Notions de Websocket et implémentation simple en Go.
- Exemple d'application temps réel : chat ou notifications.
## Séance 3 : Sécurité, structuration avancée et bonnes pratiques Web

**Durée :** 3 heures

### Objectifs pédagogiques

- Introduire les notions essentielles de sécurité dans une application web Go.
- Appliquer des patterns de structuration pour des projets plus robustes.
### Contenus

#### Notions essentielles de sécurité

- Gestion des authentifications et autorisations.
- Sécurisation des entrées utilisateur (validation et sanitation).
- Prévention des attaques courantes (XSS, CSRF, injection SQL).
#### Patterns de structuration avancés

- Architecture hexagonale et clean architecture en Go.
- Organisation du code pour la maintenabilité.
- Gestion des dépendances et tests unitaires.