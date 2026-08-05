# 11) Flyweight (Peso-Mosca / Objeto Leve)

> **Categoria:** Estrutural

---

# O que é o padrão Flyweight?

O **Flyweight (Peso-Mosca)** é um padrão de projeto estrutural cujo objetivo é **reduzir o consumo de memória compartilhando objetos que possuem informações iguais**.

Ao invés de criar milhares de objetos praticamente idênticos, o Flyweight cria apenas **uma única instância da parte que é comum** e permite que diversos objetos a reutilizem.

As informações que mudam de um objeto para outro **não ficam armazenadas dentro do Flyweight**. Elas são fornecidas externamente sempre que o objeto é utilizado.

Por isso o padrão divide os dados em dois grupos:

- **Estado Intrínseco:** informações compartilhadas entre vários objetos.
- **Estado Extrínseco:** informações que variam para cada objeto.

Em outras palavras:

> **O Flyweight compartilha tudo aquilo que pode ser compartilhado e deixa cada objeto armazenar apenas aquilo que realmente é diferente.**

---

# Problema que o Flyweight resolve

Imagine um jogo com:

- 500.000 árvores
- 2.000.000 partículas
- 300.000 estrelas
- 100.000 soldados

Cada árvore possui:

- imagem
- textura
- cor
- modelo 3D
- altura
- largura

Se criarmos um objeto completo para cada árvore, teremos centenas de milhares de cópias exatamente iguais.

Exemplo:

```
Árvore 1

Imagem
Textura
Modelo
Cor
Posição

Árvore 2

Imagem
Textura
Modelo
Cor
Posição

Árvore 3

Imagem
Textura
Modelo
Cor
Posição
```

Observe que:

Imagem, textura e modelo são exatamente iguais.

A única diferença é:

- posição
- rotação
- escala

Estamos desperdiçando memória.

O Flyweight resolve isso compartilhando apenas uma árvore "modelo".

Cada árvore do mapa guarda apenas:

```
x

y

rotação
```

E reutiliza:

```
imagem

textura

modelo
```

---

# Motivação (cenário real)

Imagine um editor de texto.

Um documento possui:

```
1.000.000 caracteres
```

Se cada letra armazenasse:

- fonte
- tamanho
- cor
- estilo

O consumo de memória seria enorme.

Entretanto, imagine que todas as letras utilizam:

```
Arial

14px

Preto
```

Não faz sentido repetir essas informações um milhão de vezes.

O Flyweight cria apenas um objeto:

```
Arial
14px
Preto
```

E todas as letras compartilham essa configuração.

Cada caractere armazena apenas:

```
posição

caractere
```

---

# Analogia do mundo real

Imagine uma biblioteca.

Cada livro possui centenas de leitores.

Não é criada uma cópia do livro para cada pessoa.

Existe apenas um exemplar físico.

Cada leitor possui apenas:

- nome
- data do empréstimo

O livro é compartilhado.

Esse compartilhamento representa exatamente o Flyweight.

---

# Conceito fundamental

O Flyweight separa os dados em duas categorias.

## Estado Intrínseco (Compartilhado)

São informações que nunca mudam.

Exemplos:

- imagem
- textura
- modelo
- fonte
- cor
- sprite

Esses dados ficam dentro do Flyweight.

---

## Estado Extrínseco (Não compartilhado)

São informações específicas de cada objeto.

Exemplos:

- posição
- velocidade
- rotação
- escala
- quantidade de vida

Esses dados são passados sempre que o objeto é utilizado.

---

# Estrutura do padrão

```
                 Cliente
                     │
                     ▼
             FlyweightFactory
                     │
          reutiliza objetos
                     │
      ┌──────────────┴──────────────┐
      ▼                             ▼
 Flyweight A                 Flyweight B
      ▲
      │
Estado Intrínseco

Estado Extrínseco
é passado pelo cliente
```

---

# Participantes

## 1. Flyweight

Representa o objeto compartilhado.

Contém apenas o estado intrínseco.

---

## 2. FlyweightFactory

É responsável por:

- criar Flyweights;
- armazená-los;
- reutilizá-los.

Nunca cria duas instâncias iguais.

---

## 3. Cliente

Solicita Flyweights para a fábrica.

Também fornece o estado extrínseco durante a utilização.

---

## 4. Estado Intrínseco

É compartilhado entre diversos objetos.

---

## 5. Estado Extrínseco

É exclusivo de cada utilização.

---

# Diagrama UML simplificado

