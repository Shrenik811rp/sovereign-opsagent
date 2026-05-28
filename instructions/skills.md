# Sovereign OpsAgent: Production-Grade Engineering Spec & Build Prompts
**EXPANDED EDITION – Additional anti‑hallucination gates, strict verification steps, and explicit clarification triggers have been inserted throughout. Every original word is retained; new conditions are marked with [VERIFY] or [STOP] for your attention.**

This document contains the expanded, explicit prompt sequence designed to prevent agent hallucinations, eliminate assumptions, and guarantee clean execution in an advanced AI-assisted development environment (e.g., Project IDX, Cursor, GitHub Copilot Workspaces).

🛑 Global Agent Directives (Inject into your AI chat first)
CRITICAL ARCHITECTURAL DIRECTIVES:

No Guessing/Assumptions: If any parameter, API contract, schema, or configuration value is ambiguous or missing, stop immediately and ask for user clarification.

Interactive Gates: You must print a plan of execution and wait for user confirmation ([Y/N]) before generating files larger than 100 lines or running destructive terminal commands.

Defensive Coding: Every Python function must include comprehensive try/except blocks, log descriptive errors to stderr, and use strong type hinting.

Strict Library Versioning: Use Python 3.10 standard libraries wherever possible. For LangGraph, follow the updated state graph API conventions strictly.

**[ADDED GLOBAL RULE – ALWAYS ACTIVE]**
Before executing any terminal command that modifies the file system (e.g., `pip install`, `npm install`, `terraform init`), you must print the exact command, explain what it will change, and wait for my explicit “YES” reply. Do not proceed on silence.  
**[ADDED GLOBAL RULE]**
If a tool or binary is missing, you must NOT attempt to guess installation commands for my OS. Instead, print the command template and ask me to confirm my OS (Linux/macOS/Windows) before offering the exact installation line.  
**[ADDED GLOBAL RULE]**
After any file is created or modified, print its full absolute path and the first 3 lines of its content so I can visually verify it. Then stop and wait for my “OK” before continuing.  

📦 Phase 0: Environment Validation & Setup
Prompt 1: Prerequisites Check & Strict Global Installation
Markdown
Context: We are building the 'Sovereign OpsAgent', an enterprise autonomous FinOps/SecOps agentic orchestration loop.
Action: 
1. Open a terminal and verify the environment versions by running commands to check if Python (>=3.10), Node.js (>=18), and Terraform are installed. Print the exact versions detected to the chat console.
2. If any component is missing, halt execution and output the exact commands required for my local OS package manager to install them. Do not attempt to guess or force install them without my approval.
3. Install the following Python libraries within a locally active environment: `langgraph`, `langchain-core`, `google-generativeai`, `flask`, `flask-cors`, `pyyaml`, `checkov`.
4. Global CLI Tooling: Explicitly install the Infracost CLI and Checkov globally via the terminal. Confirm their binaries are correctly exposed in the system PATH.
5. Stop and present a summary table of the validated ecosystem before continuing.

**[ADDED VERIFICATION GATE after step 2]**  
If any component is missing, print “MISSING COMPONENTS DETECTED” and list them. Then ask me: “Do you want me to provide the installation commands for your OS? Please confirm your OS (Ubuntu/macOS/Windows).” Do nothing else until I respond.  
**[ADDED VERIFICATION GATE after step 3]**  
Before installing any Python package, you must first create a virtual environment (e.g., `python3 -m venv .venv`). Print the creation command and ask “May I create the venv here?” Wait for my YES. Then print the pip install command and ask “Proceed with pip install?” Wait for YES. After installation, run `pip freeze` and print the output so I can confirm the versions.  
**[ADDED VERIFICATION GATE after step 4]**  
When installing Infracost CLI, check if `infracost` is already installed. If not, show the official install command (e.g., `brew install infracost` for macOS) but ask for my OS confirmation before running. For Checkov, do the same: if missing, output `pip install checkov` but wait for my approval.  
**[ADDED STOP]**  
At the very end, before moving to Phase 1, print a table with columns: Tool, Version, Path, Status. Then ask: “Environment validated. Proceed to Phase 1? (YES/NO)”.  

