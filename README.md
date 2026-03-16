# 🎯 Complete DevOps Best Practices Implementation Guide

> **目標：從零到一，用實際代碼實現 CI/CD、容器化、監控，確保軟件交付的可靠性、安全性和效率。**

---

## 📖 全景圖：我們要建什麼？

text

```text
┌──────────────────────────────────────────────────────────────┐
│                                                              │
│  開發者寫代碼                                                 │
│     │                                                        │
│     ▼                                                        │
│  ┌──────────────────────────────────────────────────────┐    │
│  │  🔵 Phase 1: Source Control (Git)                     │    │
│  │  → Git branching strategy                             │    │
│  │  → Pull Requests + Code Review                        │    │
│  └──────────────────┬───────────────────────────────────┘    │
│                     │                                        │
│                     ▼                                        │
│  ┌──────────────────────────────────────────────────────┐    │
│  │  🟢 Phase 2: CI Pipeline                              │    │
│  │  → Lint → Test → Build → Scan → Artifact              │    │
│  └──────────────────┬───────────────────────────────────┘    │
│                     │                                        │
│                     ▼                                        │
│  ┌──────────────────────────────────────────────────────┐    │
│  │  🟡 Phase 3: Containerization (Docker)                │    │
│  │  → Dockerfile → Build Image → Push to Registry        │    │
│  └──────────────────┬───────────────────────────────────┘    │
│                     │                                        │
│                     ▼                                        │
│  ┌──────────────────────────────────────────────────────┐    │
│  │  🟠 Phase 4: CD Pipeline                              │    │
│  │  → Deploy to Staging → Test → Deploy to Production    │    │
│  └──────────────────┬───────────────────────────────────┘    │
│                     │                                        │
│                     ▼                                        │
│  ┌──────────────────────────────────────────────────────┐    │
│  │  🔴 Phase 5: Orchestration (Kubernetes)               │    │
│  │  → Deployment → Service → Ingress → Auto-scaling      │    │
│  └──────────────────┬───────────────────────────────────┘    │
│                     │                                        │
│                     ▼                                        │
│  ┌──────────────────────────────────────────────────────┐    │
│  │  🟣 Phase 6: Monitoring & Observability               │    │
│  │  → Metrics → Logs → Traces → Alerts                   │    │
│  └──────────────────┬───────────────────────────────────┘    │
│                     │                                        │
│                     ▼                                        │
│  ┌──────────────────────────────────────────────────────┐    │
│  │  ⚫ Phase 7: Security (DevSecOps)                     │    │
│  │  → SAST → SCA → DAST → Secret Scanning               │    │
│  └──────────────────────────────────────────────────────┘    │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## 📁 Project Structure（項目結構）

text

```text
my-awesome-app/
├── 📁 .github/
│   └── 📁 workflows/
│       ├── ci.yml                    # CI Pipeline
│       ├── cd-staging.yml            # CD to Staging
│       └── cd-production.yml         # CD to Production
│
├── 📁 src/                           # Application Source Code
│   ├── app.py                        # Main application
│   ├── routes/
│   ├── services/
│   ├── models/
│   └── utils/
│
├── 📁 tests/                         # Tests
│   ├── unit/
│   ├── integration/
│   └── e2e/
│
├── 📁 docker/                        # Docker files
│   ├── Dockerfile                    # Production Dockerfile
│   ├── Dockerfile.dev                # Development Dockerfile
│   └── docker-compose.yml            # Local development
│
├── 📁 k8s/                           # Kubernetes manifests
│   ├── base/
│   │   ├── deployment.yml
│   │   ├── service.yml
│   │   ├── ingress.yml
│   │   ├── hpa.yml
│   │   └── configmap.yml
│   ├── overlays/
│   │   ├── staging/
│   │   └── production/
│   └── kustomization.yml
│
├── 📁 terraform/                     # Infrastructure as Code
│   ├── main.tf
│   ├── variables.tf
│   └── outputs.tf
│
├── 📁 monitoring/                    # Monitoring configs
│   ├── prometheus/
│   │   └── prometheus.yml
│   ├── grafana/
│   │   └── dashboards/
│   └── alertmanager/
│       └── alertmanager.yml
│
├── 📁 scripts/                       # Utility scripts
│   ├── setup.sh
│   └── health-check.sh
│
├── .dockerignore
├── .gitignore
├── .env.example
├── requirements.txt
├── pyproject.toml
└── README.md
```

---

## 🔵 Phase 1: Source Control — Git Strategy

### Git Branching Strategy（Trunk-Based Development）

text

```text
┌──────────────────────────────────────────────────────┐
│                                                      │
│  main ────●────●────●────●────●────●────●──→        │
│           │         ↑    │         ↑                 │
│           │         │    │         │                 │
│  feature/ └──●──●──●┘   └──●──●──●┘                │
│  branches   feat-1        feat-2                     │
│                                                      │
│  規則：                                              │
│  1. main 分支永遠是可部署的                           │
│  2. Feature branches 從 main 分出                    │
│  3. 通過 Pull Request 合併回 main                    │
│  4. PR 必須通過 CI + Code Review                     │
│  5. 合併後自動部署到 Staging                         │
│  6. 手動批准後部署到 Production                      │
│                                                      │
└──────────────────────────────────────────────────────┘
```

### Branch Protection Rules（.github settings）：

yaml

```yaml
# 在 GitHub Repository Settings 中配置：

# Branch: main
# Protection Rules:
#   ✅ Require pull request reviews (至少 1 人審查)
#   ✅ Require status checks to pass (CI 必須通過)
#   ✅ Require branches to be up to date
#   ✅ Require signed commits
#   ✅ Do not allow force pushes
#   ✅ Do not allow deletions
```

### Commit Convention（提交規範）：

bash

```bash
# Conventional Commits 格式：
# <type>(<scope>): <description>

# Types:
# feat:     新功能
# fix:      修復 Bug
# docs:     文檔更新
# style:    代碼格式（不影響邏輯）
# refactor: 重構
# test:     測試
# chore:    構建/工具變更
# ci:       CI/CD 變更
# perf:     性能優化
# security: 安全修復

# 範例：
git commit -m "feat(auth): add JWT token refresh mechanism"
git commit -m "fix(payment): resolve double charge issue #123"
git commit -m "ci: add security scanning to pipeline"
git commit -m "docs(api): update API documentation for v2"
```

---

## 🟢 Phase 2: CI Pipeline — GitHub Actions

### 完整的 CI Pipeline

yaml

```yaml
# .github/workflows/ci.yml
# ============================================
# 🟢 Continuous Integration Pipeline
# 觸發條件：每次 Push 或 PR 到 main
# ============================================

name: 🚀 CI Pipeline

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

# 設定環境變量
env:
  PYTHON_VERSION: "3.11"
  DOCKER_REGISTRY: "ghcr.io"
  IMAGE_NAME: ${{ github.repository }}

# ============================================
# 定義所有 Jobs
# ============================================
jobs:

  # ==========================================
  # Job 1: 代碼質量檢查
  # ==========================================
  code-quality:
    name: 🔍 Code Quality
    runs-on: ubuntu-latest
    
    steps:
      # Step 1: 拉取代碼
      - name: 📥 Checkout code
        uses: actions/checkout@v4
      
      # Step 2: 設定 Python
      - name: 🐍 Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: ${{ env.PYTHON_VERSION }}
          cache: 'pip'
      
      # Step 3: 安裝依賴
      - name: 📦 Install dependencies
        run: |
          python -m pip install --upgrade pip
          pip install -r requirements.txt
          pip install -r requirements-dev.txt
      
      # Step 4: 代碼格式檢查（Black）
      - name: 🎨 Check code formatting (Black)
        run: |
          black --check --diff src/ tests/
      
      # Step 5: Import 排序檢查（isort）
      - name: 📋 Check import sorting (isort)
        run: |
          isort --check-only --diff src/ tests/
      
      # Step 6: Linting（Ruff — 比 flake8 快 100 倍）
      - name: 🔍 Lint with Ruff
        run: |
          ruff check src/ tests/
      
      # Step 7: 類型檢查（MyPy）
      - name: 🏷️ Type checking (MyPy)
        run: |
          mypy src/ --ignore-missing-imports

  # ==========================================
  # Job 2: 單元測試
  # ==========================================
  unit-tests:
    name: 🧪 Unit Tests
    runs-on: ubuntu-latest
    needs: code-quality  # 代碼質量通過後才跑測試
    
    # 測試矩陣（多 Python 版本）
    strategy:
      matrix:
        python-version: ["3.10", "3.11", "3.12"]
    
    steps:
      - name: 📥 Checkout code
        uses: actions/checkout@v4
      
      - name: 🐍 Set up Python ${{ matrix.python-version }}
        uses: actions/setup-python@v5
        with:
          python-version: ${{ matrix.python-version }}
          cache: 'pip'
      
      - name: 📦 Install dependencies
        run: |
          pip install -r requirements.txt
          pip install -r requirements-dev.txt
      
      # 運行單元測試 + 覆蓋率報告
      - name: 🧪 Run unit tests with coverage
        run: |
          pytest tests/unit/ \
            --cov=src \
            --cov-report=xml:coverage.xml \
            --cov-report=html:htmlcov \
            --cov-fail-under=80 \
            --junitxml=test-results.xml \
            -v
      
      # 上傳覆蓋率報告
      - name: 📊 Upload coverage to Codecov
        if: matrix.python-version == '3.11'
        uses: codecov/codecov-action@v3
        with:
          file: coverage.xml
          fail_ci_if_error: true
      
      # 上傳測試結果
      - name: 📋 Upload test results
        if: always()
        uses: actions/upload-artifact@v4
        with:
          name: test-results-${{ matrix.python-version }}
          path: test-results.xml

  # ==========================================
  # Job 3: 整合測試
  # ==========================================
  integration-tests:
    name: 🔗 Integration Tests
    runs-on: ubuntu-latest
    needs: unit-tests
    
    # 啟動依賴服務（PostgreSQL, Redis）
    services:
      postgres:
        image: postgres:16-alpine
        env:
          POSTGRES_DB: testdb
          POSTGRES_USER: testuser
          POSTGRES_PASSWORD: testpass
        ports:
          - 5432:5432
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
      
      redis:
        image: redis:7-alpine
        ports:
          - 6379:6379
        options: >-
          --health-cmd "redis-cli ping"
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
    
    steps:
      - name: 📥 Checkout code
        uses: actions/checkout@v4
      
      - name: 🐍 Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: ${{ env.PYTHON_VERSION }}
          cache: 'pip'
      
      - name: 📦 Install dependencies
        run: |
          pip install -r requirements.txt
          pip install -r requirements-dev.txt
      
      - name: 🔗 Run integration tests
        env:
          DATABASE_URL: postgresql://testuser:testpass@localhost:5432/testdb
          REDIS_URL: redis://localhost:6379/0
        run: |
          pytest tests/integration/ -v --timeout=60

  # ==========================================
  # Job 4: 安全掃描 (DevSecOps)
  # ==========================================
  security-scan:
    name: 🔒 Security Scan
    runs-on: ubuntu-latest
    needs: code-quality
    
    steps:
      - name: 📥 Checkout code
        uses: actions/checkout@v4
      
      # SAST - 靜態代碼安全分析
      - name: 🔍 SAST - Bandit (Python Security)
        run: |
          pip install bandit
          bandit -r src/ -f json -o bandit-report.json || true
          bandit -r src/ -ll  # 只顯示 Medium+ 的問題
      
      # SCA - 依賴庫漏洞掃描
      - name: 📦 SCA - Safety (Dependency Check)
        run: |
          pip install safety
          safety check --full-report
      
      # Secret 掃描 - 檢查是否有密鑰洩露
      - name: 🔑 Secret Scanning - TruffleHog
        uses: trufflesecurity/trufflehog@main
        with:
          extra_args: --only-verified
      
      # Snyk 漏洞掃描
      - name: 🛡️ Snyk Security Scan
        uses: snyk/actions/python-3.10@master
        continue-on-error: true
        env:
          SNYK_TOKEN: ${{ secrets.SNYK_TOKEN }}
        with:
          args: --severity-threshold=high
      
      # 上傳安全報告
      - name: 📋 Upload security reports
        if: always()
        uses: actions/upload-artifact@v4
        with:
          name: security-reports
          path: |
            bandit-report.json

  # ==========================================
  # Job 5: 構建 Docker Image
  # ==========================================
  build:
    name: 🐳 Build Docker Image
    runs-on: ubuntu-latest
    needs: [unit-tests, integration-tests, security-scan]
    
    permissions:
      contents: read
      packages: write
    
    outputs:
      image_tag: ${{ steps.meta.outputs.tags }}
      image_digest: ${{ steps.build-push.outputs.digest }}
    
    steps:
      - name: 📥 Checkout code
        uses: actions/checkout@v4
      
      # 設定 Docker Buildx（支持多架構構建）
      - name: 🔧 Set up Docker Buildx
        uses: docker/setup-buildx-action@v3
      
      # 登入 Container Registry
      - name: 🔐 Login to GitHub Container Registry
        uses: docker/login-action@v3
        with:
          registry: ${{ env.DOCKER_REGISTRY }}
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}
      
      # 生成 Image 標籤
      - name: 🏷️ Docker metadata
        id: meta
        uses: docker/metadata-action@v5
        with:
          images: ${{ env.DOCKER_REGISTRY }}/${{ env.IMAGE_NAME }}
          tags: |
            # Git SHA
            type=sha,prefix=
            # Branch name
            type=ref,event=branch
            # PR number
            type=ref,event=pr
            # Semantic version
            type=semver,pattern={{version}}
            # Latest (only on main)
            type=raw,value=latest,enable={{is_default_branch}}
      
      # 構建和推送 Docker Image
      - name: 🐳 Build and Push Docker Image
        id: build-push
        uses: docker/build-push-action@v5
        with:
          context: .
          file: docker/Dockerfile
          push: ${{ github.event_name != 'pull_request' }}
          tags: ${{ steps.meta.outputs.tags }}
          labels: ${{ steps.meta.outputs.labels }}
          cache-from: type=gha
          cache-to: type=gha,mode=max
          platforms: linux/amd64,linux/arm64
      
      # 掃描 Docker Image 漏洞
      - name: 🔍 Scan Docker Image - Trivy
        uses: aquasecurity/trivy-action@master
        with:
          image-ref: ${{ env.DOCKER_REGISTRY }}/${{ env.IMAGE_NAME }}:${{ github.sha }}
          format: 'sarif'
          output: 'trivy-results.sarif'
          severity: 'CRITICAL,HIGH'
          exit-code: '1'
      
      # 上傳掃描結果到 GitHub Security
      - name: 📋 Upload Trivy scan results
        uses: github/codeql-action/upload-sarif@v2
        if: always()
        with:
          sarif_file: 'trivy-results.sarif'
      
      # 打印 Image 信息
      - name: 📊 Image Summary
        run: |
          echo "### 🐳 Docker Image Built Successfully" >> $GITHUB_STEP_SUMMARY
          echo "| Property | Value |" >> $GITHUB_STEP_SUMMARY
          echo "|----------|-------|" >> $GITHUB_STEP_SUMMARY
          echo "| Image | \`${{ env.IMAGE_NAME }}\` |" >> $GITHUB_STEP_SUMMARY
          echo "| Tags | \`${{ steps.meta.outputs.tags }}\` |" >> $GITHUB_STEP_SUMMARY
          echo "| Digest | \`${{ steps.build-push.outputs.digest }}\` |" >> $GITHUB_STEP_SUMMARY

  # ==========================================
  # Job 6: 通知
  # ==========================================
  notify:
    name: 📢 Notify
    runs-on: ubuntu-latest
    needs: [build]
    if: always()
    
    steps:
      - name: 📢 Slack Notification
        uses: 8398a7/action-slack@v3
        with:
          status: ${{ job.status }}
          text: |
            CI Pipeline ${{ job.status == 'success' && '✅ Passed' || '❌ Failed' }}
            Branch: ${{ github.ref_name }}
            Commit: ${{ github.sha }}
            Author: ${{ github.actor }}
          webhook_url: ${{ secrets.SLACK_WEBHOOK }}
