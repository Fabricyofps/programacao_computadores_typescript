# 19) Observer (Observador)

> **Categoria:** Comportamental

---

# O que é o padrão Observer?

O **Observer (Observador)** é um padrão de projeto comportamental utilizado quando **um objeto precisa avisar automaticamente vários outros objetos quando alguma mudança acontece**.

O objeto que sofre a alteração é chamado de:

```text
Subject
```

ou:

```text
Observable
```

Os objetos interessados em receber notificações são chamados de:

```text
Observers
```

ou:

```text
Observadores
```

A principal ideia é criar uma relação:

```
Um para muitos
```

Ou seja:

> **Quando um objeto muda, todos os interessados são avisados automaticamente.**

---

# Problema que o Observer resolve

Imagine um sistema de monitoramento de preços.

Um produto possui o valor:

```text
Notebook

R$ 1500
```

Vários usuários desejam receber uma notificação quando o preço mudar.

Sem Observer, o sistema poderia fazer algo assim:

```ts
alterarPreco();

enviarEmail();

enviarSMS();

enviarPush();

atualizarDashboard();

atualizarRelatorio();
```

O problema é que o objeto Produto passa a conhecer todos os sistemas interessados.

Isso gera:

- alto acoplamento;
- dificuldade de manutenção;
- código difícil de expandir.

Se amanhã surgir:

```text
WhatsApp

↓

Novo tipo de alerta
```

seria necessário alterar o código existente.

O Observer resolve isso permitindo que novos observadores sejam adicionados sem modificar o objeto principal.

---

# Motivação (cenário real)

Imagine uma inscrição em um canal do YouTube.

Você segue um canal.

Quando um vídeo novo é publicado:

```
Canal publica vídeo

↓

Notifica inscritos

↓

Cada inscrito recebe o aviso
```

O canal não precisa conhecer cada usuário individualmente.

Ele apenas informa:

> "Existe uma nova atualização."

Cada inscrito decide como reagir.

Esse é exatamente o funcionamento do Observer.

---

# Analogia do mundo real

Imagine uma estação meteorológica.

Ela mede:

```text
Temperatura

Umidade

Pressão
```

Quando os dados mudam:

```
Estação

↓

Notifica:
- aplicativo
- painel público
- celular
- televisão
```

A estação não precisa saber como cada sistema funciona.

Ela apenas envia a atualização.

---

# Ideia central

O padrão possui dois grupos principais:

## Subject

Objeto observado.

Responsável por:

- armazenar observadores;
- adicionar observadores;
- remover observadores;
- enviar notificações.

---

## Observer

Objeto interessado nas mudanças.

Responsável por:

- receber notificações;
- reagir aos eventos.

---

# Estrutura do padrão

```text
                 Subject

        +--------------------+

        | lista Observadores |

        +---------+----------+

                  |

                  |

        notificar(evento)

                  |

     ----------------------------

     |             |            |

     ▼             ▼            ▼


 Observer A   Observer B   Observer C
```

---

# Participantes

## 1. Subject

É o objeto que possui o estado.

Exemplo:

```text
Produto
```

ou:

```text
Canal
```

Ele mantém uma lista de interessados.

---

## 2. Observer

Define o contrato de notificação.

Normalmente possui um método:

```ts
atualizar();
```

ou:

```ts
update();
```

---

## 3. Concrete Observer

Implementa uma reação específica.

Exemplos:

```text
EnviarEmail

EnviarSMS

EnviarPush
```

---

## 4. Cliente

Configura a relação:

```
Subject

+

Observers
```

---

# Diagrama UML simplificado

```text
              +----------------+
              |    Subject     |
              +----------------+
              | observadores[] |
              +-------+--------+
                      |
                      |
              notificar()
                      |
        --------------------------------
        |              |               |
        ▼              ▼               ▼

+-------------+ +-------------+ +-------------+
| EmailAlert  | | PushAlert   | | SMSAlert    |
+-------------+ +-------------+ +-------------+
| update()    | | update()    | | update()    |
+-------------+ +-------------+ +-------------+
```

