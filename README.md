# flask-cicd-demo 🚀

A production-grade CI/CD pipeline built with GitHub Actions and Docker. Automatically runs tests, checks code quality, and pushes a Docker image to Docker Hub on every commit — completing in under 60 seconds.

---

## Pipeline Overview

```
Push to main
     │
     ▼
┌─────────┐     ┌─────────┐     ┌──────────────────┐
│  pytest  │────▶│ flake8  │────▶│ Docker Hub Push  │
│  (test)  │     │ (lint)  │     │ (build + deploy) │
└─────────┘     └─────────┘     └──────────────────┘
```

3 jobs run in sequence. If any job fails, the pipeline stops. No broken code reaches Docker Hub.

---

## What This Project Demonstrates

- **Automated testing** — pytest runs on every push, catching bugs before they ship
- **Code quality enforcement** — flake8 linting ensures clean, readable code
- **Docker image publishing** — multi-stage build pushed to Docker Hub with commit SHA tagging
- **CI catching real failures** — pipeline intentionally broke in Run #2 and caught the error automatically
- **Fast feedback loop** — full pipeline completes in ~34 seconds

---

## Tech Stack

| Tool | Purpose |
|------|---------|
| Python / Flask | Web application |
| pytest | Automated testing |
| flake8 | Code linting |
| Docker | Containerization |
| GitHub Actions | CI/CD automation |
| Docker Hub | Image registry |

---

## Project Structure

```
flask-cicd-demo/
├── app.py                        # Flask application
├── test_app.py                   # pytest test suite
├── Dockerfile                    # Container definition
├── requirements.txt              # Python dependencies
└── .github/
    └── workflows/
        └── ci.yml                # GitHub Actions pipeline
```

---

## GitHub Actions Workflow

```yaml
name: Flask App CI/CD

on:
  push:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Set up Python
        uses: actions/setup-python@v4
        with:
          python-version: '3.11'
      - name: Install dependencies
        run: pip install -r requirements.txt
      - name: Run tests
        run: pytest

  lint:
    needs: test
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Install flake8
        run: pip install flake8
      - name: Run linting
        run: flake8 .

  docker-build-push:
    needs: lint
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Login to Docker Hub
        uses: docker/login-action@v2
        with:
          username: ${{ secrets.DOCKERHUB_USERNAME }}
          password: ${{ secrets.DOCKERHUB_TOKEN }}
      - name: Build and push
        uses: docker/build-push-action@v4
        with:
          push: true
          tags: baqirops/flask-cicd-demo:${{ github.sha }}
```

---

## Docker Hub

Image is available at:
```
docker pull baqirops/flask-cicd-demo:<commit-sha>
```

Each image is tagged with its commit SHA — making every build fully traceable and rollback-ready.

---

## Pipeline Run History

| Run | Commit | Result | Time |
|-----|--------|--------|------|
| #1 | Initial pipeline | ✅ Pass | 12s |
| #2 | Break test (intentional) | ❌ Fail | 15s |
| #3 | Fix test | ✅ Pass | 13s |
| #4 | Add Docker Hub push | ✅ Pass | 34s |
| #5 | Add README | ✅ Pass | 43s |
| #6 | Add matrix builds + caching | ✅ Pass | 46s |

Run #2 shows the CI pipeline catching a real failure and preventing bad code from reaching production.

---

## How to Run Locally

```bash
# Clone the repo
git clone https://github.com/baqir-ops/flask-cicd-demo.git
cd flask-cicd-demo

# Install dependencies
pip install -r requirements.txt

# Run the app
python app.py

# Run tests
pytest

# Run linting
flake8 .
```

## Run with Docker

```bash
docker build -t flask-cicd-demo .
docker run -p 5000:5000 flask-cicd-demo
```

---

## Author

**Muhammad Baqir** — DevOps Engineer  
GitHub: [baqir-ops](https://github.com/baqir-ops)  
Docker Hub: [baqirops](https://hub.docker.com/u/baqirops)  
LinkedIn: [linkedin.com/in/baqir-nawaz-devops](https://linkedin.com/in/baqir-nawaz-devops)

    