```

---

## 🟡 Phase 3: Containerization — Docker

### Production Dockerfile（多階段構建）

dockerfile

```dockerfile
# docker/Dockerfile
# ============================================
# 🐳 Multi-stage Production Dockerfile
# Best Practices:
#   ✅ Multi-stage build（減小 Image 大小）
#   ✅ Non-root user（安全）
#   ✅ Layer caching（加速構建）
#   ✅ .dockerignore（排除不需要的文件）
#   ✅ Health check（健康檢查）
#   ✅ Minimal base image（Alpine）
# ============================================

# ============================================
# Stage 1: Builder（構建階段）
# ============================================
FROM python:3.11-slim AS builder

# 設定工作目錄
WORKDIR /app

# 安裝系統依賴（需要編譯的庫）
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
        build-essential \
        libpq-dev \
    && rm -rf /var/lib/apt/lists/*

# 先複製依賴文件（利用 Docker layer cache）
COPY requirements.txt .

# 安裝 Python 依賴到 /install 目錄
RUN pip install --no-cache-dir --prefix=/install -r requirements.txt

# ============================================
# Stage 2: Production（生產階段）
# ============================================
FROM python:3.11-slim AS production

# 元數據
LABEL maintainer="your-team@company.com"
LABEL version="1.0.0"
LABEL description="My Awesome App"

# 設定環境變量
ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONPATH=/app \
    PORT=8000

# 安裝運行時依賴（不包含構建工具）
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
        libpq5 \
        curl \
        tini \
    && rm -rf /var/lib/apt/lists/*

# 創建非 root 用戶（安全最佳實踐）
RUN groupadd -r appuser && \
    useradd -r -g appuser -d /app -s /sbin/nologin appuser

# 設定工作目錄
WORKDIR /app

# 從 builder 階段複製已安裝的依賴
COPY --from=builder /install /usr/local

# 複製應用代碼
COPY src/ ./src/
COPY scripts/health-check.sh ./scripts/

# 設定文件權限
RUN chown -R appuser:appuser /app && \
    chmod +x scripts/health-check.sh

# 切換到非 root 用戶
USER appuser

# 暴露端口
EXPOSE ${PORT}

# 健康檢查
HEALTHCHECK --interval=30s --timeout=10s --start-period=5s --retries=3 \
    CMD curl -f http://localhost:${PORT}/health || exit 1

# 使用 tini 作為 PID 1（正確處理信號）
ENTRYPOINT ["tini", "--"]

# 啟動應用
CMD ["python", "-m", "uvicorn", "src.app:app", \
     "--host", "0.0.0.0", \
     "--port", "8000", \
     "--workers", "4", \
     "--access-log"]
```

### Docker Compose（本地開發）

yaml

```yaml
# docker/docker-compose.yml
# ============================================
# 🐳 Local Development Environment
# ============================================

version: "3.9"

services:
  # ==========================
  # 應用服務
  # ==========================
  app:
    build:
      context: ..
      dockerfile: docker/Dockerfile.dev
    container_name: my-app
    ports:
      - "8000:8000"
    volumes:
      - ../src:/app/src          # 代碼熱重載
      - ../tests:/app/tests
    environment:
      - DATABASE_URL=postgresql://user:pass@postgres:5432/appdb
      - REDIS_URL=redis://redis:6379/0
      - LOG_LEVEL=DEBUG
      - ENV=development
    depends_on:
      postgres:
        condition: service_healthy
      redis:
        condition: service_healthy
    restart: unless-stopped
    networks:
      - app-network

  # ==========================
  # PostgreSQL
  # ==========================
  postgres:
    image: postgres:16-alpine
    container_name: my-postgres
    ports:
      - "5432:5432"
    environment:
      POSTGRES_DB: appdb
      POSTGRES_USER: user
      POSTGRES_PASSWORD: pass
    volumes:
      - postgres_data:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U user -d appdb"]
      interval: 10s
      timeout: 5s
      retries: 5
    networks:
      - app-network

  # ==========================
  # Redis
  # ==========================
  redis:
    image: redis:7-alpine
    container_name: my-redis
    ports:
      - "6379:6379"
    volumes:
      - redis_data:/data
    healthcheck:
      test: ["CMD", "redis-cli", "ping"]
      interval: 10s
      timeout: 5s
      retries: 5
    networks:
      - app-network

  # ==========================
  # Monitoring Stack
  # ==========================
  prometheus:
    image: prom/prometheus:latest
    container_name: my-prometheus
    ports:
      - "9090:9090"
    volumes:
      - ../monitoring/prometheus/prometheus.yml:/etc/prometheus/prometheus.yml
      - prometheus_data:/prometheus
    networks:
      - app-network

  grafana:
    image: grafana/grafana:latest
    container_name: my-grafana
    ports:
      - "3000:3000"
    environment:
      - GF_SECURITY_ADMIN_PASSWORD=admin
    volumes:
      - grafana_data:/var/lib/grafana
      - ../monitoring/grafana/dashboards:/etc/grafana/provisioning/dashboards
    depends_on:
      - prometheus
    networks:
      - app-network

volumes:
  postgres_data:
  redis_data:
  prometheus_data:
  grafana_data:

networks:
  app-network:
    driver: bridge
```

### .dockerignore

text

```text
# .dockerignore
.git
.github
.gitignore
.env
.env.*
*.md
LICENSE
docker-compose*.yml
Dockerfile*
__pycache__
*.pyc
*.pyo
.pytest_cache
.mypy_cache
.ruff_cache
htmlcov
coverage.xml
.coverage
*.egg-info
dist
build
node_modules
.vscode
.idea
terraform/
monitoring/
k8s/
tests/
docs/
```

---

## 🟠 Phase 4: CD Pipeline — Staging & Production

### CD to Staging（自動部署到 Staging）

yaml

```yaml
# .github/workflows/cd-staging.yml
# ============================================
# 🟠 CD Pipeline - Deploy to Staging
# 觸發條件：main 分支 CI 通過後自動觸發
# ============================================

name: 🚀 Deploy to Staging

on:
  workflow_run:
    workflows: ["🚀 CI Pipeline"]
    branches: [main]
    types:
      - completed

env:
  CLUSTER_NAME: my-cluster-staging
  NAMESPACE: staging
  DOCKER_REGISTRY: ghcr.io
  IMAGE_NAME: ${{ github.repository }}

jobs:
  deploy-staging:
    name: 🏗️ Deploy to Staging
    runs-on: ubuntu-latest
    if: ${{ github.event.workflow_run.conclusion == 'success' }}
    
    environment:
      name: staging
      url: https://staging.myapp.com
    
    steps:
      - name: 📥 Checkout code
        uses: actions/checkout@v4
      
      # 配置 kubectl
      - name: ⚙️ Configure kubectl
        uses: azure/k8s-set-context@v3
        with:
          method: kubeconfig
          kubeconfig: ${{ secrets.KUBE_CONFIG_STAGING }}
      
      # 用 Kustomize 生成 Staging 配置
      - name: 📋 Generate Staging manifests
        run: |
          cd k8s
          kustomize edit set image \
            app-image=${{ env.DOCKER_REGISTRY }}/${{ env.IMAGE_NAME }}:${{ github.sha }}
          kustomize build overlays/staging > staging-manifests.yml
          cat staging-manifests.yml
      
      # 部署到 Staging
      - name: 🚀 Deploy to Staging
        run: |
          kubectl apply -f k8s/staging-manifests.yml -n ${{ env.NAMESPACE }}
          
          # 等待部署完成
          kubectl rollout status deployment/my-app \
            -n ${{ env.NAMESPACE }} \
            --timeout=300s
      
      # 運行 Smoke Tests
      - name: 🧪 Smoke Tests
        run: |
          # 等待服務就緒
          sleep 30
          
          # 健康檢查
          HTTP_STATUS=$(curl -s -o /dev/null -w "%{http_code}" \
            https://staging.myapp.com/health)
          
          if [ "$HTTP_STATUS" != "200" ]; then
            echo "❌ Health check failed! Status: $HTTP_STATUS"
            exit 1
          fi
          
          echo "✅ Health check passed! Status: $HTTP_STATUS"
      
      # 運行 E2E Tests
      - name: 🧪 E2E Tests on Staging
        run: |
          pip install -r requirements-dev.txt
          pytest tests/e2e/ \
            --base-url=https://staging.myapp.com \
            -v --timeout=120
      
      # 部署失敗自動回滾
      - name: ⏪ Rollback on failure
        if: failure()
        run: |
          echo "❌ Deployment failed! Rolling back..."
          kubectl rollout undo deployment/my-app -n ${{ env.NAMESPACE }}
          kubectl rollout status deployment/my-app -n ${{ env.NAMESPACE }}
```

### CD to Production（需要手動批准）

yaml

```yaml
# .github/workflows/cd-production.yml
# ============================================
# 🔴 CD Pipeline - Deploy to Production
# 觸發條件：手動觸發 + 需要審批
# ============================================

name: 🚀 Deploy to Production

on:
  workflow_dispatch:
    inputs:
      image_tag:
        description: 'Docker image tag to deploy'
        required: true
        type: string
      deployment_strategy:
        description: 'Deployment strategy'
        required: true
        default: 'canary'
        type: choice
        options:
          - canary
          - blue-green
          - rolling

env:
  CLUSTER_NAME: my-cluster-production
  NAMESPACE: production
  DOCKER_REGISTRY: ghcr.io
  IMAGE_NAME: ${{ github.repository }}

jobs:
  # ==========================================
  # Step 1: 預檢查
  # ==========================================
  pre-check:
    name: ✅ Pre-deployment Checks
    runs-on: ubuntu-latest
    steps:
      - name: 🔍 Verify image exists
        run: |
          docker manifest inspect \
            ${{ env.DOCKER_REGISTRY }}/${{ env.IMAGE_NAME }}:${{ inputs.image_tag }}
      
      - name: 📊 Check Staging health
        run: |
          HTTP_STATUS=$(curl -s -o /dev/null -w "%{http_code}" \
            https://staging.myapp.com/health)
          
          if [ "$HTTP_STATUS" != "200" ]; then
            echo "⚠️ Staging is unhealthy! Aborting production deploy."
            exit 1
          fi

  # ==========================================
  # Step 2: 金絲雀部署（先部署 10%）
  # ==========================================
  canary-deploy:
    name: 🐤 Canary Deployment (10%)
    runs-on: ubuntu-latest
    needs: pre-check
    if: ${{ inputs.deployment_strategy == 'canary' }}
    
    environment:
      name: production-canary
      url: https://myapp.com
    
    steps:
      - name: 📥 Checkout code
        uses: actions/checkout@v4
      
      - name: ⚙️ Configure kubectl
        uses: azure/k8s-set-context@v3
        with:
          method: kubeconfig
          kubeconfig: ${{ secrets.KUBE_CONFIG_PRODUCTION }}
      
      # 部署金絲雀版本（10% 流量）
      - name: 🐤 Deploy Canary (10% traffic)
        run: |
          # 部署金絲雀 Deployment
          cat <<EOF | kubectl apply -f -
          apiVersion: apps/v1
          kind: Deployment
          metadata:
            name: my-app-canary
            namespace: ${{ env.NAMESPACE }}
            labels:
              app: my-app
              track: canary
          spec:
            replicas: 1
            selector:
              matchLabels:
                app: my-app
                track: canary
            template:
              metadata:
                labels:
                  app: my-app
                  track: canary
              spec:
                containers:
                - name: app
                  image: ${{ env.DOCKER_REGISTRY }}/${{ env.IMAGE_NAME }}:${{ inputs.image_tag }}
                  ports:
                  - containerPort: 8000
                  readinessProbe:
                    httpGet:
                      path: /health
                      port: 8000
                    initialDelaySeconds: 5
                    periodSeconds: 10
                  livenessProbe:
                    httpGet:
                      path: /health
                      port: 8000
                    initialDelaySeconds: 15
                    periodSeconds: 20
          EOF
          
          kubectl rollout status deployment/my-app-canary \
            -n ${{ env.NAMESPACE }} --timeout=300s
      
      # 監控金絲雀 15 分鐘
      - name: 📊 Monitor Canary (15 minutes)
        run: |
          echo "📊 Monitoring canary deployment for 15 minutes..."
          
          for i in $(seq 1 15); do
            echo "--- Minute $i/15 ---"
            
            # 檢查錯誤率
            ERROR_RATE=$(curl -s \
              "http://prometheus:9090/api/v1/query?query=rate(http_requests_total{status=~\"5..\",track=\"canary\"}[5m])" \
              | jq -r '.data.result[0].value[1] // "0"')
            
            # 檢查延遲
            LATENCY=$(curl -s \
              "http://prometheus:9090/api/v1/query?query=histogram_quantile(0.99,rate(http_request_duration_seconds_bucket{track=\"canary\"}[5m]))" \
              | jq -r '.data.result[0].value[1] // "0"')
            
            echo "Error Rate: $ERROR_RATE | P99 Latency: ${LATENCY}s"
            
            # 如果錯誤率 > 5% 或延遲 > 2s → 自動回滾
            if (( $(echo "$ERROR_RATE > 0.05" | bc -l) )); then
              echo "❌ Error rate too high! Rolling back canary..."
              kubectl delete deployment my-app-canary -n ${{ env.NAMESPACE }}
              exit 1
            fi
            
            sleep 60
          done
          
          echo "✅ Canary is healthy!"

  # ==========================================
  # Step 3: 全量部署
  # ==========================================
  full-deploy:
    name: 🚀 Full Production Deployment
    runs-on: ubuntu-latest
    needs: canary-deploy
    
    # ⚠️ 需要人工審批！
    environment:
      name: production
      url: https://myapp.com
    
    steps:
      - name: 📥 Checkout code
        uses: actions/checkout@v4
      
      - name: ⚙️ Configure kubectl
        uses: azure/k8s-set-context@v3
        with:
          method: kubeconfig
          kubeconfig: ${{ secrets.KUBE_CONFIG_PRODUCTION }}
      
      # 更新主 Deployment
      - name: 🚀 Update Production Deployment
        run: |
          kubectl set image deployment/my-app \
            app=${{ env.DOCKER_REGISTRY }}/${{ env.IMAGE_NAME }}:${{ inputs.image_tag }} \
            -n ${{ env.NAMESPACE }}
          
          kubectl rollout status deployment/my-app \
            -n ${{ env.NAMESPACE }} --timeout=600s
      
      # 刪除金絲雀
      - name: 🧹 Clean up Canary
        run: |
          kubectl delete deployment my-app-canary \
            -n ${{ env.NAMESPACE }} --ignore-not-found
      
      # 最終驗證
      - name: ✅ Final Verification
        run: |
          sleep 30
          
          HTTP_STATUS=$(curl -s -o /dev/null -w "%{http_code}" \
            https://myapp.com/health)
          
          if [ "$HTTP_STATUS" != "200" ]; then
            echo "❌ Production health check failed!"
            echo "⏪ Initiating rollback..."
            kubectl rollout undo deployment/my-app -n ${{ env.NAMESPACE }}
            exit 1
          fi
          
          echo "✅ Production deployment successful!"
          echo "🏷️ Image: ${{ inputs.image_tag }}"
      
      # 創建 Git Tag
      - name: 🏷️ Create Release Tag
        run: |
          git tag -a "deploy-$(date +%Y%m%d-%H%M%S)" \
            -m "Production deployment: ${{ inputs.image_tag }}"
          git push --tags
```

---

## 🔴 Phase 5: Kubernetes Manifests

### Base Deployment

yaml

```yaml
# k8s/base/deployment.yml
# ============================================
# 🔴 Kubernetes Deployment
# ============================================

apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
  labels:
    app: my-app
    version: v1
spec:
  replicas: 3
  revisionHistoryLimit: 5        # 保留 5 個版本方便回滾
  
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1                # 最多多出 1 個 Pod
      maxUnavailable: 0          # 不允許任何 Pod 不可用
  
  selector:
    matchLabels:
      app: my-app
  
  template:
    metadata:
      labels:
        app: my-app
        version: v1
      annotations:
        prometheus.io/scrape: "true"
        prometheus.io/port: "8000"
        prometheus.io/path: "/metrics"
    spec:
      # 安全設定
      securityContext:
        runAsNonRoot: true
        runAsUser: 1000
        fsGroup: 1000
      
      # 優雅終止
      terminationGracePeriodSeconds: 60
      
      # 服務帳號
      serviceAccountName: my-app-sa
      
      containers:
        - name: app
          image: app-image:latest      # 由 CI/CD 替換
          
          ports:
            - containerPort: 8000
              protocol: TCP
          
          # 環境變量（從 ConfigMap 和 Secret）
          envFrom:
            - configMapRef:
                name: my-app-config
            - secretRef:
                name: my-app-secrets
          
          # 資源限制
          resources:
            requests:
              cpu: "250m"
              memory: "256Mi"
            limits:
              cpu: "1000m"
              memory: "512Mi"
          
          # 就緒探針（什麼時候可以接收流量）
          readinessProbe:
            httpGet:
              path: /health/ready
              port: 8000
            initialDelaySeconds: 5
            periodSeconds: 10
            timeoutSeconds: 5
            failureThreshold: 3
          
          # 存活探針（什麼時候需要重啟）
          livenessProbe:
            httpGet:
              path: /health/live
              port: 8000
            initialDelaySeconds: 15
            periodSeconds: 20
            timeoutSeconds: 5
            failureThreshold: 3
          
          # 啟動探針（慢啟動保護）
          startupProbe:
            httpGet:
              path: /health/live
              port: 8000
            failureThreshold: 30
            periodSeconds: 10
          
          # 安全上下文
          securityContext:
            allowPrivilegeEscalation: false
            readOnlyRootFilesystem: true
            capabilities:
              drop:
                - ALL
          
          # Volume mounts
          volumeMounts:
            - name: tmp
              mountPath: /tmp
      
      volumes:
        - name: tmp
          emptyDir: {}
      
      # Pod 反親和性（分散到不同節點）
      affinity:
        podAntiAffinity:
          preferredDuringSchedulingIgnoredDuringExecution:
            - weight: 100
              podAffinityTerm:
                labelSelector:
                  matchExpressions:
                    - key: app
                      operator: In
                      values:
                        - my-app
                topologyKey: kubernetes.io/hostname

---
# ============================================
# Service
# ============================================
apiVersion: v1
kind: Service
metadata:
  name: my-app-service
  labels:
    app: my-app
spec:
  type: ClusterIP
  ports:
    - port: 80
      targetPort: 8000
      protocol: TCP
  selector:
    app: my-app

---
# ============================================
# HPA（Horizontal Pod Autoscaler）自動擴縮
# ============================================
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: my-app-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: my-app
  minReplicas: 3
  maxReplicas: 20
  metrics:
    # CPU 使用率 > 70% 時擴容
    - type: Resource
      resource:
        name: cpu
        target:
          type: Utilization
          averageUtilization: 70
    # Memory > 80% 時擴容
    - type: Resource
      resource:
        name: memory
        target:
          type: Utilization
          averageUtilization: 80
    # 自定義指標：每秒請求數 > 100 時擴容
    - type: Pods
      pods:
        metric:
          name: http_requests_per_second
        target:
          type: AverageValue
          averageValue: "100"
  behavior:
    scaleUp:
      stabilizationWindowSeconds: 60
      policies:
        - type: Pods
          value: 2
          periodSeconds: 60
    scaleDown:
      stabilizationWindowSeconds: 300
      policies:
        - type: Pods
          value: 1
          periodSeconds: 120

---
# ============================================
# Ingress（流量入口）
# ============================================
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: my-app-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    cert-manager.io/cluster-issuer: letsencrypt-prod
    nginx.ingress.kubernetes.io/rate-limit: "100"
    nginx.ingress.kubernetes.io/rate-limit-window: "1m"
    nginx.ingress.kubernetes.io/ssl-redirect: "true"
    nginx.ingress.kubernetes.io/force-ssl-redirect: "true"
spec:
  tls:
    - hosts:
        - myapp.com
      secretName: myapp-tls
  rules:
    - host: myapp.com
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: my-app-service
                port:
                  number: 80

---
# ============================================
# PDB（Pod Disruption Budget）高可用保護
# ============================================
apiVersion: policy/v1
kind: PodDisruptionBudget
metadata:
  name: my-app-pdb
spec:
  minAvailable: 2             # 至少保持 2 個 Pod 運行
  selector:
    matchLabels:
      app: my-app
```

---

## 🟣 Phase 6: Monitoring & Observability

### Application Code with Metrics

python

```python
# src/app.py
# ============================================
# 🟣 FastAPI Application with Full Observability
# ============================================

import time
import logging
import uuid
from contextlib import asynccontextmanager

from fastapi import FastAPI, Request, Response
from fastapi.middleware.cors import CORSMiddleware
from prometheus_client import (
    Counter, Histogram, Gauge, Info,
    generate_latest, CONTENT_TYPE_LATEST,
)
import structlog

# ============================================
# 📊 Prometheus Metrics（指標定義）
# ============================================

# 請求計數器
REQUEST_COUNT = Counter(
    "http_requests_total",
    "Total HTTP requests",
    ["method", "endpoint", "status_code"]
)

# 請求延遲直方圖
REQUEST_LATENCY = Histogram(
    "http_request_duration_seconds",
    "HTTP request latency in seconds",
    ["method", "endpoint"],
    buckets=[0.01, 0.025, 0.05, 0.1, 0.25, 0.5, 1.0, 2.5, 5.0, 10.0]
)

# 活躍請求數
ACTIVE_REQUESTS = Gauge(
    "http_active_requests",
    "Number of active HTTP requests"
)

# 應用信息
APP_INFO = Info(
    "app",
    "Application information"
)

# 業務指標
ORDERS_TOTAL = Counter(
    "business_orders_total",
    "Total orders processed",
    ["status"]  # success, failed, cancelled
)

ORDER_AMOUNT = Histogram(
    "business_order_amount_dollars",
    "Order amount in dollars",
    buckets=[10, 25, 50, 100, 250, 500, 1000]
)

# ============================================
# 📝 Structured Logging（結構化日誌）
# ============================================

structlog.configure(
    processors=[
        structlog.contextvars.merge_contextvars,
        structlog.processors.add_log_level,
        structlog.processors.TimeStamper(fmt="iso"),
        structlog.processors.StackInfoRenderer(),
        structlog.processors.format_exc_info,
        structlog.processors.JSONRenderer()  # JSON 格式（方便 ELK 處理）
    ],
    wrapper_class=structlog.make_filtering_bound_logger(logging.INFO),
    context_class=dict,
    logger_factory=structlog.PrintLoggerFactory(),
)

logger = structlog.get_logger()

# ============================================
# 🚀 FastAPI Application
# ============================================

@asynccontextmanager
async def lifespan(app: FastAPI):
    """應用生命周期管理"""
    # 啟動時
    APP_INFO.info({
        "version": "1.0.0",
        "environment": "production",
    })
    logger.info("application_started", version="1.0.0")
    
    yield
    
    # 關閉時
    logger.info("application_shutdown")

app = FastAPI(
    title="My Awesome App",
    version="1.0.0",
    lifespan=lifespan,
)

# CORS
app.add_middleware(
    CORSMiddleware,
    allow_origins=["*"],
    allow_methods=["*"],
    allow_headers=["*"],
)

# ============================================
# 🔧 Middleware（中間件）
# ============================================

@app.middleware("http")
async def metrics_middleware(request: Request, call_next):
    """收集每個請求的指標"""
    
    # 生成請求 ID（用於追蹤）
    request_id = str(uuid.uuid4())
    
    # 綁定到結構化日誌
    structlog.contextvars.clear_contextvars()
    structlog.contextvars.bind_contextvars(
        request_id=request_id,
        method=request.method,
        path=request.url.path,
        client_ip=request.client.host,
    )
    
    # 記錄活躍請求
    ACTIVE_REQUESTS.inc()
    
    # 計時
    start_time = time.time()
    
    # 記錄請求開始
    logger.info("request_started")
    
    try:
        # 處理請求
        response: Response = await call_next(request)
        
        # 計算延遲
        duration = time.time() - start_time
        
        # 記錄指標
        endpoint = request.url.path
        REQUEST_COUNT.labels(
            method=request.method,
            endpoint=endpoint,
            status_code=response.status_code
        ).inc()
        
        REQUEST_LATENCY.labels(
            method=request.method,
            endpoint=endpoint
        ).observe(duration)
        
        # 記錄請求完成
        logger.info(
            "request_completed",
            status_code=response.status_code,
            duration_ms=round(duration * 1000, 2),
        )
        
        # 添加追蹤 Header
        response.headers["X-Request-ID"] = request_id
        response.headers["X-Response-Time"] = f"{duration:.4f}s"
        
        return response
    
    except Exception as e:
        duration = time.time() - start_time
        
        REQUEST_COUNT.labels(
            method=request.method,
            endpoint=request.url.path,
            status_code=500
        ).inc()
        
        logger.error(
            "request_failed",
            error=str(e),
            duration_ms=round(duration * 1000, 2),
            exc_info=True,
        )
        raise
    
    finally:
        ACTIVE_REQUESTS.dec()

# ============================================
# 📡 Health Check Endpoints
# ============================================

@app.get("/health")
async def health():
    """基本健康檢查"""
    return {"status": "healthy", "version": "1.0.0"}

@app.get("/health/ready")
async def readiness():
    """就緒檢查（檢查依賴）"""
    checks = {}
    
    # 檢查數據庫
    try:
        # await db.execute("SELECT 1")
        checks["database"] = "healthy"
    except Exception as e:
        checks["database"] = f"unhealthy: {e}"
        return Response(
            content=str({"status": "not_ready", "checks": checks}),
            status_code=503
        )
    
    # 檢查 Redis
    try:
        # await redis.ping()
        checks["redis"] = "healthy"
    except Exception as e:
        checks["redis"] = f"unhealthy: {e}"
        return Response(
            content=str({"status": "not_ready", "checks": checks}),
            status_code=503
        )
    
    return {"status": "ready", "checks": checks}

@app.get("/health/live")
async def liveness():
    """存活檢查"""
    return {"status": "alive"}

# ============================================
# 📊 Metrics Endpoint（Prometheus 抓取）
# ============================================

@app.get("/metrics")
async def metrics():
    """Prometheus metrics endpoint"""
    return Response(
        content=generate_latest(),
        media_type=CONTENT_TYPE_LATEST
    )

# ============================================
# 🏢 Business Endpoints（業務接口）
# ============================================

@app.post("/api/v1/orders")
async def create_order(request: Request):
    """創建訂單"""
    body = await request.json()
    
    try:
        order_id = str(uuid.uuid4())
        amount = body.get("amount", 0)
        
        # 業務邏輯...
        logger.info(
            "order_created",
            order_id=order_id,
            amount=amount,
            customer_id=body.get("customer_id"),
        )
        
        # 記錄業務指標
        ORDERS_TOTAL.labels(status="success").inc()
        ORDER_AMOUNT.observe(amount)
        
        return {
            "order_id": order_id,
            "status": "created",
            "amount": amount,
        }
    
    except Exception as e:
        ORDERS_TOTAL.labels(status="failed").inc()
        logger.error("order_creation_failed", error=str(e))
        raise
```

### Prometheus Configuration

yaml

```yaml
# monitoring/prometheus/prometheus.yml
# ============================================
# 📊 Prometheus Configuration
# ============================================

global:
  scrape_interval: 15s          # 每 15 秒抓取一次
  evaluation_interval: 15s      # 每 15 秒評估一次規則
  scrape_timeout: 10s

# 告警規則文件
rule_files:
  - "alert_rules.yml"

# Alertmanager 配置
alerting:
  alertmanagers:
    - static_configs:
        - targets: ["alertmanager:9093"]

# 抓取配置
scrape_configs:
  # Prometheus 自身
  - job_name: "prometheus"
    static_configs:
      - targets: ["localhost:9090"]

  # 應用服務
  - job_name: "my-app"
    kubernetes_sd_configs:
      - role: pod
    relabel_configs:
      - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_scrape]
        action: keep
        regex: true
      - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_path]
        action: replace
        target_label: __metrics_path__
        regex: (.+)
      - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_port]
        action: replace
        regex: (.+)
        target_label: __address__
        replacement: \$1

  # Node Exporter（節點指標）
  - job_name: "node-exporter"
    static_configs:
      - targets: ["node-exporter:9100"]

  # cAdvisor（容器指標）
  - job_name: "cadvisor"
    static_configs:
      - targets: ["cadvisor:8080"]
```

### Alert Rules

yaml

```yaml
# monitoring/prometheus/alert_rules.yml
# ============================================
# 🚨 Alert Rules
# ============================================

groups:
  # ==========================================
  # 應用告警
  # ==========================================
  - name: application_alerts
    rules:
      # 高錯誤率告警
      - alert: HighErrorRate
        expr: |
          sum(rate(http_requests_total{status_code=~"5.."}[5m]))
          /
          sum(rate(http_requests_total[5m]))
          > 0.05
        for: 5m
        labels:
          severity: critical
        annotations:
          summary: "🚨 High error rate detected"
          description: |
            Error rate is {{ $value | humanizePercentage }} 
            (threshold: 5%)

      # 高延遲告警
      - alert: HighLatency
        expr: |
          histogram_quantile(0.99, 
            sum(rate(http_request_duration_seconds_bucket[5m])) by (le)
          ) > 2
        for: 5m
        labels:
          severity: warning
        annotations:
          summary: "⚠️ High P99 latency detected"
          description: "P99 latency is {{ $value }}s (threshold: 2s)"

      # 應用掛了
      - alert: AppDown
        expr: up{job="my-app"} == 0
        for: 1m
        labels:
          severity: critical
        annotations:
          summary: "🔴 Application is DOWN"
          description: "{{ $labels.instance }} is not responding"

      # Pod 頻繁重啟
      - alert: PodCrashLooping
        expr: |
          rate(kube_pod_container_status_restarts_total{
            namespace="production"
          }[15m]) > 0
        for: 5m
        labels:
          severity: critical
        annotations:
          summary: "🔄 Pod is crash looping"
          description: "Pod {{ $labels.pod }} is restarting frequently"

  # ==========================================
  # 資源告警
  # ==========================================
  - name: resource_alerts
    rules:
      # CPU 使用率過高
      - alert: HighCPUUsage
        expr: |
          (sum(rate(container_cpu_usage_seconds_total{
            namespace="production"
          }[5m])) by (pod)
          /
          sum(kube_pod_container_resource_limits{
            namespace="production", resource="cpu"
          }) by (pod)) > 0.85
        for: 10m
        labels:
          severity: warning
        annotations:
          summary: "⚠️ High CPU usage"
          description: "Pod {{ $labels.pod }} CPU: {{ $value | humanizePercentage }}"

      # 內存使用率過高
      - alert: HighMemoryUsage
        expr: |
          (container_memory_working_set_bytes{namespace="production"}
          /
          container_spec_memory_limit_bytes{namespace="production"}) > 0.85
        for: 10m
        labels:
          severity: warning
        annotations:
          summary: "⚠️ High memory usage"
          description: "Pod {{ $labels.pod }} Memory: {{ $value | humanizePercentage }}"

      # 磁盤空間不足
      - alert: DiskSpaceRunningOut
        expr: |
          (node_filesystem_avail_bytes{mountpoint="/"} 
          / node_filesystem_size_bytes{mountpoint="/"}) < 0.15
        for: 5m
        labels:
          severity: critical
        annotations:
          summary: "🔴 Disk space running out"
          description: "Only {{ $value | humanizePercentage }} disk space remaining"

  # ==========================================
  # 業務告警
  # ==========================================
  - name: business_alerts
    rules:
      # 訂單失敗率過高
      - alert: HighOrderFailureRate
        expr: |
          sum(rate(business_orders_total{status="failed"}[10m]))
          /
          sum(rate(business_orders_total[10m]))
          > 0.1
        for: 5m
        labels:
          severity: critical
        annotations:
          summary: "🚨 High order failure rate"
          description: "{{ $value | humanizePercentage }} orders are failing"

      # 訂單量異常下降
      - alert: OrderVolumeDropped
        expr: |
          sum(rate(business_orders_total[1h]))
          < 
          sum(rate(business_orders_total[1h] offset 1d)) * 0.5
        for: 30m
        labels:
          severity: warning
        annotations:
          summary: "⚠️ Order volume dropped significantly"
          description: "Current order rate is less than 50% of yesterday"
```

### Alertmanager Configuration

yaml

```yaml
# monitoring/alertmanager/alertmanager.yml
# ============================================
# 📢 Alertmanager Configuration
# ============================================

global:
  resolve_timeout: 5m

route:
  group_by: ['alertname', 'severity']
  group_wait: 10s
  group_interval: 5m
  repeat_interval: 3h
  receiver: 'default'
  
  routes:
    # Critical → PagerDuty + Slack
    - match:
        severity: critical
      receiver: 'critical-alerts'
      continue: true
    
    # Warning → Slack only
    - match:
        severity: warning
      receiver: 'warning-alerts'

receivers:
  - name: 'default'
    slack_configs:
      -
```


## 🟣 Phase 6: Monitoring & Observability

### Alertmanager Configuration

yaml

```yaml
# monitoring/alertmanager/alertmanager.yml
# ============================================
# 📢 Alertmanager Configuration
# ============================================

global:
  resolve_timeout: 5m
  slack_api_url: 'https://hooks.slack.com/services/YOUR/SLACK/WEBHOOK'

route:
  group_by: ['alertname', 'severity']
  group_wait: 10s
  group_interval: 5m
  repeat_interval: 3h
  receiver: 'default'
  
  routes:
    # 🔴 Critical → PagerDuty + Slack + Email
    - match:
        severity: critical
      receiver: 'critical-alerts'
      repeat_interval: 15m
      continue: true
    
    # 🟡 Warning → Slack only
    - match:
        severity: warning
      receiver: 'warning-alerts'
      repeat_interval: 1h
    
    # 🔵 Info → Slack (low priority channel)
    - match:
        severity: info
      receiver: 'info-alerts'
      repeat_interval: 4h

receivers:
  # ==========================================
  # Default Receiver
  # ==========================================
  - name: 'default'
    slack_configs:
      - channel: '#alerts-default'
        send_resolved: true
        title: '{{ .GroupLabels.alertname }}'
        text: >-
          {{ range .Alerts }}
          *Alert:* {{ .Annotations.summary }}
          *Description:* {{ .Annotations.description }}
          *Severity:* {{ .Labels.severity }}
          *Started:* {{ .StartsAt }}
          {{ end }}

  # ==========================================
  # 🔴 Critical Alerts → PagerDuty + Slack
  # ==========================================
  - name: 'critical-alerts'
    pagerduty_configs:
      - service_key: '<PAGERDUTY_SERVICE_KEY>'
        severity: 'critical'
        description: '{{ .GroupLabels.alertname }}: {{ .CommonAnnotations.summary }}'
        details:
          firing: '{{ template "pagerduty.default.description" . }}'
    
    slack_configs:
      - channel: '#alerts-critical'
        send_resolved: true
        color: '{{ if eq .Status "firing" }}danger{{ else }}good{{ end }}'
        title: '🚨 CRITICAL: {{ .GroupLabels.alertname }}'
        text: >-
          {{ range .Alerts }}
          *Summary:* {{ .Annotations.summary }}
          *Description:* {{ .Annotations.description }}
          *Instance:* {{ .Labels.instance }}
          *Started:* {{ .StartsAt }}
          {{ end }}
        actions:
          - type: button
            text: '📊 View in Grafana'
            url: 'https://grafana.myapp.com/d/app-dashboard'
          - type: button
            text: '📋 Runbook'
            url: 'https://wiki.myapp.com/runbooks/{{ .GroupLabels.alertname }}'
    
    email_configs:
      - to: 'oncall-team@company.com'
        from: 'alerts@company.com'
        smarthost: 'smtp.company.com:587'
        auth_username: 'alerts@company.com'
        auth_password: '<EMAIL_PASSWORD>'
        send_resolved: true
        html: |
          <h2>🚨 Critical Alert: {{ .GroupLabels.alertname }}</h2>
          {{ range .Alerts }}
          <p><b>Summary:</b> {{ .Annotations.summary }}</p>
          <p><b>Description:</b> {{ .Annotations.description }}</p>
          {{ end }}

  # ==========================================
  # 🟡 Warning Alerts → Slack
  # ==========================================
  - name: 'warning-alerts'
    slack_configs:
      - channel: '#alerts-warning'
        send_resolved: true
        color: '{{ if eq .Status "firing" }}warning{{ else }}good{{ end }}'
        title: '⚠️ WARNING: {{ .GroupLabels.alertname }}'
        text: >-
          {{ range .Alerts }}
          *Summary:* {{ .Annotations.summary }}
          *Description:* {{ .Annotations.description }}
          {{ end }}

  # ==========================================
  # 🔵 Info Alerts → Slack (low priority)
  # ==========================================
  - name: 'info-alerts'
    slack_configs:
      - channel: '#alerts-info'
        send_resolved: true
        title: 'ℹ️ INFO: {{ .GroupLabels.alertname }}'
        text: '{{ .CommonAnnotations.description }}'

# ==========================================
# 抑制規則（避免重複告警）
# ==========================================
inhibit_rules:
  # Critical 告警觸發時，抑制同一告警的 Warning
  - source_match:
      severity: 'critical'
    target_match:
      severity: 'warning'
    equal: ['alertname']
  
  # 應用 Down 時，抑制所有該應用的其他告警
  - source_match:
      alertname: 'AppDown'
    target_match_re:
      alertname: 'High.*'
    equal: ['instance']
```

---

### Grafana Dashboard（JSON 配置）

json

```json
// monitoring/grafana/dashboards/app-overview.json
// ============================================
// 📊 Grafana Dashboard - Application Overview
// ============================================

{
  "dashboard": {
    "title": "🚀 Application Overview",
    "tags": ["application", "production"],
    "timezone": "browser",
    "refresh": "10s",
    
    "panels": [
      {
        "title": "📊 Request Rate (req/sec)",
        "type": "timeseries",
        "gridPos": { "h": 8, "w": 12, "x": 0, "y": 0 },
        "targets": [
          {
            "expr": "sum(rate(http_requests_total[5m])) by (status_code)",
            "legendFormat": "Status {{status_code}}"
          }
        ]
      },
      {
        "title": "⏱️ Response Time (P50, P95, P99)",
        "type": "timeseries",
        "gridPos": { "h": 8, "w": 12, "x": 12, "y": 0 },
        "targets": [
          {
            "expr": "histogram_quantile(0.50, sum(rate(http_request_duration_seconds_bucket[5m])) by (le))",
            "legendFormat": "P50"
          },
          {
            "expr": "histogram_quantile(0.95, sum(rate(http_request_duration_seconds_bucket[5m])) by (le))",
            "legendFormat": "P95"
          },
          {
            "expr": "histogram_quantile(0.99, sum(rate(http_request_duration_seconds_bucket[5m])) by (le))",
            "legendFormat": "P99"
          }
        ]
      },
      {
        "title": "❌ Error Rate (%)",
        "type": "stat",
        "gridPos": { "h": 4, "w": 6, "x": 0, "y": 8 },
        "targets": [
          {
            "expr": "sum(rate(http_requests_total{status_code=~'5..'}[5m])) / sum(rate(http_requests_total[5m])) * 100"
          }
        ],
        "fieldConfig": {
          "defaults": {
            "thresholds": {
              "steps": [
                { "color": "green", "value": 0 },
                { "color": "yellow", "value": 1 },
                { "color": "red", "value": 5 }
              ]
            },
            "unit": "percent"
          }
        }
      },
      {
        "title": "🏃 Active Requests",
        "type": "gauge",
        "gridPos": { "h": 4, "w": 6, "x": 6, "y": 8 },
        "targets": [
          {
            "expr": "sum(http_active_requests)"
          }
        ]
      },
      {
        "title": "📦 Pod Count",
        "type": "stat",
        "gridPos": { "h": 4, "w": 6, "x": 12, "y": 8 },
        "targets": [
          {
            "expr": "count(kube_pod_info{namespace='production', pod=~'my-app.*'})"
          }
        ]
      },
      {
        "title": "💻 CPU Usage per Pod",
        "type": "timeseries",
        "gridPos": { "h": 8, "w": 12, "x": 0, "y": 12 },
        "targets": [
          {
            "expr": "sum(rate(container_cpu_usage_seconds_total{namespace='production', pod=~'my-app.*'}[5m])) by (pod)",
            "legendFormat": "{{pod}}"
          }
        ]
      },
      {
        "title": "🧠 Memory Usage per Pod",
        "type": "timeseries",
        "gridPos": { "h": 8, "w": 12, "x": 12, "y": 12 },
        "targets": [
          {
            "expr": "container_memory_working_set_bytes{namespace='production', pod=~'my-app.*'} / 1024 / 1024",
            "legendFormat": "{{pod}}"
          }
        ],
        "fieldConfig": {
          "defaults": { "unit": "decmbytes" }
        }
      },
      {
        "title": "🛒 Order Rate",
        "type": "timeseries",
        "gridPos": { "h": 8, "w": 12, "x": 0, "y": 20 },
        "targets": [
          {
            "expr": "sum(rate(business_orders_total[5m])) by (status)",
            "legendFormat": "{{status}}"
          }
        ]
      },
      {
        "title": "💰 Order Amount Distribution",
        "type": "heatmap",
        "gridPos": { "h": 8, "w": 12, "x": 12, "y": 20 },
        "targets": [
          {
            "expr": "sum(rate(business_order_amount_dollars_bucket[5m])) by (le)"
          }
        ]
      }
    ]
  }
}
```

---

### Centralized Logging（ELK / Loki）

yaml

```yaml
# monitoring/loki/loki-config.yml
# ============================================
# 📝 Loki Configuration (Lightweight Log Aggregation)
# ============================================

auth_enabled: false

server:
  http_listen_port: 3100

common:
  path_prefix: /loki
  storage:
    filesystem:
      chunks_directory: /loki/chunks
      rules_directory: /loki/rules
  replication_factor: 1
  ring:
    kvstore:
      store: inmemory

schema_config:
  configs:
    - from: 2024-01-01
      store: tsdb
      object_store: filesystem
      schema: v12
      index:
        prefix: index_
        period: 24h

limits_config:
  retention_period: 30d          # 保留 30 天的日誌

---
# monitoring/promtail/promtail-config.yml
# ============================================
# 📝 Promtail Configuration (Log Collector)
# ============================================

server:
  http_listen_port: 9080

positions:
  filename: /tmp/positions.yaml

clients:
  - url: http://loki:3100/loki/api/v1/push

scrape_configs:
  # 收集 Kubernetes Pod 日誌
  - job_name: kubernetes-pods
    kubernetes_sd_configs:
      - role: pod
    relabel_configs:
      - source_labels: [__meta_kubernetes_pod_label_app]
        target_label: app
      - source_labels: [__meta_kubernetes_namespace]
        target_label: namespace
      - source_labels: [__meta_kubernetes_pod_name]
        target_label: pod
    pipeline_stages:
      # 解析 JSON 日誌
      - json:
          expressions:
            level: level
            message: event
            request_id: request_id
            timestamp: timestamp
            duration_ms: duration_ms
            status_code: status_code
      
      # 設定日誌級別
      - labels:
          level:
          status_code:
      
      # 設定時間戳
      - timestamp:
          source: timestamp
          format: RFC3339
```

### Distributed Tracing（分佈式追蹤）

python

```python
# src/tracing.py
# ============================================
# 🔗 OpenTelemetry Distributed Tracing
# ============================================

from opentelemetry import trace
from opentelemetry.exporter.otlp.proto.grpc.trace_exporter import OTLPSpanExporter
from opentelemetry.instrumentation.fastapi import FastAPIInstrumentor
from opentelemetry.instrumentation.httpx import HTTPXClientInstrumentor
from opentelemetry.instrumentation.sqlalchemy import SQLAlchemyInstrumentor
from opentelemetry.instrumentation.redis import RedisInstrumentor
from opentelemetry.sdk.trace import TracerProvider
from opentelemetry.sdk.trace.export import BatchSpanProcessor
from opentelemetry.sdk.resources import Resource

def setup_tracing(app, service_name: str = "my-app"):
    """設定 OpenTelemetry 分佈式追蹤"""
    
    # 定義服務資源
    resource = Resource.create({
        "service.name": service_name,
        "service.version": "1.0.0",
        "deployment.environment": "production",
    })
    
    # 創建 Tracer Provider
    provider = TracerProvider(resource=resource)
    
    # 配置導出器（發送到 Jaeger/Tempo）
    exporter = OTLPSpanExporter(
        endpoint="http://tempo:4317",
        insecure=True,
    )
    provider.add_span_processor(BatchSpanProcessor(exporter))
    
    # 設定全局 Tracer
    trace.set_tracer_provider(provider)
    
    # 自動注入追蹤到各框架
    FastAPIInstrumentor.instrument_app(app)       # FastAPI
    HTTPXClientInstrumentor().instrument()          # HTTP 客戶端
    SQLAlchemyInstrumentor().instrument()           # SQLAlchemy
    RedisInstrumentor().instrument()                # Redis
    
    return trace.get_tracer(service_name)


# ============================================
# 使用追蹤的業務代碼範例
# ============================================

from opentelemetry import trace

tracer = trace.get_tracer("my-app")

async def process_order(order_data: dict):
    """處理訂單（帶追蹤）"""
    
    # 創建一個 Span（追蹤區間）
    with tracer.start_as_current_span("process_order") as span:
        
        # 添加屬性
        span.set_attribute("order.id", order_data["id"])
        span.set_attribute("order.amount", order_data["amount"])
        span.set_attribute("order.customer_id", order_data["customer_id"])
        
        # 子 Span：驗證訂單
        with tracer.start_as_current_span("validate_order"):
            validate_result = await validate_order(order_data)
            span.set_attribute("validation.result", str(validate_result))
        
        # 子 Span：扣減庫存
        with tracer.start_as_current_span("deduct_inventory"):
            await deduct_inventory(order_data["items"])
        
        # 子 Span：處理付款
        with tracer.start_as_current_span("process_payment") as payment_span:
            payment_span.set_attribute("payment.method", order_data["payment_method"])
            payment_result = await process_payment(order_data)
            payment_span.set_attribute("payment.status", payment_result["status"])
        
        # 子 Span：發送通知
        with tracer.start_as_current_span("send_notification"):
            await send_order_confirmation(order_data)
        
        span.set_attribute("order.status", "completed")
        
        return {"status": "success", "order_id": order_data["id"]}

# 追蹤的結果（在 Jaeger/Tempo 中看到）：
# 
# process_order (120ms)
# ├── validate_order (15ms)
# ├── deduct_inventory (25ms)
# ├── process_payment (60ms)
# └── send_notification (20ms)
#
# → 一目了然每個步驟花了多長時間！
# → 跨服務也能追蹤整個請求鏈路！
```

---

## ⚫ Phase 7: Security（DevSecOps）

### Secret Management

python

```python
# src/config.py
# ============================================
# 🔐 Secure Configuration Management
# ============================================

import os
from functools import lru_cache
from pydantic_settings import BaseSettings
from pydantic import Field

class Settings(BaseSettings):
    """
    安全的配置管理
    - 從環境變量讀取
    - 敏感信息從 Secret Manager 讀取
    - 永遠不硬編碼密碼！
    """
    
    # ==========================================
    # 基本配置（非敏感）
    # ==========================================
    APP_NAME: str = "my-app"
    APP_VERSION: str = "1.0.0"
    ENV: str = Field(default="production")
    LOG_LEVEL: str = Field(default="INFO")
    PORT: int = Field(default=8000)
    
    # ==========================================
    # 數據庫（敏感！）
    # ==========================================
    DATABASE_URL: str = Field(
        ...,  # 必填
        description="PostgreSQL connection string"
    )
    DATABASE_POOL_SIZE: int = Field(default=20)
    DATABASE_MAX_OVERFLOW: int = Field(default=10)
    
    # ==========================================
    # Redis（敏感！）
    # ==========================================
    REDIS_URL: str = Field(
        ...,
        description="Redis connection string"
    )
    
    # ==========================================
    # API Keys（非常敏感！）
    # ==========================================
    OPENAI_API_KEY: str = Field(
        default="",
        description="OpenAI API Key"
    )
    
    JWT_SECRET_KEY: str = Field(
        ...,
        description="JWT signing key"
    )
    
    # ==========================================
    # CORS
    # ==========================================
    ALLOWED_ORIGINS: list[str] = Field(
        default=["https://myapp.com"]
    )
    
    class Config:
        env_file = ".env"
        env_file_encoding = "utf-8"
        case_sensitive = True
        
        # ❌ 永遠不要在日誌中打印這些字段
        json_schema_extra = {
            "sensitive_fields": [
                "DATABASE_URL",
                "REDIS_URL", 
                "OPENAI_API_KEY",
                "JWT_SECRET_KEY"
            ]
        }

@lru_cache()
def get_settings() -> Settings:
    """獲取配置（帶緩存）"""
    return Settings()

# ============================================
# 🔐 HashiCorp Vault Integration
# ============================================

import hvac

class VaultClient:
    """從 HashiCorp Vault 讀取密鑰"""
    
    def __init__(self):
        self.client = hvac.Client(
            url=os.getenv("VAULT_ADDR", "https://vault.company.com"),
            token=os.getenv("VAULT_TOKEN"),
        )
    
    def get_secret(self, path: str, key: str) -> str:
        """從 Vault 讀取密鑰"""
        try:
            response = self.client.secrets.kv.v2.read_secret_version(
                path=path,
                mount_point="secret",
            )
            return response["data"]["data"][key]
        except Exception as e:
            raise ValueError(f"Failed to read secret {path}/{key}: {e}")
    
    def get_database_url(self) -> str:
        """獲取數據庫連接字符串"""
        return self.get_secret("myapp/database", "url")
    
    def get_api_key(self, service: str) -> str:
        """獲取 API 密鑰"""
        return self.get_secret(f"myapp/api-keys", service)


# ============================================
# 🔐 AWS Secrets Manager Integration
# ============================================

import boto3
import json

class AWSSecretManager:
    """從 AWS Secrets Manager 讀取密鑰"""
    
    def __init__(self, region: str = "ap-east-1"):
        self.client = boto3.client(
            "secretsmanager",
            region_name=region,
        )
    
    def get_secret(self, secret_name: str) -> dict:
        """獲取密鑰"""
        try:
            response = self.client.get_secret_value(
                SecretId=secret_name
            )
            return json.loads(response["SecretString"])
        except Exception as e:
            raise ValueError(f"Failed to get secret {secret_name}: {e}")
```

### Kubernetes Secrets

yaml

```yaml
# k8s/base/secrets.yml
# ============================================
# 🔐 Kubernetes Secrets（加密敏感信息）
# ============================================

# ⚠️ 在實際生產中，不要把 Secret 放在 Git 裡！
# 使用以下方式之一：
# 1. External Secrets Operator（從 Vault/AWS 同步）
# 2. Sealed Secrets（加密後可以放 Git）
# 3. SOPS（Mozilla 的加密工具）

# 方法 1：External Secrets Operator
apiVersion: external-secrets.io/v1beta1
kind: ExternalSecret
metadata:
  name: my-app-secrets
  namespace: production
spec:
  refreshInterval: 1h
  secretStoreRef:
    kind: ClusterSecretStore
    name: aws-secrets-manager
  target:
    name: my-app-secrets
    creationPolicy: Owner
  data:
    - secretKey: DATABASE_URL
      remoteRef:
        key: myapp/production/database
        property: url
    
    - secretKey: REDIS_URL
      remoteRef:
        key: myapp/production/redis
        property: url
    
    - secretKey: JWT_SECRET_KEY
      remoteRef:
        key: myapp/production/jwt
        property: secret_key
    
    - secretKey: OPENAI_API_KEY
      remoteRef:
        key: myapp/production/openai
        property: api_key

---
# 方法 2：Sealed Secrets（加密的 Secret，可以放 Git）
apiVersion: bitnami.com/v1alpha1
kind: SealedSecret
metadata:
  name: my-app-secrets
  namespace: production
spec:
  encryptedData:
    DATABASE_URL: AgBy3i4OJSWK+Pi...encrypted...
    REDIS_URL: AgBy3i4OJSWK+Pi...encrypted...
    JWT_SECRET_KEY: AgBy3i4OJSWK+Pi...encrypted...

---
# Network Policy（網絡隔離）
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: my-app-network-policy
  namespace: production
spec:
  podSelector:
    matchLabels:
      app: my-app
  policyTypes:
    - Ingress
    - Egress
  ingress:
    # 只允許 Ingress Controller 訪問
    - from:
        - namespaceSelector:
            matchLabels:
              name: ingress-nginx
      ports:
        - protocol: TCP
          port: 8000
    # 允許 Prometheus 抓取指標
    - from:
        - namespaceSelector:
            matchLabels:
              name: monitoring
      ports:
        - protocol: TCP
          port: 8000
  egress:
    # 允許訪問數據庫
    - to:
        - namespaceSelector:
            matchLabels:
              name: database
      ports:
        - protocol: TCP
          port: 5432
    # 允許訪問 Redis
    - to:
        - namespaceSelector:
            matchLabels:
              name: cache
      ports:
        - protocol: TCP
          port: 6379
    # 允許 DNS
    - to:
        - namespaceSelector: {}
      ports:
        - protocol: UDP
          port: 53
        - protocol: TCP
          port: 53
```

---

## 📖 Phase 8: Infrastructure as Code（Terraform）

hcl

```hcl
# terraform/main.tf
# ============================================
# 🏗️ Infrastructure as Code - AWS EKS
# ============================================

terraform {
  required_version = ">= 1.5.0"
  
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
    kubernetes = {
      source  = "hashicorp/kubernetes"
      version = "~> 2.23"
    }
  }
  
  # 遠程狀態存儲（團隊協作必須）
  backend "s3" {
    bucket         = "myapp-terraform-state"
    key            = "production/terraform.tfstate"
    region         = "ap-east-1"
    encrypt        = true
    dynamodb_table = "terraform-locks"
  }
}

provider "aws" {
  region = var.aws_region
  
  default_tags {
    tags = {
      Project     = "my-app"
      Environment = var.environment
      ManagedBy   = "terraform"
      Team        = "platform"
    }
  }
}

# ============================================
# VPC
# ============================================
module "vpc" {
  source  = "terraform-aws-modules/vpc/aws"
  version = "~> 5.0"
  
  name = "${var.project_name}-vpc"
  cidr = "10.0.0.0/16"
  
  azs             = ["${var.aws_region}a", "${var.aws_region}b", "${var.aws_region}c"]
  private_subnets = ["10.0.1.0/24", "10.0.2.0/24", "10.0.3.0/24"]
  public_subnets  = ["10.0.101.0/24", "10.0.102.0/24", "10.0.103.0/24"]
  
  enable_nat_gateway   = true
  single_nat_gateway   = false  # 每個 AZ 一個 NAT Gateway（高可用）
  enable_dns_hostnames = true
  
  # EKS 需要的標籤
  private_subnet_tags = {
    "kubernetes.io/role/internal-elb" = 1
  }
  public_subnet_tags = {
    "kubernetes.io/role/elb" = 1
  }
}

# ============================================
# EKS Cluster
# ============================================
module "eks" {
  source  = "terraform-aws-modules/eks/aws"
  version = "~> 19.0"
  
  cluster_name    = "${var.project_name}-${var.environment}"
  cluster_version = "1.28"
  
  vpc_id     = module.vpc.vpc_id
  subnet_ids = module.vpc.private_subnets
  
  # 集群端點訪問
  cluster_endpoint_public_access  = true
  cluster_endpoint_private_access = true
  
  # 節點組
  eks_managed_node_groups = {
    # 通用節點
    general = {
      name           = "general"
      instance_types = ["t3.large"]
      
      min_size     = 2
      max_size     = 10
      desired_size = 3
      
      labels = {
        role = "general"
      }
    }
    
    # 監控節點
    monitoring = {
      name           = "monitoring"
      instance_types = ["t3.medium"]
      
      min_size     = 1
      max_size     = 3
      desired_size = 2
      
      labels = {
        role = "monitoring"
      }
      
      taints = [{
        key    = "dedicated"
        value  = "monitoring"
        effect = "NO_SCHEDULE"
      }]
    }
  }
  
  # 集群插件
  cluster_addons = {
    coredns = {
      most_recent = true
    }
    kube-proxy = {
      most_recent = true
    }
    vpc-cni = {
      most_recent = true
    }
  }
}

# ============================================
# RDS（PostgreSQL）
# ============================================
module "rds" {
  source  = "terraform-aws-modules/rds/aws"
  version = "~> 6.0"
  
  identifier = "${var.project_name}-db"
  
  engine               = "postgres"
  engine_version       = "16.1"
  family               = "postgres16"
  major_engine_version = "16"
  instance_class       = "db.t3.medium"
  
  allocated_storage     = 50
  max_allocated_storage = 200
  
  db_name  = "appdb"
  username = "dbadmin"
  port     = 5432
  
  # 高可用
  multi_az = true
  
  # 網絡
  db_subnet_group_name   = module.vpc.database_subnet_group_name
  vpc_security_group_ids = [aws_security_group.rds.id]
  
  # 備份
  backup_retention_period = 7
  backup_window          = "03:00-04:00"
  maintenance_window     = "Mon:04:00-Mon:05:00"
  
  # 加密
  storage_encrypted = true
  
  # 刪除保護
  deletion_protection = true
  
  # 性能洞察
  performance_insights_enabled = true
}

# ============================================
# ElastiCache（Redis）
# ============================================
module "elasticache" {
  source  = "terraform-aws-modules/elasticache/aws"
  version = "~> 1.0"
  
  cluster_id         = "${var.project_name}-redis"
  engine             = "redis"
  engine_version     = "7.0"
  node_type          = "cache.t3.medium"
  num_cache_clusters = 2  # 主從複製
  
  subnet_group_name  = module.vpc.elasticache_subnet_group_name
  security_group_ids = [aws_security_group.redis.id]
  
  at_rest_encryption_enabled = true
  transit_encryption_enabled = true
}

# ============================================
# Security Groups
# ============================================
resource "aws_security_group" "rds" {
  name_prefix = "${var.project_name}-rds-"
  vpc_id      = module.vpc.vpc_id
  
  ingress {
    from_port       = 5432
    to_port         = 5432
    protocol        = "tcp"
    security_groups = [module.eks.node_security_group_id]
    description     = "Allow PostgreSQL from EKS nodes"
  }
}

resource "aws_security_group" "redis" {
  name_prefix = "${var.project_name}-redis-"
  vpc_id      = module.vpc.vpc_id
  
  ingress {
    from_port       = 6379
    to_port         = 6379
    protocol        = "tcp"
    security_groups = [module.eks.node_security_group_id]
    description     = "Allow Redis from EKS nodes"
  }
}

# ============================================
# Outputs
# ============================================
output "eks_cluster_endpoint" {
  value = module.eks.cluster_endpoint
}

output "eks_cluster_name" {
  value = module.eks.cluster_name
}

output "rds_endpoint" {
  value     = module.rds.db_instance_endpoint
  sensitive = true
}

output "redis_endpoint" {
  value     = module.elasticache.cluster_address
  sensitive = true
}
```

hcl

```hcl
# terraform/variables.tf
# ============================================

variable "project_name" {
  type    = string
  default = "my-app"
}

variable "environment" {
  type    = string
  default = "production"
}

variable "aws_region" {
  type    = string
  default = "ap-east-1"
}
```

---

## 📖 Phase 9: Incident Management Scripts

### Automated Rollback Script

bash

```bash
#!/bin/bash
# scripts/rollback.sh
# ============================================
# ⏪ Automated Rollback Script
# ============================================

set -euo pipefail

NAMESPACE="${1:-production}"
DEPLOYMENT="${2:-my-app}"
TIMEOUT="${3:-300}"

echo "╔══════════════════════════════════════════╗"
echo "║  ⏪ ROLLBACK INITIATED                   ║"
echo "║  Namespace: ${NAMESPACE}                  ║"
echo "║  Deployment: ${DEPLOYMENT}                ║"
echo "╚══════════════════════════════════════════╝"

# Step 1: 查看回滾歷史
echo ""
echo "📋 Revision History:"
kubectl rollout history deployment/${DEPLOYMENT} -n ${NAMESPACE}

# Step 2: 獲取當前版本
CURRENT_IMAGE=$(kubectl get deployment ${DEPLOYMENT} -n ${NAMESPACE} \
  -o jsonpath='{.spec.template.spec.containers[0].image}')
echo ""
echo "📌 Current Image: ${CURRENT_IMAGE}"

# Step 3: 執行回滾
echo ""
echo "⏪ Rolling back to previous version..."
kubectl rollout undo deployment/${DEPLOYMENT} -n ${NAMESPACE}

# Step 4: 等待回滾完成
echo "⏳ Waiting for rollback to complete (timeout: ${TIMEOUT}s)..."
if kubectl rollout status deployment/${DEPLOYMENT} -n ${NAMESPACE} --timeout=${TIMEOUT}s; then
  NEW_IMAGE=$(kubectl get deployment ${DEPLOYMENT} -n ${NAMESPACE} \
    -o jsonpath='{.spec.template.spec.containers[0].image}')
  
  echo ""
  echo "╔══════════════════════════════════════════╗"
  echo "║  ✅ ROLLBACK SUCCESSFUL                  ║"
  echo "║  From: ${CURRENT_IMAGE}                  ║"
  echo "║  To:   ${NEW_IMAGE}                      ║"
  echo "╚══════════════════════════════════════════╝"
else
  echo ""
  echo "╔══════════════════════════════════════════╗"
  echo "║  ❌ ROLLBACK FAILED                      ║"
  echo "║  Manual intervention required!           ║"
  echo "╚══════════════════════════════════════════╝"
  
  # 打印 Pod 狀態
  echo ""
  echo "📊 Current Pod Status:"
  kubectl get pods -n ${NAMESPACE} -l app=${DEPLOYMENT}
  
  echo ""
  echo "📋 Recent Events:"
  kubectl get events -n ${NAMESPACE} --sort-by='.lastTimestamp' | tail -20
  
  exit 1
fi

# Step 5: 健康檢查
echo ""
echo "🏥 Running health check..."
sleep 10

HEALTH_STATUS=$(curl -s -o /dev/null -w "%{http_code}" \
  "https://${DEPLOYMENT}.myapp.com/health" || echo "000")

if [ "$HEALTH_STATUS" == "200" ]; then
  echo "✅ Health check passed! (Status: ${HEALTH_STATUS})"
else
  echo "⚠️ Health check returned status: ${HEALTH_STATUS}"
  echo "Please investigate manually."
fi
```

### Health Check Script

bash

```bash
#!/bin/bash
# scripts/health-check.sh
# ============================================
# 🏥 Comprehensive Health Check Script
# ============================================

set -euo pipefail

BASE_URL="${1:-https://myapp.com}"
TIMEOUT=10

echo "🏥 Running Health Checks for: ${BASE_URL}"
echo "=========================================="

PASS=0
FAIL=0

check() {
  local name=\$1
  local url=\$2
  local expected_status=${3:-200}
  
  status=$(curl -s -o /dev/null -w "%{http_code}" \
    --max-time ${TIMEOUT} "${url}" || echo "000")
  
  if [ "$status" == "$expected_status" ]; then
    echo "  ✅ ${name}: OK (${status})"
    ((PASS++))
  else
    echo "  ❌ ${name}: FAILED (expected ${expected_status}, got ${status})"
    ((FAIL++))
  fi
}

check_json() {
  local name=\$1
  local url=\$2
  local json_path=\$3
  local expected_value=\$4
  
  response=$(curl -s --max-time ${TIMEOUT} "${url}" || echo "{}")
  actual_value=$(echo "$response" | jq -r "${json_path}" 2>/dev/null || echo "null")
  
  if [ "$actual_value" == "$expected_value" ]; then
    echo "  ✅ ${name}: OK (${actual_value})"
    ((PASS++))
  else
    echo "  ❌ ${name}: FAILED (expected '${expected_value}', got '${actual_value}')"
    ((FAIL++))
  fi
}

# ==========================================
# 基本健康檢查
# ==========================================
echo ""
echo "📡 Basic Health Checks:"
check "Main endpoint" "${BASE_URL}/"
check "Health endpoint" "${BASE_URL}/health"
check "Readiness" "${BASE_URL}/health/ready"
check "Liveness" "${BASE_URL}/health/live"
check "Metrics" "${BASE_URL}/metrics"

# ==========================================
# API 端點檢查
# ==========================================
echo ""
echo "🔌 API Endpoint Checks:"
check "API v1" "${BASE_URL}/api/v1/status"
check "API docs" "${BASE_URL}/docs"

# ==========================================
# 依賴服務檢查
# ==========================================
echo ""
echo "🔗 Dependency Checks:"
check_json "Database" "${BASE_URL}/health/ready" ".checks.database" "healthy"
check_json "Redis" "${BASE_URL}/health/ready" ".checks.redis" "healthy"

# ==========================================
# SSL 證書檢查
# ==========================================
echo ""
echo "🔒 SSL Certificate Check:"
DOMAIN=$(echo ${BASE_URL} | sed 's|https://||' | sed 's|/.*||')
EXPIRY=$(echo | openssl s_client -servername ${DOMAIN} -connect ${DOMAIN}:443 2>/dev/null \
  | openssl x509 -noout -enddate 2>/dev/null | cut -d= -f2)
if [ -n "$EXPIRY" ]; then
  EXPIRY_EPOCH=$(date -d "$EXPIRY" +%s 2>/dev/null || date -jf "%b %d %T %Y %Z" "$EXPIRY" +%s)
  NOW_EPOCH=$(date +%s)
  DAYS_LEFT=$(( (EXPIRY_EPOCH - NOW_EPOCH) / 86400 ))
  
  if [ $DAYS_LEFT -gt 30 ]; then
    echo "  ✅ SSL Certificate: Valid (${DAYS_LEFT} days remaining)"
    ((PASS++))
  elif [ $DAYS_LEFT -gt 0 ]; then
    echo "  ⚠️ SSL Certificate: Expiring soon! (${DAYS_LEFT} days remaining)"
    ((FAIL++))
  else
    echo "  ❌ SSL Certificate: EXPIRED!"
    ((FAIL++))
  fi
fi

# ==========================================
# 結果摘要
# ==========================================
echo ""
echo "=========================================="
TOTAL=$((PASS + FAIL))
echo "📊 Results: ${PASS}/${TOTAL} passed, ${FAIL}/${TOTAL} failed"

if [ $FAIL -eq 0 ]; then
  echo "✅ All checks passed!"
  exit 0
else
  echo "❌ Some checks failed!"
  exit 1
fi
```

---

## 📖 Phase 10: SLOs and Error Budget

### SLO Definition

yaml

```yaml
# monitoring/slo/slo-definition.yml
# ============================================
# 📊 Service Level Objectives (SLOs)
# ============================================

service: my-app
owner: platform-team
tier: tier-1

slos:
  # ==========================================
  # SLO 1: 可用性 (Availability)
  # ==========================================
  - name: availability
    description: "API 成功響應的比例"
    target: 99.9%                    # 三個九
    window: 30d                       # 30 天滾動窗口
    
    sli:
      type: availability
      good_events: |
        sum(rate(http_requests_total{status_code!~"5.."}[5m]))
      total_events: |
        sum(rate(http_requests_total[5m]))
    
    error_budget:
      monthly_budget_minutes: 43.2    # 每月允許 43.2 分鐘停機
      # 30天 × 24小時 × 60分鐘 × 0.1% = 43.2 分鐘
    
    alerts:
      - burn_rate: 14.4               # 1小時內消耗完整月預算
        window: 1h
        severity: critical
      - burn_rate: 6.0                # 6小時內消耗完整月預算
        window: 6h
        severity: critical
      - burn_rate: 1.0                # 正常消耗速率
        window: 3d
        severity: warning

  # ==========================================
  # SLO 2: 延遲 (Latency)
  # ==========================================
  - name: latency
    description: "P99 延遲 < 500ms 的請求比例"
    target: 99.0%
    window: 30d
    
    sli:
      type: latency
      threshold_ms: 500
      good_events: |
        sum(rate(http_request_duration_seconds_bucket{le="0.5"}[5m]))
      total_events: |
        sum(rate(http_request_duration_seconds_count[5m]))

  # ==========================================
  # SLO 3: 訂單處理成功率
  # ==========================================
  - name: order_success_rate
    description: "訂單處理成功的比例"
    target: 99.5%
    window: 30d
    
    sli:
      type: quality
      good_events: |
        sum(rate(business_orders_total{status="success"}[5m]))
      total_events: |
        sum(rate(business_orders_total[5m]))
```

### Error Budget Prometheus Rules

yaml

```yaml
# monitoring/prometheus/slo_rules.yml
# ============================================
# 📊 SLO Recording & Alert Rules
# ============================================

groups:
  - name: slo_rules
    interval: 30s
    rules:
      # ==========================================
      # 可用性 SLI（每 30 秒計算一次）
      # ==========================================
      - record: slo:availability:ratio_rate5m
        expr: |
          sum(rate(http_requests_total{status_code!~"5.."}[5m]))
          /
          sum(rate(http_requests_total[5m]))

      # 30 天可用性
      - record: slo:availability:ratio_rate30d
        expr: |
          sum(increase(http_requests_total{status_code!~"5.."}[30d]))
          /
          sum(increase(http_requests_total[30d]))

      # ==========================================
      # 錯誤預算剩餘
      # ==========================================
      - record: slo:error_budget:remaining
        expr: |
          1 - (
            (1 - slo:availability:ratio_rate30d)
            /
            (1 - 0.999)
          )
        # 結果：
        # 1.0 = 100% 預算剩餘
        # 0.5 = 50% 預算剩餘
        # 0.0 = 預算耗盡！停止發布新功能！
        # < 0 = 超支了！

      # ==========================================
      # 告警：錯誤預算即將耗盡
      # ==========================================
      - alert: ErrorBudgetBurnRateTooHigh
        expr: slo:error_budget:remaining < 0.25
        for: 5m
        labels:
          severity: critical
        annotations:
          summary: "🚨 Error budget almost exhausted!"
          description: |
            Only {{ $value | humanizePercentage }} error budget remaining.
            STOP deploying new features and focus on reliability!

      - alert: ErrorBudgetExhausted
        expr: slo:error_budget:remaining <= 0
        for: 1m
        labels:
          severity: critical
        annotations:
          summary: "🔴 Error budget EXHAUSTED!"
          description: |
            Error budget is at {{ $value | humanizePercentage }}.
            All deployments should be frozen until SLO is restored.
```

---

## 📊 Phase 11: Complete DevOps Checklist

text

```text
┌──────────────────────────────────────────────────────────┐
│              DevOps Best Practices Checklist ✅            │
│                                                          │
│  🔵 Source Control                                       │
│  ├── [✅] Git branching strategy (trunk-based)           │
│  ├── [✅] Branch protection rules                        │
│  ├── [✅] Conventional commits                           │
│  ├── [✅] Pull Request + Code Review                     │
│  └── [✅] .gitignore properly configured                 │
│                                                          │
│  🟢 CI Pipeline                                          │
│  ├── [✅] Code linting (Ruff/Black/isort)                │
│  ├── [✅] Type checking (MyPy)                           │
│  ├── [✅] Unit tests with coverage > 80%                 │
│  ├── [✅] Integration tests with real services           │
│  ├── [✅] Test matrix (multiple Python versions)         │
│  ├── [✅] Security scanning (SAST/SCA/Secrets)           │
│  ├── [✅] Build artifacts (Docker image)                 │
│  └── [✅] Container image vulnerability scanning         │
│                                                          │
│  🟡 Containerization                                     │
│  ├── [✅] Multi-stage Dockerfile                         │
│  ├── [✅] Non-root user                                  │
│  ├── [✅] Health checks                                  │
│  ├── [✅] .dockerignore                                  │
│  ├── [✅] Minimal base image                             │
│  ├── [✅] Layer caching optimization                     │
│  └── [✅] Docker Compose for local dev                   │
│                                                          │
│  🟠 CD Pipeline                                          │
│  ├── [✅] Auto-deploy to Staging                         │
│  ├── [✅] Smoke tests on Staging                         │
│  ├── [✅] E2E tests on Staging                           │
│  ├── [✅] Manual approval for Production                 │
│  ├── [✅] Canary deployment strategy                     │
│  ├── [✅] Automated rollback on failure                  │
│  └── [✅] Release tagging                                │
│                                                          │
│  🔴 Kubernetes                                           │
│  ├── [✅] Deployment with rolling updates                │
│  ├── [✅] Resource requests and limits                   │
│  ├── [✅] Readiness/Liveness/Startup probes              │
│  ├── [✅] Horizontal Pod Autoscaler (HPA)                │
│  ├── [✅] Pod Disruption Budget (PDB)                    │
│  ├── [✅] Network Policies                               │
│  ├── [✅] Security Context (non-root, read-only)         │
│  ├── [✅] Pod Anti-affinity                              │
│  └── [✅] Ingress with TLS                               │
│                                                          │
│  🟣 Monitoring & Observability                           │
│  ├── [✅] Metrics (Prometheus)                           │
│  │   ├── Request rate, latency, errors                   │
│  │   ├── Business metrics                                │
│  │   └── Resource metrics                                │
│  ├── [✅] Logs (Structured JSON → Loki/ELK)              │
│  │   ├── Structured logging (structlog)                  │
│  │   ├── Request ID correlation                          │
│  │   └── Centralized log aggregation                     │
│  ├── [✅] Traces (OpenTelemetry → Jaeger/Tempo)          │
│  │   ├── Distributed tracing                             │
│  │   └── Auto-instrumentation                            │
│  ├── [✅] Dashboards (Grafana)                           │
│  ├── [✅] Alerts (Alertmanager)                          │
│  │   ├── Error rate alerts                               │
│  │   ├── Latency alerts                                  │
│  │   ├── Resource alerts                                 │
│  │   └── Business alerts                                 │
│  └── [✅] SLOs and Error Budgets                         │
│                                                          │
│  ⚫ Security (DevSecOps)                                 │
│  ├── [✅] SAST (Bandit)                                  │
│  ├── [✅] SCA (Safety/Snyk)                              │
│  ├── [✅] Secret scanning (TruffleHog)                   │
│  ├── [✅] Container scanning (Trivy)                     │
│  ├── [✅] Secret management (Vault/AWS SM)               │
│  ├── [✅] Network policies                               │
│  ├── [✅] Non-root containers                            │
│  └── [✅] TLS everywhere                                 │
│                                                          │
│  🏗️ Infrastructure as Code                               │
│  ├── [✅] Terraform for cloud resources                  │
│  ├── [✅] Remote state with locking                      │
│  ├── [✅] Modular design                                 │
│  └── [✅] Version controlled                             │
│                                                          │
│  🔄 Incident Management                                 │
│  ├── [✅] Automated rollback script                      │
│  ├── [✅] Health check script                            │
│  ├── [✅] On-call rotation                               │
│  ├── [✅] Post-mortem template                           │
│  └── [✅] Runbooks                                       │
│                                                          │
└──────────────────────────────────────────────────────────┘
```

---

## 📊 DORA Metrics Targets

text

```text
┌──────────────────────────────────────────────────────────┐
│                    DORA Metrics Goals                      │
│                                                          │
│  📊 Before DevOps → After DevOps                         │
│                                                          │
│  🚀 Deployment Frequency                                  │
│     Monthly → Multiple times per day                     │
│     ❌ → ✅                                               │
│                                                          │
│  ⏱️ Lead Time for Changes                                │
│     2-4 weeks → < 1 day                                  │
│     ❌ → ✅                                               │
│                                                          │
│  🔄 Change Failure Rate                                   │
│     30-50% → < 5%                                        │
│     ❌ → ✅                                               │
│                                                          │
│  ⏰ Mean Time to Recovery (MTTR)                          │
│     Hours/Days → < 1 hour                                │
│     ❌ → ✅                                               │
│                                                          │
│  ──────────────────────────────────────────────          │
│                                                          │
│  💡 Remember:                                             │
│                                                          │
│  「DevOps is not just tools — it's CULTURE.」            │
│  「Automate everything you can.」                         │
│  「Measure everything that matters.」                     │
│  「Fail fast, learn fast, recover fast.」                │
│  「Security is everyone's responsibility.」              │
│                                                          │
└──────────────────────────────────────────────────────────┘
```

---

> 💡 **這份指南涵蓋了 DevOps 最佳實踐的完整實現：**
> 
> ✅ **Source Control** — Git branching + PR reviews  
> ✅ **CI** — Lint → Test → Build → Scan → Artifact  
> ✅ **Containerization** — Multi-stage Docker + Compose  
> ✅ **CD** — Staging → Canary → Production + Auto-rollback  
> ✅ **Orchestration** — Kubernetes + HPA + PDB + Network Policy  
> ✅ **Monitoring** — Metrics + Logs + Traces + Alerts + SLOs  
> ✅ **Security** — SAST + SCA + Secrets + Network + TLS  
> ✅ **IaC** — Terraform for cloud infrastructure  
> ✅ **Incident Management** — Rollback + Health checks + Runbooks
