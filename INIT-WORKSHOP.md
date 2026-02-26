# GlobalAudit AI Workshop Initialization

Execute the following tasks to set up the workshop environment.

## Task 1: Environment Setup
Ensure the following directories exist:
- `public/`
- `samples/`
- `tests/`

## Task 2: Backend Implementation
**File:** `index.js`
**Prompt:**
Act as a Senior Backend Architect. Write a Node.js Express server (`index.js`).
* **AI Model:** Initialize `gemini-3.1-pro-preview` using the `@google/genai` SDK.
* **SDK Configuration:** Initialize `GoogleGenAI` with `apiKey: process.env.GOOGLE_API_KEY`.
* **Storage Rules:** Use `multer` diskStorage pointed exclusively to `/tmp/uploads`.
* **Cleanup Policy:** You MUST implement an `fs.unlinkSync(path)` call within a `finally` block to delete every uploaded file immediately after model inference.
* **Environment:** Do NOT use `dotenv` or load `.env` files. Rely exclusively on system environment variables.
* **Response Format:** Extract `Vendor`, `Date`, `Amount`, and `TaxID`. Treat 'Tax ID', 'VAT ID', 'VAT No', 'VAT', and 'TIN' as the exact same thing for compliance validation. Note that these IDs are often alphanumeric. Scan the entire document carefully for any alphanumeric string associated with these labels. IMPORTANT: The extracted TaxID MUST be an actual reference number (e.g., 'GB123456789', '12-3456789'). If the text found is a placeholder like 'Insert your VAT ID', '[Your Tax ID]', or similar instructional text, treat it as missing. Always output the valid found value under the 'TaxID' key. Set `ComplianceRisk` to true if a valid `TaxID` is missing or empty. Flag `ManualReview` as true if `Amount > 5000` OR if `ComplianceRisk` is true. Return ONLY valid JSON.

## Task 3: Frontend Implementation
**File:** `public/index.html`
**Prompt:**
Act as a Lead Frontend Engineer. Generate `public/index.html` using React 18 and Tailwind CSS via CDN.
* **Components:** Create a `FileUpload` component with a preview and an `AuditDashboard` table to display the extracted Vendor, Date, Amount, and Tax ID.
* **State Management:** Display a 'Scanning...' state and map JSON audit flags to visual risk indicators. If TaxID is missing, render an amber badge "Missing (Compliance Risk)". Show a red badge if ManualReview is true, otherwise a green badge for Auto-Approved.
* **UX:** Professional corporate 'Slate' theme.

## Task 4: DevOps Configuration
**Files:** `package.json`, `Dockerfile`, `deploy.sh`
**Prompt:**
Generate the following supporting infrastructure:
1. **package.json:** Include `express`, `@google/genai`, and `multer`. Set `dev` script to `nodemon index.js`.
2. **Dockerfile:** Use `node:20-slim`. Ensure `/tmp/uploads` is created with write permissions (`777`).
3. **deploy.sh:** Write a gcloud command to deploy to Cloud Run using `--allow-unauthenticated` (environment variables will be injected by the platform).

## Task 5: Test Data Generation
Generate the following 6 images in the `samples/` directory (3 OK and 3 NOT OK):

**OK Invoices (Compliant & Auto-Approved):**
1.  **samples/invoice_ok_1.png**: "Generate a standard compliant invoice from Office Depot for $150.00, clearly showing a Tax ID number."
2.  **samples/invoice_ok_2.png**: "Generate a clean, professional consulting invoice from 'Acme Corp' for $3,200.00 with a visible Tax ID."
3.  **samples/invoice_ok_3.png**: "Generate a simple software subscription invoice from 'SaaS Inc' for $99.99 with a visible alphanumeric VAT ID."

**NOT OK Invoices (Manual Review / Compliance Risk):**
4.  **samples/invoice_not_ok_1.png**: "Generate a high-value invoice from Azure Cloud Services for $12,400.50, clearly showing a Tax ID number."
5.  **samples/invoice_not_ok_2.png**: "Generate a messy receipt with no Tax ID from a local vendor."
6.  **samples/invoice_not_ok_3.png**: "Generate a handwritten invoice from a contractor for $450.00 with no TIN or Tax ID visible anywhere."

## Task 6: Automated Testing Suite
**Files:** `tests/unit.test.js`, `tests/e2e.test.js`, `package.json`
**Prompt:**
Act as a QA Automation Engineer. Generate the following test suites:
1. **Unit Tests (`tests/unit.test.js`):** Use `jest` and `supertest`. Mock the `@google/genai` SDK to avoid real API calls. Test the `/audit` endpoint to verify the backend logic for Auto-Approval, High Amount (Manual Review), and Missing Tax ID (Compliance Risk).
2. **E2E Tests (`tests/e2e.test.js`):** Write a test script that uploads the actual images from the `samples/` directory to the server and verifies the final HTTP responses and logic flags match expectations.
3. **Package Update:** Update `package.json` to include `jest` and `supertest` as devDependencies, and configure the `scripts` section in `package.json` with `"test": "jest tests/unit.test.js"` for unit tests and `"test:e2e": "node tests/e2e.test.js"` for end-to-end tests.

## Task 7: Execution & Verification
After all code and test data generation is complete, execute the test suites to ensure the system is working correctly.

Run the following commands in the terminal:
```bash
# Run the unit tests (mocked AI responses)
npm run test

# Run the end-to-end tests (real AI inference against the generated samples)
npm run test:e2e
```
