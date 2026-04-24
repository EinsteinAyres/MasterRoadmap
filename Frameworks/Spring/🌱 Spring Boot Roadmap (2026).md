
## 📌 1. Fundamentos do Spring (BASE OBRIGATÓRIA)

### 🔹 Conceitos

- [ ]  O que é Spring Framework
- [ ]  Por que usar Spring
- [ ]  Arquitetura do Spring

### 🔹 Core

- [ ]  IoC (Inversion of Control)
- [ ]  Dependency Injection
- [ ]  Beans
- [ ]  Bean Scope
- [ ]  Lifecycle de Beans

### 🔹 Annotations (🔥 MUITO COBRADO)

- [ ]  @Component
- [ ]  @Service
- [ ]  @Repository
- [ ]  @Controller
- [ ]  @Autowired

---

## 📌 2. Spring Boot (CORE)

(Baseado no centro do diagrama da página 1 )

- [ ]  O que é Spring Boot
- [ ]  Auto Configuration
- [ ]  Starter Dependencies
- [ ]  application.yml / properties
- [ ]  Embedded Server (Tomcat)

---

## 📌 3. Spring MVC (APIs REST)

- [ ]  Arquitetura MVC
- [ ]  Controllers
- [ ]  @RestController
- [ ]  @RequestMapping
- [ ]  @GetMapping / @PostMapping
- [ ]  DTOs
- [ ]  Validação (@Valid)

🔥 Enriquecimento:

- [ ]  Tratamento global de exceções (@ControllerAdvice)
- [ ]  Padronização de response (ResponseEntity)

---

## 📌 4. Persistência de Dados

(Baseado em Spring Data + Hibernate )

### 🔹 JPA / Hibernate

- [ ]  Entidades (@Entity)
- [ ]  Relacionamentos (OneToMany, ManyToOne…)
- [ ]  Entity Lifecycle
- [ ]  Transactions (@Transactional)

### 🔹 Spring Data

- [ ]  JPA Repository
- [ ]  Query Methods
- [ ]  @Query

🔥 Avançado (diferencial):

- [ ]  N+1 problem
- [ ]  FetchType.LAZY vs EAGER
- [ ]  Performance de queries

---

## 📌 5. Segurança (Spring Security)

(Parte esquerda do diagrama )

- [ ]  Authentication
- [ ]  Authorization
- [ ]  JWT
- [ ]  OAuth2

🔥 Enriquecimento:

- [ ]  Filtros de segurança
- [ ]  SecurityFilterChain
- [ ]  PasswordEncoder

---

## 📌 6. AOP (Aspect Oriented Programming)

- [ ]  Conceito de AOP
- [ ]  @Aspect
- [ ]  Logging com AOP
- [ ]  Cross-cutting concerns

---

## 📌 7. Testes (MUITO COBRADO)

(Baseado na seção Testing )

- [ ]  @SpringBootTest
- [ ]  @MockBean
- [ ]  JUnit
- [ ]  MockMvc

🔥 Avançado:

- [ ]  Testes de integração reais
- [ ]  Testcontainers

---

## 📌 8. Actuator & Observabilidade

- [ ]  Spring Actuator
- [ ]  Health checks
- [ ]  Metrics

🔥 Enriquecimento:

- [ ]  Micrometer
- [ ]  Prometheus + Grafana

---

## 📌 9. Microservices com Spring

(Baseado na parte “Microservices / Spring Cloud” )

### 🔹 Spring Cloud

- [ ]  Eureka (Service Discovery)
- [ ]  OpenFeign
- [ ]  API Gateway

### 🔹 Resiliência

- [ ]  Circuit Breaker
- [ ]  Retry

### 🔹 Configuração

- [ ]  Config Server

---

## 📌 10. Integração & Comunicação

- [ ]  REST Clients
- [ ]  Feign Client
- [ ]  WebClient

🔥 Avançado:

- [ ]  Comunicação assíncrona (RabbitMQ / Kafka)

---

## 📌 11. Arquitetura Profissional (🔥 DIFERENCIAL)

(Enriquecimento estratégico)

- [ ]  Clean Architecture
- [ ]  Hexagonal Architecture
- [ ]  DDD (básico)

---

## 📌 12. Performance & Boas Práticas

(Baseado + enriquecido do backend performance)

- [ ]  Paginação
- [ ]  Cache (Spring Cache + Redis)
- [ ]  Connection Pool (Hikari)
- [ ]  Compressão de resposta
- [ ]  Profiling

---

## 📌 13. Deploy & Produção

- [ ]  Docker
- [ ]  Variáveis de ambiente
- [ ]  Profiles (dev, prod)
- [ ]  Logging estruturado