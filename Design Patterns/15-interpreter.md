# 15) Interpreter (Intérprete)

> **Categoria:** Comportamental

---

# O que é o padrão Interpreter?

O **Interpreter (Intérprete)** é um padrão de projeto comportamental utilizado para **interpretar uma linguagem ou conjunto de regras**, transformando expressões em resultados.

Em outras palavras, ele permite que uma aplicação **entenda uma linguagem criada especificamente para um determinado domínio**, conhecida como **DSL (Domain Specific Language)**.

Ao invés de escrever regras diretamente no código-fonte, elas podem ser escritas em forma de texto.

Exemplo:

```text
valor > 100
```

ou

```text
status = PAGO
```

ou ainda

```text
idade >= 18
```

O papel do Interpreter é ler essa expressão, entendê-la e produzir um resultado.

> **O padrão Interpreter transforma texto em comportamento.**

---

# Problema que o Interpreter resolve

Imagine um sistema de e-commerce.

O administrador deseja criar promoções sem precisar alterar o código.

Exemplo:

```text
valor > 500
```

ou

```text
cliente = PREMIUM
```

ou

```text
estado = SP
```

Sem o padrão Interpreter, seria necessário criar vários `if` espalhados pelo sistema.

```ts
if (pedido.valor > 500) {
    ...
}

if (pedido.cliente === "PREMIUM") {
    ...
}

if (pedido.estado === "SP") {
    ...
}
```

Cada nova regra exigiria alterar o código da aplicação.

Com o Interpreter, as regras passam a ser escritas em texto e interpretadas em tempo de execução.

---

# Motivação (cenário real)

Imagine um filtro de pesquisa em um sistema ERP.

O usuário digita:

```text
valor > 1000
```

O sistema retorna apenas os pedidos acima de R$ 1000.

Depois ele digita:

```text
status = PAGO
```

O sistema mostra apenas pedidos pagos.

O programa está interpretando essas expressões para decidir quais registros devem ser exibidos.

---

# Analogia do mundo real

Imagine um tradutor.

Uma pessoa fala:

```text
Hello
```

O tradutor interpreta e responde:

```text
Olá
```

Ele não apenas copia o texto.

Ele entende seu significado.

O Interpreter funciona da mesma maneira.

Ele interpreta símbolos de uma linguagem e produz um resultado.

---

# O que é uma DSL?

DSL significa:

> **Domain Specific Language**

Ou seja:

**Linguagem Específica de Domínio.**

Alguns exemplos:

SQL

```sql
SELECT * FROM produtos
```

Regex

```text
[0-9]+
```

Excel

```text
=SOMA(A1:A10)
```

Filtros do MongoDB

```json
{
  "idade": {
    "$gt": 18
  }
}
```

Todos esses exemplos representam pequenas linguagens.

O padrão Interpreter é frequentemente utilizado para implementá-las.

---

# Estrutura do padrão

```text
Expressão em texto

↓

Parser

↓

Árvore de Sintaxe (AST)

↓

Interpreter

↓

Resultado
```

---

# Participantes

## 1. Contexto

Representa os dados utilizados durante a interpretação.

Exemplo:

```text
valor = 150

status = PAGO
```

---

## 2. Expressão

Representa uma regra da linguagem.

Exemplo:

```text
valor > 100
```

---

## 3. Parser

Transforma o texto em uma estrutura de objetos.

Essa estrutura normalmente é chamada de:

```
AST
```

(Abstract Syntax Tree)

---

## 4. Interpreter

Percorre a AST.

Cada nó sabe interpretar a si próprio.

---

## 5. Cliente

Fornece a expressão e os dados.

---

# Diagrama UML simplificado

```text
                 Cliente
                    │
                    ▼
              Expressão Texto
                    │
                    ▼
                 Parser
                    │
                    ▼
                  AST
                    │
                    ▼
              Interpreter
                    │
                    ▼
               Resultado
```

---

# O que é uma AST?

AST significa:

> **Abstract Syntax Tree**

Ou:

> **Árvore de Sintaxe Abstrata**

Em vez de interpretar diretamente a string:

```text
valor > 100
```

criamos uma representação em objetos.

Exemplo:

```text
Comparação

├── campo

│     valor

├── operador

│     >

└── valor

      100
```

Essa árvore é muito mais fácil de interpretar do que uma string.

---

# Implementação completa em TypeScript

```ts
/**
 * ============================================================
 * INTERPRETER
 * ============================================================
 *
 * Neste exemplo construiremos um pequeno
 * interpretador capaz de avaliar expressões
 * simples como:
 *
 * valor > 100
 * status = PAGO
 * idade >= 18
 */

/**
 * Operadores suportados.
 */
type Operador = '>' | '<' | '>=' | '<=' | '=';

/**
 * Dados disponíveis durante
 * a interpretação.
 */
type Contexto = Record<string, string | number>;

/**
 * ============================================================
 * NÓ DA AST
 * ============================================================
 */

type Comparacao = {
  tipo: 'Comparacao';

  campo: string;

  operador: Operador;

  valor: string | number;
};

/**
 * Neste exemplo existe apenas
 * um tipo de expressão.
 */
type AST = Comparacao;

/**
 * ============================================================
 * PARSER
 * ============================================================
 *
 * Converte uma string em AST.
 */

function analisarExpressao(expressao: string): AST {
  const partes = expressao

    .trim()

    .split(/\s+/);

  const campo = partes[0];

  const operador = partes[1] as Operador;

  const textoValor = partes.slice(2).join(' ');

  /**
   * Tenta converter para número.
   */
  const numero = Number(textoValor);

  const valor = Number.isNaN(numero) ? textoValor : numero;

  return {
    tipo: 'Comparacao',

    campo,

    operador,

    valor,
  };
}

/**
 * ============================================================
 * INTERPRETER
 * ============================================================
 */

function interpretar(
  arvore: AST,

  contexto: Contexto,
): boolean {
  const valorEsquerda = contexto[arvore.campo];

  const valorDireita = arvore.valor;

  switch (arvore.operador) {
    case '>':
      return Number(valorEsquerda) > Number(valorDireita);

    case '<':
      return Number(valorEsquerda) < Number(valorDireita);

    case '>=':
      return Number(valorEsquerda) >= Number(valorDireita);

    case '<=':
      return Number(valorEsquerda) <= Number(valorDireita);

    case '=':
      return String(valorEsquerda) === String(valorDireita);
  }
}

/**
 * ============================================================
 * CLIENTE
 * ============================================================
 */

const contexto: Contexto = {
  valor: 250,

  status: 'PAGO',

  idade: 21,
};

const regra1 = analisarExpressao('valor > 100');

console.log(
  interpretar(
    regra1,

    contexto,
  ),
);

const regra2 = analisarExpressao('status = PAGO');

console.log(
  interpretar(
    regra2,

    contexto,
  ),
);

const regra3 = analisarExpressao('idade >= 18');

console.log(
  interpretar(
    regra3,

    contexto,
  ),
);
```

---

# Saída esperada

```text
true

true

true
```

---

# Fluxo de execução (passo a passo)

## Passo 1

O usuário escreve uma regra.

```text
valor > 100
```

---

## Passo 2

O Parser recebe a string.

↓

Divide em partes.

```text
Campo

↓

valor

Operador

↓

>

Valor

↓

100
```

---

## Passo 3

O Parser monta a AST.

```text
Comparação

↓

campo = valor

↓

operador = >

↓

valor = 100
```

---

## Passo 4

O Interpreter recebe a AST.

↓

Lê o contexto.

```text
valor = 250
```

---

## Passo 5

Realiza a comparação.

```text
250 > 100
```

↓

Resultado

```text
true
```

---

# Fluxo interno

```text
Texto

↓

Parser

↓

AST

↓

Interpreter

↓

Resultado
```

---

