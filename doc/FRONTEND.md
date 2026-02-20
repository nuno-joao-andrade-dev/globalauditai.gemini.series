# Frontend Implementation (`public/index.html`)

The frontend is a single-page application built using React 18 and styled with Tailwind CSS.

## Architecture

To simplify deployment and remove build steps, the frontend is entirely contained within `public/index.html`. It uses Content Delivery Networks (CDNs) to load its dependencies:
*   **React & ReactDOM (UMD build):** For component-based UI.
*   **Babel (Standalone):** Compiles JSX syntax directly in the browser at runtime.
*   **Tailwind CSS (CDN script):** In-browser utility-first CSS styling, configured with a custom 'Slate' theme.

## Components

### 1. `App` (Root Component)
Manages the global state of the application:
*   `auditResult` (Object | null): Stores the JSON response from the backend.
*   `isScanning` (Boolean): Tracks the loading state during the API request.

It renders the header, the `FileUpload` component, and the `AuditDashboard` component.

### 2. `FileUpload`
Provides the user interface for selecting and uploading an invoice image.
*   **State:** Manages a `preview` state (String | null) to store a local `URL.createObjectURL()` representation of the uploaded file for immediate visual feedback.
*   **Behavior:** When a file is selected, it triggers the `onScanning(true)` callback, constructs a `FormData` object with the file appended as `invoice`, and uses the Fetch API to send a `POST` request to `/audit`.
*   **Error Handling:** Catches fetch errors and alerts the user, resetting the scanning state.

### 3. `AuditDashboard`
Responsible for displaying the results of the AI extraction and evaluating the risk flags to provide visual feedback.
*   **Loading State:** If `scanning` is true, it displays a spinning CSS loader and a pulsing "Scanning and Analyzing Document..." message.
*   **Badge Rendering Logic (`renderBadge`):**
    *   If `result.ManualReview` is true: Renders a red "Manual Review Required" badge.
    *   If `result.ComplianceRisk` is true (or `TaxID` is missing): Renders an amber "Missing (Compliance Risk)" badge.
    *   Otherwise: Renders a green "Auto-Approved" badge.
*   **Data Table:** Displays the `Vendor`, `Date`, `Amount` (formatted with a `$` symbol), and `Tax ID`. If the `Tax ID` is missing, it explicitly renders the word "Missing" in an amber color for emphasis.