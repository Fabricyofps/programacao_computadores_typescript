# 01) Abstract Factory (Fábrica Abstrata)

## Categoria

**Padrão Criacional (Creational Pattern)**

---

# O que é o padrão?

O **Abstract Factory** (Fábrica Abstrata) é um padrão criacional cujo objetivo é fornecer uma interface para criar **famílias de objetos relacionados**, sem que o código cliente conheça ou dependa das implementações concretas.

Em outras palavras, o cliente solicita uma "família" de objetos e a fábrica entrega todos os objetos compatíveis entre si.

Ao invés de criar objetos utilizando diretamente o operador `new`, o cliente delega essa responsabilidade para uma fábrica.

Isso permite trocar completamente uma implementação apenas alterando a fábrica utilizada.

---

# Problema que resolve

Imagine um sistema de e-commerce utilizado em diversos países.

Cada país possui:

- Gateway de pagamento diferente;
- Regra de cálculo de frete diferente;
- Em alguns casos emissão de nota fiscal diferente;
- Em outros casos cálculo de impostos diferente.

Sem o Abstract Factory, o código normalmente fica assim:

```ts
if (pais === 'BR') {
  // Mercado Pago
  // Correios
}

if (pais === 'US') {
  // Stripe
  // UPS
}

if (pais === 'EU') {
  // Adyen
  // DHL
}
```

Com o crescimento do sistema, esse tipo de código acaba espalhado por dezenas de arquivos.

Sempre que um novo país é adicionado, diversos pontos do sistema precisam ser modificados.

Isso viola principalmente o **Open/Closed Principle (OCP)**, pois é necessário alterar código existente para adicionar novos comportamentos.

---

# Motivação (Cenário real)

Imagine que uma empresa possui uma plataforma de e-commerce utilizada em diversos países.

Quando um cliente realiza uma compra, o sistema precisa decidir automaticamente:

- Qual gateway de pagamento utilizar;
- Como calcular o frete.

Essas duas decisões precisam estar sincronizadas.

Por exemplo:

**Brasil**

- Mercado Pago
- Correios

**Europa**

- Stripe
- DHL

O serviço de checkout não deveria conhecer essas implementações.

Ele apenas solicita uma fábrica apropriada para a região e recebe todos os objetos necessários.

---

# Analogia do mundo real

Imagine uma concessionária de veículos.

Você escolhe uma categoria de veículo:

### Linha Econômica

- Motor econômico
- Bancos em tecido
- Rodas simples

### Linha Luxo

- Motor premium
- Bancos em couro
- Rodas esportivas

Você não escolhe cada componente separadamente.

Ao escolher uma linha, todos os componentes pertencem à mesma família.

O Abstract Factory funciona exatamente dessa forma.

---

# Estrutura do padrão

```text
                 Cliente
                    │
                    ▼
          FabricaLoja (interface)
             ▲               ▲
             │               │
      FabricaBrasil    FabricaEuropa
             │               │
     ┌───────┘               └─────────┐
     ▼                                 ▼

GatewayPagamento            GatewayPagamento
CalculadoraFrete            CalculadoraFrete
```

O cliente conhece apenas a interface da fábrica.

Nunca conhece as implementações concretas.

---

# Participantes

## Produto Abstrato

Define o contrato dos objetos criados.

No exemplo:

- `GatewayPagamento`
- `CalculadoraFrete`

---

## Produtos Concretos

São as implementações dos contratos.

No exemplo:

- `GatewayMercadoPago`
- `GatewayStripe`
- `CalculadoraFreteBrasil`
- `CalculadoraFreteEuropa`

---

## Fábrica Abstrata

Define quais objetos podem ser criados.

```ts
interface FabricaLoja {
  criarGatewayPagamento(): GatewayPagamento;

  criarCalculadoraFrete(): CalculadoraFrete;
}
```

---

## Fábricas Concretas

Implementam a criação das famílias de objetos.

Exemplo:

- `FabricaBrasil`
- `FabricaEuropa`

---

## Cliente

O cliente utiliza apenas a fábrica.

Ele nunca instancia diretamente os produtos concretos.

---

# Implementação completa em TypeScript

