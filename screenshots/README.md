
# CI/CD Assignment 1 — Automated CI Pipeline

## 1. Project Overview

This project demonstrates how to build a basic Continuous Integration (CI) pipeline using GitHub Actions, Docker, Nginx, and HTMLHint.

The objective is to automatically run checks whenever code is pushed to GitHub.

For this assignment, I created a simple HTML webpage, packaged it using Docker, tested it locally, and configured GitHub Actions to automate the checking process.

My pipeline includes:

- Automatic triggering on every Git push
- Checking out the repository code
- HTML linting using HTMLHint
- Building a Docker image
- Checking a GitHub Secret using an environment variable

**GitHub Repository:** https://github.com/abdirisakm519-glitch/assignment1-cicd

---

## 2. Technologies Used

| Technology | Purpose |
|---|---|
| Git | Version control |
| GitHub | Hosting the repository |
| GitHub Actions | Automating the CI pipeline |
| Docker | Building and running containers |
| Nginx | Hosting the HTML webpage |
| HTML | Creating the webpage |
| HTMLHint | Checking HTML code for mistakes |
| GitHub Secrets | Storing sensitive values |
| Environment Variables | Passing configuration to workflow steps |
| VS Code | Writing and editing project files |

---

## 3. Project Structure

```text
assignment1-cicd/
├── .github/
│   └── workflows/
│       └── ci.yml
├── screenshots/
│   ├── ci-workflow.png
│   ├── dockerfile.png
│   ├── html-code.png
│   ├── nginx-webpage.png
│   ├── docker-build.png
│   ├── docker-run.png
│   ├── github-actions-secrets.png
│   └── github-actions.png
├── Dockerfile
├── index.html
└── README.md
```

---

## 4. HTML Webpage

I created an `index.html` file for my assignment.

The webpage displays a heading identifying the CI/CD project.

During testing, HTMLHint identified a problem with my original HTML because it was missing the `DOCTYPE` declaration.

I corrected the HTML document to use the proper HTML structure.

### HTML Source Code Screenshot

![HTML Source Code](screenshots/html-code.png)

### Nginx Webpage Screenshot

I opened my Docker-hosted webpage in the browser at:

http://localhost:9090

![Nginx Webpage](screenshots/nginx-webpage.png)

---

## 5. Docker Configuration

I created a Dockerfile to package my HTML webpage into a Docker image using Nginx.

### Dockerfile

```dockerfile
FROM nginx:alpine
COPY index.html /usr/share/nginx/html/index.html
```

### Dockerfile Explanation

- `FROM nginx:alpine` selects a lightweight Nginx base image.
- `COPY` copies the local HTML file into Nginx's webpage directory inside the Docker image.

### Dockerfile Screenshot

![Dockerfile](screenshots/dockerfile.png)

### Building the Docker Image

I used the following command:

```bash
docker build -t assignment1-cicd .
```

This command builds a Docker image called `assignment1-cicd`.

The `.` tells Docker to use the current project directory as the build context.

### Docker Build Screenshot

![Docker Build](screenshots/docker-build.png)

### Running the Docker Container

I started the container using:

```bash
docker run -d --name assignment1-cicd -p 9090:80 assignment1-cicd
```

Explanation:

- `docker run` creates and starts a container.
- `-d` runs it in the background.
- `--name` assigns the container a name.
- `-p 9090:80` maps port 9090 on my Mac to port 80 inside the container.
- `assignment1-cicd` is the name of my Docker image.

### Docker Run Screenshot

![Docker Run](screenshots/docker-run.png)

### Updating the Docker Image

After modifying my HTML file, I rebuilt the Docker image:

```bash
docker build -t assignment1-cicd .
```

I then stopped and removed the existing container:

```bash
docker stop assignment1-cicd
docker rm assignment1-cicd
```

Finally, I created a new container using the updated image:

```bash
docker run -d --name assignment1-cicd -p 9090:80 assignment1-cicd
```

This allowed me to view the updated webpage.

---

## 6. GitHub Actions CI Pipeline

I created a GitHub Actions workflow inside:

`.github/workflows/ci.yml`

The workflow automatically runs whenever I push changes to GitHub.

### CI Workflow

```yaml
name: assignment 1 cicd

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: checkout code
        uses: actions/checkout@v4

      - name: html linting
        run: npx --yes htmlhint index.html

      - name: docker build
        run: docker build -t assignment1-cicd .

      - name: check secrets
        env:
          MY_TEST_SECRET: ${{ secrets.MY_TEST_SECRET }}
        run: |
          if [ -n "$MY_TEST_SECRET" ]; then
            echo "Secret is set"
          else
            echo "Secret is not set"
            exit 1
          fi
```

### Explanation of the Workflow

**1. Push Trigger**

```yaml
on: [push]
```

Automatically runs the pipeline whenever changes are pushed to GitHub.

**2. Checkout Code**

