#  Conteinerização

## 📌 Conceito
Conteinerização é uma forma de empacotar aplicações com todas as suas dependências em um ambiente isolado, garantindo que rodem da mesma forma em qualquer máquina.

---

## ⚙️ Como funciona
- Utiliza recursos do kernel Linux:
  - Namespaces (isolamento)
  - cgroups (controle de recursos)
- Compartilha o kernel do host (diferente de VM)

---

## 🆚 Comparação

- Bare Metal → direto no hardware
- VM → virtualiza sistema operacional
- Container → virtualiza processo (mais leve)

---

## 🔥 Vantagens
- Portabilidade
- Escalabilidade
- Isolamento
- Deploy rápido

---

## ⚠️ Pontos importantes
- Containers são efêmeros
- Não armazenam dados por padrão

---

## 🧠 Flashcards

O que é conteinerização?::Empacotar aplicação e dependências em ambiente isolado

Diferença entre container e VM?::Container compartilha kernel, VM virtualiza SO

O que são namespaces?::Isolamento de processos no Linux

O que são cgroups?::Controle de uso de CPU e memória

---

## 🔗 Conexões
- [[Docker]]
- [[Kubernetes]]
- [[Microsserviços]]
