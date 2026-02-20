# Testing Strategy

GlobalAudit AI implements a two-tier testing strategy to ensure both the API contract and the AI prompt logic function as expected.

## 1. Unit Testing (`tests/unit.test.js`)
Unit tests are designed to run quickly without making actual network requests to the Google Gemini API, saving costs and execution time.

*   **Framework:** Jest and Supertest.
*   **Mocking:** The `@google/genai` SDK is mocked using `jest.mock()`. We intercept the `GoogleGenAI` constructor and force the `models.generateContent` method to return a predefined, static JSON response string representing different business scenarios.
*   **Test Cases:**
    1.  *Standard Invoice:* Mocks a return of `$150.00` and a valid `TaxID`. Asserts `ManualReview` and `ComplianceRisk` are false.
    2.  *High Value:* Mocks a return of `$12,400.50`. Asserts `ManualReview` is true.
    3.  *Missing Tax ID:* Mocks an empty `TaxID`. Asserts `ComplianceRisk` and `ManualReview` are true.

Run Unit Tests:
```bash
npm run test
```

## 2. End-to-End (E2E) Testing (`tests/e2e.test.js`)
E2E tests validate the entire flow, including the actual AI model's ability to interpret real images and the backend's parsing and cleanup logic.

*   **Execution:** A custom Node.js script that spawns a child process to start the Express server on an alternate port (`8081`).
*   **Data Source:** Iterates over the 6 sample images generated in the `samples/` directory (3 OK invoices, 3 NOT OK invoices).
*   **Behavior:** Uses the native `fetch` and `FormData` (with `Blob` casting) to upload each image to the local running server.
*   **Assertions:** Validates the returned JSON payload against a specific `check` function defined for each image (e.g., ensuring `invoice_not_ok_1.png` correctly trips the `Amount > 5000` flag).
*   **Cleanup:** Kills the spawned server process after the tests complete.

Run E2E Tests:
```bash
npm run test:e2e
```