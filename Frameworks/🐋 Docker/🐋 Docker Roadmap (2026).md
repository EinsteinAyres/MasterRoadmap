

# 🧠 ROADMAP ESTRATÉGICO – DOCKER (2026+)

---

# 🧩 PARTE 1 – NÚCLEO QUE GERA VALOR IMEDIATO

├─ Fundamentos de Containers  
├─ Imagens e Containers na prática  
└─ Dockerfile profissional

---

# ⚡ PARTE 2 – AMBIENTES REAIS DE BACKEND

├─ Docker Compose  
├─ Redes entre serviços  
├─ Volumes e persistência  
└─ API + Banco + Redis + RabbitMQ

---

# ☁️ PARTE 3 – DEVOPS E ENTREGA CONTÍNUA

├─ Build otimizado de imagens  
├─ Registry e versionamento  
└─ CI/CD com build, scan e push

---

# 📊 PARTE 4 – QUALIDADE, SEGURANÇA E OBSERVABILIDADE

├─ Logs e troubleshooting  
├─ Healthcheck + restart policy  
├─ Scan de vulnerabilidades  
└─ Resource limits

---

# 🚀 PARTE 5 – DIFERENCIAIS DE ALTO IMPACTO

├─ Multi-stage build  
├─ Imagens slim / distroless  
├─ Docker para Java/Spring  
├─ Docker para Python/FastAPI  
├─ Produção cloud-ready  
└─ Ponte para Kubernetes

---

# 📘 SUMÁRIO ESTRATÉGICO – DOCKER

---

## 🔰 1. FUNDAMENTOS DO DOCKER

> Objetivo: não ser “dev que só roda docker compose up”

### 1.1 Conceitos essenciais

- O que é Docker
- Containers vs Máquinas Virtuais
- Docker Engine
- Docker CLI
- Docker Desktop
- Imagem vs Container
- Registry
- Docker Hub

---

### 1.2 Comandos básicos

- `docker run`
- `docker ps`
- `docker stop`
- `docker rm`
- `docker images`
- `docker rmi`
- `docker logs`
- `docker exec -it`
- `docker inspect`

---

## ⚙️ 2. IMAGENS E CONTAINERS

> Aqui começa o uso real no backend

### 2.1 Imagens

- Tags
- Layers
- Imagens oficiais
- Imagens Alpine/Slim
- Digest
- Pull e Push

---

### 2.2 Containers

- Ciclo de vida do container
- Port mapping
- Variáveis de ambiente
- Restart policy
- Healthcheck
- Logs
- Execução em background

---

## 🧱 3. DOCKERFILE — CORE DO DOCKER

### 3.1 Instruções principais

- `FROM`
- `WORKDIR`
- `COPY`
- `RUN`
- `CMD`
- `ENTRYPOINT`
- `EXPOSE`
- `ENV`
- `ARG`
- `USER`

---

### 3.2 Boas práticas

- Usar `.dockerignore`
- Evitar imagem `latest`
- Rodar como usuário não-root
- Reduzir camadas desnecessárias
- Separar build e runtime
- Fixar versões
- Usar imagens leves

---

### 3.3 Muito cobrado

- `CMD` vs `ENTRYPOINT`
- `COPY` vs `ADD`
- `ARG` vs `ENV`
- Build cache
- Multi-stage build

---

## 🌐 4. REDES DOCKER

### 4.1 Conceitos

- Bridge network
- Host network
- None network
- DNS interno do Docker
- Comunicação entre containers
- Exposição de portas

---

### 4.2 Prática real

- API acessando banco pelo nome do serviço
- Backend + PostgreSQL
- Backend + Redis
- Backend + RabbitMQ
- Backend + Nginx

---

## 💾 5. VOLUMES E PERSISTÊNCIA

### 5.1 Conceitos

- Named volumes
- Bind mounts
- Anonymous volumes
- Persistência de dados
- Diferença entre volume e filesystem do container

---

### 5.2 Cenários reais

- Persistir dados do PostgreSQL
- Persistir dados do MySQL
- Persistir dados do Redis
- Compartilhar arquivos entre host e container
- Evitar perda de dados ao remover containers

---

## 🧩 6. DOCKER COMPOSE

> Essencial para ambiente local profissional

### 6.1 Base

- `compose.yml`
- Services
- Networks
- Volumes
- Environment variables
- `depends_on`

---

### 6.2 Comandos principais

- `docker compose up`
- `docker compose up -d`
- `docker compose down`
- `docker compose logs`
- `docker compose exec`
- `docker compose build`
- `docker compose ps`

---

### 6.3 Ambientes reais

- API + PostgreSQL
- API + Redis
- API + RabbitMQ
- API + Nginx
- Stack completa de desenvolvimento

---

## ☕ 7. DOCKER PARA JAVA / SPRING BOOT

### 7.1 Core

- Dockerfile para Spring Boot
- Build com Maven
- Build com Gradle
- JAR em container
- Profiles do Spring
- Variáveis de ambiente
- Conexão com banco via Compose

---

### 7.2 Avançado

- Multi-stage build
- Imagem final com JRE
- Buildpacks
- Distroless Java
- JVM tuning em container
- Limites de memória e CPU

---

