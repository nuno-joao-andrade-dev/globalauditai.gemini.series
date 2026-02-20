# Deployment & DevOps

GlobalAudit AI is configured for a seamless deployment to Google Cloud Run, a managed compute platform that automatically scales stateless containers.

## Configuration Files

### 1. `Dockerfile`
The Dockerfile defines the container image.
*   **Base Image:** `node:20-slim`. A lightweight Debian-based Node.js environment.
*   **Dependency Installation:** Copies `package.json` and runs `npm install --production` to omit development dependencies like Jest.
*   **Filesystem Setup:** Explicitly creates the `/tmp/uploads` directory and assigns it `777` permissions to ensure the `multer` middleware never encounters `EACCES` permission errors when writing uploaded images.
*   **Startup:** Exposes port `8080` (Cloud Run default) and executes `npm start` (`node index.js`).

### 2. `deploy.sh`
A bash script containing the exact `gcloud` command required to deploy the source code to Cloud Run.
*   `--source .`: Instructs Cloud Build to automatically build the Docker image from the local source directory.
*   `--region us-central1`: Specifies the deployment region.
*   `--allow-unauthenticated`: Makes the service publicly accessible over the internet.

## Environment Variables

The application intentionally avoids `.env` files (like `dotenv`) to strictly rely on native system environment variables. This prevents accidentally committing secrets to version control and aligns with 12-factor app methodologies.

In production (Cloud Run), environment variables must be injected via the platform configuration.

Required Environment Variables:
*   `GOOGLE_CLOUD_API_KEY`: The API key generated from Google Cloud / Vertex AI required to authenticate the `@google/genai` SDK.
*   `GOOGLE_CLOUD_PROJECT`: Required natively by Google Cloud SDKs to identify the host project.