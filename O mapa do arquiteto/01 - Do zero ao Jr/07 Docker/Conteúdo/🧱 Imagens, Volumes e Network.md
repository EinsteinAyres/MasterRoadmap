
## 📌 Imagens
Imagem é um template imutável usado para criar containers.

Ex: nginx, postgres

---

## 📌 Volumes

### 🔹 Conceito
Persistência de dados fora do container

### 🔹 Tipos
- Volume (gerenciado pelo Docker)
- Bind Mount (diretório do host)

---

## 📌 Network

### 🔹 Conceito
Permite comunicação entre containers

### 🔹 Tipos
- bridge (default)
- host
- custom network

---

## 🔥 Exemplo

docker network create minha-rede
docker run --network minha-rede

---

## ⚠️ Pontos importantes
- Containers são efêmeros → use volumes
- Use network custom para microservices

---

## 🧠 Flashcards

O que é uma imagem Docker?::Template imutável para criar containers

Para que serve volume?::Persistir dados fora do container

Diferença entre volume e bind mount?::Volume é gerenciado pelo Docker

O que é network bridge?::Rede padrão para containers

---

## 🔗 Conexões
- [[Docker]]
- [[⚙️ Docker Compose]]