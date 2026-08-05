# 20) State (Estado)

> **Categoria:** Comportamental

---

# O que é o padrão State?

O **State (Estado)** é um padrão de projeto comportamental utilizado quando um objeto possui **diferentes comportamentos dependendo do seu estado atual**.

A ideia principal é remover grandes estruturas condicionais como:

```ts
if (estado === 'ATIVO') {
} else if (estado === 'PAGO') {
} else if (estado === 'CANCELADO') {
}
```

e transformar cada estado em uma classe independente.

Em outras palavras:

> **O padrão State permite que um objeto altere seu comportamento quando seu estado interno muda.**

---

# Problema que o State resolve

Imagine um sistema de pedidos.

Um pedido pode estar em vários estados:

```text
CRIADO

↓

AGUARDANDO PAGAMENTO

↓

PAGO

↓

ENVIADO

↓

ENTREGUE
```

Cada estado possui regras diferentes.

Exemplo:

## Pedido criado

Pode:

- pagar;
- cancelar.

---

## Pedido pago

Pode:

- enviar;
- gerar nota fiscal.

Não pode:

- pagar novamente.

---

## Pedido cancelado

Não pode:

- alterar;
- pagar;
- enviar.

---

Uma implementação comum seria:

```ts
switch (estado) {
  case 'CRIADO':
    // regras

    break;

  case 'PAGO':
    // regras

    break;

  case 'CANCELADO':
    // regras

    break;
}
```

O problema é que conforme novos estados aparecem, esse código cresce muito.

O State transforma esses estados em objetos independentes.

---

# Motivação (cenário real)

Imagine um caixa eletrônico.

O comportamento muda conforme a situação:

```text
Sem cartão

↓

Cartão inserido

↓

Senha validada

↓

Dinheiro liberado
```

Quando o estado muda:

- novas ações ficam disponíveis;
- algumas ações deixam de existir.

O caixa eletrônico não precisa de vários `if`.

Ele apenas delega o comportamento para o estado atual.

---

# Analogia do mundo real

Imagine uma pessoa usando um celular.

O comportamento depende do modo atual:

## Modo normal

Permite:

- chamadas;
- internet;
- notificações.

---

## Modo avião

Permite:

- usar aplicativos offline.

Não permite:

- chamadas;
- dados móveis.

---

## Modo economia

Altera:

- brilho;
- desempenho;
- consumo.

O celular possui o mesmo aparelho, mas comportamentos diferentes dependendo do estado.

---

# Ideia central

O padrão possui três elementos principais:

```text
Context

↓

State

↓

Concrete States
```

O objeto principal mantém uma referência para o estado atual.

Quando recebe uma ação:

```text
Context

↓

Estado atual

↓

Executa comportamento
```

---

# Estrutura do padrão

```text
                 Cliente

                    |

                    ▼

              +------------+
              |  Context   |
              +------------+
              | estado     |
              +------+-----+
                     |
                     |
                     ▼

              +-------------+
              |   Estado    |
              +-------------+
              | executar()  |
              +------+------+
                     |
        -----------------------------
        |             |             |
        ▼             ▼             ▼

   Estado A      Estado B      Estado C
```

---

# Participantes

## 1. Context

É o objeto principal.

Responsável por:

- manter o estado atual;
- delegar operações;
- trocar estados.

Exemplo:

```text
PedidoContext
```

---

## 2. State

Define o contrato dos estados.

Normalmente possui métodos como:

```ts
executar();
```

ou:

```ts
handle();
```

---

## 3. Concrete State

Implementa comportamentos específicos.

Exemplos:

```text
PedidoAtivo

PedidoPago

PedidoCancelado
```

---

## 4. Cliente

Cria o contexto e define o estado inicial.

---

# Diagrama UML simplificado

```text
              +----------------+
              | PedidoContext  |
              +----------------+
              | estado         |
              +----------------+
                    |
                    |
                    ▼

              +----------------+
              | EstadoPedido   |
              +----------------+
              | handle()       |
              +----------------+
                    |
        ----------------------------
        |             |             |
        ▼             ▼             ▼

   AtivoState   PagoState   CanceladoState
```

