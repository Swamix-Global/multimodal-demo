# 🚀 TIAV Studio: Multimodal AI Workspace

[![FastAPI](https://img.shields.io/badge/FastAPI-005571?style=for-the-badge&logo=fastapi)](https://fastapi.tiangolo.com)
[![React](https://img.shields.io/badge/React-20232A?style=for-the-badge&logo=react&logoColor=61DAFB)](https://react.dev)
[![Vite](https://img.shields.io/badge/Vite-646CFF?style=for-the-badge&logo=vite&logoColor=FFDF00)](https://vite.dev)
[![Android](https://img.shields.io/badge/Android-3DDC84?style=for-the-badge&logo=android&logoColor=white)](https://developer.android.com)
[![NVIDIA NIM](https://img.shields.io/badge/NVIDIA-76B900?style=for-the-badge&logo=nvidia&logoColor=white)](https://build.nvidia.com)
[![OpenRouter](https://img.shields.io/badge/OpenRouter-8C4FFF?style=for-the-badge&logo=openai&logoColor=white)](https://openrouter.ai)

A premium, full-stack **Multimodal AI Workspace** designed to interface seamlessly with advanced language and reasoning models. Built with a responsive **React (Vite)** web client, a native **Android frontend scaffold**, and a high-performance **FastAPI backend** utilizing **NVIDIA NIM** and **OpenRouter** as the model intelligence layer.

---

## 🎨 Conversational Experience & UI/UX

This project has been redesigned to feature an internship-grade, **ChatGPT-style conversational interface**:

*   **Responsive Chat History:** User inputs (right aligned, accent bubble) and streaming assistant responses (left aligned, slate bubble) are preserved in a scrollable, smooth conversation panel.
*   **Token-by-Token Streaming:** Incremental data chunks stream seamlessly from the FastAPI server without log-like visual artifacts.
*   **Blinking Cursor & Typing Dots:** Bouncing three-dot typing indicators appear while the stream initializes, followed by a blinking active cursor.
*   **Full Native Markdown Renderer:** Built-in lightweight, zero-dependency parsing dynamically compiles:
    *   *Headers* (`#`, `##`, `###`), *Lists* (ordered/unordered), and *Blockquotes*.
    *   **Fenced Code Blocks** inside syntax-highlighted containers with dedicated **"Copy Code"** and **"Copy Response"** clipboard utilities.
*   **Generation Abort:** Allows users to cancel/terminate long running streams on demand.

---

## 🏗️ System Architecture

The application isolates standard compute/orchestration from heavy inference tasks:

```text
       ┌────────────────────────┐
       │     React Web App      │ <─── (Vercel)
       │    Android Scaffold    │ <─── (Native Kotlin)
       └───────────┬────────────┘
                   │
                   │ (HTTP Post / SSE Stream / JSON)
                   ▼
       ┌────────────────────────┐
       │  FastAPI Backend Host  │ <─── (Railway / Render)
       └───────────┬────────────┘
                   │
                   │ (Secure API Call)
                   ▼
       ┌────────────────────────┐
       │   OpenRouter Gateway   │ <─── (Model Hub)
       └───────────┬────────────┘
                   │
                   ▼
      [ Switchable Models: Qwen / Claude / GPT / NVIDIA NIM ]
```

---

## 📂 Project Structure

```text
multimodal-demo/
├── backend/                       # FastAPI Server
│   ├── main.py                    # Server endpoints, CORS & SSE Stream logic
│   ├── results.json               # LLM Performance benchmark logs
│   ├── cloud_provider_table.md    # Hosting / Cloud platform comparison
│   ├── model_speeds_table.md      # Latency & TPS speed benchmarks
│   └── requirements.txt           # Python backend dependencies
├── frontend/                      # React Web Application
│   ├── src/
│   │   ├── App.jsx                # UI components & stream hook
│   │   ├── App.css                # Bubble layouts & animations
│   │   └── main.jsx               # React entry point
│   ├── package.json               # Node web configuration
│   └── vite.config.js             # Vite compiler config
├── frontend-android/              # Native Android App Scaffold
│   └── app/                       # Android Studio Kotlin project structure
├── DEPLOYMENT.md                  # Detailed production deployment notes
└── README.md                      # Project documentation
```

---

## 🛠️ Local Installation & Setup

### Prerequisites
*   **Python 3.10+**
*   **Node.js 18+**
*   **Android Studio** (Optional, for mobile frontend compile)

### 1. Backend Setup (FastAPI)
Navigate to the `backend` directory, set up your Python virtual environment, and install dependencies:

```bash
# Navigate to backend
cd backend

# Create virtual environment
python -m venv venv

# Activate virtual environment
# On Windows (PowerShell):
.\venv\Scripts\Activate.ps1
# On macOS/Linux:
source venv/bin/activate

# Install requirements
pip install -r requirements.txt

# Run development server
uvicorn main:app --reload --host 127.0.0.1 --port 8000
```

*   **API Local URL:** `http://127.0.0.1:8000`
*   **Swagger Docs:** `http://127.0.0.1:8000/docs`

---

### 2. Frontend Setup (React / Vite)
Open a new terminal session, navigate to the `frontend` directory, install dependencies, and spin up Vite:

```bash
# Navigate to frontend
cd frontend

# Install package dependencies
npm install

# Run Vite dev server
npm run dev
```

*   **Web App Local URL:** `http://localhost:5173`

---

### 3. Android Scaffold Setup
For compile, preview, or modification of the native mobile frontend layout:
1. Open **Android Studio**.
2. Select **Open an Existing Project** and locate `multimodal-demo/frontend-android/`.
3. Allow Gradle to sync and compile the project structure.
4. Run on a physical Android device or emulator running API level 26+.

---

## 🌐 Production Deployment

### 1. FastAPI Backend (Railway or Render)
*   **Root Directory:** `backend`
*   **Build Command:** `pip install -r requirements.txt`
*   **Start Command:** `uvicorn main:app --host 0.0.0.0 --port $PORT`
*   **Environment Variable:**
    *   `ENV` = `production` *(Automatically opens backend CORS to secure public endpoints).*
    *   `NVIDIA_API_KEY` = `[your-nvidia-api-key]`

### 2. React Web App (Vercel)
*   **Root Directory:** `frontend`
*   **Framework Preset:** `Vite`
*   **Environment Variable:**
    *   `VITE_API_BASE_URL` = `https://your-backend.up.railway.app` *(Points web client to backend instead of localhost)*

> [!WARNING]
> **The Vite Build Gotcha:** Vite binds variables dynamically at **build time**, not runtime. If you add or modify environment variables in Vercel, you **must trigger a manual redeploy (new build)** in the Vercel deployments page for the configurations to compile into your JavaScript assets.

---

## 🔍 Troubleshooting & CORS

#### ❌ "Failed to Fetch" / Localhost Connection
*   *Cause:* The Vercel static build was compiled using the default fallback `'http://127.0.0.1:8000'`.
*   *Fix:* Verify that `VITE_API_BASE_URL` is set in Vercel's Environment Variables and trigger a **Redeploy** to compile the variable into the app.

#### ❌ CORS Origin Blocks
*   *Cause:* Production domain mismatch when credentials are required.
*   *Fix:* Our updated CORS middleware in `main.py` dynamically resolves this. In the absence of a strict list, it defaults to:
    ```python
    origins = ["*"]
    allow_credentials = False
    ```
    This completely eliminates preflight request blockages on deployed services.

---

## 🔮 Future Enhancements
*   [ ] **Multimodal Backend Pipeline:** Transition from local upload scaffolds to deep visual, speech-to-text, and audio-synthesis processing models.
*   [ ] **Persistent Database History:** Integrate PostgreSQL or SQLite to persist user conversation threads across sessions.
*   [ ] **Full Mobile Integration:** Implement full streaming API calls in the Kotlin Android scaffold.

---

## 👥 Author
**Abhinav Shukla**
