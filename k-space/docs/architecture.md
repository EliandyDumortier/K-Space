# K-SPACE — ARCHITECTURE DU PROJET

## Vue d'ensemble

K-Space est une plateforme sociale pour fans de K-Dramas qui suit le schéma d'architecture **Simplon Dev IA** avec 3 blocs principaux:

1. **BLOC 1 — DATA**: Collection, stockage et exposition des données
2. **BLOC 2 — AI**: Modèles d'intelligence artificielle et recommandations
3. **BLOC 3 — APPLICATION**: Application web intégrant les services IA

---

## BLOC 1 — DATA
**Réaliser la collecte, le stockage et la mise à disposition des données**

### 1.1 Extraction et Agrégation de Données (C1, C2, C3)

**Sources de données:**
- Web Scraping: MyDramaList, IMDB, sites de K-Dramas
- APIs externes: The Movie Database (TMDB), drama databases
- Fichiers CSV: historiques utilisateurs, ratings
- Bases de données existantes

**Pipeline de traitement:**
```
Sources Externes → Scrapers → Data Cleaning → Normalisation → PostgreSQL
```

**Technologies:**
- Python (BeautifulSoup, Scrapy pour scraping)
- Pandas (nettoyage et transformation)
- Requests (appels API)

**Localisation dans le projet:**
```
data_pipeline/
├── scrapers/
│   ├── mydramalist_scraper.py
│   ├── imdb_scraper.py
│   └── tmdb_api.py
├── processors/
│   ├── clean_data.py
│   └── normalize.py
└── loaders/
    └── db_loader.py
```

### 1.2 Base de Données PostgreSQL (C4)

**Architecture:**
- Hébergement: Supabase (PostgreSQL managé)
- 9 tables principales
- Relations normalisées (3NF)
- Row Level Security (RLS) pour la sécurité

**Tables:**
- users, dramas, genres
- user_dramas, ratings, recommendations
- communities, community_members, messages

**Localisation dans le projet:**
```
database/
├── migrations/
│   ├── 001_create_users.sql
│   ├── 002_create_dramas.sql
│   └── ...
└── seeds/
    ├── genres.sql
    └── sample_dramas.sql
```

### 1.3 API REST Data (C5)

**Endpoints:**
- `GET /dramas` - Liste des K-Dramas
- `GET /dramas/:id` - Détails d'un drama
- `GET /genres` - Liste des genres
- `POST /ratings` - Ajouter une note
- `GET /user/:id/history` - Historique de visionnage

**Technologies:**
- FastAPI (Python)
- SQLAlchemy (ORM)
- Pydantic (validation)

**Localisation dans le projet:**
```
backend/api/
├── data_routes.py
├── queries.py
└── models.py
```

---

## BLOC 2 — AI
**Intégrer des modèles et des services d'intelligence artificielle**

### 2.1 Veille Technique et POC (C6, C7, C8)

**Recherche:**
- Étude des algorithmes de recommandation
- Comparaison: Collaborative Filtering vs Content-Based
- Exploration: Scikit-learn, Surprise, TensorFlow

**Proof of Concept:**
- Prototype avec données de test
- Validation des métriques (RMSE, Precision@K, Recall@K)
- A/B testing sur échantillon d'utilisateurs

### 2.2 Développement du Modèle IA

**Algorithme: Collaborative Filtering**

**Entrées:**
- Matrice user-drama (ratings)
- Historique de visionnage
- Métadonnées dramas (genre, année)

**Processus d'entraînement:**
```python
# 1. Charger les données
user_ratings = load_ratings_from_db()

# 2. Preprocessing
user_item_matrix = create_matrix(user_ratings)

# 3. Split train/test
X_train, X_test = train_test_split(user_item_matrix)

# 4. Entraîner le modèle
model = CollaborativeFiltering()
model.fit(X_train)

# 5. Évaluation
rmse = model.evaluate(X_test)

# 6. Sauvegarder
model.save('models/recommendation_v1.pkl')
```

**Localisation dans le projet:**
```
ai_service/
├── models/
│   ├── collaborative_filtering.py
│   └── content_based.py
├── training/
│   ├── train_model.py
│   └── evaluate.py
└── saved_models/
    └── recommendation_v1.pkl
```

### 2.3 API REST IA (C9)

**Endpoint principal:**
```
POST /api/recommend
{
  "user_id": "uuid",
  "limit": 10
}

Response:
{
  "recommendations": [
    {
      "drama_id": "uuid",
      "score": 0.95,
      "reason": "Based on your love for Romance dramas"
    }
  ]
}
```

**Technologies:**
- FastAPI
- Joblib (chargement modèle)
- NumPy/Pandas

**Localisation dans le projet:**
```
ai_service/api/
├── recommendation_routes.py
└── inference.py
```

### 2.4 Monitoring Modèle IA & CI/CD (C11, C12, C13)

**Suivi des performances:**
- Métriques: Accuracy, RMSE, user satisfaction
- Logs: prédictions, temps de réponse
- Alertes: dégradation de performance

**Pipeline CI/CD:**
```
Code Push → Tests → Retraining → Validation → Deployment
```

**Retraining automatique:**
- Déclenché mensuellement
- Avec nouvelles données utilisateurs
- Versioning des modèles

---

## BLOC 3 — APPLICATION
**Réaliser une application intégrant un service d'intelligence artificielle**

### 3.1 Analyse du Besoin (C14, C15, C16)

**User Stories:**
1. En tant qu'utilisateur, je veux suivre les dramas que j'ai regardés
2. En tant qu'utilisateur, je veux noter les dramas (1-10)
3. En tant qu'utilisateur, je veux recevoir des recommandations personnalisées
4. En tant qu'utilisateur, je veux rejoindre des communautés
5. En tant qu'utilisateur, je veux chatter dans les communautés

