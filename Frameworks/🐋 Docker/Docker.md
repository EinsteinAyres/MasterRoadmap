

## 📌 1. Fundamentos do Docker — BASE OBRIGATÓRIA

### 🔹 Conceitos

- [ ]  O que é Docker
- [ ]  Por que usar Docker
- [ ]  Containers vs Máquinas Virtuais
- [ ]  Imagens vs Containers
- [ ]  Docker Engine
- [ ]  Docker CLI
- [ ]  Docker Desktop
- [ ]  Registry
- [ ]  Docker Hub

---

## 📌 2. Primeiros Comandos Docker

- [ ]  `docker --version`
- [ ]  `docker pull`
- [ ]  `docker images`
- [ ]  `docker run`
- [ ]  `docker ps`
- [ ]  `docker ps -a`
- [ ]  `docker stop`
- [ ]  `docker start`
- [ ]  `docker restart`
- [ ]  `docker rm`
- [ ]  `docker rmi`
- [ ]  `docker logs`
- [ ]  `docker exec -it`

🔥 Enriquecimento:

- [ ]  Diferença entre container rodando e container parado
- [ ]  Como acessar o terminal de um container
- [ ]  Como inspecionar containers com `docker inspect`

---

## 📌 3. Imagens Docker

### 🔹 Conceitos

- [ ]  O que é uma imagem
- [ ]  Camadas de imagem
- [ ]  Tags
- [ ]  Imagens oficiais
- [ ]  Imagens Alpine
- [ ]  Digest
- [ ]  Pull policy

### 🔹 Comandos

- [ ]  `docker build`
- [ ]  `docker tag`
- [ ]  `docker push`
- [ ]  `docker history`
- [ ]  `docker image prune`

🔥 Avançado:

- [ ]  Otimização de tamanho de imagem
- [ ]  Imagens distroless
- [ ]  Multi-stage build

---

## 📌 4. Dockerfile — CORE DO DOCKER

### 🔹 Instruções Essenciais

- [ ]  `FROM`
- [ ]  `WORKDIR`
- [ ]  `COPY`
- [ ]  `ADD`
- [ ]  `RUN`
- [ ]  `CMD`
- [ ]  `ENTRYPOINT`
- [ ]  `EXPOSE`
- [ ]  `ENV`
- [ ]  `ARG`
- [ ]  `USER`

### 🔹 Boas Práticas

- [ ]  Ordem correta das camadas
- [ ]  Cache de build
- [ ]  `.dockerignore`
- [ ]  Rodar como usuário não-root
- [ ]  Separar build e runtime
- [ ]  Evitar instalar dependências desnecessárias

🔥 Muito cobrado:

- [ ]  Diferença entre `CMD` e `ENTRYPOINT`
- [ ]  Diferença entre `COPY` e `ADD`
- [ ]  Diferença entre `ARG` e `ENV`

---

## 📌 5. Containers na Prática

- [ ]  Rodar aplicação backend
- [ ]  Rodar banco de dados em container
- [ ]  Rodar Redis em container
- [ ]  Rodar Nginx em container
- [ ]  Mapear portas
- [ ]  Passar variáveis de ambiente
- [ ]  Montar volumes
- [ ]  Executar comandos dentro do container
- [ ]  Debugar container quebrado

🔥 Enriquecimento:

- [ ]  Healthcheck
- [ ]  Restart policy
- [ ]  Logs em tempo real
- [ ]  Container ephemeral

---

## 📌 6. Volumes e Persistência

### 🔹 Conceitos

- [ ]  O que são volumes
- [ ]  Bind mounts
- [ ]  Named volumes
- [ ]  Anonymous volumes
- [ ]  Persistência de dados
- [ ]  Diferença entre volume e filesystem do container

### 🔹 Comandos

- [ ]  `docker volume ls`
- [ ]  `docker volume create`
- [ ]  `docker volume inspect`
- [ ]  `docker volume rm`
- [ ]  `docker volume prune`

🔥 Prática real:

- [ ]  Persistir dados do PostgreSQL
- [ ]  Persistir dados do MySQL
- [ ]  Persistir dados do Redis
- [ ]  Compartilhar arquivos entre host e container

---

## 📌 7. Redes Docker

### 🔹 Conceitos

