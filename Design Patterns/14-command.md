# 14) Command (Comando)

> **Categoria:** Comportamental

---

# O que é o padrão Command?

O **Command (Comando)** é um padrão de projeto comportamental que transforma **uma solicitação ou ação em um objeto**.

Em vez de executar uma operação diretamente, criamos um objeto responsável por representar aquela ação.

Esse objeto pode ser:

- executado;
- armazenado;
- enfileirado;
- registrado em log;
- desfeito (_Undo_);
- refeito (_Redo_);
- enviado pela rede;
- agendado para execução futura.

Em outras palavras:

> **O Command encapsula uma operação inteira dentro de um objeto.**

Essa ideia desacopla completamente quem solicita uma ação de quem realmente sabe executá-la.

---

# Problema que o Command resolve

Imagine um sistema de e-commerce.

Existem vários botões:

- Adicionar produto
- Remover produto
- Aplicar cupom
- Finalizar compra
- Cancelar pedido

Sem Command, cada botão executa diretamente sua lógica.

```text
Botão

↓

Carrinho

↓

Banco

↓

Atualiza tela
```

Agora imagine que o sistema precisa implementar:

- histórico de ações;
- desfazer operações;
- refazer operações;
- filas de execução;
- agendamento de comandos;
- auditoria.

O código começa a ficar bastante complexo.

O padrão Command resolve esse problema encapsulando cada ação em uma classe própria.

---

# Motivação (cenário real)

Imagine um editor de texto.

Você digita:

```
Olá Mundo
```

Depois pressiona:

```
CTRL + Z
```

O texto volta ao estado anterior.

Depois:

```
CTRL + Y
```

O texto reaparece.

Como o programa sabe exatamente o que desfazer?

Porque cada ação realizada foi armazenada como um objeto.

Cada objeto sabe:

- como executar;
- como desfazer.

Esse é exatamente o padrão Command.

---

# Analogia do mundo real

Imagine um restaurante.

O cliente faz um pedido.

O garçom não prepara a comida.

Ele apenas escreve o pedido.

Esse pedido é entregue para a cozinha.

A cozinha sabe como executá-lo.

O pedido representa exatamente um **Command**.

O garçom representa o **Invoker**.

A cozinha representa o **Receiver**.

---

# Estrutura do padrão

```text
             Cliente
                 │
                 ▼
            Cria Command
                 │
                 ▼
             Invoker
                 │
          execute()
                 │
                 ▼
             Receiver
```

---

# Participantes

## 1. Command (Comando)

Define a interface comum para todos os comandos.

Normalmente possui:

```ts
executar();
```

e opcionalmente:

```ts
desfazer();
```

---

## 2. Concrete Command (Comando Concreto)

Implementa uma ação específica.

Exemplos:

- AdicionarProduto
- RemoverProduto
- AplicarCupom
- CancelarPedido

---

## 3. Receiver (Receptor)

É quem realmente sabe executar o trabalho.

O Command apenas delega para ele.

---

## 4. Invoker

É quem dispara os comandos.

Também pode:

- armazenar histórico;
- desfazer;
- refazer;
- registrar logs.

---

## 5. Cliente

Cria os comandos.

Escolhe quais comandos serão executados.

---

# Diagrama UML simplificado

```text
                     +------------------------+
                     |      Comando           |
                     +------------------------+
                     | + executar()           |
                     | + desfazer()           |
                     +------------▲-----------+
                                  |
          ---------------------------------------------
          |                                           |
          ▼                                           ▼
+--------------------------+          +---------------------------+
| RemoverItemCommand       |          | AdicionarItemCommand      |
+--------------------------+          +---------------------------+
| executar()               |          | executar()                |
| desfazer()               |          | desfazer()                |
+-------------+------------+          +-------------+-------------+
              |                                     |
              ▼                                     ▼
        +-----------------------------------------------+
        |             ServicoCarrinho                   |
        +-----------------------------------------------+

                     ▲
                     |
              ControleHistorico
```

---

# Implementação completa em TypeScript

