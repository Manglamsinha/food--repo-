# ReactFood 🍽️

A modern, full-stack food ordering web application built with React 19 and Node.js. Browse a curated menu, manage your cart, and place orders through a smooth, modal-driven checkout — all powered by a lightweight Express REST API.

---

## Overview

**ReactFood** is a single-page food ordering application that replicates the core experience of a restaurant ordering platform. Users can browse a rich menu of 20 dishes, add items to a persistent shopping cart, adjust quantities on the fly, and complete a checkout form that submits their order to a backend server.

**The problem it solves:** Building a self-contained, full-stack ordering UI is a common real-world requirement. This project demonstrates clean separation of concerns between a React frontend and a Node.js API — including async data fetching, global state management via Context + `useReducer`, modal-based UX flows, and form handling with React 19's `useActionState`.

**Who it is for:**
- Developers learning React 19 patterns (Context API, custom hooks, `useActionState`)
- Students following along with a structured React course
- Developers who want a clean, runnable full-stack starter to extend into a production app

**Why it is useful:** The project covers patterns you'll use in virtually every real-world SPA: global state management, custom HTTP hooks, optimistic UI states (loading/error), portal-based modals, and a decoupled REST backend — all in a compact, well-organised codebase.

---

## Features

### 🛒 Cart Management
- Add any meal to the cart with a single click
- Increment or decrement item quantities directly from the cart modal
- Quantities auto-update the live cart item count displayed in the header
- Cart total is computed dynamically and formatted as USD currency

### 🍴 Meal Catalogue
- Fetches all available meals from the backend on page load
- Responsive CSS Grid layout that adapts to any screen width
- Each card shows a high-quality dish photo, name, price badge, and description
- Graceful loading state ("Fetching meals…") and inline error display if the API is unreachable

### 💳 Checkout Flow
- Modal-driven, multi-step UX: Cart → Checkout → Order Confirmation
- Collects full customer details: name, email, street address, postal code, and city
- Server-side validation with field-level error messages surfaced back to the UI
- Leverages React 19's `useActionState` for clean async form submission
- Shows a sending spinner while the request is in-flight
- Displays a success confirmation modal on a 201 response
- Clears cart automatically after a successful order

### 📡 HTTP Layer
- Single reusable `useHttp` custom hook encapsulates all fetch logic
- Handles GET (auto-fired on mount) and POST (manually triggered) requests
- Exposes `data`, `isLoading`, `error`, `sendRequest`, and `clearData` — a complete async data-fetching interface

### 🎨 UI & Design
- Dark, warm-toned theme with a `#ffc404` golden accent colour scheme
- Google Fonts: Raleway (body) + Lato (headings / buttons)
- Animated modal entrance (`fade-slide-up` keyframe)
- Native HTML `<dialog>` element managed via React `useRef` + `useEffect` — no third-party modal library
- React Portal rendering for modals into a dedicated `#modal` DOM node
- Reusable `Button` (solid and text-only variant) and `Input` UI primitives

### 🗄️ Backend API
- Serves the meal list from a JSON flat-file data store
- Persists submitted orders to `orders.json` with a random ID
- Simulates a 1-second processing delay to demo loading states
- Permissive CORS headers for local full-stack development

---

## Tech Stack

### Frontend
| Technology | Version | Purpose |
|---|---|---|
| React | 19.0.0 | UI component framework |
| React DOM | 19.0.0 | DOM rendering, `createPortal` for modals |
| Vite | 4.5 | Dev server, HMR, and production build tooling |
| Vanilla CSS | — | All styling (no CSS framework) |
| Google Fonts | — | Raleway & Lato typefaces |

### Backend
| Technology | Version | Purpose |
|---|---|---|
| Node.js | ≥ 18 (ESM) | Runtime |
| Express | 4.21 | HTTP server and routing |
| body-parser | 1.20 | JSON request body parsing |
| `node:fs/promises` | built-in | Async file-based data store reads/writes |

