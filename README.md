# F3Next-Public


F3Next 🚀
Automated ERP Implementation & Management System for Manufacturing

F3Next is an event-driven, multi-agent system designed to automate the end-to-end lifecycle of ERP adoption for manufacturers. By leveraging a container-based architecture and temporal workflows, F3Next handles selection, configuration, data import, testing, hosting, and ongoing support without heavy manual intervention.

🏗 System Architecture
F3Next implements a container-based architecture where every ERP module operates within a separate container/directory. The system is event-driven and orchestrated using Temporal task queues to ensure reliability across long-running workflows.

Core Workflow
The system follows a linear but concurrent automation pipeline:

Discovery: AI agents interact with the user to identify needs.

Infrastructure Provisioning: Dedicated Docker containers are deployed on EC2 clusters.

Configuration: Modules are configured via a "Node Bank" (Q&A-driven tagging system).

Data Migration: Templates are generated, validated, and imported automatically.

🤖 The Agent Ecosystem
F3Next relies on a suite of specialized agents, each responsible for a specific domain of the implementation process:

1. Discovery Agent (Chatbot)
Role: The first point of contact.

Function: Chats with the user to discover pain points and operational requirements.

Output: Recommends the specific ERP software and modules required, then initiates the respective Temporal task queues.

2. Infra Agent
Role: DevOps & Deployment.

Function: Automatically provisions resources on an EC2 cluster.

Output: Deploys a Docker container for the selected application/module and sets up a self-managed database.

3. Config Agent
Role: Requirement Gathering.

Function: Asks pre-configured, niche-specific questions stored in Node Banks (PostgreSQL).

Logic: Every answer extracts specific tags that refine the company's niche and determine the subsequent branch of questions.

4. Config-Apply Agent
Role: Execution.

Function: Takes the structured configuration data obtained by the Config Agent.

Output: Applies settings directly to the deployed container instance.

5. Template Agent
Role: Data Preparation.

Function: Generates custom CSV templates based on the active configuration.

Output: Provides the user with the exact format needed for their company data.

6. Validation Agent
Role: Quality Assurance.

Function: Scans user-uploaded CSV files for errors, formatting issues, or data inconsistencies before import.

7. Import Agent
Role: Migration.

Function: Takes the validated data and executes the bulk import into the deployed ERP instance.

🛠 Tech Stack
Orchestration: Temporal.io (Workflow management)

Backend: Python 3.11, FastAPI

AI & Logic: LangChain

Database: PostgreSQL (Node Banks & Application Data), Redis

Infrastructure: Docker, AWS EC2

Frontend: Next.js

📂 Project Structure
Bash
f3next/
├── containers/             # Source code for specific agents (Discovery, Config, etc.)
├── backend/                # FastAPI backend services
├── infra/                  # Docker compose files and IaC scripts
└── frontend/               # Next.js user interface
