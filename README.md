# CodeLens AI — Multi-Language Code Review Assistant

**Academic Capstone & Demonstration Web Application**  
*Deterministic, Client-Side Static Analysis & AI-Style Code Review Assistant*

---

## 1. Problem Statement
Modern software engineering education and collaborative code reviews require rapid identification of security anti-patterns, style violations, and potential bugs before code is committed or compiled. Traditional enterprise SAST (Static Application Security Testing) solutions require complex cloud backend architectures, remote code transmission, proprietary license keys, or containerized execution sandboxes.

Students and privacy-conscious organizations require a **lightweight, immediate, and zero-trust** tool capable of inspecting source files locally without executing untrusted code or exposing proprietary logic to external APIs.

---

## 2. Objective & Project Goal
**CodeLens AI** provides an offline, client-side static analysis platform running directly in any modern web browser. It evaluates source files written in **9 programming languages**, generates immediate diagnostics, computes an overall **Code Health Score (0–100)**, and provides an **offline, rule-based AI remediation assistant** with contextual code snippets and confidence ratings.

---

## 3. Key Features
- **Zero-Dependency Architecture**: Runs completely in the browser via native HTML, CSS, and vanilla JavaScript without Node.js, npm, or paid APIs.
- **Language-Agnostic Static Analyzers**:
  - **Full Basic Static Analyzers**: Python (`.py`), JavaScript (`.js`), SQL (`.sql`), HTML (`.html`), and CSS (`.css`).
  - **Basic Pattern Analyzers**: Java (`.java`), C# (`.cs`), C (`.c`), and C++ (`.cpp`, `.cc`, `.hpp`).
- **Safety First & Safe Execution**: Read-only static analysis. Source code is never compiled, interpreted, or executed with `eval()`.
- **Pure Client-Side ZIP Unpacker**: Extract and analyze `.zip` archive trees without server uploads.
- **AI-Style Remediation Engine**: Deterministic, rule-based AI guidance providing explanation, recommendation, and a "Copy Fix" button.
- **Interactive Multi-Filter Table**: Filter by Language, Severity (High, Medium, Low), Category, File, or full-text query.
- **Auditing & Reporting**: Export full findings to structured **JSON** or printable **HTML Reports**.
- **Pre-Packaged Demo Project**: Immediate single-click "Load Sample Project" for rapid live demonstration.

---

## 4. Supported Languages Matrix

| Language | Extensions | Analysis Level | Detected Patterns |
| :--- | :--- | :--- | :--- |
| **Python** | `.py` | Full basic static | `eval()`, `exec()`, hardcoded secrets/API keys, bare `except:`, `print()` debugging, `TODO/FIXME`, long lines (>100 chars) |
| **JavaScript** | `.js` | Full basic static | `eval()`, `innerHTML` DOM XSS, `document.write()`, `console.log()` leftovers, `var` vs `let/const`, secrets, `TODO` |
| **SQL** | `.sql` | Full basic static | String concatenation SQLi, `SELECT *`, `UPDATE`/`DELETE` missing `WHERE`, credentials, `TODO` |
| **HTML** | `.html`, `.htm` | Full basic static | Missing `<img>` `alt` accessibility, inline `onclick` handlers, missing `<html lang>`, missing viewport meta tag, `TODO` |
| **CSS** | `.css` | Full basic static | Duplicate selectors, `!important` abuse, empty declaration blocks `{ }`, long lines, `TODO` |
| **Java** | `.java` | Basic pattern analysis | Hardcoded secrets/tokens, silently swallowed exceptions in `catch`, `Runtime.getRuntime().exec()`, `TODO` |
| **C#** | `.cs` | Basic pattern analysis | Hardcoded credentials, empty `catch` blocks, `Process.Start` shell execution risks, `TODO` |
| **C** | `.c` | Basic pattern analysis | Dangerous `gets()`, unbounded `strcpy()`, `strcat()`, `sprintf()`, `scanf()` missing width, `system()`, secrets, `TODO` |
| **C++** | `.cpp`, `.cc`, `.hpp` | Basic pattern analysis | Unsafe legacy buffer calls, `system("pause")`, command injection risks, credentials, `TODO` |

*Note: In accordance with academic project integrity guidelines, Java, C#, C, and C++ are explicitly presented as "Basic pattern analysis" heuristics, with deeper AST adapter extensions identified on the roadmap.*

---

## 5. Technology Stack
- **Structure**: Semantic HTML5 (Single Page Architecture).
- **Styling**: Tailwind CSS (loaded via CDN) with custom dark developer-tool design tokens.
- **Scripting**: Pure Vanilla JavaScript (ES6+), organized into modular namespaces.
- **Persistence**: Browser `localStorage` (optional recent-run history and user preference toggles).
- **Archive Ingestion**: Native `DataView` PKZIP walker combined with browser `DecompressionStream`.

