# GlobalAudit AI Architecture

GlobalAudit AI is an intelligent document processing system designed to extract and validate compliance data from uploaded invoice and receipt images.

## System Overview

The application is built on a serverless, stateless architecture optimized for deployment on Google Cloud Run. It utilizes a React frontend to capture user input and an Express.js backend that interfaces directly with the Gemini 3.1 Pro Preview multimodal model via the `@google/genai` SDK.

## Data Flow

1.  **User Upload:** A user uploads an invoice image (PNG, JPG, etc.) through the React frontend UI (`public/index.html`).
2.  **API Request:** The frontend sends a `multipart/form-data` POST request to the backend `/audit` endpoint.
3.  **Temporary Storage:** The backend Express server uses `multer` to temporarily save the uploaded file to the local disk at `/tmp/uploads`.
4.  **AI Inference:** The backend reads the file from `/tmp/uploads`, converts it to a base64 string, and sends it to the Gemini API along with a highly specific system prompt.
5.  **Compliance Logic:** The Gemini model extracts the `Vendor`, `Date`, `Amount`, and `TaxID` (or VAT ID). Based on the prompt instructions, it evaluates the `Amount` and the presence of the `TaxID` to flag the document for `ManualReview` or `ComplianceRisk`.
6.  **Cleanup (Stateless Enforcement):** Immediately after the AI inference returns a result or fails, the backend `finally` block executes `fs.unlinkSync()` to permanently delete the uploaded file from `/tmp/uploads`. This ensures no memory leaks or storage bloat occurs on the ephemeral serverless instance.
7.  **Client Response:** The backend parses the AI's JSON response, enforces floating-point type safety on the `Amount`, and returns the structured JSON to the frontend.
8.  **UI Update:** The frontend receives the JSON and dynamically updates the `AuditDashboard` to display the extracted fields and corresponding visual risk badges (Auto-Approved, Manual Review Required, or Missing Compliance Risk).

## Core Principles

*   **Statelessness:** No database or persistent storage is used. Every request is isolated, making it infinitely scalable horizontally via Cloud Run.
*   **Zero-Build Frontend:** The frontend relies on CDN-delivered React, Babel, and Tailwind CSS, eliminating complex build pipelines (`Webpack`, `Vite`) and allowing the entire UI to be served statically from a single `index.html` file.
*   **Prompt-Driven Logic:** Instead of complex OCR parsing libraries (like Tesseract) and regex matching algorithms, the core business logic (identifying VAT numbers vs placeholders) is entirely delegated to the reasoning capabilities of the Gemini 3 Pro model.