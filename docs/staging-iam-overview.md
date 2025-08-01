## 🚧 Staging Environment

| Type                      | Name                                                               | Role(s) Assigned                                                                                               |
|---------------------------|--------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------|
| Default Compute SA        | `YYYYYY-compute@developer.gserviceaccount.com`                     | `Editor` *(⚠️ overly permissive – consider reducing scope)*                                                   |
| Terraform-created SA      | `cicd-runner@project-id.iam.gserviceaccount.com`                   | Cloud Run Developer, Service Account User                                                                      |
| App Runtime SA            | `genai-app-sample-cr-sa@stagng-project-id.iam.gserviceaccount.com` | Cloud Trace Agent, Discovery Engine Editor, Logs Writer, Storage Admin, Vertex AI User                         |
| Bootstrap Auth SA         | `local-vscode-gcp-auth@project-id.iam.gserviceaccount.com`         | Cloud Build Editor, Cloud Trace Agent, Logs Writer, **Owner**, Vertex AI Administrator, Vertex AI User, Viewer |
| Admin Account             | `account_email@gmail.com`                                          | **Owner**                                                                                                      |

---

### 🔍 Explanations and Purpose

#### **Default Compute Service Account**
- **What it is**: Automatically created when Compute Engine is first enabled.
- **ID format**: `<project-number>-compute@developer.gserviceaccount.com`
- **Role assigned**: `Editor` by default (⚠️ Not recommended for production workloads).
- **Recommendation**: Use **custom service accounts** for workloads and restrict this account's access.

#### **CI/CD Runner Service Account**
- **What it is**: Created by Terraform.
- **Purpose**: Executes Cloud Run deployments and interacts with other GCP services during CI/CD pipelines.
- **Roles**: `Cloud Run Developer`, `Service Account User`.

#### **GenAI App Runtime Service Account**
- **What it is**: Bound to the Cloud Run application in the staging environment.
- **Purpose**: Grants the app secure, least-privilege access to GCP services it needs to function.
- **Roles**: Access to Vertex AI, Discovery Engine, and Storage — matching expected behavior for GenAI-based applications.

#### **Bootstrap Auth SA**
- **What it is**: A manually created service account used to authenticate local development tools like **VS Code** to GCP before Terraform applies infrastructure.
- **Purpose**: Enables Terraform execution, bootstrap configuration, and admin-level interaction with GCP from local dev environments.
- **Roles**: Broad privileges (`Owner`, `Vertex AI Admin`, `Logs Writer`, etc.)—**should not be used in production workloads**.

#### **Admin Account**
- **What it is**: A personal/admin Google account (e.g., `account_email@gmail.com`) with full Owner access.
- **Best Practice**: Use this account only for emergency escalations or manual project setup tasks.

---

### ✅ Staging vs Dev Differences

| Area                  | Dev                                               |                 Staging                              |
|-----------------------|---------------------------------------------------|------------------------------------------------------|
| **App Runtime SA**    | Not present or minimally scoped                   | Fully defined and bound to GenAI Cloud Run app       |
| **CI/CD Runner SA**   | Has broader permissions (Artifact Registry, etc.) | Scoped only to Cloud Run and SA usage                |
| **Roles**             | Dev is more permissive for testing/debugging      | Staging applies **least-privilege** for CI/CD flows  |
| **Usage Context**     | Local development and early bootstrapping         | Mid-tier environment for pre-prod testing            |

---

### ✅ Prod vs Staging Differences

| Area                  | Staging                                          | Prod                                                                   |
|-----------------------|--------------------------------------------------|------------------------------------------------------------------------|
| **App Runtime SA**    | `stagng-project-id` domain                       | `prod-project-id` domain, identical roles                              |
| **CI/CD Runner SA**   | Narrower scope                                   | Similar narrow scope                                                   |
| **Compute SA**        | Different account ID, same default account type  | Same default pattern with project-specific ID                          |
| **Bootstrap Auth SA** | Present                                          | Present, same roles                                                    |
| **Security Policy**   | Slightly more flexible for experimentation       | Must follow stricter controls, should avoid Owner role on workload SAs |

## ✅ Source

 LLM generated documentation based on my prompts from general curiosity, experience deploying and troubleshooting the application.
 Helped clear up and organize environments, accounts, roles and good practices around cloud infra setup