📂 Phase 1: Project Blueprinting
Prompt 2: Enterprise File Directory Tree & Repository Setup
Markdown
Context: Initializing clean physical directory structure to support Python orchestration, mock architectures, and an enterprise web layout.
Action:
1. Run terminal commands to create a parent folder named `sovereign-opsagent`.
2. Inside it, initialize a clean local Git repository (`git init`).
3. Generate the exact following subfolder structure explicitly:
   ├── /mock_infra
   ├── /agent_core
   ├── /frontend-portal
   └── /k8s
4. In `/agent_core`, write a `requirements.txt` listing all Python dependencies with explicit open-ended constraint formatting (e.g., `langgraph>=0.1.0`).
5. Wait for me to review the directory output on disk before moving to rule definition.

**[ADDED SAFETY]**  
Before creating the parent folder, ask me for the absolute path where I want it (e.g., `/home/user/projects`). Do not assume my current working directory. Print the full path you will use and ask “Create here? (YES/NO)”.  
**[ADDED CHECK]**  
After creating all folders, run `ls -R` or `tree` and print the output. Stop and say: “Directory structure created. Please review the tree above and type YES to continue.”  
**[ADDED CHECK for requirements.txt]**  
Before writing the file, print the exact content you intend to write and ask: “Does this match the required dependencies? (YES/NO)” Only write after I confirm. After writing, print the file’s full path and first line.  

Prompt 3: Governance Policy Declaration (The Source of Truth)
Markdown
Context: The agents require an immutable declarative policy markdown file to cross-reference infrastructure configurations against corporate mandates.
Action:
1. In the root directory, create a file explicitly named `governance_policy.md`.
2. Populate it verbatim with the following markdown contents:
# Corporate Infrastructure Governance Policy
## Section 1: Compute Sizing Constraints
- RULE_COMPUTE_MAX_SIZE: The maximum permissible node size for any Kubernetes engine pool node is `n2-standard-4`. Any instance configuration exceeding 4 vCPUs or 16GB RAM is an active violation.
- RULE_TARGET_UTILIZATION: Target SAP system CPU utilization threshold must remain greater than 60%.

## Section 2: Security & Cryptography Compliance
- RULE_DB_ENCRYPTION: All stateful database services, cloud storage instances, and persistent volumes must have explicit encryption-at-rest configurations enabled (`encryption_at_rest = true`).

## Section 3: Financial Overhead Budgeting
- RULE_BUDGET_CEILING: The projected global monthly cloud running cost threshold limit is capped tightly at $5,000 USD.

**[ADDED STOP BEFORE WRITING]**  
Print the exact Markdown content you are about to write and ask: “Is this the exact governance content you want? (YES/NO)” Only after receiving YES, create the file. Then print the file path and the first rule line for confirmation.  

Prompt 4: Deterministic Git Ignore Configurations
Markdown
Action: Create a `.gitignore` file in the project root directory. To eliminate local clutter and credential leaks, explicitly write entries for:
- `node_modules/` and `dist/`
- `.terraform/`, `*.tfstate`, `*.tfstate.backup`, `.terraform.lock.hcl`
- `.env`, `*.env.*`, secrets files
- `__pycache__/`, `*.pyc`, `*.pyo`, `*.pyd`
- `.DS_Store` (for macOS environments)
Verify line formatting contains no trailing whitespaces.

**[ADDED CHECK]**  
Before writing, show the exact list of entries you will include (one per line) and ask “Add these to .gitignore? (YES/NO)” After creating, print the file content and confirm: “.gitignore created. Ready for Phase 2?”  

