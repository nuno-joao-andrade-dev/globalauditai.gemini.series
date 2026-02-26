# Backend Implementation (`index.js`)

The backend is a lightweight Node.js Express server acting as a secure proxy between the client and the Google Gemini API.

## Core Dependencies

*   `express`: The web server framework.
*   `multer`: Middleware for handling `multipart/form-data` (file uploads).
*   `@google/genai`: The official the latest version of Google AI SDK for interacting with Gemini models.
*   `fs` & `path`: Native Node modules for file system operations.

## Endpoint: `POST /audit`

This is the primary and only functional endpoint in the application.

### File Handling
The endpoint uses `multer` configured with a `diskStorage` engine. The destination is strictly hardcoded to `/tmp/uploads`. If the directory does not exist, it is created recursively.
*   *Security Note:* Using `/tmp` ensures compatibility with serverless environments like Google Cloud Run, where the filesystem is memory-backed and ephemeral.

### Integration with Gemini (`@google/genai`)
The `@google/genai` latest version SDK is initialized using the `process.env.GOOGLE_API_KEY` and vertexai set to true . It calls the `gemini-3.1-pro-preview` model.

**The System Prompt:**
The prompt is carefully engineered to handle multimodal extraction and compliance validation in a single pass:
1.  **Extraction:** Extracts Vendor, Date, Amount, and TaxID.
2.  **Equivalence:** Instructs the model to treat 'Tax ID', 'VAT ID', 'VAT No', 'VAT', and 'TIN' as the exact same concept.
3.  **Validation:** Explicitly requires the TaxID to be an *actual reference number* (e.g., alphanumeric strings like GB123456789) and ignores placeholders like "[Insert your VAT ID]".
4.  **Formatting:** Enforces that the `Amount` key is a floating-point number, omitting currency symbols.
5.  **Business Logic:**
    *   Sets `ComplianceRisk: true` if a valid TaxID is missing.
    *   Sets `ManualReview: true` if `Amount > 5000` OR if `ComplianceRisk` is true.
6.  **Output:** Forces the model to return ONLY valid JSON using `responseMimeType: 'application/json'`.

### Post-Processing & Safeguards
After receiving the response from Gemini (`response.text`), the backend performs a safeguard check:
*   It strips any markdown block formatting (e.g., ```json ... ```) that the model might erroneously inject despite the `responseMimeType` config.
*   It parses the `Amount` property ensuring it is a float. If the model returned a string (e.g., "$150.00"), it strips non-numeric characters and converts it.

### The Cleanup Policy
A critical component of this backend is the `finally` block attached to the `try/catch` execution.
Regardless of whether the Gemini API call succeeds or fails, `fs.unlinkSync(filePath)` is executed to delete the uploaded image. This guarantees the server remains stateless and the memory-backed `/tmp` directory does not fill up and crash the instance.