```ts
/**
 * ============================================================
 * COMMAND (COMANDO)
 * ============================================================
 *
 * Neste exemplo construiremos um sistema
 * de carrinho de compras com suporte
 * a Undo.
 */

/**
 * Modelo do carrinho.
 */
type Carrinho = {
  itens: string[];
};

/**
 * ============================================================
 * RECEIVER
 * ============================================================
 *
 * Esta classe conhece as regras
 * para manipular o carrinho.
 */

class ServicoCarrinho {
  adicionarItem(
    carrinho: Carrinho,

    item: string,
  ): void {
    carrinho.itens.push(item);
  }

  removerItem(
    carrinho: Carrinho,

    item: string,
  ): void {
    carrinho.itens = carrinho.itens.filter((produto) => produto !== item);
  }
}

/**
 * ============================================================
 * INTERFACE COMMAND
 * ============================================================
 */

interface Comando {
  executar(): void;

  desfazer?(): void;
}

/**
 * ============================================================
 * COMANDO
 * Remover Item
 * ============================================================
 */

class ComandoRemoverItem implements Comando {
  private itemRemovido: string | null = null;

  constructor(
    private carrinho: Carrinho,

    private servico: ServicoCarrinho,

    private item: string,
  ) {}

  executar(): void {
    console.log(`Removendo ${this.item}`);

    if (this.carrinho.itens.includes(this.item)) {
      this.itemRemovido = this.item;

      this.servico.removerItem(
        this.carrinho,

        this.item,
      );
    }
  }

  desfazer(): void {
    if (!this.itemRemovido) {
      return;
    }

    console.log(`Desfazendo remoção de ${this.itemRemovido}`);

    this.servico.adicionarItem(
      this.carrinho,

      this.itemRemovido,
    );

    this.itemRemovido = null;
  }
}

/**
 * ============================================================
 * COMANDO
 * Adicionar Item
 * ============================================================
 */

class ComandoAdicionarItem implements Comando {
  constructor(
    private carrinho: Carrinho,

    private servico: ServicoCarrinho,

    private item: string,
  ) {}

  executar(): void {
    console.log(`Adicionando ${this.item}`);

    this.servico.adicionarItem(
      this.carrinho,

      this.item,
    );
  }

  desfazer(): void {
    console.log(`Desfazendo adição de ${this.item}`);

    this.servico.removerItem(
      this.carrinho,

      this.item,
    );
  }
}

/**
 * ============================================================
 * INVOKER
 * ============================================================
 *
 * Responsável por executar
 * os comandos e manter
 * o histórico.
 */

class ControleHistorico {
  private historico: Comando[] = [];

  private indice = -1;

  executar(comando: Comando): void {
    /**
     * Remove comandos
     * que estavam no Redo.
     */
    this.historico = this.historico.slice(
      0,

      this.indice + 1,
    );

    comando.executar();

    this.historico.push(comando);

    this.indice++;
  }

  desfazer(): void {
    if (this.indice < 0) {
      return;
    }

    const comando = this.historico[this.indice];

    comando.desfazer?.();

    this.indice--;
  }
}

/**
 * ============================================================
 * CLIENTE
 * ============================================================
 */

const carrinho: Carrinho = {
  itens: ['Notebook', 'Mouse'],
};

const servico = new ServicoCarrinho();

const controle = new ControleHistorico();

controle.executar(
  new ComandoAdicionarItem(
    carrinho,

    servico,

    'Teclado',
  ),
);

console.log(carrinho.itens);

controle.executar(
  new ComandoRemoverItem(
    carrinho,

    servico,

    'Mouse',
  ),
);

console.log(carrinho.itens);

controle.desfazer();

console.log(carrinho.itens);

controle.desfazer();

console.log(carrinho.itens);
```

---

# Saída esperada

```text
Adicionando Teclado

[ 'Notebook', 'Mouse', 'Teclado' ]

Removendo Mouse

[ 'Notebook', 'Teclado' ]

Desfazendo remoção de Mouse

[ 'Notebook', 'Teclado', 'Mouse' ]

Desfazendo adição de Teclado

[ 'Notebook', 'Mouse' ]
```

---

# Fluxo de execução (passo a passo)

## Passo 1

O cliente cria um comando.

```ts
new ComandoAdicionarItem(...)
```

---

## Passo 2

O comando é enviado ao Invoker.

```text
Cliente

↓

ControleHistorico
```

---

## Passo 3

O Invoker chama:

```ts
executar();
```

---

## Passo 4

O comando delega a operação ao Receiver.

```text
Comando

↓

ServicoCarrinho
```

---

## Passo 5

O Receiver altera o carrinho.

---

## Passo 6

O comando é armazenado no histórico.

```text
Histórico

↓

Adicionar Produto

↓

Remover Produto
```

---

## Passo 7

Quando o usuário solicita:

```text
CTRL + Z
```

O Invoker recupera o último comando.

↓

Executa:

```ts
desfazer();
```

↓

O estado anterior é restaurado.

---

# Fluxo interno

