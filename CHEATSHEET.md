## 📄 CHEATSHEET.md: Prompting for Success

When using **Gemini 3.1 Pro** via the `gemini-cli` or within your application logic, the quality of your output depends on the "Vibe" and "Context" you provide.

### 1. The "Think-First" Debugging Prompt

If your code is failing or throwing an error, don't just ask for a fix. Ask Gemini to "think" through the stack trace.

**Prompt Template:**

> "I am receiving the following error: `[PASTE_ERROR_HERE]`.
> Here is my current `index.js` logic: `[PASTE_CODE_HERE]`.
> 1. Analyze the root cause.
> 2. Explain the fix.
> 3. Provide the corrected code snippet using Gemini 3 best practices."
> 
> 

### 2. Adjusting "Thinking Levels"

Gemini 3 Pro supports different reasoning depths. Use these parameters in your backend code to balance speed vs. accuracy.

| Thinking Level | Use Case | Result |
| --- | --- | --- |
| **Low** | Simple data extraction (e.g., just the Date). | Fast, low latency, lower cost. |
| **Medium** | Standard auditing and math calculations. | Balanced performance. |
| **High** | Complex compliance, policy reasoning, or multi-page docs. | Deep reasoning, higher accuracy, slightly slower. |

### 3. Prompting for Better Multimodal Extraction

When auditing documents, the "System Instruction" is your policy manual.

**Pro-Tip Prompt:**

> "You are an Expert Procurement Auditor. When looking at this image:
> * If the Vendor Name is handwritten, use high-confidence OCR.
> * If the currency is not USD, convert it to USD using a placeholder rate of 1.1 and flag it.
> * Return the result in **Structured JSON** with keys: `vendor`, `total`, `is_compliant`, and `reasoning`."
> 
> 

# Check if billing is enabled, must return true

 gcloud billing projects describe $GOOGLE_CLOUD_PROJECT

 
---
