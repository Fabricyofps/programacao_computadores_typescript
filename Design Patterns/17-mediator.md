# 17) Mediator (Mediador)

> **Categoria:** Comportamental

---

# O que é o padrão Mediator?

O **Mediator (Mediador)** é um padrão de projeto comportamental que **centraliza a comunicação entre diversos objetos**, evitando que eles dependam diretamente uns dos outros.

Em vez de um objeto conversar diretamente com vários outros objetos, todos passam a se comunicar através de um **Mediador**, responsável por coordenar as interações.

Em outras palavras:

> **O Mediator transforma uma comunicação "todos com todos" em uma comunicação "todos com um".**

Esse padrão reduz significativamente o acoplamento entre os componentes de um sistema.

---

# Problema que o Mediator resolve

Imagine uma interface de cadastro composta por vários componentes:

- Campo Nome
- Campo E-mail
- Campo CPF
- Botão Salvar
- Botão Cancelar
- Mensagens de erro

Sem Mediator, cada componente precisa conhecer vários outros componentes.

Exemplo:

```text
Campo Nome

↓

Botão Salvar

↓

Mensagem

↓

Campo CPF

↓

Campo Email
```

À medida que novos componentes são adicionados, a quantidade de dependências cresce rapidamente.

Esse problema é conhecido como:

> **Acoplamento excessivo.**

O Mediator resolve isso fazendo com que todos conversem apenas com ele.

---

# Motivação (cenário real)

Imagine um aeroporto.

Os aviões não conversam diretamente entre si.

Se cada avião tivesse que coordenar pousos e decolagens com todos os outros, haveria um enorme risco de colisões.

Em vez disso, todos se comunicam com:

```
Torre de Controle
```

A torre recebe as informações e decide quem deve pousar, quem deve aguardar e quem pode decolar.

A torre representa exatamente o **Mediator**.

---

# Analogia do mundo real

Imagine um grupo de WhatsApp.

Sem um administrador, cada pessoa precisaria conversar diretamente com todas as outras.

Agora imagine que existe um organizador responsável por distribuir todas as mensagens importantes.

Ele decide:

- quem recebe;
- quando recebe;
- como recebe.

Esse organizador é o Mediator.

---

# Estrutura do padrão

```text
          Componente A
                 │
                 │
                 ▼
             Mediator
            /    |    \
           ▼     ▼     ▼
Componente B  Componente C  Componente D
```

Todos os componentes conhecem apenas o Mediator.

Eles não conhecem uns aos outros.

---

# Participantes

## 1. Mediator

Define como a comunicação será realizada.

É o responsável por coordenar todos os componentes.

---

## 2. Concrete Mediator

Implementa a lógica de comunicação.

Decide:

- quem será notificado;
- quais eventos devem ser propagados;
- quais componentes participam da comunicação.

---

## 3. Colleague (Componente)

São os objetos que participam da comunicação.

Eles conhecem apenas o Mediator.

---

## 4. Cliente

Cria os componentes e registra todos no Mediator.

---

# Diagrama UML simplificado

```text
                 +----------------------+
                 |      Mediador        |
                 +----------------------+
                 | + notificar()        |
                 +----------▲-----------+
                            |
       ---------------------------------------------
       |                  |                       |
       ▼                  ▼                       ▼
+---------------+  +---------------+   +----------------+
| Jogador       |  | Inimigo       |   | NPC            |
+---------------+  +---------------+   +----------------+
```

---

# Implementação completa em TypeScript

```ts
/**
 * ============================================================
 * MEDIATOR
 * ============================================================
 *
 * Neste exemplo simularemos um jogo.
 *
 * O jogador e os inimigos não conversam
 * diretamente.
 *
 * Toda comunicação acontece através
 * do Mediador.
 */

/**
 * Interface comum para todos
 * os participantes.
 */
interface Participante {
  receberEvento(
    evento: string,

    dados?: unknown,
  ): void;
}

/**
 * ============================================================
 * MEDIADOR
 * ============================================================
 */

class MediadorJogo {
  private jogador?: Jogador;

  private inimigos: Inimigo[] = [];

  registrarJogador(jogador: Jogador): void {
    this.jogador = jogador;
  }

  registrarInimigo(inimigo: Inimigo): void {
    this.inimigos.push(inimigo);
  }

  /**
   * Centraliza toda a comunicação.
   */
  notificar(
    evento: string,

    dados?: unknown,
  ): void {
    console.log(`Mediator recebeu o evento: ${evento}`);

    for (const inimigo of this.inimigos) {
      inimigo.receberEvento(
        evento,

        dados,
      );
    }

    this.jogador?.receberEvento(
      evento,

      dados,
    );
  }
}

/**
 * ============================================================
 * JOGADOR
 * ============================================================
 */

class Jogador implements Participante {
  constructor(private mediador: MediadorJogo) {}

  atacar(): void {
    console.log('Jogador atacou!');

    this.mediador.notificar(
      'JOGADOR_ATACOU',

      {
        dano: 15,
      },
    );
  }

  receberEvento(evento: string): void {
    console.log(`Jogador recebeu o evento: ${evento}`);
  }
}

/**
 * ============================================================
 * INIMIGO
 * ============================================================
 */

class Inimigo implements Participante {
  constructor(
    private mediador: MediadorJogo,

    private nome: string,
  ) {}

  receberEvento(
    evento: string,

    dados?: unknown,
  ): void {
    if (evento === 'JOGADOR_ATACOU') {
      console.log(
        `${this.nome} entrou em combate.`,

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

const mediador = new MediadorJogo();

const jogador = new Jogador(mediador);

mediador.registrarJogador(jogador);

mediador.registrarInimigo(
  new Inimigo(
    mediador,

    'Orc',
  ),
);

mediador.registrarInimigo(
  new Inimigo(
    mediador,

    'Goblin',
  ),
);

mediador.registrarInimigo(
  new Inimigo(
    mediador,

    'Dragão',
  ),
);

jogador.atacar();
```

