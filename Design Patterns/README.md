# Design Patterns com TypeScript

> Este material apresenta os **21 Design Patterns clássicos (GoF)** utilizando **TypeScript**, com foco em aplicações práticas no desenvolvimento de software moderno.
>
> Cada padrão é explicado de forma objetiva, acompanhado de exemplos do mundo real e implementações em TypeScript.

---

# O que são Design Patterns?

**Design Patterns (Padrões de Projeto)** são soluções reutilizáveis para problemas recorrentes encontrados durante o desenvolvimento de software orientado a objetos.

Eles **não são bibliotecas**, **frameworks** ou **trechos de código prontos**. Em vez disso, representam modelos de solução que ajudam desenvolvedores a criar sistemas mais organizados, flexíveis, reutilizáveis e fáceis de manter.

Um Design Pattern descreve uma forma de estruturar classes, objetos e responsabilidades para resolver um determinado tipo de problema arquitetural.

Em outras palavras, um padrão de projeto funciona como um **guia de boas práticas**, permitindo que diferentes equipes resolvam problemas semelhantes utilizando soluções amplamente conhecidas pela comunidade.

---

# Origem dos Design Patterns

Embora ideias semelhantes já existissem anteriormente, o conceito de **Design Patterns** ganhou notoriedade em **1994**, com a publicação do livro:

> **Design Patterns: Elements of Reusable Object-Oriented Software**

A obra foi escrita por quatro pesquisadores conhecidos mundialmente como **Gang of Four (GoF)**:

- Erich Gamma
- Richard Helm
- Ralph Johnson
- John Vlissides

O livro reuniu padrões já utilizados por desenvolvedores experientes e os organizou de maneira sistemática, tornando-se uma das obras mais influentes da Engenharia de Software.

Até hoje, seus conceitos continuam sendo ensinados em universidades e utilizados em linguagens como Java, C#, C++, Python, PHP, Kotlin e TypeScript.

---

# A influência de Christopher Alexander

O termo **Design Pattern** não surgiu originalmente na Computação.

Ele foi inspirado nos trabalhos do arquiteto Christopher Alexander, que estudava padrões recorrentes utilizados na construção de cidades, edifícios e espaços urbanos.

Em seu livro **A Pattern Language**, Alexander demonstrava que determinados problemas de arquitetura apareciam repetidamente e que existiam soluções que funcionavam bem na maioria dos casos.

Os autores do livro _Design Patterns_ aplicaram essa mesma ideia ao desenvolvimento de software: se determinados problemas aparecem constantemente durante a criação de sistemas, então faz sentido documentar soluções reutilizáveis para esses problemas.

---

# Qual problema os Design Patterns resolvem?

Durante o desenvolvimento de software é comum enfrentar desafios como:

- excesso de acoplamento entre classes;
- dificuldade para reutilizar código;
- manutenção complexa;
- crescimento desorganizado da aplicação;
- dependências rígidas entre componentes;
- dificuldade para realizar testes;
- código duplicado;
- mudanças que exigem alterações em diversos arquivos.

Os Design Patterns oferecem maneiras conhecidas e testadas de minimizar esses problemas.

Em vez de reinventar soluções a cada projeto, o desenvolvedor pode aplicar um padrão adequado para organizar melhor o código.

---

# Benefícios da utilização

Entre as principais vantagens estão:

- maior reutilização de código;
- melhor organização da arquitetura;
- redução do acoplamento entre classes;
- aumento da coesão dos componentes;
- facilidade de manutenção;
- facilidade para realizar testes;
- maior flexibilidade para evoluir o sistema;
- melhoria na comunicação entre desenvolvedores.

Além disso, conhecer Design Patterns facilita a leitura de códigos de terceiros, pois muitos frameworks e bibliotecas utilizam esses padrões internamente.

---

# Design Patterns não são regras

Um erro comum entre iniciantes é acreditar que todo projeto precisa utilizar vários padrões de projeto.

Isso não é verdade.

Os Design Patterns devem ser utilizados **quando realmente resolvem um problema existente**.

Aplicar um padrão sem necessidade pode aumentar a complexidade do sistema, tornando o código mais difícil de entender e manter.

