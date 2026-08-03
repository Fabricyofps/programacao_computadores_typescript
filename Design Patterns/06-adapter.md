# 06) Adapter (Adaptador)

## Categoria

**Padrão Estrutural (Structural Pattern)**

---

# O que é o Adapter?

O **Adapter (Adaptador)** é um padrão de projeto **estrutural** cujo objetivo é **permitir que duas classes com interfaces incompatíveis consigam trabalhar juntas**, sem que seja necessário modificar nenhuma delas.

Ele atua como um **tradutor** entre dois componentes.

Imagine que uma classe espera receber um objeto com determinados métodos, mas a implementação existente possui uma interface completamente diferente.

Ao invés de alterar o código antigo (legado) ou modificar o sistema atual, criamos uma terceira classe chamada **Adapter**, responsável por converter chamadas, parâmetros e respostas entre essas duas interfaces.

Em resumo:

> O Adapter permite reutilizar classes existentes mesmo quando suas interfaces são incompatíveis.

Esse padrão é muito comum quando precisamos integrar:

- bibliotecas de terceiros;
- APIs antigas (legadas);
- sistemas externos;
- SDKs;
- serviços desenvolvidos por outras equipes.

---

# Problema que o Adapter resolve

Imagine que sua empresa modernizou todo o sistema de pagamentos.

O novo sistema trabalha com a seguinte interface:

```ts
interface GatewayPagamento {
  pagar(valor: number): string;
}
```

Porém, o gateway antigo utilizado pela empresa possui outra interface.

```ts
processarPagamento(valor: string): boolean;
```

Observe que existem duas incompatibilidades.

O novo sistema utiliza:

- `number`

Enquanto o sistema legado utiliza:

- `string`

Além disso:

O novo sistema espera um retorno:

```text
string
```

Enquanto o legado retorna:

```text
boolean
```

Sem um Adapter, cada módulo da aplicação precisaria fazer conversões manualmente.

```ts
const sucesso = legado.processarPagamento(valor.toFixed(2));
```

Esse código acabaria espalhado por dezenas de arquivos.

Sempre que o sistema legado mudasse, toda a aplicação precisaria ser alterada.

O Adapter resolve esse problema centralizando toda essa tradução em apenas uma classe.

---

# Motivação (Cenário Real)

Imagine que uma empresa desenvolveu um sistema de pagamentos há quinze anos.

Hoje ela decide criar um aplicativo moderno.

O problema é que trocar imediatamente o gateway de pagamento seria muito caro.

A solução escolhida foi continuar utilizando o sistema antigo, mas criar uma camada intermediária que traduzisse as chamadas da nova aplicação.

Essa camada é exatamente o Adapter.

Dessa forma:

- o sistema moderno permanece limpo;
- o sistema legado continua funcionando;
- quando chegar o momento de substituir o gateway, apenas o Adapter precisará ser alterado.

---

# Analogia do Mundo Real

Imagine que você comprou um notebook americano.

O carregador possui um plugue de dois pinos achatados.

Entretanto, a tomada da sua casa utiliza o padrão brasileiro.

O notebook funciona perfeitamente.

A tomada também.

O problema está apenas na incompatibilidade entre os conectores.

A solução é utilizar um adaptador de tomada.

O adaptador não altera nem o notebook nem a tomada.

Ele apenas traduz a conexão entre ambos.

O padrão Adapter funciona exatamente dessa maneira.

---

# Estrutura do Padrão

```text
                   Cliente
                      │
                      ▼
             GatewayPagamento
                      ▲
                      │
          GatewayPagamentoAdapter
                      │
                      ▼
          GatewayPagamentoLegado
```

O cliente conversa apenas com a interface moderna.

O Adapter faz toda a tradução para o sistema legado.

---

# Participantes

## 1. Cliente (Client)

É quem utiliza o sistema.

O cliente conhece apenas a interface esperada.

Ele nunca conversa diretamente com o sistema legado.

---

## 2. Interface Alvo (Target)

Representa a interface esperada pelo cliente.

No nosso exemplo:

```ts
interface GatewayPagamento
```

Todos os componentes modernos utilizam esse contrato.

---

## 3. Adaptado (Adaptee)

É a classe existente que desejamos reutilizar.

Ela já funciona corretamente.

O problema é que sua interface é diferente da esperada.

No exemplo:

```text
GatewayPagamentoLegado
```

---

## 4. Adaptador (Adapter)

É a classe responsável por converter:

- métodos;
- parâmetros;
- tipos;
- retornos.

Ela conecta as duas interfaces.

---

# Implementação Completa em TypeScript (Totalmente Comentada)