```yaml
uses: actions/checkout@v4
```

Downloads the repository files onto the GitHub Actions runner.

**3. HTML Linting**

```yaml
run: npx --yes htmlhint index.html
```

Checks the HTML file for common mistakes and coding-standard problems.

**4. Docker Build**

```yaml
run: docker build -t assignment1-cicd .
```

Verifies that Docker can successfully build the image.

**5. GitHub Secrets Check**

Checks whether the configured GitHub Secret is available to the workflow.

### GitHub Actions Workflow Screenshot

![CI Workflow](screenshots/ci-workflow.png)

---

## 7. GitHub Secrets and Environment Variables

I created a practice GitHub Secret named:

`MY_TEST_SECRET`

I configured it using:

**GitHub Repository → Settings → Secrets and variables → Actions**

Inside my workflow, I used:

```yaml
env:
  MY_TEST_SECRET: ${{ secrets.MY_TEST_SECRET }}
```

This retrieves the stored GitHub Secret and makes it available as an environment variable within that step.

The workflow checks whether the environment variable contains a value.

If the secret is available, the workflow displays:

```text
Secret is set
```

If the secret is missing, the step fails.

The secret value is not printed in the workflow logs.

This was included as an additional learning exercise to practise using GitHub Secrets securely.

### GitHub Secrets Screenshot

![GitHub Secrets](screenshots/github-actions-secrets.png)

---

## 8. Troubleshooting and Debugging

During this assignment, I encountered several problems and learned how to troubleshoot them.

### Problem 1 — Empty Dockerfile

Initially, Docker reported:

```text
the Dockerfile cannot be empty
```

I corrected the Dockerfile by adding the required `FROM` and `COPY` instructions.

### Problem 2 — Docker Port Conflict

While running the container, I received an error indicating that port 8080 was already allocated.

I solved this by changing the host port to 9090:

```bash
docker run -d --name assignment1-cicd -p 9090:80 assignment1-cicd
```

This allowed my container to run without conflicting with another application.

### Problem 3 — GitHub Actions HTML Linting Failure

My first GitHub Actions run failed during HTML linting.

HTMLHint reported:

```text
Doctype must be declared before any non-comment content.
```

I identified that my HTML file was missing the required DOCTYPE declaration.

I corrected the HTML structure and prepared the updated file for another CI run.

This helped me understand how linting identifies errors automatically.

### Problem 4 — Git Files Not Tracked

Some files were not included in my initial Git push.

Using:

```bash
git status
```

I discovered that my `.github` directory, `Dockerfile`, and `index.html` were untracked.

I added the missing files, committed the changes, and pushed them to my GitHub repository.

---

## 9. Git and GitHub

I created a local Git repository and connected it to GitHub.

The main commands I used were:

### Check Git Status

```bash
git status
```

Shows modified and untracked files.

### Stage Changes

```bash
git add .
```

Stages project changes for the next commit.

### Commit Changes

```bash
git commit -m "Complete CI/CD Assignment 1"
```

Saves the staged changes in the local Git history.

### Push Changes

```bash
git push
```

Uploads the committed changes to GitHub.

Each push triggers the GitHub Actions workflow.

---

## 10. GitHub Actions Execution Evidence

My CI workflow includes:

1. Checkout code
2. HTML linting
3. Docker build
4. GitHub Secrets check

The workflow is configured to run automatically on every push.

### GitHub Actions Screenshot

![GitHub Actions](screenshots/github-actions.png)

The latest workflow result can be checked in the GitHub Actions tab:

https://github.com/abdirisakm519-glitch/assignment1-cicd/actions

---

## 11. What I Learned

Through this assignment, I learned how to:

- Create and manage a GitHub repository
- Initialize Git locally
- Stage, commit, and push changes
- Write a Dockerfile
- Build Docker images
- Create and run Docker containers
- Understand Docker port mapping
- Rebuild images after updating application files
- Create a GitHub Actions workflow
- Use YAML syntax and indentation
- Automatically trigger CI checks on a push
- Use HTMLHint for automated linting
- Debug GitHub Actions failures
- Work with GitHub Secrets
- Use environment variables with `env:`
- Organize project screenshots
- Document a project using Markdown

---

## 12. Conclusion

This project demonstrates how to create a basic Continuous Integration pipeline using GitHub Actions.

I created an HTML webpage, packaged it with Docker and Nginx, and tested it locally.

I configured GitHub Actions to automatically check the HTML, build the Docker image, and verify that a GitHub Secret is available whenever changes are pushed.

I also gained experience troubleshooting Docker, Git, and CI pipeline errors.

The assignment improved my understanding of how automated checks help identify problems during software development.

---

## 13. Repository Links

**GitHub Repository:**

https://github.com/abdirisakm519-glitch/assignment1-cicd

**GitHub Actions:**

https://github.com/abdirisakm519-glitch/assignment1-cicd/actions
