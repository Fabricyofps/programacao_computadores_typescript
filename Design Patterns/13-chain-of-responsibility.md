# 13) Chain of Responsibility (Cadeia de Responsabilidade)

> **Categoria:** Comportamental

---

# O que é o padrão Chain of Responsibility?

O **Chain of Responsibility (Cadeia de Responsabilidade)** é um padrão de projeto comportamental que permite **encadear vários objetos responsáveis por processar uma solicitação**, dando a cada um deles a oportunidade de tratar essa solicitação ou passá-la para o próximo da cadeia.

Cada objeto da cadeia conhece apenas:

- sua própria responsabilidade;
- quem é o próximo elemento da sequência.

Ele **não precisa conhecer toda a cadeia**, apenas quem vem logo depois.

Em outras palavras:

> **Uma solicitação percorre uma sequência de objetos até que seja processada ou até que a cadeia termine.**

Esse padrão elimina grandes estruturas de decisão como:

```ts
if (...)
else if (...)
else if (...)
else if (...)
```

e também reduz o acoplamento entre quem faz a requisição e quem realmente a processa.

---

# Problema que o Chain of Responsibility resolve

Imagine um sistema de aprovação de pedidos.

Antes que um pedido seja enviado, várias validações precisam acontecer.

Por exemplo:

- Verificar estoque;
- Validar pagamento;
- Confirmar endereço;
- Verificar limite de crédito;
- Aplicar regras antifraude;
- Calcular frete;
- Autorizar expedição.

Sem o padrão Chain of Responsibility, normalmente encontramos algo parecido com isto:

```ts
if (!pedido.temEstoque) {
    ...
}

if (!pedido.pagamentoOk) {
    ...
}

if (!pedido.enderecoValido) {
    ...
}

if (!pedido.antifraudeAprovado) {
    ...
}
```

Com o tempo aparecem novas regras.

O método cresce.

Depois cresce novamente.

Até se transformar em uma função enorme difícil de manter.

O Chain of Responsibility resolve esse problema dividindo cada validação em uma classe independente.

---

# Motivação (cenário real)

Imagine um aeroporto.

Antes de embarcar, o passageiro passa por diversas etapas.

```
Recepção

↓

Check-in

↓

Despacho de bagagem

↓

Raio-X

↓

Polícia Federal

↓

Portão de embarque
```

Cada setor possui apenas uma responsabilidade.

Quando termina seu trabalho, encaminha o passageiro para o próximo.

Nenhum funcionário precisa conhecer todas as etapas.

Essa é exatamente a ideia do Chain of Responsibility.

---

# Analogia do mundo real

Imagine um atendimento em uma empresa.

Você liga para o suporte.

Primeiro atende a recepcionista.

Se ela não puder resolver:

↓

Encaminha para o suporte técnico.

↓

Se necessário:

↓

Encaminha para o setor financeiro.

↓

Depois:

↓

Gerente.

Cada pessoa decide:

- resolver;
- ou encaminhar para o próximo.

---

# Estrutura do padrão

```text
               Cliente
                  │
                  ▼
      Handler 1 (Estoque)
                  │
                  ▼
    Handler 2 (Pagamento)
                  │
                  ▼
     Handler 3 (Entrega)
                  │
                  ▼
          Handler Final
```

---

# Participantes

## 1. Cliente

É quem inicia a solicitação.

Ele conhece apenas o primeiro elemento da cadeia.

---

## 2. Handler (Manipulador)

É a classe abstrata responsável por definir:

- quem é o próximo handler;
- como a solicitação será encaminhada.

---

## 3. Concrete Handler (Manipulador Concreto)

Implementa uma regra específica.

Exemplos:

- Estoque
- Pagamento
- Antifraude
- Frete
- Entrega

Cada um decide:

- processar;
- interromper;
- ou encaminhar.

---

## 4. Próximo Handler

Representa o próximo elemento da cadeia.

---

# Diagrama UML simplificado

```text
                 +---------------------------+
                 |        Handler            |
                 +---------------------------+
                 | - proximo                |
                 | + definirProximo()       |
                 | + tratar()               |
                 +-------------▲------------+
                               |
      -------------------------------------------------------
      |                    |                    |             |
      ▼                    ▼                    ▼             ▼
+--------------+   +---------------+   +---------------+   +--------------+
| Estoque      |   | Pagamento     |   | Entrega       |   | Final        |
+--------------+   +---------------+   +---------------+   +--------------+
| tratar()     |   | tratar()      |   | tratar()      |   | tratar()     |
+--------------+   +---------------+   +---------------+   +--------------+
```

---

# Implementação completa em TypeScript