```text
                 +----------------------+
                 | FlyweightFactory     |
                 +----------------------+
                 | - cache              |
                 | + obter(...)         |
                 +----------+-----------+
                            |
                reutiliza objetos
                            |
          +-----------------+------------------+
          |                                    |
+----------------------+         +----------------------+
| Flyweight            |         | Cliente              |
+----------------------+         +----------------------+
| cor                  |         | posição              |
| sprite               |         | velocidade           |
| tamanho              |         +----------------------+
| renderizar(...)      |
+----------------------+
```

---

# Implementação completa em TypeScript

```ts
/**
 * ============================================================
 * FLYWEIGHT (PESO-MOSCA)
 * ============================================================
 *
 * Neste exemplo construiremos um sistema
 * de renderização de estrelas.
 *
 * Milhares de estrelas podem compartilhar
 * exatamente a mesma imagem.
 */

/**
 * ============================================================
 * ESTADO INTRÍNSECO
 * ============================================================
 *
 * Essas informações são compartilhadas.
 */

type Cor = 'BRANCO' | 'AZUL' | 'AMARELO';

type EstrelaCompartilhada = {
  cor: Cor;

  raio: number;

  sprite: string;
};

/**
 * ============================================================
 * FLYWEIGHT FACTORY
 * ============================================================
 */

class FabricaFlyweight {
  /**
   * Cache de objetos compartilhados.
   */
  private cache = new Map<string, EstrelaCompartilhada>();

  obter(
    cor: Cor,

    raio: number,
  ): EstrelaCompartilhada {
    const chave = `${cor}-${raio}`;

    if (this.cache.has(chave)) {
      return this.cache.get(chave)!;
    }

    console.log(`Criando Flyweight ${chave}`);

    const estrela = {
      cor,

      raio,

      sprite: `sprite-${cor.toLowerCase()}-${raio}`,
    };

    this.cache.set(chave, estrela);

    return estrela;
  }
}

/**
 * ============================================================
 * ESTADO EXTRÍNSECO
 * ============================================================
 *
 * Cada estrela possui sua própria posição.
 */

type Estrela = {
  x: number;

  y: number;

  velocidade: number;
};

/**
 * ============================================================
 * RENDERIZADOR
 * ============================================================
 */

class Renderizador {
  renderizar(
    compartilhada: EstrelaCompartilhada,

    estrela: Estrela,
  ): void {
    console.log(
      `Renderizando ${compartilhada.sprite}
             em (${estrela.x}, ${estrela.y})
             velocidade ${estrela.velocidade}`,
    );
  }
}

/**
 * ============================================================
 * CLIENTE
 * ============================================================
 */

const fabrica = new FabricaFlyweight();

const renderizador = new Renderizador();

const estrelas: Estrela[] = [];

for (let i = 0; i < 1000; i++) {
  estrelas.push({
    x: Math.random() * 800,

    y: Math.random() * 600,

    velocidade: Math.random() * 5,
  });
}

const branca = fabrica.obter(
  'BRANCO',

  2,
);

const azul = fabrica.obter(
  'AZUL',

  2,
);

const amarela = fabrica.obter(
  'AMARELO',

  3,
);

for (let i = 0; i < estrelas.length; i++) {
  const estrela = estrelas[i];

  const compartilhada = i % 3 === 0 ? branca : i % 3 === 1 ? azul : amarela;

  if (i < 10) {
    renderizador.renderizar(
      compartilhada,

      estrela,
    );
  }
}
```

---

# Fluxo de execução (passo a passo)

## Passo 1

O cliente solicita um Flyweight.

```ts
fabrica.obter('BRANCO', 2);
```

---

## Passo 2

A fábrica procura no cache.

```
Existe?

↓

Sim

↓

Retorna objeto existente
```

---

## Passo 3

Caso não exista.

```
Existe?

↓

Não

↓

Cria novo objeto

↓

Armazena no cache

↓

Retorna
```

---

## Passo 4

O cliente cria milhares de objetos contendo apenas:

```
x

y

velocidade
```

Esses dados representam o estado extrínseco.

---

## Passo 5

Durante a renderização.

O cliente fornece:

```
Flyweight

+

Estado Extrínseco
```

A união dessas informações permite desenhar cada estrela corretamente.

---

# Fluxo interno

```text
Cliente

↓

FabricaFlyweight

↓

Cache

↓

Flyweight Compartilhado

↓

Renderizador

↓

Estado Extrínseco

↓

Renderização
```

Observe que milhares de estrelas utilizam o mesmo objeto compartilhado.

---

# Exemplo visual

Sem Flyweight:

```text
Estrela

Imagem

Cor

Sprite

Raio

Posição

Velocidade

--------------------

Estrela

Imagem

Cor

Sprite

Raio

Posição

Velocidade

--------------------

Estrela

Imagem

Cor

Sprite

Raio

Posição

Velocidade
```

