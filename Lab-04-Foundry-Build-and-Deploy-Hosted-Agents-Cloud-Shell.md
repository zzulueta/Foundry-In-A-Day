# Lab 04: Building and Deploying Foundry Hosted Agents in Azure Cloud Shell

## Overview

In this lab, use Azure Cloud Shell, the Azure Developer CLI (`azd`), and the Cloud Shell editor to build and deploy two Microsoft Foundry hosted agents:

- An executive summary agent
- A multi-agent resume and job description analyzer

You will reuse the Foundry resource, `project01` project, and `gpt-5.4` model deployment from Lab 01.

**Estimated time:** 90 minutes

## Prerequisites

- An Azure subscription with access to the Foundry project and permission to deploy hosted agents
- Completion of Labs 01, 02, and 03, including the `rg-foundry-lab` resource group, Foundry resource, `project01` project, and `gpt-5.4` model deployment
- An Azure Cloud Shell session with **Bash** selected
- Python 3.13 available in Cloud Shell for local testing
- Basic understanding of Python and AI agents

## What you will build

- Two source-code hosted agents connected to the existing `project01` project and `gpt-5.4` deployment
- An executive summary agent that turns technical incident descriptions into concise, business-focused summaries
- A resume analyzer that compares job requirements with a candidate's stated qualifications

## Step 1: Build the Basic Agent

### 1.1 Prepare Cloud Shell

1. In the Azure Portal, click the **Cloud Shell** icon in the top navigation bar (looks like `>_`)

2. Select **Bash** as your shell environment

3. Select No storage account required.

4. Select your Subscription.

5. Select Apply.

6. Wait for Cloud Shell to initialize (this may take a minute)

7. Once Cloud Shell opens, you'll see a Bash command prompt

8. Install the AI agent extension:
   ```bash
   azd ext install microsoft.foundry
   ```

9. Authenticate:
   ```bash
   azd auth login
   ```

### 1.2 Initialize the agent project

1. Initialize the project from the manifest. This creates a new `agent-framework-agent-basic-responses` folder for you:

  ```bash
  azd ai agent init -m https://github.com/microsoft-foundry/foundry-samples/blob/main/samples/python/hosted-agents/agent-framework/responses/01-basic/azure.yaml
  ```
  > Follow the prompts to:
  - Login to your GitHub account 
  - Configure your Foundry project and model deployment. 

2. Change into the generated project folder (run all remaining `azd` commands from here):

  ```bash
  cd agent-framework-agent-basic-responses
  ```

### 1.3 Explore the project structure

1. Explore the project directory to understand its structure.
  ```bash
  code .
  ```

2. You should see a structure similar to the following:
  ```
  agent-framework-agent-basic-responses/
  ├── azure.yaml
  ├── src/
  │   └── agent-framework-agent-basic-responses/
  │       ├── .env.example
  │       ├── main.py
  │       └── pyproject.toml
  │       └── uv.lock
  └── README.md
  ```

  - `azure.yaml` contains the configuration for your Foundry project and model deployment.
  - `src/` contains the source code for your hosted agents.
  - `README.md` provides an overview of the project.
  - `.env.example` contains example environment variables for local development.
  - `pyproject.toml` defines the Python project and its dependencies.
  - `uv.lock` locks the versions of the dependencies.

### 1.4 Modify the azure.yaml

1. Open the `azure.yaml` file in the project directory.

2. Update the configuration as needed for your Foundry project and model deployment.
   - name: `basicagent` (found under services:)
   - deployments:
       - model name: `gpt-5.4`
       - name: `gpt-5.4`
       
3. Save the file (Ctrl+S) after making the necessary changes.

### 1.5 Modify the main.py file

1. Open the `main.py` file in the project directory.

2. Replace with the code from: <https://github.com/zzulueta/foundry-bootcamp/blob/master/Hosted%20Agent/basicagent.py>

3. Save the file (Ctrl+S) after making the necessary changes.


### 1.6 Rename the .env.example file to .env

1. Open the `.env.example` file in the project directory.

2. Update the environment variables for your local development.
    > You should get the Foundry Project Endpoint from the Home page of your Foundry project.

3. Save the file (Ctrl+S) after making the necessary changes.

4. Rename the `.env.example` file to `.env` for local development.
  ```bash
  cd src/agent-framework-agent-basic-responses/
  mv .env.example .env
  ```

### 1.7 Run the agent locally

1. Install uv and prepare your project’s Python environment:
  ```bash
  curl -LsSf https://astral.sh/uv/install.sh | sh
  source "$HOME/.local/bin/env"
  uv --version
  uv sync --frozen --python 3.13
  ```
  
2. Run the following command to start the agent locally:
  ```bash
  azd ai agent run
  ```
  > The agent host will start on `http://localhost:8088`.

3. Open another cloud shell to interact with the agent while it is running locally.
  ```bash
  curl -sS -H "Content-Type: application/json" \
  -X POST http://localhost:8088/responses \
  -d '{"input":"Users cannot access the internal Sales Portal. It returns 503 Service Unavailable and started this morning. Sales cannot submit customer orders, delaying new opportunities."}'
  ```