```ts
/**
 * ============================================================
 * CHAIN OF RESPONSIBILITY
 * ============================================================
 *
 * Neste exemplo criaremos uma cadeia
 * de validação para pedidos.
 *
 * Cada etapa poderá:
 *
 * - Aprovar
 * - Reprovar
 * - Encaminhar para a próxima
 */

/**
 * Representa um pedido.
 */
type Pedido = {
  id: string;

  valor: number;

  possuiEstoque: boolean;

  pagamentoAprovado: boolean;

  enderecoValido: boolean;
};

/**
 * Resultado do processamento.
 */
type Resultado =
  | {
      sucesso: true;

      mensagem: string;
    }
  | {
      sucesso: false;

      erro: string;
    };

/**
 * ============================================================
 * HANDLER
 * ============================================================
 */

abstract class ManipuladorPedido {
  private proximo: ManipuladorPedido | null = null;

  /**
   * Define o próximo manipulador.
   */
  definirProximo(manipulador: ManipuladorPedido): ManipuladorPedido {
    this.proximo = manipulador;

    return manipulador;
  }

  /**
   * Método responsável por percorrer
   * toda a cadeia.
   */
  tratar(pedido: Pedido): Resultado {
    const resultado = this.processar(pedido);

    if (!resultado.sucesso) {
      return resultado;
    }

    if (this.proximo) {
      return this.proximo.tratar(pedido);
    }

    return resultado;
  }

  protected abstract processar(pedido: Pedido): Resultado;
}

/**
 * ============================================================
 * VALIDA ESTOQUE
 * ============================================================
 */

class ValidadorEstoque extends ManipuladorPedido {
  protected processar(pedido: Pedido): Resultado {
    console.log('Validando estoque...');

    if (!pedido.possuiEstoque) {
      return {
        sucesso: false,

        erro: 'Produto sem estoque.',
      };
    }

    return {
      sucesso: true,

      mensagem: 'Estoque aprovado.',
    };
  }
}

/**
 * ============================================================
 * VALIDA PAGAMENTO
 * ============================================================
 */

class ValidadorPagamento extends ManipuladorPedido {
  protected processar(pedido: Pedido): Resultado {
    console.log('Validando pagamento...');

    if (!pedido.pagamentoAprovado) {
      return {
        sucesso: false,

        erro: 'Pagamento recusado.',
      };
    }

    return {
      sucesso: true,

      mensagem: 'Pagamento aprovado.',
    };
  }
}

/**
 * ============================================================
 * VALIDA ENDEREÇO
 * ============================================================
 */

class ValidadorEndereco extends ManipuladorPedido {
  protected processar(pedido: Pedido): Resultado {
    console.log('Validando endereço...');

    if (!pedido.enderecoValido) {
      return {
        sucesso: false,

        erro: 'Endereço inválido.',
      };
    }

    return {
      sucesso: true,

      mensagem: 'Endereço validado.',
    };
  }
}

/**
 * ============================================================
 * HANDLER FINAL
 * ============================================================
 */

class PedidoAprovado extends ManipuladorPedido {
  protected processar(_pedido: Pedido): Resultado {
    return {
      sucesso: true,

      mensagem: 'Pedido pronto para expedição.',
    };
  }
}

/**
 * ============================================================
 * CLIENTE
 * ============================================================
 */

const estoque = new ValidadorEstoque();

const pagamento = new ValidadorPagamento();

const endereco = new ValidadorEndereco();

const aprovado = new PedidoAprovado();

/**
 * Montagem da cadeia.
 */
estoque

  .definirProximo(pagamento)

  .definirProximo(endereco)

  .definirProximo(aprovado);

/**
 * Pedido aprovado.
 */
const pedido1 = {
  id: 'PED001',

  valor: 500,

  possuiEstoque: true,

  pagamentoAprovado: true,

  enderecoValido: true,
};

console.log(estoque.tratar(pedido1));

/**
 * Pedido recusado.
 */
const pedido2 = {
  id: 'PED002',

  valor: 300,

  possuiEstoque: true,

  pagamentoAprovado: false,

  enderecoValido: true,
};

console.log(estoque.tratar(pedido2));
```

---

# Fluxo de execução (passo a passo)

## Passo 1

O cliente monta a cadeia.

```text
Estoque

↓

Pagamento

↓

Endereço

↓

Pedido Aprovado
```

---

## Passo 2

O cliente envia um pedido.

```ts
estoque.tratar(pedido);
```

Observe que o cliente conhece apenas o primeiro manipulador.

---

## Passo 3

O primeiro handler verifica o estoque.

```
Existe estoque?

↓

Não

↓

Interrompe a cadeia
```

ou

```
Sim

↓

Próximo handler
```

---

## Passo 4

O segundo handler valida o pagamento.

```
Pagamento aprovado?

↓

Não

↓

Fim da cadeia
```

ou

```
Sim

↓

Próximo
```

---

## Passo 5

O terceiro handler valida o endereço.

