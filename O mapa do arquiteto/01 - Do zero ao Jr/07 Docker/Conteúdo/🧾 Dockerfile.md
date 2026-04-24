# 🧾 Dockerfile

## 📌 Conceito
Arquivo que define como uma imagem Docker será construída.

---

## ⚙️ Estrutura básica

FROM openjdk:17
WORKDIR /app
COPY . .
RUN mvn clean install
CMD ["java", "-jar", "app.jar"]

---

## 🔥 Principais instruções

- FROM → imagem base
- COPY → copia arquivos
- RUN → executa comandos
- CMD → comando inicial
- WORKDIR → diretório de trabalho

---

## ⚠️ Boas práticas

- Usar imagens leves (alpine)
- Minimizar camadas
- Usar .dockerignore
- Cache eficiente

---

## 🚀 Avançado
- Multi-stage build
- Redução de tamanho da imagem
- Segurança de imagem

---

## 🧠 Flashcards

O que é Dockerfile?::Arquivo que define como construir uma imagem

O que faz FROM?::Define imagem base

Diferença entre RUN e CMD?::RUN executa build, CMD executa container

O que é multi-stage build?::Construir imagem em etapas para otimizar tamanho

---

## 🔗 Conexões
- [[Docker]]
- [[CI/CD]]