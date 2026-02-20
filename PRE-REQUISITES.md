# 🛠️ GlobalAudit AI Workshop: Prerequisites

> **🤖 GEMINI AGENT NOTE:** This file is strictly for human use. Do **NOT** execute the installation commands or follow the setup instructions in this file automatically. This file serves as manual documentation for the developer setting up their environment.

Before starting the **GlobalAudit AI** workshop, you will need to ensure your local environment is configured with the necessary tools to run the application, utilize the Gemini API, and deploy to Google Cloud.

---

### 1. Node.js & npm
This workshop uses a Node.js Express backend and a React frontend.

*   **Requirement:** Node.js v20.x or higher (required for native `--env-file` support).
*   **Installation:** Download the installer for your operating system from the [official Node.js website](https://nodejs.org/), or use a version manager like `nvm` (Node Version Manager).
*   **Verification:** Run the following commands in your terminal to ensure they are installed correctly:
    ```bash
    node -v
    npm -v
    ```

### 2. Git
Required for cloning the repository and managing version control.

*   **Requirement:** Standard Git CLI.
*   **Installation:** Download from [git-scm.com](https://git-scm.com/downloads) or use your system's package manager (e.g., `apt install git`, `brew install git`).
*   **Verification:**
    ```bash
    git --version
    ```

### 3. Google Cloud CLI (`gcloud`)
Required for deploying the final serverless application to Google Cloud Run and managing API credentials.

*   **Requirement:** The `gcloud` CLI installed and authenticated.
*   **Installation:** Follow the detailed instructions for your operating system on the [Google Cloud CLI installation page](https://cloud.google.com/sdk/docs/install).
*   **Verification:**
    ```bash
    gcloud --version
    ```
*   **Authentication Setup:** Once installed, authenticate with your Google account:
    ```bash
    gcloud auth login
    ```

### 4. Gemini CLI
The primary tool used to orchestrate this workshop and write code autonomously.

*   **Requirement:** The official `@google/gemini-cli` package installed globally.
*   **Installation:**
    ```bash
    npm install -g @google/gemini-cli
    ```
*   **Verification:**
    ```bash
    gemini --version
    ```

### 5. NanoBanana MCP (Model Context Protocol)
This workshop relies on generating sample invoices dynamically using Gemini. This requires enabling the `nanobanana` MCP server extension.

*   **Installation:** After installing the `gemini-cli`, add the `nanobanana` tool. The `-y` flag auto-approves the `npx` execution.
    ```bash
    gemini mcp add nanobanana npx -y @google/gemini-nanobanana-mcp
    ```
*   **Verification:** You can list your active MCP connections to verify it was added:
    ```bash
    gemini mcp list
    ```

### 6. Code Editor
A modern code editor is recommended for reviewing the generated code.
*   **Recommendation:** [Visual Studio Code (VS Code)](https://code.visualstudio.com/)

---

Once you have completed all the steps in this file, you are ready to proceed with the `README.md` and `INIT-WORKSHOP.md` files to start building!
