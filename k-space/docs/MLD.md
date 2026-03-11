# K-SPACE — MLD (Modèle Logique des Données)

## TABLES

### users
- **id_user** (PK)
- username
- email
- password_hash
- avatar_url
- created_at

### dramas
- **id_drama** (PK)
- **id_genre** (FK → genres)
- title
- description
- release_year
- episode_count
- poster_url
- created_at

### genres
- **id_genre** (PK)
- name
- description
- created_at

### user_dramas *(table associative)*
- **id_user_drama** (PK)
- **id_user** (FK → users)
- **id_drama** (FK → dramas)
- watched_at
- status
- UNIQUE(id_user, id_drama)

### ratings
- **id_rating** (PK)
- **id_user** (FK → users)
- **id_drama** (FK → dramas)
- score
- review_text
- created_at
- UNIQUE(id_user, id_drama)

### communities
- **id_community** (PK)
- name
- description
- drama_related
- created_at

### community_members *(table associative)*
- **id_community_member** (PK)
- **id_user** (FK → users)
- **id_community** (FK → communities)
- joined_at
- role
- UNIQUE(id_user, id_community)

### messages
- **id_message** (PK)
- **id_community** (FK → communities)
- **id_user** (FK → users)
- content
- created_at

### recommendations
- **id_recommendation** (PK)
- **id_user** (FK → users)
- **id_drama** (FK → dramas)
- reason
- created_at

## RÈGLES DE TRANSFORMATION

### Relations 1:N
- Ajout de clé étrangère dans la table côté N
- Exemple: `dramas.id_genre` → `genres.id_genre`

### Relations N:M
- Création de tables associatives
- Exemples:
  - USER ↔ DRAMA → `user_dramas`
  - USER ↔ COMMUNITY → `community_members`

### Contraintes d'intégrité
- PRIMARY KEY sur tous les id_*
- FOREIGN KEY pour maintenir l'intégrité référentielle
- UNIQUE pour éviter les doublons dans les relations N:M
- NOT NULL sur les champs obligatoires
