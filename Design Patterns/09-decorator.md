# 09) Decorator (Decorador)

> **Categoria:** Estrutural

---

# O que é o padrão Decorator?

O **Decorator (Decorador)** é um padrão de projeto estrutural que permite **adicionar novas funcionalidades a um objeto dinamicamente**, sem alterar sua classe original.

Em vez de modificar uma classe existente ou criar inúmeras subclasses para representar todas as combinações possíveis de comportamentos, o Decorator "embrulha" (decora) um objeto com outro objeto que implementa a mesma interface.

Esse novo objeto adiciona um comportamento antes, depois ou ao redor da operação original.

Em outras palavras:

> **O Decorator adiciona responsabilidades a um objeto em tempo de execução, preservando sua interface e evitando alterações na implementação existente.**

Esse padrão segue um dos princípios mais importantes da orientação a objetos:

> **"Prefira composição em vez de herança."**

---

# Problema que o Decorator resolve

Imagine um sistema de e-commerce.

Todo produto possui um preço base.

Porém, dependendo da situação, esse preço pode sofrer alterações.

Por exemplo:

- Aplicar imposto
- Aplicar desconto
- Acrescentar frete
- Acrescentar taxa de serviço
- Aplicar cupom de desconto
- Converter moeda
- Arredondar valor

Agora imagine todas as possíveis combinações.

```
Preço Base

Preço Base + Imposto

Preço Base + Desconto

Preço Base + Frete

Preço Base + Frete + Imposto

Preço Base + Imposto + Desconto

Preço Base + Desconto + Frete

Preço Base + Desconto + Frete + Imposto

...
```

Sem o Decorator, uma solução comum seria criar uma classe para cada combinação.

```
CalculadoraPreco

CalculadoraPrecoComImposto

CalculadoraPrecoComDesconto

CalculadoraPrecoComFrete

CalculadoraPrecoComImpostoEDesconto

CalculadoraPrecoComImpostoEDescontoEFrete

CalculadoraPrecoComFreteEImpostoECupom

...
```

Esse problema é conhecido como:

> **Explosão de classes (Class Explosion).**

Quanto mais funcionalidades surgem, mais classes precisam ser criadas.

O Decorator elimina esse problema permitindo empilhar comportamentos dinamicamente.

---

# Motivação (cenário real)

Imagine uma cafeteria.

Você pede um café.

Preço:

```
R$ 10
```

Depois decide adicionar:

```
+ Leite
```

Agora:

```
R$ 12
```

Depois:

```
+ Chantilly
```

Agora:

```
R$ 15
```

Depois:

```
+ Calda de Chocolate
```

Agora:

```
R$ 18
```

Perceba que o café continua sendo o mesmo.

O que muda são os complementos.

Cada complemento adiciona um comportamento novo.

É exatamente isso que o Decorator faz.

---

# Analogia do mundo real

Imagine um presente.

Você possui apenas a caixa.

```
Presente
```

Agora você adiciona:

```
Papel de presente
```

Depois:

```
Laço
```

Depois:

```
Etiqueta
```

Depois:

```
Sacola
```

Cada camada adiciona algo novo.

Nenhuma delas modifica o presente original.

Apenas o envolve.

O Decorator funciona exatamente assim.

---

# Estrutura do padrão

```
                 Componente
                      ▲
          ┌───────────┴───────────┐
          │                       │
Objeto Base              Decorador Abstrato
                              ▲
                ┌─────────────┴─────────────┐
                │                           │
          Decorador A                 Decorador B
```

---

# Participantes

## 1. Componente (Component)

É a interface comum.

Todos os objetos implementam essa interface.

No nosso exemplo:

```ts
CalculadoraPreco;
```

---

## 2. Componente Concreto (Concrete Component)

É o objeto original.

Não possui funcionalidades extras.

Apenas executa seu comportamento básico.

No exemplo:

```ts
CalculadoraPrecoBase;
```

---

## 3. Decorador Abstrato (Decorator)

Recebe outro componente.

Também implementa a mesma interface.

Seu objetivo é servir como base para todos os decoradores.

---

## 4. Decoradores Concretos (Concrete Decorators)

São responsáveis por adicionar novas funcionalidades.

Cada um possui apenas uma responsabilidade.

Exemplos:

- Imposto
- Desconto
- Frete
- Cupom
- Cashback

---

## 5. Cliente

O cliente decide quais decoradores utilizar.

Ele pode combinar comportamentos livremente.

---

# Diagrama UML simplificado

