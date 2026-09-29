# Infraestructura y DevOps para Prompts

## Introducción: La Infraestructura como Ventaja Competitiva

En el prompt engineering empresarial, la infraestructura no es solo soporte técnico; es el fundamento que permite escalabilidad, confiabilidad y innovación continua. Esta sección explora las mejores prácticas para construir sistemas de infraestructura que soporten operaciones de IA a escala empresarial.

## Arquitectura de Infraestructura para LLM Operations

### 1. Diseño de Arquitectura Cloud-Native

**Arquitectura de Microservicios para Prompts:**
```yaml
# kubernetes/prompt-service-architecture.yaml
apiVersion: v1
kind: Namespace
metadata:
  name: prompt-engineering
  labels:
    environment: production
    team: ai-platform
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: prompt-orchestrator
  namespace: prompt-engineering
spec:
  replicas: 3
  selector:
    matchLabels:
      app: prompt-orchestrator
  template:
    metadata:
      labels:
        app: prompt-orchestrator
    spec:
      containers:
      - name: orchestrator
        image: ai-platform/prompt-orchestrator:v2.1.0
        ports:
        - containerPort: 8080
        env:
        - name: REDIS_URL
          valueFrom:
            secretKeyRef:
              name: redis-credentials
              key: url
        - name: MODEL_ENDPOINTS
          valueFrom:
            configMapKeyRef:
              name: model-config
              key: endpoints
        resources:
          requests:
            memory: "256Mi"
            cpu: "250m"
          limits:
            memory: "512Mi"
            cpu: "500m"
        livenessProbe:
          httpGet:
            path: /health
            port: 8080
          initialDelaySeconds: 30
          periodSeconds: 10
        readinessProbe:
          httpGet:
            path: /ready
            port: 8080
          initialDelaySeconds: 5
          periodSeconds: 5
---
apiVersion: v1
kind: Service
metadata:
  name: prompt-orchestrator-service
  namespace: prompt-engineering
spec:
  selector:
    app: prompt-orchestrator
  ports:
  - protocol: TCP
    port: 80
    targetPort: 8080
  type: ClusterIP
```

**Infraestructura como Código (Terraform):**
```hcl
# infrastructure/main.tf
terraform {
  required_version = ">= 1.0"
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
    kubernetes = {
      source  = "hashicorp/kubernetes"
      version = "~> 2.20"
    }
  }
}

# EKS Cluster para Prompt Engineering
module "eks_cluster" {
  source = "./modules/eks"
  
  cluster_name = "prompt-engineering-cluster"
  cluster_version = "1.27"
  
  vpc_id = module.vpc.vpc_id
  subnet_ids = module.vpc.private_subnets
  
  node_groups = {
    general = {
      instance_types = ["m5.large", "m5.xlarge"]
      min_size = 2
      max_size = 10
      desired_size = 3
      
      labels = {
        role = "general"
      }
    }
    
    gpu_inference = {
      instance_types = ["g4dn.xlarge", "g4dn.2xlarge"]
      min_size = 0
      max_size = 5
      desired_size = 1
      
      labels = {
        role = "gpu-inference"
        "nvidia.com/gpu" = "true"
      }
      
      taints = [
        {
          key = "nvidia.com/gpu"
          value = "true"
          effect = "NO_SCHEDULE"
        }
      ]
    }
  }
  
  tags = {
    Environment = "production"
    Project = "prompt-engineering"
    Team = "ai-platform"
  }
}

# Redis Cluster para Cache y Session Management
resource "aws_elasticache_replication_group" "prompt_cache" {
  replication_group_id       = "prompt-cache"
  description                = "Redis cluster for prompt caching"
  
  node_type                  = "cache.r6g.large"
  port                       = 6379
  parameter_group_name       = "default.redis7"
  
  num_cache_clusters         = 3
  automatic_failover_enabled = true
  multi_az_enabled          = true
  
  subnet_group_name = aws_elasticache_subnet_group.prompt_cache.name
  security_group_ids = [aws_security_group.redis.id]
  
  at_rest_encryption_enabled = true
  transit_encryption_enabled = true
  
  tags = {
    Environment = "production"
    Project = "prompt-engineering"
  }
}

# PostgreSQL para Metadata y Audit Trail
resource "aws_rds_cluster" "prompt_metadata" {
  cluster_identifier      = "prompt-metadata"
  engine                 = "aurora-postgresql"
  engine_version         = "14.9"
  database_name          = "prompt_engineering"
  master_username        = "prompt_admin"
  manage_master_user_password = true
  
  serverlessv2_scaling_configuration {
    max_capacity = 16
    min_capacity = 0.5
  }
  
  vpc_security_group_ids = [aws_security_group.rds.id]
  db_subnet_group_name   = aws_db_subnet_group.prompt_metadata.name
  
  backup_retention_period = 30
  preferred_backup_window = "03:00-04:00"
  preferred_maintenance_window = "sun:04:00-sun:05:00"
  
  encryption_enabled = true
  deletion_protection = true
  
  tags = {
    Environment = "production"
    Project = "prompt-engineering"
  }
}
```

