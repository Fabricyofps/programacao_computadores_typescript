# 04) Prototype (Protótipo)

## Categoria

**Padrão Criacional (Creational Pattern)**

---

# O que é o Prototype?

O **Prototype (Protótipo)** é um padrão de projeto **criacional** cujo objetivo é **criar novos objetos a partir da cópia de um objeto já existente**, ao invés de construí-los do zero.

Em vez de utilizar um construtor para inicializar todos os atributos novamente, criamos uma cópia de um objeto já configurado e, posteriormente, realizamos apenas as alterações necessárias.

O Prototype é especialmente útil quando:

- a criação do objeto é lenta;
- o objeto possui muitos atributos;
- a inicialização exige diversas configurações;
- precisamos criar várias variações muito parecidas entre si.

A ideia principal é bastante simples:

> Em vez de construir novamente, **copie um objeto existente**.

---

# Problema que o Prototype resolve

Imagine que você esteja desenvolvendo um sistema de e-commerce.

Existe um produto chamado:

```text
Camisa Slim
```

Esse produto possui dezenas de informações:

- nome
- categoria
- descrição
- fabricante
- garantia
- peso
- dimensões
- preço base
- estoque mínimo
- atributos técnicos
- imagens
- entre vários outros campos.

Agora imagine que você precise criar novas variações.

Por exemplo:

- Camisa Vermelha
- Camisa Azul
- Camisa Preta
- Camisa Verde

A maior parte das informações será exatamente igual.

Apenas alguns atributos mudam.

Sem o Prototype, provavelmente faríamos algo assim:

```ts
new Produto(...);
new Produto(...);
new Produto(...);
new Produto(...);
```

Repetindo dezenas de parâmetros todas as vezes.

Além de gerar muito código repetido, isso aumenta a chance de erros.

Com o Prototype fazemos apenas:

```ts
const camisaAzul = camisaBase.clone();
```

Depois alteramos somente os campos necessários.

---

# Motivação (Cenário Real)

Imagine uma gráfica que produz cartões de visita.

Um cliente possui um modelo padrão contendo:

- logotipo;
- cores;
- fonte;
- endereço;
- telefone;
- layout.

Agora o cliente contrata mais dez funcionários.

Cada cartão será praticamente igual.

A única informação diferente será:

- nome;
- cargo;
- e-mail.

Seria desperdício criar cada cartão do zero.

Muito mais simples copiar o modelo existente e alterar apenas os dados pessoais.

É exatamente isso que o Prototype faz.

---

# Analogia do Mundo Real

Imagine um professor entregando uma prova para 40 alunos.

Ele não escreve cada prova manualmente.

Ele cria apenas uma.

Depois utiliza uma fotocopiadora para gerar várias cópias.

Cada aluno recebe uma prova idêntica.

Posteriormente, cada um escreve seu próprio nome.

O Prototype funciona da mesma maneira.

Existe um objeto original.

Os demais objetos surgem como cópias dele.

---

# Estrutura do Padrão

```text
                +----------------------+
                |      Protótipo       |
                |----------------------|
                | + clone()            |
                +----------▲-----------+
                           |
                           |
                 +---------+----------+
                 | ProdutoTemplate    |
                 |--------------------|
                 | clone()            |
                 +---------▲----------+
                           |
                        Cliente
```

O cliente nunca precisa conhecer todos os detalhes de construção.

Ele apenas solicita uma cópia.

---

# Participantes

## 1. Protótipo (Prototype)

É a interface que define o método responsável pela clonagem.

Exemplo:

```ts
interface Produto {
  clone(): Produto;
}
```

Toda classe clonável deverá implementar esse contrato.

---

## 2. Protótipo Concreto (Concrete Prototype)

É a implementação real.

Ela sabe exatamente como criar uma cópia de si mesma.

No nosso exemplo:

```text
ProdutoTemplate
```

---

## 3. Cliente

É quem solicita uma cópia do objeto.

O cliente não precisa conhecer:

- construtores;
- lógica de inicialização;
- detalhes internos.

Ele simplesmente chama:

```ts
clone();
```

---

# Implementação Completa em TypeScript

