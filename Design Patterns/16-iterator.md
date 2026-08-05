# 16) Iterator (Iterador)

> **Categoria:** Comportamental

---

# O que é o padrão Iterator?

O **Iterator (Iterador)** é um padrão de projeto comportamental que permite **percorrer os elementos de uma coleção sem expor sua estrutura interna**.

Em vez de o cliente acessar diretamente índices, listas, árvores ou páginas de uma coleção, ele utiliza um objeto chamado **Iterador**, responsável por controlar a navegação.

Em outras palavras:

> **O Iterator sabe como percorrer a coleção. O cliente apenas solicita o próximo elemento.**

Dessa forma, o cliente não precisa conhecer:

- como os dados estão armazenados;
- se a coleção é um Array;
- se os dados vêm de um banco;
- se existe paginação;
- se os elementos estão em uma árvore;
- ou qualquer outro detalhe da implementação.

---

# Problema que o Iterator resolve

Imagine um sistema que consulta produtos de uma API.

A API retorna apenas 10 produtos por requisição.

Primeira chamada:

```text
Página 1

↓

10 produtos
```

Depois:

```text
Página 2

↓

10 produtos
```

Depois:

```text
Página 3

↓

10 produtos
```

Sem Iterator, cada desenvolvedor precisaria implementar toda a lógica de paginação.

```ts
while (pagina <= totalPaginas) {

    const resposta = api.buscarPagina(...);

    ...

}
```

Essa lógica acabaria duplicada em vários lugares da aplicação.

O Iterator encapsula toda essa responsabilidade.

O cliente apenas faz:

```ts
while (iterador.temProximo()) {
  console.log(iterador.proximo());
}
```

---

# Motivação (cenário real)

Imagine um livro.

Você não precisa saber:

- como as páginas foram impressas;
- onde ficam armazenadas;
- como foram encadernadas.

Você apenas vira uma página por vez.

O Iterator funciona exatamente assim.

Ele sabe qual é o próximo elemento.

Você apenas pede:

> "Me entregue o próximo."

---

# Analogia do mundo real

Imagine um controle remoto.

Você não precisa conhecer a eletrônica da televisão.

Você apenas pressiona:

```
Canal +

↓

Próximo canal
```

ou

```
Canal -

↓

Canal anterior
```

O controle remoto representa o Iterator.

A televisão representa a coleção.

---

# Estrutura do padrão

```text
             Cliente
                 │
                 ▼
             Iterator
                 │
                 ▼
             Coleção
```

---

# Participantes

## 1. Iterator

Define como navegar pela coleção.

Normalmente possui métodos como:

```ts
temProximo();
```

```ts
proximo();
```

Opcionalmente:

```ts
reiniciar();
```

```ts
anterior();
```

---

## 2. Concrete Iterator

Implementa a lógica de navegação.

Pode controlar:

- posição atual;
- página atual;
- índice;
- cursor.

---

## 3. Aggregate (Coleção)

Representa a estrutura que contém os dados.

Exemplo:

- Array;
- Lista;
- Árvore;
- API paginada;
- Banco de dados.

---

## 4. Cliente

Utiliza apenas o Iterator.

Não conhece os detalhes da coleção.

---

# Diagrama UML simplificado

```text
                  +------------------------+
                  |       Iterator         |
                  +------------------------+
                  | + temProximo()         |
                  | + proximo()            |
                  +------------▲-----------+
                               |
                               |
                 +-------------+-------------+
                 |                           |
                 ▼                           ▼
      IteradorItens                 IteradorProdutos

                 │
                 ▼
         ServicoPaginado
```

---

# Implementação completa em TypeScript

