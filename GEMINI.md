# 🧠 Gemini 3 Pro Master Instructions

This file serves as the **System Context** and **Orchestration Guide** for the `gemini-cli`. It defines the persona, security boundaries, and technical standards for the **GlobalAudit AI** platform.

### 🛡️ The Importance of Agentic Orchestration

The `INIT-WORKSHOP.md` file is the **orchestrator** of this agentic workflow. While Gemini 3 Pro provides the "brain" (reasoning and code generation), the markdown file provides the **chassis** (structure and execution instructions).

* **Deterministic Guardrails:** The guide ensures that non-deterministic AI outputs are funneled into a reliable file structure (`/tmp`, `/public`, `/tests`).
* **Systemic Coherence:** By following the prompts from this file, we ensure that the generated backend, frontend, and DevOps layers share a unified dependency graph and security model.
* **Stateless Reliability:** The orchestrator enforces serverless best practices, specifically the use of ephemeral storage to prevent memory leaks in production environments like Cloud Run.

---

### 🏗️ 1. Backend: Secure Auditor Logic

**Constraint:** All file handling must be stateless. Use `@google/genai` for enterprise-grade access.

> **System Prompt:**
> "Act as a Senior Backend Architect. Write a Node.js Express server (`index.js`).
> * **AI Model:** Initialize `gemini-3.1-pro-preview` using the `@google/genai` SDK.
> * **SDK Configuration:** Initialize `GoogleGenAI` with `apiKey: process.env.GOOGLE_CLOUD_API_KEY`.
> * **Storage Rules:** Use `multer` diskStorage pointed exclusively to `/tmp/uploads`.
> * **Cleanup Policy:** You MUST implement an `fs.unlinkSync(path)` call within a `finally` block to delete every uploaded file immediately after model inference.
> * **Response Format:** Extract `Vendor`, `Date`, `Amount`, and `TaxID`. Treat 'Tax ID', 'VAT ID', 'VAT No', 'VAT', and 'TIN' as the exact same thing for compliance validation. Note that these IDs are often alphanumeric. Scan the entire document carefully for any alphanumeric string associated with these labels. IMPORTANT: The extracted TaxID MUST be an actual reference number (e.g., 'GB123456789', '12-3456789'). If the text found is a placeholder like 'Insert your VAT ID', '[Your Tax ID]', or similar instructional text, treat it as missing. Always output the valid found value under the 'TaxID' key. Set `ComplianceRisk` to true if a valid `TaxID` is missing or empty. Flag `ManualReview` as true if `Amount > 5000` OR if `ComplianceRisk` is true. Return ONLY valid JSON."
> 
> 

---

### ⚛️ 2. Frontend: React Compliance Portal

**Constraint:** High interactivity with zero build overhead.

> **System Prompt:**
> "Act as a Lead Frontend Engineer. Generate `public/index.html` using React 18 and Tailwind CSS via CDN.
> * **Components:** Create a `FileUpload` component with a preview and an `AuditDashboard` table to display the extracted Vendor, Date, Amount, and Tax ID.
> * **State Management:** Display a 'Scanning...' state and map JSON audit flags to visual risk indicators. If TaxID is missing, render an amber badge "Missing (Compliance Risk)". Show a red badge if ManualReview is true, otherwise a green badge for Auto-Approved.
> * **UX:** Professional corporate 'Slate' theme."
> 
> 

---

### 🧪 3. DevOps & Containerization

**Constraint:** Production-ready containerization.

> **System Prompt:**
> "Generate the following supporting infrastructure:
> 1. **package.json:** Include `express`, `@google/genai`, and `multer`. Set `dev` script to `nodemon index.js` (relying on system environment variables).
> 2. **Dockerfile:** Use `node:20-slim`. Ensure `/tmp/uploads` is created with write permissions (`777`).
> 3. **deploy.sh:** Write a gcloud command to deploy to Cloud Run using `--allow-unauthenticated` (environment variables will be injected by the platform)."
> 
> 

---

### 🌍 Environment Configuration

**Constraint:** Do NOT use `.env` files.

The application must rely exclusively on system environment variables for configuration, particularly `GOOGLE_CLOUD_PROJECT`. This ensures compatibility with Cloud Run and proper authentication via Application Default Credentials (ADC).

> **System Prompt (Environment):**
> "Ensure all code accesses configuration via `process.env.VARIABLE_NAME` directly, without importing `dotenv` or loading `.env` files."


---

### 🖼️ 4. Test Data: Multimodal Stress Test Suite

**Constraint:** Create a diverse set of 6 test cases (3 OK and 3 NOT OK) to verify compliance logic.

> **System Prompt (Invoices Generator):**
> "Generate 6 distinct high-fidelity images for audit stress testing:
> 
> **OK Invoices:**
> 1. **invoice_ok_1.png:** A standard compliant invoice from 'Office Depot' for '$150.00', clearly showing a Tax ID number.
> 2. **invoice_ok_2.png:** A clean, professional consulting invoice from 'Acme Corp' for '$3,200.00' with a visible Tax ID.
> 3. **invoice_ok_3.png:** A simple software subscription invoice from 'SaaS Inc' for '$99.99' with a visible alphanumeric VAT ID.
> 
> **NOT OK Invoices:**
> 4. **invoice_not_ok_1.png:** A high-value invoice from 'Azure Cloud Services' for '$12,400.50', clearly showing a Tax ID number (This should trigger the 'Manual Review' flag based on amount only).
> 5. **invoice_not_ok_2.png:** A messy, handwritten-style receipt from a local vendor that intentionally leaves out the 'Tax ID' field (This should trigger the 'Compliance Risk' flag).
> 6. **invoice_not_ok_3.png:** A handwritten invoice from a contractor for '$450.00' with no TIN or Tax ID visible anywhere (This should trigger the 'Compliance Risk' flag).
> 
> All images should look professional, include varying fonts, and have realistic noise to test Gemini's OCR capabilities."

---

---

### 🤖 5. Automated Testing Suite

**Constraint:** Ensure comprehensive coverage through unit and end-to-end (E2E) testing without incurring unnecessary API costs during CI.

> **System Prompt (Tests):**
> "Act as a QA Automation Engineer. Generate the following test suites:
> 1. **Unit Tests (`tests/unit.test.js`):** Use `jest` and `supertest`. Mock the `@google/genai` SDK to avoid real API calls. Test the `/audit` endpoint to verify the logic for Auto-Approval, High Amount (Manual Review), and Missing Tax ID (Compliance Risk).
> 2. **E2E Tests (`tests/e2e.test.js`):** Write a script that uploads the actual images from the `samples/` directory to a running instance of the server and verifies the final HTTP responses.
> 3. **Package Update:** Update `package.json` to include `jest` and `supertest` as devDependencies, and configure the `scripts` section in `package.json` with `"test": "jest tests/unit.test.js"` for unit tests and `"test:e2e": "node tests/e2e.test.js"` for end-to-end tests."

### 🚀 6. Execution & Verification

**Constraint:** Ensure the developer runs the test suites to validate the entire setup after code generation.

> **System Prompt (Execution):**
> "Instruct the user to run the following commands to execute both the unit tests and the end-to-end tests to verify the system's compliance logic and OCR capabilities:
> \`\`\`bash
> npm run test
> npm run test:e2e
> \`\`\`"

### 💡 Implementation Note

To execute the multi-file generation, open `INIT-WORKSHOP.md` and follow the step-by-step instructions.
