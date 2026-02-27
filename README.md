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

---

## 9. DevOps Assignment – Design & Architecture Answers

The following sections answer the questions from the **“DevOps Assignment (Full-Time Role)”** document in a narrative, human-readable way.  
The focus is on **infrastructure design, tradeoffs, and operability**, using the small FastAPI + Next.js app in this repository.

### 9.1 High-Level Approach

- **Application**: The provided FastAPI backend and Next.js frontend, packaged into Docker images.
- **Local / small setup**: `docker-compose` (as defined in this repo) is used for local development and for a simple single-host deployment.
- **Cloud platforms chosen**:
  - **AWS** – primary cloud.
  - **GCP** – secondary cloud.
- **Runtime model**: In both clouds, the app is deployed as **managed containers**, not raw VMs and not Kubernetes, to keep operations simple while still supporting scaling and reliability.
- **Environments** in each cloud: **dev**, **staging**, and **prod**, with different scaling and safety settings (details below).

The same Docker images built from this repository are reused across **local**, **AWS**, and **GCP**, which simplifies CI/CD and parity between environments.

---

### 9.2 Cloud & Region Selection (Requirement 1⃣)

#### AWS

- **Region**: `ap-south-1` (Mumbai).
- **Why this region**:
  - Close to expected primary user base (India / nearby regions), so **good latency** from browsers to the frontend.
  - Generally **cheaper** than some US/EU regions while still offering all the managed services needed (ECS, ALB, RDS/managed DBs if needed later, etc.).
  - Mature ecosystem and availability of documentation and support.
- **Tradeoffs**:
  - Slightly higher latency for users in the US/EU compared to `us-east-1` or `eu-west-1`.
  - Pricing may differ per service; if most traffic later comes from another geography, the region choice may need to be revisited.

#### GCP

- **Region**: `asia-south1` (Mumbai).
- **Why this region**:
  - Mirrors the AWS choice to keep **latency** low for the same primary user base.
  - Close physical locality between AWS and GCP regions simplifies multi-cloud troubleshooting and data residency reasoning.
- **Tradeoffs**:
  - Similar: if traffic becomes very global, a multi-region GCP setup might be required.

Overall, both regions are chosen to **optimize latency for the expected majority of users** and to reduce cost while still offering all necessary managed services.

---

### 9.3 Compute & Runtime Decisions (Requirement 2⃣)

For both clouds, the app is containerized using the Dockerfiles in `backend/` and `frontend/`.

#### AWS

- **Choice**: **AWS ECS on Fargate** (managed containers).
- **Why not VMs (EC2 only)**:
  - Would require manual scaling groups, OS patching, and capacity management.
  - More operational overhead for such a small application.
- **Why not Kubernetes (EKS)**:
  - Kubernetes is powerful but adds significant operational complexity (cluster upgrades, control-plane cost, networking add-ons, Ingress controllers).
  - For a small service without complex microservices requirements, this would be overkill.
- **Why Fargate**:
  - No servers to manage; task-based scaling.
  - Easy to declare CPU/memory per service.
  - Integrates well with ALB for HTTP(S) traffic.

#### GCP

- **Choice**: **Cloud Run** (fully managed serverless containers).
- **Why not VMs (GCE)**:
  - Similar reasoning as EC2: more manual work for patching, capacity, and HA.
- **Why not Kubernetes (GKE)**:
  - Again, overkill in terms of control-plane complexity for this small app.
- **Why Cloud Run**:
  - Deploy Docker images directly.
  - Autoscaling is built-in, including scale-to-zero for dev and possibly staging.
  - Simple integration with HTTPS and IAM.

#### Impact on scalability, complexity, and cost

- **Scalability**:
  - Both ECS+Fargate and Cloud Run support horizontal scaling out based on CPU, memory, or request metrics.
- **Operational complexity**:
  - Both are **much simpler** than running Kubernetes or raw VMs.
- **Cost**:
  - Pay-per-use model (Cloud Run) and task-level billing (Fargate) are appropriate for a low-traffic, small application.

