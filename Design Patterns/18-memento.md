# 18) Memento (Memorando)

> **Categoria:** Comportamental

---

# O que é o padrão Memento?

O **Memento (Memorando)** é um padrão de projeto comportamental utilizado para **capturar e armazenar o estado interno de um objeto**, permitindo restaurá-lo posteriormente sem quebrar o encapsulamento.

Em outras palavras:

> **O Memento cria uma fotografia (snapshot) de um objeto em determinado momento.**

Esse snapshot pode ser utilizado para:

- desfazer alterações (_Undo_);
- criar histórico de versões;
- recuperar estados anteriores;
- criar pontos de restauração.

---

# Problema que o Memento resolve

Imagine um editor de texto.

O usuário escreve:

```text
Olá
```

Depois altera para:

```text
Olá mundo
```

Depois decide voltar atrás:

```text
CTRL + Z
```

O sistema precisa saber:

- qual era o texto anterior;
- onde estava o cursor;
- quais informações precisavam ser restauradas.

Uma solução simples seria guardar uma cópia do objeto inteiro.

Porém isso pode gerar problemas:

- expõe detalhes internos;
- permite alterações indevidas;
- aumenta o acoplamento.

O Memento resolve isso criando um objeto específico para armazenar o estado.

---

# Motivação (cenário real)

Imagine um jogo eletrônico.

Durante uma fase, o jogador possui:

```text
Vida: 80

Posição:
X: 120
Y: 50

Itens:
Espada
Poção
```

Ao passar por um ponto de salvamento, o jogo precisa guardar esse estado.

Depois, caso o jogador morra:

```
Carregar jogo salvo
```

O sistema restaura exatamente aquele momento.

O arquivo salvo representa um Memento.

---

# Analogia do mundo real

Imagine uma máquina fotográfica.

Você tira uma foto de um momento.

A foto guarda:

- aparência;
- posição;
- cenário.

Mas ela não altera a pessoa fotografada.

O Memento funciona da mesma forma.

Ele captura um estado sem interferir no objeto original.

---

# Ideia central

O padrão divide responsabilidades em três participantes:

```text
Originator

↓

Cria e restaura estados


Memento

↓

Armazena uma cópia do estado


Caretaker

↓

Guarda os snapshots
```

---

# Estrutura do padrão

```text
             Cliente

                │

                ▼

          Caretaker

       (Histórico)

                │

                ▼

           Memento

       (Snapshot)

                │

                ▼

          Originator

       (Objeto original)
```

---

# Participantes

## 1. Originator

É o objeto que possui o estado real.

Responsabilidades:

- alterar seu estado;
- criar Mementos;
- restaurar estados antigos.

Exemplo:

```text
Editor
```

---

## 2. Memento

É o objeto que guarda uma cópia do estado.

Características:

- normalmente imutável;
- não deve permitir alterações externas;
- representa uma versão anterior.

Exemplo:

```text
Documento salvo
```

---

## 3. Caretaker

Responsável por armazenar os Mementos.

Ele não conhece os detalhes internos do estado.

Ele apenas guarda:

```text
versão 1

versão 2

versão 3
```

---

## 4. Cliente

Coordena o processo.

Decide quando:

- salvar;
- restaurar;
- criar histórico.

---

# Diagrama UML simplificado

```text
              +----------------+
              |    Cliente     |
              +----------------+
                      |
                      |
                      ▼

              +----------------+
              |   Caretaker    |
              +----------------+
              | - mementos[]   |
              +-------+--------+
                      |
                      |
                      ▼

              +----------------+
              |    Memento     |
              +----------------+
              | - estado       |
              +-------+--------+
                      |
                      |
                      ▼

              +----------------+
              |  Originator    |
              +----------------+
              | + salvar()     |
              | + restaurar()  |
              +----------------+
```

---

# Implementação completa em TypeScript

```ts
/**
 * ============================================================
 * MEMENTO
 * ============================================================
 *
 * Exemplo:
 *
 * Editor de texto com histórico.
 *
 * O editor poderá:
 *
 * - escrever textos;
 * - salvar versões;
 * - restaurar versões antigas.
 */

/**
 * Estado interno do editor.
 */
type EstadoEditor = {
  texto: string;

  cursor: number;
};

/**
 * ============================================================
 * MEMENTO
 * ============================================================
 *
 * Guarda uma fotografia do estado.
 *
 * Este objeto não deve ser alterado
 * diretamente.
 */

class MementoEditor {
  constructor(private readonly estado: EstadoEditor) {}

  obterEstado(): EstadoEditor {
    return {
      texto: this.estado.texto,

      cursor: this.estado.cursor,
    };
  }
}

/**
 * ============================================================
 * ORIGINATOR
 * ============================================================
 *
 * Objeto que possui o estado original.
 */

class Editor {
  private estado: EstadoEditor = {
    texto: '',

    cursor: 0,
  };

  escrever(texto: string): void {
    this.estado.texto = texto;

    this.estado.cursor = texto.length;
  }

  moverCursor(posicao: number): void {
    this.estado.cursor = posicao;
  }

  obterEstado(): EstadoEditor {
    return {
      texto: this.estado.texto,

      cursor: this.estado.cursor,
    };
  }

  /**
   * Cria uma fotografia do estado atual.
   */
  criarMemento(): MementoEditor {
    return new MementoEditor(this.obterEstado());
  }

  /**
   * Restaura um estado anterior.
   */
  restaurar(memento: MementoEditor): void {
    this.estado = memento.obterEstado();
  }
}

/**
 * ============================================================
 * CARETAKER
 * ============================================================
 *
 * Responsável pelo histórico.
 */

class HistoricoEditor {
  private historico: MementoEditor[] = [];

  salvar(editor: Editor): void {
    this.historico.push(editor.criarMemento());
  }

  desfazer(editor: Editor): void {
    const ultimoEstado = this.historico.pop();

    if (!ultimoEstado) {
      console.log('Nenhum estado salvo.');

      return;
    }

    editor.restaurar(ultimoEstado);
  }
}

/**
 * ============================================================
 * CLIENTE
 * ============================================================
 */

const editor = new Editor();

const historico = new HistoricoEditor();

historico.salvar(editor);

editor.escrever('Olá');

historico.salvar(editor);

editor.escrever('Olá mundo');

console.log(
  'Estado atual:',

  editor.obterEstado(),
);

historico.desfazer(editor);

console.log(
  'Depois do undo:',

  editor.obterEstado(),
);
```

