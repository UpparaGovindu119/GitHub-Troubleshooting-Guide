# Tool-wise Interview Quick Sheet

## 1) Git & GitHub
- **git init** - Initialize a new repository
- **git clone** - Clone a remote repository
- **git status** - Check status of files
- **git add .** - Stage all changes
- **git commit -m "message"** - Commit with message
- **git push origin main** - Push to remote
- **git pull origin main** - Pull from remote
- **git checkout -b feature** - Create and switch branch
- **git merge** - Merge branches
- **git rebase** - Rebase commits
- **git stash** - Stash uncommitted changes
- **git log** - View commit history
- **git diff** - Show differences
- **git reset --soft/hard** - Undo commits
- **Merge conflicts** - Resolve manually, commit merge
- **GitHub PR** - Pull requests for code review
- **Branches** - Isolated development
- **Reviews** - Code quality checks
- **Merge strategies** - Squash, rebase, merge commit

## 2) Linux / Shell
- **ls, cd, pwd** - Navigation
- **mkdir, touch, rm, cp, mv** - File operations
- **cat, grep, find, awk, sed** - Text processing
- **chmod, chown** - Permissions
- **ps, top, kill** - Process management
- **ssh** - Remote access
- **cron jobs** - Scheduled tasks
- **File permissions** - rwx for user, group, others
- **Environment variables** - $PATH, $HOME, etc.
- **Pipes and redirection** - |, >, >>

## 3) Java / Spring Boot
- **OOP** - Inheritance, polymorphism, encapsulation, abstraction
- **Collections** - List, Set, Map, Queue
- **Exception handling** - try-catch-finally, throws
- **Streams** - Filter, map, reduce
- **Multithreading** - Thread, Runnable, ExecutorService
- **Spring Boot annotations** - @SpringBootApplication, @RestController, @Service, @Repository
- **Dependency Injection** - @Autowired, constructor injection
- **REST API** - @GetMapping, @PostMapping, @RequestBody, @PathVariable
- **JPA/Hibernate** - @Entity, @Id, @ManyToOne, @OneToMany
- **Transactions** - @Transactional, rollback
- **Profiles** - application-prod.properties, @Profile
- **Actuator** - /health, /metrics
- **Security** - Authentication, authorization, JWT

## 4) Python
- **Data types** - int, str, list, dict, tuple, set
- **Loops and functions** - for, while, def, lambda
- **OOP** - class, __init__, inheritance
- **File handling** - open(), read(), write(), close()
- **Exception handling** - try-except-finally
- **Lists, dicts, sets** - Mutable collections
- **Comprehensions** - [x for x in range(10)]
- **Modules and packages** - import, __init__.py
- **Virtual environment** - venv, pip
- **Popular libraries** - requests, pandas, json, flask
- **Flask/FastAPI basics** - @app.route(), def handler()

## 5) Databases
- **SQL basics** - SELECT, INSERT, UPDATE, DELETE
- **SELECT with WHERE** - Filter rows
- **JOIN** - INNER, LEFT, RIGHT, FULL
- **GROUP BY and ORDER BY** - Aggregation and sorting
- **CRUD operations** - Create, Read, Update, Delete
- **Indexes** - Speed up queries
- **Normalization** - 1NF, 2NF, 3NF
- **Transactions** - Multiple operations atomically
- **ACID** - Atomicity, Consistency, Isolation, Durability
- **MySQL/PostgreSQL** - Relational databases
- **Stored procedures** - Database-level functions
- **NoSQL basics** - MongoDB, key-value stores

## 6) API & Microservices
- **REST principles** - Stateless, resource-based
- **HTTP methods** - GET, POST, PUT, DELETE, PATCH
- **JSON** - Data format for APIs
- **Request/response lifecycle** - Request → Process → Response
- **Status codes** - 200 (OK), 201 (Created), 400 (Bad Request), 401 (Unauthorized), 404 (Not Found), 500 (Server Error)
- **API versioning** - /v1/users, /v2/users
- **Swagger/OpenAPI** - API documentation
- **Service-to-service communication** - REST, gRPC, message queues
- **Circuit breaker** - Prevent cascading failures
- **Retry pattern** - Exponential backoff
- **Idempotency** - Same request = same result

## 7) Docker
- **docker build** - Build image from Dockerfile
- **docker run** - Run container
- **docker ps** - List running containers
- **docker images** - List images
- **docker logs** - View container logs
- **docker exec** - Execute command in container
- **docker-compose** - Multi-container orchestration
- **Dockerfile best practices** - Minimize layers, use .dockerignore
- **Layer caching** - Reuse layers for faster builds
- **Networking and volumes** - Container communication, persistent storage
- **Container vs VM** - Lightweight vs heavy

