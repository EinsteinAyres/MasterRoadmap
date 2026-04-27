
# ☁️ ROADMAP CLOUD COMPUTING — FOCO MERCADO

## 🧠 Visão geral (o jogo real)

Cloud não é:

> “saber serviços”

Cloud é:

> **arquitetar sistemas distribuídos confiáveis, escaláveis e baratos**

---

# 🧩 FASE 1 — FUNDAMENTOS (obrigatório)

## 🔹 Conceitos base

- IaaS / PaaS / SaaS
- regiões / AZs
- alta disponibilidade
- escalabilidade horizontal vs vertical
- latência e throughput
- CAP theorem
- fault tolerance
- stateless vs stateful
- shared responsibility model

---

## 🔹 Redes (CRÍTICO)

Sem isso você não evolui.

- VPC
- Subnets (public/private)
- CIDR
- NAT Gateway
- Internet Gateway
- Security Groups
- NACL
- DNS (Route53)
- Load Balancer

---

## 🔹 Prática (fase 1)

👉 Crie:

- 1 VPC
- 2 subnets (public + private)
- 1 EC2
- 1 Load Balancer

---

# ⚙️ FASE 2 — COMPUTE E EXECUÇÃO

## 🔹 Compute

- EC2 (fundamental)
- Auto Scaling
- AMI
- Spot Instances

## 🔹 Serverless

- Lambda
- Event-driven architecture

## 🔹 Containers

- Docker
- ECS
- EKS (Kubernetes)

---

## 🔥 Prática

### Projeto:

API Java Spring Boot  
↓  
Docker  
↓  
Deploy em ECS  
↓  
Load Balancer

---

# 🗄️ FASE 3 — STORAGE & DATABASES

## 🔹 Storage

- S3 (o mais importante da AWS)
- versionamento
- lifecycle
- storage classes

## 🔹 Bancos

- RDS (PostgreSQL/MySQL)
- DynamoDB
- ElastiCache (Redis)

---

## 🔥 Prática

👉 Pipeline:

API → grava no S3 → processa → salva no RDS

---

# 🔄 FASE 4 — INTEGRAÇÃO E EVENTOS

## 🔹 Mensageria

- SQS
- SNS
- EventBridge

## 🔹 Padrões

- pub/sub
- fan-out
- dead letter queue
- retry/backoff
- eventual consistency

---

## 🔥 Projeto

API → SQS → Worker → Banco

---

# 🧱 FASE 5 — INFRA COMO CÓDIGO (MANDATÓRIO)

## 🔹 Ferramentas

- Terraform (principal)
- CloudFormation (conceito)

## 🔹 Conceitos

- idempotência
- estado (state)
- módulos
- variáveis
- ambientes (dev/prod)

---

## 🔥 Prática

👉 Criar toda infra via Terraform:

VPC + EC2 + RDS + S3

---

# 🔐 FASE 6 — SEGURANÇA (o que te diferencia)

## 🔹 IAM

- users
- roles
- policies
- least privilege

## 🔹 Segurança real

- secrets manager
- encryption (KMS)
- TLS
- WAF
- Shield
- auditoria (CloudTrail)

---

## 🔥 Prática

👉 Criar:

- roles específicas
- acesso mínimo necessário
- API protegida

---

# 📊 FASE 7 — OBSERVABILIDADE

## 🔹 Logs

- CloudWatch Logs
- structured logging

## 🔹 Métricas

- CPU, memória, latência

## 🔹 Tracing

- X-Ray

## 🔹 Alertas

- alarmes
- dashboards

---

## 🔥 Prática

👉 Criar:

- dashboard de métricas
- alertas de erro

---

# 🚀 FASE 8 — CI/CD

## 🔹 Ferramentas

- GitHub Actions
- GitLab CI
- AWS CodePipeline

## 🔹 Conceitos

- build
- test
- deploy
- rollback

---

## 🔥 Projeto

Push no Git → build → Docker → deploy automático ECS

---

# 🧠 FASE 9 — ARQUITETURA DE SISTEMAS

## 🔹 Arquiteturas

- microservices
- event-driven
- serverless
- hexagonal (você já usa 👀)

## 🔹 Boas práticas

- desacoplamento
- resiliência
- circuit breaker
- retry
- timeout

---

# 💰 FASE 10 — CUSTO (o que o mercado cobra MUITO)

## 🔹 Otimização

- dimensionamento
- autoscaling
- reserved instances
- spot instances
- storage lifecycle

---

## 🔥 Prática

👉 Simular custo:

- EC2 vs Lambda
- S3 vs RDS

---

# 🧪 FASE 11 — PROJETO REAL (PORTFÓLIO)

## 💥 Plataforma completa

Frontend  
↓  
API Gateway  
↓  
Lambda / ECS  
↓  
SQS  
↓  
Worker  
↓  
RDS  
↓  
S3  
↓  
CloudWatch

---

## 📌 Diferenciais do projeto

✔ Terraform  
✔ CI/CD  
✔ Logs + métricas  
✔ Alertas  
✔ Segurança  
✔ Documentação C4  
✔ README profissional

---

# 🧩 STACK RECOMENDADA (pra você)

Com base no seu perfil:

AWS  
Terraform  
Docker  
ECS (ou EKS depois)  
S3  
RDS (PostgreSQL)  
SQS  
Lambda  
CloudWatch  
GitHub Actions  
Java + Spring Boot

---

# 🎯 TRILHA POR SEMANA (resumo)

## Semana 1–2

- VPC + EC2 + S3

## Semana 3–4

- Docker + ECS

## Semana 5–6

- RDS + SQS

## Semana 7–8

- Terraform

## Semana 9–10

- CI/CD

## Semana 11–12

- Projeto completo

---

# 🧠 RESUMO (mentalidade senior)

Cloud NÃO é:  
❌ saber clicar no console

Cloud É:  
✔ projetar sistemas resilientes  
✔ reduzir custo  
✔ escalar sem quebrar  
✔ automatizar tudo

---

# 🚀 Próximo nível (posso montar pra você)

Posso criar:

✔ roadmap **Cloud + Engenharia de Dados integrado**  
✔ projeto completo com arquitetura real (nível empresa)  
✔ plano focado em vaga AWS Pleno/Senior