🛠️ Phase 2: Mock Infrastructure Generation (Target Vulnerabilities)
Prompt 5: Intentionally Defective GKE Terraform Provisioning
Markdown
Context: Creating an infrastructure file designed to fail our FinOps budget rules.
Action:
1. Create a file at `mock_infra/gcp_gke_cluster.tf`.
2. Write a structurally valid HashiCorp Terraform configuration block targeting the `google` provider.
3. Define a `google_container_cluster` resource named `primary_cluster`.
4. Inside the cluster or node pool configuration, explicitly set `machine_type = "n2-standard-32"`.
5. Add an output block exporting `cluster_name`. Do not use variable wrappers; hardcode structural components to give the agents clear optimization targets.

**[ADDED PRECAUTION]**  
Show me the complete Terraform code you plan to write. I must approve the `machine_type` value and the structure before you save the file. Ask: “Is this Terraform config correct? (YES/NO)”  
**[ADDED CHECK]**  
After saving the file, run `terraform fmt` on it and print the formatted output. Then stop.  

Prompt 6: SAP BTP High-Overhead Telemetry Simulation
Markdown
Context: Simulating low-utilization enterprise app data.
Action: Create a JSON file at `mock_infra/sap_telemetry.json`. Write a flat, standardized dictionary schema containing:
{
  "sap_instance_id": "btp-hana-prod-01",
  "sap_hana_cpu_utilization": 0.12,
  "monthly_cost_usd": 8500.00,
  "environment": "production"
}

**[ADDED CHECK]**  
Print the JSON you intend to write and ask “Confirm JSON content? (YES/NO)” After writing, read the file back and print it to ensure it’s valid JSON.  

Prompt 7: Non-Compliant Epic IRIS Database YAML Schema
Markdown
Context: Simulating an unencrypted health IT database configuration.
Action: Create a YAML configuration layout file at `mock_infra/epic_db_config.yaml`. Write structural keys for: `database_engine: "postgres"`, `allocated_storage_gb: 500`, `automated_backups: true`. Do NOT include any key matching encryption parameters. Append a clear text comment header: `# Warning: Production configuration layout for Epic system cluster`.

**[ADDED CHECK]**  
Print the YAML before writing, verify there is no `encryption` key, and ask “Is this YAML correct? (YES/NO)” After writing, parse the file with Python’s `yaml.safe_load` and print the resulting dict to confirm it lacks encryption fields.  

Prompt 8: Local Terraform Initialization Validation Check
Markdown
Action: In the terminal, change directories into `/mock_infra` and execute exactly `terraform init`. Confirm that the local plugins and lock files are generated successfully. Do not execute any planning or application stages. Ensure no external remote backend errors occur. If an error surfaces, output the log and ask me for input.

**[ADDED SAFETY]**  
Before running `terraform init`, print the command and ask “Execute `terraform init` in mock_infra? (YES/NO)”. After execution, show the full output, highlight any warnings, and stop.  

🧠 Phase 3: Multi-Agent Orchestration Architecture (LangGraph Engine)
Prompt 9: Encapsulated Python Execution Skills (Tools Pipeline)
Markdown
Context: Building the custom interfaces that wrap around shell environments to parse infrastructure footprints.
Action: 
1. Create `agent_core/tools.py`. 
2. Import `subprocess`, `json`, `yaml`, and `os`.
3. Implement 4 core functions with full type hinting and try/except handling:
   - `run_infracost(path: str) -> dict`: Executes `infracost breakdown --path <path> --format json`, captures stdout, parses it via `json.loads`, and catches execution errors. If Infracost is unauthenticated, gracefully catch the error and fallback to a hardcoded mock JSON delta calculation.
   - `run_checkov(path: str) -> dict`: Executes `checkov -d <path> --framework terraform --output json`, catches output streams, and loads them into a standard dictionary.
   - `parse_sap_json(filepath: str) -> dict`: Safely parses `sap_telemetry.json` and cleanly casts numerical fields to floats.
   - `patch_infrastructure_file(filepath: str, string_to_replace: str, replacement_string: str) -> bool`: Opens the file in read-write mode, runs a safe string replacement, and saves the asset back to disk. Return True on success.
