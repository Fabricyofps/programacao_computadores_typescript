# 02) Builder (Construtor)

## Categoria

**Padrão Criacional (Creational Pattern)**

---

# O que é o padrão?

O **Builder** (Construtor) é um padrão criacional cujo objetivo é separar o processo de construção de um objeto complexo da sua representação final.

Ao invés de criar um objeto utilizando um único construtor com muitos parâmetros ou diversos `if/else`, o Builder permite construir o objeto **passo a passo**, tornando o código mais legível, flexível e reutilizável.

Esse padrão é especialmente útil quando um objeto possui muitas propriedades opcionais ou quando existem diferentes maneiras de montar esse mesmo objeto.

---

# Problema que resolve

Imagine que sua aplicação precisa gerar diferentes tipos de relatórios.

Alguns usuários desejam apenas:

- Sumário;
- Gráficos.

Outros precisam de:

- Sumário;
- Gráficos;
- Tabelas;
- Conclusões.

Outros ainda desejam incluir:

- Assinaturas;
- Rodapé;
- Informações confidenciais;
- Indicadores personalizados.

Sem o Builder, normalmente o código acaba assim:

```ts
const relatorio = new Relatorio('Vendas', true, false, true, true, false, true, false, true);
```

Após alguns meses, ninguém consegue lembrar o significado de cada parâmetro.

Outra alternativa comum é utilizar dezenas de propriedades opcionais ou diversos blocos de `if/else`, tornando a criação do objeto difícil de entender e manter.

O Builder resolve esse problema permitindo montar o objeto de forma gradual.

---

# Motivação (Cenário real)

Imagine um sistema de Business Intelligence (BI).

Cada cliente pode configurar quais seções deseja incluir em um relatório.

Alguns relatórios são destinados para executivos e possuem apenas indicadores resumidos.

Já outros são técnicos e apresentam todas as informações disponíveis.

Apesar de ambos serem relatórios, a forma como são construídos é diferente.

O Builder permite reutilizar o mesmo processo de construção para gerar diferentes versões do mesmo produto.

---

# Analogia do mundo real

Imagine pedir um sanduíche em uma hamburgueria.

Você escolhe os ingredientes um por um.

- Pão
- Carne
- Queijo
- Bacon
- Salada
- Molhos

Ao final, todos esses ingredientes formam um único produto.

Você não recebe o sanduíche pronto logo após escolher o pão.

Ele é construído passo a passo até ficar completo.

O Builder funciona exatamente dessa maneira.

---

# Estrutura do padrão

```text
               Cliente
                  │
                  ▼
        DiretorRelatorio (opcional)
                  │
                  ▼
         BuilderRelatorio (interface)
                  ▲
                  │
      BuilderRelatorioPadrao
                  │
                  ▼
             Relatório
```

O cliente solicita a construção.

O Builder monta o objeto passo a passo.

Ao final, o produto é entregue completamente construído.

---

# Participantes

## Produto

É o objeto complexo que será construído.

No exemplo:

- `Relatorio`

---

## Builder

Define todas as etapas necessárias para construir o produto.

No exemplo:

- `BuilderRelatorio`

---

## Builder Concreto

Implementa cada etapa da construção.

No exemplo:

- `BuilderRelatorioPadrao`

---

## Diretor (Opcional)

Responsável por definir uma sequência de construção.

Ele conhece a ordem correta das etapas.

No exemplo:

- `DiretorRelatorio`

---

## Cliente

Solicita a construção do objeto utilizando o Builder.

---

# Implementação completa em TypeScript

```ts
/**********************************************************************
 * BUILDER
 *
 * Exemplo:
 * Sistema de geração de relatórios.
 *
 * O relatório pode possuir diversas seções opcionais.
 *
 * O Builder permite montar o relatório passo a passo,
 * deixando o código mais legível e flexível.
 **********************************************************************/

/*=====================================================
=                     PRODUTO                         =
=====================================================*/

/**
 * Representa o relatório final.
 *
 * Observe que o Builder é responsável por preencher
 * todas essas propriedades durante a construção.
 */
type Relatorio = {
  titulo: string;

  possuiSumario: boolean;

  possuiGraficos: boolean;

  possuiTabelas: boolean;

  possuiConclusoes: boolean;

  metadados: {
    geradoEm: string;
  };
};

/*=====================================================
=                    BUILDER                          =
=====================================================*/

/**
 * Define todas as etapas necessárias
 * para construir um relatório.
 *
 * O retorno "this" permite utilizar
 * uma API fluente (Method Chaining).
 */
interface BuilderRelatorio {
  definirTitulo(titulo: string): this;

  adicionarSumario(): this;

  adicionarGraficos(): this;

  adicionarTabelas(): this;

  adicionarConclusoes(): this;

  construir(): Relatorio;
}

/*=====================================================
=               BUILDER CONCRETO                      =
=====================================================*/

class BuilderRelatorioPadrao implements BuilderRelatorio {
  private titulo = '';

  private possuiSumario = false;

  private possuiGraficos = false;

  private possuiTabelas = false;

  private possuiConclusoes = false;

  definirTitulo(titulo: string): this {
    this.titulo = titulo;

    return this;
  }

  adicionarSumario(): this {
    this.possuiSumario = true;

    return this;
  }

  adicionarGraficos(): this {
    this.possuiGraficos = true;

    return this;
  }

  adicionarTabelas(): this {
    this.possuiTabelas = true;

    return this;
  }

  adicionarConclusoes(): this {
    this.possuiConclusoes = true;

    return this;
  }

  /**
   * Finaliza a construção do objeto.
   *
   * Após chamar este método,
   * o relatório está completamente montado.
   */
  construir(): Relatorio {
    return {
      titulo: this.titulo,

      possuiSumario: this.possuiSumario,

      possuiGraficos: this.possuiGraficos,

      possuiTabelas: this.possuiTabelas,

      possuiConclusoes: this.possuiConclusoes,

      metadados: {
        geradoEm: new Date().toISOString(),
      },
    };
  }
}

/*=====================================================
=                     DIRETOR                         =
=====================================================*/

/**
 * O Diretor conhece "receitas"
 * para montar diferentes tipos de relatório.
 *
 * Sua utilização é opcional.
 */

type PerfilRelatorio = 'executivo' | 'tecnico';

class DiretorRelatorio {
  constructor(private builder: BuilderRelatorio) {}

  montar(perfil: PerfilRelatorio): BuilderRelatorio {
    if (perfil === 'executivo') {
      return this.builder
        .definirTitulo('Relatório Executivo')
        .adicionarSumario()
        .adicionarGraficos()
        .adicionarConclusoes();
    }

    return this.builder
      .definirTitulo('Relatório Técnico')
      .adicionarSumario()
      .adicionarGraficos()
      .adicionarTabelas()
      .adicionarConclusoes();
  }
}

/*=====================================================
=                     CLIENTE                         =
=====================================================*/

const builder = new BuilderRelatorioPadrao();

const diretor = new DiretorRelatorio(builder);

const relatorioExecutivo = diretor.montar('executivo').construir();

console.log(relatorioExecutivo);

const relatorioTecnico = diretor.montar('tecnico').construir();

console.log(relatorioTecnico);
```

