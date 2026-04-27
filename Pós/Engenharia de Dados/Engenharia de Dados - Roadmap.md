# Roadmap de Engenharia de Dados — foco mercado/prática

## 1. Fundamentos obrigatórios

### SQL avançado

Domine antes de qualquer ferramenta moderna:

- `JOINs`, `CTEs`, `window functions`
- agregações, subqueries, views
- modelagem relacional
- índices e plano de execução
- particionamento
- otimização de queries
- transações e isolamento
- procedures e funções

**Prática:**

- PostgreSQL + datasets reais
- criar queries analíticas
- otimizar queries lentas com `EXPLAIN ANALYZE`

---

## 2. Python para dados

### Essencial

- manipulação de arquivos CSV, JSON, Parquet
- `pandas`
- `requests`
- conexão com bancos
- scripts ETL
- tratamento de erros
- logs
- variáveis de ambiente
- testes básicos

### Bibliotecas importantes

- pandas
- pyarrow
- sqlalchemy
- psycopg2
- requests
- pydantic
- pytest

**Prática:**

- consumir uma API pública
- tratar dados
- salvar em PostgreSQL
- gerar arquivos Parquet

---

## 3. Linux, Git e Docker

### Linux

- terminal
- permissões
- processos
- cron
- variáveis de ambiente
- SSH
- logs

### Git

- branches
- pull requests
- versionamento de pipelines
- `.gitignore`
- tags/releases

### Docker

- Dockerfile
- docker-compose
- volumes
- networks
- containers para PostgreSQL, Airflow, Spark

**Prática:**

- subir PostgreSQL + Airflow + MinIO com Docker Compose.

---

## 4. Modelagem de dados

### Relacional

- normalização
- constraints
- chaves
- integridade referencial

### Analítica

- Star Schema
- Snowflake Schema
- fatos e dimensões
- SCD Type 1 e Type 2
- Data Marts
- Data Warehouse

### Data Vault

- hubs
- links
- satellites
- quando usar e quando evitar

**Prática:**

- modelar um DW de vendas com:
    - `fact_sales`
    - `dim_customer`
    - `dim_product`
    - `dim_date`

---

## 5. ETL, ELT e ingestão

### Conceitos

- ETL vs ELT
- batch
- streaming
- CDC
- incremental load
- full load
- upsert
- idempotência
- backfill
- retries
- checkpoints

### Ferramentas

- Python
- Airflow
- dbt
- Spark
- Kafka
- Debezium
- Fivetran/Airbyte

**Prática:**

- pipeline incremental:
    - API → raw → staging → trusted → analytics

---

## 6. Data Lake e Lakehouse

### Conceitos

- Data Lake
- Data Warehouse
- Lakehouse
- zonas: raw, bronze, silver, gold
- schema-on-read
- schema evolution
- compactação
- particionamento
- formatos colunares

### Formatos

- CSV
- JSON
- Avro
- Parquet
- ORC

### Table formats

- Delta Lake
- Apache Iceberg
- Apache Hudi

**Prática:**

- salvar dados em Parquet
- particionar por data
- consultar com Spark ou DuckDB

---

## 7. Orquestração com Airflow

### Essencial

- DAGs
- tasks
- operators
- sensors
- retries
- schedules
- backfill
- XCom
- connections
- variables
- pools
- SLAs

### Boas práticas

- DAG idempotente
- tasks pequenas
- logs claros
- retry com estratégia
- alertas
- separação entre código e configuração

**Prática:**

- DAG diária:
    - extrai dados de API
    - valida
    - grava no lake
    - transforma
    - carrega DW

---

## 8. Transformações com dbt

### Fundamentos

- models
- sources
- seeds
- snapshots
- tests
- macros
- materializations
- documentation
- lineage

### Boas práticas

- staging models
- intermediate models
- marts
- naming convention
- testes de unicidade e não-nulo
- documentação automática

**Prática:**

- criar projeto dbt com:
    - `stg_orders`
    - `int_revenue`
    - `mart_sales_daily`

---

## 9. Big Data com Spark

### Essencial

- DataFrame API
- Spark SQL
- lazy evaluation
- transformations/actions
- partitions
- shuffle
- cache/persist
- broadcast join
- leitura e escrita Parquet
- tuning básico

### PySpark

- leitura de dados
- tratamento
- joins
- agregações
- window functions
- UDFs com cuidado

**Prática:**

- processar milhões de linhas
- otimizar job com particionamento e broadcast join

---

## 10. Streaming e mensageria

### Kafka

