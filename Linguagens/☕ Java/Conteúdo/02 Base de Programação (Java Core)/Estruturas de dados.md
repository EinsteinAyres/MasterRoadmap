

## Estruturas de dados: o que são e por que existem

### 1. A definição mais direta

Uma estrutura de dados é uma forma organizada de armazenar e acessar dados na memória. Não é um conceito abstrato — é uma decisão concreta sobre como os bytes são dispostos e como você interage com eles.

Se uma variável simples guarda um valor, uma estrutura de dados guarda uma coleção de valores e define as regras de como eles se relacionam, como são acessados e como crescem.

---

### 2. Por que arrays não bastam

Um array resolve o problema mais básico: armazenar múltiplos elementos do mesmo tipo em sequência. Mas ele é rígido — tamanho fixo, acesso apenas por índice numérico, sem busca eficiente, sem ordenação automática.

As estruturas de dados surgem para responder perguntas que o array sozinho não responde bem:

- "Preciso adicionar e remover elementos constantemente sem me preocupar com tamanho." → **Listas dinâmicas.**
- "Preciso buscar um elemento por chave, não por posição, e quero que seja rápido." → **Mapas.**
- "Preciso garantir que não haja duplicatas." → **Conjuntos.**
- "Preciso processar itens na ordem inversa de chegada." → **Pilhas.**
- "Preciso processar o primeiro que chegou primeiro." → **Filas.**
- "Preciso manter dados ordenados automaticamente." → **Árvores.**

---

### 3. A taxonomia básica

As estruturas de dados se agrupam em famílias conforme a organização interna:

**Estruturas lineares:** elementos organizados em sequência.
- Arrays, listas encadeadas, pilhas, filas.

**Estruturas associativas:** elementos acessados por chave, não por posição.
- Mapas de hash, árvores de busca.

**Estruturas de conjunto:** armazenam elementos únicos, sem duplicatas.
- HashSet, TreeSet.

**Estruturas hierárquicas:** elementos organizados em relações pai-filho.
- Árvores binárias, heaps, tries.

**Estruturas de grafo:** elementos conectados por relações arbitrárias.
- Grafos direcionados, não direcionados, ponderados.

---

### 4. A pergunta que define a escolha

Toda estrutura de dados é uma resposta a um trade-off. Não existe "a melhor" — existe a mais adequada para seu padrão de acesso:

- Você acessa mais por posição ou por chave?
- Você faz mais leituras ou mais escritas?
- A ordem dos elementos importa?
- Duplicatas são permitidas?
- O tamanho é conhecido de antemão ou varia dinamicamente?
- Você precisa de acesso concorrente?

As estruturas que o Java oferece no **Collections Framework** — `ArrayList`, `LinkedList`, `HashMap`, `TreeMap`, `HashSet`, `PriorityQueue`, entre outras — são implementações dessas estruturas clássicas, otimizadas e prontas para uso. Cada uma será destrinchada em detalhe nos capítulos seguintes.

---

### 5. O que importa agora

Antes de entrar em cada estrutura específica, leve três ideias:

**Estrutura de dados não é sintaxe — é comportamento.** Duas estruturas podem ter métodos parecidos (`add`, `get`, `remove`) e performances radicalmente diferentes.

**A estrutura certa elimina código.** Uma `PriorityQueue` substitui dezenas de linhas de ordenação manual. Um `HashMap` substitui loops aninhados de busca.

**Mudar a estrutura muda a complexidade do algoritmo.** O que é O(n) em uma lista pode ser O(1) em um mapa. A escolha da estrutura de dados é, frequentemente, a decisão de performance mais importante que você fará em um sistema.

---

O Collections Framework do Java e as estruturas clássicas que ele implementa serão explorados na sequência — cada um com seu funcionamento interno, complexidade de operações, armadilhas e cenários de uso.