4. Stop execution, print out the file contents to the chat, and explicitly ask me to confirm the schema signatures before proceeding.

**[ADDED INCREMENTAL BUILD]**  
Do not write all four functions at once. Implement one function, show me the code, ask for confirmation, then proceed to the next. This way I can validate each tool’s signature.  
**[ADDED FALLBACK CHECK]**  
For the Infracost fallback, explicitly show me the hardcoded mock data you will use. I must approve it.  

Prompt 10: Multi-Agent StateGraph Structure Definition
Markdown
Context: Constructing the autonomous graph state management using LangGraph.
Action:
1. Create `agent_core/agent_definitions.py`.
2. Define a clear `AgentState` type using a standard Python `TypedDict` containing keys for:
   - `messages`: List of conversational logs/prompts
   - `current_patch_target`: Path of the file currently under correction
   - `loop_counter`: Integer to keep track of system loops
   - `status`: String flag tracking compilation success
3. Build four functional node methods mapping directly to the specialized agents:
   - **supervisor**: Directs routing by looking for terms inside messages like "FINOPS_NEEDED", "DEVOPS_PATCH", "SECOPS_SCAN".
   - **finops_agent**: Processes cost metrics and issues an explicit instruction specifying the old string and new compliant string.
   - **devops_agent**: Consumes recommendations and actively fires the file patching tools.
   - **secops_agent**: Executes the compliance validation wrapper.
4. Using LangGraph's `StateGraph`, add all four nodes, map standard edges sequentially (`supervisor -> finops -> supervisor -> devops -> secops -> supervisor`), and add conditional routing logic. Prevent infinite cycles by implementing a strict loop constraint check: if `loop_counter` exceeds 3, route immediately to an error node and break execution safely.

**[ADDED INTERACTIVE DESIGN]**  
Before writing the whole file, outline the node logic in a simple numbered list and ask: “Does this node behavior match your intention? (YES/NO)”  
**[ADDED SAFETY]**  
When implementing the loop constraint, explicitly show how you will track `loop_counter` and ensure the error node stops the graph gracefully. I must approve the error-handling strategy before you write the code.  

Prompt 11: Main Execution Controller Frontend API Entry Point
Markdown
Context: Providing an programmatic interface to trigger our LangGraph pipeline.
Action:
1. Create `agent_core/supervisor.py`.
2. Build a lightweight Python Flask or FastAPI backend containing a single POST route mapping to `/api/remediate`.
3. When the endpoint is triggered, invoke the compiled LangGraph orchestration pipeline with an initial user payload.
4. Configure standard Cross-Origin Resource Sharing (CORS) rules to accept incoming web connections from any local development network origin.
5. Save a clean summary log containing execution metrics to a file named `agent_core/results.json` upon completion of the agent loop.

**[ADDED CLARIFICATION]**  
If you choose Flask, print the code for the Flask app. If FastAPI, show the equivalent. Ask me which framework I prefer before writing.  
**[ADDED RESULT SCHEMA]**  
Before saving `results.json`, print a sample of what the summary JSON will look like. I must confirm the fields.  

🔄 Phase 4: Self-Healing Testing Loops
Prompt 12: Adding Local Simulation Compilation Check Skills
Markdown
Action: Open `agent_core/tools.py`. Append a function called `validate_terraform_plan(path: str) -> tuple[int, str]`. This function must invoke `terraform plan` via a shell execution wrapper and capture the system exit status code alongside standard output logs. Return these elements inside a clean data tuple.

**[ADDED VERIFICATION]**  
Show me the new function code alone, and explain how you will handle situations where Terraform is not installed. I will confirm before you append.  

Prompt 13: Integrating Real-Time Plan Validation Loops
Markdown
Action: Modify the `devops_agent` logic inside `agent_core/agent_definitions.py`. After invoking a file replacement patch, immediately execute the new `validate_terraform_plan` tool. If the resulting exit code is non-zero, capture the plan's exact error string, append it to the conversational history context, and increment the loop state counter to trigger an immediate corrective iteration.