---

# Saída esperada

```text
Estado atual:

{
 texto: "Olá mundo",
 cursor: 10
}


Depois do undo:

{
 texto: "Olá",
 cursor: 3
}
```

---

# Fluxo de execução (passo a passo)

## Passo 1

O editor inicia vazio.

```text
texto = ""
```

---

## Passo 2

O histórico salva o estado atual.

```text
Editor

↓

Memento

↓

Histórico
```

---

## Passo 3

O usuário escreve:

```text
Olá
```

---

## Passo 4

Novo estado é salvo.

```text
Versão 1

↓

Olá
```

---

## Passo 5

Usuário altera para:

```text
Olá mundo
```

---

## Passo 6

O usuário executa:

```text
CTRL + Z
```

---

## Passo 7

O Caretaker recupera o último Memento.

---

## Passo 8

O Originator restaura seu estado.

Resultado:

```text
Olá
```

---

# Fluxo interno

Salvar:

```text
Editor

↓

Criar Memento

↓

Guardar Histórico
```

Restaurar:

```text
Histórico

↓

Memento

↓

Editor
```

---

# Como reconhecer que é Memento?

Alguns sinais indicam esse padrão:

- Existe necessidade de salvar versões anteriores.
- Um objeto precisa voltar a um estado antigo.
- Existe histórico de alterações.
- O estado interno deve permanecer protegido.

Exemplos:

- CTRL + Z;
- jogos salvos;
- versões de documentos;
- snapshots de banco;
- sistemas de configuração.

---

# Vantagens

## Preserva encapsulamento

O estado interno não precisa ser exposto.

---

## Implementação simples de Undo

Facilita criar histórico de alterações.

---

## Recuperação rápida

Restaurar um snapshot geralmente é simples.

---

## Controle de versões

Permite criar múltiplos pontos de restauração.

---

# Desvantagens

## Consumo de memória

Muitos snapshots podem ocupar bastante espaço.

---

## Custo de criação

Objetos grandes podem ser caros para copiar.

---

## Gerenciamento de histórico

É necessário controlar:

- quantidade de versões;
- descarte;
- limite de memória.

---

# Quando utilizar

Utilize Memento quando:

- precisar implementar Undo/Redo;
- precisar salvar estados anteriores;
- criar pontos de restauração;
- implementar histórico de versões.

Exemplos comuns:

- Editores de texto;
- Jogos;
- Sistemas CAD;
- Softwares gráficos;
- Sistemas de configuração;
- Sistemas de workflow.

---

# Quando evitar

Evite utilizar quando:

- o estado do objeto for muito grande;
- salvar versões completas for inviável;
- apenas pequenas alterações precisarem ser registradas.

Nestes casos, pode ser melhor utilizar:

- logs de alterações;
- event sourcing;
- armazenamento incremental.

---

# Relação com os princípios SOLID

## S — Single Responsibility Principle (SRP)

Cada classe possui uma responsabilidade:

- Editor controla estado.
- Memento guarda estado.
- Histórico gerencia versões.

---

## O — Open/Closed Principle (OCP)

Novas formas de salvar estados podem ser adicionadas sem alterar o Editor.

---

## L — Liskov Substitution Principle (LSP)

Mementos podem ser tratados através de sua abstração.

---

## I — Interface Segregation Principle (ISP)

Cada participante possui apenas responsabilidades necessárias.

---

## D — Dependency Inversion Principle (DIP)

O gerenciamento de histórico depende do conceito de Memento, não dos detalhes internos do Editor.

---

# Anti-exemplo

Um erro comum é guardar diretamente a referência do estado.

Exemplo:

```ts
historico.push(editor.estado);
```

Problema:

O histórico não possui uma cópia.

Ele possui apenas uma referência para o mesmo objeto.

Se o estado mudar:

```ts
editor.estado.texto = 'Novo texto';
```

O histórico também será alterado.

Resultado:

```text
Estado antigo perdido.
```

O Memento evita isso criando uma cópia independente do estado.

---

# Exercício proposto

Implemente um sistema de personagem de jogo utilizando Memento.

## Estado do personagem:

```ts
{
  vida: number;

  mana: number;

  posicao: {
    x: number;

    y: number;
  }
}
```

Crie:

## Originator

```ts
Personagem;
```

Responsável por:

- receber dano;
- recuperar vida;
- movimentar.

---

## Memento

```ts
MementoPersonagem;
```

Responsável por armazenar snapshots.

---

## Caretaker

```ts
SaveGame;
```

Responsável pelos pontos de salvamento.

---

# Desafio extra

Implemente:

```ts
refazer();
```

permitindo:

```text
CTRL + Z

↓

voltar estado


CTRL + Y

↓

avançar estado
```

Crie um histórico semelhante ao funcionamento de:

- Visual Studio Code;
- Photoshop;
- Word;
- Jogos com sistema de save.

O objetivo do exercício é compreender como o padrão Memento permite **guardar estados anteriores de forma segura, mantendo o encapsulamento e possibilitando recursos como Undo, histórico e restauração de versões.**