### 2. Pipeline de CI/CD para Prompts

**GitHub Actions Workflow:**
```yaml
# .github/workflows/prompt-deployment.yml
name: Prompt Engineering CI/CD

on:
  push:
    branches: [main, develop]
    paths: ['prompts/**', 'src/**', 'tests/**']
  pull_request:
    branches: [main]
    paths: ['prompts/**', 'src/**', 'tests/**']

env:
  REGISTRY: ghcr.io
  IMAGE_NAME: ${{ github.repository }}/prompt-service

jobs:
  validate-prompts:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Setup Python
        uses: actions/setup-python@v4
        with:
          python-version: '3.11'
          
      - name: Install dependencies
        run: |
          pip install -r requirements-dev.txt
          
      - name: Validate prompt schemas
        run: |
          python scripts/validate_prompt_schemas.py --directory prompts/
          
      - name: Run prompt tests
        run: |
          pytest tests/prompts/ -v --cov=src/prompt_engine --cov-report=xml
          
      - name: Upload coverage reports
        uses: codecov/codecov-action@v3
        with:
          file: ./coverage.xml

  security-scan:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Run security scan
        uses: securecodewarrior/github-action-add-sarif@v1
        with:
          sarif-file: 'security-scan-results.sarif'
          
      - name: Scan for secrets
        uses: trufflesecurity/trufflehog@main
        with:
          path: ./
          base: ${{ github.event.repository.default_branch }}
          head: HEAD

  build-and-test:
    needs: [validate-prompts, security-scan]
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v2
        
      - name: Login to Container Registry
        uses: docker/login-action@v2
        with:
          registry: ${{ env.REGISTRY }}
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}
          
      - name: Extract metadata
        id: meta
        uses: docker/metadata-action@v4
        with:
          images: ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}
          tags: |
            type=ref,event=branch
            type=ref,event=pr
            type=sha,prefix={{branch}}-
            
      - name: Build and push Docker image
        uses: docker/build-push-action@v4
        with:
          context: .
          platforms: linux/amd64,linux/arm64
          push: true
          tags: ${{ steps.meta.outputs.tags }}
          labels: ${{ steps.meta.outputs.labels }}
          cache-from: type=gha
          cache-to: type=gha,mode=max

  integration-tests:
    needs: build-and-test
    runs-on: ubuntu-latest
    services:
      redis:
        image: redis:7-alpine
        ports:
          - 6379:6379
      postgres:
        image: postgres:14
        env:
          POSTGRES_PASSWORD: test_password
          POSTGRES_DB: test_db
        ports:
          - 5432:5432
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5

    steps:
      - uses: actions/checkout@v3
      
      - name: Setup Python
        uses: actions/setup-python@v4
        with:
          python-version: '3.11'
          
      - name: Install dependencies
        run: |
          pip install -r requirements.txt
          pip install -r requirements-test.txt
          
      - name: Run integration tests
        env:
          REDIS_URL: redis://localhost:6379
          DATABASE_URL: postgresql://postgres:test_password@localhost:5432/test_db
          OPENAI_API_KEY: ${{ secrets.OPENAI_API_KEY }}
        run: |
          pytest tests/integration/ -v --tb=short

  deploy-staging:
    if: github.ref == 'refs/heads/develop'
    needs: [integration-tests]
    runs-on: ubuntu-latest
    environment: staging
    
    steps:
      - uses: actions/checkout@v3
      
      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v2
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: us-west-2
          
      - name: Setup kubectl
        uses: azure/setup-kubectl@v3
        with:
          version: 'v1.27.0'
          
      - name: Update kubeconfig
        run: |
          aws eks update-kubeconfig --region us-west-2 --name prompt-engineering-staging
          
      - name: Deploy to staging
        run: |
          envsubst < k8s/staging/deployment.yaml | kubectl apply -f -
          kubectl rollout status deployment/prompt-service -n staging

  deploy-production:
    if: github.ref == 'refs/heads/main'
    needs: [integration-tests]
    runs-on: ubuntu-latest
    environment: production
    
    steps:
      - uses: actions/checkout@v3
      
      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v2
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: us-west-2
          
      - name: Setup kubectl
        uses: azure/setup-kubectl@v3
        with:
          version: 'v1.27.0'
          
      - name: Update kubeconfig
        run: |
          aws eks update-kubeconfig --region us-west-2 --name prompt-engineering-cluster
          
      - name: Blue-Green Deployment
        run: |
          # Deploy to green environment
          envsubst < k8s/production/deployment-green.yaml | kubectl apply -f -
          kubectl rollout status deployment/prompt-service-green -n production
          
          # Run smoke tests
          python scripts/smoke_tests.py --environment green
          
          # Switch traffic to green
          kubectl patch service prompt-service -n production -p '{"spec":{"selector":{"version":"green"}}}'
          
          # Wait and verify
          sleep 30
          python scripts/verify_deployment.py --environment production
          
          # Clean up old blue deployment
          kubectl delete deployment prompt-service-blue -n production --ignore-not-found=true
```