- [ ]  O que é Docker Network
- [ ]  Bridge network
- [ ]  Host network
- [ ]  None network
- [ ]  Comunicação entre containers
- [ ]  DNS interno do Docker
- [ ]  Exposição de portas

### 🔹 Comandos

- [ ]  `docker network ls`
- [ ]  `docker network create`
- [ ]  `docker network inspect`
- [ ]  `docker network rm`

🔥 Muito cobrado:

- [ ]  Diferença entre `ports` e `expose`
- [ ]  Como uma API acessa um banco por nome de serviço
- [ ]  Comunicação backend + database + redis

---

## 📌 8. Docker Compose — CORE PARA DEV

### 🔹 Conceitos

- [ ]  O que é Docker Compose
- [ ]  Arquivo `compose.yml`
- [ ]  Services
- [ ]  Networks
- [ ]  Volumes
- [ ]  Environment variables
- [ ]  Depends_on

### 🔹 Comandos

- [ ]  `docker compose up`
- [ ]  `docker compose up -d`
- [ ]  `docker compose down`
- [ ]  `docker compose logs`
- [ ]  `docker compose ps`
- [ ]  `docker compose exec`
- [ ]  `docker compose build`

🔥 Prática real:

- [ ]  API + PostgreSQL
- [ ]  API + Redis
- [ ]  API + RabbitMQ
- [ ]  API + Nginx
- [ ]  Ambiente completo de desenvolvimento

---

## 📌 9. Docker para Backend Java/Spring

- [ ]  Dockerfile para Spring Boot
- [ ]  Multi-stage build com Maven
- [ ]  Multi-stage build com Gradle
- [ ]  Imagem final com JRE
- [ ]  Variáveis de ambiente
- [ ]  Profiles do Spring
- [ ]  Conexão com PostgreSQL via Compose
- [ ]  Healthcheck da aplicação
- [ ]  Otimização de imagem Java

🔥 Avançado:

- [ ]  Buildpacks
- [ ]  Distroless Java
- [ ]  JVM tuning em container
- [ ]  Limites de memória e CPU

---

## 📌 10. Docker para Backend Python/FastAPI

- [ ]  Dockerfile para FastAPI
- [ ]  Ambiente com `venv`
- [ ]  `requirements.txt`
- [ ]  `pyproject.toml`
- [ ]  Poetry
- [ ]  uv
- [ ]  Uvicorn
- [ ]  Gunicorn + Uvicorn workers
- [ ]  Variáveis de ambiente
- [ ]  Conexão com PostgreSQL via Compose
- [ ]  Alembic migrations

🔥 Avançado:

- [ ]  Imagem slim
- [ ]  Multi-stage build em Python
- [ ]  Cache de dependências
- [ ]  Rodar como usuário não-root

---

## 📌 11. Segurança em Docker

- [ ]  Rodar container como não-root
- [ ]  Não expor portas desnecessárias
- [ ]  Usar imagens oficiais/confiáveis
- [ ]  Fixar versões de imagens
- [ ]  Evitar secrets no Dockerfile
- [ ]  Variáveis de ambiente com cuidado
- [ ]  Scan de vulnerabilidades
- [ ]  Princípio do menor privilégio

Ferramentas:

- [ ]  Docker Scout
- [ ]  Trivy
- [ ]  Snyk
- [ ]  Grype

🔥 Muito cobrado:

- [ ]  Por que não usar `latest` em produção
- [ ]  Como reduzir superfície de ataque
- [ ]  Como lidar com secrets

---

## 📌 12. Build, Registry e Versionamento

- [ ]  Build local
- [ ]  Tags semânticas
- [ ]  Versionamento de imagens
- [ ]  Docker Hub
- [ ]  GitHub Container Registry
- [ ]  Amazon ECR
- [ ]  Push de imagem
- [ ]  Pull de imagem
- [ ]  Imagens privadas
- [ ]  Estratégia de tags

🔥 Prática real:

- [ ]  `app:dev`
- [ ]  `app:staging`
- [ ]  `app:prod`
- [ ]  `app:1.0.0`
- [ ]  `app:sha-do-commit`

---

## 📌 13. Logs, Debug e Troubleshooting