---

### 9.4 Networking & Traffic Flow (Requirement 3⃣)

#### AWS

- **VPC**:
  - A dedicated VPC with public and private subnets.
- **Frontend**:
  - Deployed as an ECS Fargate service behind an **Application Load Balancer (ALB)**.
  - ALB is public, with HTTPS termination (ACM-managed certificates).
- **Backend**:
  - Also an ECS Fargate service, running the FastAPI app.
  - Runs in **private subnets**.
  - Only receives traffic from the frontend ECS service (or from the ALB via internal routing).
- **Security groups / firewall**:
  - ALB SG: allows inbound TCP/443 from the internet, forwards traffic to frontend service.
  - Frontend service SG: accepts traffic only from ALB SG.
  - Backend service SG: accepts traffic only from frontend service SG (and optionally internal admin/network).
- **Frontend ↔ backend communication**:
  - Within the AWS network, the frontend uses an internal DNS name like `http://backend.service.local:8000` (or an internal load balancer target).
  - For the browser-based frontend, `NEXT_PUBLIC_API_URL` is configured to a public URL that routes to the backend through appropriate ingress, never using `http://backend:8000` directly from the internet.

#### GCP

- **Networking**:
  - Both frontend and backend are deployed as separate **Cloud Run services**.
  - Public access is enabled for the frontend; backend can be restricted to accept only traffic from the frontend (using IAM or internal-only configuration).
- **Ingress**:
  - Cloud Run provides managed HTTPS endpoints.
  - Optionally, **Cloud Load Balancer** can sit in front of Cloud Run for custom domains and shared SSL certificates.
- **Security**:
  - Backend Cloud Run service is configured to require authenticated calls (service-to-service IAM) where possible.
  - Frontend talks to backend using `NEXT_PUBLIC_API_URL` pointing to the backend’s HTTPS endpoint or through a unified domain + path-based routing.

#### Architecture diagrams (conceptual)

Even without rendered images, the design is:

- **AWS**: Internet → ALB (HTTPS) → Frontend ECS service → Backend ECS service (private).
- **GCP**: Internet → HTTPS endpoint / Load Balancer → Frontend Cloud Run → Backend Cloud Run (restricted).

---

### 9.5 Environment Separation (Requirement 4⃣)

For **each cloud** there are three environments:

- **dev**:
  - Lowest cost, minimal resources.
  - May allow scale-to-zero (Cloud Run) or a single small Fargate task.
  - Used for testing branches and experimental features.
- **staging**:
  - Mirrors prod configuration as closely as possible (same container sizes, similar autoscaling rules) but with **lower max capacity**.
  - Used for pre-release verification and integration tests.
- **prod**:
  - Higher minimum capacity and more conservative autoscaling limits.
  - Stricter network rules and monitoring/alerting thresholds.

In IaC, environments are separated by:

- **Different state files / workspaces** per environment (see 9.8).
- **Distinct naming conventions** for resources, e.g.:
  - `dev-devops-frontend`, `stg-devops-frontend`, `prod-devops-frontend`.

---

### 9.6 Scalability & Availability (Requirement 5⃣)

- **What scales automatically**:
  - **AWS**: ECS services can scale tasks based on CPU, memory, or request/load metrics.
  - **GCP**: Cloud Run services autoscale based on incoming request volume and concurrency.
  - Load balancers and managed networking components adjust capacity transparently.
- **What does not scale automatically**:
  - The underlying database (if/when introduced) might have manual sizing limits.
  - Any external third-party dependencies or rate-limited APIs.
- **Handling traffic spikes**:
  - Prod environments are configured with:
    - Higher maximum task counts (ECS) and concurrent instances (Cloud Run).
    - Alerts when utilization passes thresholds so capacity can be adjusted before saturation.
- **Minimum availability guarantees**:
  - At least **two tasks/instances** in different availability zones for prod where applicable.
  - Health checks from ALB / Cloud Run remove unhealthy instances from rotation.

---