### 3. Gestión de Configuración y Secrets

**Configuración con Helm y ConfigMaps:**
```yaml
# helm/prompt-service/values.yaml
replicaCount: 3

image:
  repository: ghcr.io/company/prompt-service
  pullPolicy: IfNotPresent
  tag: ""

service:
  type: ClusterIP
  port: 80
  targetPort: 8080

ingress:
  enabled: true
  className: "nginx"
  annotations:
    cert-manager.io/cluster-issuer: "letsencrypt-prod"
    nginx.ingress.kubernetes.io/rate-limit: "100"
    nginx.ingress.kubernetes.io/rate-limit-window: "1m"
  hosts:
    - host: prompts.company.com
      paths:
        - path: /
          pathType: Prefix
  tls:
    - secretName: prompt-service-tls
      hosts:
        - prompts.company.com

autoscaling:
  enabled: true
  minReplicas: 3
  maxReplicas: 20
  targetCPUUtilizationPercentage: 70
  targetMemoryUtilizationPercentage: 80

resources:
  limits:
    cpu: 1000m
    memory: 1Gi
  requests:
    cpu: 500m
    memory: 512Mi

nodeSelector:
  kubernetes.io/arch: amd64

tolerations: []

affinity:
  podAntiAffinity:
    preferredDuringSchedulingIgnoredDuringExecution:
    - weight: 100
      podAffinityTerm:
        labelSelector:
          matchExpressions:
          - key: app.kubernetes.io/name
            operator: In
            values:
            - prompt-service
        topologyKey: kubernetes.io/hostname

config:
  environment: production
  logLevel: info
  
  cache:
    enabled: true
    ttl: 3600
    maxSize: 10000
    
  models:
    openai:
      enabled: true
      maxConcurrentRequests: 100
      rateLimitRpm: 3000
      timeoutSeconds: 30
    
    anthropic:
      enabled: true
      maxConcurrentRequests: 50
      rateLimitRpm: 1000
      timeoutSeconds: 45
      
  monitoring:
    metrics:
      enabled: true
      port: 9090
    tracing:
      enabled: true
      jaeger:
        endpoint: "http://jaeger-collector:14268/api/traces"
        
  security:
    rateLimiting:
      enabled: true
      defaultRpm: 60
      burstSize: 10
    authentication:
      enabled: true
      jwksUrl: "https://auth.company.com/.well-known/jwks.json"
```

