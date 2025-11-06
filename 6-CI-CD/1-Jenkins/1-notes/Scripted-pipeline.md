Absolutely 👍 — here’s a **complete, structured Markdown (`.md`) note** on **Jenkins Scripted Pipeline**, including:

* Full explanation
* Comparison with Declarative Pipeline
* Syntax, examples, and features
* Advanced usage examples (parameters, stages, parallel, post conditions)

---

# 🧩 Jenkins Scripted Pipeline — Complete Notes

## 🏗️ What is a Jenkins Scripted Pipeline?

A **Scripted Pipeline** is one of the two ways to define a Jenkins Pipeline (the other is **Declarative Pipeline**).
It is written in **Groovy**, providing **maximum flexibility and control** over pipeline logic.

Scripted Pipelines were the **first generation** of Jenkins pipelines, used before the Declarative syntax was introduced.

---

## ⚙️ Scripted vs Declarative Pipeline — Quick Comparison

| Feature / Aspect       | **Scripted Pipeline**                                  | **Declarative Pipeline**                 |
| ---------------------- | ------------------------------------------------------ | ---------------------------------------- |
| **Language**           | Groovy (imperative)                                    | YAML-like structured syntax              |
| **Structure**          | Flexible, free-form code                               | Strict and well-defined structure        |
| **Ease of Use**        | Complex for beginners                                  | Easier to read/write                     |
| **Validation**         | Errors appear at runtime                               | Validated before execution               |
| **Control Flow**       | Full control — loops, conditions, functions            | Limited — restricted logic inside blocks |
| **When to Use**        | For complex logic, custom flow control, dynamic builds | For CI/CD pipelines with standard steps  |
| **Example Keywords**   | `node {}`, `stage {}`, `steps {}`                      | `pipeline {}`, `stages {}`, `steps {}`   |
| **Error Handling**     | Must be handled with try/catch                         | Built-in post conditions available       |
| **Parallel Execution** | Supported (manual code)                                | Easier with declarative `parallel` block |

---

## 🧠 Basic Structure of Scripted Pipeline

Scripted pipelines are written in **Groovy**, typically inside a `Jenkinsfile`.

```groovy
node {
    stage('Build') {
        echo 'Building the application...'
        sh 'npm install'
    }

    stage('Test') {
        echo 'Running tests...'
        sh 'npm test'
    }

    stage('Deploy') {
        echo 'Deploying the app...'
        sh './deploy.sh'
    }
}
```

### 🔍 Explanation:

* `node {}` → defines where the pipeline runs (agent).
* `stage('name')` → divides the pipeline into visual steps.
* `sh` / `bat` → runs shell or Windows commands.
* `echo` → prints messages in the console output.

---

## 🧩 Jenkins Pipeline Execution Flow

```
node
 ┣━━ stage('Build')
 ┃    ┗━━ steps...
 ┣━━ stage('Test')
 ┃    ┗━━ steps...
 ┗━━ stage('Deploy')
      ┗━━ steps...
```

Each stage executes sequentially on the defined Jenkins agent.

---

## 🛠️ Features of Scripted Pipeline

### 1. **Dynamic Logic**

Use loops, if-else, or custom functions freely.

```groovy
node {
    stage('Dynamic Stages') {
        def stages = ['Build', 'Test', 'Deploy']
        for (s in stages) {
            stage(s) {
                echo "Running stage: ${s}"
            }
        }
    }
}
```

---

### 2. **Parallel Execution**

```groovy
node {
    stage('Parallel Jobs') {
        parallel (
            "Unit Tests": {
                sh 'npm run test:unit'
            },
            "Integration Tests": {
                sh 'npm run test:integration'
            }
        )
    }
}
```

---

### 3. **Using Environment Variables**

```groovy
node {
    stage('Env Example') {
        env.ENVIRONMENT = "staging"
        echo "Deploying to ${env.ENVIRONMENT}"
    }
}
```

---

### 4. **Using Parameters**

You can define parameters in Jenkins UI and access them in the pipeline:

```groovy
node {
    stage('Input') {
        echo "Deploying to ${params.ENVIRONMENT}"
    }
}
```

Or define them inside the file (Declarative style hybrid):

```groovy
properties([
    parameters([
        string(name: 'ENVIRONMENT', defaultValue: 'dev', description: 'Environment to deploy')
    ])
])
```

---

### 5. **Error Handling with try/catch**

