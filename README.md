<div align="center">

# 🎂 BakeSmart

**Design your cake in 3D, see the price update live, and order it online.**

A full-stack bakery e-commerce and cake-customization platform for Bangladesh, with bilingual (Bangla / English) support, local payments, delivery scheduling and dashboards for admins and delivery staff.

![Status](https://img.shields.io/badge/status-in%20active%20development-orange)
![TypeScript](https://img.shields.io/badge/TypeScript-3178C6?logo=typescript&logoColor=white)
![React](https://img.shields.io/badge/React-20232A?logo=react&logoColor=61DAFB)
![Node.js](https://img.shields.io/badge/Node.js-339933?logo=nodedotjs&logoColor=white)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-4169E1?logo=postgresql&logoColor=white)
![License](https://img.shields.io/badge/license-MIT-blue)

<!-- After the first GitHub Actions run, add the CI badge:
![CI](https://github.com/<your-username>/bakesmart/actions/workflows/ci.yml/badge.svg)
-->

[Features](#-features) · [Tech Stack](#-tech-stack) · [Architecture](#-architecture) · [Getting Started](#-getting-started) · [API](#-api-overview) · [Roadmap](#-roadmap)

</div>

---

> **Project status:** BakeSmart is being built in public, one milestone at a time. The [Roadmap](#-roadmap) shows exactly what is done and what is next. Each ticked item links to working, tested code.

## 📖 Table of Contents

- [About](#-about)
- [Features](#-features)
- [Tech Stack](#-tech-stack)
- [Architecture](#-architecture)
- [Data Model](#-data-model)
- [Order Lifecycle](#-order-lifecycle)
- [Getting Started](#-getting-started)
- [Environment Variables](#-environment-variables)
- [Available Scripts](#-available-scripts)
- [API Overview](#-api-overview)
- [Testing](#-testing)
- [Deployment](#-deployment)
- [Project Structure](#-project-structure)
- [Non-Functional Requirements](#-non-functional-requirements)
- [Roadmap](#-roadmap)
- [Contributing](#-contributing)
- [License](#-license)
- [Author](#-author)

## 🍰 About

Ordering a custom cake in Bangladesh usually means phone calls, photos sent back and forth, and a price that changes at pickup. BakeSmart turns that into one clear online flow.

**For customers**, it is a place to browse, design a cake in 3D, see the exact price as they choose each option, pay with bKash, Nagad or card, and track the order to their door.

**For bakeries**, it is a dashboard to manage products, pricing, promotions, orders and delivery capacity, with analytics on what sells.

### Who it is for

| User | What they can do |
| --- | --- |
| **Guest** | Browse products, offers and outlets; switch language and theme |
| **Customer** | Design and order cakes, pay online, track orders, review, save favourites and designs |
| **Bakery admin** | Manage products, pricing, promotions, inventory, orders, roles and analytics |
| **Delivery staff** | See assigned deliveries, update delivery status, view routes |

## ✨ Features

### Shopping and customization

- **3D cake designer:** drag-and-drop layers, colours and decorations, with a live 3D preview you can rotate and zoom
- **Live, server-verified pricing:** the price updates with every ingredient, size and decoration, and the server recalculates it at checkout instead of trusting the browser
- **Gift packaging:** themed boxes, ribbons, cards and messages, with live cost updates
- **Catalogue:** browse by category, with search and filters; 3D previews of existing bakery items
- **Saved designs gallery:** save, reopen and share your cake designs
- **Favourites and wishlist**

### Ordering and payments

- **Secure checkout** with bKash, Nagad and cards through the SSLCommerz gateway (sandbox in development)
- **Idempotent payment webhooks:** a retried payment notification can never mark an order paid twice
- **Smart delivery scheduling:** time slots validated against bakery hours, daily capacity and holidays
- **Order tracking** through a logged status workflow, with email updates at each step
- **Order history and one-click reorder**

### Community

- **Ratings and reviews:** 1 to 5 stars, comments and bakery replies, plus a public review wall
- **Outlet map:** find nearby bakery outlets on an interactive map
- **Contact and feedback form** for website issues

### Experience

- **Bangla and English** interface
- **Light and dark mode**
- **Offline browsing:** cached product pages and images (Progressive Web App); purchasing requires a connection
- **Fully responsive** on phones, tablets and desktops

### Administration

- **Role management** for admins, bakers and delivery staff
- **Product, option and price management**
- **Promotions and discount codes**
- **Analytics dashboard:** revenue, top products, order volume and peak delivery slots

### Personalization (later milestone)

- **Cake recommendations** based on a customer's past orders, favourites and popular combinations

## 🛠 Tech Stack

| Layer | Technology | Why |
| --- | --- | --- |
| **Frontend** | React + TypeScript (Vite), Tailwind CSS | Type-safe, fast development builds |
| **3D** | Three.js via React Three Fiber and Drei | Declarative 3D scenes inside React |
| **Forms and validation** | React Hook Form + Zod | One validation schema shared by client and server |
| **State and data fetching** | TanStack Query | Caching, retries and loading states |
| **Internationalization** | i18next (react-i18next) | Bangla and English |
| **Maps** | Leaflet + OpenStreetMap | Free, no API key required |
| **Offline / PWA** | vite-plugin-pwa (Workbox) | Cached pages and images |
| **Backend** | Node.js + Express + TypeScript | REST API |
| **Database** | PostgreSQL + Prisma ORM | Relational data and ACID transactions for orders and payments |
| **Authentication** | argon2 password hashing, JWT in httpOnly cookies, email OTP, role-based access control | Secure sessions and roles |
| **Payments** | SSLCommerz (bKash, Nagad, cards) | Bangladesh's local payment methods through one gateway |
| **Image storage** | Cloudinary | Design uploads and product images |
| **Email** | Nodemailer (SMTP) | Order confirmations and status updates |
| **Testing** | Vitest, Supertest, Playwright | Unit, API and end-to-end tests |
| **DevOps** | Docker Compose, GitHub Actions | Reproducible local setup and CI on every push |
| **Hosting** | Vercel (frontend), Render or Railway (API), Neon (PostgreSQL) | Free tiers for a live demo |
| **Design** | Figma, Canva | Mockups and assets |

## 🏗 Architecture

```mermaid
flowchart LR
    subgraph Client["Browser (React + TypeScript PWA)"]
        UI["Pages and components"]
        R3F["3D designer<br/>(React Three Fiber)"]
        SW["Service worker<br/>(offline cache)"]
    end

    subgraph API["API server (Node.js + Express)"]
        AUTH["Auth and roles"]
        CAT["Catalogue and options"]
        PRICE["Pricing engine"]
        ORD["Orders and delivery slots"]
        PAY["Payments and webhooks"]
        ADM["Admin and analytics"]
    end

    DB[("PostgreSQL<br/>(Prisma)")]
    IMG["Cloudinary"]
    GW["SSLCommerz<br/>(bKash, Nagad, cards)"]
    MAIL["SMTP email"]

    UI --> AUTH & CAT & ORD & ADM
    R3F --> PRICE
    AUTH & CAT & PRICE & ORD & PAY & ADM --> DB
    CAT --> IMG
    PAY <--> GW
    ORD --> MAIL
```

**Key design decisions**

- **The server owns the price.** The browser shows a live estimate using the same shared pricing module, but checkout always recalculates on the server.
- **Orders and payments change together.** Order and payment updates run inside one database transaction, so an order is never "paid" without a matching payment record.
- **Webhooks are idempotent.** Each payment notification carries a unique key; repeats are detected and ignored.
- **Every status change is logged** with who made it and when.

## 🗃 Data Model

```mermaid
erDiagram
    USER ||--o{ ORDER : places
    USER ||--o{ REVIEW : writes
    USER ||--o{ SAVED_DESIGN : saves
    USER ||--o{ FAVOURITE : marks
    CAKE ||--o{ ORDER_ITEM : "ordered as"
    CAKE ||--o{ REVIEW : receives
    OPTION_GROUP ||--o{ OPTION : contains
    ORDER ||--|{ ORDER_ITEM : contains
    ORDER ||--o{ ORDER_STATUS_HISTORY : logs
    ORDER ||--o{ PAYMENT : "paid by"
    ORDER }o--|| DELIVERY_SLOT : "scheduled in"

    USER {
        uuid id
        string name
        string email
        string passwordHash
        enum role "CUSTOMER | ADMIN | BAKER | DELIVERY"
    }
    CAKE {
        uuid id
        string name
        string category
        int basePrice "in paisa"
    }
    OPTION_GROUP {
        uuid id
        string name "flavour, size, tiers, topping"
    }
    OPTION {
        uuid id
        string label
        int priceDelta "in paisa"
    }
    ORDER {
        uuid id
        enum status
        int total "in paisa"
    }
    ORDER_ITEM {
        uuid id
        json customization
        int unitPrice
    }
    ORDER_STATUS_HISTORY {
        uuid id
        enum fromStatus
        enum toStatus
        uuid changedBy
        datetime changedAt
    }
    PAYMENT {
        uuid id
        string idempotencyKey
        enum method "BKASH | NAGAD | CARD"
        enum state
    }
    DELIVERY_SLOT {
        uuid id
        datetime startsAt
        int capacity
    }
```

Money is stored as whole numbers in **paisa** (1 taka = 100 paisa) to avoid rounding errors.

## 🔄 Order Lifecycle

```mermaid
stateDiagram-v2
    [*] --> PENDING: order placed
    PENDING --> CONFIRMED: payment received
    PENDING --> CANCELLED: payment failed or timed out
    CONFIRMED --> BAKING: baker starts
    BAKING --> READY: cake finished
    READY --> OUT_FOR_DELIVERY: picked up by delivery staff
    OUT_FOR_DELIVERY --> DELIVERED: handed to customer
    CONFIRMED --> CANCELLED: cancelled by admin
    DELIVERED --> [*]
    CANCELLED --> [*]
```

Only the allowed transitions above are accepted by the API; anything else returns `409 Conflict`.

## 🚀 Getting Started

### Prerequisites

- [Node.js](https://nodejs.org/) 20 or later
- [pnpm](https://pnpm.io/) (or npm)
- [Docker](https://www.docker.com/) and Docker Compose
- Git

### Installation

```bash
# 1. Clone the repository
git clone https://github.com/<your-username>/bakesmart.git
cd bakesmart

# 2. Install dependencies for client and server
pnpm install

# 3. Create your environment files
cp server/.env.example server/.env
cp client/.env.example client/.env

# 4. Start PostgreSQL in Docker
docker compose up -d db

# 5. Create the database tables and load sample data
pnpm --filter server prisma migrate dev
pnpm --filter server db:seed

# 6. Run client and server together
pnpm dev
```

The client runs at `http://localhost:5173` and the API at `http://localhost:4000`.

### Run everything in Docker

```bash
docker compose up --build
```

### Demo accounts (after seeding)

| Role | Email | Password |
| --- | --- | --- |
| Admin | `admin@bakesmart.test` | `Admin123!` |
| Customer | `customer@bakesmart.test` | `Customer123!` |
| Delivery | `delivery@bakesmart.test` | `Delivery123!` |

These exist only in your local seeded database.

## 🔐 Environment Variables

**`server/.env`**

| Variable | Description | Example |
| --- | --- | --- |
| `PORT` | API port | `4000` |
| `DATABASE_URL` | PostgreSQL connection string | `postgresql://bakesmart:bakesmart@localhost:5432/bakesmart` |
| `JWT_SECRET` | Long random string for signing tokens | `change-me` |
| `CLIENT_URL` | Allowed origin for CORS | `http://localhost:5173` |
| `SSLCZ_STORE_ID` | SSLCommerz sandbox store ID | `your-sandbox-id` |
| `SSLCZ_STORE_PASSWORD` | SSLCommerz sandbox password | `your-sandbox-password` |
| `SSLCZ_IS_LIVE` | `false` for sandbox | `false` |
| `CLOUDINARY_URL` | Cloudinary credentials | `cloudinary://key:secret@cloud` |
| `SMTP_HOST`, `SMTP_PORT`, `SMTP_USER`, `SMTP_PASS` | Email settings | — |

**`client/.env`**

| Variable | Description | Example |
| --- | --- | --- |
| `VITE_API_URL` | API base URL | `http://localhost:4000/api` |

Never commit real `.env` files; only the `.env.example` templates belong in Git.

## 📜 Available Scripts

| Command | What it does |
| --- | --- |
| `pnpm dev` | Runs client and server in development mode |
| `pnpm build` | Builds both apps for production |
| `pnpm test` | Runs unit and API tests |
| `pnpm test:e2e` | Runs Playwright end-to-end tests |
| `pnpm lint` | Runs ESLint |
| `pnpm format` | Formats code with Prettier |
| `pnpm --filter server db:seed` | Loads sample cakes, options and users |

## 🔌 API Overview

Base URL: `/api`. All responses are JSON.

| Method | Endpoint | Access | Description |
| --- | --- | --- | --- |
| `POST` | `/auth/register` | Public | Create an account |
| `POST` | `/auth/login` | Public | Log in (sets an httpOnly cookie) |
| `POST` | `/auth/verify-otp` | Public | Verify the email OTP |
| `POST` | `/auth/logout` | Signed in | Log out |
| `GET` | `/cakes` | Public | List cakes, with search, category and price filters |
| `GET` | `/cakes/:id` | Public | Cake details, options and reviews |
| `GET` | `/options` | Public | Option groups (flavour, size, tiers, toppings, packaging) |
| `POST` | `/pricing/quote` | Public | Price a customization (same logic as checkout) |
| `GET` | `/delivery-slots` | Public | Available slots for a date |
| `POST` | `/orders` | Customer | Place an order |
| `GET` | `/orders/me` | Customer | My order history |
| `PATCH` | `/orders/:id/status` | Admin, Baker, Delivery | Move an order to its next status |
| `POST` | `/payments/init` | Customer | Start an SSLCommerz checkout |
| `POST` | `/payments/webhook` | SSLCommerz | Payment notification (idempotent) |
| `POST` | `/reviews` | Customer | Rate and review a cake |
| `GET` | `/admin/analytics` | Admin | Revenue, top products, order volume |

Full request and response examples will live in [`docs/api.md`](docs/api.md).

## 🧪 Testing

| Level | Tool | What it covers |
| --- | --- | --- |
| Unit | Vitest | Pricing engine, slot validation, status transitions |
| API | Vitest + Supertest | Every endpoint, including auth and role checks |
| End-to-end | Playwright | Sign up → customize → checkout; admin moves an order to Delivered |

```bash
pnpm test         # unit and API tests
pnpm test:e2e     # end-to-end tests
```

GitHub Actions runs linting and tests on every push and pull request.

## ☁️ Deployment

| Part | Platform |
| --- | --- |
| Frontend | Vercel |
| API | Render or Railway |
| Database | Neon (PostgreSQL) |
| Images | Cloudinary |

A live demo link will be added here once the first milestone is deployed.

## 📁 Project Structure

```text
bakesmart/
├── client/                  # React + TypeScript frontend
│   ├── public/
│   └── src/
│       ├── components/      # Reusable UI components
│       ├── features/        # cakes, designer, cart, orders, admin, reviews
│       ├── three/           # 3D scenes and models
│       ├── i18n/            # Bangla and English translations
│       ├── hooks/
│       └── pages/
├── server/                  # Express + TypeScript API
│   ├── prisma/
│   │   ├── schema.prisma    # Database schema
│   │   └── seed.ts          # Sample data
│   └── src/
│       ├── modules/         # auth, cakes, pricing, orders, payments, admin
│       ├── middleware/      # auth, roles, validation, errors
│       └── app.ts
├── shared/                  # Pricing logic and Zod schemas used by both apps
├── docs/                    # API docs, diagrams, screenshots
├── .github/workflows/       # CI pipeline
├── docker-compose.yml
└── README.md
```

## 📐 Non-Functional Requirements

| Category | Target |
| --- | --- |
| **Performance** | Product pages load in under 2 seconds on 4G; the 3D designer stays smooth on mid-range phones |
| **Security** | HTTPS everywhere, hashed passwords, httpOnly JWT cookies, role checks on every protected route, payments handled by the gateway (no card data stored) |
| **Reliability** | 99% uptime target, daily database backups |
| **Usability** | Every customization step usable without instructions |
| **Scalability** | New branches, menus and option types added through data, not code changes |
| **Portability** | Responsive on phones, tablets and desktops |
| **Localization** | Full Bangla and English interface |
| **Maintainability** | Modular code, typed end to end, documented API, automated tests |

## 🗺 Roadmap

### Milestone 1: Foundation
- [ ] Repository setup: TypeScript, ESLint, Prettier, folder structure
- [ ] PostgreSQL in Docker Compose, Prisma schema, migrations and seed data
- [ ] CI pipeline on GitHub Actions

### Milestone 2: Accounts and catalogue
- [ ] Registration, login and email OTP
- [ ] Role-based access control
- [ ] Cake catalogue with categories, search and filters

### Milestone 3: Customization
- [ ] Shared pricing engine (client estimate, server-verified total)
- [ ] Option-based customizer with live price
- [ ] Design image upload
- [ ] Gift packaging options

### Milestone 4: Orders and delivery
- [ ] Order placement and order-status workflow with history
- [ ] Delivery slots validated against hours, capacity and holidays
- [ ] Baker, admin and delivery dashboards
- [ ] Email notifications

### Milestone 5: Payments
- [ ] SSLCommerz sandbox checkout (bKash, Nagad, card)
- [ ] Idempotent payment webhook inside a database transaction

### Milestone 6: Launch
- [ ] Unit, API and end-to-end tests
- [ ] Deployment and live demo
- [ ] Screenshots, architecture diagram and demo video

### Milestone 7: Enhancements
- [ ] 3D cake designer with drag-and-drop layers and decorations
- [ ] Bangla and English, light and dark mode
- [ ] Reviews, review wall, favourites and saved designs
- [ ] Outlet map
- [ ] Offline browsing (PWA)
- [ ] Promotions and analytics dashboard
- [ ] Personalized cake recommendations

## 🤝 Contributing

Suggestions and bug reports are welcome.

1. Fork the repository
2. Create a branch: `git checkout -b feature/your-feature`
3. Commit using [Conventional Commits](https://www.conventionalcommits.org/): `git commit -m "feat: add delivery slot validation"`
4. Push and open a pull request

## 📄 License

Released under the [MIT License](LICENSE).

## 👩‍💻 Author

**Cynthia Safa**
Computer Science and Engineering, BRAC University

[![GitHub](https://img.shields.io/badge/GitHub-181717?logo=github&logoColor=white)](https://github.com/httpscynthiasafayo)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-0A66C2?logo=linkedin&logoColor=white)](https://www.linkedin.com/in/cynthia-safa2226/)
[![Email](https://img.shields.io/badge/Email-D14836?logo=gmail&logoColor=white)](mailto:cynthiasafa16@gmail.com)
