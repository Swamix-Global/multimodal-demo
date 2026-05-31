# TIAV Multimodal App — Production Deployment Guide

> **Scope:** Unified guide for deploying the TIAV FastAPI backend to Railway/Render and the React/Vite frontend to Vercel.
> **Last updated:** May 2026

---

## 🏗️ Architectural Diagram

```text
       [ React Frontend ] (Deployed on Vercel)
               │
               ▼  (HTTP POST / JSON / Stream)
     [ FastAPI Backend Server ] (Deployed on Railway / Render)
               │
               ▼  (External API Calls)
          [ OpenRouter ]
               │
               ▼  (LLM Inference)
      [ Switchable Models ] (Qwen / Claude / GPT / etc.)
```

---

## 🟢 Part 1: Frontend Deployment (Vercel)

The React frontend compiles down to static assets using **Vite**.

### 1. Environment Variable to Set in Vercel
To point your production frontend to your deployed backend instead of `localhost`, you must configure a specific environment variable in the Vercel dashboard:

* **Variable Name:** `VITE_API_BASE_URL`
* **Variable Value:** `https://your-backend-url.railway.app` (replace with your active backend endpoint URL)

> [!IMPORTANT]
> - Vite automatically embeds variables starting with `VITE_` into the production client bundle during the build step.
> - **Do not add a trailing slash** at the end of the URL (e.g. use `https://tiav-backend.up.railway.app`, not `https://tiav-backend.up.railway.app/`).

### 2. Deployment Steps
1. Push your repository to GitHub.
2. Log into the [Vercel Dashboard](https://vercel.com).
3. Click **Add New** → **Project**, and select your Git repository.
4. Set the following project configurations:
   - **Framework Preset:** Vite
   - **Root Directory:** `frontend`
5. Expand the **Environment Variables** section and input:
   - Key: `VITE_API_BASE_URL`
   - Value: `https://your-backend-url.railway.app`
6. Click **Deploy**. Vercel will build the frontend and serve it at a public `https://*.vercel.app` domain.

---

## 🔵 Part 2: Backend Deployment (Railway or Render)

The backend is built in **FastAPI** and uses **uvicorn** for high-throughput ASGI serving.

### 1. Deployment Requirements
Make sure the `requirements.txt` file is present in the `backend/` directory so the hosting provider can install all dependencies. 
Your `backend/requirements.txt` currently contains:
```text
fastapi
uvicorn
requests
python-multipart
```
Additionally, ensure a dynamic port binding command is used since Railway and Render inject a random `$PORT` environment variable.

### 2. Environment Variables to Set
To secure CORS and connect to NVIDIA's NIM endpoints:

1. **`ENV`**: Set to `production`. (Configures backend CORS to allow all origins `["*"]` dynamically).
2. **`ALLOWED_ORIGINS`** (Optional): A comma-separated list of exact allowed domain origins (e.g., `https://your-frontend.vercel.app`) if you wish to lock down cross-origin requests instead of using `"*"`.
3. **`PORT`**: Will be injected automatically by Render or Railway.

### 3. Deployment Steps (Railway — Recommended)
1. Go to the [Railway Dashboard](https://railway.app).
2. Click **New Project** → **Deploy from GitHub repo**.
3. Select your repository.
4. Set the **Root Directory** or **Build Command** configurations:
   - **Root Directory:** `backend`
   - **Start Command:** `uvicorn main:app --host 0.0.0.0 --port $PORT`
5. Under **Variables**, add:
   - `ENV`: `production`
6. Railway will automatically build, provision an SSL certificate, and assign a public domain (e.g., `https://backend-production.up.railway.app`).

### 4. Deployment Steps (Render)
1. Go to the [Render Dashboard](https://render.com).
2. Click **New** → **Web Service** and connect your GitHub repo.
3. Set the following details:
   - **Root Directory:** `backend`
   - **Runtime:** Python
   - **Build Command:** `pip install -r requirements.txt`
   - **Start Command:** `uvicorn main:app --host 0.0.0.0 --port $PORT`
4. Under **Advanced** → **Environment Variables**, add:
   - `ENV`: `production`
5. Click **Create Web Service**.

---

## 🏆 Production Testing & Verification

Once both frontend and backend are successfully deployed:

1. Go to your frontend Vercel URL (e.g., `https://tiav-multimodal-studio.vercel.app`).
2. Type a message in the chat box and press **Enter**.
3. The network tab will make cross-origin requests to `https://your-backend-url.railway.app/text`.
4. If there is a connection issue, the chat box will immediately display:
   > *"Failed to connect to the backend server. If in production, ensure your VITE_API_BASE_URL environment variable is set to your active backend address."*
5. Once working, standard tokens will stream smoothly word-by-word into conversational ChatGPT bubbles!