---

# Implementação completa em TypeScript

```ts
/**
 * ============================================================
 * STATE
 * ============================================================
 *
 * Exemplo:
 *
 * Fluxo de pagamento de um pedido.
 *
 * O comportamento muda conforme
 * o estado atual do pedido.
 */

/**
 * Eventos possíveis.
 */

type EventoPedido = 'PAGAR' | 'CANCELAR';

/**
 * ============================================================
 * INTERFACE STATE
 * ============================================================
 */

interface EstadoPedido {
  executar(
    evento: EventoPedido,

    contexto: PedidoContext,
  ): void;

  obterNome(): string;
}

/**
 * ============================================================
 * CONTEXT
 * ============================================================
 *
 * Mantém o estado atual.
 */

class PedidoContext {
  constructor(private estado: EstadoPedido) {}

  alterarEstado(novoEstado: EstadoPedido): void {
    this.estado = novoEstado;

    console.log(`Novo estado: ${this.estado.obterNome()}`);
  }

  processarEvento(evento: EventoPedido): void {
    this.estado.executar(
      evento,

      this,
    );
  }

  obterEstadoAtual(): string {
    return this.estado.obterNome();
  }
}

/**
 * ============================================================
 * ESTADO: ATIVO
 * ============================================================
 */

class EstadoAtivo implements EstadoPedido {
  executar(
    evento: EventoPedido,

    contexto: PedidoContext,
  ): void {
    if (evento === 'PAGAR') {
      console.log('Iniciando pagamento...');

      contexto.alterarEstado(new EstadoAguardandoPagamento());
    }

    if (evento === 'CANCELAR') {
      console.log('Pedido cancelado.');

      contexto.alterarEstado(new EstadoCancelado());
    }
  }

  obterNome(): string {
    return 'ATIVO';
  }
}

/**
 * ============================================================
 * ESTADO: AGUARDANDO PAGAMENTO
 * ============================================================
 */

class EstadoAguardandoPagamento implements EstadoPedido {
  executar(
    evento: EventoPedido,

    contexto: PedidoContext,
  ): void {
    if (evento === 'PAGAR') {
      console.log('Pagamento aprovado.');

      contexto.alterarEstado(new EstadoPago());
    }

    if (evento === 'CANCELAR') {
      console.log('Pagamento cancelado.');

      contexto.alterarEstado(new EstadoCancelado());
    }
  }

  obterNome(): string {
    return 'AGUARDANDO_PAGAMENTO';
  }
}

/**
 * ============================================================
 * ESTADO: PAGO
 * ============================================================
 */

class EstadoPago implements EstadoPedido {
  executar(
    evento: EventoPedido,

    contexto: PedidoContext,
  ): void {
    if (evento === 'CANCELAR') {
      console.log('Pedido pago não pode ser cancelado.');

      return;
    }

    console.log('Pedido já está pago.');
  }

  obterNome(): string {
    return 'PAGO';
  }
}

/**
 * ============================================================
 * ESTADO: CANCELADO
 * ============================================================
 */

class EstadoCancelado implements EstadoPedido {
  executar(
    _evento: EventoPedido,

    _contexto: PedidoContext,
  ): void {
    console.log('Pedido cancelado. Nenhuma ação disponível.');
  }

  obterNome(): string {
    return 'CANCELADO';
  }
}

/**
 * ============================================================
 * CLIENTE
 * ============================================================
 */

const pedido = new PedidoContext(new EstadoAtivo());

pedido.processarEvento('PAGAR');

pedido.processarEvento('PAGAR');

pedido.processarEvento('CANCELAR');
```

---

# Saída esperada

```text
Iniciando pagamento...

Novo estado:
AGUARDANDO_PAGAMENTO


Pagamento aprovado.

Novo estado:
PAGO


Pedido pago não pode ser cancelado.
```

---

# Fluxo de execução (passo a passo)

## Passo 1

O pedido começa no estado:

```text
ATIVO
```

---

## Passo 2

O usuário solicita pagamento.

```ts
processarEvento('PAGAR');
```

---

## Passo 3

O Context chama o estado atual.

```text
PedidoContext

↓

EstadoAtivo
```

