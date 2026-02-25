## DevOps Assignment – FastAPI & Next.js

This project contains a **FastAPI backend** and a **Next.js frontend**.  
You can run it either **locally** (Python & Node) or with **Docker + docker-compose**.

---

## 1. Project Structure

- **backend**: FastAPI application
  - `app/main.py`: API with `/api/health` and `/api/message`
  - `requirements.txt`: Python dependencies
  - `Dockerfile`: backend container image
- **frontend**: Next.js application
  - `pages/index.js`: main page calling the backend
  - `.env.local`: frontend configuration (backend URL)
  - `package.json`: Node/Next dependencies and scripts
  - `Dockerfile`: frontend container image
- **docker-compose.yml**: runs backend + frontend together

---

## 2. Running Locally (No Docker)

### 2.1 Backend (FastAPI)

From the project root:

```bash
cd backend
python3 -m venv venv
source venv/bin/activate          # Windows: .\venv\Scripts\activate
pip install -r requirements.txt
uvicorn app.main:app --reload --host 0.0.0.0 --port 8000
```

The backend will be available at:

- `http://localhost:8000/api/health`
- `http://localhost:8000/api/message`

### 2.2 Frontend (Next.js)

Open a new terminal, from the project root:

```bash
cd frontend
npm install                       # or: yarn
```

Configure the backend URL for local development in `frontend/.env.local`:

```bash
NEXT_PUBLIC_API_URL=http://localhost:8000
```

Then start the dev server:

```bash
npm run dev                       # or: yarn dev
```

Frontend will be available at:

- `http://localhost:3000`

If everything is working, the page will show:

- A status that the backend is connected
- The message: **"You've successfully integrated the backend!"**
- The current backend URL being used

---

## 3. Running with Docker & docker-compose

### 3.1 Build and Run

From the project root:

```bash
docker-compose up --build
```

This will:

- Build and start **backend** on port **8000**
- Build and start **frontend** on port **3000**

Access:

- Backend (direct): `http://localhost:8000/api/health`
- Frontend: `http://localhost:3000`

The `docker-compose.yml` file:

- Builds the backend from `backend/Dockerfile` and exposes `8000:8000`
- Builds the frontend from `frontend/Dockerfile`
- Passes `NEXT_PUBLIC_API_URL=http://localhost:8000` so the browser calls the backend on the host at port 8000

To stop the containers:

```bash
docker-compose down
```

---

## 4. Backend Docker Image (FastAPI)

`backend/Dockerfile` uses Python 3.12, installs `requirements.txt`, copies the `app` folder, and runs Uvicorn:

- Exposes port **8000**
- Command:
  - `uvicorn app.main:app --host 0.0.0.0 --port 8000`

You can build and run just the backend container:

```bash
cd backend
docker build -t devops-backend .
docker run --rm -p 8000:8000 devops-backend
```

---

## 5. Frontend Docker Image (Next.js)

`frontend/Dockerfile` is a multi-stage image:

- **deps** stage: installs NPM dependencies with `npm ci`
- **builder** stage:
  - Copies source code
  - Uses build-time argument `NEXT_PUBLIC_API_URL`
  - Runs `npm run build`
- **runner** stage:
  - Copies the built Next.js app
  - Runs `npm run start` in production mode on port **3000**

You can build and run just the frontend container (assuming backend is already running on the host at `http://localhost:8000`):

```bash
cd frontend
docker build -t devops-frontend --build-arg NEXT_PUBLIC_API_URL=http://localhost:8000 .
docker run --rm -p 3000:3000 devops-frontend
```

Then open `http://localhost:3000`.

---

## 6. Changing the Backend URL (Public URLs)

The frontend uses the environment variable **`NEXT_PUBLIC_API_URL`** to know where to call the backend.

### 6.1 Local `.env.local`

In `frontend/.env.local`:

```bash
NEXT_PUBLIC_API_URL=https://your-backend-url.com
```

Restart `npm run dev` (or rebuild for production) after changing this.