**Gestión de Secrets con External Secrets Operator:**
```yaml
# k8s/external-secrets.yaml
apiVersion: external-secrets.io/v1beta1
kind: SecretStore
metadata:
  name: aws-secrets-manager
  namespace: prompt-engineering
spec:
  provider:
    aws:
      service: SecretsManager
      region: us-west-2
      auth:
        serviceAccount:
          name: external-secrets-sa
---
apiVersion: external-secrets.io/v1beta1
kind: ExternalSecret
metadata:
  name: prompt-service-secrets
  namespace: prompt-engineering
spec:
  refreshInterval: 300s
  secretStoreRef:
    name: aws-secrets-manager
    kind: SecretStore
  target:
    name: prompt-service-secrets
    creationPolicy: Owner
    template:
      type: Opaque
      data:
        openai-api-key: "{{ .openai_api_key }}"
        anthropic-api-key: "{{ .anthropic_api_key }}"
        database-url: "postgresql://{{ .db_username }}:{{ .db_password }}@{{ .db_host }}:5432/{{ .db_name }}"
        redis-url: "redis://{{ .redis_host }}:6379"
        jwt-secret: "{{ .jwt_secret }}"
  data:
  - secretKey: openai_api_key
    remoteRef:
      key: "prompt-service/openai"
      property: api_key
  - secretKey: anthropic_api_key
    remoteRef:
      key: "prompt-service/anthropic"
      property: api_key
  - secretKey: db_username
    remoteRef:
      key: "prompt-service/database"
      property: username
  - secretKey: db_password
    remoteRef:
      key: "prompt-service/database"
      property: password
  - secretKey: db_host
    remoteRef:
      key: "prompt-service/database"
      property: host
  - secretKey: db_name
    remoteRef:
      key: "prompt-service/database"
      property: database
  - secretKey: redis_host
    remoteRef:
      key: "prompt-service/redis"
      property: host
  - secretKey: jwt_secret
    remoteRef:
      key: "prompt-service/auth"
      property: jwt_secret
```

## Monitoreo y Observabilidad

### 1. Stack de Observabilidad Completo

**Configuración de Prometheus y Grafana:**
```python
# monitoring/prometheus_config.py
from prometheus_client import Counter, Histogram, Gauge, start_http_server
import time
import functools

class PromptMetrics:
    def __init__(self):
        # Contadores básicos
        self.prompt_requests_total = Counter(
            'prompt_requests_total',
            'Total number of prompt requests',
            ['prompt_id', 'model', 'status']
        )
        
        self.prompt_tokens_total = Counter(
            'prompt_tokens_total',
            'Total number of tokens processed',
            ['prompt_id', 'model', 'type']  # type: input, output
        )
        
        # Histogramas para latencia
        self.prompt_request_duration = Histogram(
            'prompt_request_duration_seconds',
            'Time spent processing prompt requests',
            ['prompt_id', 'model'],
            buckets=[0.1, 0.5, 1.0, 2.5, 5.0, 10.0, 30.0, 60.0]
        )
        
        self.model_api_duration = Histogram(
            'model_api_duration_seconds',
            'Time spent calling model APIs',
            ['model', 'endpoint'],
            buckets=[0.1, 0.5, 1.0, 2.5, 5.0, 10.0, 30.0]
        )
        
        # Gauges para estado actual
        self.active_requests = Gauge(
            'prompt_active_requests',
            'Number of currently active prompt requests',
            ['model']
        )
        
        self.cache_hit_rate = Gauge(
            'prompt_cache_hit_rate',
            'Cache hit rate for prompt responses',
            ['cache_type']
        )
        
        self.model_availability = Gauge(
            'model_availability',
            'Model availability status (1=available, 0=unavailable)',
            ['model', 'endpoint']
        )
        
        # Métricas de calidad
        self.prompt_quality_score = Histogram(
            'prompt_quality_score',
            'Quality score of prompt responses',
            ['prompt_id', 'metric_type'],
            buckets=[0.0, 0.2, 0.4, 0.6, 0.7, 0.8, 0.9, 0.95, 1.0]
        )
    
    def track_request(self, prompt_id: str, model: str):
        """Decorator para trackear requests de prompts"""
        def decorator(func):
            @functools.wraps(func)
            def wrapper(*args, **kwargs):
                start_time = time.time()
                self.active_requests.labels(model=model).inc()
                
                try:
                    result = func(*args, **kwargs)
                    status = 'success'
                    
                    # Trackear tokens si están disponibles
                    if isinstance(result, dict) and 'usage' in result:
                        usage = result['usage']
                        if 'prompt_tokens' in usage:
                            self.prompt_tokens_total.labels(
                                prompt_id=prompt_id, model=model, type='input'
                            ).inc(usage['prompt_tokens'])
                        if 'completion_tokens' in usage:
                            self.prompt_tokens_total.labels(
                                prompt_id=prompt_id, model=model, type='output'
                            ).inc(usage['completion_tokens'])
                    
                    return result
                    
                except Exception as e:
                    status = 'error'
                    raise
                    
                finally:
                    duration = time.time() - start_time
                    self.prompt_request_duration.labels(
                        prompt_id=prompt_id, model=model
                    ).observe(duration)
                    
                    self.prompt_requests_total.labels(
                        prompt_id=prompt_id, model=model, status=status
                    ).inc()
                    
                    self.active_requests.labels(model=model).dec()
            
            return wrapper
        return decorator
    
    def record_quality_metrics(self, prompt_id: str, quality_scores: Dict[str, float]):
        """Registra métricas de calidad"""
        for metric_type, score in quality_scores.items():
            self.prompt_quality_score.labels(
                prompt_id=prompt_id, metric_type=metric_type
            ).observe(score)

# Instancia global de métricas
metrics = PromptMetrics()

# Función para iniciar servidor de métricas
def start_metrics_server(port: int = 8000):
    start_http_server(port)
    print(f"Metrics server started on port {port}")
```

