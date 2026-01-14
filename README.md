# Food Reel System

A short-form food video platform where food partners can upload short food reels and users can browse, like, and save them.

---

## Problem Statement

Many small food businesses lack a simple way to showcase short video clips of dishes to attract customers. This project provides a lightweight platform for food partners to upload short food reels and for users to discover, like, and save those reels.

---

## Features

### Core Features
- Food partner upload of short video reels (multipart upload, stored via ImageKit)
- Centralized reel feed (`GET /api/food`) that returns all food items
- Like / unlike a reel (increments or decrements `likeCount`)
- Save / unsave a reel (persisted in the `Save` collection)
- Food partner profile endpoint that returns the partner and their food items

### User Interactions
- Register / login as a **user** (cookie-based JWT authentication)
- Browse vertical reels feed with auto-play using IntersectionObserver
- Like and save reels directly from the feed
- View saved reels on a dedicated Saved page
- Visit food partner pages (`/food-partner/:id`)

### Authentication
- Separate accounts for **users** and **food partners**
- JWT-based authentication stored in cookies:
  - `usertoken` for user routes
  - `partnertoken` for food partner routes
- Middleware protection for routes that require authentication

### Backend / API Capabilities
- Express-based REST API
- Routes organized under `/api/auth`, `/api/food`, and `/api/food-partner`
- Multipart uploads handled using `multer` (in-memory storage)
- Media stored remotely using ImageKit
- Persistent storage using MongoDB with Mongoose

---
Video


[Screen Recording 2026-01-14 111254.zip](https://github.com/user-attachments/files/24605943/Screen.Recording.2026-01-14.111254.zip)

---

## Tech Stack

### Frontend
- React (v19) + Vite
- react-router-dom
- axios
- gsap

### Backend
- Node.js + Express
- Authentication: bcrypt, jsonwebtoken (JWT)
- File handling: multer, ImageKit
- Utilities: cookie-parser, cors, dotenv, uuid

### Database
- MongoDB (via Mongoose)

### Tools
- ImageKit Node SDK (`@imagekit/nodejs`)
- Nodemon (`npx nodemon server.js`)

---

## How the System Works

### High-level Architecture
- Frontend (Vite + React) communicates with the backend API running on port `3000`
- Backend exposes REST endpoints and persists data in MongoDB
- Video files are uploaded as multipart form-data to `/api/food` and stored on ImageKit
- The returned video URL is stored in the `food.video` field

### Request Flow
1. User or food partner signs up or logs in via `/api/auth/*`
2. Successful authentication sets a JWT cookie (`usertoken` or `partnertoken`)
3. Frontend sends requests with `withCredentials: true`
4. Public feed is fetched using `GET /api/food`
5. Food partners upload reels via `POST /api/food`
6. Users like (`POST /api/food/like`) and save (`POST /api/food/save`) reels
7. Saved reels are fetched via `GET /api/food/saved`

### Reel Feed Logic
- Backend returns all food documents through `GET /api/food`
- Frontend renders them using a reusable `ReelFeed` component
- IntersectionObserver auto-plays the reel when ~60% visible
- Like and Save actions update the UI optimistically
- Saved feed maps populated `Save` records into the same reel format

---

## API Reference (Selected Endpoints)

### Authentication
- `POST /api/auth/user/register`
- `POST /api/auth/user/login`
- `POST /api/auth/foodpartner/register`
- `POST /api/auth/foodpartner/login`

### Food
- `POST /api/food` — Upload a food reel (food partner only)
- `GET /api/food` — Get all food reels
- `POST /api/food/like` — Like / unlike a reel
- `POST /api/food/save` — Save / unsave a reel
- `GET /api/food/saved` — Get saved reels

### Food Partner
- `GET /api/food-partner/:id` — Get partner details and their food items

---

## Database Models

- `User` — { fullname, email (unique), password }
- `FoodPartner` — { name, contactName, phone, address, email (unique), password }
- `Food` — { name, video, description, foodPartner, likeCount, savesCount }
- `Like` — { user, food }
- `Save` — { user, food }

---

## Known Limitations & Notes

- The `Save` collection persists saved reels, but `food.savesCount` is not automatically updated on save/unsave.
- Comment-related UI exists on the frontend, but backend models and endpoints for comments are not implemented yet.
- Secrets are managed via `.env` files; production deployments should use secure secret management.
- Automated tests are not included in the current version.

---

## Local Setup

### Prerequisites
- Node.js (16+ recommended)
- MongoDB (local or cloud)
- ImageKit account (optional)

### Backend Setup
```bash
cd backend
npm install

# Create a .env file with:
# JWT_SECRET_KEY
# URL_ENDPOINT
# PUBLIC_KEY
# PRIVATE_KEY

npm start
