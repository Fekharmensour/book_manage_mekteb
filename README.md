# 📚 Book Fair Sales Management System

A **very simple, fast, and beginner-friendly web application** to manage book sales during a book fair. The system allows multiple presenters (sellers) to quickly sell books with minimal interaction, and allows an admin to monitor sales and stock.

---

## 🎯 Project Overview

**Goal:** Build an ultra-simple POS (Point of Sale) system for book fairs.

**Key Features:**
- ✅ Multiple sellers (presenters) can sell books simultaneously
- ✅ Admin dashboard to monitor sales and stock
- ✅ Simple username-only login (no passwords, no OAuth)
- ✅ Fast and responsive UI
- ✅ Inventory management
- ✅ Sales tracking

**Target Users:** Non-technical users at book fair events

---

## 🧱 Tech Stack

| Layer | Technology |
|-------|------------|
| **Frontend** | React 19 + TypeScript + Vite |
| **Backend** | Express 5 + TypeScript + Node.js 20 |
| **Database** | PostgreSQL + Drizzle ORM |
| **Validation** | Zod |
| **API Codegen** | Orval (OpenAPI spec) |
| **Styling** | Tailwind CSS + Radix UI |
| **Package Manager** | pnpm (monorepo) |
| **Build Tool** | esbuild |

---

## 📁 Project Structure

```
.
├── back/                          # Backend server
│   ├── src/
│   │   ├── app.ts                # Express app setup
│   │   ├── index.ts              # Server entry point
│   │   ├── env-loader.ts         # Environment variables
│   │   ├── routes/               # API endpoints
│   │   ├── middlewares/          # Express middlewares
│   │   └── lib/                  # Utilities (logger, etc)
│   ├── build.mjs                 # esbuild configuration
│   └── package.json
│
├── front/                         # Frontend React app
│   ├── src/
│   │   ├── components/           # React components
│   │   ├── pages/                # Page components
│   │   ├── hooks/                # Custom React hooks
│   │   ├── lib/                  # Utilities & configs
│   │   └── App.tsx               # Root component
│   ├── vite.config.ts
│   └── package.json
│
├── lib/                           # Shared libraries (monorepo)
│   ├── db/                       # Database schema & ORM
│   ├── api-spec/                 # OpenAPI specification
│   ├── api-zod/                  # Generated Zod schemas
│   └── api-client-react/         # Generated React Query hooks
│
├── scripts/                       # Utility scripts
├── pnpm-workspace.yaml           # Monorepo configuration
└── package.json
```

---

## 🚀 Quick Start

### Prerequisites

- **Node.js**: 20+ (recommended 24)
- **pnpm**: v10+
- **PostgreSQL**: 14+ (local or cloud-hosted)

### Installation & Running Locally

#### 1. Install Dependencies
```bash
pnpm install
```

#### 2. Configure Environment Variables

Create a `.env` file in the `back/` directory:

```env
# Database Connection
DATABASE_URL="postgresql://user:password@localhost:5432/book_fair_db"

# Server Configuration
PORT=5000
NODE_ENV=development
```

**Local PostgreSQL Setup:**
```bash
# Start PostgreSQL (macOS with Homebrew)
brew services start postgresql

# Or with Docker
docker run -e POSTGRES_PASSWORD=password -p 5432:5432 postgres:16
```

#### 3. Initialize Database

Push the schema to your database:

```bash
cd lib/db
pnpm run push
```

**Force push** (only if you need to reset):
```bash
pnpm run push-force
```

#### 4. Generate API Types (from OpenAPI spec)

```bash
pnpm --filter @workspace/api-spec run codegen
```

This generates:
- Zod schemas (`lib/api-zod/`)
- React Query hooks (`lib/api-client-react/`)
- TypeScript types

#### 5. Start Development Servers

**Terminal 1 - Backend (Port 5000):**
```bash
cd back
pnpm run dev
```

**Terminal 2 - Frontend (Port 5001):**
```bash
cd front
pnpm run dev
```

**Open in browser:**
- Frontend: http://localhost:5001
- API Health: http://localhost:5000/health

---

## 🗄️ Database Schema

### Users Table
```sql
CREATE TABLE users (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  username VARCHAR(255) UNIQUE NOT NULL,
  is_admin BOOLEAN DEFAULT false,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
)
```

### Products Table
```sql
CREATE TABLE products (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  name VARCHAR(255) NOT NULL,
  description TEXT,
  stock INTEGER NOT NULL DEFAULT 0,
  price_buy DECIMAL(10, 2),
  price_sell DECIMAL(10, 2) NOT NULL,
  cover_image VARCHAR(255),
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
)
```

### Sales Table
```sql
CREATE TABLE sales (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  product_id UUID NOT NULL REFERENCES products(id),
  user_id UUID NOT NULL REFERENCES users(id),
  quantity INTEGER NOT NULL,
  unit_price DECIMAL(10, 2) NOT NULL,
  total_price DECIMAL(10, 2) NOT NULL,
  buyer_name VARCHAR(255),
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
)
```

