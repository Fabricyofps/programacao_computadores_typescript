# 21) Strategy (Estratégia)

> **Categoria:** Comportamental

---

# O que é o padrão Strategy?

O **Strategy (Estratégia)** é um padrão de projeto comportamental utilizado quando um sistema possui **diferentes formas de realizar uma mesma operação** e precisa escolher entre elas de maneira flexível.

A ideia principal é:

> **Separar algoritmos que possuem o mesmo objetivo em classes independentes, permitindo trocar a implementação sem alterar quem utiliza o algoritmo.**

Em vez de criar vários `if` ou `switch`, o sistema trabalha com uma abstração comum.

---

# Problema que resolve

Imagine um sistema de cálculo de frete.

Existem diferentes regras:

## Frete econômico

```text
Preço menor

Entrega mais lenta
```

---

## Frete expresso

```text
Preço maior

Entrega mais rápida
```

---

## Frete promocional

```text
Aplica desconto

Possui regras específicas
```

Uma solução comum seria:

```ts
calcularFrete(tipoFrete) {


    switch(tipoFrete) {


        case "ECONOMICO":

            // cálculo econômico


        break;


        case "EXPRESSO":

            // cálculo expresso


        break;


        case "PROMOCAO":

            // cálculo promocional


        break;


    }

}
```

Esse código funciona no início.

Porém, com o crescimento do sistema:

- novos tipos de frete aparecem;
- o método fica enorme;
- qualquer alteração aumenta o risco de erros.

O Strategy resolve criando uma classe para cada algoritmo.

---

# Motivação (cenário real)

Imagine um aplicativo de pagamentos.

Um cliente pode pagar utilizando:

```text
Cartão

Pix

Boleto

Carteira Digital
```

Todos realizam a mesma ação:

```text
realizar pagamento
```

Mas cada um possui uma implementação diferente.

O sistema principal não precisa saber como cada pagamento funciona.

Ele apenas chama:

```ts
processarPagamento();
```

A estratégia escolhida define o comportamento.

---

# Analogia do mundo real

Imagine uma pessoa indo ao trabalho.

Ela possui diferentes estratégias de transporte:

```text
Carro

Ônibus

Bicicleta

Metrô
```

O objetivo é o mesmo:

```text
Chegar ao trabalho
```

Mas o algoritmo muda:

- custo;
- tempo;
- distância;
- conforto.

O meio de transporte escolhido representa a Strategy.

---

# Ideia central

O padrão possui três elementos:

```text
Context

↓

Strategy

↓

Concrete Strategies
```

O Context utiliza uma estratégia, mas não conhece os detalhes internos dela.

---

# Estrutura do padrão

```text
                Cliente

                   |

                   ▼

             +------------+
             |  Context   |
             +------------+
             | estrategia |
             +------+-----+
                    |
                    |
                    ▼

             +--------------+
             |  Strategy    |
             +--------------+
             | executar()   |
             +------+-------+
                    |
       -----------------------------
       |             |             |
       ▼             ▼             ▼

  Estrategia A  Estrategia B  Estrategia C
```

---

# Participantes

## 1. Context

É o objeto que utiliza o algoritmo.

Responsável por:

- armazenar a estratégia atual;
- executar a operação delegando para a estratégia.

Exemplo:

```text
CalculadoraFrete
```

---

## 2. Strategy

Define o contrato comum entre os algoritmos.

Exemplo:

```ts
calcular();
```

---

## 3. Concrete Strategy

Implementa uma variação específica.

Exemplos:

```text
FreteEconomico

FreteExpresso

FreteComDesconto
```

---

## 4. Cliente

Escolhe qual estratégia será utilizada.

---

# Diagrama UML simplificado

```text
                 Cliente

                    |

                    ▼

          +----------------+
          | Calculadora    |
          | Frete          |
          +----------------+
          | estrategia     |
          +--------+-------+
                   |
                   |
                   ▼

          +----------------+
          | CalculoFrete   |
          +----------------+
          | calcular()     |
          +----------------+
                   |
      --------------------------------
      |              |               |
      ▼              ▼               ▼

FreteEconomico FreteExpresso FreteDesconto
```