```ts
/**
 * ============================================================
 * INTERFACE PROTOTYPE
 * ============================================================
 *
 * Toda classe que desejar ser clonada deverá implementar
 * este contrato.
 */

interface Produto {
  id: string;
  nome: string;
  precoBase: number;
  atributos: Record<string, string | number>;
  /**
   * Retorna uma cópia completa do objeto.
   */
  clone(): Produto;
}

/**
 * ============================================================
 * PROTOTYPE CONCRETO
 * ============================================================
 */

class ProdutoTemplate implements Produto {
  constructor(
    public id: string,
    public nome: string,
    public precoBase: number,
    public atributos: Record<string, string | number>,
  ) {}

  /**
   * Cria uma NOVA instância copiando os dados atuais.
   *
   * Observe que retornamos um novo objeto.
   *
   * Não estamos retornando "this".
   */

  public clone(): Produto {
    /**
     * Criamos uma cópia dos atributos.
     *
     * Isso evita que os dois objetos compartilhem
     * a mesma referência na memória.
     */

    const novosAtributos = {
      ...this.atributos,
    };

    return new ProdutoTemplate(this.id, this.nome, this.precoBase, novosAtributos);
  }
}

/**
 * ============================================================
 * OBJETO ORIGINAL (PROTÓTIPO)
 * ============================================================
 */

const produtoModelo = new ProdutoTemplate('template-001', 'Camisa Slim', 79.9, {
  modelo: 'Slim',
  garantiaMeses: 12,
  tecido: 'Algodão',
});

/**
 * ============================================================
 * CLONANDO O PRODUTO
 * ============================================================
 */

const camisaVermelha = produtoModelo.clone();

camisaVermelha.id = 'produto-101';

camisaVermelha.atributos = {
  ...camisaVermelha.atributos,
  cor: 'Vermelha',
  tamanho: 'M',
};

/**
 * ============================================================
 * OUTRA CLONAGEM
 * ============================================================
 */

const camisaAzul = produtoModelo.clone();

camisaAzul.id = 'produto-102';
camisaAzul.atributos = {
  ...camisaAzul.atributos,
  cor: 'Azul',
  tamanho: 'G',
};

console.log(produtoModelo);
console.log(camisaVermelha);
console.log(camisaAzul);
```

---

# Fluxo de Execução (Passo a Passo)

### Passo 1

Criamos um objeto completamente configurado.

```ts
const produtoModelo = new ProdutoTemplate(...)
```

↓

### Passo 2

O cliente solicita uma cópia.

```ts
produtoModelo.clone();
```

↓

### Passo 3

O método `clone()` cria uma nova instância.

```ts
new ProdutoTemplate(...)
```

↓

### Passo 4

Todos os atributos são copiados para o novo objeto.

↓

### Passo 5

O cliente modifica apenas os dados necessários.

```ts
camisaVermelha.atributos.cor = 'Vermelha';
```

↓

### Passo 6

O objeto original continua inalterado.

↓

### Passo 7

Agora existem dois objetos independentes.

---

# Cópia Rasa (Shallow Copy) x Cópia Profunda (Deep Copy)

Esse é um dos pontos mais importantes do Prototype.

## Cópia Rasa (Shallow Copy)

Na cópia rasa, objetos internos continuam compartilhando a mesma referência.

Exemplo:

```ts
const copia = original;
```

ou

```ts
const copia = {
  ...original,
};
```

Se existir um objeto dentro do objeto principal, ambos apontarão para o mesmo local da memória.

Isso pode gerar efeitos colaterais inesperados.

---

## Cópia Profunda (Deep Copy)

Na cópia profunda, cada objeto interno também é duplicado.

Assim, alterações em uma instância não afetam a outra.

Em sistemas reais isso pode ser feito utilizando:

- `structuredClone()` (JavaScript moderno)
- bibliotecas especializadas
- métodos próprios de clonagem
- serialização (quando apropriado)

> **Atenção:** utilizar apenas o operador `...` copia apenas o primeiro nível do objeto. Se existirem objetos ou arrays aninhados, será necessário implementar uma clonagem mais profunda.

---

# Diagrama UML Simplificado

```text
                  <<interface>>
                     Produto
             ---------------------
             + clone(): Produto
                     ▲
                     │
        +-----------------------------+
        |      ProdutoTemplate        |
        |-----------------------------|
        | id                          |
        | nome                        |
        | precoBase                   |
        | atributos                   |
        | clone()                     |
        +-------------▲---------------+
                      |
                   Cliente
```

---

# Como reconhecer um Prototype?

Alguns sinais são bastante característicos:

- existe um método chamado `clone()`;
- novos objetos surgem como cópias de outro objeto;
- pequenas alterações são feitas após a clonagem;
- a construção do objeto é complexa ou custosa;
- o cliente evita utilizar diretamente o construtor.

Sempre que você observar essas características, provavelmente está diante do padrão Prototype.

---

# Vantagens

## Evita construções repetitivas

Não é necessário preencher dezenas de atributos novamente.

---

## Melhor desempenho

Quando construir um objeto é caro, copiar costuma ser muito mais rápido.

---

## Redução de duplicação

Grande parte da configuração é reutilizada.

---