**Cadre technique:**
- Frontend: React + TypeScript
- Backend: FastAPI (Python)
- Database: PostgreSQL (Supabase)
- AI Service: Python + Scikit-learn

**Méthodologie Agile:**
- Sprints de 2 semaines
- Daily standups
- Revues de sprint

### 3.2 Application Frontend (C17)

**Technologies:**
- React 18
- TypeScript
- Tailwind CSS
- React Router (navigation)
- React Query (data fetching)

**Pages principales:**
```
/login          - Authentification
/dashboard      - Tableau de bord personnel
/dramas         - Catalogue de dramas
/dramas/:id     - Détails d'un drama
/recommendations - Recommandations IA
/communities    - Liste des communautés
/communities/:id - Chat communauté
/profile        - Profil utilisateur
```

**Localisation dans le projet:**
```
frontend/
├── src/
│   ├── components/
│   │   ├── DramaCard.tsx
│   │   ├── RatingForm.tsx
│   │   └── RecommendationFeed.tsx
│   ├── pages/
│   │   ├── Dashboard.tsx
│   │   ├── DramaCatalog.tsx
│   │   └── Recommendations.tsx
│   ├── services/
│   │   ├── api.ts
│   │   └── auth.ts
│   └── context/
│       └── UserContext.tsx
```

### 3.3 Application Backend (C17)

**Architecture:**
```
Frontend → Backend API → Data API
                       → AI API
                       → PostgreSQL
```

**Responsabilités:**
- Authentification utilisateurs
- Orchestration des appels (Data API + AI API)
- Business logic
- Gestion des sessions

**Endpoints principaux:**
```
POST /auth/login
POST /auth/register
GET  /dramas
POST /dramas/:id/rate
GET  /recommendations/:user_id
POST /communities/:id/messages
```

**Localisation dans le projet:**
```
backend/
├── app/
│   ├── main.py
│   ├── auth/
│   │   └── routes.py
│   ├── routes/
│   │   ├── dramas.py
│   │   ├── ratings.py
│   │   └── communities.py
│   └── services/
│       ├── recommendation_service.py
│       └── drama_service.py
```

### 3.4 CI/CD (C18, C19)

**Pipeline GitHub Actions:**
```yaml
name: Deploy K-Space

on: push

jobs:
  test:
    - Run unit tests
    - Run integration tests

  build:
    - Build Frontend
    - Build Backend Docker image

  deploy:
    - Deploy to staging
    - Run E2E tests
    - Deploy to production
```

**Tests:**
- Unit tests: Jest (Frontend), Pytest (Backend)
- Integration tests: API endpoints
- E2E tests: Playwright

### 3.5 Monitoring & Incident Management (C20, C21)

**Monitoring Application:**
- Sentry: Error tracking
- DataDog: Performance monitoring
- Logs: CloudWatch / Supabase Logs

**Alertes:**
- Slack notifications
- PagerDuty (incidents critiques)

**Métriques suivies:**
- Temps de réponse API
- Taux d'erreur
- Satisfaction utilisateur (NPS)
- Click-through rate des recommandations

---

## FLUX DE DONNÉES COMPLET

```
┌─────────────────┐
│  External APIs  │
│  Web Scraping   │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Data Pipeline  │
│  (Cleaning)     │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│   PostgreSQL    │
│   (Supabase)    │
└────────┬────────┘
         │
    ┌────┴────┐
    │         │
    ▼         ▼
┌─────┐   ┌─────┐
│Data │   │ ML  │
│ API │   │Train│
└──┬──┘   └──┬──┘
   │         │
   │         ▼
   │    ┌─────────┐
   │    │ AI API  │
   │    └────┬────┘
   │         │
   └────┬────┘
        │
        ▼
   ┌─────────┐
   │ Backend │
   │  API    │
   └────┬────┘
        │
        ▼
   ┌──────────┐
   │ Frontend │
   │  React   │
   └──────────┘
        │
        ▼
   ┌──────────┐
   │   User   │
   └──────────┘
```

---

## STRUCTURE DU PROJET

```
k-space/
├── ai_service/              # BLOC 2
│   ├── models/
│   ├── training/
│   ├── inference/
│   └── api/
│
├── backend/                 # BLOC 3
│   ├── app/
│   │   ├── api/            # BLOC 1
│   │   ├── auth/
│   │   ├── routes/
│   │   └── services/
│   └── requirements.txt
│
├── data_pipeline/           # BLOC 1
│   ├── scrapers/
│   ├── processors/
│   └── loaders/
│
├── database/                # BLOC 1
│   ├── migrations/
│   └── seeds/
│
├── frontend/                # BLOC 3
│   ├── src/
│   │   ├── components/
│   │   ├── pages/
│   │   ├── services/
│   │   └── context/
│   └── package.json
│
├── docker/
│   ├── Dockerfile.api
│   ├── Dockerfile.ai
│   └── docker-compose.yml
│
├── tests/
│   ├── unit/
│   ├── integration/
│   └── e2e/
│
└── docs/
    ├── MCD.md
    ├── MLD.md
    ├── MPD.md
    └── architecture.md
```

---

## TECHNOLOGIES RÉSUMÉES

| Composant | Technologie |
|-----------|-------------|
| Frontend | React + TypeScript + Tailwind CSS |
| Backend API | FastAPI (Python) |
| Data API | FastAPI (Python) |
| AI Service | Python + Scikit-learn |
| Database | PostgreSQL (Supabase) |
| Data Pipeline | Python (BeautifulSoup, Pandas) |
| Testing | Jest, Pytest, Playwright |
| CI/CD | GitHub Actions |
| Monitoring | Sentry, DataDog |
| Deployment | Docker, Vercel/Railway |
