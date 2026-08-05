# 08) Composite (Composto)

> **Categoria:** Estrutural

---

# O que é o Composite?

O **Composite (Composto)** é um padrão de projeto estrutural que permite **compor objetos em estruturas de árvore (hierarquias)** e tratar tanto objetos individuais quanto grupos de objetos exatamente da mesma forma.

A grande ideia deste padrão é que **todos os elementos da árvore compartilham a mesma interface**, permitindo que o código cliente não precise saber se está trabalhando com um único objeto ou com um conjunto deles.

Em outras palavras:

> "Se um objeto implementa a interface, pouco importa se ele representa apenas um elemento ou uma coleção inteira."

Isso elimina diversos `if`, `switch` e verificações de tipo espalhadas pelo código.

---

# Problema que o Composite resolve

Imagine que você possui uma estrutura hierárquica.

Alguns exemplos:

- Sistema de arquivos (pastas e arquivos)
- Menu de um site
- Organograma de uma empresa
- Categorias de produtos
- Árvore de comentários
- Componentes de uma interface gráfica (GUI)

Agora imagine que você deseja realizar alguma operação nessa estrutura.

Por exemplo:

- Renderizar todos os elementos
- Calcular tamanho total
- Imprimir na tela
- Exportar para JSON
- Aplicar desconto
- Percorrer toda a estrutura

Sem Composite normalmente fazemos algo parecido com isto:

```ts
if (objeto instanceof Pasta) {
  // percorre filhos
} else if (objeto instanceof Arquivo) {
  // imprime arquivo
}
```

Depois aparecem novos tipos:

- Atalho
- Link
- Pasta Compactada
- Pasta Virtual

Então o código cresce:

```ts
if (...)
else if (...)
else if (...)
else if (...)
```

Agora imagine esse código repetido em dezenas de lugares.

O Composite elimina exatamente esse problema.

---

# Motivação (cenário real)

Imagine um sistema de gerenciamento de arquivos.

```
Documentos
│
├── Trabalho.docx
├── Faculdade.pdf
│
└── Fotos
    ├── Praia.jpg
    ├── Família.jpg
    └── Cachorro.png
```

O usuário deseja visualizar toda a árvore.

Pergunta:

Como imprimir tudo?

Sem Composite:

```text
Se for pasta...
    percorra filhos

Se for arquivo...
    imprima nome
```

Com Composite:

```text
Todo objeto sabe se renderizar.
```

O cliente simplesmente chama:

```ts
raiz.renderizar();
```

Sem precisar conhecer os detalhes internos.

---

# Analogia do mundo real

Imagine uma empresa.

```
Empresa

    Departamento Comercial
        João
        Maria

    Departamento Financeiro
        Carlos
        Fernanda
```

Um funcionário é um elemento.

Um departamento é um grupo de elementos.

A empresa inteira é outro grupo.

Todos podem responder à mesma pergunta:

> "Quantas pessoas existem aqui?"

O funcionário responde:

```
1
```

O departamento soma seus funcionários.

A empresa soma seus departamentos.

Quem perguntou não precisa saber quem respondeu.

Esse é exatamente o Composite.

---

# Estrutura do padrão

```
            Componente
                 ▲
         ┌───────┴────────┐
         │                │
      Folha          Composto
(MenuItem)            (Menu)
                           │
                    possui vários
                    Componentes
```

---

# Participantes

## 1. Componente (Component)

É a interface comum.

Define todas as operações que folhas e compostos deverão implementar.

No nosso exemplo:

```ts
ComponenteMenu;
```

---

## 2. Folha (Leaf)

Representa um objeto simples.

Não possui filhos.

No exemplo:

```
Início
Produtos
Contato
FAQ
```

Cada um deles é um `ItemMenu`.

---

## 3. Composto (Composite)

Representa um objeto que contém outros objetos.

Pode conter:

- folhas
- outros compostos

No exemplo:

```
Menu Principal
```

contém

```
Início
Produtos
Suporte
```

e

```
Suporte
```

contém

```
Contato
FAQ
```

---

## 4. Cliente

É quem utiliza a estrutura.

Ele não sabe se está chamando um Item ou um Menu.

Ele apenas utiliza a interface comum.

---

# Diagrama UML simplificado

```text
                   +----------------------+
                   |  ComponenteMenu      |
                   +----------------------+
                   | + renderizar()       |
                   +----------▲-----------+
                              |
          +-------------------+-------------------+
          |                                       |
+----------------------+          +------------------------------+
|      ItemMenu        |          |            Menu              |
+----------------------+          +------------------------------+
| - titulo             |          | - titulo                    |
+----------------------+          | - componentes[]             |
| + renderizar()       |          +------------------------------+
|                      |          | + adicionar()               |
+----------------------+          | + remover()                 |
                                  | + renderizar()              |
                                  +--------------+--------------+
                                                 |
                                   contém vários Componentes
```

---