### 1.8 Deploy to Foundry

1. Once tested locally, stop the agent that is running locally by clicking Ctrl+C in the terminal where it is running.

2. Deploy to Microsoft Foundry:
  ```bash
  azd deploy
  ```

3. After deployment, you can interact with your agent through the Foundry interface or using the appropriate API endpoints.
```
I am currently unable to connect to the company VPN while working remotely. Whenever I enter my username and password, the VPN client attempts to connect but eventually displays the error message, “Authentication failed. Please verify your credentials and try again.” My regular internet connection is working normally.

I have already restarted my laptop, disconnected and reconnected to Wi-Fi, restarted the VPN client, and carefully re-entered my username and password. I also attempted the connection several times, but I continue to receive the same authentication error.

This issue is preventing me from accessing internal applications, shared folders, and other company resources required for my work. Please investigate whether the issue is related to my account credentials, MFA authentication, VPN configuration, or the VPN service itself, and let me know if there are any additional troubleshooting steps I should perform.
```

## Step 2: Build the Sequential Agent

### 2.1 Modify the azure.yaml

1. Open the `azure.yaml` file in the project directory.

2. Update the configuration as needed for your Foundry project and model deployment.
   - name: `sequentialagent` (found under services:)
       
3. Save the file (Ctrl+S) after making the necessary changes.

### 2.2 Modify the main.py file

1. Open the `main.py` file in the project directory.

2. Replace with the code from: <https://github.com/zzulueta/foundry-bootcamp/blob/master/Hosted%20Agent/main.py>

3. Save the file (Ctrl+S) after making the necessary changes.

### 2.3 Run the agent locally

1. Run the following command to start the agent locally:
  ```bash
  azd ai agent run
  ```
  > The agent host will start on `http://localhost:8088`.

2. Open another cloud shell to interact with the agent while it is running locally.
  ```bash
  curl -sS \
  -H "Content-Type: application/json" \
  -X POST http://localhost:8088/responses \
  -d '{"input":"**Resume:**\nJane Doe\nSenior Software Engineer with 5 years of experience in Python, Django, and AWS.\nBuilt microservices handling 10K+ requests/second. Led a team of 4 developers.\nCertifications: AWS Solutions Architect Associate.\nEducation: B.S. Computer Science, State University.\n\n**Job Description:**\nSenior Cloud Engineer at Contoso Ltd.\nRequired: Python, Azure, Kubernetes, Terraform, CI/CD pipelines.\nPreferred: Go, monitoring (Prometheus/Grafana), cost optimization.\nExperience: 5+ years in cloud infrastructure.\nCertifications: Azure Solutions Architect Expert preferred."}'
  ```

### 2.4 Deploy to Foundry

1. Once tested locally, stop the agent that is running locally by clicking Ctrl+C in the terminal where it is running.

2. Deploy to Microsoft Foundry:
  ```bash
  azd deploy
  ```

3. After deployment, you can interact with your agent through the Foundry interface or using the appropriate API endpoints.
```
**Resume:**
Jane Doe
Senior Software Engineer with 5 years of experience in Python, Django, and AWS.
Built microservices handling 10K+ requests/second. Led a team of 4 developers.
Certifications: AWS Solutions Architect Associate.
Education: B.S. Computer Science, State University.

**Job Description:**
Senior Cloud Engineer at Contoso Ltd.
Required: Python, Azure, Kubernetes, Terraform, CI/CD pipelines.
Preferred: Go, monitoring (Prometheus/Grafana), cost optimization.
Experience: 5+ years in cloud infrastructure.
Certifications: Azure Solutions Architect Expert preferred.
```

## Step 3: Build the Group Chat Agent

### 3.1 Modify the azure.yaml

1. Open the `azure.yaml` file in the project directory.

2. Update the configuration as needed for your Foundry project and model deployment.
   - name: `groupchatagent` (found under services:)
       
3. Save the file (Ctrl+S) after making the necessary changes.

### 3.2 Modify the main.py file

1. Open the `main.py` file in the project directory.

2. Replace with the code from: <https://github.com/zzulueta/foundry-bootcamp/blob/master/Hosted%20Agent/groupchat.py>

3. Save the file (Ctrl+S) after making the necessary changes.

### 3.3 Run the agent locally

1. Run in the same directory as the pyproject.toml file the following command to install a new dependency:
  ```bash
  uv add --python 3.13 agent-framework-orchestrations
  ```

2. Run the following command to start the agent locally:
  ```bash
  azd ai agent run
  ```
  > The agent host will start on `http://localhost:8088`.

3. Open another cloud shell to interact with the agent while it is running locally.
  ```bash
  curl -sS \
  -H "Content-Type: application/json" \
  -X POST http://localhost:8088/responses \
  -d '{"input":"**Resume:**\nJane Doe\nSenior Software Engineer with 5 years of experience in Python, Django, and AWS.\nBuilt microservices handling 10K+ requests/second. Led a team of 4 developers.\nCertifications: AWS Solutions Architect Associate.\nEducation: B.S. Computer Science, State University.\n\n**Job Description:**\nSenior Cloud Engineer at Contoso Ltd.\nRequired: Python, Azure, Kubernetes, Terraform, CI/CD pipelines.\nPreferred: Go, monitoring (Prometheus/Grafana), cost optimization.\nExperience: 5+ years in cloud infrastructure.\nCertifications: Azure Solutions Architect Expert preferred."}'
  ```

