Perfect 👏 — since you’re preparing for **DevOps interviews**, let’s go over the **top GitHub Actions interview questions** that are commonly asked for **DevOps Engineer**, **CI/CD**, and **Automation** roles.

I’ve categorized them into **Basic → Intermediate → Advanced → Scenario-based** so you can revise step-by-step.

---

## 🟢 **Basic Level — Fundamentals**

1. **What is GitHub Actions?**
   → Explain it as GitHub’s native CI/CD and automation platform that helps automate workflows directly in repositories.

2. **What are workflows, jobs, steps, and actions?**

   * **Workflow:** Automated process defined in `.github/workflows/` YAML files.
   * **Job:** A set of steps that run on the same runner.
   * **Step:** A single task (run command or action).
   * **Action:** Reusable unit of code (custom or marketplace).

3. **Where do you store workflow definitions in a GitHub repo?**
   → Inside the `.github/workflows/` directory.

4. **What are GitHub runners?**
   → Machines that execute your jobs (GitHub-hosted or self-hosted).

5. **What are GitHub-hosted runners and self-hosted runners?**

   * Hosted: Managed by GitHub (Ubuntu, Windows, macOS).
   * Self-hosted: Your own servers, customized environments.

6. **How do you trigger a workflow?**
   → Using `on:` events like `push`, `pull_request`, `workflow_dispatch`, `schedule`, etc.

7. **What are composite actions in GitHub Actions?**
   → A reusable group of steps defined in a YAML file (like functions).

8. **What is the default location for secrets?**
   → In repository → Settings → Secrets → Actions.

9. **How do you pass secrets into a workflow?**

   ```yaml
   env:
     MY_SECRET: ${{ secrets.MY_SECRET }}
   ```

10. **How do you debug a failed GitHub Actions workflow?**
    → Use `ACTIONS_RUNNER_DEBUG` and `ACTIONS_STEP_DEBUG` secrets set to `true`.

---

## 🟠 **Intermediate Level — Core CI/CD Concepts**

1. **Explain `needs:` in GitHub Actions.**
   → Defines job dependencies to control execution order.
   Example:

   ```yaml
   jobs:
     build:
       runs-on: ubuntu-latest
     test:
       needs: build
       runs-on: ubuntu-latest
   ```

2. **What is the difference between `run:` and `uses:`?**

   * `run:` executes shell commands.
   * `uses:` calls a prebuilt action.

3. **How can you reuse workflows across repositories?**
   → Using `workflow_call` and referencing reusable workflows.

4. **How can you cache dependencies between runs?**
   → Using `actions/cache@v3`.

5. **What’s the purpose of `matrix` strategy?**
   → Run jobs in parallel with different configurations (e.g., Node versions).

   ```yaml
   strategy:
     matrix:
       node: [14, 16, 18]
   ```

6. **How can you manually trigger a workflow?**
   → Using `workflow_dispatch`.

7. **What are environment variables and how do you define them?**

   ```yaml
   env:
     NODE_ENV: production
   ```

8. **What’s the difference between `env:` and `secrets:`?**

   * `env:` → visible variables
   * `secrets:` → encrypted variables

9. **How do you store build artifacts between jobs?**
   → Use `actions/upload-artifact` and `actions/download-artifact`.

10. **What’s the difference between `pull_request` and `push` triggers?**

---

## 🔵 **Advanced Level — DevOps Production Scenarios**

1. **How do you deploy to AWS / Docker Hub / Kubernetes using GitHub Actions?**
   → Mention you can use AWS CLI actions, Docker login + push, or `kubectl` commands.

2. **How can you ensure sensitive data like AWS keys are protected?**
   → Use GitHub Secrets and never hardcode credentials.

3. **How do you handle multiple environments (dev, staging, prod)?**
   → Using `environments:` in workflow or separate YAMLs for each stage.

4. **What is a reusable workflow, and how do you call it?**

   ```yaml
   jobs:
     deploy:
       uses: org/repo/.github/workflows/deploy.yml@main
   ```

5. **How can you control concurrency (avoid multiple runs at once)?**

   ```yaml
   concurrency:
     group: deploy
     cancel-in-progress: true
   ```

6. **What are GitHub Environments and Environment Protection Rules?**
   → Define per-environment approvals, required reviewers, or secrets.

7. **How can you conditionally run a step or job?**

   ```yaml
   if: github.ref == 'refs/heads/main'
   ```

8. **How do you trigger workflows only for specific folder changes?**

   ```yaml
   on:
     push:
       paths:
         - 'frontend/**'
   ```

9. **What’s the difference between `workflow_dispatch` and `repository_dispatch`?**

   * `workflow_dispatch`: Manual trigger.
   * `repository_dispatch`: External trigger via API.

10. **How can you run a job only on pull requests to the main branch?**

```yaml
on:
  pull_request:
    branches:
      - main
```

---

## 🔴 **Scenario-Based / Practical Questions**

1. **You have 3 jobs — build, test, and deploy. Deploy should only run if build and test succeed. How do you do it?**
   → Use `needs: [build, test]`.

2. **How to deploy only when code is merged into `main`?**
   → Conditional using `if: github.ref == 'refs/heads/main'`.

3. **How do you create a CI pipeline for Node.js with caching?**
   → Use `actions/setup-node` and `actions/cache`.

4. **How do you roll back a failed deployment automatically?**
   → Add a rollback job triggered `if: failure()`.

5. **How do you handle secrets rotation in GitHub Actions?**
   → Update via API or GitHub UI; use environment secrets for versioned environments.

6. **You need to build Docker images and push to ECR. What steps are needed?**

   * Configure AWS credentials using `aws-actions/configure-aws-credentials`.
   * Login to ECR.
   * Build and push using `docker build` and `docker push`.

7. **How can you schedule a workflow every day at 2 AM?**

   ```yaml
   on:
     schedule:
       - cron: '0 2 * * *'
   ```

8. **How do you handle artifacts larger than 2GB?**
   → Use external storage like S3.

---

## ⚙️ **Bonus: Real-world DevOps CI/CD Question**

> “Explain how you would build a CI/CD pipeline using GitHub Actions for a full-stack app (frontend + backend + Docker).”

✅ **Answer outline:**

1. Build → Lint, Test, and Build both frontend and backend.
2. Dockerize → Build images, tag with commit SHA.
3. Push → Push to ECR or Docker Hub.
4. Deploy → Use SSH or kubectl to deploy to EC2 or Kubernetes.
5. Notify → Send deployment success/failure via Slack action.

---

Would you like me to create a **PDF cheat sheet** (with YAML examples + short answers for each question)?
It’s great for last-minute DevOps interview prep.