### 9.7 Deployment Strategy (Requirement 6⃣)

At a high level, deployments are split into **infrastructure** and **application**:

- **Infrastructure deploys**:
  - Performed via IaC (Terraform).
  - Changes are rolled out environment by environment: dev → staging → prod.
  - For risky changes, prod is updated only after staging passes smoke tests.
- **Application deploys**:
  - CI pipeline builds Docker images for `backend` and `frontend`.
  - Images are pushed to container registries (ECR for AWS, Artifact Registry or GCR for GCP).
  - Services (ECS tasks / Cloud Run services) are updated to use the new image tag.

**Downtime expectations**:

- ECS and Cloud Run both support **rolling** or **blue/green-style** rollouts so:
  - New tasks/instances start and become healthy.
  - Old ones are drained and then stopped.
  - This keeps downtime close to zero for typical deployments.

**Rollback strategy**:

- If a deployment is unhealthy (failed health checks, increased error rates):
  - ECS: revert the service to the previous task definition revision.
  - Cloud Run: roll back to the last known good revision.
  - Because infra is declarative, rolling back application versions is low-risk.

**Failure handling during deploys**:

- Health checks fail → load balancers stop sending traffic to bad tasks.
- CI marks the deployment as failed and does not promote to the next environment.

---

### 9.8 Infrastructure as Code & State Management (Requirement 7⃣)

**Infrastructure as Code**:

- All cloud resources (VPCs, ECS services, Cloud Run services, load balancers, IAM roles, etc.) are defined using **Terraform**.
- This ensures reproducible, reviewable changes and supports code review for infra.

**State storage & isolation**:

- **AWS**:
  - Terraform state is stored in an **S3 bucket** (e.g. `devops-assignment-tfstate`).
  - **DynamoDB** table is used for **state locking** to prevent concurrent applies.
  - Separate state files or workspaces per environment: `dev`, `staging`, `prod`.
- **GCP**:
  - State is stored in a **GCS bucket** dedicated for Terraform state.
  - Separate prefixes or workspaces per environment.

**Locking**:

- Terraform’s remote backend locking (DynamoDB on AWS, or alternative locking in GCP) ensures only one apply operation can modify state at a time.

**Recovery considerations**:

- State buckets are versioned, so if state is corrupted or a bad apply happens, previous versions can be restored.
- Critical resources use **Terraform lifecycle** settings carefully, to avoid accidental deletion of key infrastructure (e.g. buckets that contain logs or state).

Ignoring state management would be a critical mistake; hence, the design explicitly treats it as a first-class concern.

---

### 9.9 Security & Identity (Requirement 8⃣)

**Deployment identity (CI/CD)**:

- CI (e.g. GitHub Actions) uses **OIDC-based short-lived credentials** to assume roles in AWS and access service accounts in GCP.
- No long-lived static cloud keys are stored in GitHub secrets where avoidable.

**Human access control**:

- **AWS**:
  - Developers use IAM roles with least privilege.
  - Administrative access is gated behind MFA.
- **GCP**:
  - Developers use IAM roles bound to their identities or groups, again with least privilege.

**Secret storage & injection**:

- **AWS**:
  - Secrets (DB credentials, API keys, etc.) are stored in **AWS Secrets Manager** or **SSM Parameter Store**.
  - ECS tasks read secrets at runtime via IAM roles.
- **GCP**:
  - Secrets are stored in **Secret Manager**.
  - Cloud Run services access secrets via service account permissions.

**Least privilege principles**:

- Each service (frontend, backend, CI pipeline) has its own IAM role/service account.
- Roles are granted only the permissions strictly required for that component.
- No secrets are baked into:
  - Git repositories.
  - Docker images.
  - CI logs.

---

### 9.10 Failure & Operational Thinking (Requirement 9⃣)

For each deployment, consider:

- **Smallest failure unit**:
  - A single ECS task, a single Cloud Run instance, or a single AZ.
- **What breaks first**:
  - Individual container instance failures (e.g. bad deploy, resource exhaustion).
  - Misconfigured environment variables or network rules.