---

## 6. Architecture Diagram

```
+-------------------------------------------------------------------------+
|                  CodeLens AI — Browser Application                      |
+-------------------------------------------------------------------------+
                                     |
               +---------------------+---------------------+
               |                                           |
    [ FileHandler / ZIP Decoder ]                 [ Built-in Sample Project ]
               |                                           |
               +---------------------+---------------------+
                                     | (File objects: name, content)
                         [ Language Detector ]
                                     |
          +--------------------------+--------------------------+
          |                                                     |
  [ Static Analyzers (Full) ]                       [ Pattern Analyzers (Basic) ]
  - Python Analyzer                                 - C / C++ Pattern Engine
  - JavaScript Analyzer                             - Java Pattern Engine
  - SQL Analyzer                                    - C# Pattern Engine
  - HTML Analyzer
  - CSS Analyzer
          |                                                     |
          +--------------------------+--------------------------+
                                     | (Standardized issue entities)
                         [ AI Remediation Engine ]
                                     |
              +----------------------+----------------------+
              |                                             |
   [ Dashboard & Score Engine ]                   [ Filterable Issues UI ]
              |                                             |
   [ JSON & Print Export ]                        [ Fix Inspector Modal ]
```

---

## 7. How to Run Locally

### Option 1: Direct File Open (Zero Server Required)
1. Save `index.html` to any folder on your computer.
2. Double-click `index.html` or open it with Google Chrome, Mozilla Firefox, Microsoft Edge, or Apple Safari.
3. The application is completely functional right away.

### Option 2: Local HTTP Server (Optional)
If running inside VS Code or terminal:
```bash
# Python 3
python -m http.server 8000

# Node (npx)
npx serve .
```
Navigate to `http://localhost:8000`.

---

## 8. User Flow Guide
1. **Explore the Dashboard**: View code-health metrics, severity breakdowns, and previous evaluation runs.
2. **Ingest Code**:
   - Click **"Load Sample Project"** on the top bar to instantly populate 9 multi-language test files.
   - Alternatively, navigate to **Upload & Analyze** and drag in your own source files or a `.zip` archive.
3. **Execute Analysis**: Click **"Analyze Code Now"**.
4. **Inspect Findings**:
   - Filter by severity, language, or search query.
   - Click **"Inspect & Fix"** on any finding to open the AI Explanation Card.
   - Click **"Copy Fix"** to grab the remediation code snippet.
5. **Export Results**:
   - Navigate to **Reports** and click **"Download JSON"** for automated pipelines or **"Print / Save HTML"** for a printable executive summary.

---

## 9. Data Model Specification

Each detected finding conforms to the following internal schema:
```json
{
  "id": "py_eval_10",
  "file": "sample.py",
  "language": "Python",
  "line": 10,
  "severity": "High",
  "type": "Security",
  "title": "Use of Dangerous Dynamic Execution (eval / exec)",
  "description": "Executing dynamic code strings with eval() or exec() can enable Arbitrary Code Execution (ACE) if inputs are unverified.",
  "code": "user_obj = eval(payload_string)",
  "recommendation": "Use ast.literal_eval() for safe data deserialization or refactor to avoid dynamic evaluation.",
  "suggestedFix": "import ast\n# Safe alternative for parsing literal structures:\ndata = ast.literal_eval(untrusted_str)",
  "confidence": "High"
}
```

---

## 10. Security & Privacy Model
- **No Remote Telemetry**: No network requests are made with user code. Code never leaves your local machine.
- **Immutable Parsing**: Source files are ingested as read-only text tokens and discarded from memory upon clearing or page refresh.
- **Zero Secrets Storage**: Stored history contains only aggregate metrics (file count, scores, issue counts). Source text and discovered tokens are never written to `localStorage`.

---

## 11. Limitations & Future Roadmap
- **Limitations**:
  - Analyzers rely on static regex and token heuristics rather than full abstract syntax trees (AST) or compiler type resolution.
  - Inter-procedural taint analysis across multiple files is not performed.
- **Future Roadmap**:
  - Compile Tree-sitter parsers to WebAssembly (Wasm) for full AST queries directly in the client.
  - Support custom rule creation via user-defined regex and JSON rulepacks.
  - Add git diff patch export to apply suggested fixes directly to repositories.

---

## 12. College Submission Disclaimer
*This project is developed as an academic capstone demonstration. It is designed to illustrate language-agnostic static analysis concepts, client-side architectural separation, and deterministic automated remediation without reliance on external commercial cloud APIs.*