### 6.2 Docker / Production

When building the frontend Docker image:

```bash
docker build -t devops-frontend \
  --build-arg NEXT_PUBLIC_API_URL=https://your-backend-url.com \
  .
```

Or, in `docker-compose.yml`, adjust the build ARG and environment:

- `NEXT_PUBLIC_API_URL=https://your-backend-url.com`

Then rebuild:

```bash
docker-compose up --build
```

---

## 7. Testing the Integration

1. Ensure both backend and frontend are running (either locally or via Docker).
2. Open the frontend in a browser:
   - Local: `http://localhost:3000`
   - Or your deployed frontend URL.
3. Confirm that:
   - Backend status is healthy.
   - Message: **"You've successfully integrated the backend!"** is displayed.
   - The backend URL shown matches your configured `NEXT_PUBLIC_API_URL`.

---

## 8. Exporting This Guide to PDF

To create a **PDF** of these instructions:

- Open this `README.md` in any Markdown viewer (for example, GitHub, VS Code Markdown preview, or a browser-based Markdown viewer).
- Use **Print → Save as PDF** to export it.

You can then submit or share that PDF as documentation of how to build and run the project (locally and with Docker / docker-compose).

# DevOps Assignment

This project consists of a FastAPI backend and a Next.js frontend that communicates with the backend.

## Project Structure

```
.
├── backend/               # FastAPI backend
│   ├── app/
│   │   └── main.py       # Main FastAPI application
│   └── requirements.txt    # Python dependencies
└── frontend/              # Next.js frontend
    ├── pages/
    │   └── index.js     # Main page
    ├── public/            # Static files
    └── package.json       # Node.js dependencies
```

## Prerequisites

- Python 3.8+
- Node.js 16+
- npm or yarn

## Backend Setup

1. Navigate to the backend directory:
   ```bash
   cd backend
   ```

2. Create a virtual environment (recommended):
   ```bash
   python -m venv venv
   source venv/bin/activate  # On Windows: .\venv\Scripts\activate
   ```

3. Install dependencies:
   ```bash
   pip install -r requirements.txt
   ```

4. Run the FastAPI server:
   ```bash
   uvicorn app.main:app --reload --port 8000
   ```

   The backend will be available at `http://localhost:8000`

## Frontend Setup

1. Navigate to the frontend directory:
   ```bash
   cd frontend
   ```

2. Install dependencies:
   ```bash
   npm install
   # or
   yarn
   ```

3. Configure the backend URL (if different from default):
   - Open `.env.local`
   - Update `NEXT_PUBLIC_API_URL` with your backend URL
   - Example: `NEXT_PUBLIC_API_URL=https://your-backend-url.com`

4. Run the development server:
   ```bash
   npm run dev
   # or
   yarn dev
   ```

   The frontend will be available at `http://localhost:3000`

## Changing the Backend URL

To change the backend URL that the frontend connects to:

1. Open the `.env.local` file in the frontend directory
2. Update the `NEXT_PUBLIC_API_URL` variable with your new backend URL
3. Save the file
4. Restart the Next.js development server for changes to take effect

Example:
```
NEXT_PUBLIC_API_URL=https://your-new-backend-url.com
```

## For deployment:
   ```bash
   npm run build
   # or
   yarn build
   ```

   AND

   ```bash
   npm run start
   # or
   yarn start
   ```

   The frontend will be available at `http://localhost:3000`

## Testing the Integration

1. Ensure both backend and frontend servers are running
2. Open the frontend in your browser (default: http://localhost:3000)
3. If everything is working correctly, you should see:
   - A status message indicating the backend is connected
   - The message from the backend: "You've successfully integrated the backend!"
   - The current backend URL being used

## API Endpoints

- `GET /api/health`: Health check endpoint
  - Returns: `{"status": "healthy", "message": "Backend is running successfully"}`

- `GET /api/message`: Get the integration message
  - Returns: `{"message": "You've successfully integrated the backend!"}`