- topics
- partitions
- offsets
- consumer groups
- retention
- schema registry
- producer/consumer
- exactly-once vs at-least-once

### Streaming

- Spark Structured Streaming
- Kafka Streams
- Flink, em nível conceitual

### CDC

- Debezium
- Change Data Capture
- outbox pattern

**Prática:**

- PostgreSQL → Debezium → Kafka → Spark → Lakehouse

---

## 11. Cloud para Engenharia de Dados

Escolha uma cloud principal. Pela sua base, **AWS faz muito sentido**.

### AWS

- S3
- IAM
- Glue
- Athena
- Redshift
- EMR
- Lambda
- Step Functions
- Kinesis
- MSK
- CloudWatch
- Lake Formation

### Azure

- ADLS
- Data Factory
- Synapse
- Databricks
- Event Hubs

### GCP

- BigQuery
- Cloud Storage
- Dataflow
- Dataproc
- Pub/Sub
- Composer

**Prática AWS:**

- API → S3 raw → Glue Catalog → Athena → dbt/Athena → dashboard.

---

## 12. Governança, segurança e qualidade

### Governança

- catálogo de dados
- linhagem
- ownership
- classificação de dados
- LGPD
- data contracts

### Segurança

- IAM mínimo necessário
- criptografia em repouso
- criptografia em trânsito
- secrets manager
- mascaramento
- controle de acesso por camada

### Qualidade

- Great Expectations
- Soda
- dbt tests
- validação de schema
- checks de volume
- checks de duplicidade
- checks de null

**Prática:**

- quebrar pipeline quando:
    - schema muda
    - volume cai muito
    - chaves duplicam
    - campo obrigatório vem nulo

---

## 13. Observabilidade de dados

### Monitore

- latência
- volume
- freshness
- completude
- erros por etapa
- custo
- tempo de execução
- qualidade dos dados

### Ferramentas

- Airflow logs
- Prometheus/Grafana
- OpenLineage/Marquez
- DataHub
- Monte Carlo, conceito
- CloudWatch

**Prática:**

- dashboard de saúde dos pipelines.

---

# Roadmap prático por fases

## Fase 1 — Base forte

**Objetivo:** ficar empregável em ETL/SQL/Python.

- SQL avançado
- Python para ETL
- PostgreSQL
- Git
- Docker
- Linux básico

**Projeto:**  
API pública → PostgreSQL → queries analíticas.

---

## Fase 2 — Pipelines reais

**Objetivo:** construir pipelines profissionais.

- Airflow
- dbt
- Parquet
- camadas raw/staging/analytics
- testes de dados

**Projeto:**  
Pipeline com Airflow + dbt + PostgreSQL + MinIO.

---

## Fase 3 — Data Warehouse

**Objetivo:** dominar modelagem analítica.

- Star Schema
- fatos/dimensões
- SCD
- métricas
- marts

**Projeto:**  
DW de e-commerce com histórico de clientes e vendas.

---

## Fase 4 — Big Data

**Objetivo:** processar dados em escala.

- Spark
- PySpark
- particionamento
- otimização
- Lakehouse

**Projeto:**  
Processamento de arquivos grandes em Spark + Parquet.

---

## Fase 5 — Streaming

**Objetivo:** dados em tempo quase real.

- Kafka
- CDC
- Debezium
- consumers
- schema registry

**Projeto:**  
PostgreSQL → Kafka → Spark Streaming → Lakehouse.

---

## Fase 6 — Cloud

**Objetivo:** virar perfil desejado no mercado.

- AWS S3
- Glue
- Athena
- Redshift
- IAM
- CloudWatch
- Terraform básico

**Projeto final:**  
Data Platform completa na AWS.

---

# Stack recomendada para você

Pelo seu perfil Java/Spring/AWS, eu seguiria esta stack:

SQL + PostgreSQL  
Python  
Docker  
Airflow  
dbt  
Spark/PySpark  
Kafka  
Debezium  
S3  
Glue  
Athena  
Redshift  
Terraform  
Great Expectations  
Grafana/CloudWatch

---

# Projeto de portfólio ideal

## Plataforma de Dados de E-commerce

### Arquitetura

API externa  
   ↓  
Airflow  
   ↓  
Raw Zone / S3 ou MinIO  
   ↓  
Spark/Python  
   ↓  
Trusted Zone / Parquet  
   ↓  
dbt  
   ↓  
Data Warehouse  
   ↓  
Dashboard

### Diferenciais

- ingestão incremental
- particionamento por data
- testes de qualidade
- logs
- retries
- documentação
- lineage
- CI/CD
- Docker Compose
- README profissional
- diagrama C4