```text
                +---------------------------+
                |    CalculadoraPreco       |
                +---------------------------+
                | + calcular(preco): number |
                +------------▲--------------+
                             |
          +------------------+-------------------+
          |                                      |
+---------------------------+     +------------------------------+
| CalculadoraPrecoBase      |     | DecoradorPreco              |
+---------------------------+     +------------------------------+
| + calcular()              |     | - componente                |
+---------------------------+     +--------------▲--------------+
                                                 |
                           +----------------------+----------------------+
                           |                                             |
               +-----------------------+                  +------------------------+
               | ComImposto            |                  | ComDesconto            |
               +-----------------------+                  +------------------------+
               | + calcular()          |                  | + calcular()           |
               +-----------------------+                  +------------------------+
```

---

# Implementação completa em TypeScript

```ts
/**
 * ===========================================================
 * DECORATOR (DECORADOR)
 * ===========================================================
 *
 * Neste exemplo construiremos uma calculadora de preços.
 *
 * O objetivo é adicionar funcionalidades
 * dinamicamente sem modificar a classe original.
 */

/**
 * Interface comum.
 *
 * Todos os objetos deverão implementar
 * esta interface.
 */
interface CalculadoraPreco {
  /**
   * Calcula o preço final.
   */
  calcular(precoBase: number): number;
}

/**
 * ===========================================================
 * COMPONENTE CONCRETO
 * ===========================================================
 *
 * Representa o comportamento básico.
 */
class CalculadoraPrecoBase implements CalculadoraPreco {
  calcular(precoBase: number): number {
    return precoBase;
  }
}

/**
 * ===========================================================
 * DECORADOR ABSTRATO
 * ===========================================================
 *
 * Recebe outro objeto que também implementa
 * CalculadoraPreco.
 *
 * Isso permite empilhar vários decoradores.
 */
abstract class DecoradorPreco implements CalculadoraPreco {
  constructor(protected calculadora: CalculadoraPreco) {}

  abstract calcular(precoBase: number): number;
}

/**
 * ===========================================================
 * DECORADOR
 * Adiciona imposto de 10%
 * ===========================================================
 */
class ComImposto extends DecoradorPreco {
  calcular(precoBase: number): number {
    const valorAtual = this.calculadora.calcular(precoBase);

    const imposto = valorAtual * 0.1;

    return valorAtual + imposto;
  }
}

/**
 * ===========================================================
 * DECORADOR
 * Aplica desconto
 * ===========================================================
 */
class ComDesconto extends DecoradorPreco {
  constructor(
    calculadora: CalculadoraPreco,

    private percentual: number,
  ) {
    super(calculadora);
  }

  calcular(precoBase: number): number {
    const valorAtual = this.calculadora.calcular(precoBase);

    const desconto = valorAtual * (this.percentual / 100);

    return valorAtual - desconto;
  }
}

/**
 * ===========================================================
 * DECORADOR
 * Acrescenta frete fixo
 * ===========================================================
 */
class ComFrete extends DecoradorPreco {
  constructor(
    calculadora: CalculadoraPreco,

    private valorFrete: number,
  ) {
    super(calculadora);
  }

  calcular(precoBase: number): number {
    const valorAtual = this.calculadora.calcular(precoBase);

    return valorAtual + this.valorFrete;
  }
}

/**
 * ===========================================================
 * CLIENTE
 * ===========================================================
 */

// Objeto base
let calculadora: CalculadoraPreco = new CalculadoraPrecoBase();

// Empilhando comportamentos
calculadora = new ComDesconto(calculadora, 10);

calculadora = new ComImposto(calculadora);

calculadora = new ComFrete(calculadora, 15);

const precoFinal = calculadora.calcular(100);

console.log(precoFinal);
```

---

# Fluxo de execução (passo a passo)

## Passo 1

O cliente cria o objeto base.

```
Preço Base
```

Valor:

```
100
```

---

## Passo 2

É adicionado um decorador de desconto.

```
Preço Base

↓

Desconto
```

Agora:

```
100

↓

90
```

---

## Passo 3

É adicionado o imposto.

```
Preço Base

↓

Desconto

↓

Imposto
```

Agora:

```
90

↓

99
```

---

## Passo 4

É adicionado o frete.

```
Preço Base

↓

Desconto

↓

Imposto

↓

Frete
```

Agora:

```
99

↓

114
```

---

## Passo 5

O cliente chama apenas:

```ts
calculadora.calcular(100);
```

Cada decorador recebe o resultado do anterior, adiciona seu comportamento e devolve o novo valor, formando uma cadeia de chamadas.

---

# Fluxo interno das chamadas

```text
Cliente

↓

ComFrete

↓

ComImposto

↓

ComDesconto

↓

CalculadoraPrecoBase

↓

100

↑

90

↑

99

↑

114
```

Esse encadeamento é chamado de **cadeia de decoração (Decorator Chain)**.