```ts
/**
 * ============================================================
 * ITERATOR
 * ============================================================
 *
 * Neste exemplo criaremos um iterador
 * para consumir uma API paginada.
 *
 * O cliente não saberá que existem páginas.
 */

/**
 * Produto retornado pela API.
 */
type Produto = {
  id: string;

  nome: string;
};

/**
 * ============================================================
 * INTERFACE ITERATOR
 * ============================================================
 */

interface Iterador<T> {
  temProximo(): boolean;

  proximo(): T;
}

/**
 * ============================================================
 * PÁGINA RETORNADA PELA API
 * ============================================================
 */

class Pagina {
  constructor(
    private produtos: Produto[],

    private paginaAtual: number,

    private totalPaginas: number,
  ) {}

  obterProdutos(): Produto[] {
    return this.produtos;
  }

  obterPaginaAtual(): number {
    return this.paginaAtual;
  }

  obterTotalPaginas(): number {
    return this.totalPaginas;
  }
}

/**
 * ============================================================
 * SERVIÇO PAGINADO
 * ============================================================
 */

class ServicoPaginado {
  private produtos: Produto[];

  constructor() {
    this.produtos = Array.from(
      { length: 25 },

      (_, indice) => ({
        id: `P${indice + 1}`,

        nome: `Produto ${indice + 1}`,
      }),
    );
  }

  buscarPagina(
    pagina: number,

    tamanhoPagina: number,
  ): Pagina {
    const inicio = (pagina - 1) * tamanhoPagina;

    const fim = inicio + tamanhoPagina;

    const itens = this.produtos.slice(
      inicio,

      fim,
    );

    const totalPaginas = Math.ceil(this.produtos.length / tamanhoPagina);

    return new Pagina(
      itens,

      pagina,

      totalPaginas,
    );
  }
}

/**
 * ============================================================
 * ITERADOR
 * ============================================================
 */

class IteradorProdutos implements Iterador<Produto> {
  private paginaAtual = 1;

  private buffer: Produto[] = [];

  private indiceBuffer = 0;

  private totalPaginas = 0;

  constructor(
    private servico: ServicoPaginado,

    private tamanhoPagina: number,
  ) {
    this.carregarPagina();
  }

  /**
   * Busca uma nova página.
   */
  private carregarPagina(): void {
    const pagina = this.servico.buscarPagina(
      this.paginaAtual,

      this.tamanhoPagina,
    );

    this.buffer = pagina.obterProdutos();

    this.indiceBuffer = 0;

    this.totalPaginas = pagina.obterTotalPaginas();
  }

  temProximo(): boolean {
    if (this.indiceBuffer < this.buffer.length) {
      return true;
    }

    return this.paginaAtual < this.totalPaginas;
  }

  proximo(): Produto {
    if (this.indiceBuffer >= this.buffer.length) {
      this.paginaAtual++;

      this.carregarPagina();
    }

    return this.buffer[this.indiceBuffer++];
  }
}

/**
 * ============================================================
 * CLIENTE
 * ============================================================
 */

const servico = new ServicoPaginado();

const iterador = new IteradorProdutos(
  servico,

  10,
);

const produtos: Produto[] = [];

while (iterador.temProximo()) {
  produtos.push(iterador.proximo());
}

console.log(produtos.length);

console.log(produtos);
```

---

# Saída esperada

```text
25
```

```text
[
 Produto 1,
 Produto 2,
 Produto 3,
 ...
 Produto 25
]
```

Observe que o cliente percorreu todos os produtos sem precisar saber que a API estava dividida em páginas.

---

# Fluxo de execução (passo a passo)

## Passo 1

O cliente cria o iterador.

```ts
const iterador = new IteradorProdutos(...);
```

---

## Passo 2

O Iterator carrega automaticamente a primeira página.

```text
Página 1

↓

Produtos 1 ao 10
```

---

## Passo 3

O cliente pergunta:

```ts
temProximo();
```

Resposta:

```text
Sim
```

---

## Passo 4

O cliente solicita:

```ts
proximo();
```

O Iterator entrega o próximo produto.

---

## Passo 5

Quando o buffer termina:

```text
Produto 10

↓

Fim da página
```

O Iterator busca automaticamente a próxima página.

```text
Página 2

↓

Produtos 11 ao 20
```

---

## Passo 6

O processo continua até o último produto.

O cliente nunca percebe que novas páginas foram carregadas.

---

# Fluxo interno