---

# Implementação completa em TypeScript

```ts
/**
 * ============================================================
 * OBSERVER
 * ============================================================
 *
 * Exemplo:
 *
 * Sistema de alerta de preço.
 *
 * Quando o preço de um produto aumenta,
 * todos os interessados recebem uma notificação.
 */

/**
 * ============================================================
 * INTERFACE OBSERVER
 * ============================================================
 */

interface Observador {
  atualizar(
    evento: string,

    dados?: unknown,
  ): void;
}

/**
 * ============================================================
 * SUBJECT
 * ============================================================
 *
 * Mantém os observadores
 * e envia notificações.
 */

class Produto {
  private observadores: Observador[] = [];

  constructor(
    private nome: string,

    private preco: number,
  ) {}

  adicionarObservador(observador: Observador): void {
    this.observadores.push(observador);
  }

  removerObservador(observador: Observador): void {
    this.observadores = this.observadores.filter((item) => item !== observador);
  }

  alterarPreco(novoPreco: number): void {
    this.preco = novoPreco;

    console.log(`Preço alterado para ${novoPreco}`);

    this.notificar(
      'PRECO_ALTERADO',

      {
        produto: this.nome,

        preco: novoPreco,
      },
    );
  }

  private notificar(
    evento: string,

    dados?: unknown,
  ): void {
    for (const observador of this.observadores) {
      observador.atualizar(
        evento,

        dados,
      );
    }
  }
}

/**
 * ============================================================
 * OBSERVADORES CONCRETOS
 * ============================================================
 */

/**
 * Envia alertas por email.
 */

class AlertaEmail implements Observador {
  atualizar(
    evento: string,

    dados?: unknown,
  ): void {
    if (evento === 'PRECO_ALTERADO') {
      console.log(
        '[EMAIL] Enviando alerta:',

        dados,
      );
    }
  }
}

/**
 * Envia notificações push.
 */

class AlertaPush implements Observador {
  atualizar(
    evento: string,

    dados?: unknown,
  ): void {
    if (evento === 'PRECO_ALTERADO') {
      console.log(
        '[PUSH] Enviando notificação:',

        dados,
      );
    }
  }
}

/**
 * Atualiza dashboard.
 */

class DashboardPreco implements Observador {
  atualizar(
    evento: string,

    dados?: unknown,
  ): void {
    if (evento === 'PRECO_ALTERADO') {
      console.log(
        '[DASHBOARD] Atualizando dados:',

        dados,
      );
    }
  }
}

/**
 * ============================================================
 * CLIENTE
 * ============================================================
 */

const produto = new Produto(
  'Notebook',

  1500,
);

produto.adicionarObservador(new AlertaEmail());

produto.adicionarObservador(new AlertaPush());

produto.adicionarObservador(new DashboardPreco());

produto.alterarPreco(2000);
```

---

# Saída esperada

```text
Preço alterado para 2000

[EMAIL] Enviando alerta:
{
 produto: "Notebook",
 preco: 2000
}

[PUSH] Enviando notificação:
{
 produto: "Notebook",
 preco: 2000
}

[DASHBOARD] Atualizando dados:
{
 produto: "Notebook",
 preco: 2000
}
```

---

# Fluxo de execução (passo a passo)

## Passo 1

O produto é criado.

```text
Notebook

Preço:

1500
```

---

## Passo 2

Observadores são registrados.

```text
Produto

↓

Email

↓

Push

↓

Dashboard
```

---

## Passo 3

O preço muda.

```ts
alterarPreco(2000);
```

---

## Passo 4

O Subject identifica a alteração.

---

## Passo 5

O Subject percorre sua lista.

```text
Observador 1

↓

Observador 2

↓

Observador 3
```

---

## Passo 6

Cada observador recebe:

```ts
atualizar();
```

---

## Passo 7

Cada observador decide como reagir.

Exemplo:

```text
Email

↓

Enviar mensagem
```

```text
Push

↓

Enviar notificação
```

---

# Fluxo interno

