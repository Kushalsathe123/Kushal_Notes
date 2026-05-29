# Kushal's Tech Study Guide
> Deep dive into technologies from your resume — concepts + examples, topic by topic.

---

## Table of Contents
1. [Docker](#1-docker)
2. [Kubernetes](#2-kubernetes)
3. [Terraform](#3-terraform)
4. [Azure (Core Services)](#4-azure-core-services)
5. [AWS (Core Services)](#5-aws-core-services)
6. [CI/CD with GitHub Actions](#6-cicd-with-github-actions)
7. [Microservices Architecture](#7-microservices-architecture)
8. [Azure Service Bus (Messaging)](#8-azure-service-bus-messaging)
9. [.NET / ASP.NET Core](#9-net--aspnet-core)
10. [JWT Authentication](#10-jwt-authentication)
11. [SQL & Azure SQL](#11-sql--azure-sql)
12. [Nginx (Reverse Proxy)](#12-nginx-reverse-proxy)
13. [Monitoring: Azure Monitor & CloudWatch](#13-monitoring-azure-monitor--cloudwatch)

---

## 1. Docker

### What is Docker?
Docker packages your application and its dependencies into a **container** — a lightweight, portable, self-sufficient unit that runs the same way everywhere.

### Core Concepts
| Concept | Description |
|---|---|
| **Image** | Blueprint/template for a container (read-only) |
| **Container** | Running instance of an image |
| **Dockerfile** | Script that defines how to build an image |
| **Docker Hub** | Public registry to store/share images |
| **Volume** | Persistent storage for containers |
| **Network** | Communication channel between containers |

### Dockerfile Example (ASP.NET Core)
```dockerfile
# Build stage
FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
WORKDIR /app
COPY *.csproj ./
RUN dotnet restore
COPY . ./
RUN dotnet publish -c Release -o out

# Runtime stage
FROM mcr.microsoft.com/dotnet/aspnet:8.0
WORKDIR /app
COPY --from=build /app/out .
EXPOSE 80
ENTRYPOINT ["dotnet", "MyApp.dll"]
```

### Key Commands
```bash
docker build -t myapp:v1 .          # Build image
docker run -d -p 8080:80 myapp:v1   # Run container (detached, port mapped)
docker ps                            # List running containers
docker logs <container_id>           # View logs
docker exec -it <id> /bin/bash       # Shell into container
docker stop <id>                     # Stop container
docker-compose up -d                 # Start multi-container app
```

### Docker Compose Example
```yaml
version: '3.8'
services:
  api:
    build: ./api
    ports:
      - "5000:80"
    environment:
      - ConnectionStrings__Default=Server=db;Database=mydb;
    depends_on:
      - db
  db:
    image: mcr.microsoft.com/mssql/server:2022-latest
    environment:
      SA_PASSWORD: "YourPassword123!"
      ACCEPT_EULA: "Y"
    ports:
      - "1433:1433"
```

### Things to Know Deeply
- Difference between `CMD` vs `ENTRYPOINT` in Dockerfile
- Multi-stage builds (why they reduce image size)
- Bind mounts vs Volumes
- Docker networking modes: bridge, host, none
- `.dockerignore` file

---

## 2. Kubernetes

### What is Kubernetes?
Kubernetes (K8s) is an **orchestration platform** that automates deploying, scaling, and managing containerized applications across a cluster of machines.

### Core Architecture
```
Control Plane:
  ├── API Server       → Entry point for all K8s commands
  ├── etcd             → Distributed key-value store (cluster state)
  ├── Scheduler        → Decides which node runs which pod
  └── Controller Mgr  → Maintains desired state

Worker Nodes:
  ├── kubelet          → Agent that runs on each node
  ├── kube-proxy       → Handles networking rules
  └── Container Runtime (Docker/containerd)
```

### Core Objects
| Object | Purpose |
|---|---|
| **Pod** | Smallest deployable unit; 1+ containers |
| **Deployment** | Manages pod replicas + rolling updates |
| **Service** | Stable network endpoint for pods |
| **ConfigMap** | Inject config as env vars or files |
| **Secret** | Store sensitive data (base64 encoded) |
| **Ingress** | HTTP routing rules to services |
| **Namespace** | Logical cluster isolation |
| **PersistentVolume** | Storage abstraction |

### Deployment Manifest Example
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp-deployment
  namespace: production
spec:
  replicas: 3
  selector:
    matchLabels:
      app: myapp
  template:
    metadata:
      labels:
        app: myapp
    spec:
      containers:
      - name: myapp
        image: myapp:v1
        ports:
        - containerPort: 80
        resources:
          requests:
            memory: "128Mi"
            cpu: "250m"
          limits:
            memory: "256Mi"
            cpu: "500m"
        env:
        - name: DB_CONNECTION
          valueFrom:
            secretKeyRef:
              name: db-secret
              key: connection-string
```

### Service Manifest Example
```yaml
apiVersion: v1
kind: Service
metadata:
  name: myapp-service
spec:
  selector:
    app: myapp
  ports:
  - port: 80
    targetPort: 80
  type: ClusterIP   # or LoadBalancer for external access
```

### Key kubectl Commands
```bash
kubectl get pods -n production            # List pods in namespace
kubectl describe pod <pod-name>           # Detailed pod info
kubectl logs <pod-name> -f                # Stream logs
kubectl exec -it <pod-name> -- /bin/sh    # Shell into pod
kubectl apply -f deployment.yaml          # Apply manifest
kubectl delete -f deployment.yaml         # Delete resources
kubectl scale deployment myapp --replicas=5
kubectl rollout status deployment/myapp
kubectl rollout undo deployment/myapp     # Rollback
kubectl get nodes                         # List cluster nodes
```

### Things to Know Deeply
- Rolling update vs Recreate strategy
- Liveness vs Readiness probes
- Horizontal Pod Autoscaler (HPA)
- Resource requests vs limits
- EKS-specific: node groups, IAM roles for service accounts (IRSA)
- AKS-specific: Azure AD integration, managed identity

---

## 3. Terraform

### What is Terraform?
Terraform is an **Infrastructure as Code (IaC)** tool by HashiCorp. You describe infrastructure in `.tf` files and Terraform provisions it across cloud providers.

### Core Concepts
| Concept | Description |
|---|---|
| **Provider** | Plugin for a cloud (AWS, Azure, GCP) |
| **Resource** | Infrastructure component to create |
| **Variable** | Input values for reusability |
| **Output** | Values to expose after apply |
| **State** | `.tfstate` file tracking real infra |
| **Module** | Reusable group of resources |
| **Plan** | Preview changes before applying |

### Basic Workflow
```bash
terraform init      # Download providers
terraform plan      # Preview changes
terraform apply     # Create/update infra
terraform destroy   # Tear down infra
terraform fmt       # Format code
terraform validate  # Check syntax
```

### Example: Azure App Service via Terraform
```hcl
# provider.tf
terraform {
  required_providers {
    azurerm = {
      source  = "hashicorp/azurerm"
      version = "~> 3.0"
    }
  }
}

provider "azurerm" {
  features {}
}

# main.tf
resource "azurerm_resource_group" "rg" {
  name     = "myapp-rg"
  location = "East US"
}

resource "azurerm_service_plan" "plan" {
  name                = "myapp-plan"
  resource_group_name = azurerm_resource_group.rg.name
  location            = azurerm_resource_group.rg.location
  os_type             = "Linux"
  sku_name            = "B1"
}

resource "azurerm_linux_web_app" "app" {
  name                = "myapp-webapp"
  resource_group_name = azurerm_resource_group.rg.name
  location            = azurerm_resource_group.rg.location
  service_plan_id     = azurerm_service_plan.plan.id

  site_config {
    application_stack {
      dotnet_version = "8.0"
    }
  }
}
```

### Variables Example
```hcl
# variables.tf
variable "location" {
  description = "Azure region"
  type        = string
  default     = "East US"
}

# Usage
resource "azurerm_resource_group" "rg" {
  location = var.location
}
```

### Things to Know Deeply
- Remote state storage (Azure Blob / S3)
- `terraform.tfvars` for environment-specific values
- State locking to prevent concurrent changes
- `depends_on` for explicit dependencies
- Modules: how to call and structure them
- `count` and `for_each` for creating multiple resources

---

## 4. Azure (Core Services)

### Azure App Service
- PaaS for hosting web apps, APIs, background jobs
- Supports .NET, Node, Python, Java, Docker
- Deployment slots (staging → swap to prod with zero downtime)
- Autoscaling based on CPU/memory/schedule

```bash
# Deploy via Azure CLI
az webapp up --name myapp --resource-group myRG --runtime "DOTNET:8.0"
```

### Azure SQL
- Managed SQL Server in the cloud
- DTU vs vCore pricing models
- Geo-replication for disaster recovery
- Elastic pools for multiple databases sharing resources
- Connection string format:
  ```
  Server=tcp:myserver.database.windows.net,1433;
  Initial Catalog=mydb;
  User ID=admin;Password=xxx;
  ```

### Azure Service Bus
*(See Section 8 for deep dive)*

### Azure Active Directory (AD / Entra ID)
- Identity platform: users, groups, app registrations
- OAuth 2.0 / OpenID Connect flows
- App Registration → Client ID + Secret → JWT tokens
- Managed Identity: apps authenticate without storing credentials

### Azure Monitor & Application Insights
- **Azure Monitor**: platform-level metrics (CPU, memory, requests)
- **Application Insights**: APM tool — traces, exceptions, dependency calls, custom events
- Log Analytics workspace for querying with KQL (Kusto Query Language)

```kusto
// KQL Example: Find slow requests
requests
| where duration > 3000
| summarize count() by name, bin(timestamp, 1h)
| order by count_ desc
```

### Key Azure CLI Commands
```bash
az login
az group create --name myRG --location eastus
az webapp list --resource-group myRG
az sql db list --server myserver --resource-group myRG
az monitor metrics list --resource <resource-id> --metric "CpuPercentage"
```

---

## 5. AWS (Core Services)

### EKS (Elastic Kubernetes Service)
- Managed Kubernetes control plane
- Node groups: managed (AWS handles nodes) vs self-managed
- IAM Roles for Service Accounts (IRSA) — pods get AWS permissions without keys
- `eksctl` tool to create/manage clusters

```bash
eksctl create cluster --name my-cluster --region us-east-1 --nodes 3
aws eks update-kubeconfig --name my-cluster --region us-east-1
```

### ECS (Elastic Container Service)
- AWS-native container orchestration (simpler than K8s)
- **Fargate**: serverless containers (no EC2 management)
- **EC2 launch type**: you manage the nodes
- Task Definition = Docker run config; Service = keeps N tasks running

### EC2 (Elastic Compute Cloud)
- Virtual machines in AWS
- Instance types: t3 (burstable), m5 (general), c5 (compute), r5 (memory)
- Security Groups = firewall rules
- Key Pairs for SSH access

### VPC (Virtual Private Cloud)
```
VPC (10.0.0.0/16)
  ├── Public Subnet (10.0.1.0/24)  → Internet Gateway → Load Balancer
  └── Private Subnet (10.0.2.0/24) → NAT Gateway → EC2/RDS
```
- Route Tables: control traffic flow
- NACLs (stateless) vs Security Groups (stateful)

### RDS (Relational Database Service)
- Managed database: MySQL, PostgreSQL, SQL Server, Aurora
- Multi-AZ for high availability (automatic failover)
- Read replicas for read scaling
- Automated backups + point-in-time restore

### Lambda
- Serverless functions, event-driven
- Triggers: API Gateway, S3, SQS, EventBridge
- Cold start problem (especially with .NET/Java)
- Max 15 min execution, 10GB memory

---

## 6. CI/CD with GitHub Actions

### Core Concepts
| Term | Description |
|---|---|
| **Workflow** | YAML file in `.github/workflows/` |
| **Trigger** | Event that starts the workflow (push, PR, schedule) |
| **Job** | Group of steps running on one runner |
| **Step** | Single command or action |
| **Runner** | VM that executes jobs (ubuntu-latest, windows-latest) |
| **Action** | Reusable step from Marketplace |
| **Secret** | Encrypted env var stored in repo settings |

### Example: Build & Deploy .NET to Azure App Service
```yaml
name: Deploy to Azure

on:
  push:
    branches: [main]

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout code
      uses: actions/checkout@v4

    - name: Setup .NET
      uses: actions/setup-dotnet@v3
      with:
        dotnet-version: '8.0.x'

    - name: Restore dependencies
      run: dotnet restore

    - name: Build
      run: dotnet build --configuration Release --no-restore

    - name: Test
      run: dotnet test --no-build --verbosity normal

    - name: Publish
      run: dotnet publish -c Release -o ./publish

    - name: Deploy to Azure App Service
      uses: azure/webapps-deploy@v2
      with:
        app-name: ${{ secrets.AZURE_APP_NAME }}
        publish-profile: ${{ secrets.AZURE_PUBLISH_PROFILE }}
        package: ./publish
```

### Things to Know Deeply
- Caching dependencies (`actions/cache`) to speed up pipelines
- Matrix builds (test across multiple .NET versions)
- Environment protection rules (require approval before prod deploy)
- Self-hosted runners
- Reusable workflows (`workflow_call`)
- OIDC-based auth with Azure (no static secrets)

---

## 7. Microservices Architecture

### What & Why
Split a monolithic app into small, independent services that:
- Own their own database
- Communicate over APIs or messages
- Can be deployed, scaled, and updated independently

### Patterns You Used
**Pub/Sub via Azure Service Bus** (your EPAM work):
```
Producer Service → Service Bus Topic → Consumer Service A
                                     → Consumer Service B
```

**API Gateway Pattern**:
```
Client → API Gateway (auth, rate limit, routing)
           ├── User Service
           ├── Recipe Service
           └── Notification Service
```

### Key Patterns to Study
| Pattern | Purpose |
|---|---|
| **Circuit Breaker** | Stop calling failing services |
| **Retry with Backoff** | Retry transient failures gracefully |
| **Saga** | Distributed transactions across services |
| **Strangler Fig** | Gradually migrate monolith to microservices |
| **Sidecar** | Attach helper containers (logging, proxy) |
| **Health Check** | Expose `/health` endpoint for orchestrators |

### Inter-Service Communication
- **Synchronous**: REST, gRPC (request-response)
- **Asynchronous**: Message queues (Service Bus, SQS, RabbitMQ)

### When to use Async (your real-world experience)
- Decouples failure domains (one service down ≠ others down)
- Enables horizontal scaling of consumers independently
- Better resilience under load spikes

---

## 8. Azure Service Bus (Messaging)

### Core Concepts
| Concept | Description |
|---|---|
| **Queue** | Point-to-point; one sender, one receiver |
| **Topic** | Pub/Sub; one sender, multiple subscribers |
| **Subscription** | Named filter on a topic |
| **Message** | Data payload + metadata |
| **Dead Letter Queue** | Messages that failed processing |
| **Session** | Ordered message delivery per group |

### Producer Example (C#)
```csharp
var client = new ServiceBusClient(connectionString);
var sender = client.CreateSender("notifications-topic");

var message = new ServiceBusMessage(JsonSerializer.Serialize(new {
    UserId = 42,
    Type = "ClaimApproved",
    Timestamp = DateTime.UtcNow
}));

await sender.SendMessageAsync(message);
```

### Consumer Example (C#)
```csharp
var processor = client.CreateProcessor("notifications-topic", "email-subscription");

processor.ProcessMessageAsync += async args => {
    var body = args.Message.Body.ToString();
    var notification = JsonSerializer.Deserialize<NotificationEvent>(body);

    await SendEmailAsync(notification);
    await args.CompleteMessageAsync(args.Message); // Acknowledge
};

processor.ProcessErrorAsync += async args => {
    Console.WriteLine(args.Exception.Message);
    // Message goes to DLQ after max delivery count
};

await processor.StartProcessingAsync();
```

### Things to Know Deeply
- At-least-once vs exactly-once delivery
- Lock duration and message renewal
- Dead Letter Queue: when to use it and how to replay
- Peek-lock vs Receive-and-Delete mode
- Message sessions for ordered processing

---

## 9. .NET / ASP.NET Core

### Middleware Pipeline
```csharp
var builder = WebApplication.CreateBuilder(args);
builder.Services.AddControllers();
builder.Services.AddDbContext<AppDbContext>(opt =>
    opt.UseSqlServer(builder.Configuration.GetConnectionString("Default")));
builder.Services.AddScoped<IOrderService, OrderService>();

var app = builder.Build();
app.UseHttpsRedirection();
app.UseAuthentication();
app.UseAuthorization();
app.MapControllers();
app.Run();
```

### REST API Controller Pattern
```csharp
[ApiController]
[Route("api/[controller]")]
[Authorize]
public class OrdersController : ControllerBase
{
    private readonly IOrderService _orderService;

    public OrdersController(IOrderService orderService)
    {
        _orderService = orderService;
    }

    [HttpGet("{id}")]
    public async Task<IActionResult> GetOrder(int id)
    {
        var order = await _orderService.GetByIdAsync(id);
        return order == null ? NotFound() : Ok(order);
    }

    [HttpPost]
    public async Task<IActionResult> CreateOrder([FromBody] CreateOrderDto dto)
    {
        var result = await _orderService.CreateAsync(dto);
        return CreatedAtAction(nameof(GetOrder), new { id = result.Id }, result);
    }
}
```

### SOLID Principles (Quick Ref)
| Principle | Meaning | Example |
|---|---|---|
| **S** ingle Responsibility | One class, one job | `OrderService` only handles orders |
| **O** pen/Closed | Open for extension, closed for modification | Add new payment method via new class |
| **L** iskov Substitution | Subclass must be substitutable for base | `SqlOrderRepo` works wherever `IOrderRepo` is expected |
| **I** nterface Segregation | Small focused interfaces | `IReadable` + `IWritable` instead of one giant interface |
| **D** ependency Inversion | Depend on abstractions | Inject `IOrderService`, not `OrderService` |

### Things to Know Deeply
- DI lifetimes: Singleton, Scoped, Transient
- Entity Framework Core: migrations, lazy vs eager loading
- Middleware order matters (auth before authorization)
- `IOptions<T>` for typed configuration
- Global exception handling with middleware

---

## 10. JWT Authentication

### How It Works
```
1. Client sends credentials (username + password)
2. Server validates → generates JWT token
3. Client stores token (localStorage / httpOnly cookie)
4. Client sends token in every request: Authorization: Bearer <token>
5. Server validates signature + expiry on each request
```

### JWT Structure
```
Header.Payload.Signature

Header:  { "alg": "HS256", "typ": "JWT" }
Payload: { "sub": "42", "name": "Kushal", "role": "Admin", "exp": 1234567890 }
Signature: HMACSHA256(base64(header) + "." + base64(payload), secret)
```

### JWT in ASP.NET Core
```csharp
// Program.cs
builder.Services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer(options => {
        options.TokenValidationParameters = new TokenValidationParameters {
            ValidateIssuer = true,
            ValidateAudience = true,
            ValidateLifetime = true,
            ValidateIssuerSigningKey = true,
            ValidIssuer = builder.Configuration["Jwt:Issuer"],
            ValidAudience = builder.Configuration["Jwt:Audience"],
            IssuerSigningKey = new SymmetricSecurityKey(
                Encoding.UTF8.GetBytes(builder.Configuration["Jwt:Key"]))
        };
    });

// Token generation
var token = new JwtSecurityToken(
    issuer: "myapp",
    audience: "myapp-users",
    claims: new[] { new Claim(ClaimTypes.NameIdentifier, userId.ToString()),
                    new Claim(ClaimTypes.Role, "Admin") },
    expires: DateTime.UtcNow.AddHours(1),
    signingCredentials: new SigningCredentials(key, SecurityAlgorithms.HmacSha256)
);
return new JwtSecurityTokenHandler().WriteToken(token);
```

### Things to Know Deeply
- Access token vs Refresh token pattern
- Token expiry and rotation strategy
- Storing tokens: httpOnly cookie vs localStorage (XSS risk)
- Role-based vs Claims-based authorization
- OAuth 2.0 flows: Authorization Code, Client Credentials, Implicit

---

## 11. SQL & Azure SQL

### Query Optimization (From Your EPAM Work)
```sql
-- Slow: missing index, implicit conversion
SELECT * FROM Claims WHERE CAST(ClaimDate AS DATE) = '2024-01-01'

-- Fast: SARGable query + index
SELECT ClaimId, Status, Amount FROM Claims
WHERE ClaimDate >= '2024-01-01' AND ClaimDate < '2024-01-02'

-- Check execution plan
SET STATISTICS IO ON
SET STATISTICS TIME ON
-- Run your query here
```

### Stored Procedure Optimization Tips
- Avoid `SELECT *` — select only needed columns
- Use appropriate indexes (covering indexes)
- Avoid functions on indexed columns in WHERE clause
- Use `WITH (NOLOCK)` for read-only dashboards (careful: dirty reads)
- Avoid cursors — use set-based operations instead
- `OPTION (RECOMPILE)` for parameter sniffing issues

### Index Types
| Type | Use Case |
|---|---|
| **Clustered** | Physical sort order of table (1 per table) |
| **Non-Clustered** | Separate index structure |
| **Covering** | Include all columns query needs |
| **Filtered** | Index on subset of rows |
| **Columnstore** | Analytics / data warehouse queries |

### Useful Queries
```sql
-- Find slow queries
SELECT TOP 10 total_elapsed_time/execution_count AS avg_time,
       execution_count, text
FROM sys.dm_exec_query_stats
CROSS APPLY sys.dm_exec_sql_text(sql_handle)
ORDER BY avg_time DESC

-- Check missing indexes
SELECT * FROM sys.dm_db_missing_index_details

-- Table size
SELECT t.name, SUM(a.total_pages) * 8 AS TotalKB
FROM sys.tables t JOIN sys.indexes i ON t.object_id = i.object_id
JOIN sys.partitions p ON i.object_id = p.object_id
JOIN sys.allocation_units a ON p.partition_id = a.container_id
GROUP BY t.name ORDER BY TotalKB DESC
```

---

## 12. Nginx (Reverse Proxy)

### What Nginx Does in Your Setup
```
Client Request
     ↓
  Nginx (port 80/443)
     ↓
  ASP.NET Core App (port 5000)
```

### Basic Config: Reverse Proxy
```nginx
server {
    listen 80;
    server_name myapp.com;

    location / {
        proxy_pass http://localhost:5000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection keep-alive;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_cache_bypass $http_upgrade;
    }
}
```

### SSL with Let's Encrypt (Certbot)
```bash
sudo apt install certbot python3-certbot-nginx
sudo certbot --nginx -d myapp.com
# Auto-renewal
sudo crontab -e
0 12 * * * /usr/bin/certbot renew --quiet
```

### Things to Know Deeply
- Rate limiting: `limit_req_zone`
- Load balancing: `upstream` block with round-robin / least_conn
- Caching static files: `expires` header
- Security headers: HSTS, X-Frame-Options, CSP
- Difference between Nginx and Apache

---

## 13. Monitoring: Azure Monitor & CloudWatch

### Azure Monitor Key Concepts
- **Metrics**: Numeric time-series (CPU %, request count, latency)
- **Logs**: Structured event data (stored in Log Analytics)
- **Alerts**: Trigger actions when thresholds are breached
- **Action Groups**: What to do on alert (email, webhook, Azure Function)
- **Application Insights**: SDK-level APM (traces, exceptions, custom events)

### KQL (Kusto Query Language) Essentials
```kusto
// Exception count by type (last 24h)
exceptions
| where timestamp > ago(24h)
| summarize count() by type
| order by count_ desc

// Request failure rate
requests
| where timestamp > ago(1h)
| summarize total=count(), failed=countif(success==false)
| extend failureRate = (failed * 100.0) / total

// Slow dependencies
dependencies
| where duration > 1000
| summarize avg(duration) by name, type
| order by avg_duration desc
```

### CloudWatch Key Concepts
- **Metrics**: CPU, NetworkIn/Out, custom metrics via SDK
- **Logs**: CloudWatch Logs — log groups and log streams
- **Log Insights**: Query language for logs
- **Alarms**: Trigger on metric threshold → SNS → email/Lambda
- **Dashboards**: Visual panels for metrics

### CloudWatch Log Insights Example
```
fields @timestamp, @message
| filter @message like /ERROR/
| stats count(*) as errorCount by bin(1h)
| sort errorCount desc
```

### Things to Know Deeply
- Setting up custom metrics from your app code
- Alert → Action Group → PagerDuty/Slack integration
- Application Insights distributed tracing (correlation IDs)
- Cost optimization: log retention policies

---

## Study Sequence Recommendation

```
Week 1: Docker → Kubernetes basics → kubectl hands-on
Week 2: Terraform → provision real Azure resources
Week 3: Azure deep dive (App Service, SQL, Service Bus)
Week 4: AWS deep dive (EKS, ECS, VPC, RDS)
Week 5: CI/CD (GitHub Actions pipelines)
Week 6: Microservices patterns + Monitoring
```

## Hands-On Practice Ideas
- [ ] Dockerize your GSS project and run it locally
- [ ] Deploy it to a local K8s cluster (minikube or kind)
- [ ] Write Terraform to provision your Azure App Service
- [ ] Set up a GitHub Actions pipeline for your project
- [ ] Create an Azure Service Bus topic and write a consumer in C#
- [ ] Query Application Insights logs using KQL

---

*Built from Kushal's resume — last updated May 2026*