### Data Storage
| Layer | Mechanism |
|---|---|
| Meals catalogue | `backend/data/available-meals.json` — static flat file, read on every request |
| Orders | `backend/data/orders.json` — append-only flat file, written on each POST `/orders` |

> **Note:** There is no database. Data persistence is handled entirely through JSON files on disk, making this zero-dependency to set up and ideal for learning/demo purposes.

### APIs & Integrations
| Endpoint | Method | Description |
|---|---|---|
| `GET /meals` | GET | Returns the full meals array |
| `POST /orders` | POST | Validates and persists a new order |

Static meal images are served from `backend/public/images/` via Express's `express.static` middleware.

---

## Project Structure

```
food--repo--main/
├── index.html                  # Vite HTML entry point
├── vite.config.js              # Vite + React plugin configuration
├── package.json                # Frontend dependencies (React 19, Vite)
├── eslint.config.js            # ESLint rules for React + Hooks
│
├── src/
│   ├── main.jsx                # React root — mounts <App /> into #root
│   ├── App.jsx                 # Root component; composes context providers + layout
│   ├── App.css                 # Root-level styles
│   ├── index.css               # Global styles, CSS variables, animations
│   │
│   ├── assets/
│   │   └── logo.jpg            # Header logo image
│   │
│   ├── components/
│   │   ├── Header.jsx          # Top nav with logo, app title, and cart button
│   │   ├── Meals.jsx           # Fetches & renders the meal grid
│   │   ├── MealItem.jsx        # Individual meal card (image, name, price, CTA)
│   │   ├── Cart.jsx            # Cart modal (item list, total, checkout CTA)
│   │   ├── CartItem.jsx        # Single cart row with +/- quantity controls
│   │   ├── Checkout.jsx        # Checkout form modal with order submission
│   │   └── Error.jsx           # Reusable inline error display component
│   │   └── UI/
│   │       ├── Button.jsx      # Reusable button (solid + text-only variants)
│   │       ├── Input.jsx       # Labelled form input primitive
│   │       └── Modal.jsx       # Native <dialog>-based portal modal
│   │
│   ├── store/
│   │   ├── CartContext.jsx     # Cart state (items, addItem, removeItem, clearCart)
│   │   └── UserProgressContext.jsx  # UI flow state ('', 'cart', 'checkout')
│   │
│   ├── hooks/
│   │   └── useHttp.js          # Generic fetch hook (GET/POST, loading, error)
│   │
│   └── util/
│       └── formatting.js       # Intl.NumberFormat USD currency formatter
│
└── backend/
    ├── app.js                  # Express server (GET /meals, POST /orders, CORS)
    ├── package.json            # Backend dependencies (express, body-parser)
    └── data/
        ├── available-meals.json    # 20 menu items with name, price, description, image
        └── orders.json             # Persisted orders array (append-only)
    └── public/
        └── images/             # 20 high-res JPEG dish photographs
```

---

## Architecture

ReactFood follows a clean **client–server separation** with two independently runnable processes:

```
Browser (React SPA — Vite :5173)
        │
        │  GET /meals  (on mount)
        │  POST /orders (on checkout submit)
        ▼
Express API Server (:3000)
        │
        ├── Reads  → backend/data/available-meals.json
        └── Writes → backend/data/orders.json
        └── Serves → backend/public/images/*
```

**State management** is handled entirely through React Context:

- `CartContext` — owns the `items[]` array and exposes `addItem`, `removeItem`, `clearCart` actions via a `useReducer`-based reducer (ADD_ITEM, REMOVE_ITEM, CLEAR_CART).
- `UserProgressContext` — owns a `progress` string (`''` | `'cart'` | `'checkout'`) that controls which modal is visible at any given time. This decouples modal visibility from the components that trigger it.

Both contexts are provided at the application root in `App.jsx`, wrapping all children.

