# SWE LAB Project — Campus Delivery Web App

A full-stack, role-based campus delivery platform built with React (Vite) and Node.js (Express + MongoDB).
The system enables students to order from campus shops, vendors to manage menus and orders, delivery staff to handle fulfillment, and admins to oversee platform operations.

---

## Table of Contents
- [Overview](#overview)
- [Core Features](#core-features)
- [Tech Stack](#tech-stack)
- [Project Architecture](#project-architecture)
- [Getting Started](#getting-started)
- [Environment Variables](#environment-variables)
- [Available Scripts](#available-scripts)
- [Seed Data and Demo Accounts](#seed-data-and-demo-accounts)
- [API Route Summary](#api-route-summary)
- [Authorization Model](#authorization-model)
- [Troubleshooting](#troubleshooting)
- [Future Improvements](#future-improvements)

---

## Overview

This project implements a multi-role delivery workflow for a campus ecosystem:

- **Students** browse approved shops, add items to cart, place pickup/delivery orders, and review orders.
- **Vendors** manage shop details and product inventory, track order queues, and update order statuses.
- **Delivery Partners** claim available delivery orders and mark them as delivered.
- **Admins** monitor users, shops, and orders, and perform platform moderation actions.

The application is organized as a monorepo with separate frontend (`client/`) and backend (`server/`) applications.

---

## Core Features

- JWT-based authentication and protected routes
- Role-based access control (`student`, `vendor`, `delivery`, `admin`)
- Shop and menu management for vendors
- Student cart, checkout, and order history workflows
- Delivery assignment and fulfillment tracking
- Review system for completed orders
- Admin dashboard controls for users, shops, and platform stats
- Seed script for local demo/testing data

---

## Tech Stack

### Frontend (`client/`)
- React 18
- Vite 5
- React Router v6
- Axios
- Tailwind CSS
- GSAP

### Backend (`server/`)
- Node.js + Express 4
- MongoDB + Mongoose
- JWT (`jsonwebtoken`)
- Password hashing (`bcryptjs`)
- CORS, Morgan, Dotenv

---

## Project Architecture

```text
SWE_LAB_PROJECT_Delivery_Web_App/
├─ client/                     # React + Vite frontend
│  ├─ src/
│  │  ├─ api/                  # Axios instance and API utilities
│  │  ├─ components/           # Shared UI and route guards
│  │  ├─ context/              # Auth and cart state providers
│  │  ├─ hooks/                # Custom hooks
│  │  └─ pages/                # Role-specific pages
│  └─ vite.config.js           # Dev proxy for /api -> backend
└─ server/                     # Express backend
   ├─ config/                  # DB connection
   ├─ controllers/             # Business logic by module
   ├─ middleware/              # Auth and role guards
   ├─ models/                  # Mongoose schemas
   ├─ routes/                  # REST routes by module
   ├─ seed.js                  # Seed data for local setup
   └─ server.js                # API bootstrap
```

---

## Getting Started

### Prerequisites

- Node.js 18+
- npm 9+
- MongoDB (local instance or remote URI)

### 1) Clone and install dependencies

Install dependencies separately for frontend and backend:

- In `client/`: install npm packages
- In `server/`: install npm packages

### 2) Configure environment variables

Create or update `server/.env` with the required values (see [Environment Variables](#environment-variables)).

### 3) (Optional) Seed demo data

Run the backend seed script to populate users, shops, products, orders, and reviews.

### 4) Run backend and frontend

- Start backend from `server/` (default: `http://localhost:5000`)
- Start frontend from `client/` (default: `http://localhost:5173`)

Frontend API calls use `/api` and are proxied to the backend in development.

---

## Environment Variables

Backend environment file: `server/.env`

| Variable | Required | Description | Example |
|---|---|---|---|
| `PORT` | Yes | Backend server port | `5000` |
| `MONGO_URI` | Yes | MongoDB connection string | `mongodb://127.0.0.1:27017/campus_delivery` |
| `JWT_SECRET` | Yes | Secret used to sign/verify JWT tokens | `replace_with_a_strong_secret` |
| `CLIENT_URL` | Yes | Allowed CORS origin for frontend | `http://localhost:5173` |

> **Security Note:** Never commit real secrets to source control.

---

## Available Scripts

### Frontend (`client/package.json`)

| Script | Purpose |
|---|---|
| `npm run dev` | Start Vite development server |
| `npm run build` | Build frontend for production |
| `npm run preview` | Preview production build locally |

### Backend (`server/package.json`)

| Script | Purpose |
|---|---|
| `npm run start` | Run backend server with Node |
| `npm run dev` | Run backend server with Nodemon |
| `npm run seed` | Populate database with sample data |

---

## Seed Data and Demo Accounts

After running `npm run seed` in `server/`, you can log in with:

| Role | Email | Password |
|---|---|---|
| Admin | `admin@campus.edu` | `admin123` |
| Vendor | `ravi@campus.edu` | `vendor123` |
| Vendor | `priya@campus.edu` | `vendor123` |
| Delivery | `amit@campus.edu` | `delivery123` |
| Student | `arjun@campus.edu` | `student123` |
| Student | `sneha@campus.edu` | `student123` |
| Student | `rahul@campus.edu` | `student123` |

---

## API Route Summary

Base API URL (dev): `http://localhost:5000/api`

### Auth
- `POST /auth/register`
- `POST /auth/login`
- `GET /auth/me`

### Shops
- `GET /shops`
- `GET /shops/:id`
- `GET /shops/my-shop` *(vendor)*
- `POST /shops` *(vendor)*
- `PUT /shops/:id` *(vendor)*
- `PATCH /shops/:id/toggle-open` *(vendor)*

### Products
- `GET /products/shop/:shopId`
- `POST /products` *(vendor)*
- `PUT /products/:id` *(vendor)*
- `DELETE /products/:id` *(vendor)*
- `PATCH /products/:id/toggle` *(vendor)*

### Cart (student)
- `GET /cart`
- `POST /cart/add`
- `PUT /cart/update`
- `DELETE /cart/clear`

### Orders
- `POST /orders` *(student)*
- `GET /orders/my` *(student)*
- `GET /orders/shop/:shopId` *(vendor)*
- `GET /orders/shop/:shopId/pickup-history` *(vendor)*
- `GET /orders/:id`
- `PATCH /orders/:id/status` *(vendor/admin)*
- `PATCH /orders/:id/pickup-collected` *(vendor/admin)*
- `PATCH /orders/:id/cancel` *(student)*

### Reviews
- `POST /reviews` *(student)*
- `GET /reviews/shop/:shopId`

### Delivery (delivery partner)
- `GET /delivery/available`
- `POST /delivery/assign/:orderId`
- `PATCH /delivery/deliver/:orderId`
- `GET /delivery/my`

### Admin
- `GET /admin/shops/pending`
- `GET /admin/shops`
- `PATCH /admin/shops/:id/approve`
- `PATCH /admin/shops/:id/suspend`
- `GET /admin/users`
- `PATCH /admin/users/:id/toggle`
- `GET /admin/stats`
- `GET /admin/orders`

---

## Authorization Model

- Authentication is token-based using JWT.
- Token is expected in `Authorization` header as `Bearer <token>`.
- Backend middleware enforces route-level role restrictions.
- Inactive users are blocked from authenticated access.

---

## Troubleshooting

- **Frontend cannot reach API**
  - Verify backend is running on port `5000`.
  - Confirm `client/vite.config.js` proxy points `/api` to `http://localhost:5000`.
  - Ensure `CLIENT_URL` in `server/.env` matches frontend origin.

- **MongoDB connection failed**
  - Confirm MongoDB is running.
  - Validate `MONGO_URI` in `server/.env`.

- **Invalid or expired token**
  - Log in again to refresh JWT.
  - Ensure `JWT_SECRET` remains consistent for active sessions.

---

## Future Improvements

- Add automated tests (unit + integration + end-to-end)
- Add API documentation (OpenAPI/Swagger)
- Introduce refresh-token flow and stricter auth hardening
- Add observability (structured logging and metrics)
- Add CI/CD pipeline and deployment guides

---

## License

This project is intended for academic/lab use. Add a formal license if this repository is to be distributed.