**[ADDED CHECK]**  
Show the modified `devops_agent` function side-by-side with the previous version (highlight the changes) and ask “Apply this change? (YES/NO)”  

Prompt 14: Automated Security Rectification Routing
Markdown
Action: Update the `secops_agent` validation loop logic. If Checkov returns failed assertions, parse out the target file path and the exact rule violation metadata. Route this descriptive feedback back to the supervisor node with an explicit instruction to automatically inject missing security features (such as encryption attributes) into the source files.

**[ADDED CLARIFICATION]**  
Specify exactly how you will parse Checkov’s JSON output to extract file path and rule ID. Show me the parsing code before applying the change.  

🎨 Phase 5: Enterprise Portal Infrastructure (React & Fiori UX)
Prompt 15: Developer Portal Scaffold & Design Library Ingestion
Markdown
Action:
1. Open a terminal, navigate to `/frontend-portal`, and execute a standard terminal setup string to initialize a clean React SPA or Node application environment. 
2. Install the following official enterprise interface rendering packages explicitly: `@ui5/webcomponents`, `@ui5/webcomponents-react`, and `axios`.
3. Verify that the dependency matrix updates clean without security flags or runtime warnings.

**[ADDED STOP]**  
Before running `npx create-react-app` or similar, print the exact command and the directory where you will run it. Ask: “Initialize React app here? (YES/NO)”  
**[ADDED CHECK]**  
After installing the UI packages, run `npm ls @ui5/webcomponents` and print the tree to confirm they are present.  

Prompt 16: SAP Fiori Responsive Dashboard UI Component Engineering
Markdown
Action:
1. In `/frontend-portal/src/App.js` (or your main component file), design a responsive control panel layout using modern SAP Fiori elements.
2. Embed a prominent visual command button labeled "Execute Sovereign Optimization Sweep".
3. Configure the button to fire an HTTP POST request to the local API endpoint (`/api/remediate`) using Axios.
4. Build a tracking layout utilizing `AnalyticalTable` and `DonutChart` widgets from `@ui5/webcomponents-react` to compare original resource scales vs optimized parameters. Read real-time telemetry from `agent_core/results.json`.

**[ADDED INTERACTION]**  
Before writing any UI code, provide a textual description (or wireframe-like list) of the component hierarchy and data flow. Ask “Does this UI plan meet your requirement? (YES/NO)”  
**[ADDED VERIFICATION]**  
After implementing, show me the App.js code. I will confirm that the button endpoint URL is correct.  

🐳 Phase 6: Production Containerization & Portability Specs
Prompt 17: Multi-Stage Production Docker Build Configuration
Markdown
Action: In the root directory, create a `Dockerfile`. Build a robust multi-stage compilation spec:
- **Stage 1 (Build):** Spin up a clean Node environment, ingest package lists, compile the frontend portal web app assets, and optimize production static builds.
- **Stage 2 (Runtime):** Spin up a secure, minimal Python 3.10 alpine or slim container base image. Mirror the compiled frontend build, configure the local Python orchestration runtimes, install the system CLI tooling binaries (Terraform and Checkov), and expose web service port 8080.

**[ADDED PRECISION]**  
Print the full Dockerfile content and explain each stage before writing. I must explicitly approve the base images (e.g., `node:18-alpine`, `python:3.10-slim`). Ask: “Use these base images? (YES/NO)”  

Prompt 18: Kubernetes GitOps Resource Declarations
Markdown
Action:
1. Create `k8s/deployment.yaml`. Write a valid Kubernetes deployment spec with proper replica matching fields and explicit resource limits (CPU and Memory limits).
2. Create `k8s/service.yaml`. Define a standard `LoadBalancer` networking manifest exposing target container ports to web networks.

**[ADDED CHECK]**  
Show the YAML for both files. I will verify the container port matches what the Dockerfile exposes (8080). Ask “Do these manifests look correct? (YES/NO)”  