Tudo é duplicado.

---

Com Flyweight:

```text
Flyweight

Imagem

Cor

Sprite

Raio

↓

Compartilhado

----------------------------

Estrela 1

Posição

Velocidade

----------------------------

Estrela 2

Posição

Velocidade

----------------------------

Estrela 3

Posição

Velocidade
```

A memória utilizada é significativamente menor.

---

# Como reconhecer que é Flyweight?

Alguns sinais característicos:

- Existem milhares de objetos semelhantes.
- Grande parte das informações é repetida.
- O consumo de memória é elevado.
- É possível separar dados compartilhados dos dados específicos.
- Há uma fábrica responsável por reutilizar instâncias.

---

# Vantagens

## Redução de memória

O mesmo objeto é reutilizado diversas vezes.

---

## Melhor desempenho

Menos objetos precisam ser criados pelo sistema.

---

## Menor trabalho para o Garbage Collector

Como existem menos instâncias, o coletor de lixo realiza menos operações.

---

## Reutilização

Objetos compartilhados podem ser utilizados por milhares de clientes.

---

# Desvantagens

## Maior complexidade

É necessário separar corretamente o estado intrínseco do extrínseco.

---

## Código mais difícil de compreender

Principalmente para quem está iniciando.

---

## Dependência da fábrica

É preciso controlar cuidadosamente o cache para evitar objetos duplicados.

---

# Quando utilizar

Utilize Flyweight quando:

- existirem milhares ou milhões de objetos semelhantes;
- a memória estiver se tornando um problema;
- boa parte dos dados puder ser compartilhada;
- o custo de criação de objetos for elevado.

Exemplos comuns:

- Jogos
- Motores gráficos
- Partículas
- Árvores em mapas
- Caracteres em editores de texto
- Ícones
- Emojis
- Fontes
- Sprites
- Objetos de CAD

---

# Quando evitar

Evite utilizar Flyweight quando:

- existirem poucos objetos;
- praticamente todas as informações forem diferentes;
- o consumo de memória não for um problema;
- a complexidade adicional não compensar os benefícios.

---

# Relação com os princípios SOLID

## S — Single Responsibility Principle (SRP)

Cada classe possui uma responsabilidade específica.

- A fábrica gerencia o cache.
- O Flyweight representa o estado compartilhado.
- O renderizador apenas desenha os objetos.

---

## O — Open/Closed Principle (OCP)

Novos Flyweights podem ser adicionados sem modificar o cliente.

---

## L — Liskov Substitution Principle (LSP)

Qualquer Flyweight pode substituir outro desde que implemente o mesmo comportamento esperado.

---

## I — Interface Segregation Principle (ISP)

Cada classe expõe apenas os métodos necessários para sua responsabilidade.

---

## D — Dependency Inversion Principle (DIP)

O cliente pode depender de abstrações em vez de implementações concretas da fábrica ou dos Flyweights.

---

# Anti-exemplo

Um erro comum é criar um objeto completo para cada elemento, duplicando todas as informações compartilháveis.

```ts
for (let i = 0; i < 1000000; i++) {
  const estrela = {
    cor: 'BRANCO',

    sprite: 'sprite-branco',

    raio: 2,

    x: Math.random(),

    y: Math.random(),

    velocidade: 3,
  };
}
```

Nesse exemplo, milhões de objetos armazenam repetidamente a mesma cor, sprite e raio, desperdiçando memória.

---

# Exercício proposto

Implemente um sistema de árvores para um jogo utilizando o padrão Flyweight.

### Requisitos

Crie uma fábrica chamada:

```ts
FabricaArvores;
```

O estado intrínseco deverá conter:

- espécie;
- textura;
- modelo 3D;
- altura padrão.

Cada árvore do mapa deverá armazenar apenas:

- posição X;
- posição Y;
- rotação;
- escala.

A fábrica deverá reutilizar árvores da mesma espécie.

### Exemplo

```text
Carvalho

↓

Compartilhado

↓

100.000 árvores utilizando o mesmo modelo
```

---

### Desafio extra

Implemente um método chamado:

```ts
quantidadeFlyweights();
```

Esse método deverá informar quantos objetos compartilhados realmente existem no cache.

Depois crie um mapa contendo **100.000 árvores** utilizando apenas **5 espécies diferentes**.

Ao final, imprima:

```text
Quantidade de árvores:

100000

Quantidade de Flyweights:

5
```

Esse exercício demonstra claramente o principal benefício do padrão Flyweight: **economizar memória compartilhando objetos comuns**, mantendo apenas as informações variáveis em cada instância individual.