```text
Mudança no estado

↓

Subject

↓

Notificar

↓

Observers

↓

Reações individuais
```

---

# Como reconhecer que é Observer?

Alguns sinais indicam claramente esse padrão:

- Um objeto muda e vários precisam saber.
- Existe uma relação um-para-muitos.
- Novos interessados podem ser adicionados dinamicamente.
- O objeto principal não deve conhecer detalhes dos consumidores.

Exemplos:

- Sistemas de eventos;
- Notificações;
- Mensageria;
- Interfaces gráficas;
- Monitoramento;
- Sistemas financeiros.

---

# Vantagens

## Baixo acoplamento

O Subject não conhece detalhes dos observadores.

---

## Fácil extensão

Novos observadores podem ser adicionados.

Exemplo:

```text
Novo:

WhatsAppAlert
```

Sem alterar o Produto.

---

## Comunicação automática

Os interessados recebem atualizações automaticamente.

---

## Flexibilidade

Um mesmo evento pode gerar várias ações diferentes.

---

# Desvantagens

## Muitos eventos

Um único evento pode disparar várias ações inesperadas.

---

## Dificuldade de depuração

Pode ser difícil descobrir quem recebeu determinado evento.

---

## Ordem de execução

A ordem dos observadores pode influenciar o resultado.

---

# Quando utilizar

Utilize Observer quando:

- vários objetos dependem de outro;
- mudanças precisam ser propagadas automaticamente;
- existir sistema baseado em eventos.

Exemplos comuns:

- Sistemas de notificações;
- React/Vue/Angular;
- Eventos de interface;
- Marketplaces;
- Sistemas de monitoramento;
- Brokers de mensagens;
- WebSockets.

---

# Quando evitar

Evite utilizar quando:

- existir apenas um consumidor;
- a comunicação for simples;
- a ordem das chamadas precisar ser totalmente previsível.

---

# Relação com os princípios SOLID

## S — Single Responsibility Principle (SRP)

Cada observador possui sua própria responsabilidade.

Exemplo:

```text
Email

↓

Somente envia emails
```

---

## O — Open/Closed Principle (OCP)

Novos observadores podem ser adicionados sem alterar o Subject.

---

## L — Liskov Substitution Principle (LSP)

Qualquer observador pode substituir outro desde que implemente a interface.

---

## I — Interface Segregation Principle (ISP)

A interface define apenas o necessário:

```ts
atualizar();
```

---

## D — Dependency Inversion Principle (DIP)

O Subject depende da abstração:

```ts
Observador;
```

e não de classes concretas.

---

# Anti-exemplo

Um erro comum é criar dependências diretas.

```ts
class Produto {
  alterarPreco() {
    email.enviar();

    push.enviar();

    dashboard.atualizar();
  }
}
```

Problemas:

- Produto conhece todos os sistemas.
- Adicionar um novo alerta exige alterar Produto.
- Alto acoplamento.

Com Observer:

```text
Produto

↓

Observador

↓

Qualquer implementação
```

O objeto principal permanece independente.

---

# Exercício proposto

Implemente um sistema de acompanhamento de pedidos.

## Requisitos

Crie um Subject:

```ts
Pedido;
```

Ele deverá possuir estados:

```text
CRIADO

PAGO

ENVIADO

ENTREGUE
```

---

Crie observadores:

```ts
EmailCliente;
```

Envia atualização por email.

---

```ts
SistemaLogistica;
```

Atualiza o transporte.

---

```ts
AplicativoMobile;
```

Envia notificações push.

---

## Desafio extra

Implemente um sistema de eventos genérico:

```ts
EventBus;
```

Que permita:

```ts
eventBus.on();

eventBus.emit();
```

Exemplo:

```ts
eventBus.emit(
  'PEDIDO_PAGO',

  pedido,
);
```

O objetivo é compreender uma das maiores vantagens do padrão Observer:

**criar sistemas orientados a eventos, onde objetos podem reagir automaticamente às mudanças sem criar dependências diretas entre componentes.**