---

# Saída esperada

```text
Jogador atacou!

Mediator recebeu o evento: JOGADOR_ATACOU

Orc entrou em combate.

Goblin entrou em combate.

Dragão entrou em combate.

Jogador recebeu o evento: JOGADOR_ATACOU
```

---

# Fluxo de execução (passo a passo)

## Passo 1

O cliente cria o Mediador.

```text
MediadorJogo
```

---

## Passo 2

Cria os participantes.

```text
Jogador

↓

Orc

↓

Goblin

↓

Dragão
```

---

## Passo 3

Todos são registrados no Mediador.

---

## Passo 4

O jogador realiza uma ação.

```text
atacar()
```

---

## Passo 5

O jogador não conversa diretamente com os inimigos.

Ele apenas chama:

```ts
mediador.notificar(...)
```

---

## Passo 6

O Mediador recebe o evento.

↓

Decide quem será notificado.

---

## Passo 7

Cada inimigo recebe o evento.

```text
Orc

↓

Goblin

↓

Dragão
```

---

## Passo 8

Cada inimigo executa sua própria reação.

---

# Fluxo interno

```text
Jogador

↓

Mediator

↓

Orc

↓

Goblin

↓

Dragão
```

Observe que nenhum inimigo conhece os demais.

Todos dependem apenas do Mediador.

---

# Como reconhecer que é Mediator?

Alguns sinais indicam claramente esse padrão:

- Muitos objetos precisam se comunicar.
- Existe um objeto central responsável por coordenar as mensagens.
- Os participantes não conhecem uns aos outros.
- As dependências entre componentes são reduzidas.

---

# Vantagens

## Baixo acoplamento

Os componentes deixam de depender diretamente entre si.

---

## Centralização

Toda a lógica de comunicação fica concentrada em um único lugar.

---

## Facilidade de manutenção

Alterações na comunicação normalmente afetam apenas o Mediador.

---

## Reutilização

Os componentes podem ser reutilizados em outros mediadores.

---

## Organização

Evita uma rede complexa de dependências entre objetos.

---

# Desvantagens

## Mediador muito grande

Se todas as regras forem colocadas em um único Mediador, ele poderá se tornar uma classe excessivamente complexa.

Esse problema é conhecido como **God Object**.

---

## Ponto central

Grande parte da lógica do sistema passa a depender do Mediador.

---

# Quando utilizar

Utilize Mediator quando:

- muitos objetos precisam trocar mensagens;
- houver excesso de dependências entre componentes;
- desejar centralizar regras de comunicação;
- quiser reduzir o acoplamento entre objetos.

Exemplos comuns:

- Interfaces gráficas (GUI);
- Jogos;
- Chats;
- Sistemas de eventos;
- Sistemas de workflow;
- Controle de tráfego aéreo;
- Plataformas de automação.

---

# Quando evitar

Evite utilizar quando:

- poucos objetos se comunicam;
- a comunicação é simples e direta;
- a criação de um Mediador apenas adicionaria complexidade desnecessária.

---

# Relação com os princípios SOLID

## S — Single Responsibility Principle (SRP)

Cada componente possui apenas sua responsabilidade.

A coordenação fica concentrada no Mediador.

---

## O — Open/Closed Principle (OCP)

Novos participantes podem ser adicionados sem alterar os existentes.

---

## L — Liskov Substitution Principle (LSP)

Qualquer participante concreto pode substituir outro desde que implemente a interface `Participante`.

---

## I — Interface Segregation Principle (ISP)

Os participantes implementam apenas os métodos necessários para receber eventos.

---

## D — Dependency Inversion Principle (DIP)

Os componentes dependem da abstração do Mediador, e não uns dos outros.

---

# Anti-exemplo

Um erro comum é fazer todos os objetos conhecerem todos os outros.

```ts
class Jogador {
  atacar(inimigo: Inimigo) {
    inimigo.receberDano();
  }
}
```

Depois:

```ts
class Inimigo {

    avisarNPC(...)

    avisarAliados(...)

    avisarChefe(...)

    avisarSistema(...)
}
```

Com o crescimento do sistema, cada objeto passa a depender de vários outros, formando uma rede difícil de manter.

O Mediator elimina esse problema centralizando toda a comunicação.

---

# Exercício proposto

Implemente um sistema de chat utilizando o padrão Mediator.

### Requisitos

Crie um Mediador chamado:

```ts
SalaDeChat;
```

Depois implemente participantes:

- Usuário;
- Administrador;
- Moderador.

Todos deverão enviar mensagens através da sala.

Nenhum participante poderá conversar diretamente com outro.

---

## Desafio extra

Implemente salas independentes.

Exemplo:

```text
Sala Java

Sala TypeScript

Sala Python
```

Cada sala deverá possuir seu próprio Mediador.

Os usuários registrados em uma sala não deverão receber mensagens enviadas em outra.

Esse exercício demonstra um dos maiores benefícios do padrão Mediator: **reduzir o acoplamento entre objetos centralizando a comunicação em um único componente, tornando o sistema mais organizado, flexível e fácil de manter.**