---

# Implementação completa em TypeScript

```ts
/**
 * ============================================================
 * STRATEGY
 * ============================================================
 *
 * Exemplo:
 *
 * Sistema de cálculo de frete.
 *
 * O cálculo pode mudar dependendo
 * da estratégia escolhida.
 */

/**
 * ============================================================
 * MODELO DO PEDIDO
 * ============================================================
 */

type PedidoFrete = {
  pesoKg: number;

  distanciaKm: number;

  valorProdutos: number;
};

/**
 * ============================================================
 * INTERFACE STRATEGY
 * ============================================================
 */

interface EstrategiaFrete {
  calcular(pedido: PedidoFrete): number;
}

/**
 * ============================================================
 * ESTRATÉGIA: FRETE ECONÔMICO
 * ============================================================
 */

class FreteEconomico implements EstrategiaFrete {
  calcular(pedido: PedidoFrete): number {
    return 10 + pedido.pesoKg * 1.2 + pedido.distanciaKm * 0.15;
  }
}

/**
 * ============================================================
 * ESTRATÉGIA: FRETE EXPRESSO
 * ============================================================
 */

class FreteExpresso implements EstrategiaFrete {
  calcular(pedido: PedidoFrete): number {
    return 20 + pedido.pesoKg * 2 + pedido.distanciaKm * 0.25;
  }
}

/**
 * ============================================================
 * ESTRATÉGIA: FRETE COM DESCONTO
 * ============================================================
 */

class FreteComDesconto implements EstrategiaFrete {
  constructor(private percentualDesconto: number) {}

  calcular(pedido: PedidoFrete): number {
    const valorBase = new FreteEconomico().calcular(pedido);

    const desconto = valorBase * (this.percentualDesconto / 100);

    return valorBase - desconto;
  }
}

/**
 * ============================================================
 * CONTEXT
 * ============================================================
 *
 * Não sabe como cada cálculo funciona.
 *
 * Apenas executa a estratégia escolhida.
 */

class CalculadoraFrete {
  constructor(private estrategia: EstrategiaFrete) {}

  alterarEstrategia(estrategia: EstrategiaFrete): void {
    this.estrategia = estrategia;
  }

  calcular(pedido: PedidoFrete): number {
    return this.estrategia.calcular(pedido);
  }
}

/**
 * ============================================================
 * CLIENTE
 * ============================================================
 */

const pedido: PedidoFrete = {
  pesoKg: 8,

  distanciaKm: 120,

  valorProdutos: 300,
};

const calculadora = new CalculadoraFrete(new FreteEconomico());

console.log(
  'Frete econômico:',

  calculadora.calcular(pedido),
);

calculadora.alterarEstrategia(new FreteExpresso());

console.log(
  'Frete expresso:',

  calculadora.calcular(pedido),
);

calculadora.alterarEstrategia(new FreteComDesconto(15));

console.log(
  'Frete com desconto:',

  calculadora.calcular(pedido),
);
```

---

# Saída esperada

```text
Frete econômico:
36.8


Frete expresso:
66


Frete com desconto:
31.28
```

---

# Fluxo de execução (passo a passo)

## Passo 1

O cliente cria a calculadora.

```text
CalculadoraFrete
```

com:

```text
FreteEconomico
```

como estratégia inicial.

---

## Passo 2

O cliente solicita o cálculo.

```ts
calcular(pedido);
```

---

## Passo 3

O Context delega:

```text
CalculadoraFrete

↓

EstrategiaFrete
```

---

## Passo 4

A estratégia executa seu algoritmo.

Exemplo:

```text
FreteEconomico

↓

Calcula valor
```

---

## Passo 5

O cliente pode trocar a estratégia.

```ts
alterarEstrategia(new FreteExpresso());
```

---

## Passo 6

O mesmo Context agora utiliza outro algoritmo.

Sem alterar:

- Calculadora;
- Pedido;
- Cliente.

---

# Fluxo interno

```text
Cliente

↓

Context

↓

Strategy escolhida

↓

Algoritmo específico
```