---

## 🔐 Authentication & User Roles

### Authentication Model

**Username-only login** (NO passwords, NO OAuth):

1. User enters username
2. System checks if user exists in database
3. If exists → Store user in `localStorage` → User is logged in
4. If not → Show error

### User Roles

| Role | Permissions |
|------|------------|
| **Admin** | Full access (manage products, view all sales, manage users) |
| **Presenter/Seller** | Can only sell books and view their own sales |

---

## 📝 Configuration

### Environment Variables

| Variable | Description | Required | Default |
|----------|-------------|----------|---------|
| `DATABASE_URL` | PostgreSQL connection string | ✅ Yes | - |
| `PORT` | Server port | ✅ Yes | 5000 |
| `NODE_ENV` | Environment (development/production) | ❌ No | development |

### Vite Configuration (Frontend)

Frontend runs on `http://0.0.0.0:5001` by default (supports network access).

```bash
# Frontend dev
pnpm run dev      # Vite dev server

# Frontend build
pnpm run build    # Production build

# Frontend preview
pnpm run serve    # Serve production build
```

---

## 🛠️ Common Commands

### Development

```bash
# Install dependencies
pnpm install

# Typecheck all packages
pnpm run typecheck

# Build all packages
pnpm run build

# Start backend dev server
cd back && pnpm run dev

# Start frontend dev server
cd front && pnpm run dev

# Regenerate API types from OpenAPI spec
pnpm --filter @workspace/api-spec run codegen

# Push database schema changes
cd lib/db && pnpm run push
```

### Database Management

```bash
# Push schema to database (dev)
cd lib/db
pnpm run push

# Force push schema (resets data - careful!)
pnpm run push-force
```

---

## 🚢 Deployment

This project supports multiple deployment platforms. Choose one:

### Option 1: Fly.io (Backend + Frontend)

**Best for:** Full-stack deployment, good free tier

**Setup:**
1. Install Fly CLI: https://fly.io/docs/hands-on/install-flyctl/
2. Authenticate:
```bash
flyctl auth login
```
3. Configure PostgreSQL database on Fly.io (or use external database)
4. Deploy:
```bash
flyctl deploy
```

**Configuration file:** `fly.toml`

```yaml
app = 'book-fair-api'
primary_region = 'cdg'  # Change to your region

[http_service]
  internal_port = 8080
  force_https = true
  auto_stop_machines = 'stop'
  auto_start_machines = true
  min_machines_running = 0

[[vm]]
  size = 'shared-cpu-1x'
  cpus = 1
  memory_mb = 512
```

**Regions:** `cdg` (Paris), `iad` (Virginia), `sin` (Singapore), `syd` (Sydney), etc.

**Free Tier:**
- 3 shared-cpu-1x VMs
- 3GB RAM total
- $0/month (but add payment method)

### Option 2: Netlify (Frontend Only)

**Best for:** Static frontend deployment, easy setup

**Setup:**
1. Create account on https://netlify.com
2. Connect GitHub repository
3. Build command: `pnpm --filter @workspace/book-fair run build`
4. Publish directory: `front/dist`

**Configuration file:** `netlify.toml`

```toml
[build]
  command = "pnpm --filter @workspace/api-spec run codegen && pnpm --filter @workspace/book-fair run build"
  publish = "front/dist"

[build.environment]
  NODE_VERSION = "20"
  PNPM_FLAGS = "--no-frozen-lockfile"

[[redirects]]
  from = "/*"
  to = "/index.html"
  status = 200
```

**Free Tier:** ✅ Unlimited sites, 100 GB/month bandwidth

**⚠️ Note:** Netlify only hosts static files. You need a separate backend hosting.

### Option 3: Replit (Full Stack)

**Best for:** Quick prototyping, beginner-friendly

**Setup:**
1. Import repository on https://replit.com
2. Replit auto-detects Node.js project
3. Click "Run" to start

**Configuration:** See `replit.md`

**Free Tier:** Development environment (limited compute)

### Option 4: Railway.app (Backend + Frontend)

**Best for:** Easy deployment, good free tier

**Setup:**
1. Create account on https://railway.app
2. Connect GitHub repository
3. Add PostgreSQL database from Railway marketplace
4. Deploy automatically

**Free Tier:** $5/month free credits

### Option 5: Docker (Any Cloud)

**Deploy anywhere that supports Docker:**

```bash
# Build Docker image
docker build -t book-fair-api:latest .

# Run locally
docker run -p 8080:8080 \
  -e DATABASE_URL="postgresql://..." \
  -e PORT=8080 \
  book-fair-api:latest
```

**Cloud Options:**
- AWS ECS / Lambda
- Google Cloud Run
- Azure Container Instances
- DigitalOcean App Platform
- Heroku

---

## 🆓 Free Hosting & Database Options

### Database (PostgreSQL)