---

## Getting Started

### Prerequisites

- **Node.js** v18 or higher
- **npm** v8 or higher

### Installation & Running

You need two terminal sessions — one for the frontend and one for the backend.

**1. Start the backend API server**

```bash
cd backend
npm install
npm start
# Server runs on http://localhost:3000
```

**2. Start the frontend dev server**

```bash
# From the project root
npm install
npm run dev
# App runs on http://localhost:5173
```

Open [http://localhost:5173](http://localhost:5173) in your browser.

> The frontend expects the backend to be available at `http://localhost:3000`. Both servers must be running simultaneously for the app to function.

### Build for Production

```bash
# From the project root
npm run build
# Output → dist/
npm run preview  # Preview the production build locally
```

---

## Available Scripts

### Frontend (root)

| Script | Description |
|---|---|
| `npm run dev` | Start Vite dev server with HMR |
| `npm run build` | Build optimised production bundle to `dist/` |
| `npm run preview` | Serve the production build locally |
| `npm run lint` | Run ESLint across all `.js` and `.jsx` files |

### Backend (`backend/`)

| Script | Description |
|---|---|
| `npm start` | Start the Express server on port 3000 |

---

## API Reference

### `GET /meals`

Returns the full list of available meals.

**Response `200 OK`**
```json
[
  {
    "id": "m1",
    "name": "Mac & Cheese",
    "price": "8.99",
    "description": "Creamy cheddar cheese mixed with perfectly cooked macaroni...",
    "image": "images/mac-and-cheese.jpg"
  }
]
```

### `POST /orders`

Submits a new order.

**Request Body**
```json
{
  "order": {
    "items": [
      { "id": "m1", "name": "Mac & Cheese", "price": "8.99", "quantity": 2 }
    ],
    "customer": {
      "name": "Jane Doe",
      "email": "jane@example.com",
      "street": "123 Main St",
      "postal-code": "10001",
      "city": "New York"
    }
  }
}
```

**Response `201 Created`**
```json
{ "message": "Order created!" }
```

**Response `400 Bad Request`** — if any required customer field is missing or the items array is empty.
```json
{ "message": "Missing data: Email, name, street, postal code or city is missing." }
```

---

## Key Design Patterns

**Custom HTTP Hook (`useHttp`)** — A single hook abstracts all fetch logic. It auto-fires GET requests on mount and exposes a `sendRequest` function for manual POST calls. Components only deal with `data`, `isLoading`, and `error`.

**Context + useReducer for Cart** — Rather than prop-drilling or a third-party state library, cart state is managed with a `useReducer` reducer inside a Context provider. This is idiomatic React and scales well for this app size.

**`useActionState` for Form Submission (React 19)** — The Checkout form uses React 19's `useActionState` hook to bind the form's `action` attribute to an async function, naturally handling the pending/submitting state without manual `useState` boilerplate.

**Native `<dialog>` Modal** — Rather than relying on a UI library, the Modal component leverages the browser-native `<dialog>` element with `showModal()` / `close()` for accessible, backdrop-aware modals. React Portals render them outside the normal component tree into a dedicated `#modal` div.

---

## Potential Improvements

- **Database** — Replace the flat-file JSON store with SQLite or PostgreSQL for real persistence and concurrent-write safety.
- **Authentication** — Add user login so order history can be tracked per account.
- **Quantity selector on meal cards** — Allow users to pick a quantity before adding rather than tapping "+" multiple times in the cart.
- **Environment variables** — Move the hardcoded `http://localhost:3000` base URL into a `.env` file for easier environment switching.
- **Tests** — Add Vitest unit tests for the cart reducer and `useHttp` hook; Playwright E2E tests for the checkout flow.
- **Deployment** — Containerise both services with Docker Compose for one-command local setup and straightforward cloud deployment.

---

## License

This project is intended for educational purposes. No license file is included in the repository.