---

# Como reconhecer que é Strategy?

Alguns sinais indicam esse padrão:

- Existem várias formas de realizar uma mesma tarefa.
- O algoritmo pode mudar em tempo de execução.
- Existe uma interface comum entre implementações.
- Há muitos `if/switch` escolhendo algoritmos.

Exemplos:

- Cálculo de impostos;
- Meios de pagamento;
- Compressão de arquivos;
- Ordenação;
- Autenticação;
- Fretes;
- Cálculo de descontos.

---

# Vantagens

## Elimina condicionais gigantes

Substitui:

```ts
switch(tipo)
```

por classes especializadas.

---

## Fácil extensão

Novas estratégias podem ser adicionadas.

Exemplo:

```text
FreteInternacional
```

sem modificar o código existente.

---

## Código mais organizado

Cada algoritmo possui sua própria classe.

---

## Troca dinâmica

A estratégia pode mudar durante a execução.

---

# Desvantagens

## Mais classes

Cada variação vira uma implementação.

---

## Complexidade inicial

Para problemas pequenos pode parecer exagerado.

---

## Muitas estratégias

Um sistema com dezenas de estratégias precisa de boa organização.

---

# Quando utilizar

Utilize Strategy quando:

- existem algoritmos alternativos;
- regras de negócio variam;
- o comportamento precisa ser escolhido dinamicamente.

Exemplos:

- Sistemas financeiros;
- Cálculo de frete;
- Processamento de pagamentos;
- Sistemas de descontos;
- Inteligência artificial em jogos;
- Ordenação e filtros.

---

# Quando evitar

Evite quando:

- existe apenas uma implementação;
- o comportamento nunca muda;
- criar classes extras não traz benefício.

---

# Relação com os princípios SOLID

## S — Single Responsibility Principle (SRP)

Cada estratégia possui uma única responsabilidade.

Exemplo:

```text
FreteExpresso

↓

Somente calcula frete expresso
```

---

## O — Open/Closed Principle (OCP)

Novas estratégias podem ser adicionadas sem modificar o Context.

---

## L — Liskov Substitution Principle (LSP)

Qualquer estratégia pode substituir outra:

```ts
EstrategiaFrete;
```

---

## I — Interface Segregation Principle (ISP)

A interface contém somente o comportamento necessário:

```ts
calcular();
```

---

## D — Dependency Inversion Principle (DIP)

O Context depende da abstração:

```ts
EstrategiaFrete;
```

e não de implementações específicas.

---

# Anti-exemplo

Uma implementação problemática:

```ts
class CalculadoraFrete {
  calcular(
    tipo: string,

    pedido: PedidoFrete,
  ) {
    if (tipo === 'ECONOMICO') {
      // cálculo econômico
    } else if (tipo === 'EXPRESSO') {
      // cálculo expresso
    } else if (tipo === 'DESCONTO') {
      // cálculo desconto
    }
  }
}
```

Problemas:

- cresce indefinidamente;
- viola Open/Closed;
- mistura vários algoritmos.

Com Strategy:

```text
Calculadora

↓

Estrategia

↓

Algoritmo escolhido
```

Cada regra fica isolada.

---

# Exercício proposto

Implemente um sistema de pagamento utilizando Strategy.

## Estratégias:

Crie uma interface:

```ts
MetodoPagamento;
```

com:

```ts
pagar(valor:number):void
```

---

Implemente:

## PagamentoCartao

Regras:

- adiciona taxa de 3%.

---

## PagamentoPix

Regras:

- aplica desconto de 5%.

---

## PagamentoBoleto

Regras:

- adiciona prazo de compensação.

---

## Contexto

Crie:

```ts
ProcessadorPagamento;
```

que recebe qualquer estratégia.

---

# Desafio extra

Permita escolher o pagamento dinamicamente:

```ts
processador.usar(new PagamentoPix());
```

Depois:

```ts
processador.usar(new PagamentoCartao());
```

O objetivo é compreender como o padrão Strategy permite criar sistemas flexíveis, substituindo algoritmos sem alterar o código que utiliza esses comportamentos.
