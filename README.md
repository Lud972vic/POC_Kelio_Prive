<div align="center">

# Kelio Data Explorer & Importer

Exploration, audit et import des données Kelio (salariés, absences, badgeages, organigramme) dans une base locale Symfony.

[![PHP](https://img.shields.io/badge/PHP-%3E%3D%208.4-777BB4?logo=php)](https://www.php.net/)
[![Symfony](https://img.shields.io/badge/Symfony-8.0-000000?logo=symfony)](https://symfony.com/)
[![Doctrine](https://img.shields.io/badge/ORM-Doctrine-59666C?logo=doctrine)](https://www.doctrine-project.org/)
[![Build](https://img.shields.io/badge/build-local-green)](#tests)
[![License](https://img.shields.io/badge/license-proprietary-red)](#licence)

</div>

---

## Résumé

Ce projet est une application Symfony permettant :

- d’interroger les Web Services Kelio (sandbox) pour récupérer des données de ressources humaines ;
- de visualiser ces données dans une interface web ergonomique (salariés, absences, badgeages, organigramme) ;
- de copier les données sélectionnées dans une base MySQL locale pour analyse, audit ou prototypage ;
- de construire des requêtes avancées sur les données importées via une interface graphique.

Il s’agit d’un **POC orienté métier RH** qui illustre l’intégration de Kelio dans un écosystème PHP/Symfony moderne.

---

## Table des matières

- [Résumé](#résumé)
- [Fonctionnalités](#fonctionnalités)
- [Architecture & structure du projet](#architecture--structure-du-projet)
- [Technologies utilisées](#technologies-utilisées)
- [Prérequis](#prérequis)
- [Installation](#installation)
- [Exemples d’utilisation](#exemples-dutilisation)
  - [Interface web](#interface-web)
  - [API d’import des absences](#api-dimport-des-absences)
- [Déploiement](#déploiement)
- [Tests](#tests)
- [Captures d’écran & diagrammes](#captures-décran--diagrammes)
- [Contributeurs](#contributeurs)
- [Licence](#licence)
- [Contact](#contact)

---

## Fonctionnalités

- **Exploration des salariés Kelio**
  - Chargement de la liste des salariés via les Web Services Kelio.
  - Filtre par matricule, code badge, nom/prénom ou clé technique.
  - Pagination et export CSV.
  - Copie des salariés en base locale (`Employee`).

- **Gestion des absences**
  - Formulaire de filtre avancé (population, groupe, période, salarié).
  - Chargement des dossiers d’absence depuis Kelio.
  - Copie en base locale dans l’entité `Absence` avec logique d’upsert basée sur `absenceFileKey`.

- **Gestion des badgeages**
  - Interrogation des Web Services de pointage.
  - Visualisation des pointages (date, heure, type, terminal, etc.).
  - Copie en base locale dans l’entité `Clocking`.

- **Organigramme**
  - Récupération de la structure d’organisation (société, établissement, service, niveaux).
  - Stockage dans `OrganizationChartLevel` pour analyses ultérieures.

- **Requêtes avancées**
  - Interface graphique pour construire des requêtes sur les données importées.
  - Sélection des tables, champs, conditions et limites de résultats.
  - Exécution et prévisualisation des résultats, avec possibilité d’export.
  - Sauvegarde et rechargement de requêtes prédéfinies (`SavedQuery`).

- **Documentation intégrée**
  - Page de documentation fonctionnelle et technique accessible depuis l’interface.
  - Explication des principaux flux (salariés, absences, badgeages, organigramme, requêtes avancées).

---

## Architecture & structure du projet

Le projet suit les conventions standard d’un **projet Symfony 8** avec Doctrine ORM et asset-mapper.

### Arborescence principale

```bash
app/
├─ assets/                 # Frontend (JS, Stimulus, CSS)
│  ├─ app.js               # Logique principale du POC (UI + appels Ajax)
│  ├─ styles/app.css       # Styles spécifiques à l’application
│  └─ controllers/         # Contrôleurs Stimulus
├─ bin/
│  ├─ console              # Console Symfony
│  └─ phpunit              # Lanceur de tests
├─ config/
│  ├─ packages/            # Configuration des bundles (doctrine, twig, monolog, etc.)
│  ├─ routes/              # Configuration des routes
│  ├─ services.yaml        # Déclaration des services
│  └─ routes.yaml          # Routes principales
├─ migrations/             # Migrations Doctrine (création des tables Employee, Absence, etc.)
├─ public/
│  ├─ index.php            # Front controller
│  ├─ css/                 # CSS tiers (Bootstrap)
│  └─ images/              # Ressources graphiques
├─ src/
│  ├─ Controller/          # Contrôleurs HTTP
│  │  ├─ AccueilController.php
│  │  ├─ KelioServicesController.php
│  │  ├─ KelioEmployeeImportController.php
│  │  ├─ KelioAbsenceImportController.php
│  │  ├─ KelioClockingImportController.php
│  │  ├─ KelioOrganizationImportController.php
│  │  └─ AdvancedQueryController.php
│  ├─ Entity/              # Entités Doctrine (Employee, Absence, Clocking, etc.)
│  ├─ Repository/          # Repositories Doctrine
│  └─ Service/             # Services métier (clients Kelio, définition des requêtes avancées)
├─ templates/
│  ├─ base.html.twig       # Layout principal
│  ├─ accueil/             # Page d’accueil
│  └─ kelio/               # Pages de services et requêtes avancées
├─ tests/
│  └─ Controller/          # Tests fonctionnels et d’intégration
└─ composer.json           # Dépendances PHP
```

---

## Technologies utilisées

- **Backend**
  - PHP >= 8.4
  - Symfony 8 (Framework, HTTP Kernel, Routing, Security, Validator, Serializer…)
  - Doctrine ORM & Doctrine Migrations

- **Frontend**
  - Symfony Asset Mapper + Importmap
  - Stimulus & Turbo (UX)
  - Bootstrap (CSS)
  - JavaScript vanilla pour la logique de POC (`assets/app.js`)

- **Tests & qualité**
  - PHPUnit
  - Symfony BrowserKit & CssSelector

---

## Prérequis

- PHP **8.4** ou supérieur
- Composer
- Serveur de base de données compatible Doctrine (MySQL/MariaDB recommandé)
- Extensions PHP: `ext-ctype`, `ext-iconv`
- Accès aux Web Services **Kelio sandbox** (identifiants API)

Optionnel:

- Outil `symfony` CLI pour le serveur local

---

## Installation

1. **Cloner le dépôt**

```bash
git clone <URL_DU_DEPOT_GITHUB> kelio-app
cd kelio-app
```

2. **Installer les dépendances PHP**

```bash
composer install
```

3. **Configurer les variables d’environnement**

Copier le fichier `.env` vers `.env.local` et adapter au besoin:

```bash
cp .env .env.local
```

Configurer notamment:

```dotenv
APP_ENV=dev
DATABASE_URL="mysql://user:password@127.0.0.1:3306/kelio"
```

4. **Créer la base de données et appliquer les migrations**

```bash
php bin/console doctrine:database:create
php bin/console doctrine:migrations:migrate
```

5. **Compiler les assets frontend**

```bash
php bin/console importmap:install   # si nécessaire
php bin/console asset-map:compile
```

En production, pensez à définir l’environnement:

```bash
APP_ENV=prod php bin/console asset-map:compile
```

6. **Démarrer le serveur de développement**

Avec PHP natif:

```bash
php -S 127.0.0.1:8000 -t public
```

ou avec la CLI Symfony:

```bash
symfony server:start -d
```

L’application sera accessible sur `http://127.0.0.1:8000`.

---

## Exemples d’utilisation

### Interface web

1. Rendez-vous sur la page d’accueil (`/`) pour saisir les identifiants Kelio (sandbox).
2. Accédez au menu **Services salariés** pour:
   - charger la liste des salariés,
   - filtrer les résultats,
   - paginer les listes,
   - copier les données en base locale.
3. Accédez au menu **Requêtes avancées** pour construire des requêtes sur les données importées.

### API d’import des absences

L’API d’import des absences est exposée sur:

```http
POST /kelio/absences/import
Content-Type: application/json
X-Requested-With: XMLHttpRequest
```

Exemple de payload:

```json
{
  "items": [
    {
      "absenceFileKey": 123,
      "startDate": "2024-01-01",
      "endDate": "2024-01-02",
      "absenceTypeAbbreviation": "CP",
      "employeeIdentificationNumber": "00001065"
    }
  ]
}
```

Comportement:

- si `absenceFileKey` existe déjà en base → mise à jour de l’enregistrement ;
- sinon → création d’une nouvelle absence.

Réponse typique:

```json
{
  "success": true,
  "imported": 1
}
```

En cas d’erreur BDD (connexion, contrainte, etc.), la réponse contient un message explicite:

```json
{
  "success": false,
  "message": "Erreur lors de la copie des absences en base de données."
}
```

---

## Déploiement

Quelques bonnes pratiques pour le déploiement en **production**:

1. Installer les dépendances sans les paquets de dev:

```bash
composer install --no-dev --optimize-autoloader
```

2. Exécuter les migrations:

```bash
APP_ENV=prod php bin/console doctrine:migrations:migrate --no-interaction
```

3. Compiler les assets frontend:

```bash
APP_ENV=prod php bin/console asset-map:compile
```

Cette commande génère les fichiers dans `public/assets` (JS, CSS, Stimulus, Turbo, etc.) ainsi que le manifeste utilisé par l’application.

4. Vider et préchauffer le cache:

```bash
APP_ENV=prod php bin/console cache:clear
APP_ENV=prod php bin/console cache:warmup
```

---

## Tests

Les tests sont basés sur **PHPUnit** et les outils de test Symfony.

### Préparer la base de tests

1. Créer la base de tests:

```bash
php bin/console doctrine:database:create --env=test
```

2. Appliquer les migrations dans cette base:

```bash
php bin/console doctrine:migrations:migrate --env=test
```

### Lancer la suite de tests

```bash
php bin/phpunit
```

Les tests couvrent notamment:

- l’intégration avec les Web Services Kelio (via des doubles de test) ;
- l’import et l’upsert des absences en base ;
- le comportement de l’API côté contrôleurs.

---

## Captures d’écran & diagrammes

Vous pouvez ajouter ici des captures d’écran de l’interface ou des diagrammes d’architecture.

Exemples (à adapter selon le dépôt GitHub) :

```markdown

```

Pour un README GitHub, il est recommandé de placer les images dans un dossier `docs/` ou `public/` et d’utiliser des chemins relatifs.

---

## Contributeurs

- **Lud972vic** – Développement backend & frontend, intégration Kelio, conception fonctionnelle
- Autres contributeurs internes – Relecture fonctionnelle, scénarios de test métier

N’hésitez pas à compléter cette section avec les noms, rôles et contacts des contributeurs.

---

## Licence

Ce projet est distribué sous une licence **propriétaire**.

- Le code source ne peut être utilisé, copié ou diffusé qu’avec l’autorisation explicite du détenteur des droits.
- Pour toute question relative aux droits d’utilisation, merci de consulter le responsable du projet.

---

## Contact

Pour toute question, suggestion ou demande de support :

- Responsable projet / technique : **Lud972vic**
