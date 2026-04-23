
## 📌 Conceito
Ferramenta para definir e rodar múltiplos containers com um único arquivo.

---

## ⚙️ Exemplo

version: "3"

services:
  app:
    build: .
    ports:
      - "8080:8080"
  db:
    image: postgres
    environment:
      POSTGRES_PASSWORD: 123

---

## 🔥 Comandos

docker-compose up
docker-compose down
docker-compose build

---

## 🎯 Vantagens

- Orquestração local
- Facilita microservices
- Configuração centralizada

---

## ⚠️ Boas práticas

- Separar configs por ambiente
- Usar .env
- Nomear serviços claramente

---

## 🚀 Avançado

- Healthchecks
- Dependências (depends_on)
- Escalabilidade

---

## 🧠 Flashcards

O que é Docker Compose?::Ferramenta para rodar múltiplos containers

O que faz docker-compose up?::Sobe todos os serviços

Para que serve depends_on?::Define dependência entre containers

Qual vantagem do Compose?::Orquestração simples de múltiplos serviços

---

## 🔗 Conexões
- [[Microservices]]
- [[🐳Docker]]
- [[Kubernetes]]