

Em Java, o método **`.hashCode()`** é um método da classe base java.lang.Object que **retorna um número inteiro (int)** representando o “código hash” de um objeto.

---

## 🧠 Ideia central (bem direta)

O `.hashCode()` serve para **identificar rapidamente um objeto em estruturas de dados**, como:

- HashMap
- HashSet

Essas estruturas usam o hash para **acessar elementos de forma extremamente rápida (O(1))**.

---

## ⚙️ Como funciona (na prática)

Quando você coloca um objeto em um `HashMap`, o Java:

1. Chama `.hashCode()` do objeto
2. Usa esse número para decidir **em qual “bucket” ele vai ficar**
3. Quando você busca o objeto, ele usa o hash novamente para ir direto ao lugar certo

---

## 📌 Exemplo simples

```java
String nome = "Einstein";
int hash = nome.hashCode();
System.out.println(hash);
```

Esse número será sempre o mesmo **para o mesmo conteúdo**, enquanto o objeto não mudar.

---

## ⚠️ Regra mais importante (ESSENCIAL PRA MERCADO)

Existe um contrato entre `.equals()` e `.hashCode()`:

> Se dois objetos são iguais (`equals() == true`), então eles **DEVEM ter o mesmo hashCode**.

---

## 💣 Exemplo clássico de erro

```java
class Pessoa {    
	String nome;
}
```

Se você não sobrescrever (`override`) `.hashCode()` e `.equals()`, pode acontecer:

```java
Pessoa p1 = new Pessoa("Einstein");
Pessoa p2 = new Pessoa("Einstein");

System.out.println(p1.equals(p2)); // false
```

Mesmo com os mesmos dados.

---

## ✅ Implementação correta

```java
import java.util.Objects;

class Pessoa {
    String nome;
        @Override
        public boolean equals(Object o) {
            if (this == o) return true;
            if (!(o instanceof Pessoa)) 
	        return false;        
		    Pessoa p = (Pessoa) o;
		    return Objects.equals(nome, p.nome);
		}    
		
		@Override    
		public int hashCode() {
		    return Objects.hash(nome);    
	}
}
```

---

## 🚀 Insight de mercado (2026)

Saber isso bem te diferencia porque:

- Evita bugs silenciosos em `HashMap`, `Set`, cache, etc.
- É base para:
    - DDD (entidades vs value objects)
    - Cache distribuído (Redis, etc.)
    - Sistemas de alta performance

---

## 🧩 Resumo mental (pra nunca esquecer)

- `.hashCode()` → **onde o objeto fica**
- `.equals()` → **se o objeto é igual**
- Os dois precisam estar **alinhados**