## 🐍 8. DOCKER PARA PYTHON / FASTAPI

### 8.1 Core

- Dockerfile para FastAPI
- Uvicorn
- Gunicorn + Uvicorn workers
- `requirements.txt`
- `pyproject.toml`
- Poetry
- uv
- Variáveis de ambiente
- Alembic migrations

---

### 8.2 Avançado

- Imagem slim
- Multi-stage build
- Cache de dependências
- Rodar como usuário não-root
- Otimizar startup
- Evitar imagem gigante

---

## 🔐 9. SEGURANÇA EM DOCKER

> Muito importante para mercado e entrevistas

### 9.1 Boas práticas

- Não usar usuário root
- Não expor portas desnecessárias
- Não colocar secrets no Dockerfile
- Fixar versões de imagens
- Usar imagens oficiais
- Reduzir superfície de ataque
- Princípio do menor privilégio

---

### 9.2 Ferramentas

- Docker Scout
- Trivy
- Snyk
- Grype

---

## 📦 10. BUILD, REGISTRY E VERSIONAMENTO

### 10.1 Build

- Build local
- Build com tag
- Build com cache
- BuildKit
- Multi-stage build

---

### 10.2 Registry

- Docker Hub
- GitHub Container Registry
- Amazon ECR
- Imagens públicas e privadas

---

### 10.3 Estratégia de tags

- `app:dev`
- `app:staging`
- `app:prod`
- `app:1.0.0`
- `app:sha-do-commit`

---

## 🛠️ 11. LOGS, DEBUG E TROUBLESHOOTING

### 11.1 Comandos úteis

- `docker logs`
- `docker inspect`
- `docker stats`
- `docker top`
- `docker events`
- `docker compose logs`

---

### 11.2 Problemas reais

- Container não sobe
- Porta ocupada
- API não conecta no banco
- Volume não persiste
- Variável de ambiente errada
- Container reiniciando em loop
- Imagem muito grande
- Erro de permissão

---

## ⚡ 12. PERFORMANCE E OTIMIZAÇÃO

### 12.1 Imagens

- Reduzir tamanho da imagem
- Usar `.dockerignore`
- Usar imagem slim/distroless
- Melhorar cache de build
- Remover dependências desnecessárias

---

### 12.2 Runtime

- Limites de CPU
- Limites de memória
- Startup time
- Healthcheck
- Workers
- Logs eficientes

---

## 🔄 13. DOCKER EM CI/CD

### 13.1 Pipeline básico

- Checkout
- Testes
- Build da imagem
- Scan de vulnerabilidades
- Push para registry
- Deploy

---

### 13.2 Ferramentas

- GitHub Actions
- GitLab CI
- Jenkins
- Docker Scout
- Trivy

---

## ☁️ 14. DOCKER EM PRODUÇÃO

### 14.1 Conceitos

- Diferença entre dev e produção
- Configuração externa
- Secrets externos
- Logging driver
- Resource limits
- Restart policy
- Healthcheck
- Rollback por imagem

---

### 14.2 Importante

- Docker Compose não substitui orquestrador completo
- Docker prepara o caminho para Kubernetes
- Produção exige observabilidade, segurança e rollback

---

## ☸️ 15. DOCKER + KUBERNETES

> Docker é a base mental para entender Kubernetes

- Container image
- Pod
- Deployment
- Service
- ConfigMap
- Secret
- Ingress
- Readiness probe
- Liveness probe
- Horizontal scaling

---

## 🚀 16. PROJETOS PRÁTICOS

### Projeto 1 — Fundamentos

- Rodar Nginx em container
- Mapear porta
- Criar volume
- Acessar logs
- Entrar no container com `exec`

---

### Projeto 2 — Backend + Banco

- API Spring Boot ou FastAPI
- PostgreSQL
- Docker Compose
- Variáveis de ambiente
- Volume persistente

---

### Projeto 3 — Stack Backend Completa

- API
- PostgreSQL
- Redis
- RabbitMQ
- Nginx
- Healthcheck
- Logs

---

### Projeto 4 — Pipeline Profissional

- Dockerfile otimizado
- Build no GitHub Actions
- Scan com Trivy
- Push para GitHub Container Registry

---

# 🧭 ORDEM IDEAL DE ESTUDO

1. Fundamentos do Docker
2. Comandos básicos
3. Imagens e containers
4. Dockerfile
5. Volumes
6. Redes
7. Docker Compose
8. Docker para Java/Spring
9. Docker para Python/FastAPI
10. Segurança
11. Build e Registry
12. Logs e troubleshooting
13. Otimização
14. CI/CD
15. Produção
16. Kubernetes

---

# 🎯 VISÃO DE MERCADO

Se você dominar:

- Dockerfile bem feito
- Docker Compose
- Redes
- Volumes
- Variáveis de ambiente
- Debug de containers
- Build de imagens
- Integração com banco/cache

👉 Você já está em um nível **Pleno forte para backend moderno**.

Se adicionar:

- Multi-stage build
- Segurança de imagens
- Scan de vulnerabilidades
- CI/CD com registry
- Observabilidade
- Produção cloud-ready
- Kubernetes

👉 Você entra em um nível **Senior-ready / DevOps-aware**, muito valorizado em times backend profissionais.