```groovy
node {
    try {
        stage('Build') {
            sh 'exit 1'
        }
    } catch (err) {
        echo "Build failed: ${err}"
        currentBuild.result = 'FAILURE'
    } finally {
        echo "Cleaning up..."
    }
}
```

---

### 6. **Using Credentials**

```groovy
node {
    stage('Login') {
        withCredentials([usernamePassword(credentialsId: 'dockerHub', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
            sh 'echo Logging into Docker Hub'
            sh "docker login -u ${USER} -p ${PASS}"
        }
    }
}
```

---

### 7. **Post-Build Actions (Manual in Scripted)**

Unlike Declarative pipelines that have `post {}` blocks, Scripted ones handle cleanup manually.

```groovy
node {
    try {
        stage('Build') {
            sh 'make build'
        }
    } catch (e) {
        echo 'Build failed!'
    } finally {
        echo 'Post build cleanup...'
        deleteDir()
    }
}
```

---

### 8. **Calling Functions**

You can define reusable Groovy functions:

```groovy
def deploy(env) {
    echo "Deploying to ${env}"
}

node {
    stage('Deploy') {
        deploy('production')
    }
}
```

---

## 🧱 Example: Full Scripted Pipeline

```groovy
node {
    try {
        stage('Checkout') {
            checkout scm
        }

        stage('Build') {
            sh 'npm install'
        }

        stage('Test') {
            parallel (
                "Unit Tests": { sh 'npm run test:unit' },
                "Integration Tests": { sh 'npm run test:integration' }
            )
        }

        stage('Package') {
            sh 'npm run build'
        }

        stage('Deploy') {
            sh './deploy.sh'
        }

    } catch (err) {
        echo "Pipeline failed: ${err}"
        currentBuild.result = 'FAILURE'
    } finally {
        echo "Pipeline completed!"
    }
}
```

---

## 🧩 Declarative Equivalent

```groovy
pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Build') {
            steps {
                sh 'npm install'
            }
        }
        stage('Test') {
            parallel {
                stage('Unit Tests') {
                    steps { sh 'npm run test:unit' }
                }
                stage('Integration Tests') {
                    steps { sh 'npm run test:integration' }
                }
            }
        }
        stage('Package') {
            steps { sh 'npm run build' }
        }
        stage('Deploy') {
            steps { sh './deploy.sh' }
        }
    }

    post {
        always { echo 'Pipeline completed!' }
        failure { echo 'Pipeline failed!' }
    }
}
```

---

## ⚡ Key Takeaways

| Concept        | Scripted Pipeline                | Declarative Pipeline     |
| -------------- | -------------------------------- | ------------------------ |
| Flexibility    | ✅ Maximum                        | ⚠️ Limited               |
| Syntax         | Groovy-based                     | Simple and structured    |
| Error Handling | Manual (try/catch)               | Built-in `post` blocks   |
| Best Use       | Complex pipelines, dynamic logic | Standard CI/CD           |
| Maintenance    | Harder for teams                 | Easier for collaboration |

---

## 🧰 When to Use Scripted Pipeline

✅ Use **Scripted** when:

* You need **complex logic**, loops, or dynamic stage creation.
* You want **full Groovy control**.
* You’re integrating **legacy pipelines** or doing **conditional branching**.

🚫 Avoid Scripted if:

* Your team prefers **readability over control**.
* You’re building **simple CI/CD pipelines** (Declarative is better).

---

## 🧾 Reference Groovy Snippets

| Task              | Code Snippet                              |
| ----------------- | ----------------------------------------- |
| Run Shell Command | `sh 'ls -la'`                             |
| Set Env Variable  | `env.MY_VAR = "value"`                    |
| Conditional Logic | `if (params.DEPLOY) { sh './deploy.sh' }` |
| Parallel Jobs     | `parallel job1: { ... }, job2: { ... }`   |
| Sleep/Wait        | `sleep(time: 5, unit: 'MINUTES')`         |

---

## 📚 Summary

* Scripted pipelines are **powerful but complex**.
* Written entirely in **Groovy syntax**.
* Allow **dynamic control, loops, conditions, and error handling**.
* Declarative pipelines are preferred for **readability and maintainability**, but both can **coexist**.

---

Would you like me to add **a real-world example** (like “Build → Test → Docker Build → Push → Deploy”) in Scripted style too?
I can include it at the end of the Markdown for your portfolio or interview prep.