```ts
/**********************************************************************
 * ABSTRACT FACTORY
 *
 * Exemplo:
 * Sistema de checkout internacional.
 *
 * Cada país possui uma família de objetos:
 *
 * Brasil
 *  - Mercado Pago
 *  - Calculadora de Frete Brasil
 *
 * Europa
 *  - Stripe
 *  - Calculadora de Frete Europa
 *
 * O cliente conhece apenas a fábrica.
 **********************************************************************/

/*=====================================================
=                 PRODUTOS ABSTRATOS                  =
=====================================================*/

/**
 * Responsável por efetuar pagamentos.
 *
 * Todas as implementações devem seguir
 * exatamente este contrato.
 */
interface GatewayPagamento {
  pagar(valor: number): string;
}

/**
 * Responsável por calcular o valor do frete.
 */
interface CalculadoraFrete {
  calcular(pesoKg: number, distanciaKm: number): number;
}

/*=====================================================
=                  FÁBRICA ABSTRATA                   =
=====================================================*/

/**
 * Toda fábrica concreta deve ser capaz
 * de criar uma família completa de objetos.
 */
interface FabricaLoja {
  criarGatewayPagamento(): GatewayPagamento;

  criarCalculadoraFrete(): CalculadoraFrete;
}

/*=====================================================
=                IMPLEMENTAÇÃO BRASIL                 =
=====================================================*/

class GatewayMercadoPago implements GatewayPagamento {
  pagar(valor: number): string {
    return `Mercado Pago recebeu R$ ${valor.toFixed(2)}`;
  }
}

class CalculadoraFreteBrasil implements CalculadoraFrete {
  calcular(pesoKg: number, distanciaKm: number): number {
    return 8 + pesoKg * 2 + distanciaKm * 0.25;
  }
}

class FabricaBrasil implements FabricaLoja {
  criarGatewayPagamento(): GatewayPagamento {
    return new GatewayMercadoPago();
  }

  criarCalculadoraFrete(): CalculadoraFrete {
    return new CalculadoraFreteBrasil();
  }
}

/*=====================================================
=                IMPLEMENTAÇÃO EUROPA                 =
=====================================================*/

class GatewayStripe implements GatewayPagamento {
  pagar(valor: number): string {
    return `Stripe recebeu € ${valor.toFixed(2)}`;
  }
}

class CalculadoraFreteEuropa implements CalculadoraFrete {
  calcular(pesoKg: number, distanciaKm: number): number {
    return 12 + pesoKg * 1.6 + distanciaKm * 0.3;
  }
}

class FabricaEuropa implements FabricaLoja {
  criarGatewayPagamento(): GatewayPagamento {
    return new GatewayStripe();
  }

  criarCalculadoraFrete(): CalculadoraFrete {
    return new CalculadoraFreteEuropa();
  }
}

/*=====================================================
=                     CLIENTE                         =
=====================================================*/

/**
 * O serviço de checkout depende apenas
 * da abstração da fábrica.
 *
 * Ele não conhece Mercado Pago, Stripe,
 * Correios ou qualquer implementação concreta.
 */
class ServicoCheckout {
  constructor(private fabrica: FabricaLoja) {}

  finalizarCompra(valorProduto: number, pesoKg: number, distanciaKm: number): void {
    const gateway = this.fabrica.criarGatewayPagamento();

    const calculadoraFrete = this.fabrica.criarCalculadoraFrete();

    const valorFrete = calculadoraFrete.calcular(pesoKg, distanciaKm);

    const valorTotal = valorProduto + valorFrete;

    console.log(gateway.pagar(valorTotal));
  }
}

/*=====================================================
=                    DEMONSTRAÇÃO                     =
=====================================================*/

const checkoutBrasil = new ServicoCheckout(new FabricaBrasil());

checkoutBrasil.finalizarCompra(120, 8, 100);

const checkoutEuropa = new ServicoCheckout(new FabricaEuropa());

checkoutEuropa.finalizarCompra(120, 8, 100);
```

---

# Fluxo de execução

```text
Cliente

↓

Cria a FabricaBrasil

↓

Instancia o ServicoCheckout

↓

Solicita um GatewayPagamento

↓

Solicita uma CalculadoraFrete

↓

Calcula o frete

↓

Calcula o valor total da compra

↓

Realiza o pagamento
```

