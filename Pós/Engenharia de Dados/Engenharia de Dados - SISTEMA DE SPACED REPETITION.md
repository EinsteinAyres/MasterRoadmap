



# 🧠 ⚙️ SISTEMA DE SPACED REPETITION 

## 🎯 Objetivo

- Fixar conceitos técnicos complexos (SQL, Spark, Kafka…)
- Reduzir esquecimento
- Revisar automaticamente no tempo certo
- Aumentar retenção de longo prazo

---

# 🔌 1. PLUGIN QUE VOCÊ VAI USAR

👉 **Spaced Repetition (Plugin oficial da comunidade)**

## 📥 Instalação

1. Obsidian → Settings
2. Community Plugins
3. Disable Safe Mode
4. Search: **Spaced Repetition**
5. Install + Enable

---

# ⚙️ 2. CONFIGURAÇÃO IDEAL (PROFISSIONAL)

Vá em:

> Settings → Spaced Repetition

Configure assim:

New Cards Limit: 20  
Review Cards Limit: 100  
  
Easy Interval: 4 days  
Good Interval: 2 days  
Hard Interval: 1 day  
  
Maximum Interval: 60 days

👉 Isso cria um ciclo eficiente sem sobrecarregar.

---

# 🧱 3. ESTRUTURA DE PASTAS (IMPORTANTE)

📁 Engenharia de Dados  
 ┣ 📂 01 - Fundamentos  
 ┣ 📂 02 - Pipelines  
 ┣ 📂 03 - Modelagem  
 ┣ 📂 04 - Big Data  
 ┣ 📂 05 - Streaming  
 ┣ 📂 06 - Cloud  
 ┗ 📂 Revisão (Flashcards)

---

# 🧠 4. TIPOS DE FLASHCARDS (PADRÃO)

Você vai usar 3 tipos:

---

## 🟢 Tipo 1 — Conceito Direto

O que é ETL?  
:: Processo de Extrair, Transformar e Carregar dados.

---

## 🔵 Tipo 2 — Cloze (O MAIS PODEROSO)

ETL significa {{c1::Extract, Transform, Load}}

Kafka usa {{c1::partitions}} para escalar paralelismo

---

## 🔴 Tipo 3 — Técnica/Prática

Como otimizar uma query SQL?  
:: Usar índices, evitar SELECT *, analisar plano com EXPLAIN ANALYZE

---

# 🔁 5. COMO INTEGRAR COM O ROADMAP

Agora vem o diferencial.

👉 Cada semana do roadmap vira:

- 📘 Nota de estudo
- 🧠 Flashcards dentro da nota

---

## 📅 Exemplo — Semana 1 (SQL)

# 📅 Semana 1 — SQL Avançado  
  
## 🧠 Conceitos  
  
O que é CTE?  
:: Common Table Expression, permite criar subqueries nomeadas  
  
Window Functions servem para quê?  
:: Realizar cálculos mantendo o contexto da linha  
  
## 🔁 Cloze  
  
CTE significa {{c1::Common Table Expression}}  
  
ROW_NUMBER() é uma {{c1::window function}}  
  
## ⚙️ Prática  
  
Como identificar query lenta?  
:: Usar EXPLAIN ANALYZE

---

# 🔄 6. ROTINA DIÁRIA (CRÍTICO)

## 📅 Todo dia (10–20 min)

- [ ] Revisar flashcards do dia  
- [ ] Criar 3–10 novos flashcards

---

## 📅 Toda semana

- [ ] Revisar conceitos mais difíceis  
- [ ] Refinar flashcards  
- [ ] Apagar cards ruins

---

# 🧠 🔥 7. REGRAS DE OURO (EVITAR ERROS)

## ❌ Não faça:

- Flashcards gigantes
- Explicações longas
- Copiar texto inteiro

---

## ✅ Faça:

- Perguntas diretas
- Respostas curtas
- Um conceito por card

---

# 🚀 8. FLASHCARDS PRONTOS (ENGENHARIA DE DADOS)

Já te adianto alguns 👇

---

## 🟢 SQL

O que é índice em banco de dados?  
:: Estrutura que melhora a velocidade de busca  
  
Window function mantém o quê?  
:: O contexto das linhas

---

## 🔵 ETL

Qual a diferença entre ETL e ELT?  
:: ETL transforma antes de carregar, ELT depois  
  
Idempotência significa {{c1::executar várias vezes sem mudar o resultado}}

---

## 🟣 Kafka

Kafka usa {{c1::consumer groups}} para paralelismo  
  
Offset representa {{c1::posição da mensagem no tópico}}

---

## 🔥 Spark

Spark usa {{c1::lazy evaluation}}  
  
Transformation é {{c1::preguiçosa}}, action é {{c2::execução}}

---

## ☁️ Cloud

S3 é usado como {{c1::Data Lake}}  
  
Athena permite {{c1::consultar dados sem servidor}}

---

# 🧠 💥 9. SISTEMA AVANÇADO (SE QUISER IR ALÉM)

Você pode evoluir para:

## 🧩 Tags por nível

#iniciante  
#intermediario  
#avancado

---

## 🧠 Cards por stack

#sql  
#spark  
#kafka  
#aws

---

## 🔥 Revisão focada

Filtrar no plugin por:

- tema
- dificuldade
- erro recorrente

---

# 🏆 RESULTADO DESSE SISTEMA

Se você usar isso direito:

👉 Você não esquece:

- SQL avançado
- conceitos de pipeline
- arquitetura de dados

👉 E chega em entrevista:

> respondendo rápido, claro e com segurança