# Como reconhecer que é Interpreter?

Alguns sinais indicam claramente esse padrão:

- Existe uma pequena linguagem.
- Expressões são escritas em texto.
- O sistema cria uma árvore de sintaxe (AST).
- Cada nó sabe interpretar a si próprio.
- O comportamento é definido pelas regras da linguagem.

---

# Vantagens

## Flexibilidade

Novas regras podem ser criadas sem alterar o código do cliente.

---

## Organização

Cada tipo de expressão fica isolado em uma classe ou estrutura.

---

## Reutilização

A linguagem pode ser utilizada em diferentes partes da aplicação.

---

## Extensibilidade

Novos operadores podem ser adicionados facilmente.

Exemplo:

```text
!=

E

OU

CONTÉM

IN

LIKE
```

---

# Desvantagens

## Complexidade

Quanto maior a linguagem, maior será o número de classes.

---

## Performance

Interpretar expressões é mais lento do que executar código compilado.

---

## Parser

Criar um parser completo pode ser bastante trabalhoso.

---

# Quando utilizar

Utilize Interpreter quando:

- existir uma linguagem própria;
- usuários puderem criar regras;
- filtros forem escritos em texto;
- expressões precisarem ser avaliadas dinamicamente.

Exemplos comuns:

- SQL;
- Regex;
- Fórmulas do Excel;
- Sistemas de regras de negócio;
- Motores de workflow;
- Sistemas de filtros;
- Linguagens de templates;
- Calculadoras.

---

# Quando evitar

Evite utilizar quando:

- as regras forem extremamente simples;
- a linguagem crescer demais.

Nesses casos pode ser melhor utilizar ferramentas prontas como:

- ANTLR;
- PEG.js;
- Nearley;
- Chevrotain.

---

# Relação com os princípios SOLID

## S — Single Responsibility Principle (SRP)

Cada expressão interpreta apenas um tipo de regra.

---

## O — Open/Closed Principle (OCP)

Novos tipos de expressões podem ser adicionados sem alterar as existentes.

---

## L — Liskov Substitution Principle (LSP)

Qualquer expressão concreta pode substituir outra desde que respeite o contrato da linguagem.

---

## I — Interface Segregation Principle (ISP)

Cada expressão implementa apenas os métodos necessários para sua interpretação.

---

## D — Dependency Inversion Principle (DIP)

O cliente depende das abstrações da linguagem e não das implementações específicas de cada expressão.

---

# Anti-exemplo

Um erro bastante comum é utilizar:

```ts
eval(expressao);
```

Exemplo:

```ts
eval('valor > 100');
```

Isso é extremamente perigoso.

Se um usuário escrever:

```text
process.exit()
```

ou

```text
window.location = ...
```

o sistema poderá executar código arbitrário.

Além dos riscos de segurança, `eval()` dificulta a manutenção e reduz a performance.

O padrão Interpreter cria uma linguagem controlada, permitindo apenas as regras previamente definidas pela aplicação.

---

# Exercício proposto

Implemente um interpretador para filtros de produtos.

### Requisitos

O sistema deverá aceitar regras como:

```text
preco > 100
```

```text
estoque >= 10
```

```text
categoria = Informática
```

Crie:

- um Parser;
- uma AST;
- um Interpreter.

Depois permita interpretar essas regras utilizando um contexto com dados de produtos.

---

## Desafio extra

Expanda a linguagem adicionando operadores lógicos.

Exemplo:

```text
preco > 100 E estoque > 5
```

```text
categoria = Informática OU categoria = Games
```

Para isso, crie novos nós na AST, como:

- `ExpressaoE`
- `ExpressaoOu`

Cada um deverá combinar o resultado de outras expressões, formando uma árvore de sintaxe mais complexa.

Esse exercício demonstra um dos maiores benefícios do padrão Interpreter: **representar regras de negócio como objetos, permitindo criar linguagens específicas, flexíveis e extensíveis, sem depender de estruturas fixas no código-fonte.**