## Facilita criação de variações

Ideal para objetos muito parecidos entre si.

---

## Menor chance de erros

Como a maior parte dos dados já existe no protótipo, reduzimos esquecimentos durante a criação.

---

# Desvantagens

## Clonagem pode ser complexa

Principalmente quando existem muitos objetos internos.

---

## Necessidade de cópia profunda

Uma implementação incorreta pode compartilhar referências entre objetos.

---

## Consumo de memória

Cada clone ocupa espaço adicional na memória.

---

## Maior responsabilidade da classe

A própria classe precisa saber como criar corretamente uma cópia de si mesma.

---

# Quando utilizar

Utilize Prototype quando:

- criar objetos é caro;
- existem muitas configurações repetidas;
- diversas variações são geradas a partir de um modelo;
- deseja reduzir código duplicado;
- deseja evitar construtores enormes.

---

# Quando evitar

Evite utilizar quando:

- os objetos possuem poucos atributos;
- a construção é extremamente simples;
- não existem variações do objeto;
- copiar o objeto é mais difícil do que criá-lo novamente.

Nessas situações, utilizar diretamente o construtor costuma ser mais simples e legível.

---

# Relação com os Princípios SOLID

## S — Single Responsibility Principle (SRP)

A classe conhece sua estrutura e também é responsável por saber como gerar uma cópia consistente de si mesma, concentrando a lógica de clonagem em um único lugar.

---

## O — Open/Closed Principle (OCP)

Podemos criar novos tipos de produtos clonáveis implementando a interface `Produto`, sem modificar o código que utiliza o método `clone()`.

---

## L — Liskov Substitution Principle (LSP)

Qualquer implementação da interface `Produto` pode ser utilizada no lugar de outra.

```ts
ProdutoTemplate

↓

ProdutoDigital

↓

ProdutoPersonalizado
```

O cliente continuará chamando apenas:

```ts
clone();
```

---

## I — Interface Segregation Principle (ISP)

A interface define apenas o comportamento essencial para objetos clonáveis.

```ts
interface Produto {
  clone(): Produto;
}
```

Ela não obriga implementações a fornecer funcionalidades que não utilizam.

---

## D — Dependency Inversion Principle (DIP)

O cliente depende da abstração `Produto`, e não de uma implementação específica como `ProdutoTemplate`.

Isso facilita substituições e testes.

---

# Anti-exemplo (O que NÃO fazer)

Neste exemplo, toda vez que uma nova variação é criada, repetimos toda a lógica de construção do objeto.

```ts
function criarProdutoVariacao(cor: string) {
  return new ProdutoTemplate('produto', 'Camisa Slim', 79.9, {
    modelo: 'Slim',
    garantiaMeses: 12,
    tecido: 'Algodão',
    cor,
  });
}
```

Problemas:

- repetição de código;
- manutenção mais difícil;
- maior chance de esquecer algum atributo;
- alterações precisam ser replicadas em vários locais;
- qualquer mudança na estrutura do produto exige atualizar todas as construções.

Com o Prototype, basta manter um modelo bem configurado e gerar cópias sempre que necessário.

---

# Exercício Proposto

Você foi contratado para evoluir o sistema de e-commerce.

Agora, além das camisas, será necessário cadastrar canecas personalizadas.

## Requisitos

Crie uma nova classe chamada:

```text
ProdutoCaneca
```

Ela deve implementar a interface `Produto`.

Adicione atributos específicos, como:

- capacidade;
- material;
- podeIrAoMicroondas.

Implemente corretamente o método `clone()`.

Depois:

1. Crie um modelo padrão de caneca.
2. Gere três clones.
3. Personalize cada clone com:
   - cor diferente;
   - estampa diferente;
   - preço diferente.

4. Exiba todos no console.

### Desafio Extra

Adicione ao produto um atributo mais complexo, por exemplo:

```ts
historicoDePrecos: number[]
```

ou

```ts
fabricante: {
  nome: string;
  endereco: string;
}
```

Implemente uma **cópia profunda (Deep Copy)** para garantir que alterações feitas em um clone **não afetem** os demais objetos.

Ao finalizar, responda às seguintes perguntas:

- O objeto original permaneceu inalterado?
- Os clones compartilham alguma referência na memória?
- Em quais situações uma cópia rasa (`Shallow Copy`) causaria problemas?
- Quais vantagens o Prototype trouxe em comparação à criação manual de todos os objetos?

Se você conseguiu responder a essas perguntas e criar clones independentes do objeto original, então compreendeu o verdadeiro propósito do **Prototype**: **reutilizar objetos existentes para criar novas instâncias de forma eficiente, segura e com muito menos código repetido**.