```
Endereço válido?

↓

Sim

↓

Próximo
```

---

## Passo 6

O último handler informa:

```
Pedido aprovado.
```

---

# Fluxo interno

```text
Cliente

↓

ValidadorEstoque

↓

ValidadorPagamento

↓

ValidadorEndereco

↓

PedidoAprovado
```

Caso alguma etapa falhe:

```text
Cliente

↓

ValidadorEstoque

↓

ValidadorPagamento

↓

Erro

↓

Fim
```

A cadeia é interrompida imediatamente.

---

# Como reconhecer que é Chain of Responsibility?

Alguns sinais indicam claramente esse padrão:

- Existe uma sequência de objetos.
- Cada objeto possui apenas uma responsabilidade.
- Cada etapa decide se continua ou interrompe o processamento.
- O cliente conhece apenas o primeiro elemento.
- Novas etapas podem ser adicionadas facilmente.

---

# Vantagens

## Baixo acoplamento

O cliente não conhece as implementações das validações.

---

## Fácil extensão

Novas regras podem ser adicionadas sem modificar as existentes.

---

## Reutilização

Os manipuladores podem ser reutilizados em diferentes cadeias.

---

## Organização

Cada regra fica isolada em uma classe específica.

---

## Fácil manutenção

Alterar uma validação não afeta as demais.

---

# Desvantagens

## Fluxo menos explícito

Pode ser mais difícil descobrir em qual etapa a solicitação foi interrompida.

---

## Muitas classes

Cada regra geralmente gera um novo manipulador.

---

## Ordem importante

Uma ordem incorreta na cadeia pode gerar comportamentos inesperados.

---

# Quando utilizar

Utilize Chain of Responsibility quando:

- várias validações precisam ser executadas;
- o processamento ocorre em etapas;
- novas regras surgem frequentemente;
- deseja evitar grandes blocos de `if/else`;
- diferentes fluxos precisam compartilhar partes da lógica.

Exemplos comuns:

- Validação de pedidos;
- Processamento de pagamentos;
- Middleware em APIs;
- Filtros HTTP;
- Sistemas de autenticação;
- Aprovação de documentos;
- Processamento de formulários;
- Pipelines de dados.

---

# Quando evitar

Evite utilizar quando:

- existir apenas uma validação simples;
- todas as regras precisarem conhecer umas às outras;
- a sequência nunca mudar e possuir poucas etapas.

Nesses casos, uma função simples pode ser suficiente.

---

# Relação com os princípios SOLID

## S — Single Responsibility Principle (SRP)

Cada manipulador executa apenas uma validação.

Exemplo:

- estoque;
- pagamento;
- endereço.

---

## O — Open/Closed Principle (OCP)

Novos manipuladores podem ser adicionados sem alterar os existentes.

---

## L — Liskov Substitution Principle (LSP)

Qualquer manipulador pode substituir outro desde que respeite o contrato da classe base.

---

## I — Interface Segregation Principle (ISP)

A classe abstrata define apenas os métodos necessários para o processamento da cadeia.

---

## D — Dependency Inversion Principle (DIP)

O cliente depende da abstração (`ManipuladorPedido`) e não das implementações concretas.

---

# Anti-exemplo

Um erro comum é concentrar todas as validações em um único método.

```ts
function validarPedido(pedido: Pedido) {
  if (!pedido.possuiEstoque) {
    return 'Sem estoque';
  }

  if (!pedido.pagamentoAprovado) {
    return 'Pagamento recusado';
  }

  if (!pedido.enderecoValido) {
    return 'Endereço inválido';
  }

  // dezenas de novas validações...
}
```

Com o crescimento do sistema, esse método se torna difícil de ler, testar e manter.

---

# Exercício proposto

Implemente um sistema de aprovação de empréstimos utilizando o padrão Chain of Responsibility.

### Requisitos

Crie os seguintes manipuladores:

- `ValidadorCpf`
- `ValidadorRenda`
- `ValidadorScoreCredito`
- `ValidadorDocumentacao`
- `EmprestimoAprovado`

Cada manipulador deverá verificar uma condição específica.

Se a validação falhar:

- interrompa imediatamente a cadeia;
- retorne o motivo da reprovação.

Caso todas as etapas sejam aprovadas:

```text
Empréstimo aprovado.
```

---

## Desafio extra

Implemente um manipulador chamado:

```ts
ValidadorFraude;
```

Ele deverá verificar se o cliente possui alguma suspeita de fraude.

Adicione esse manipulador **sem modificar nenhuma das classes existentes**, apenas reorganizando a montagem da cadeia no cliente.

O objetivo é demonstrar uma das maiores vantagens do padrão Chain of Responsibility: **adicionar novas regras de processamento de forma flexível, desacoplada e sem alterar o código dos manipuladores já existentes.**
