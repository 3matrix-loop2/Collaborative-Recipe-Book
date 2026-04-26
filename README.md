# 🍳 Collaborative Recipe Book

A full-stack community recipe platform where users can share, discover, rate, and comment on recipes in real time.

![RecipeBook](https://img.shields.io/badge/Status-Production%20Ready-brightgreen)
![Node](https://img.shields.io/badge/Node.js-18%2B-green)
![MongoDB](https://img.shields.io/badge/MongoDB-7.0-green)
![Socket.IO](https://img.shields.io/badge/Socket.IO-4.6-blue)

---

## ✨ Features

| Feature | Details |
|---|---|
| **Authentication** | JWT-based register/login/logout, bcrypt password hashing |
| **Recipes** | Create, edit, delete (owner only), view all, recipe detail |
| **Search** | Search by title, ingredients, or description |
| **Filter & Sort** | Filter by 8 categories; sort by newest, rating, popularity |
| **Ratings** | 1–5 star ratings, one per user, live average display |
| **Comments** | Add and view comments per recipe |
| **Image Upload** | Multer-powered image upload with drag & drop UI |
| **User Profiles** | View profile, bio, recipe count, and recipe grid |
| **Real-time** | Socket.IO: live updates for new recipes, ratings, comments |
| **Responsive** | Mobile-first, works on all screen sizes |
| **Tests** | Jest test suites for auth and recipe endpoints |

---

## 🗂 Project Structure

```
collaborative-recipe-book/
├── backend/
│   ├── config/
│   │   └── database.js          # MongoDB connection
│   ├── controllers/
│   │   ├── authController.js    # Register, login, profile
│   │   └── recipeController.js  # Full recipe CRUD + rate/comment
│   ├── middleware/
│   │   ├── auth.js              # JWT protect/optional middleware
│   │   ├── upload.js            # Multer image upload
│   │   └── errorHandler.js      # Global error handling
│   ├── models/
│   │   ├── User.js              # User schema (bcrypt, virtuals)
│   │   └── Recipe.js            # Recipe schema (ratings, comments)
│   ├── routes/
│   │   ├── auth.js              # /api/auth/*
│   │   └── recipes.js           # /api/recipes/*
│   ├── tests/
│   │   ├── auth.test.js         # Auth endpoint tests
│   │   └── recipes.test.js      # Recipe endpoint tests
│   ├── uploads/                 # Uploaded images (gitignored)
│   ├── .env.example
│   ├── package.json
│   └── server.js                # Express + Socket.IO entry point
│
└── frontend/
    ├── css/
    │   └── main.css             # Complete design system
    ├── js/
    │   ├── auth.js              # Auth state management
    │   ├── recipes.js           # Recipe API client
    │   ├── utils.js             # UI utilities, cards, toasts
    │   ├── index.js             # Home page logic
    │   ├── browse.js            # Browse page logic
    │   ├── recipe-detail.js     # Recipe detail + real-time
    │   ├── add-recipe.js        # Create/edit recipe form
    │   └── profile.js           # User profile page
    ├── index.html               # Home page
    ├── recipes.html             # Browse all recipes
    ├── recipe.html              # Recipe detail
    ├── add-recipe.html          # Add/edit recipe form
    └── profile.html             # User profile
```

---

## 🚀 Quick Start (Local Development)

### Prerequisites
- Node.js 18+
- MongoDB 6+ (local) or MongoDB Atlas URI
- npm or yarn

### 1. Clone & Install Backend

```bash
cd collaborative-recipe-book/backend
npm install
```

### 2. Configure Environment

```bash
cp .env.example .env
```

Edit `.env`:
```env
PORT=5000
NODE_ENV=development
MONGODB_URI=mongodb://localhost:27017/recipe-book
JWT_SECRET=your_very_long_random_secret_here_change_this
JWT_EXPIRE=7d
FRONTEND_URL=http://localhost:3000
MAX_FILE_SIZE=5242880
UPLOAD_PATH=./uploads
```

### 3. Start Backend

```bash
# Development (with auto-reload)
npm run dev

# Production
npm start
```

Server starts at: `http://localhost:5000`
Health check: `http://localhost:5000/api/health`

### 4. Serve Frontend

Use any static server. Examples:

```bash
# Using Node's http-server (install once: npm i -g http-server)
cd frontend
http-server -p 3000 -c-1

# Using Python
cd frontend
python3 -m http.server 3000

# Using VS Code Live Server extension
# Right-click index.html → Open with Live Server
```

Frontend at: `http://localhost:3000`

> **Important:** The `API_BASE` in `js/auth.js`, `js/recipes.js`, and other JS files points to `http://localhost:5000/api`. If your backend runs on a different port, update these values.

### 5. Run Tests

```bash
cd backend
npm test
```

---

## 📡 API Reference

### Authentication

| Method | Endpoint | Auth | Description |
|---|---|---|---|
| POST | `/api/auth/register` | No | Create account |
| POST | `/api/auth/login` | No | Login, get JWT |
| GET | `/api/auth/me` | ✅ | Get current user |
| GET | `/api/auth/profile/:userId` | No | Public user profile |

### Recipes

| Method | Endpoint | Auth | Description |
|---|---|---|---|
| GET | `/api/recipes` | No | List recipes (paginated, filterable) |
| GET | `/api/recipes/search?q=...` | No | Search by title/ingredient |
| GET | `/api/recipes/:id` | No | Single recipe detail |
| POST | `/api/recipes` | ✅ | Create recipe (multipart/form-data) |
| PUT | `/api/recipes/:id` | ✅ (owner) | Update recipe |
| DELETE | `/api/recipes/:id` | ✅ (owner) | Delete recipe |
| POST | `/api/recipes/:id/rate` | ✅ | Rate recipe (1–5) |
| POST | `/api/recipes/:id/comment` | ✅ | Add comment |
| GET | `/api/recipes/user/:userId` | No | Recipes by user |

### Query Parameters for `GET /api/recipes`

| Param | Values | Description |
|---|---|---|
| `page` | number | Page number (default: 1) |
| `limit` | number | Results per page (default: 12) |
| `category` | Veg, Non-Veg, Vegan, Dessert, Breakfast, Snack, Beverage, Other | Filter by category |
| `sort` | `rating`, `popular`, `oldest` | Sort order (default: newest) |

### Socket.IO Events

| Event | Direction | Payload |
|---|---|---|
| `join:recipe` | Client → Server | `recipeId` |
| `recipe:created` | Server → Client | `{ _id, title, category, ... }` |
| `recipe:updated` | Server → Client | `{ _id, title }` |
| `recipe:deleted` | Server → Client | `{ _id }` |
| `recipe:rated` | Server → Room | `{ recipeId, averageRating, ratingCount }` |
| `recipe:commented` | Server → Room | `{ recipeId, comment }` |

---

## 🌐 Deployment

### Backend → Render

1. Push backend to a GitHub repo
2. Go to [render.com](https://render.com) → New Web Service
3. Connect your repo, set:
   - **Build Command:** `npm install`
   - **Start Command:** `node server.js`
4. Add Environment Variables (same as `.env` but with production values):
   - `MONGODB_URI` → your Atlas URI
   - `JWT_SECRET` → long random string
   - `FRONTEND_URL` → your Netlify/Vercel URL
   - `NODE_ENV` → `production`

### Frontend → Netlify

1. Go to [netlify.com](https://netlify.com) → New site from Git
2. Select your repo, set publish directory to `frontend/`
3. Before deploying, update all `API_BASE` constants in the JS files:
   ```js
   const API_BASE = 'https://your-backend.onrender.com/api';
   ```
4. Deploy

### Database → MongoDB Atlas

1. Create account at [mongodb.com/atlas](https://mongodb.com/atlas)
2. Create a free M0 cluster
3. Create a database user (username + password)
4. Whitelist `0.0.0.0/0` in Network Access (for Render)
5. Get your connection string:
   ```
   mongodb+srv://<user>:<password>@cluster0.xxxxx.mongodb.net/recipe-book?retryWrites=true&w=majority
   ```
6. Set this as `MONGODB_URI` in Render environment variables

---

## 🔒 Security Notes

- Passwords are hashed with bcrypt (12 salt rounds)
- JWTs expire after 7 days
- File uploads are validated by MIME type and extension
- Upload size is capped at 5MB
- CORS is restricted to `FRONTEND_URL` in production
- Password field is excluded from all API responses by default
- Owner-only routes verified server-side (not just client-side)

---

## 🛠 Tech Stack

**Backend:** Node.js, Express.js, MongoDB, Mongoose, Socket.IO, Multer, bcryptjs, JWT, express-validator, Jest, Supertest

**Frontend:** HTML5, CSS3 (Flexbox + Grid), Vanilla JavaScript (ES6+), TypeScript (API types), Socket.IO client

---

## 📄 License

MIT — feel free to use, modify, and share.