---

# Fluxo de execução

```text
Cliente

↓

Cria o Builder

↓

Cria o Diretor

↓

Solicita um perfil de relatório

↓

Diretor executa as etapas

↓

Builder configura cada propriedade

↓

Builder finaliza a construção

↓

Relatório é retornado ao cliente
```

Passo a passo:

1. O cliente cria uma instância do Builder.
2. O Builder inicia o objeto com valores padrão.
3. O Diretor (opcional) define quais etapas serão executadas.
4. Cada método do Builder adiciona uma parte ao relatório.
5. Ao chamar `construir()`, o objeto final é criado.
6. O relatório pronto é devolvido ao cliente.

---

# Diagrama UML simplificado

```text
                    Cliente
                       │
                       ▼
                DiretorRelatorio
                       │
                       ▼

              <<interface>>
             BuilderRelatorio
     + definirTitulo()
     + adicionarSumario()
     + adicionarGraficos()
     + adicionarTabelas()
     + adicionarConclusoes()
     + construir()

                    ▲
                    │
     BuilderRelatorioPadrao
                    │
                    ▼
                Relatorio
```

---

# Vantagens

- Torna a construção de objetos complexos mais legível.
- Evita construtores enormes.
- Permite criar diferentes representações do mesmo objeto.
- Facilita reutilizar a lógica de construção.
- Favorece uma API fluente (Fluent Interface).
- Reduz a chance de esquecer etapas importantes durante a construção.

---

# Desvantagens

- Aumenta a quantidade de classes.
- Pode ser um exagero para objetos simples.
- Em projetos pequenos, um construtor tradicional pode ser suficiente.

---

# Quando utilizar

Utilize o Builder quando:

- O objeto possui muitos atributos.
- Existem muitas propriedades opcionais.
- A construção ocorre em várias etapas.
- Existem diferentes maneiras de montar o mesmo objeto.
- Você deseja tornar o código mais legível.

Exemplos:

- Relatórios.
- Documentos PDF.
- Objetos de configuração.
- Consultas SQL.
- Requisições HTTP.
- Configuração de servidores.
- Construção de interfaces gráficas.

---

# Quando evitar

Evite utilizar quando:

- O objeto possui poucas propriedades.
- Existe apenas uma forma simples de criação.
- Um construtor comum resolve o problema de forma clara.

---

# Relação com SOLID

## SRP (Single Responsibility Principle)

O Builder é responsável apenas pela construção do objeto.

A lógica de negócio permanece em outras classes.

---

## OCP (Open/Closed Principle)

É possível criar novos Builders sem alterar o código existente.

---

## LSP (Liskov Substitution Principle)

Qualquer implementação de `BuilderRelatorio` pode substituir outra.

---

## ISP (Interface Segregation Principle)

A interface do Builder expõe apenas os métodos necessários para construir o objeto.

---

## DIP (Dependency Inversion Principle)

O cliente depende da abstração `BuilderRelatorio`, e não da implementação concreta.

---

# Exercício proposto

A empresa decidiu criar um novo tipo de relatório destinado ao setor financeiro.

Implemente um novo Builder chamado:

- `BuilderRelatorioFinanceiro`

Esse Builder deve gerar relatórios contendo:

- Título personalizado;
- Sumário;
- Tabelas;
- Conclusões;
- Um novo campo chamado `possuiAssinaturaDigital`.

Depois, adapte o Diretor para suportar o perfil:

```text
financeiro
```

Ao final, gere um relatório utilizando o novo Builder.

---

# Resumo

O **Builder** é um padrão criacional utilizado para construir objetos complexos de forma gradual.

Seu principal objetivo é separar **o processo de construção** da **representação final do objeto**, tornando o código mais organizado, legível e reutilizável.

Sempre que um objeto possuir muitas propriedades, diversas combinações de configuração ou uma construção composta por várias etapas, o Builder costuma ser uma excelente escolha.
