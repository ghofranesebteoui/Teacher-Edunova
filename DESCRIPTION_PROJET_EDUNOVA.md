# 📱 EduNova - Documentation Complète du Projet

## 🎯 Vue d'ensemble

**EduNova** est une plateforme d'e-learning moderne permettant la gestion de cours, quiz, devoirs et communication entre enseignants et étudiants.

### Technologies utilisées

**Backend:**

- Node.js + Express.js
- MySQL (base de données relationnelle)
- JWT (authentification)
- Bcrypt (hashage des mots de passe)
- Multer (upload de fichiers)
- Nodemailer (envoi d'emails)
- Google OAuth (authentification Google)

**Frontend:**

- React 18.3.1
- React Router DOM (navigation)
- Axios (requêtes HTTP)
- Firebase (intégration)
- Framer Motion (animations)
- React Icons
- SweetAlert2 (notifications)

---

## 🏗️ Architecture du Projet

### Structure Backend (`/Backend`)

```
Backend/
├── src/
│   ├── config/
│   │   └── db.js                    # Configuration MySQL
│   ├── middlewares/
│   │   └── authmiddleware.js        # Vérification JWT
│   ├── modules/
│   │   ├── auth/                    # Authentification
│   │   ├── course/                  # Gestion des cours
│   │   ├── assignment/              # Devoirs et quiz
│   │   ├── messaging/               # Messagerie instantanée
│   │   ├── notifications/           # Système de notifications
│   │   ├── profile/                 # Profils utilisateurs
│   │   ├── student/                 # Fonctionnalités étudiants
│   │   └── user/                    # Gestion utilisateurs
│   └── utils/
│       ├── email.js                 # Envoi d'emails
│       └── generateToken.js         # Génération JWT
├── uploads/                         # Fichiers uploadés (PDF, PPTX, etc.)
├── migrations/                      # Scripts SQL
└── server.js                        # Point d'entrée
```

### Structure Frontend (`/Frontend`)

```
Frontend/
├── src/
│   ├── components/
│   │   ├── AuthHeader.jsx           # En-tête authentification
│   │   ├── Footer.jsx               # Pied de page
│   │   ├── InstantMessaging.jsx     # Chat instantané
│   │   └── NotificationDropdown.jsx # Dropdown notifications
│   ├── pages/
│   │   ├── auth/                    # Pages connexion/inscription
│   │   ├── teacher/                 # Pages enseignant
│   │   ├── student/                 # Pages étudiant
│   │   ├── admin/                   # Pages administrateur
│   │   └── shared/                  # Pages partagées
│   ├── services/
│   │   ├── auth.js                  # Service authentification
│   │   ├── courseService.js         # Service cours
│   │   ├── assignmentService.js     # Service devoirs
│   │   ├── messagingService.js      # Service messagerie
│   │   └── quizService.js           # Service quiz
│   └── App.js                       # Routage principal
└── public/                          # Assets statiques
```

---

## 👥 Rôles Utilisateurs

### 1. **Étudiant** (`etudiant`)

- Consulter les cours inscrits
- Passer des quiz
- Soumettre des devoirs
- Voir ses notes et badges
- Poser des questions aux enseignants
- Messagerie avec enseignants

### 2. **Enseignant** (`enseignant`)

- Créer et gérer des cours
- Créer des quiz et devoirs
- Corriger les soumissions
- Gérer le planning
- Répondre aux questions étudiants
- Messagerie avec étudiants
- Voir les statistiques

### 3. **Administrateur** (`admin`)

- Toutes les permissions enseignant
- Gestion des utilisateurs
- Accès complet au système

---

## 🗄️ Modèle de Base de Données

### Tables principales

#### **users**

```sql
- id (PK)
- nom, prenom, email
- password (hashé)
- role (etudiant/enseignant/admin)
- email_verified (boolean)
- verification_token
- reset_password_token
- created_at, updated_at
```

#### **courses**

```sql
- id (PK)
- teacher_id (FK → users)
- title, description
- code (unique)
- status (draft/published/archived)
- created_at, updated_at
```

#### **course_contents**

```sql
- id (PK)
- course_id (FK → courses)
- title
- type (file/link/video)
- file_path ou url
- created_at
```

#### **course_enrollments**

```sql
- id (PK)
- course_id (FK → courses)
- student_id (FK → users)
- enrolled_at
- status (active/completed/dropped)
```

#### **assignments** (Quiz et devoirs)

```sql
- id (PK)
- course_id (FK → courses)
- teacher_id (FK → users)
- title, description
- type (quiz/assignment/exam)
- status (draft/published/archived)
- due_date
- time_limit (minutes)
- max_attempts
- passing_score
- total_points
- created_at, updated_at
```

#### **quiz_questions**

```sql
- id (PK)
- assignment_id (FK → assignments)
- question_text
- question_type (multiple_choice/true_false/short_answer/essay)
- options (JSON)
- correct_answer
- points
- order_index
```

#### **assignment_submissions**

```sql
- id (PK)
- assignment_id (FK → assignments)
- student_id (FK → users)
- status (pending/submitted/graded)
- score
- submitted_at, graded_at
- feedback
- attempt_number
- time_spent
```

#### **quiz_answers**

```sql
- id (PK)
- submission_id (FK → assignment_submissions)
- question_id (FK → quiz_questions)
- answer_text
- is_correct
- points_earned
```

#### **conversations**

```sql
- id (PK)
- teacher_id (FK → users)
- student_id (FK → users)
- course_id (FK → courses)
- last_message_at
- created_at
```

#### **messages**

```sql
- id (PK)
- conversation_id (FK → conversations)
- sender_id (FK → users)
- receiver_id (FK → users)
- message (TEXT)
- is_read (boolean)
- created_at
```

#### **notifications**

```sql
- id (PK)
- user_id (FK → users)
- type (success/info/warning/error)
- title, message
- is_read (boolean)
- created_at
```

#### **badges**

```sql
- id (PK)
- name, description
- icon, color
- criteria_type (quiz_count/score_average/streak/perfect_score)
- criteria_value
```

#### **student_badges**

```sql
- id (PK)
- student_id (FK → users)
- badge_id (FK → badges)
- quiz_id (FK → assignments)
- earned_at
```

#### **course_schedule**

```sql
- id (PK)
- course_id (FK → courses)
- teacher_id (FK → users)
- title, description
- scheduled_date
- duration_minutes
- location
- type (lecture/lab/exam/office_hours)
- status (scheduled/completed/cancelled)
```

#### **student_questions**

```sql
- id (PK)
- course_id (FK → courses)
- student_id (FK → users)
- teacher_id (FK → users)
- question (TEXT)
- answer (TEXT)
- status (pending/answered/closed)
- created_at, answered_at
```

---

## 🔌 API Endpoints

### **Authentification** (`/api/auth`)

```
POST   /register                    # Inscription
POST   /login                       # Connexion
POST   /google-login                # Connexion Google OAuth
POST   /logout                      # Déconnexion
POST   /forgot-password             # Demande réinitialisation
POST   /reset-password/:token       # Réinitialiser mot de passe
GET    /verify-email/:token         # Vérifier email
POST   /resend-verification         # Renvoyer email vérification
```

### **Cours** (`/api/courses`)

```
GET    /                            # Liste cours enseignant
POST   /                            # Créer un cours
GET    /stats/teacher               # Statistiques enseignant
GET    /dashboard/stats             # Stats dashboard
GET    /dashboard/schedule          # Planning enseignant
POST   /dashboard/schedule          # Créer événement planning
DELETE /dashboard/schedule/:id      # Supprimer événement
GET    /dashboard/questions         # Questions étudiants
PUT    /dashboard/questions/:id/answer  # Répondre question
GET    /dashboard/resources         # Ressources enseignant
GET    /:id                         # Détails d'un cours
PUT    /:id                         # Modifier un cours
DELETE /:id                         # Supprimer un cours
POST   /:id/contents                # Ajouter contenu (upload)
POST   /:id/links                   # Ajouter lien
DELETE /:id/contents/:contentId     # Supprimer contenu
```

### **Devoirs/Quiz** (`/api/assignments`)

```
GET    /                            # Liste devoirs enseignant
POST   /                            # Créer devoir/quiz
GET    /:id                         # Détails devoir
DELETE /:id                         # Supprimer devoir
POST   /submissions/:submissionId/grade  # Noter soumission
```

### **Étudiants** (`/api/student`)

```
GET    /courses                     # Cours inscrits
GET    /courses/:id                 # Détails cours
POST   /courses/:id/enroll          # S'inscrire à un cours
GET    /assignments                 # Devoirs/quiz disponibles
GET    /assignments/:id             # Détails devoir
POST   /assignments/:id/submit      # Soumettre devoir
GET    /quiz/:id                    # Détails quiz
POST   /quiz/:id/submit             # Soumettre quiz
GET    /badges                      # Badges obtenus
GET    /stats                       # Statistiques étudiant
```

### **Messagerie** (`/api/messaging`)

```
GET    /conversations               # Liste conversations
GET    /conversations/:id/messages  # Messages d'une conversation
POST   /messages                    # Envoyer message
POST   /conversations               # Créer conversation
PUT    /conversations/:id/read      # Marquer comme lu
```

### **Notifications** (`/api/notifications`)

```
GET    /                            # Liste notifications
GET    /unread-count                # Nombre non lues
PUT    /:id/read                    # Marquer comme lue
PUT    /mark-all-read               # Tout marquer comme lu
DELETE /:id                         # Supprimer notification
```

### **Profil** (`/api/profile`)

```
GET    /                            # Profil utilisateur
PUT    /                            # Modifier profil
PUT    /password                    # Changer mot de passe
POST   /avatar                      # Upload avatar
```

---

## 🎨 Pages Frontend

### **Pages Enseignant**

1. **TeacherDashboard** - Tableau de bord principal
2. **TeacherProfile** - Profil enseignant
3. **TeacherCourseDetail** - Détails et gestion d'un cours
4. **TeacherAssignments** - Liste des quiz/devoirs
5. **CreateQuiz** - Créer un quiz
6. **QuizResults** - Résultats d'un quiz
7. **TeacherPlanning** - Gestion du planning
8. **TeacherQuestions** - Questions des étudiants
9. **TeacherResources** - Ressources pédagogiques
10. **TeacherMessaging** - Messagerie instantanée
11. **TeacherNotifications** - Centre de notifications
12. **TeacherStatistics** - Statistiques détaillées

### **Pages Étudiant**

1. **StudentDashboard** - Tableau de bord étudiant
2. **StudentProfile** - Profil étudiant
3. **StudentQuizzes** - Liste des quiz disponibles
4. **TakeQuiz** - Passer un quiz

### **Pages Authentification**

1. **LandingPage** - Page d'accueil
2. **LoginPage** - Connexion
3. **ForgotPassword** - Mot de passe oublié
4. **ResetPassword** - Réinitialiser mot de passe
5. **VerifyEmail** - Vérification email

### **Pages Partagées**

1. **CoursesList** - Liste des cours

---

## 🔐 Système d'Authentification

### Flux d'authentification

1. **Inscription** → Email de vérification envoyé
2. **Vérification email** → Compte activé
3. **Connexion** → JWT token généré (24h)
4. **Token stocké** → localStorage (token + user)
5. **Middleware** → Vérifie token sur chaque requête protégée

### Google OAuth

- Intégration avec `@react-oauth/google`
- Connexion rapide sans mot de passe
- Création automatique du compte

---

## 📊 Fonctionnalités Clés

### 1. **Gestion des Cours**

- Création/modification/suppression
- Upload de fichiers (PDF, PPTX, etc.)
- Ajout de liens (YouTube, etc.)
- Gestion des contenus
- Inscription des étudiants

### 2. **Système de Quiz**

- Types de questions: QCM, Vrai/Faux, Réponse courte, Essai
- Limite de temps
- Tentatives multiples
- Correction automatique (QCM/Vrai-Faux)
- Correction manuelle (Réponse courte/Essai)
- Score et feedback

### 3. **Système de Badges**

- Badges automatiques selon critères
- Types: First Steps, Quiz Master, Perfect Score, etc.
- Gamification de l'apprentissage

### 4. **Messagerie Instantanée**

- Conversations 1-à-1 (enseignant ↔ étudiant)
- Messages en temps réel
- Statut lu/non lu
- Historique des conversations

### 5. **Notifications**

- Types: success, info, warning, error
- Notifications en temps réel
- Centre de notifications
- Compteur de non lues

### 6. **Planning**

- Gestion des sessions de cours
- Types: Cours, TP, Examen, Permanence
- Calendrier visuel
- Rappels automatiques

### 7. **Questions/Réponses**

- Étudiants posent des questions
- Enseignants répondent
- Statut: pending/answered/closed
- Historique des Q&A

### 8. **Statistiques**

- Enseignant: nombre de cours, étudiants, quiz, taux de réussite
- Étudiant: progression, notes moyennes, badges

---

## 🔒 Sécurité

- **Mots de passe**: Hashés avec bcryptjs
- **JWT**: Tokens expirables (24h)
- **Helmet**: Protection headers HTTP
- **CORS**: Configuration stricte
- **Validation**: Données validées côté backend
- **Upload**: Limite de taille (500MB)
- **SQL Injection**: Protection via mysql2/promise
- **XSS**: Protection via sanitization

---

## 📦 Variables d'Environnement

### Backend (`.env`)

```env
PORT=5000
NODE_ENV=development

# Database
DB_HOST=localhost
DB_USER=root
DB_PASSWORD=
DB_NAME=edunova
DB_PORT=3306

# JWT
JWT_SECRET=votre_secret_jwt
JWT_EXPIRE=24h

# Email (Nodemailer)
EMAIL_HOST=smtp.gmail.com
EMAIL_PORT=587
EMAIL_USER=votre_email@gmail.com
EMAIL_PASSWORD=votre_mot_de_passe_app

# Google OAuth
GOOGLE_CLIENT_ID=votre_client_id
GOOGLE_CLIENT_SECRET=votre_client_secret

# Frontend URL
FRONTEND_URL=http://localhost:3000
```

### Frontend (`.env`)

```env
REACT_APP_API_URL=http://localhost:5000/api
REACT_APP_GOOGLE_CLIENT_ID=votre_client_id
```

---

## 🚀 Installation et Démarrage

### Backend

```bash
cd Backend
npm install
node runMigration.js  # Exécuter migrations
npm run dev           # Mode développement
npm start             # Mode production
```

### Frontend

```bash
cd Frontend
npm install
npm start             # http://localhost:3000
npm run build         # Build production
```

---

## 📱 Recommandations pour la Version Mobile

### Technologies suggérées

1. **React Native** (recommandé)

   - Réutilisation du code React
   - Performance native
   - Expo pour développement rapide

2. **Flutter**
   - Performance excellente
   - UI moderne
   - Développement rapide

### Adaptations nécessaires

#### 1. **Navigation**

- Remplacer React Router par React Navigation
- Bottom tabs pour navigation principale
- Stack navigation pour pages détails

#### 2. **UI/UX Mobile**

- Design responsive adapté mobile
- Gestures (swipe, pull-to-refresh)
- Bottom sheets au lieu de modals
- Navigation drawer pour menu

#### 3. **Fonctionnalités spécifiques mobile**

- Push notifications (Firebase Cloud Messaging)
- Upload photos depuis caméra
- Mode hors ligne (cache local)
- Biométrie (Face ID/Touch ID)

#### 4. **Performance**

- Lazy loading des images
- Pagination des listes
- Cache des données
- Optimisation des requêtes API

#### 5. **Stockage local**

- AsyncStorage (React Native)
- SQLite pour données complexes
- Cache des cours téléchargés

#### 6. **API adaptations**

- Même backend REST API
- Ajouter endpoints pour mobile si nécessaire
- Optimiser payload (moins de données)
- Compression des images

### Structure suggérée (React Native)

```
mobile/
├── src/
│   ├── navigation/
│   │   ├── AppNavigator.js
│   │   ├── AuthNavigator.js
│   │   ├── TeacherNavigator.js
│   │   └── StudentNavigator.js
│   ├── screens/
│   │   ├── auth/
│   │   ├── teacher/
│   │   ├── student/
│   │   └── shared/
│   ├── components/
│   │   ├── common/
│   │   ├── teacher/
│   │   └── student/
│   ├── services/
│   │   ├── api.js
│   │   ├── auth.js
│   │   ├── courses.js
│   │   └── messaging.js
│   ├── store/              # Redux/Context
│   ├── utils/
│   └── constants/
├── assets/
└── App.js
```

### Priorités de développement mobile

1. **Phase 1**: Authentification + Dashboard
2. **Phase 2**: Cours + Contenus
3. **Phase 3**: Quiz + Devoirs
4. **Phase 4**: Messagerie
5. **Phase 5**: Notifications + Planning
6. **Phase 6**: Profil + Statistiques

---

## 📝 Notes importantes

- Le backend est déjà prêt pour mobile (API REST)
- Authentification JWT compatible mobile
- Upload de fichiers fonctionne avec FormData
- Messagerie peut être améliorée avec WebSocket pour temps réel
- Considérer Socket.io pour chat en temps réel sur mobile

---

## 🎯 Prochaines étapes pour mobile

1. Choisir la technologie (React Native recommandé)
2. Configurer l'environnement de développement
3. Créer la structure du projet
4. Implémenter l'authentification
5. Développer les écrans principaux
6. Intégrer les API existantes
7. Ajouter les fonctionnalités spécifiques mobile
8. Tests et optimisations
9. Déploiement (App Store + Google Play)

---

**Auteur**: Emna Benhazem  
**Version**: 1.0.0  
**Date**: Décembre 2024