```ts
/**
 * ============================================================
 * TARGET
 * ============================================================
 *
 * Interface esperada pelo sistema moderno.
 */

interface GatewayPagamento {
  /**
   * Realiza um pagamento.
   */

  pagar(valor: number): string;
}

/**
 * ============================================================
 * ADAPTEE (LEGADO)
 * ============================================================
 *
 * Esta classe já existe.
 *
 * Não podemos alterá-la.
 */

class GatewayPagamentoLegado {
  /**
   * O sistema legado trabalha com string.
   */

  public processarPagamento(valor: string): boolean {
    console.log('Sistema legado processando pagamento...');

    return Number(valor) > 0;
  }
}

/**
 * ============================================================
 * ADAPTER
 * ============================================================
 *
 * Traduz a interface moderna para a interface antiga.
 */

class GatewayPagamentoAdapter implements GatewayPagamento {
  constructor(private gatewayLegado: GatewayPagamentoLegado) {}

  public pagar(valor: number): string {
    /**
     * Convertendo number para string.
     */

    const valorConvertido = valor.toFixed(2);

    /**
     * Chamando o sistema legado.
     */

    const pagamentoAprovado = this.gatewayLegado.processarPagamento(valorConvertido);

    /**
     * Convertendo boolean para string.
     */

    if (pagamentoAprovado) {
      return 'Pagamento aprovado.';
    }

    return 'Pagamento recusado.';
  }
}

/**
 * ============================================================
 * CLIENTE
 * ============================================================
 */

const gatewayLegado = new GatewayPagamentoLegado();

/**
 * O cliente trabalha apenas
 * com a interface moderna.
 */

const gateway: GatewayPagamento = new GatewayPagamentoAdapter(gatewayLegado);

console.log(gateway.pagar(250));
```

---

# Fluxo de Execução (Passo a Passo)

### Passo 1

O cliente solicita um pagamento.

```ts
gateway.pagar(250);
```

↓

### Passo 2

A chamada chega ao Adapter.

↓

### Passo 3

O Adapter converte:

```ts
250;
```

para

```ts
'250.00';
```

↓

### Passo 4

O Adapter chama o sistema legado.

```ts
processarPagamento('250.00');
```

↓

### Passo 5

O sistema legado retorna.

```ts
true;
```

↓

### Passo 6

O Adapter converte:

```ts
true;
```

para

```text
"Pagamento aprovado."
```

↓

### Passo 7

O cliente recebe o resultado sem saber que existia um sistema legado.

---

# Diagrama UML Simplificado

```text
                 <<interface>>
              GatewayPagamento
              ---------------------
              + pagar()

                    ▲
                    │
            GatewayPagamentoAdapter
            ----------------------------
            - gatewayLegado
            + pagar()

                    │
                    ▼

            GatewayPagamentoLegado
            ----------------------------
            + processarPagamento()
```

---

# Como reconhecer um Adapter?

Alguns sinais indicam claramente esse padrão.

- existe uma classe intermediária;
- duas interfaces são incompatíveis;
- ocorre conversão de parâmetros;
- ocorre conversão de retorno;
- o cliente desconhece completamente o sistema adaptado.

Sempre que você encontrar uma classe "tradutora", provavelmente está diante de um Adapter.

---

# Vantagens

## Reutilização de código

Permite continuar utilizando sistemas antigos.

---

## Baixo acoplamento

O cliente não depende da implementação legado.

---

## Facilidade de substituição

Quando o sistema antigo for removido, basta alterar o Adapter.

---

## Centralização da conversão

Toda a lógica de tradução fica concentrada em um único lugar.

---

## Facilita migrações

Ideal para modernização gradual de sistemas.

---

# Desvantagens

## Mais uma camada

Existe uma classe adicional.

---

## Conversões complexas

Algumas integrações exigem muitas adaptações.

---

## Pequena perda de desempenho

Existe um processamento extra durante a tradução.

Na maioria das aplicações isso é irrelevante.

---

# Quando utilizar

Utilize Adapter quando:

- integrar sistemas legados;
- utilizar bibliotecas de terceiros;
- consumir APIs antigas;
- interfaces forem incompatíveis;
- modificar o código existente não for possível.

---

# Quando evitar

Evite Adapter quando:

- você controla ambos os sistemas e pode padronizar as interfaces;
- a adaptação é extremamente simples e utilizada em apenas um local;
- o sistema legado será removido imediatamente.

Nesses casos, um Adapter pode adicionar complexidade desnecessária.

---

# Relação com os Princípios SOLID

## S — Single Responsibility Principle (SRP)

O Adapter possui apenas uma responsabilidade:

Traduzir uma interface para outra.

Ele não implementa regras de negócio.

---

## O — Open/Closed Principle (OCP)

Podemos adicionar novos adaptadores sem alterar o código do cliente.

Por exemplo:

- GatewayStripeAdapter
- GatewayPayPalAdapter
- GatewayMercadoPagoAdapter