Passo a passo:

1. O cliente escolhe qual fábrica utilizar.
2. O `ServicoCheckout` recebe essa fábrica por injeção de dependência.
3. Durante a finalização da compra, o serviço solicita um gateway de pagamento.
4. Em seguida solicita uma calculadora de frete.
5. O frete é calculado conforme as regras da família escolhida.
6. O valor do frete é somado ao valor do produto.
7. O gateway realiza o pagamento utilizando a implementação correta.

---

# Diagrama UML simplificado

```text
                  <<interface>>
                   FabricaLoja
         + criarGatewayPagamento()
         + criarCalculadoraFrete()

                 ▲
        ┌────────┴────────┐
        │                 │
FabricaBrasil      FabricaEuropa
        │                 │
        │ cria            │ cria
        ▼                 ▼

        <<interface>> GatewayPagamento
              ▲                  ▲
              │                  │
   GatewayMercadoPago     GatewayStripe


       <<interface>> CalculadoraFrete
              ▲                  ▲
              │                  │
CalculadoraFreteBrasil  CalculadoraFreteEuropa
```

---

# Vantagens

- Reduz o acoplamento entre o cliente e as implementações concretas.
- Garante compatibilidade entre objetos da mesma família.
- Facilita a adição de novas famílias de objetos.
- Elimina diversos blocos de `if/else`.
- Favorece a aplicação de Injeção de Dependência.
- Facilita a criação de testes unitários utilizando mocks.

---

# Desvantagens

- Aumenta a quantidade de classes do projeto.
- Pode ser um exagero para aplicações muito simples.
- Adicionar novos tipos de produtos pode exigir alterações na fábrica abstrata.

---

# Quando utilizar

Utilize este padrão quando:

- Existem diferentes famílias de objetos.
- Os objetos precisam ser compatíveis entre si.
- Você deseja trocar facilmente toda uma implementação.
- Deseja desacoplar a criação dos objetos da lógica de negócio.

Exemplos:

- Sistemas multi-país.
- Gateways de pagamento.
- Bancos de dados.
- Temas de interface.
- Provedores de armazenamento.
- Drivers de hardware.

---

# Quando evitar

Evite utilizar quando:

- Existe apenas uma implementação.
- O sistema é pequeno e dificilmente terá novas famílias.
- Apenas um objeto precisa ser criado (nesse caso, o Factory Method costuma ser suficiente).

---

# Relação com SOLID

## SRP (Single Responsibility Principle)

Cada fábrica possui apenas uma responsabilidade: criar objetos de uma família específica.

---

## OCP (Open/Closed Principle)

É possível adicionar novas famílias sem modificar o código do cliente.

---

## LSP (Liskov Substitution Principle)

O cliente trabalha apenas com abstrações, permitindo substituir implementações concretas sem alterar seu comportamento.

---

## ISP (Interface Segregation Principle)

As interfaces expõem apenas os métodos necessários para cada responsabilidade.

---

## DIP (Dependency Inversion Principle)

O cliente depende de abstrações (`FabricaLoja`, `GatewayPagamento`, `CalculadoraFrete`) e não das implementações concretas.

---

# Exercício proposto

A empresa agora iniciará operações nos Estados Unidos.

Crie uma nova família de objetos composta por:

- `GatewayPayPal`
- `CalculadoraFreteEstadosUnidos`
- `FabricaEstadosUnidos`

Depois utilize essa fábrica no serviço de checkout:

```ts
const checkout = new ServicoCheckout(new FabricaEstadosUnidos());

checkout.finalizarCompra(250, 12, 400);
```

**Desafio:** implemente essa nova família sem modificar nenhuma linha da classe `ServicoCheckout`.

---

# Resumo

O **Abstract Factory** é um padrão criacional utilizado para criar **famílias de objetos relacionados**, garantindo que todas as implementações sejam compatíveis entre si.

Seu principal objetivo é desacoplar o código cliente das implementações concretas, permitindo substituir uma família inteira de objetos sem alterar a lógica de negócio.

Esse padrão é especialmente útil em sistemas que precisam suportar múltiplos ambientes, países, temas, bancos de dados ou provedores de serviços, promovendo maior flexibilidade, organização e aderência aos princípios do SOLID.
