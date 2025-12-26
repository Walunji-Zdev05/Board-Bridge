# 🌐 BoardBridge Web

[![React](https://img.shields.io/badge/React-18.2.0-blue.svg)](https://reactjs.org)
[![Vite](https://img.shields.io/badge/Vite-5.0.0-646CFF.svg)](https://vitejs.dev)
[![Python](https://img.shields.io/badge/Python-3.11-yellow.svg)](https://python.org)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-16.0-blue.svg)](https://postgresql.org)
[![Tailwind CSS](https://img.shields.io/badge/Tailwind-3.4.0-38B2AC.svg)](https://tailwindcss.com)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

> **Classroom Without Walls** - Live lesson broadcasting platform for remote education

BoardBridge Web enables teachers to broadcast live whiteboard lessons and students to view and interact with real-time classroom content from any browser.

---

## 🎯 **Features**

### For Teachers 👨‍🏫
- 📹 **Live Broadcasting** - Stream whiteboard lessons in HD
- 📚 **Class Management** - Create and organize multiple classes
- 👥 **Student Analytics** - Track attendance and engagement
- 🎥 **Multi-Camera Support** - Switch between devices
- 📊 **Dashboard** - View session history and statistics
- 💾 **Recording** - Save broadcasts for later viewing

### For Students 👨‍🎓
- 🔴 **Join Live Sessions** - Watch broadcasts in real-time
- 📸 **Capture Snapshots** - Save important moments
- 💬 **Live Chat** - Ask questions during class
- 📱 **Responsive Design** - Works on any device
- 📂 **Personal Library** - Access all saved content
- 🔔 **Notifications** - Get alerts when teachers go live

### Admin Features
- 👤 **User Management** - Manage teachers and students
- 📈 **Analytics Dashboard** - Platform-wide statistics
- 🏫 **Institution Management** - Multi-school support
- 🔐 **Access Control** - Role-based permissions

---

## 🏗️ **Architecture**

```
boardbridge-web/
├── frontend/                 # React + Vite
│   ├── src/
│   │   ├── components/      # Reusable UI components
│   │   ├── pages/           # Page components
│   │   ├── hooks/           # Custom React hooks
│   │   ├── contexts/        # React contexts
│   │   ├── services/        # API services
│   │   ├── utils/           # Helper functions
│   │   └── styles/          # Global styles
│   ├── public/              # Static assets
│   └── package.json
│
├── backend/                  # Python (Flask/FastAPI)
│   ├── app/
│   │   ├── models/          # Database models
│   │   ├── routes/          # API endpoints
│   │   ├── services/        # Business logic
│   │   ├── middleware/      # Auth, CORS, etc.
│   │   └── utils/           # Helper functions
│   ├── migrations/          # Database migrations
│   ├── tests/               # Unit tests
│   └── requirements.txt
│
└── database/
    └── schema.sql           # PostgreSQL schema
```

---

## 🛠️ **Tech Stack**

### Frontend
| Technology | Purpose |
|------------|---------|
| **React 18** | UI framework |
| **Vite** | Build tool & dev server |
| **Tailwind CSS** | Utility-first styling |
| **React Router** | Client-side routing |
| **Axios** | HTTP client |
| **Socket.io Client** | Real-time communication |
| **Zustand/Redux** | State management |
| **React Query** | Server state management |

### Backend
| Technology | Purpose |
|------------|---------|
| **Python 3.11+** | Backend language |
| **Flask/FastAPI** | Web framework |
| **SQLAlchemy** | ORM |
| **PostgreSQL** | Primary database |
| **Redis** | Caching & sessions |
| **Socket.io** | WebSocket server |
| **JWT** | Authentication |
| **Alembic** | Database migrations |

### DevOps
- **Docker** - Containerization
- **Nginx** - Reverse proxy
- **GitHub Actions** - CI/CD
- **AWS/Heroku** - Hosting

---

## 🚀 **Getting Started**

### Prerequisites
```bash
# Frontend
Node.js 18+ and npm/yarn

# Backend
Python 3.11+
PostgreSQL 14+
Redis (optional, for caching)
```

### Installation

#### 1. Clone the Repository
```bash
git clone https://github.com/yourusername/boardbridge-web.git
cd boardbridge-web
```

#### 2. Frontend Setup
```bash
cd frontend

# Install dependencies
npm install
# or
yarn install

# Create environment file
cp .env.example .env

# Update .env with your settings
# VITE_API_URL=http://localhost:5000
# VITE_SOCKET_URL=http://localhost:5000

# Start development server
npm run dev
# or
yarn dev
```

Frontend will run on `http://localhost:5173`

#### 3. Backend Setup
```bash
cd backend

# Create virtual environment
python -m venv venv

# Activate virtual environment
# On Windows:
venv\Scripts\activate
# On macOS/Linux:
source venv/bin/activate

# Install dependencies
pip install -r requirements.txt

# Create environment file
cp .env.example .env

# Update .env with your settings
DATABASE_URL=postgresql://user:password@localhost:5432/boardbridge
SECRET_KEY=your-secret-key
JWT_SECRET=your-jwt-secret

# Initialize database
python manage.py db init
python manage.py db migrate
python manage.py db upgrade

# Start development server
python run.py
# or
flask run
# or
uvicorn main:app --reload  # for FastAPI
```

Backend will run on `http://localhost:5000`

#### 4. Database Setup
```bash
# Create PostgreSQL database
psql -U postgres
CREATE DATABASE boardbridge;
CREATE USER boardbridge_user WITH PASSWORD 'your_password';
GRANT ALL PRIVILEGES ON DATABASE boardbridge TO boardbridge_user;
\q

# Run migrations
cd backend
alembic upgrade head
```

---

## 📊 **Database Schema**

```sql
-- Users Table
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    email VARCHAR(255) UNIQUE NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    full_name VARCHAR(255) NOT NULL,
    user_type VARCHAR(20) NOT NULL, -- 'teacher', 'student', 'admin'
    institution VARCHAR(255),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Classes Table
CREATE TABLE classes (
    id SERIAL PRIMARY KEY,
    class_name VARCHAR(255) NOT NULL,
    teacher_id INTEGER REFERENCES users(id),
    student_count INTEGER DEFAULT 0,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Class Enrollments
CREATE TABLE enrollments (
    id SERIAL PRIMARY KEY,
    class_id INTEGER REFERENCES classes(id),
    student_id INTEGER REFERENCES users(id),
    enrolled_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    UNIQUE(class_id, student_id)
);

-- Live Sessions
CREATE TABLE live_sessions (
    id SERIAL PRIMARY KEY,
    class_id INTEGER REFERENCES classes(id),
    teacher_id INTEGER REFERENCES users(id),
    is_live BOOLEAN DEFAULT FALSE,
    student_count INTEGER DEFAULT 0,
    started_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    ended_at TIMESTAMP
);

-- Snapshots
CREATE TABLE snapshots (
    id SERIAL PRIMARY KEY,
    student_id INTEGER REFERENCES users(id),
    class_id INTEGER REFERENCES classes(id),
    image_url TEXT NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Indexes
CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_classes_teacher ON classes(teacher_id);
CREATE INDEX idx_enrollments_student ON enrollments(student_id);
CREATE INDEX idx_sessions_live ON live_sessions(is_live);
```

---

## 🔌 **API Endpoints**

### Authentication
```
POST   /api/auth/register      - Register new user
POST   /api/auth/login         - Login user
POST   /api/auth/logout        - Logout user
GET    /api/auth/me            - Get current user
POST   /api/auth/refresh       - Refresh JWT token
```

### Classes
```
GET    /api/classes            - Get all classes (filtered by user)
POST   /api/classes            - Create new class
GET    /api/classes/:id        - Get class details
PUT    /api/classes/:id        - Update class
DELETE /api/classes/:id        - Delete class
POST   /api/classes/:id/join   - Join class (student)
```

### Live Sessions
```
GET    /api/sessions           - Get all live sessions
POST   /api/sessions           - Start live session
GET    /api/sessions/:id       - Get session details
PUT    /api/sessions/:id/stop  - Stop live session
GET    /api/sessions/:id/join  - Join session (student)
```

### Snapshots
```
GET    /api/snapshots          - Get user's snapshots
POST   /api/snapshots          - Create snapshot
DELETE /api/snapshots/:id      - Delete snapshot
```

### WebSocket Events
```
// Client → Server
connect                - Connect to server
join_session           - Join live session room
leave_session          - Leave session room
send_message           - Send chat message

// Server → Client
session_started        - Session went live
session_ended          - Session stopped
student_joined         - Student joined session
new_message            - New chat message
board_update           - Board content updated
```

---

## 🎨 **Frontend Structure**

```jsx
// Example: Teacher Dashboard Component
import { useState, useEffect } from 'react';
import { useAuth } from '@/hooks/useAuth';
import { useClasses } from '@/hooks/useClasses';

export default function TeacherDashboard() {
  const { user } = useAuth();
  const { classes, loading, startBroadcast } = useClasses();

  return (
    <div className="min-h-screen bg-gray-50">
      <header className="bg-indigo-600 text-white p-6">
        <h1 className="text-2xl font-bold">Welcome, {user.name}</h1>
      </header>
      
      <main className="container mx-auto p-6">
        <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
          {classes.map(classItem => (
            <ClassCard 
              key={classItem.id}
              data={classItem}
              onStartBroadcast={startBroadcast}
            />
          ))}
        </div>
      </main>
    </div>
  );
}
```

---

## 🐍 **Backend Structure**

```python
# Example: Flask API Route
from flask import Blueprint, request, jsonify
from flask_jwt_extended import jwt_required, get_jwt_identity
from app.models import Class, User
from app.services.class_service import ClassService

classes_bp = Blueprint('classes', __name__)

@classes_bp.route('/api/classes', methods=['GET'])
@jwt_required()
def get_classes():
    user_id = get_jwt_identity()
    user = User.query.get(user_id)
    
    if user.user_type == 'teacher':
        classes = Class.query.filter_by(teacher_id=user_id).all()
    else:
        classes = user.enrolled_classes
    
    return jsonify([c.to_dict() for c in classes]), 200

@classes_bp.route('/api/classes', methods=['POST'])
@jwt_required()
def create_class():
    user_id = get_jwt_identity()
    data = request.json
    
    new_class = ClassService.create_class(
        teacher_id=user_id,
        class_name=data['class_name']
    )
    
    return jsonify(new_class.to_dict()), 201
```

---

## 🔒 **Environment Variables**

### Frontend (.env)
```bash
VITE_API_URL=http://localhost:5000
VITE_SOCKET_URL=http://localhost:5000
VITE_APP_NAME=BoardBridge
VITE_ENABLE_ANALYTICS=false
```

### Backend (.env)
```bash
# Flask/FastAPI
FLASK_APP=run.py
FLASK_ENV=development
SECRET_KEY=your-secret-key-here

# Database
DATABASE_URL=postgresql://user:password@localhost:5432/boardbridge

# JWT
JWT_SECRET_KEY=your-jwt-secret-here
JWT_ACCESS_TOKEN_EXPIRES=3600

# CORS
CORS_ORIGINS=http://localhost:5173,http://localhost:3000

# WebSocket
SOCKET_CORS_ORIGINS=*

# Redis (optional)
REDIS_URL=redis://localhost:6379/0

# File Upload
UPLOAD_FOLDER=./uploads
MAX_CONTENT_LENGTH=16777216  # 16MB
```

---

## 🐳 **Docker Setup**

### docker-compose.yml
```yaml
version: '3.8'

services:
  frontend:
    build: ./frontend
    ports:
      - "5173:5173"
    volumes:
      - ./frontend:/app
      - /app/node_modules
    environment:
      - VITE_API_URL=http://localhost:5000
    depends_on:
      - backend

  backend:
    build: ./backend
    ports:
      - "5000:5000"
    volumes:
      - ./backend:/app
    environment:
      - DATABASE_URL=postgresql://postgres:password@db:5432/boardbridge
      - FLASK_ENV=development
    depends_on:
      - db
      - redis

  db:
    image: postgres:16
    environment:
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=password
      - POSTGRES_DB=boardbridge
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data

  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"

volumes:
  postgres_data:
```

### Run with Docker
```bash
# Build and start all services
docker-compose up -d

# View logs
docker-compose logs -f

# Stop all services
docker-compose down

# Rebuild after changes
docker-compose up -d --build
```

---

## 🧪 **Testing**

### Frontend Tests
```bash
cd frontend

# Run unit tests
npm test

# Run with coverage
npm run test:coverage

# Run E2E tests
npm run test:e2e
```

### Backend Tests
```bash
cd backend

# Run all tests
pytest

# Run with coverage
pytest --cov=app tests/

# Run specific test file
pytest tests/test_auth.py
```

---

## 📦 **Deployment**

### Frontend (Vercel/Netlify)
```bash
# Build for production
npm run build

# Preview production build
npm run preview

# Deploy to Vercel
vercel deploy --prod

# Deploy to Netlify
netlify deploy --prod
```

### Backend (Heroku)
```bash
# Login to Heroku
heroku login

# Create app
heroku create boardbridge-api

# Add PostgreSQL
heroku addons:create heroku-postgresql:hobby-dev

# Set environment variables
heroku config:set SECRET_KEY=your-secret-key

# Deploy
git push heroku main

# Run migrations
heroku run python manage.py db upgrade
```

### Full Stack (AWS/DigitalOcean)
```bash
# Use Docker Compose
docker-compose -f docker-compose.prod.yml up -d

# With Nginx reverse proxy
# Configure SSL with Let's Encrypt
# Set up PM2 for process management
```

---

## 🎨 **Tailwind Configuration**

```js
// tailwind.config.js
export default {
  content: [
    "./index.html",
    "./src/**/*.{js,ts,jsx,tsx}",
  ],
  theme: {
    extend: {
      colors: {
        primary: {
          50: '#eef2ff',
          500: '#4f46e5',
          600: '#4338ca',
        },
        emerald: {
          500: '#059669',
          600: '#047857',
        }
      },
      fontFamily: {
        sans: ['Inter', 'sans-serif'],
      },
    },
  },
  plugins: [
    require('@tailwindcss/forms'),
    require('@tailwindcss/typography'),
  ],
}
```

---

## 🔐 **Security Best Practices**

- ✅ **Password Hashing** - bcrypt with salt rounds
- ✅ **JWT Authentication** - Secure token-based auth
- ✅ **CORS Configuration** - Restricted origins
- ✅ **SQL Injection Prevention** - Parameterized queries
- ✅ **XSS Protection** - Input sanitization
- ✅ **CSRF Tokens** - For state-changing requests
- ✅ **Rate Limiting** - Prevent abuse
- ✅ **HTTPS Only** - SSL/TLS in production
- ✅ **Environment Variables** - Never commit secrets
- ✅ **Input Validation** - Server-side validation

---

## 📈 **Performance Optimization**

### Frontend
- Code splitting with React.lazy()
- Image optimization with lazy loading
- Bundle size analysis
- Service Worker for caching
- CDN for static assets

### Backend
- Database query optimization
- Redis caching for frequent queries
- Connection pooling
- Async operations where possible
- Load balancing with multiple instances

---

## 🤝 **Contributing**

1. Fork the repository
2. Create feature branch (`git checkout -b feature/amazing-feature`)
3. Commit changes (`git commit -m 'Add amazing feature'`)
4. Push to branch (`git push origin feature/amazing-feature`)
5. Open Pull Request

### Code Style
- **Frontend**: ESLint + Prettier
- **Backend**: Black + Flake8
- Follow conventional commits

---

## 📄 **License**

MIT License - see [LICENSE](LICENSE) file

---

## 👥 **Team**

- **Full Stack Developer** - Walu
- **Frontend Developer** - me
- **Backend Developer** - me
- **UI/UX Designer** - me

---

## 📞 **Contact**

- **Website**: https://boardbridge.app
- **Email**: contact@boardbridge.app
- **GitHub**: https://github.com/yourusername/boardbridge-web

---

## 🙏 **Acknowledgments**

- React Team for amazing framework
- Vite for blazing fast builds
- Tailwind CSS for utility-first styling
- Flask/FastAPI community
- PostgreSQL team
- All contributors and testers

---

<div align="center">

**Built with 💙 for education**

[⭐ Star this repo](https://github.com/yourusername/boardbridge-web) • [🐛 Report Bug](https://github.com/yourusername/boardbridge-web/issues) • [✨ Request Feature](https://github.com/yourusername/boardbridge-web/issues)

</div>