## 8) Kubernetes
- **Pods** - Smallest deployable unit
- **ReplicaSets** - Maintain desired number of replicas
- **Deployments** - Manage ReplicaSets
- **Services** - Expose pods to network
- **kubectl commands** - kubectl get, apply, delete, describe
- **Namespaces** - Logical isolation
- **ConfigMaps & Secrets** - Configuration and sensitive data
- **Rolling update** - Gradual pod replacement
- **Health checks** - Liveness and readiness probes
- **Ingress** - External access to services
- **Persistent volumes** - Persistent storage
- **Scaling** - HPA (Horizontal Pod Autoscaler)
- **Helm basics** - Package manager for K8s

## 9) CI/CD
- **Jenkins** - Automation server
- **GitHub Actions** - CI/CD within GitHub
- **GitLab CI** - Built-in CI/CD
- **Pipeline stages** - Build → Test → Deploy
- **Build** - Compile code
- **Test** - Run automated tests
- **Deploy** - Release to production
- **Artifact management** - Store build outputs
- **Environment promotion** - Dev → Staging → Production
- **Blue-green deployment** - Two identical environments
- **Canary deployment** - Gradual rollout to subset of users

## 10) Cloud (AWS/GCP/Azure)
- **EC2** - Virtual machines
- **S3** - Object storage
- **IAM** - Identity and access management
- **VPC** - Virtual private cloud
- **Load balancer** - Distribute traffic
- **RDS** - Managed relational database
- **DynamoDB** - NoSQL database
- **Lambda** - Serverless functions
- **Route53** - DNS service
- **CloudWatch** - Monitoring and logging
- **Billing** - Track costs
- **Security** - Encryption, VPC, security groups

## 11) Testing
- **Unit testing** - Test individual functions
- **Integration testing** - Test component interactions
- **Functional testing** - Test user workflows
- **Mocking** - Simulate dependencies
- **Test coverage** - % of code tested
- **JUnit** - Java testing framework
- **PyTest** - Python testing framework
- **Mockito** - Java mocking library
- **Test pyramid** - Many unit tests, fewer integration tests

## 12) Observability
- **Logging** - Record application events
- **Metrics** - Quantitative measurements
- **Tracing** - Track request flow
- **Prometheus** - Metrics collection
- **Grafana** - Metrics visualization
- **ELK stack** - Elasticsearch, Logstash, Kibana
- **Alerts** - Notify on thresholds
- **Dashboards** - Real-time visibility

## 13) Security
- **Authentication vs Authorization** - Who you are vs what you can do
- **JWT** - JSON Web Token for stateless auth
- **OAuth2** - Delegated authorization
- **Hashing** - One-way encryption
- **Encryption** - Two-way encryption
- **Secure secrets** - Never hardcode passwords
- **CORS** - Cross-Origin Resource Sharing
- **CSRF** - Cross-Site Request Forgery protection
- **OWASP top 10** - SQL Injection, XSS, etc.

## 14) Problem Solving Approach
1. **Clarify requirements** - Ask questions
2. **Identify constraints** - Time, space, resources
3. **Design solution** - High-level approach
4. **Write pseudocode** - Logic outline
5. **Consider edge cases** - Null, empty, large input
6. **Optimize** - Improve time/space complexity
7. **Test thoroughly** - Unit, integration, edge cases

## 15) Common Interview Questions
- "Explain your project architecture"
- "Which design patterns did you use?"
- "How do you handle failure in microservices?"
- "What is the difference between SQL and NoSQL?"
- "How do you optimize slow queries?"
- "How do you debug production issues?"
- "How do you secure APIs?"
- "What is CI/CD and why do we need it?"
- "How do you approach system design?"
- "What are ACID properties?"
- "Explain microservices vs monolithic architecture"
- "How do you handle distributed transactions?"

## 16) Quick Cheat Sheet
| Concept | Explanation |
|---------|-------------|
| REST | Stateless, structured APIs using HTTP methods |
| JWT | Token-based authentication |
| Docker | Package app and dependencies into containers |
| Kubernetes | Orchestrate and manage containers at scale |
| SQL joins | INNER, LEFT, RIGHT, FULL outer joins |
| Index | Speeds up query performance |
| ACID | Atomicity, Consistency, Isolation, Durability |
| CI/CD | Automated build and deployment pipeline |
| Monitoring | Logs + Metrics + Traces |
| Microservices | Independent, loosely coupled services |
| API Gateway | Single entry point for APIs |
| Load Balancer | Distribute traffic across servers |
| Cache | Store frequently accessed data |
| Transaction | Multiple DB operations as one unit |
| Rollback | Undo changes on failure |

## 17) Additional Resources
- GitHub: Version control and collaboration
- Stack Overflow: Troubleshooting common issues
- Documentation: Official language/framework docs
- Medium/Dev.to: Technical articles
- LeetCode/HackerRank: Coding practice
- System Design Primer: Architecture patterns
- Designing Data-Intensive Applications: Advanced concepts