- **What self-recovers**:
  - ECS and Cloud Run automatically restart unhealthy tasks/instances.
  - Load balancers stop routing to failing instances.
- **What requires human intervention**:
  - Systemic misconfigurations (wrong image, bad migrations).
  - Capacity planning issues (limits too low) that cause sustained throttling.

**Alerting philosophy**:

- **Actionable alerts**:
  - High error rates (5xx) or health check failures in prod.
  - Saturation of CPU/memory for a sustained period.
  - Exhaustion of quotas or rate limits from external services.
- **Non-actionable / noisy things**:
  - Single transient task restarts (unless frequent).
  - Short-lived spikes that fully self-recover.

**What wakes someone up at 2 AM**:

- Only issues that:
  - Impact prod availability or error rates for a meaningful duration.
  - Suggest data loss/corruption, security incident, or critical SLA breach.

---

### 9.11 Future Growth Scenario (Requirement 🔟)

Assume:

- Traffic increases **10×**.
- A new backend service is added.
- One client demands **stricter isolation**.
- Another demands **region-specific data**.

**What changes**:

- Scale out ECS tasks / Cloud Run instances and possibly increase CPU/memory sizes.
- Introduce:
  - A separate backend service, with its own ECS service / Cloud Run service and clear API contracts.
  - A more robust database tier (managed relational DB or highly available NoSQL).
- For stricter isolation:
  - Provision a **logically separate environment** (and potentially separate AWS account / GCP project) dedicated to that client.
  - Isolate VPCs and IAM roles, with dedicated resources.
- For region-specific data:
  - Introduce region-specific data stores (e.g. region-specific RDS/Cloud SQL instances).
  - Route user traffic to the right region using DNS or global load balancing.

**What remains unchanged**:

- The use of containers and managed runtimes (Fargate, Cloud Run).
- The IaC approach and environment separation practices.
- The CI/CD pipeline structure (build once, deploy the same images).

**Early decisions that help**:

- Choosing managed containers instead of tightly coupling to VM-specific tooling.
- Keeping infra fully defined in Terraform.
- Designing clear separation between environments and between frontend/backend.

**Early decisions that could hurt**:

- Single-region design may need refactoring for multi-region replication.
- Any tight coupling between services (e.g. direct DB access) would make future microservice extraction harder.

---

### 9.12 What We Did NOT Do (Intentional Omissions)

To keep the solution appropriately scoped for a small application:

- **No Kubernetes**:
  - Intentionally avoided EKS/GKE to limit operational complexity.
- **No multi-region active-active setup (yet)**:
  - For early stages, single-region per cloud is acceptable.
  - Multi-region adds significant cost and complexity in data replication and failover.
- **No complex service mesh or API gateway**:
  - Simple load balancers and direct service-to-service communication are sufficient.
- **No full observability stack (e.g. custom dashboards + distributed tracing)**:
  - Basic metrics and logs via CloudWatch / Cloud Monitoring are considered enough initially.
- **No persistent database layer implemented in this assignment**:
  - The sample app does not require it; however, the design leaves room to add it later.

These omissions are deliberate to keep the system **maintainable and cost-effective** at the current scale, while leaving a clear path to grow as requirements become more demanding.

---

### 9.13 README Mapping to Assignment Deliverables

- **How to run the application**: Sections 2 and 3 of this README.
- **High-level architecture overview**: Sections 9.1–9.4.
- **Cloud & region selection**: Section 9.2.
- **Compute, networking, scaling, and availability**: Sections 9.3–9.7.
- **Infrastructure as Code & state management**: Section 9.8.
- **Security & identity**: Section 9.9.
- **Failure & operational behavior**: Section 9.10.
- **Future growth strategy**: Section 9.11.
- **What we did NOT do**: Section 9.12.

Links to:

- **Hosted URLs** (frontend/backends in AWS and GCP),
- **External architecture document**, and
- **Demo video**

should be added by filling in the relevant sections above once those resources are available.

