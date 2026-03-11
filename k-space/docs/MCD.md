# K-SPACE — MCD (Modèle Conceptuel des Données)

## ENTITÉS

### USER
- id_user
- username
- email
- password_hash
- avatar_url
- created_at

### DRAMA
- id_drama
- id_genre (FK → GENRE)
- title
- description
- release_year
- episode_count
- poster_url
- created_at

### GENRE
- id_genre
- name
- description
- created_at

### COMMUNITY
- id_community
- name
- description
- drama_related
- created_at

### MESSAGE
- id_message
- id_community (FK → COMMUNITY)
- id_user (FK → USER)
- content
- created_at

### RATING
- id_rating
- id_user (FK → USER)
- id_drama (FK → DRAMA)
- score
- review_text
- created_at

### RECOMMENDATION
- id_recommendation
- id_user (FK → USER)
- id_drama (FK → DRAMA)
- reason
- created_at

## RELATIONS

### 1. BELONGS_TO (DRAMA → GENRE)
**Cardinalité:** (1,1) DRAMA — BELONGS_TO — (0,n) GENRE
- Un drama appartient à exactement un genre
- Un genre peut contenir plusieurs dramas

### 2. WATCHES (USER ↔ DRAMA)
**Cardinalité:** (0,n) USER — WATCHES — (0,n) DRAMA
- Table associative: USER_DRAMA
- Attributs: watched_at, status (watching/completed/dropped)

### 3. RATES (USER ↔ DRAMA)
**Cardinalité:** (0,n) USER — RATES — (0,n) DRAMA
- Devient la table RATING
- Attributs: score, review_text, created_at

### 4. JOINS (USER ↔ COMMUNITY)
**Cardinalité:** (0,n) USER — JOINS — (0,n) COMMUNITY
- Table associative: COMMUNITY_MEMBER
- Attributs: joined_at, role (member/moderator/admin)

### 5. POSTS (USER → MESSAGE)
**Cardinalité:** (1,1) MESSAGE — POSTED_BY — (0,n) USER
- Un message est posté par exactement un utilisateur
- Un utilisateur peut poster plusieurs messages

### 6. CONTAINS (COMMUNITY → MESSAGE)
**Cardinalité:** (1,1) MESSAGE — BELONGS_TO — (0,n) COMMUNITY
- Un message appartient à exactement une communauté
- Une communauté contient plusieurs messages

### 7. RECEIVES (USER → RECOMMENDATION)
**Cardinalité:** (1,1) RECOMMENDATION — FOR — (0,n) USER
- Une recommandation est pour exactement un utilisateur
- Un utilisateur peut recevoir plusieurs recommandations

### 8. SUGGESTS (RECOMMENDATION → DRAMA)
**Cardinalité:** (1,1) RECOMMENDATION — SUGGESTS — (0,n) DRAMA
- Une recommandation suggère exactement un drama
- Un drama peut être suggéré dans plusieurs recommandations

## REPRÉSENTATION TEXTUELLE

```
[USER] --0,n--(WATCHES)--0,n-- [DRAMA]
  |                                |
 0,n                              1,1
  |                                |
[RATING]                    [BELONGS_TO]
  |                                |
 0,n                              0,n
  |                                |
[DRAMA]                         [GENRE]

[USER] --0,n--(JOINS)--0,n-- [COMMUNITY]
  |                                |
 0,n                              0,n
  |                                |
[MESSAGE] --1,1-- (POSTED_BY)     |
  |                                |
 1,1                               |
  |                                |
(BELONGS_TO) ----------------------+

[USER] --0,n--(RECEIVES)--1,1-- [RECOMMENDATION] --1,1--(SUGGESTS)--0,n-- [DRAMA]
```