---

## Passo 4

O estado decide o comportamento.

Resultado:

```text
Alterar para:

AGUARDANDO_PAGAMENTO
```

---

## Passo 5

Novo evento:

```text
PAGAR
```

---

## Passo 6

O comportamento agora pertence a outro objeto.

```text
EstadoAguardandoPagamento
```

---

## Passo 7

O pedido muda novamente:

```text
PAGO
```

---

## Passo 8

Novas regras passam a valer.

---

# Fluxo interno

```text
Evento

↓

Context

↓

Estado atual

↓

Comportamento específico

↓

Novo estado
```

---

# Como reconhecer que é State?

Alguns sinais indicam esse padrão:

- O comportamento depende de um estado interno.
- Existem muitos `if` ou `switch` verificando estados.
- As regras mudam conforme a situação.
- O objeto possui uma máquina de estados.

Exemplos:

- Pedidos;
- Jogos;
- Fluxos de aprovação;
- Máquinas industriais;
- Processos financeiros;
- Conexões de rede.

---

# Vantagens

## Remove condicionais gigantes

Substitui:

```ts
switch(estado)
```

por classes especializadas.

---

## Organização

Cada estado possui sua própria lógica.

---

## Facilidade de manutenção

Alterar um estado não afeta os demais.

---

## Facilita evolução

Novos estados podem ser adicionados.

---

# Desvantagens

## Muitas classes

Cada estado normalmente vira uma classe.

---

## Pode parecer complexo

Para sistemas pequenos pode ser exagerado.

---

## Controle de transições

Muitos estados podem tornar as regras de mudança difíceis.

---

# Quando utilizar

Utilize State quando:

- um objeto possui vários estados;
- cada estado possui regras próprias;
- existem muitas condições baseadas em estado.

Exemplos:

- Workflow de documentos;
- Pedidos;
- Pagamentos;
- Jogos;
- Sistemas de autenticação;
- Protocolos de comunicação.

---

# Quando evitar

Evite quando:

- existem poucos estados;
- o comportamento quase não muda;
- um simples `if` resolve o problema.

---

# Relação com os princípios SOLID

## S — Single Responsibility Principle (SRP)

Cada estado possui uma única responsabilidade.

Exemplo:

```text
EstadoPago

↓

Somente regras de pagamento concluído
```

---

## O — Open/Closed Principle (OCP)

Novos estados podem ser adicionados sem alterar o Context.

---

## L — Liskov Substitution Principle (LSP)

Todos os estados podem substituir a interface:

```ts
EstadoPedido;
```

---

## I — Interface Segregation Principle (ISP)

A interface contém apenas operações necessárias aos estados.

---

## D — Dependency Inversion Principle (DIP)

O Context depende da abstração:

```ts
EstadoPedido;
```

e não de classes específicas.

---

# Anti-exemplo

Uma implementação problemática seria:

```ts
class Pedido {
  processar(estado: string) {
    switch (estado) {
      case 'ATIVO':
        // regras

        break;

      case 'PAGO':
        // regras

        break;

      case 'CANCELADO':
        // regras

        break;
    }
  }
}
```

Problemas:

- classe cresce indefinidamente;
- difícil adicionar estados;
- código fica cheio de condições.

Com State:

```text
Pedido

↓

Estado atual

↓

Classe especializada
```

Cada estado controla seu próprio comportamento.

---

# Exercício proposto

Implemente uma máquina de estados para um chamado de suporte.

## Estados:

```text
ABERTO

EM_ANALISE

RESOLVIDO

FECHADO
```

---

## Regras:

### Aberto

Permite:

```text
iniciar análise

fechar
```

---

### Em análise

Permite:

```text
resolver

cancelar
```

---

### Resolvido

Permite:

```text
fechar
```

---

### Fechado

Nenhuma alteração permitida.

---

## Desafio extra

Adicione:

- histórico de mudanças;
- data da alteração;
- usuário responsável pela mudança.

O objetivo é compreender como o padrão State transforma **objetos com muitos comportamentos condicionais em estruturas organizadas, extensíveis e fáceis de manter através de estados independentes.**
