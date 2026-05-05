
## O que é um Enum em Java?

Um **Enum** (abreviação de _enumeration_) é um tipo de dado especial que permite que uma variável seja um conjunto de constantes predefinidas. É, essencialmente, uma classe que representa um grupo fixo de valores que não mudam.

### Para que serve?

Sua função principal é garantir **segurança de tipo** (Type Safety). Em vez de usar números inteiros (`1`, `2`, `3`) ou Strings (`"ATIVO"`, `"INATIVO"`) que podem ser digitados incorretamente, você usa nomes simbólicos rigorosamente definidos. Isso evita erros de lógica e torna o código autodocumentado.

---

### Quando usar?

Use Enums sempre que você tiver um conjunto de valores que você conhece em tempo de compilação e que dificilmente mudará.

- **Dias da semana** (SEGUNDA, TERCA...)
    
- **Status de um pedido** (PROCESSANDO, ENVIADO, ENTREGUE)
    
- **Níveis de acesso** (ADMIN, USUARIO, CONVIDADO)
    
- **Pontos cardeais** (NORTE, SUL, LESTE, OESTE)
    

---

### Como usar?

#### 1. Declaração Simples

A forma mais básica define apenas as constantes:

```Java
public enum Prioridade {
    BAIXA, MEDIA, ALTA
}
```

#### 2. Uso Prático (Switch Case)

Enums brilham em estruturas de decisão:

```Java
Prioridade nivel = Prioridade.ALTA;

switch (nivel) {
    case BAIXA -> System.out.println("Pode esperar.");
    case MEDIA -> System.out.println("Resolva hoje.");
    case ALTA  -> System.out.println("Emergência!");
}
```

#### 3. Enums com Atributos e Métodos

Como Enums em Java são classes, eles podem ter construtores, campos e métodos:

```Java
public enum StatusPedido {
    AGUARDANDO_PAGAMENTO(1),
    PAGO(2),
    ENVIADO(3);

    private final int codigo;

    // O construtor de um enum é sempre privado
    StatusPedido(int codigo) {
        this.codigo = codigo;
    }

    public int getCodigo() {
        return codigo;
    }
}
```

---

### Por que não usar constantes normais (`static final`)?

- **Validação:** Com `int`, você pode passar acidentalmente o valor `99`. Com Enum, o compilador te impede de passar algo fora do set definido.
    
- **Namespace:** As constantes ficam organizadas sob o nome do Enum, evitando colisões de nomes globais.
    
- **Funcionalidade:** Você pode iterar sobre todos os valores usando `StatusPedido.values()` ou converter uma String no objeto correto com `StatusPedido.valueOf("PAGO")`.
    

O **Caminho Crítico** aqui é: se você tem uma lista de opções fixas, use Enum. Qualquer outra abordagem (Strings ou Inteiros "mágicos") é um convite para bugs que você levará horas para rastrear depois.