---

# Como reconhecer que é Decorator?

Alguns sinais característicos:

- O objeto é "embrulhado" por outro objeto.
- Todos implementam a mesma interface.
- É possível adicionar comportamentos sem alterar a classe original.
- É possível combinar funcionalidades dinamicamente.
- A ordem dos decoradores influencia o resultado.

---

# Vantagens

## Extensibilidade

Novos comportamentos podem ser adicionados sem modificar classes existentes.

---

## Evita explosão de subclasses

Não é necessário criar uma classe para cada combinação possível.

---

## Flexibilidade

O cliente escolhe quais funcionalidades deseja utilizar e em qual ordem.

---

## Baixo acoplamento

Cada decorador possui apenas uma responsabilidade específica.

---

## Respeita o princípio Aberto/Fechado (OCP)

As classes existentes permanecem inalteradas.

---

# Desvantagens

## Ordem importa

A sequência dos decoradores pode alterar completamente o resultado.

Exemplo:

```
Desconto

↓

Imposto
```

é diferente de:

```
Imposto

↓

Desconto
```

---

## Depuração

Quando muitos decoradores são empilhados, acompanhar o fluxo pode ser mais difícil.

---

## Muitas classes

Cada comportamento geralmente exige um novo decorador.

---

# Quando utilizar

Utilize Decorator quando:

- precisar adicionar funcionalidades dinamicamente;
- quiser evitar muitas subclasses;
- desejar combinar comportamentos em tempo de execução;
- precisar manter a classe original fechada para modificações;
- diferentes combinações de funcionalidades forem necessárias.

Exemplos comuns:

- Cafés e complementos
- Processamento de preços
- Compressão de arquivos
- Criptografia
- Logs
- Cache
- Validação
- Streams de entrada e saída
- Middleware

---

# Quando evitar

Evite Decorator quando:

- o comportamento nunca muda;
- existe apenas uma única implementação;
- as combinações de funcionalidades são poucas e estáticas;
- a composição adicionaria complexidade desnecessária.

---

# Relação com os princípios SOLID

## S — Single Responsibility Principle (SRP)

Cada decorador possui uma única responsabilidade.

Exemplo:

- `ComImposto` calcula apenas imposto.
- `ComDesconto` aplica apenas desconto.
- `ComFrete` adiciona apenas o frete.

---

## O — Open/Closed Principle (OCP)

Novos comportamentos podem ser adicionados criando novos decoradores, sem modificar os existentes.

---

## L — Liskov Substitution Principle (LSP)

Qualquer decorador pode substituir o componente original, pois todos implementam a mesma interface.

---

## I — Interface Segregation Principle (ISP)

A interface é pequena e contém apenas o comportamento essencial (`calcular`).

---

## D — Dependency Inversion Principle (DIP)

Os decoradores dependem da abstração (`CalculadoraPreco`), e não de implementações concretas.

---

# Anti-exemplo

Um erro comum é criar subclasses para todas as combinações possíveis de funcionalidades.

```text
CalculadoraPrecoComImposto

CalculadoraPrecoComDesconto

CalculadoraPrecoComFrete

CalculadoraPrecoComImpostoEDesconto

CalculadoraPrecoComFreteEDesconto

CalculadoraPrecoComFreteEImpostoEDesconto

CalculadoraPrecoComCupomEFreteEImposto

...
```

Esse modelo cresce rapidamente, tornando o sistema difícil de manter. O Decorator elimina esse problema permitindo combinar comportamentos dinamicamente por composição.

---

# Exercício proposto

Implemente um sistema de notificações utilizando o padrão Decorator.

### Requisitos

Crie a interface:

```ts
Notificador;
```

Implemente inicialmente:

- `NotificadorBasico`

Depois crie os decoradores:

- `ComEmail`
- `ComSMS`
- `ComWhatsApp`
- `ComPushNotification`

Cada decorador deverá:

- implementar a interface `Notificador`;
- receber outro notificador no construtor;
- executar sua própria lógica de envio;
- delegar a chamada ao próximo elemento da cadeia.

### Exemplo esperado

```text
Enviando notificação...

↓

E-mail enviado

↓

SMS enviado

↓

WhatsApp enviado

↓

Push Notification enviada
```

### Desafio extra

Implemente um decorador chamado:

```text
ComLog
```

Ele deverá registrar no console:

- data e hora do envio;
- canal utilizado;
- mensagem enviada.

O objetivo é adicionar auditoria ao sistema **sem modificar nenhuma das classes existentes**, reforçando o principal benefício do padrão Decorator: **estender funcionalidades por composição, mantendo o código desacoplado e altamente reutilizável.**