```text
Cliente

↓

Iterator

↓

Buffer

↓

API

↓

Nova Página
```

---

# Como reconhecer que é Iterator?

Alguns sinais indicam claramente esse padrão:

- Existe um objeto responsável por percorrer elementos.
- O cliente utiliza métodos como `proximo()` ou `temProximo()`.
- A estrutura interna da coleção permanece escondida.
- Diferentes coleções podem ser percorridas da mesma forma.

---

# Vantagens

## Encapsulamento

A estrutura interna da coleção permanece oculta.

---

## Reutilização

O mesmo Iterator pode ser utilizado por diferentes clientes.

---

## Baixo acoplamento

O cliente não depende da implementação da coleção.

---

## Flexibilidade

É possível trocar a coleção sem alterar o código cliente.

---

## Navegação personalizada

Podem existir iteradores que percorrem:

- do início para o fim;
- do fim para o início;
- apenas elementos pares;
- apenas elementos filtrados.

---

# Desvantagens

## Mais abstração

Em coleções muito simples, um `for` pode ser suficiente.

---

## Mais classes

Cada estratégia de navegação pode gerar um novo Iterator.

---

## Estado interno

O Iterator precisa manter informações como posição, índice ou página atual.

---

# Quando utilizar

Utilize Iterator quando:

- precisar percorrer coleções complexas;
- quiser esconder detalhes da estrutura de dados;
- trabalhar com APIs paginadas;
- navegar por árvores;
- percorrer listas ligadas;
- oferecer diferentes formas de navegação.

Exemplos comuns:

- APIs REST paginadas;
- Cursores de banco de dados;
- Árvores de arquivos;
- Estruturas XML;
- Estruturas JSON;
- Frameworks de coleções;
- Streams de dados.

---

# Quando evitar

Evite utilizar quando:

- a coleção for extremamente simples;
- um `for...of` ou `forEach` resolver completamente o problema;
- não houver necessidade de encapsular a navegação.

---

# Relação com os princípios SOLID

## S — Single Responsibility Principle (SRP)

O Iterator é responsável apenas pela navegação.

A coleção continua responsável apenas por armazenar os dados.

---

## O — Open/Closed Principle (OCP)

Novos tipos de iteradores podem ser criados sem modificar a coleção.

---

## L — Liskov Substitution Principle (LSP)

Qualquer iterador concreto pode substituir outro desde que implemente a interface `Iterador`.

---

## I — Interface Segregation Principle (ISP)

A interface define apenas os métodos necessários para navegar na coleção.

---

## D — Dependency Inversion Principle (DIP)

O cliente depende da abstração `Iterador`, e não das implementações concretas da coleção.

---

# Anti-exemplo

Um erro comum é fazer o cliente controlar toda a paginação manualmente.

```ts
let pagina = 1;

while (pagina <= totalPaginas) {

    const resposta = api.buscarPagina(pagina);

    resposta.itens.forEach(...);

    pagina++;

}
```

Essa lógica acaba sendo duplicada em vários módulos da aplicação.

Com o Iterator, toda essa responsabilidade fica concentrada em um único objeto.

---

# Exercício proposto

Implemente um sistema de navegação por uma biblioteca digital.

### Requisitos

Crie uma coleção de livros que simule uma API paginada.

Depois implemente um iterador chamado:

```ts
IteradorLivros;
```

Ele deverá oferecer os métodos:

```ts
temProximo();
```

```ts
proximo();
```

O cliente deverá conseguir percorrer todos os livros sem saber que a coleção está dividida em páginas.

---

## Desafio extra

Implemente novos iteradores para a mesma coleção.

Exemplos:

- `IteradorReverso` (do último livro para o primeiro);
- `IteradorLivrosDisponiveis` (ignora livros indisponíveis);
- `IteradorPorCategoria` (retorna apenas livros de uma categoria específica).

O objetivo é demonstrar uma das maiores vantagens do padrão Iterator: **desacoplar a lógica de navegação da estrutura de dados, permitindo diferentes estratégias de percurso sem alterar a coleção nem o código cliente.**