# Implementação completa em TypeScript

```ts
/**
 * ===========================================================
 * COMPOSITE (COMPOSTO)
 * ===========================================================
 *
 * Neste exemplo construiremos um menu hierárquico.
 *
 * Um Menu pode conter:
 *
 * - Itens
 * - Outros Menus
 *
 * O cliente não precisa saber a diferença.
 *
 * Basta chamar:
 *
 * renderizar()
 *
 * em qualquer objeto.
 */

/**
 * Interface comum para TODOS os componentes.
 *
 * Tanto ItemMenu quanto Menu implementam
 * exatamente esta interface.
 */
interface ComponenteMenu {
  /**
   * Renderiza o componente.
   *
   * O parâmetro nível controla a identação
   * para que a estrutura fique parecida
   * com uma árvore.
   */
  renderizar(nivel: number): string;
}

/**
 * ===========================================================
 * FOLHA (Leaf)
 * ===========================================================
 *
 * Representa um item simples.
 *
 * Não possui filhos.
 */
class ItemMenu implements ComponenteMenu {
  constructor(private titulo: string) {}

  renderizar(nivel: number): string {
    // Cria espaços para organizar visualmente
    const espacos = ' '.repeat(nivel);

    return `${espacos}- ${this.titulo}\n`;
  }
}

/**
 * ===========================================================
 * COMPOSTO (Composite)
 * ===========================================================
 *
 * Um Menu pode conter vários Componentes.
 *
 * Observe que o vetor é do tipo
 *
 * ComponenteMenu
 *
 * Isso significa que ele aceita:
 *
 * - ItemMenu
 * - Menu
 *
 * Ou qualquer outra classe que implemente
 * essa interface.
 */
class Menu implements ComponenteMenu {
  /**
   * Lista de filhos.
   */
  private componentes: ComponenteMenu[] = [];

  constructor(private titulo: string) {}

  /**
   * Adiciona um novo componente.
   */
  adicionar(componente: ComponenteMenu): void {
    this.componentes.push(componente);
  }

  /**
   * Remove um componente.
   */
  remover(componente: ComponenteMenu): void {
    this.componentes = this.componentes.filter((item) => item !== componente);
  }

  /**
   * Renderiza todo o menu.
   *
   * Observe que o Menu NÃO sabe
   * quais tipos concretos existem.
   *
   * Ele apenas percorre todos os componentes
   * chamando:
   *
   * renderizar()
   *
   * Isso é Polimorfismo.
   */
  renderizar(nivel: number): string {
    const espacos = ' '.repeat(nivel);

    let resultado = `${espacos} ${this.titulo}\n`;

    for (const componente of this.componentes) {
      resultado += componente.renderizar(nivel + 4);
    }

    return resultado;
  }
}

/**
 * ===========================================================
 * CLIENTE
 * ===========================================================
 */

// Menu principal
const menuPrincipal = new Menu('Principal');

// Itens simples
menuPrincipal.adicionar(new ItemMenu('Início'));

menuPrincipal.adicionar(new ItemMenu('Produtos'));

// Submenu
const menuSuporte = new Menu('Suporte');

menuSuporte.adicionar(new ItemMenu('FAQ'));

menuSuporte.adicionar(new ItemMenu('Contato'));

menuSuporte.adicionar(new ItemMenu('Abrir Chamado'));

// Outro submenu
const menuConta = new Menu('Minha Conta');

menuConta.adicionar(new ItemMenu('Perfil'));

menuConta.adicionar(new ItemMenu('Pedidos'));

menuConta.adicionar(new ItemMenu('Sair'));

// Inserindo submenus no menu principal
menuPrincipal.adicionar(menuSuporte);
menuPrincipal.adicionar(menuConta);

// Impressão
console.log(menuPrincipal.renderizar(0));
```

---

# Saída produzida

```text
 Principal
    - Início
    - Produtos
     Suporte
        - FAQ
        - Contato
        - Abrir Chamado
     Minha Conta
        - Perfil
        - Pedidos
        - Sair
```

---

# Fluxo de execução (passo a passo)

## Passo 1

O cliente cria o menu principal.

```
Menu Principal
```

---

## Passo 2

São adicionados itens simples.

```
Principal

Início
Produtos
```

---

## Passo 3

É criado outro Menu.

```
Suporte
```

---

## Passo 4

São adicionados itens ao submenu.

```
Suporte

FAQ
Contato
Abrir Chamado
```

---

## Passo 5

O submenu é adicionado ao menu principal.

Agora temos uma árvore.

```
Principal

├── Início
├── Produtos
└── Suporte
      ├── FAQ
      ├── Contato
      └── Abrir Chamado
```

---

## Passo 6

O cliente executa:

```ts
menuPrincipal.renderizar(0);
```

---

## Passo 7

O método percorre todos os componentes.

Quando encontra um Item:

```
renderiza apenas ele
```

Quando encontra outro Menu:

```
renderiza o próprio menu

↓

percorre seus filhos

↓

cada filho também renderiza
```

Observe que isso é uma chamada **recursiva**. Cada `Menu` delega a responsabilidade de renderização aos seus próprios componentes, formando um percurso em profundidade (_Depth-First Traversal_).

---

# Como reconhecer que é Composite?

Alguns sinais indicam fortemente o uso desse padrão:

- Existe uma estrutura em árvore.
- Existe uma relação **parte-todo**.
- Objetos simples e compostos precisam ser tratados da mesma maneira.
- O código cliente não deve conhecer os tipos concretos.
- As operações são executadas recursivamente na estrutura.

---

# Vantagens

## Código muito mais simples

Não há necessidade de verificar o tipo do objeto.

---

## Polimorfismo

Todos respondem ao mesmo método.

```ts
renderizar();
```

---

## Extensível

Podemos criar novos componentes sem alterar o cliente.

Exemplo:

- MenuDinamico
- MenuFavoritos
- MenuAdministrador

Todos continuam funcionando.

---

## Redução de condicionais

Evita dezenas de:

```ts
if (...)

else if (...)

switch (...)
```

---

## Recursão natural

Árvores são naturalmente percorridas utilizando Composite.

---

# Desvantagens

## Pode facilitar modelagens excessivamente genéricas

Às vezes um componente nunca deveria possuir filhos, mas a interface pode induzir a essa possibilidade se for mal projetada.

---

## Pode aumentar a complexidade

Para estruturas muito simples, utilizar Composite pode ser mais complexo do que criar duas ou três classes específicas.

---

## Depuração

Em árvores muito profundas, acompanhar o fluxo recursivo pode ser mais difícil para iniciantes.

---

# Quando utilizar

Utilize Composite quando:

- houver hierarquias;
- existir relação parte-todo;
- objetos individuais e grupos precisarem ser tratados da mesma forma;
- desejar percorrer estruturas recursivas;
- quiser eliminar verificações constantes de tipo.

Exemplos comuns:

- Menus
- Pastas e arquivos
- Categorias
- Organogramas
- Árvores DOM (HTML)
- Componentes de interfaces gráficas
- Comentários em redes sociais
- Produtos compostos (kits)

---

# Quando evitar

Evite Composite quando:

- não existir hierarquia;
- todos os objetos forem independentes;
- a estrutura nunca possuir níveis;
- a relação parte-todo não fizer sentido.

Nesses casos, uma coleção simples (`Array`, `Set`, `Map`) geralmente é suficiente.

---

# Relação com os princípios SOLID

## S — Single Responsibility Principle (SRP)

Cada classe possui uma única responsabilidade:

- `ItemMenu` representa um item simples.
- `Menu` gerencia um conjunto de componentes.

---

## O — Open/Closed Principle (OCP)

É possível adicionar novos tipos de componentes sem modificar o código existente.

Basta implementar:

```ts
ComponenteMenu;
```

---

## L — Liskov Substitution Principle (LSP)

Qualquer implementação de `ComponenteMenu` pode substituir outra sem quebrar o funcionamento do cliente.

---

## I — Interface Segregation Principle (ISP)

A interface é pequena e objetiva, contendo apenas as operações realmente necessárias aos componentes.

---

## D — Dependency Inversion Principle (DIP)

O cliente depende da abstração (`ComponenteMenu`) e não das implementações concretas (`ItemMenu` ou `Menu`).

---

# Anti-exemplo

Um erro comum é fazer o cliente descobrir o tipo do objeto para decidir como tratá-lo:

```ts
if (componente instanceof Menu) {
  // percorre os filhos
} else if (componente instanceof ItemMenu) {
  // renderiza apenas o item
}
```

Esse código viola o princípio do polimorfismo e torna a aplicação difícil de manter. Com Composite, cada objeto sabe como executar sua própria responsabilidade.

---

# Exercício proposto

Implemente um sistema de gerenciamento de diretórios utilizando o padrão Composite.

### Requisitos

Crie a interface:

```ts
ComponenteSistemaArquivos;
```

Depois implemente:

- `Arquivo`
- `Pasta`

Cada arquivo deve possuir:

- nome
- tamanho (KB)

Cada pasta poderá conter:

- arquivos
- outras pastas

Implemente os seguintes métodos:

- `adicionar()`
- `remover()`
- `listar()`
- `calcularTamanhoTotal()`

### Desafio extra

Implemente uma classe chamada:

```text
PastaCompactada
```

Ela deverá:

- implementar a mesma interface;
- armazenar outros componentes;
- calcular o tamanho total aplicando uma taxa de compressão (por exemplo, 50%);
- funcionar normalmente quando adicionada a qualquer outra pasta, sem exigir alterações no código cliente.

> **Objetivo do exercício:** praticar o uso de composição, recursão, polimorfismo e os princípios do padrão Composite, reforçando também os conceitos de abstração e desacoplamento.
