# K-Space – Technical Architecture

## 1. Project Overview

K-Space is a platform designed for **K-Drama fans** that allows users to:

- track dramas they have watched
- rate and review K-Dramas
- create watchlists
- join communities around dramas or genres
- organize watch parties
- receive **AI-based recommendations**
- discover users with similar preferences

The project is structured to meet the requirements of the **Simplon AI Developer certification**, which requires:

- Data pipeline development
- Integration of an AI service
- Development of a full-stack application

The architecture is organized around three main components:

- Data Pipeline
- AI Service
- Web Application

---

# 2. Global System Architecture

The system follows a **three-tier architecture** separating the frontend, backend services, and database.

Users

▼

Frontend (React)

▼

Backend API (FastAPI)

▼

PostgreSQL Database


Additional services interact with this architecture:

External Data Sources
(API / Scraping / CSV)

▼

Data Pipeline (Python ETL)

▼

Database (PostgreSQL)

▼

AI Recommendation Service

▼

Application Backend (FastAPI)

▼

Frontend (React)

---

# 3. Architecture Components

## 3.1 Frontend

The frontend is responsible for user interaction and visualization.

Technology:
- React

Responsibilities:
- User authentication interface
- Viewing K-Drama information
- Managing watchlists
- Rating dramas
- Community interaction
- Displaying recommendations

The frontend communicates with the backend via **REST API requests**.

---

# 3.2 Backend API

The backend exposes the application logic and data access.

Technology:
- Python
- FastAPI

Responsibilities:

- User authentication
- CRUD operations for users, dramas, reviews and communities
- Communication with the database
- Communication with the AI service
- Exposure of REST endpoints

Example endpoints:

/api/dramas

/api/users

/api/reviews

/api/watchlist

/api/recommendations


---

# 3.3 Database

The application uses a **relational database**.

Technology:
- PostgreSQL

The database stores:

- users
- dramas
- ratings
- reviews
- watchlists
- communities
- friendships
- recommendations

The database schema will be designed using the **MERISE methodology**:

- MCD (Conceptual Data Model)
- MLD (Logical Data Model)
- MPD (Physical Data Model)

---

# 3.4 Data Pipeline

A data pipeline is responsible for collecting and preparing K-Drama information.

Technology:
- Python

Sources of data include:

1. Public APIs (example: TMDB)
2. Web scraping (example: MyDramaList)
3. CSV datasets
4. Internal application database

Pipeline stages:

Data Extraction
(API / Scraping / CSV)

▼

Data Cleaning

▼

Data Aggregation

▼

Data Storage (PostgreSQL)

This pipeline ensures that the database always contains updated K-Drama information.

---

# 3.5 AI Recommendation Service

An AI service will provide personalized recommendations.

Technology:
- Python
- Scikit-learn

Possible models:

- Collaborative filtering
- Content-based filtering
- Similarity models

Inputs:

- user watch history
- ratings
- preferred genres
- community interactions

Outputs:

- recommended dramas
- suggested friends
- suggested communities

The AI model will be exposed through a **REST API**.

Example endpoint:

/api/recommendations/{user_id}

---

# 4. Data Flow

The global data flow of the system is the following:

External Data Sources

▼

Data Pipeline

▼

PostgreSQL Database

▼

Backend API

▼

AI Recommendation Service

▼

Frontend Interface


---

# 5. Security Considerations

Security measures include:

- authentication system
- role-based access control
- secure API endpoints
- input validation
- protection against OWASP Top 10 vulnerabilities

---

# 6. Scalability Considerations

The architecture is designed to support future improvements such as:

- containerization with Docker
- CI/CD pipelines
- monitoring tools
- scalable AI models

---

# 7. Future Improvements

Future technical improvements may include:

- deployment on cloud infrastructure
- advanced recommendation algorithms
- real-time community interactions
- enhanced monitoring of AI model performance