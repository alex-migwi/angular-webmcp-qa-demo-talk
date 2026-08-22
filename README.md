# NG Kenya 2026 - WebMCP QA Demo 🧪🤖

**Speaker:** Alex Muturi  
🌐 [Blog](https://alex-migwi.github.io) | 🐦 [Twitter/X](https://x.com/MuturiAlex) | 💼 [LinkedIn](https://linkedin.com/in/alex-muturi)

---

## 📌 Talk Overview

This repository contains presentation details, talk notes, and resources for **"Exposing Angular Services to AI Agents with WebMCP & Autonomous QA"** presented at **NG Kenya 2026**.

In this talk, we explore **WebMCP (Web Model Context Protocol)**—an emerging W3C browser standard proposal that allows client-side web applications to register native application services directly into `navigator.modelContext`. We demonstrate how Angular services can be declaratively mapped to AI tools via TOML configuration files and autonomously tested by AI agents (using both local LLMs via **LM Studio** and cloud LLMs via **Google Gemini API**).

> ⚠️ **Disclaimer & Experimental Status**  
> WebMCP is currently an experimental standard proposal under active development and is not yet natively supported across all mainstream browser releases without developer flags or polyfills. The demo uses [`ng-webmcp`](https://github.com/nicoavanzdev/ng-webmcp) and an in-memory testing polyfill (`installWebMcpPolyfill()`) to showcase client-side tool registration and autonomous testing workflows in Angular V22.

---

## 💡 What is WebMCP?

**WebMCP (Web Model Context Protocol)** adapts the Model Context Protocol (MCP) for browser runtime environments. It enables client-side web applications to:

1. **Expose Application Services as AI Tools**: Register Angular `@Injectable()` service methods into `navigator.modelContext` so AI assistants (side-panels, in-page chat agents, or browser extensions) can directly call web app functionality.
2. **Declarative Tool & Test Specs**: Map service methods and define end-to-end verification suites declaratively in TOML configuration schemas.
3. **Autonomous AI QA Evaluation**: Enable AI agents to execute registered WebMCP tools, evaluate assertion results, detect bugs, and suggest precise TypeScript code fixes autonomously.

---

## 🏗️ Architecture & Demo Highlights

The accompanying demo application showcases a complete end-to-end Angular WebMCP testing workflow:

### 1. Service-to-Tool Ingestion (`webmcp-tools.toml`)
Angular services are registered at application startup using an `APP_INITIALIZER` (`provideWebMcpTomlLoader`) with two exposure modes:
- **Auto Mode (`mode = "auto"`)**: Automatically exposes all public methods on a service class (e.g., `UserService` -> `userservice_login`, `userservice_getprofile`).
- **Explicit Mode (`mode = "explicit"`)**: Exposes only explicitly declared methods with custom tool descriptions (e.g., `PaymentService` -> `paymentservice_processpayment`).

### 2. Declarative Schema Structure
Tool declarations and test plans reside co-located in `webmcp-tools.toml`:

```toml
[[tools]]
class = "UserService"
mode = "auto"

[[tools]]
class = "PaymentService"
mode = "explicit"
methods = ["processPayment", "refund", "applyDiscount"]
[tools.description_overrides]
processPayment = "Process a credit card payment securely"

[[tests]]
name = "auth_flow_validation"
description = "Verify login success, profile retrieval, and logout cleanup."
steps = [
  { tool = "userservice_login", args = { user = "test@example.com", pass = "ValidPass123" }, assert = { success = true } },
  { tool = "userservice_getprofile", args = {}, assert = { name = "Test User" } }
]
```

### 3. Autonomous AI QA Test Runners
- **LM Studio Runner (`npm run test:lm-studio`)**: Executes test suites against open-weights models running locally (e.g., Qwen 2.5 Coder, Gemma, Phi-3).
- **Gemini API Runner (`npm run test:gemini`)**: Executes test suites against Google Gemini models (`gemini-3.5-flash`).

### 4. Bug Detection & Auto-Repair Scenarios
The live talk demo highlights the AI agent handling common real-world defect scenarios:
- **Math & Logic Errors**: Detecting direct subtraction bugs in discount calculations (`PaymentService.applyDiscount`).
- **Missing Implementations**: Detecting calls to unregistered methods (`deleteAccount`) and auto-generating missing TypeScript service methods.
- **Validation Deficiencies**: Proposing guard clauses for invalid or missing inputs.

---

## 🚀 Running the Demo Codebase

The runnable demo application is available in the companion project repository:

```bash
# Navigate to the demo repository
cd ../angular-webmcp-qa-demo/app

# Install dependencies
npm install

# Start the Angular development server
npm start
```

Open your browser at **`http://localhost:4200`** to view the application.

### Execute Autonomous AI Test Suites

```bash
# 1. Run local LLM evaluation (requires LM Studio running on port 1234)
npm run test:lm-studio

# 2. Run Google Gemini API evaluation
export GEMINI_API_KEY="your-gemini-api-key"
npm run test:gemini
```

---

## 📚 Resources & References

- 🔗 **Demo Repository**: [angular-webmcp-qa-demo](https://github.com/alex-migwi/angular-webmcp-qa-demo)
- 🌐 **Author Website**: [alex-migwi.github.io](https://alex-migwi.github.io)
- 🐦 **Twitter / X**: [@MuturiAlex](https://x.com/MuturiAlex)
- 💼 **LinkedIn**: [Alex Muturi](https://linkedin.com/in/alex-muturi)
- 📦 **ng-webmcp Library**: [nicoavanzdev/ng-webmcp](https://github.com/nicoavanzdev/ng-webmcp)

---

## 📄 License

This project is licensed under the [MIT License](LICENSE).