**Dashboard de Grafana (JSON Configuration):**
```json
{
  "dashboard": {
    "id": null,
    "title": "Prompt Engineering Operations",
    "tags": ["prompt-engineering", "ai", "operations"],
    "timezone": "browser",
    "panels": [
      {
        "id": 1,
        "title": "Request Rate",
        "type": "stat",
        "targets": [
          {
            "expr": "sum(rate(prompt_requests_total[5m]))",
            "legendFormat": "Requests/sec"
          }
        ],
        "fieldConfig": {
          "defaults": {
            "unit": "reqps",
            "color": {
              "mode": "thresholds"
            },
            "thresholds": {
              "steps": [
                {"color": "green", "value": null},
                {"color": "yellow", "value": 50},
                {"color": "red", "value": 100}
              ]
            }
          }
        }
      },
      {
        "id": 2,
        "title": "Response Time Percentiles",
        "type": "timeseries",
        "targets": [
          {
            "expr": "histogram_quantile(0.50, sum(rate(prompt_request_duration_seconds_bucket[5m])) by (le))",
            "legendFormat": "50th percentile"
          },
          {
            "expr": "histogram_quantile(0.95, sum(rate(prompt_request_duration_seconds_bucket[5m])) by (le))",
            "legendFormat": "95th percentile"
          },
          {
            "expr": "histogram_quantile(0.99, sum(rate(prompt_request_duration_seconds_bucket[5m])) by (le))",
            "legendFormat": "99th percentile"
          }
        ],
        "fieldConfig": {
          "defaults": {
            "unit": "s"
          }
        }
      },
      {
        "id": 3,
        "title": "Error Rate by Model",
        "type": "timeseries",
        "targets": [
          {
            "expr": "sum(rate(prompt_requests_total{status=\"error\"}[5m])) by (model) / sum(rate(prompt_requests_total[5m])) by (model)",
            "legendFormat": "{{model}}"
          }
        ],
        "fieldConfig": {
          "defaults": {
            "unit": "percentunit",
            "max": 1,
            "min": 0
          }
        }
      },
      {
        "id": 4,
        "title": "Token Usage",
        "type": "timeseries",
        "targets": [
          {
            "expr": "sum(rate(prompt_tokens_total[5m])) by (type, model)",
            "legendFormat": "{{model}} - {{type}}"
          }
        ],
        "fieldConfig": {
          "defaults": {
            "unit": "short"
          }
        }
      },
      {
        "id": 5,
        "title": "Cache Hit Rate",
        "type": "gauge",
        "targets": [
          {
            "expr": "prompt_cache_hit_rate",
            "legendFormat": "{{cache_type}}"
          }
        ],
        "fieldConfig": {
          "defaults": {
            "unit": "percentunit",
            "max": 1,
            "min": 0,
            "thresholds": {
              "steps": [
                {"color": "red", "value": 0},
                {"color": "yellow", "value": 0.7},
                {"color": "green", "value": 0.85}
              ]
            }
          }
        }
      },
      {
        "id": 6,
        "title": "Quality Scores Distribution",
        "type": "heatmap",
        "targets": [
          {
            "expr": "sum(rate(prompt_quality_score_bucket[5m])) by (le, metric_type)",
            "legendFormat": "{{metric_type}}"
          }
        ],
        "fieldConfig": {
          "defaults": {
            "unit": "short"
          }
        }
      }
    ],
    "time": {
      "from": "now-1h",
      "to": "now"
    },
    "refresh": "30s"
  }
}
```