```text
Cliente

↓

Invoker

↓

Command

↓

Receiver

↓

Carrinho
```

Quando ocorre um Undo:

```text
Histórico

↓

Último Command

↓

desfazer()

↓

Receiver
```

---

# Como reconhecer que é Command?

Alguns sinais indicam claramente esse padrão:

- Uma ação é representada por um objeto.
- Existe uma interface comum para comandos.
- Há um objeto responsável por executar comandos.
- Existe histórico de operações.
- O sistema suporta Undo ou Redo.

---

# Vantagens

## Desacoplamento

Quem solicita uma ação não conhece sua implementação.

---

## Undo e Redo

Cada comando pode desfazer sua própria execução.

---

## Histórico

Todas as operações podem ser armazenadas.

---

## Agendamento

Comandos podem ser executados futuramente.

---

## Filas

Comandos podem ser enviados para filas de processamento.

---

## Auditoria

É fácil registrar tudo o que foi executado.

---

# Desvantagens

## Muitas classes

Cada ação normalmente gera um novo comando.

---

## Código mais extenso

Para operações muito simples, pode parecer exagerado.

---

## Histórico consome memória

Quanto maior o histórico, maior será o consumo de memória.

---

# Quando utilizar

Utilize Command quando:

- precisar implementar Undo/Redo;
- desejar registrar histórico;
- precisar enfileirar operações;
- executar ações remotamente;
- agendar tarefas;
- desacoplar quem solicita de quem executa.

Exemplos comuns:

- Editores de texto;
- Photoshop;
- IDEs;
- Sistemas de automação;
- Macros;
- Botões de interfaces gráficas;
- Filas de processamento;
- Sistemas bancários.

---

# Quando evitar

Evite utilizar quando:

- existir apenas uma operação simples;
- não houver necessidade de histórico;
- a criação de várias classes aumentar a complexidade sem benefícios.

---

# Relação com os princípios SOLID

## S — Single Responsibility Principle (SRP)

Cada comando representa apenas uma ação.

Exemplo:

- adicionar item;
- remover item;
- aplicar cupom.

---

## O — Open/Closed Principle (OCP)

Novos comandos podem ser adicionados sem alterar o Invoker.

---

## L — Liskov Substitution Principle (LSP)

Qualquer comando pode substituir outro, desde que implemente a interface `Comando`.

---

## I — Interface Segregation Principle (ISP)

A interface define apenas as operações essenciais (`executar` e, opcionalmente, `desfazer`).

---

## D — Dependency Inversion Principle (DIP)

O Invoker depende da abstração `Comando`, e não das implementações concretas.

---

# Anti-exemplo

Um erro comum é fazer os botões chamarem diretamente o serviço.

```ts
botaoAdicionar.onclick = () => {

    servicoCarrinho.adicionarItem(...);

};

botaoRemover.onclick = () => {

    servicoCarrinho.removerItem(...);

};
```

Nesse caso:

- não existe histórico;
- não há Undo;
- não é possível agendar ações;
- fica difícil registrar logs ou auditoria.

---

# Exercício proposto

Implemente um sistema de conta bancária utilizando o padrão Command.

### Requisitos

Crie a interface:

```ts
ComandoBancario;
```

Implemente os comandos:

- `ComandoDepositar`
- `ComandoSacar`
- `ComandoTransferir`

Crie também o Receiver:

```ts
ContaBancaria;
```

Ela deverá possuir:

- saldo;
- depositar();
- sacar();
- transferir().

Depois implemente um Invoker chamado:

```ts
HistoricoOperacoes;
```

Ele deverá:

- executar comandos;
- manter histórico;
- permitir desfazer a última operação.

---

## Exemplo esperado

```text
Saldo inicial:

R$ 1000

↓

Depositar R$ 500

↓

Saldo:

R$ 1500

↓

Undo

↓

Saldo:

R$ 1000
```

---

## Desafio extra

Implemente suporte a **Redo**.

Adicione um método:

```ts
refazer();
```

Sempre que um usuário executar:

```text
CTRL + Z
```

o comando deverá ser desfeito.

Quando executar:

```text
CTRL + Y
```

o mesmo comando deverá ser executado novamente.

O histórico deverá funcionar exatamente como ocorre em editores como **Visual Studio Code**, **Word**, **Photoshop** ou **Excel**.

O objetivo desse exercício é demonstrar um dos maiores benefícios do padrão Command: **transformar ações em objetos reutilizáveis, permitindo histórico, desfazer, refazer, auditoria e desacoplamento entre quem solicita e quem executa uma operação.**
