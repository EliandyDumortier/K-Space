# K-SPACE — MPD (Modèle Physique des Données)

## SCHÉMA POSTGRESQL

```sql
-- ================================================
-- TABLE: USERS
-- ================================================
CREATE TABLE users (
    id_user UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    username VARCHAR(50) UNIQUE NOT NULL,
    email VARCHAR(255) UNIQUE NOT NULL,
    password_hash TEXT NOT NULL,
    avatar_url TEXT,
    created_at TIMESTAMPTZ DEFAULT now()
);

-- ================================================
-- TABLE: GENRES
-- ================================================
CREATE TABLE genres (
    id_genre UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    name VARCHAR(100) UNIQUE NOT NULL,
    description TEXT,
    created_at TIMESTAMPTZ DEFAULT now()
);

-- ================================================
-- TABLE: DRAMAS
-- ================================================
CREATE TABLE dramas (
    id_drama UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    id_genre UUID NOT NULL REFERENCES genres(id_genre) ON DELETE RESTRICT,
    title VARCHAR(255) NOT NULL,
    description TEXT,
    release_year INTEGER,
    episode_count INTEGER,
    poster_url TEXT,
    created_at TIMESTAMPTZ DEFAULT now()
);

-- ================================================
-- TABLE: USER_DRAMAS (Watch History)
-- ================================================
CREATE TABLE user_dramas (
    id_user_drama UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    id_user UUID NOT NULL REFERENCES users(id_user) ON DELETE CASCADE,
    id_drama UUID NOT NULL REFERENCES dramas(id_drama) ON DELETE CASCADE,
    watched_at TIMESTAMPTZ DEFAULT now(),
    status VARCHAR(20) DEFAULT 'watching' CHECK (status IN ('watching', 'completed', 'dropped')),
    UNIQUE(id_user, id_drama)
);

-- ================================================
-- TABLE: RATINGS
-- ================================================
CREATE TABLE ratings (
    id_rating UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    id_user UUID NOT NULL REFERENCES users(id_user) ON DELETE CASCADE,
    id_drama UUID NOT NULL REFERENCES dramas(id_drama) ON DELETE CASCADE,
    score INTEGER NOT NULL CHECK (score >= 1 AND score <= 10),
    review_text TEXT,
    created_at TIMESTAMPTZ DEFAULT now(),
    UNIQUE(id_user, id_drama)
);

-- ================================================
-- TABLE: COMMUNITIES
-- ================================================
CREATE TABLE communities (
    id_community UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    name VARCHAR(100) UNIQUE NOT NULL,
    description TEXT,
    drama_related BOOLEAN DEFAULT false,
    created_at TIMESTAMPTZ DEFAULT now()
);

-- ================================================
-- TABLE: COMMUNITY_MEMBERS
-- ================================================
CREATE TABLE community_members (
    id_community_member UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    id_user UUID NOT NULL REFERENCES users(id_user) ON DELETE CASCADE,
    id_community UUID NOT NULL REFERENCES communities(id_community) ON DELETE CASCADE,
    joined_at TIMESTAMPTZ DEFAULT now(),
    role VARCHAR(20) DEFAULT 'member' CHECK (role IN ('member', 'moderator', 'admin')),
    UNIQUE(id_user, id_community)
);

-- ================================================
-- TABLE: MESSAGES
-- ================================================
CREATE TABLE messages (
    id_message UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    id_community UUID NOT NULL REFERENCES communities(id_community) ON DELETE CASCADE,
    id_user UUID NOT NULL REFERENCES users(id_user) ON DELETE CASCADE,
    content TEXT NOT NULL,
    created_at TIMESTAMPTZ DEFAULT now()
);

-- ================================================
-- TABLE: RECOMMENDATIONS
-- ================================================
CREATE TABLE recommendations (
    id_recommendation UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    id_user UUID NOT NULL REFERENCES users(id_user) ON DELETE CASCADE,
    id_drama UUID NOT NULL REFERENCES dramas(id_drama) ON DELETE CASCADE,
    reason TEXT,
    created_at TIMESTAMPTZ DEFAULT now()
);

-- ================================================
-- INDEXES FOR PERFORMANCE
-- ================================================
CREATE INDEX idx_dramas_genre ON dramas(id_genre);
CREATE INDEX idx_user_dramas_user ON user_dramas(id_user);
CREATE INDEX idx_user_dramas_drama ON user_dramas(id_drama);
CREATE INDEX idx_ratings_user ON ratings(id_user);
CREATE INDEX idx_ratings_drama ON ratings(id_drama);
CREATE INDEX idx_community_members_user ON community_members(id_user);
CREATE INDEX idx_community_members_community ON community_members(id_community);
CREATE INDEX idx_messages_community ON messages(id_community);
CREATE INDEX idx_messages_user ON messages(id_user);
CREATE INDEX idx_recommendations_user ON recommendations(id_user);

-- ================================================
-- COMMENTAIRES
-- ================================================
COMMENT ON TABLE users IS 'Utilisateurs de la plateforme K-Space';
COMMENT ON TABLE dramas IS 'K-Dramas disponibles sur la plateforme';
COMMENT ON TABLE genres IS 'Genres de K-Dramas (Romance, Action, etc.)';
COMMENT ON TABLE user_dramas IS 'Historique de visionnage des utilisateurs';
COMMENT ON TABLE ratings IS 'Notes et avis des utilisateurs sur les dramas';
COMMENT ON TABLE communities IS 'Communautés thématiques';
COMMENT ON TABLE community_members IS 'Membres des communautés';
COMMENT ON TABLE messages IS 'Messages postés dans les communautés';
COMMENT ON TABLE recommendations IS 'Recommandations personnalisées par IA';
```

## TYPES DE DONNÉES

| Type PostgreSQL | Usage |
|----------------|-------|
| UUID | Identifiants uniques pour toutes les tables |
| VARCHAR(n) | Chaînes de caractères de longueur limitée |
| TEXT | Texte de longueur illimitée |
| INTEGER | Nombres entiers (année, épisodes, score) |
| BOOLEAN | Valeurs vrai/faux |
| TIMESTAMPTZ | Date et heure avec fuseau horaire |

## CONTRAINTES

### Clés Primaires (PRIMARY KEY)
- Chaque table a un id_* comme clé primaire UUID

### Clés Étrangères (FOREIGN KEY)
- Maintiennent l'intégrité référentielle
- ON DELETE CASCADE: suppression en cascade pour les données dépendantes
- ON DELETE RESTRICT: empêche la suppression si des données dépendantes existent

### Contraintes UNIQUE
- Évitent les doublons (username, email, rating par user/drama)

### Contraintes CHECK
- Valident les valeurs (score entre 1-10, status parmi les valeurs autorisées)

## OPTIMISATIONS

### Index
- Index sur toutes les clés étrangères
- Améliore les performances des jointures et recherches

### Valeurs par Défaut
- `gen_random_uuid()` pour les UUID
- `now()` pour les timestamps
- Valeurs par défaut pour status, role, etc.