O cliente continua utilizando apenas a interface `GatewayPagamento`.

---

## L — Liskov Substitution Principle (LSP)

Qualquer Adapter pode substituir outro desde que implemente a mesma interface.

```ts
GatewayPagamento;
```

O cliente continuará funcionando normalmente.

---

## I — Interface Segregation Principle (ISP)

O cliente depende apenas da interface que realmente utiliza.

```ts
interface GatewayPagamento
```

Ele não precisa conhecer métodos específicos do sistema legado.

---

## D — Dependency Inversion Principle (DIP)

O cliente depende da abstração.

Nunca da implementação concreta.

```ts
GatewayPagamento;
```

Isso reduz significativamente o acoplamento.

---

# Anti-exemplo (O que NÃO fazer)

Imagine espalhar a conversão manual por toda a aplicação.

```ts
function pagarComLegado(
  gateway: GatewayPagamentoLegado,

  valor: number,
) {
  return gateway.processarPagamento(valor.toFixed(2));
}
```

Depois dezenas de arquivos fariam exatamente a mesma conversão.

Problemas:

- código duplicado;
- alto acoplamento;
- manutenção difícil;
- alterações espalhadas pelo sistema;
- risco de conversões inconsistentes.

Com o Adapter, toda essa lógica fica centralizada.

---

# Adapter x Facade

É muito comum iniciantes confundirem Adapter com Facade.

Apesar de ambos serem padrões estruturais, seus objetivos são completamente diferentes.

| Adapter                               | Facade                                        |
| ------------------------------------- | --------------------------------------------- |
| Traduz interfaces incompatíveis.      | Simplifica o acesso a um subsistema complexo. |
| Resolve problemas de compatibilidade. | Resolve problemas de complexidade.            |
| Normalmente adapta uma única classe.  | Normalmente organiza várias classes.          |

Resumo:

- **Adapter traduz.**
- **Facade simplifica.**

---

# Casos Reais de Uso

O Adapter aparece frequentemente em:

- gateways de pagamento;
- APIs REST antigas;
- bibliotecas JavaScript;
- integração entre microsserviços;
- drivers de banco de dados;
- integração com sistemas ERP;
- SDKs de terceiros;
- serviços em nuvem.

---

# Exercício Proposto

Você foi contratado para integrar um sistema moderno de envio de mensagens com um serviço legado.

O sistema moderno trabalha com a seguinte interface:

```ts
interface ServicoMensagem {
  enviarMensagem(
    destinatario: string,

    texto: string,
  ): boolean;
}
```

Entretanto, o sistema legado possui o método:

```ts
enviar(

    telefone: string,

    mensagem: string

): string;
```

## Requisitos

Crie:

- uma classe `ServicoMensagemLegado`;
- um `ServicoMensagemAdapter`;
- a interface `ServicoMensagem`.

O Adapter deverá:

- converter parâmetros, se necessário;
- converter o retorno do sistema legado;
- esconder completamente a implementação antiga do cliente.

Ao final, o código abaixo deverá funcionar.

```ts
const servico: ServicoMensagem = new ServicoMensagemAdapter(new ServicoMensagemLegado());

servico.enviarMensagem(
  '34999999999',

  'Olá!',
);
```

### Desafio Extra

Implemente um segundo Adapter para outro provedor de mensagens.

Por exemplo:

```text
ServicoWhatsAppAdapter
```

ou

```text
ServicoTelegramAdapter
```

Sem alterar absolutamente nenhuma linha do cliente.

Depois responda:

- O cliente precisou conhecer o sistema legado?
- Em quantos locais foi necessário converter os dados?
- O que aconteceria se o sistema legado mudasse novamente?
- Qual classe seria alterada?

Se apenas o **Adapter** precisou ser modificado, então você compreendeu corretamente o objetivo desse padrão: **permitir que componentes incompatíveis trabalhem juntos sem alterar suas implementações originais**, promovendo reutilização, baixo acoplamento e facilidade de manutenção.

---

# Comparação com os Padrões Anteriores

Até agora vimos padrões criacionais e, agora, iniciamos os padrões estruturais.

Cada um resolve um problema diferente:

| Padrão             | Principal objetivo                                                 |
| ------------------ | ------------------------------------------------------------------ |
| **Factory Method** | Decide como um objeto será criado.                                 |
| **Prototype**      | Cria novos objetos por meio de cópias.                             |
| **Singleton**      | Garante que exista apenas uma instância.                           |
| **Adapter**        | Permite que classes com interfaces incompatíveis trabalhem juntas. |

Perceba que o **Adapter não cria objetos nem controla seu ciclo de vida**. Seu foco está na **estrutura** da aplicação, conectando componentes que, originalmente, não conseguiriam se comunicar.
