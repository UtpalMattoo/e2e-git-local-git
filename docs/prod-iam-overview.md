## 🔒 Production (Prod) Environment – Service Accounts & IAM Roles

> **Project Purpose**: Hosts mission-critical GenAI apps and APIs. Must follow best practices for least-privilege IAM, logging, and deployment security.

---

### 👥 Service Accounts

| **Service Account ** | **Display Name**                     | **Assigned Roles**                                                                                                                                                 | **Purpose**                                                                                                                                                              |
|----------------------------------|--------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `****-compute@developer.gserviceaccount.com` | Default Compute SA                  | `Editor`                                                                                                                                                           | Automatically created by GCP for Compute Engine VMs. Grants broad permissions; if not used, remove or restrict. Left as-is unless VM-based infra is used.               |
| `cicd-runner@***.iam.gserviceaccount.com`    | CI/CD Runner SA                     | `Cloud Run Developer`, `Service Account User`                                                                                                                      | Deploys Cloud Run services as part of Terraform or Cloud Build pipelines. Only needs roles to deploy and impersonate execution SAs, ensuring separation of concerns.    |
| `genai-app-sample-cr-sa@***.iam.gserviceaccount.com` | Cloud Run GenAI App SA               | `Cloud Trace Agent`, `Discovery Engine Editor`, `Logs Writer`, `Storage Admin`, `Vertex AI User`                                                                  | Runtime identity for the GenAI application. Accesses Vertex AI, reads logs, writes metrics, and interacts with Discovery Engine. Strictly limited to what the app needs. |
| `local-vscode-gcp-auth@***.iam.gserviceaccount.com` | Local VSCode Auth SA                | `Cloud Build Editor`, `Cloud Trace Agent`, `Logs Writer`, `Owner`, `Vertex AI Administrator`, `Vertex AI User`, `Viewer`                                          | Used by developers to connect VSCode to GCP locally. High privileges to support manual setup and debugging, but **should not be used in CI/CD or runtime workloads.**    |
| `account-email@example.com`                | Human Admin Account (You)           | `Owner`                                                                                                                                                            | The primary admin account. Use with MFA enabled. Reserved for emergency access, billing, and resource provisioning. Use with caution and audit regularly.                |

---

### 🔐 Security & IAM Best Practices – Production

| **Area**                 | **Recommendation**                                                                                                                                         |
|--------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Workload SAs**         | Assign only roles necessary for runtime, such as `Vertex AI User`, `Logs Writer`, `Storage Admin`. Avoid elevated roles like `Editor` or `Owner`.          |
| **CI/CD Service Account**| Limit to `Cloud Run Developer` and `Service Account User`. It should **never** deploy as itself but only act on behalf of narrowly scoped workload SAs.    |
| **Human Accounts**       | Enforce MFA and limit `Owner` to as few accounts as possible. Use IAM conditions, org policy, and SCC to detect excessive privileges.                       |
| **Audit & Logging**      | Ensure `Cloud Trace`, `Cloud Logging`, and `Cloud Monitoring` are enabled. Set up log sinks to store long-term audit logs securely.                        |
| **Secrets Management**   | Never store secrets in code. Use **Secret Manager**, and consider **Workload Identity Federation** to avoid using long-lived service account keys.         |
| **Network Security**     | Restrict access with **Firewall Rules**, **VPC Service Controls**, and **Private Service Connect**. Ensure Cloud Run is not set to "Allow unauthenticated".|

---

### 🆚 Prod vs Staging Differences

| **Category**            | **Staging**                                                                                         | **Production**                                                                                                           |
|-------------------------|------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------|
| **Security Policy**     | More permissive; may allow `Owner` or `Editor` roles for quicker iteration.                         | Enforces strict separation of duties; workload SAs must follow least-privilege. Avoid using `Owner` in automation.      |
| **SA Role Scope**       | May include elevated permissions temporarily.                                                       | Roles are narrowly scoped per function (CI/CD, workload, bootstrap).                                                    |
| **Service Account Usage**| Fewer restrictions on service accounts.                                                            | SAs must be scoped only to their job; identity separation between build, deploy, and runtime.                           |
| **Deployment Workflow** | Allows experimentation with in-place updates, less restriction on CI/CD.                            | Enforces approval workflows, tagged releases, artifact promotion, and rollback paths.                                   |
| **Log & Monitoring**    | Logging is used, but may not be strictly audited.                                                   | Logging and metrics must be complete, auditable, and persisted (e.g., via Log Sinks to BigQuery or GCS).                |
| **Naming Convention**   | Prefixes like `stagng-`, used for visibility.                                                       | Uses `prod-` or no prefix. Clear naming is enforced to distinguish between runtime and deployment identities.           |

---

## ✅ Source

 LLM generated documentation based on my prompts from general curiosity, experience deploying and troubleshooting the application.
 Helped clear up and organize environments, accounts, roles and good practices around cloud infra setup