### 3.4 Deploy to Foundry

1. Once tested locally, stop the agent that is running locally by clicking Ctrl+C in the terminal where it is running.

2. Deploy to Microsoft Foundry:
  ```bash
  azd deploy
  ```

3. After deployment, you can interact with your agent through the Foundry interface or using the appropriate API endpoints.
```
Please evaluate this candidate for the role below.

=== RESUME ===
Name: Jordan Lee
Title: Cloud Engineer
Location: Singapore

Summary:
Cloud engineer with 4.5 years of experience building and operating containerized
workloads on AWS. Passionate about automation and developer experience.

Experience:
- Cloud Engineer, FinNova Pte Ltd (Mar 2023 – Present)
• Migrated 30+ microservices from EC2 to Amazon EKS, cutting infra cost by 22%
• Built CI/CD pipelines with GitHub Actions and Terraform for 12 product teams
• Set up Prometheus/Grafana monitoring and on-call runbooks
• Mentored 3 junior engineers

- DevOps Engineer, ShopQuick (Jan 2020 – Jun 2022)
• Containerized legacy Python apps with Docker
• Automated AWS infrastructure provisioning with CloudFormation
• Wrote Python scripts for log analysis and cost reporting

- Career break (Jul 2022 – Feb 2023): caregiving

Skills:
AWS (EKS, EC2, S3, IAM, Lambda), Kubernetes, Docker, Terraform, GitHub Actions,
Python, Bash, Prometheus, Grafana, Linux

Certifications:
- AWS Certified Solutions Architect – Associate (2022)
- Certified Kubernetes Administrator (CKA) (2023)

Education:
Diploma in Information Technology, Ngee Ann Polytechnic

=== JOB DESCRIPTION ===
Role: Senior Azure Platform Engineer
Company: Contoso Health

About the role:
Design and run the Azure platform that hosts our patient-facing AI services.

Required:
- 5+ years in cloud infrastructure or platform engineering
- Hands-on experience with Microsoft Azure (AKS, Azure Networking, Entra ID)
- Kubernetes in production
- Infrastructure as Code (Bicep or Terraform)
- CI/CD (Azure DevOps or GitHub Actions)

Preferred:
- Microsoft Certified: Azure Solutions Architect Expert or AZ-104
- Experience deploying AI/ML workloads (Azure OpenAI, Microsoft Foundry)
- Healthcare or other regulated-industry experience (HIPAA, PDPA)
- Observability with Azure Monitor / Application Insights

Education:
Bachelor's degree in Computer Science or equivalent experience

Responsibilities:
- Own AKS cluster design, security, and upgrades
- Build golden-path IaC templates for product teams
- Partner with AI teams to deploy models securely
- Lead incident response and mentor engineers
```

---

## Summary and Key Takeaways

In this lab, you used Azure Cloud Shell and the Azure Developer CLI to build, test, and deploy three hosted-agent patterns while reusing the Foundry project and model deployment from Lab 01:

- **Basic agent:** A single-purpose agent that translates technical incidents into executive-friendly summaries.
- **Sequential agent:** A multi-agent workflow that processes resume and job description analysis through ordered stages.
- **Group chat agent:** A collaborative workflow where specialized agents contribute to a shared analysis.

### Configuration and Dependencies

- **Deployment configuration:** `azure.yaml` defines the agent's Foundry configuration. The `name` under the agent service controls its deployed name; changing it targets a different agent rather than renaming an existing deployment.
- **Local configuration:** `.env` supplies the project endpoint and model settings used by the local application. Keep credentials and environment files out of source control.
- **Python dependencies:** Use `uv add` from the directory containing `pyproject.toml` to add libraries. It updates the dependency manifest, lockfile, and local environment; do not edit `uv.lock` manually.

### Development and Deployment

1. Configure the agent and implement its instructions or orchestration.
2. Run locally with `azd ai agent run` and test the Responses endpoint with `curl`.
3. Stop the local agent before starting another agent on the same port.
4. Deploy with `azd deploy` and verify behavior in Foundry using the same test inputs.

Local code or dependency changes do not automatically update the deployed agent. Restart locally to test changes, ensure deployment dependencies are included, and deploy again when ready.

### Practical Considerations

- Use single-purpose agents for focused tasks, sequential workflows for ordered processing, and group chat workflows when collaboration adds value.
- Test missing information, ambiguous requests, and mismatched qualifications. Check that responses use the supplied evidence rather than inventing facts.
- Treat resume analysis as decision support for human review, based only on job-related qualifications.
- This lab uses Cloud Shell without persistent storage. Download or commit non-sensitive source files before ending the session so your work is not lost.

**End of Lab**