### 2. Logging Estructurado y Trazabilidad

**Sistema de Logging Avanzado:**
```python
# logging/structured_logger.py
import structlog
import logging
import json
from datetime import datetime
from typing import Dict, Any, Optional
import traceback
import uuid

class PromptEngineeeringLogger:
    def __init__(self, service_name: str, environment: str):
        self.service_name = service_name
        self.environment = environment
        
        # Configurar structlog
        structlog.configure(
            processors=[
                structlog.contextvars.merge_contextvars,
                structlog.processors.add_log_level,
                structlog.processors.TimeStamper(fmt="iso"),
                self._add_service_context,
                structlog.dev.ConsoleRenderer() if environment == "development" 
                else structlog.processors.JSONRenderer()
            ],
            wrapper_class=structlog.make_filtering_bound_logger(logging.INFO),
            logger_factory=structlog.WriteLoggerFactory(),
            cache_logger_on_first_use=True,
        )
        
        self.logger = structlog.get_logger()
    
    def _add_service_context(self, logger, method_name, event_dict):
        """Añade contexto del servicio a cada log"""
        event_dict["service"] = self.service_name
        event_dict["environment"] = self.environment
        return event_dict
    
    def log_prompt_request(self, prompt_id: str, user_id: str, 
                          model: str, prompt_content: str,
                          request_id: Optional[str] = None) -> str:
        """Registra request de prompt con contexto completo"""
        
        if not request_id:
            request_id = str(uuid.uuid4())
        
        # Configurar contexto para esta request
        structlog.contextvars.bind_contextvars(
            request_id=request_id,
            prompt_id=prompt_id,
            user_id=user_id,
            model=model
        )
        
        self.logger.info(
            "prompt_request_started",
            prompt_id=prompt_id,
            user_id=user_id,
            model=model,
            prompt_length=len(prompt_content),
            request_id=request_id
        )
        
        return request_id
    
    def log_prompt_response(self, request_id: str, response_content: str,
                           usage_stats: Dict, quality_scores: Dict,
                           duration_ms: float, success: bool = True):
        """Registra respuesta de prompt con métricas"""
        
        log_data = {
            "event": "prompt_request_completed",
            "request_id": request_id,
            "success": success,
            "duration_ms": duration_ms,
            "response_length": len(response_content),
            "usage_stats": usage_stats,
            "quality_scores": quality_scores
        }
        
        if success:
            self.logger.info(**log_data)
        else:
            self.logger.error(**log_data)
    
    def log_error(self, request_id: str, error: Exception, 
                  context: Dict[str, Any] = None):
        """Registra errores con contexto completo"""
        
        error_data = {
            "event": "prompt_request_error",
            "request_id": request_id,
            "error_type": type(error).__name__,
            "error_message": str(error),
            "traceback": traceback.format_exc(),
            "context": context or {}
        }
        
        self.logger.error(**error_data)
    
    def log_model_api_call(self, model: str, endpoint: str, 
                          duration_ms: float, tokens_used: int,
                          success: bool, request_id: str):
        """Registra llamadas a APIs de modelos"""
        
        self.logger.info(
            "model_api_call",
            request_id=request_id,
            model=model,
            endpoint=endpoint,
            duration_ms=duration_ms,
            tokens_used=tokens_used,
            success=success
        )
    
    def log_cache_operation(self, operation: str, cache_key: str,
                           hit: bool, request_id: str):
        """Registra operaciones de cache"""
        
        self.logger.info(
            "cache_operation",
            request_id=request_id,
            operation=operation,
            cache_key=cache_key,
            cache_hit=hit
        )
    
    def log_security_event(self, event_type: str, user_id: str,
                          details: Dict, severity: str = "warning"):
        """Registra eventos de seguridad"""
        
        log_method = getattr(self.logger, severity.lower(), self.logger.warning)
        
        log_method(
            "security_event",
            event_type=event_type,
            user_id=user_id,
            details=details,
            severity=severity
        )

# Instancia global del logger
logger = PromptEngineeeringLogger("prompt-service", "production")
```

### 3. Alerting y Notificaciones