Por esse motivo, costuma-se dizer:

> **"Conheça os padrões para reconhecer quando eles são necessários, e não para utilizá-los em todo lugar."**

---

# Relação com os princípios SOLID

Grande parte dos Design Patterns segue naturalmente os princípios **SOLID**, como:

- responsabilidade única;
- baixo acoplamento;
- alta coesão;
- inversão de dependência;
- facilidade de extensão sem modificar código existente.

Por esse motivo, é comum estudar SOLID antes ou juntamente com Design Patterns.

Os padrões podem ser vistos como aplicações práticas desses princípios em problemas recorrentes.

---

# Como os padrões são classificados?

Os 21 padrões clássicos do GoF são divididos em três grandes grupos.

## 1. Padrões Criacionais (Creational)

São responsáveis pela criação de objetos.

Seu objetivo é tornar o processo de instanciação mais flexível, desacoplado e reutilizável.

Quando utilizar:

- esconder a lógica de criação;
- reduzir dependências;
- controlar a criação de objetos;
- criar famílias de objetos relacionados.

---

## 2. Padrões Estruturais (Structural)

Tratam da organização entre classes e objetos.

Seu foco é facilitar a composição de estruturas maiores sem aumentar o acoplamento.

Quando utilizar:

- integrar componentes incompatíveis;
- adicionar funcionalidades sem modificar classes existentes;
- simplificar sistemas complexos;
- compartilhar estruturas.

---

## 3. Padrões Comportamentais (Behavioral)

Descrevem como objetos colaboram e se comunicam.

Seu objetivo é distribuir responsabilidades de maneira organizada.

Quando utilizar:

- encapsular algoritmos;
- controlar fluxos de execução;
- implementar eventos;
- reduzir dependências entre objetos.

---

# Como este material está organizado

Cada padrão possui um arquivo independente contendo:

- definição do padrão;
- problema que ele resolve;
- quando utilizar;
- vantagens e desvantagens;
- diagrama simplificado (quando aplicável);
- cenário do mundo real;
- implementação completa em TypeScript;
- explicação detalhada do código;
- exemplos de uso.

Essa organização permite estudar cada padrão separadamente e compreender seu contexto antes de analisar a implementação.

---

# Índice dos Design Patterns (GoF)

Os **21 padrões clássicos** estão organizados em três categorias:

## Criacionais (5)

1. [Abstract Factory](./01-abstract-factory.md)
2. [Builder](./02-builder.md)
3. [Factory Method](./03-factory-method.md)
4. [Prototype](./04-prototype.md)
5. [Singleton](./05-singleton.md)

---

## Estruturais (7)

6. [Adapter](./06-adapter.md)
7. [Bridge](./07-bridge.md)
8. [Composite](./08-composite.md)
9. [Decorator](./09-decorator.md)
10. [Facade](./10-facade.md)
11. [Flyweight](./11-flyweight.md)
12. [Proxy](./12-proxy.md)

---

## Comportamentais (9)

13. [Chain of Responsibility](./13-chain-of-responsibility.md)
14. [Command](./14-command.md)
15. [Interpreter](./15-interpreter.md)
16. [Iterator](./16-iterator.md)
17. [Mediator](./17-mediator.md)
18. [Memento](./18-memento.md)
19. [Observer](./19-observer.md)
20. [State](./20-state.md)
21. [Strategy](./21-strategy.md)

---

# Considerações finais

Os Design Patterns representam décadas de experiência acumulada na construção de sistemas orientados a objetos. Eles fornecem soluções testadas para problemas recorrentes, mas não substituem a análise crítica do desenvolvedor.

Ao longo deste material, cada padrão será apresentado em detalhes, mostrando não apenas **como implementá-lo em TypeScript**, mas principalmente **quando utilizá-lo**, **qual problema ele resolve** e **quais são seus impactos na arquitetura de software**.

O objetivo não é decorar os padrões, mas desenvolver a capacidade de identificar situações em que cada um deles pode contribuir para a criação de sistemas mais organizados, flexíveis e sustentáveis ao longo do tempo.