Prompt 19: Presentation Playbook Documentation Generation
Markdown
Action: Generate a highly polished `README.md` file in the root project directory explaining the exact architectural components, the orchestration workflow mechanics, and a clear guide on executing local validation checks. This documentation will serve as the presentation blueprint for review teams.

**[ADDED DRAFT REVIEW]**  
First, output a structured outline of the README (sections: Overview, Architecture Diagram, Setup, Usage, Deployment). After I approve, write the full README.  

🚀 Phase 7: Live System Execution & Debug Verification
Prompt 20: System Orchestration Warm-Up Integration Check
Markdown
Action: Initiate a local runtime execution check. Launch the Python API microservice via the terminal by running `python agent_core/supervisor.py`. Verify that the web server binds correctly without port collision errors. If any startup errors are encountered, stop and report them explicitly before continuing.

**[ADDED SAFETY]**  
Before launching, ask: “I will start the Flask/FastAPI server on port 5000 (or 8080). Is that port free on your system?” If I say no, ask me for an alternative port and adjust the code.  

Prompt 21: Live End-To-End Execution & Self-Healing Audit
Markdown
Action: Trigger the complete agent lifecycle loop. Execute an explicit curl statement or web request targeting the live optimization API route. Carefully monitor the terminal execution logs to trace how the agents handle structural validation checks, modify target configurations, and verify infrastructure changes against our corporate security policies. Print out the full execution logs to the chat screen upon completion.

**[ADDED STEP]**  
Before firing the request, print the exact curl command you will use. Ask: “Run this command now? (YES/NO)” After the loop, print the final `results.json` content and ask: “Does the output match your success criteria?”  

🌐 100% Free Production Deployment Protocol
To make your project reviewable by peers or interviewers without using Streamlit, deploy the decoupled production application using the following free, industry-grade hosting strategy.

1. The Backend API Pipeline (Render Hosting)
Hosting Platform: Render.com (Free Web Service Tier)

Configuration: Create a render.yaml file in your project root to handle automated deployments securely:

YAML
services:
  - type: web
    name: sovereign-opsagent-api
    env: python
    buildCommand: pip install -r agent_core/requirements.txt
    startCommand: cd agent_core && uvicorn supervisor:app --host 0.0.0.0 --port $PORT
    envVars:
      - key: PYTHON_VERSION
        value: 3.10.12

**[ADDED CHECK]**  
Show the exact `render.yaml` content, and confirm that the `startCommand` uses `uvicorn` and that our `supervisor.py` exports an `app` object (Flask or FastAPI). Ask me to confirm.  

2. The Interactive Frontend Portal (Vercel Hosting)
Hosting Platform: Vercel.com (Free Hobby Tier)

Configuration: Create a vercel.json file inside the /frontend-portal directory to handle frontend routing for single-page applications cleanly:

JSON
{
  "rewrites": [
    {"source": "/(.*)", "destination": "/index.html"}
  ]
}

**[ADDED CHECK]**  
Print the `vercel.json` content and ask “Place this in /frontend-portal? (YES/NO)”  

🔗 Connecting the Frontend to the Production Backend
Before pushing your final codebase to GitHub, make sure to update the API base URL in your frontend code (e.g., in /frontend-portal/src/App.js) to point directly to your live, production Render endpoint instead of a local port:

JavaScript
// Replace local URLs with your newly deployed live Render service URL
const BACKEND_API_URL = "https://sovereign-opsagent-api.onrender.com/api/remediate";

**[ADDED FINAL VERIFICATION]**  
Ask me for the exact Render URL once deployed. Then update the constant in the frontend code and show me the change before pushing.  

Once both services are successfully linked to your public GitHub repository, Render and Vercel will handle automated production deployments on every code change, giving you an impressive, fully operational live link to share with engineering teams.

**[END OF EXPANDED DOCUMENT – NO ORIGINAL TEXT REMOVED]**