| Service | Free Tier | Setup Time | Notes |
|---------|-----------|-----------|-------|
| **Vercel Postgres** | 60MB, 3 databases | 2 min | Works with Vercel |
| **Railway** | $5 credit | 5 min | Easy, good for testing |
| **Neon** | 3 projects, 3GB | 3 min | Serverless, great DX |
| **Supabase** | 500MB, 2GB bandwidth | 3 min | Built-in auth (not needed) |
| **PlanetScale** | 5GB MySQL | 5 min | Not PostgreSQL |
| **Local Docker** | Unlimited | 2 min | Only for local dev |

**Recommended:** **Neon** or **Supabase** (easiest)

### Backend Hosting (Free)

| Service | Free Tier | Cold Start | Notes |
|---------|-----------|-----------|-------|
| **Fly.io** | 3 shared VMs | ~1s | Best value |
| **Railway** | $5 credit | 0s | Easy |
| **Render** | 1 free dyno | ~30s | Auto-sleep inactive |
| **Vercel** | Serverless | Cold start | TypeScript support |

### Frontend Hosting (Free)

| Service | Free Tier | Notes |
|---------|-----------|-------|
| **Netlify** | Unlimited | Best for React |
| **Vercel** | Unlimited | Also good |
| **GitHub Pages** | Unlimited | Static only |

---

## 📊 Deployment Decision Tree

```
Do you want free hosting?
├─ YES
│  ├─ Full-stack solution?
│  │  ├─ YES → Use Fly.io + Neon database
│  │  └─ NO → Frontend: Netlify, Backend: Railway, DB: Neon
│  └─ Limited budget?
│     └─ Full-stack → Railway + Railway Postgres ($5/mo)
└─ NO (willing to pay)
   ├─ Enterprise → AWS / Google Cloud / Azure
   └─ Simplicity → Fly.io + Railway Postgres ($10-20/mo)
```

---

## 🔧 Build & Optimization

### Frontend Build

```bash
cd front
pnpm run build    # Output: dist/

# Check bundle size
npm run analyze   # (if available)
```

### Backend Build

```bash
cd back
pnpm run build    # Output: dist/

# Start production server
pnpm run start
```

---

## 📖 API Documentation

The API is auto-documented via OpenAPI spec.

**OpenAPI Spec Location:** `lib/api-spec/openapi.yaml`

**Available Endpoints:**
- Health check: `GET /health`
- Authentication: `/auth/*`
- Products: `/products/*`
- Sales: `/sales/*`
- Users: `/users/*`
- Stats: `/stats/*`
- Orders: `/orders/*`

---

## 🐛 Troubleshooting

### `DATABASE_URL` Error
```
Error: DATABASE_URL, ensure the database is provisioned
```
**Solution:** Add `DATABASE_URL` to `.env` file in `back/` directory

### Port Already in Use
```bash
# Kill process on port 5000
lsof -ti:5000 | xargs kill -9

# Kill process on port 5001
lsof -ti:5001 | xargs kill -9
```

### pnpm Lock Issues
```bash
# Clear lockfile and reinstall
rm pnpm-lock.yaml
pnpm install
```

### Database Connection Issues
```bash
# Test connection
psql "postgresql://user:password@localhost:5432/book_fair_db"

# Or use URL format
psql "$(echo $DATABASE_URL)"
```

### TypeScript Errors
```bash
# Full typecheck
pnpm run typecheck

# Reset and rebuild
rm -rf node_modules
pnpm install
pnpm run typecheck
```

---

## 📝 Development Workflow

1. **Make code changes** → Servers auto-reload
2. **Update database schema** → Edit `lib/db/src/schema/index.ts`
3. **Push schema** → `cd lib/db && pnpm run push`
4. **Regenerate types** → `pnpm --filter @workspace/api-spec run codegen`
5. **Restart servers** if types changed → Kill terminals and restart

---

## 🤝 Contributing

1. Create a feature branch
2. Make changes (frontend, backend, or both)
3. Ensure typecheck passes: `pnpm run typecheck`
4. Commit and push
5. Open a pull request

---

## 📄 License

MIT

---

## 🎓 Learning Resources

- **Express.js:** https://expressjs.com/
- **React:** https://react.dev/
- **Drizzle ORM:** https://orm.drizzle.team/
- **Vite:** https://vitejs.dev/
- **TypeScript:** https://www.typescriptlang.org/
- **Tailwind CSS:** https://tailwindcss.com/
- **PostgreSQL:** https://www.postgresql.org/docs/

---

## ❓ FAQ

**Q: Can I change the port numbers?**
A: Yes. Backend: Set `PORT` in `.env`. Frontend: Edit `vite.config.ts`.

**Q: Do I need authentication?**
A: Username-only (no passwords). See Authentication section above.

**Q: Can I add password authentication?**
A: Yes, modify the auth flow in `back/src/routes/auth.ts`.

**Q: How do I backup the database?**
A: Use `pg_dump` or your hosting provider's backup feature.

**Q: Can I deploy just the frontend without backend?**
A: The frontend needs the backend API. Deploy both.

**Q: What's the typical deployment cost?**
A: Free tier: $0. Small production: $10-20/month. See deployment section.

---

**Happy coding! 🚀**
