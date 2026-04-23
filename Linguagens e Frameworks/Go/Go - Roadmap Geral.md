
# Golang Roadmap (Backend & High Performance)

---

## 📌 1. Fundamentos da Linguagem

- [ ]  História do Go
- [ ]  Por que usar Go (performance, concorrência)
- [ ]  Setup do ambiente
- [ ]  go run / go build

---

## 📌 2. Sintaxe Básica

- [ ]  Variáveis (`var`, `:=`)
- [ ]  Constantes (`const`, `iota`)
- [ ]  Tipos de dados
    - [ ]  int, float, bool
    - [ ]  string
    - [ ]  rune
- [ ]  Zero values
- [ ]  Type conversion

---

## 📌 3. Estruturas de Controle

- [ ]  if / else
- [ ]  switch
- [ ]  for (único loop do Go)
- [ ]  for range
- [ ]  break / continue

---

## 📌 4. Tipos Compostos

(Parte central do diagrama )

- [ ]  Arrays
- [ ]  Slices (🔥 MUITO IMPORTANTE)
- [ ]  Maps
- [ ]  Structs
- [ ]  JSON tags

🔥 Enriquecimento:

- [ ]  slice internals (capacidade e crescimento)
- [ ]  diferença slice vs array

---

## 📌 5. Funções

- [ ]  Funções básicas
- [ ]  Múltiplos retornos
- [ ]  Funções variádicas
- [ ]  Closures
- [ ]  Funções anônimas

---

## 📌 6. Ponteiros & Memória

- [ ]  Ponteiros
- [ ]  Passagem por valor
- [ ]  Garbage Collection

🔥 Avançado:

- [ ]  Escape Analysis (muito forte em entrevistas)

---

## 📌 7. Métodos & Interfaces

- [ ]  Métodos vs funções
- [ ]  Interfaces
- [ ]  Type assertions
- [ ]  Type switch

🔥 DIFERENCIAL:

- [ ]  Interface implícita (Go style)
- [ ]  Composição vs herança

---

## 📌 8. Generics (Go moderno)

- [ ]  Funções genéricas
- [ ]  Tipos genéricos
- [ ]  Constraints

---

## 📌 9. Tratamento de Erros (🔥 ESSENCIAL)

(Baseado no bloco error handling )

- [ ]  error interface
- [ ]  errors.New / fmt.Errorf
- [ ]  Wrapping errors
- [ ]  panic / recover

🔥 Avançado:

- [ ]  padrões de erro em produção

---

## 📌 10. Organização de Código

- [ ]  Packages
- [ ]  go mod
- [ ]  Dependências
- [ ]  Estrutura de projeto

---

## 📌 11. Concorrência (🔥 PRINCIPAL DIFERENCIAL DO GO)

(Baseado na parte de concurrency )

- [ ]  Goroutines
- [ ]  Channels
- [ ]  Buffered vs Unbuffered
- [ ]  select

### 🔥 Padrões

- [ ]  Worker pools
- [ ]  Fan-in / Fan-out
- [ ]  Pipelines

### 🔥 Sync

- [ ]  Mutex
- [ ]  WaitGroups

### 🔥 Context

- [ ]  Cancelamento
- [ ]  Timeout

---

## 📌 12. Standard Library (🔥 FORTE DO GO)

- [ ]  net/http
- [ ]  encoding/json
- [ ]  io / os
- [ ]  time
- [ ]  regexp

---

## 📌 13. Desenvolvimento Web

(Baseado na seção Web )

- [ ]  HTTP server (net/http)
- [ ]  APIs REST

### Frameworks (opcional)

- [ ]  Gin
- [ ]  Echo
- [ ]  Fiber

---

## 📌 14. Banco de Dados

- [ ]  database/sql
- [ ]  pgx
- [ ]  GORM

🔥 Avançado:

- [ ]  conexão pooling
- [ ]  transações

---

## 📌 15. Comunicação Avançada

- [ ]  gRPC
- [ ]  Protocol Buffers

---

## 📌 16. Logging

- [ ]  log package
- [ ]  Zap / Zerolog

---

## 📌 17. Testes

(Baseado na parte testing )

- [ ]  testing package
- [ ]  Table-driven tests
- [ ]  Benchmarks
- [ ]  Coverage

---

## 📌 18. Performance & Debugging

- [ ]  pprof
- [ ]  trace
- [ ]  race detector

🔥 MUITO FORTE:

- [ ]  profiling de CPU e memória

---

## 📌 19. Qualidade de Código

- [ ]  go vet
- [ ]  linters (golangci-lint)
- [ ]  staticcheck

---

## 📌 20. Build & Toolchain

- [ ]  go build
- [ ]  go install
- [ ]  go generate

---

## 📌 21. Deploy & Produção

- [ ]  Cross compilation
- [ ]  Build executáveis
- [ ]  Docker

---

## 📌 22. Tópicos Avançados (🔥 SENIOR)

- [ ]  Reflection
- [ ]  Unsafe package
- [ ]  CGO
- [ ]  Plugins

---

## 📌 23. DevOps & Escala

- [ ]  Docker
- [ ]  Kubernetes
- [ ]  Observabilidade