**Sistema de Alertas con Prometheus AlertManager:**
```yaml
# monitoring/alert-rules.yml
groups:
- name: prompt-service-alerts
  rules:
  
  # Alertas de performance
  - alert: HighPromptLatency
    expr: histogram_quantile(0.95, sum(rate(prompt_request_duration_seconds_bucket[5m])) by (le)) > 10
    for: 2m
    labels:
      severity: warning
      team: ai-platform
    annotations:
      summary: "High prompt processing latency detected"
      description: "95th percentile latency is {{ $value }}s for the last 5 minutes"
      runbook_url: "https://wiki.company.com/prompt-service/runbooks/high-latency"
  
  - alert: PromptServiceDown
    expr: up{job="prompt-service"} == 0
    for: 1m
    labels:
      severity: critical
      team: ai-platform
    annotations:
      summary: "Prompt service is down"
      description: "Prompt service has been down for more than 1 minute"
      runbook_url: "https://wiki.company.com/prompt-service/runbooks/service-down"
  
  # Alertas de error rate
  - alert: HighErrorRate
    expr: sum(rate(prompt_requests_total{status="error"}[5m])) / sum(rate(prompt_requests_total[5m])) > 0.05
    for: 3m
    labels:
      severity: warning
      team: ai-platform
    annotations:
      summary: "High error rate in prompt service"
      description: "Error rate is {{ $value | humanizePercentage }} for the last 5 minutes"
  
  # Alertas de calidad
  - alert: LowQualityScores
    expr: avg(prompt_quality_score) < 0.7
    for: 5m
    labels:
      severity: warning
      team: ai-platform
    annotations:
      summary: "Low quality scores detected"
      description: "Average quality score is {{ $value }} for the last 5 minutes"
  
  # Alertas de recursos
  - alert: HighMemoryUsage
    expr: (container_memory_usage_bytes{pod=~"prompt-service-.*"} / container_spec_memory_limit_bytes) > 0.9
    for: 2m
    labels:
      severity: warning
      team: ai-platform
    annotations:
      summary: "High memory usage in prompt service"
      description: "Memory usage is {{ $value | humanizePercentage }} on pod {{ $labels.pod }}"
  
  # Alertas de modelo
  - alert: ModelAPIFailure
    expr: model_availability == 0
    for: 1m
    labels:
      severity: critical
      team: ai-platform
    annotations:
      summary: "Model API is unavailable"
      description: "{{ $labels.model }} API at {{ $labels.endpoint }} is unavailable"
      runbook_url: "https://wiki.company.com/prompt-service/runbooks/model-api-failure"
```

**Configuración de AlertManager:**
```yaml
# monitoring/alertmanager.yml
global:
  smtp_smarthost: 'smtp.company.com:587'
  smtp_from: 'alerts@company.com'

route:
  group_by: ['alertname', 'team']
  group_wait: 30s
  group_interval: 5m
  repeat_interval: 4h
  receiver: 'default'
  routes:
  - match:
      severity: critical
    receiver: 'critical-alerts'
    continue: true
  - match:
      team: ai-platform
    receiver: 'ai-platform-team'

receivers:
- name: 'default'
  slack_configs:
  - api_url: 'https://hooks.slack.com/services/...'
    channel: '#alerts'
    title: 'Alert: {{ .GroupLabels.alertname }}'
    text: '{{ range .Alerts }}{{ .Annotations.description }}{{ end }}'

- name: 'critical-alerts'
  slack_configs:
  - api_url: 'https://hooks.slack.com/services/...'
    channel: '#critical-alerts'
    title: 'CRITICAL: {{ .GroupLabels.alertname }}'
    text: '{{ range .Alerts }}{{ .Annotations.description }}{{ end }}'
  pagerduty_configs:
  - service_key: 'your-pagerduty-service-key'
    description: '{{ .GroupLabels.alertname }}: {{ .CommonAnnotations.summary }}'

- name: 'ai-platform-team'
  email_configs:
  - to: 'ai-platform@company.com'
    subject: '[PROMPT-SERVICE] {{ .GroupLabels.alertname }}'
    body: |
      {{ range .Alerts }}
      Alert: {{ .Annotations.summary }}
      Description: {{ .Annotations.description }}
      Runbook: {{ .Annotations.runbook_url }}
      {{ end }}
  slack_configs:
  - api_url: 'https://hooks.slack.com/services/...'
    channel: '#ai-platform'
    title: '{{ .GroupLabels.alertname }}'
    text: '{{ range .Alerts }}{{ .Annotations.description }}{{ end }}'
```

Esta infraestructura proporciona una base sólida para operaciones de prompt engineering a escala empresarial, con observabilidad completa, deployment automatizado y gestión robusta de configuración y secrets.
