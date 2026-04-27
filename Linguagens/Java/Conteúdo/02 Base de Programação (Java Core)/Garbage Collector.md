## Garbage Collection: o faxineiro automático da JVM

### 1. O problema que o GC resolve

Em C ou C++, você aloca memória com `malloc` e libera com `free`. Esqueceu de liberar? Vazamento de memória. Liberou duas vezes? Crash. Liberou algo que ainda estava em uso? Crash ou comportamento imprevisível.

Java elimina esse problema. Você cria objetos com `new` e nunca os destrói manualmente. Quem cuida da limpeza é o **Garbage Collector** (GC) — um componente da JVM que rastreia objetos na Heap, identifica os que não têm mais referências alcançáveis e recupera o espaço que eles ocupam .

A contrapartida: você perde controle sobre *quando* a memória é liberada. O GC decide. E enquanto trabalha, pode pausar sua aplicação.

---

### 2. Mark-and-Sweep: o algoritmo fundamental

O mecanismo básico por trás de quase todo GC é o **mark-and-sweep** (marcar e varrer) .

**Fase 1 — Mark (marcar):**

O GC parte das **GC roots** — raízes de alcançabilidade. São elas: variáveis locais nas Stacks de todas as threads, campos estáticos de classes, referências JNI. A partir dessas raízes, o GC percorre o grafo de objetos como quem segue links: se A aponta para B, e A é alcançável, então B também é. Cada objeto alcançado é marcado como vivo .

**Fase 2 — Sweep (varrer):**

Tudo que não foi marcado é lixo. A memória ocupada por esses objetos é devolvida à Heap para reuso .

**Fase 3 — Compact (compactar, opcional):**

Com o tempo, objetos removidos deixam buracos na Heap — fragmentação. Se você precisa alocar um objeto de 1 MB mas só tem espaços de 500 KB, a alocação falha mesmo que haja memória total suficiente. A compactação move objetos vivos para eliminar esses buracos, restaurando grandes áreas contíguas .

---

### 3. Gerações: jovens morrem cedo

A observação empírica que moldou a maioria dos GCs modernos: **a maioria dos objetos morre jovem** . Uma String temporária criada dentro de um método é usada e descartada em milissegundos. Uma conexão de banco de dados pode viver por horas.

Para explorar isso, a Heap é dividida em duas regiões :

**Young Generation (geração jovem):** onde todo objeto novo nasce. Dividida em:

- **Eden:** o berçário. Todos os objetos são alocados aqui inicialmente.
- **Survivor Spaces (S0 e S1):** áreas que recebem objetos que sobreviveram a uma ou mais coletas.

**Old Generation (geração velha, Tenured):** objetos que sobreviveram a várias coletas na Young Generation são promovidos para cá.

A coleta na Young Generation é chamada **Minor GC**. É rápida — varre apenas objetos jovens, onde a mortalidade é altíssima. A coleta na Old Generation (ou em toda a Heap) é chamada **Major GC** ou **Full GC** — mais lenta, mais pausante .

---

### 4. Os coletores disponíveis

A JVM HotSpot oferece vários coletores. A escolha é feita com flags de inicialização.

**Serial GC** (`-XX:+UseSerialGC`)

O mais simples. Um único thread faz todo o trabalho. Durante a coleta, todas as threads da aplicação param (Stop-the-World). Adequado para aplicações pequenas, heaps de dezenas de megabytes, ambientes de single-core .

**Parallel GC** (`-XX:+UseParallelGC`)

Usa múltiplos threads para acelerar as pausas Stop-the-World. Focado em **throughput** — fazer o máximo de trabalho no menor tempo total de pausa, mesmo que cada pausa individual seja longa. Ideal para processamento batch onde pausas de alguns segundos são aceitáveis .

**G1 GC** (`-XX:+UseG1GC`, padrão desde Java 9)

O Garbage First divide a Heap em **regiões** de tamanho fixo (1 a 32 MB). Em vez de coletar a Young ou Old Generation inteira, ele seleciona as regiões com mais lixo e coleta só elas. O objetivo é cumprir uma **meta de pausa máxima** configurável (`-XX:MaxGCPauseMillis`). Equilibra throughput e latência. É a escolha padrão para a maioria dos servidores .

**ZGC** (`-XX:+UseZGC`)

Projetado para pausas de milissegundos, mesmo com heaps de terabytes. Faz quase todo o trabalho (marcação, compactação) de forma concorrente — enquanto a aplicação roda. Usa "colored pointers" e load barriers para rastrear objetos sem parar as threads. As pausas são tipicamente abaixo de 1 milissegundo. Disponível desde Java 11 (estável no 15+). Ideal para sistemas de baixíssima latência: finanças, jogos online, analytics em tempo real .

**Shenandoah** (`-XX:+UseShenandoahGC`)

Similar ao ZGC em objetivos: pausas mínimas via coleta concorrente. Desenvolvido pela Red Hat, disponível em distribuições OpenJDK específicas. Usa "Brooks pointers" e write barriers. Se você usa uma distribuição que o suporta e precisa de latência baixíssima, é uma alternativa ao ZGC .