- [ ]  `docker logs`
- [ ]  `docker inspect`
- [ ]  `docker stats`
- [ ]  `docker top`
- [ ]  `docker events`
- [ ]  Debug de porta ocupada
- [ ]  Debug de variável de ambiente
- [ ]  Debug de rede
- [ ]  Debug de volume
- [ ]  Debug de container reiniciando
- [ ]  Debug de imagem muito grande

🔥 Prática real:

- [ ]  Container não sobe
- [ ]  API não conecta no banco
- [ ]  Porta não responde
- [ ]  Volume não persiste
- [ ]  Compose sobe fora de ordem

---

## 📌 14. Performance e Otimização

- [ ]  Reduzir tamanho da imagem
- [ ]  Melhorar cache de build
- [ ]  Multi-stage build
- [ ]  `.dockerignore`
- [ ]  Imagens slim/alpine/distroless
- [ ]  Limites de CPU
- [ ]  Limites de memória
- [ ]  Startup time
- [ ]  Build time
- [ ]  Layer caching

🔥 Diferencial:

- [ ]  Profiling dentro do container
- [ ]  Tuning JVM em container
- [ ]  Workers Python em container
- [ ]  BuildKit

---

## 📌 15. Docker em CI/CD

- [ ]  Build automático de imagem
- [ ]  Testes dentro do pipeline
- [ ]  Scan de vulnerabilidades
- [ ]  Push para registry
- [ ]  Deploy por tag
- [ ]  Rollback por imagem
- [ ]  GitHub Actions
- [ ]  GitLab CI
- [ ]  Jenkins

🔥 Pipeline ideal:

- [ ]  Checkout
- [ ]  Test
- [ ]  Build image
- [ ]  Scan image
- [ ]  Push image
- [ ]  Deploy

---

## 📌 16. Docker em Produção

- [ ]  Diferença entre dev e produção
- [ ]  Configuração externa
- [ ]  Healthcheck
- [ ]  Restart policy
- [ ]  Logging driver
- [ ]  Resource limits
- [ ]  Read-only filesystem
- [ ]  Secrets externos
- [ ]  Monitoramento
- [ ]  Estratégia de rollback

🔥 Importante:

- [ ]  Docker sozinho não é orquestrador completo
- [ ]  Quando usar Docker Compose em produção
- [ ]  Quando migrar para Kubernetes/ECS

---

## 📌 17. Docker + Kubernetes

- [ ]  Por que Docker prepara para Kubernetes
- [ ]  Container image
- [ ]  Pod
- [ ]  Deployment
- [ ]  Service
- [ ]  ConfigMap
- [ ]  Secret
- [ ]  Ingress
- [ ]  Readiness probe
- [ ]  Liveness probe

🔥 Ponte estratégica:

- [ ]  Dockerfile bem feito facilita deploy em Kubernetes
- [ ]  Compose ajuda no dev, Kubernetes entra na orquestração

---

## 📌 18. Projetos Práticos

### Projeto 1 — Básico

- [ ]  Rodar Nginx em container
- [ ]  Criar página HTML simples
- [ ]  Mapear porta local

### Projeto 2 — Backend + Banco

- [ ]  API Java/Spring ou Python/FastAPI
- [ ]  PostgreSQL
- [ ]  Docker Compose
- [ ]  Variáveis de ambiente
- [ ]  Volume persistente

### Projeto 3 — Stack Completa

- [ ]  API
- [ ]  PostgreSQL
- [ ]  Redis
- [ ]  RabbitMQ
- [ ]  Nginx
- [ ]  Observabilidade básica

### Projeto 4 — Pipeline

- [ ]  Criar Dockerfile otimizado
- [ ]  Build no GitHub Actions
- [ ]  Scan com Trivy
- [ ]  Push para GitHub Container Registry

---

# 🧭 Ordem Recomendada de Estudo

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

# 🎯 Visão de Mercado

Se você dominar:

- Dockerfile bem estruturado
- Docker Compose
- Redes e volumes
- Build de imagens
- Variáveis de ambiente
- Debug de containers
- Integração com banco/cache
- Segurança básica

👉 Você já fica acima de muitos devs backend.

Se adicionar:

- Multi-stage build
- Scan de vulnerabilidades
- CI/CD com imagens
- Observabilidade
- Resource limits
- Produção
- Kubernetes

👉 Você entra em um nível **Pleno forte / Senior-ready** para times backend modernos.