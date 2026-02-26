# 🛡️ Workshop: Building "GlobalAudit AI" with Gemini 3 Pro

**Repository:** [GitHub Repository](https://github.com/nuno-joao-andrade-dev/globalauditai.gemini.series)

**GlobalAudit AI** is an enterprise-grade multimodal application that automates corporate document auditing. Using **Gemini 3 Pro**, the system "reads" invoices, extracts structured data, and evaluates compliance against corporate policies—all within a secure, stateless Google Cloud environment.

---

### 🎯 Workshop Objectives

* **Agentic Engineering:** Use `gemini-cli` to build a full-stack Node.js & React app.
* **Multimodal Intelligence:** Extract data from unstructured images using Gemini 3 Pro.
* **GCP Mastery:** Deploy a containerized, serverless application to **Google Cloud Run**.
* **Clean Architecture:** Implement stateless file handling using `/tmp` and Node 20+ native env features.

---

### 📋 Phase 1: Prerequisites & GCP Setup

Participants must prepare their Google Cloud environment before running the AI orchestrator. **Please review the [`PRE-REQUISITES.md`](PRE-REQUISITES.md) file first** for complete installation instructions for required tools like Node.js, `gcloud`, and the `nanobanana` MCP server (which is required for generating the sample invoices).

#### 1. Project & Billing Initialization

Open your [Google Cloud Shell](https://www.google.com/search?q=https://console.cloud.google.com) and execute:

```bash
# 1. Create a unique project 
export MY_PROJECT_ID="global-audit-$(date +%s)"
gcloud projects create $MY_PROJECT_ID --name="GlobalAudit AI Workshop"
gcloud config set project $MY_PROJECT_ID

# 2. Link your Billing Account
# Find your ID with: gcloud billing accounts list
export MY_BILLING_ID="YOUR_BILLING_ID_HERE"
gcloud billing projects link $MY_PROJECT_ID --billing-account=$MY_BILLING_ID

```

#### 2. Service Activation & API Credentials

```bash
# Enable required APIs
gcloud services enable aiplatform.googleapis.com run.googleapis.com artifactregistry.googleapis.com

# Create a Vertex AI API Key
# Navigate to: Vertex AI Studio > API Keys > Create API Key
# Then, export it in your shell:
export GOOGLE_API_KEY="your_new_api_key"
export GOOGLE_GENAI_USE_VERTEXAI="true"
export GOOGLE_CLOUD_LOCATION=global

# if you aren't in the cloud shell
export GOOGLE_CLOUD_PROJECT="..."

# BEFORE CONTINUE PLACE THIS KEYS IN YOUR ~/.bashrc
```

---

### 🚀 Phase 2: Execution & "Vibe Coding"

#### 1. Setup the Repository

```bash
git clone https://github.com/nuno-joao-andrade-dev/globalauditai.gemini.series
cd globalauditai.gemini.series


# Create local env file
echo "GOOGLE_API_KEY=$GOOGLE_API_KEY" > .env
echo "GOOGLE_CLOUD_PROJECT=$GOOGLE_CLOUD_PROJECT" >> .env
echo "GOOGLE_CLOUD_LOCATION=$GOOGLE_CLOUD_LOCATION" >> .env


gcloud auth application-default login

# Install the Gemini CLI tool
npm install -g @google/gemini-cli

# Enable the required MCP tools (like nanobanana for image generation)
gemini extensions install https://github.com/gemini-cli-extensions/nanobanana

gemini --model gemini-3.1-pro-preview

# check that auth and make sure you are using vertexai auth
# Gemini prompt:
/auth

# Choose vertex ai auth

```



#### 2. Run the AI Orchestrator

Open the `INIT-WORKSHOP.md` file and follow the step-by-step instructions to generate your source code using Gemini 3 Pro.

```bash
cat INIT-WORKSHOP.md
```

Or just ask Gemini to follow all the instructions:

```bash
Gemini prompt:
follow all the instructions in all *.md files except the deploy step
```

#### 3. Local Development (with Hot Reload)

The generated `package.json` includes **Nodemon** and uses the native Node 20 `--env-file` flag.

```bash

npm install
npm run dev

```

*Preview the app using the 'Web Preview' button on port 8080.*

---

### 🧪 Phase 3: Testing & Troubleshooting

* **Sample Data:** Use the AI-generated invoices located in the [`/samples`](https://www.google.com/search?q=./samples) directory for your first audit.
* **Debugging:** If the AI-generated code requires tweaks, refer to the **[CHEATSHEET.md](https://www.google.com/search?q=./CHEATSHEET.md)** for specialized debugging prompts.

---

### 🚢 Phase 4: Production Deployment

Deploy the containerized application to Google Cloud Run using the `env.yaml` configuration.

```bash
# Update env.yaml with your specific API Key
sed -i "s/your_key_here/$GOOGLE_API_KEY/" env.yaml

chmod +x deploy.sh
./deploy.sh

```

---

### 🧹 Phase 5: Comprehensive Cleanup

To ensure a zero-cost experience, follow these cleanup steps immediately after the workshop:

```bash
# 1. Delete the Cloud Run service
gcloud run services delete global-audit-ai --region us-central1 --quiet

# 2. Delete the Container Images
gcloud artifacts repositories delete cloud-run-source-deploy --location us-central1 --quiet

# 3. Shutdown the Project (Recommended)
gcloud projects delete $MY_PROJECT_ID --quiet

```

### 📚 Documentation

For a detailed breakdown of how the code works under the hood, explore the generated documentation:

*   **[System Architecture](doc/ARCHITECTURE.md):** The serverless design, data flow, and stateless concepts.
*   **[Backend Implementation](doc/BACKEND.md):** How the Gemini API is prompted and how uploads are handled safely.
*   **[Frontend Implementation](doc/FRONTEND.md):** The React component structure and logic.
*   **[Testing Strategy](doc/TESTING.md):** A deep dive into the Unit and E2E test suites.
*   **[Deployment & DevOps](doc/DEPLOYMENT.md):** Information on the Dockerfile and Cloud Run integration.

---

### 💡 Pro-Tip: Stateless Serverless

This application is designed for **Cloud Run**. All file uploads are stored in `/tmp` (memory-backed storage) and are deleted immediately after processing via `fs.unlinkSync()`. This ensures the instance remains "clean" and performant regardless of how many audits are performed.

---

**Workshop complete!** You have successfully built and deployed a Multimodal AI Auditor using Gemini 3 Pro.