---

### 5. Stop-the-World: a pausa que congela tudo

Stop-the-World (STW) é o momento em que a JVM pausa todas as threads da aplicação para executar uma fase do GC que não pode ser feita concorrentemente .

É o preço da limpeza. Nenhum coletor elimina completamente as pausas — mesmo ZGC e Shenandoah têm breves momentos STW para fases de inicialização e finalização dos ciclos. A diferença está na duração: Serial e Parallel podem pausar por segundos ou minutos em heaps grandes; G1 tenta manter pausas configuráveis (ex: 100-200 ms); ZGC e Shenandoah mantêm pausas na casa de milissegundos .

---

### 6. O que você pode controlar

**Tamanho da Heap:**

- `-Xms` — tamanho inicial. `-Xmx` — tamanho máximo.
- Para ambientes de produção, use `-Xms` = `-Xmx` para evitar redimensionamentos durante a execução .

**Escolha do coletor:**

- `-XX:+UseG1GC`, `-XX:+UseZGC`, `-XX:+UseParallelGC`, `-XX:+UseSerialGC`.

**Meta de pausa (G1):**

- `-XX:MaxGCPauseMillis=200` — o G1 tentará manter as pausas abaixo desse valor. Não é uma garantia, é uma meta .

**Threads concorrentes (ZGC):**

- `-XX:ConcGCThreads=<número>` — controla quantos threads o ZGC usa para trabalho concorrente. A heurística automática geralmente funciona bem; ajuste apenas se tiver medições que justifiquem .

**Registro de atividade do GC:**

```
-Xlog:gc*:gc.log   # Log detalhado para análise de performance
-Xlog:gc:gc.log    # Log básico (uma linha por evento)
```

Essas flags são essenciais em produção — sem logs, qualquer diagnóstico é adivinhação .

---

### 7. O que você não deve fazer

**Não invoque `System.gc()`.**

`System.gc()` *sugere* que a JVM execute uma coleta. Não há garantia de quando ou se ela ocorrerá. Na HotSpot, por padrão, força uma Full GC — que pode pausar a aplicação por tempo considerável. É quase sempre contraproducente .

Se realmente precisar de uma coleta em momento controlado (ex: entre fases de inicialização e operação), use `-XX:ExplicitGCInvokedConcurrent` para que o comando dispare uma coleta concorrente em vez de uma Full GC . Mas a regra geral é: confie no GC, não tente controlá-lo.

**Não evite criar objetos por medo do GC.**

A alocação de objetos de curta duração na Young Generation é extremamente barata — essencialmente um "pointer bumping" (avançar um ponteiro). O custo real está em objetos que sobrevivem a muitas coletas e são promovidos para a Old Generation, aumentando a pressão por Full GCs. Criar objetos de vida curta é normal e esperado .

**Não configure a Heap sem medições.**

Uma Heap grande demais aumenta a duração das pausas. Uma Heap pequena demais força coletas frequentes. O tamanho certo depende da sua aplicação, da taxa de alocação e do padrão de uso — e só se descobre com monitoramento .

---

### 8. Diagnóstico básico

Se sua aplicação Java apresenta pausas inexplicáveis, consumo crescente de memória ou `OutOfMemoryError`, comece por aqui:

1. **Ative logs de GC** (`-Xlog:gc*:gc.log`) e analise com ferramentas como GCViewer ou GCEasy.
2. **Gere um heap dump** em caso de `OutOfMemoryError` com `-XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/caminho/do/arquivo.hprof` . Analise com VisualVM, Eclipse MAT ou IntelliJ Profiler.
3. **Monitore o uso de memória** com VisualVM, JConsole ou Prometheus + JMX Exporter. Observe o padrão de consumo: sobe constantemente (possível vazamento) ou sobe e desce em dente de serra (comportamento normal com GC).

---

### 9. Resumo em uma frase

O Garbage Collector é o funcionário silencioso que varre o chão enquanto você trabalha — só que, às vezes, para todo mundo na sala, tranca a porta e não deixa ninguém mexer nada até terminar. Entender quando e por que ele faz isso é o que separa uma aplicação que funciona de uma que funciona sob carga.

---

### Fontes

1. Oracle, "HotSpot Virtual Machine Garbage Collection Tuning Guide" — documentação oficial sobre tuning de GC na HotSpot JVM .
2. Cleverence, "HotSpot JVM Garbage Collection Tuning Guide: G1, ZGC, Shenandoah" (2026) — guia prático com estratégias de tuning para coletores modernos .
3. OpenJDK Wiki, "ZGC" — documentação oficial do Z Garbage Collector, incluindo flags de configuração e requisitos .
4. IBM, "Garbage collection" (2026) — explicação detalhada do processo de mark, sweep, scavenge e operações concorrentes .
5. JavaRush, "Сборщики мусора: G1, ZGC, Shenandoah, сравнение" — visão geral e tabela comparativa dos principais coletores .
6. Carnegie Mellon University SEI, "OBJ52-J. Write garbage-collection-friendly code" — recomendações de segurança e performance para código que interage com o GC .