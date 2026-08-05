# 10) Facade (Fachada)

> **Categoria:** Estrutural

---

# O que é o padrão Facade?

O **Facade (Fachada)** é um padrão de projeto estrutural que fornece **uma interface simples e unificada para um conjunto complexo de classes, interfaces e serviços**.

Seu principal objetivo é **esconder a complexidade de um subsistema**, oferecendo ao cliente apenas um ponto de acesso simples.

Em outras palavras:

> **O Facade atua como um "recepcionista" do sistema. O cliente conversa apenas com ele, enquanto a fachada coordena toda a comunicação com os demais componentes internos.**

O cliente não precisa conhecer:

- quais classes existem;
- em que ordem devem ser chamadas;
- quais dependências possuem;
- quais validações precisam ser executadas.

Tudo isso fica encapsulado dentro da Fachada.

---

# Problema que o Facade resolve

Imagine um sistema responsável por emitir uma Nota Fiscal Eletrônica (NF-e).

Para emitir uma nota são necessárias diversas etapas:

- Validar os dados do cliente;
- Validar os produtos;
- Calcular impostos;
- Gerar o XML;
- Assinar digitalmente o documento;
- Enviar para a SEFAZ;
- Aguardar autorização;
- Gerar o DANFE;
- Salvar no banco de dados;
- Registrar logs;
- Enviar um e-mail ao cliente.

Sem o padrão Facade, o cliente precisaria conhecer todas essas etapas.

Exemplo:

```ts
validador.validar();

calculadoraImpostos.calcular();

geradorXml.gerar();

assinador.assinar();

enviadorSefaz.enviar();

geradorDanfe.gerar();

repositorio.salvar();

email.enviar();
```

Além de conhecer todas as classes, o cliente também precisa saber **a ordem correta** das chamadas.

Isso gera:

- Alto acoplamento;
- Código repetitivo;
- Maior chance de erros;
- Difícil manutenção.

O Facade resolve exatamente esse problema.

---

# Motivação (cenário real)

Imagine que você vai viajar de avião.

Você compra uma passagem.

Ao chegar ao aeroporto, você conversa apenas com um atendente.

Esse atendente coordena diversos sistemas internos:

- Consulta sua reserva;
- Confere seu documento;
- Imprime o cartão de embarque;
- Despacha sua bagagem;
- Atualiza o sistema da companhia aérea;
- Libera seu embarque.

Você não conversa individualmente com cada setor.

Existe uma "fachada" que centraliza todo esse processo.

É exatamente isso que o padrão Facade faz.

---

# Analogia do mundo real

Imagine um restaurante.

Quando você faz um pedido, conversa apenas com o garçom.

O garçom conversa com:

- cozinha;
- caixa;
- bar;
- confeitaria;
- estoque.

Você não precisa conhecer nenhum desses setores.

O garçom funciona como uma **fachada**.

---

# Estrutura do padrão

```
                 Cliente
                    │
                    ▼
              +------------+
              |  Facade    |
              +------------+
              │ coordena   │
     ┌────────┼────────┬────────┐
     ▼        ▼        ▼        ▼
 Validador  Gerador  Enviador  Banco
```

---

# Participantes

## 1. Cliente

É quem deseja utilizar o sistema.

Ele conhece apenas a Fachada.

---

## 2. Facade (Fachada)

É a classe principal do padrão.

Sua responsabilidade é:

- simplificar o acesso;
- coordenar chamadas;
- esconder a complexidade do subsistema.

No nosso exemplo:

```ts
EmissorNotaFiscal;
```

---

## 3. Subsistema

É formado pelas classes responsáveis pelo trabalho real.

Exemplo:

- Validador
- Calculador de impostos
- Gerador XML
- Assinador Digital
- Enviador SEFAZ
- Repositório
- Serviço de E-mail

Cada classe continua independente.

A Fachada apenas coordena a comunicação entre elas.

---

# Diagrama UML simplificado

```text
                     +----------------------+
                     | EmissorNotaFiscal    |
                     +----------------------+
                     | + emitirNota()       |
                     +----------+-----------+
                                |
        -------------------------------------------------
        |             |             |                  |
        ▼             ▼             ▼                  ▼
+---------------+ +---------------+ +---------------+ +----------------+
| Validador     | | GeradorXML    | | EnviadorSefaz | | Repositorio    |
+---------------+ +---------------+ +---------------+ +----------------+
| validar()     | | gerar()       | | enviar()      | | salvar()       |
+---------------+ +---------------+ +---------------+ +----------------+
```

---

# Implementação completa em TypeScript

```ts
/**
 * ============================================================
 * FACADE (FACHADA)
 * ============================================================
 *
 * Neste exemplo construiremos um sistema simplificado
 * de emissão de Nota Fiscal.
 *
 * Observe que o cliente utilizará apenas uma classe:
 *
 * EmissorNotaFiscal
 *
 * Toda a complexidade ficará escondida.
 */

/**
 * Representa os dados necessários
 * para emitir uma nota fiscal.
 */
type DadosNotaFiscal = {
  cliente: {
    nome: string;
  };

  itens: {
    nome: string;

    quantidade: number;

    precoUnitario: number;
  }[];
};

/**
 * ============================================================
 * SUBSISTEMA
 * ============================================================
 */

/**
 * Responsável por validar os dados.
 */
class ValidadorNotaFiscal {
  validar(dados: DadosNotaFiscal): void {
    if (!dados.cliente.nome) {
      throw new Error('Cliente não informado.');
    }

    if (dados.itens.length === 0) {
      throw new Error('A nota precisa possuir pelo menos um item.');
    }
  }
}

/**
 * Calcula o valor total da nota.
 */
class CalculadoraTotal {
  calcular(dados: DadosNotaFiscal): number {
    return dados.itens.reduce(
      (total, item) => total + item.precoUnitario * item.quantidade,

      0,
    );
  }
}

/**
 * Gera um número para a nota fiscal.
 */
class GeradorNotaFiscal {
  gerarNumero(): string {
    return `NF-${Date.now()}`;
  }
}

/**
 * Simula o envio da nota.
 */
class EnviadorNotaFiscal {
  enviar(numero: string): void {
    console.log(`Nota ${numero} enviada para a SEFAZ.`);
  }
}

/**
 * Simula salvar no banco.
 */
class RepositorioNotas {
  salvar(numero: string): void {
    console.log(`Nota ${numero} salva no banco.`);
  }
}

/**
 * ============================================================
 * FACADE
 * ============================================================
 *
 * Esta classe esconde toda a complexidade.
 */
class EmissorNotaFiscal {
  constructor(
    private validador = new ValidadorNotaFiscal(),

    private calculadora = new CalculadoraTotal(),

    private gerador = new GeradorNotaFiscal(),

    private enviador = new EnviadorNotaFiscal(),

    private repositorio = new RepositorioNotas(),
  ) {}

  emitirNota(dados: DadosNotaFiscal): string {
    // 1. Valida os dados
    this.validador.validar(dados);

    // 2. Calcula o valor total
    const total = this.calculadora.calcular(dados);

    console.log(`Valor total: R$ ${total}`);

    // 3. Gera o número da nota
    const numeroNota = this.gerador.gerarNumero();

    // 4. Envia para a SEFAZ
    this.enviador.enviar(numeroNota);

    // 5. Salva no banco
    this.repositorio.salvar(numeroNota);

    return numeroNota;
  }
}

/**
 * ============================================================
 * CLIENTE
 * ============================================================
 */

const emissor = new EmissorNotaFiscal();

const numero = emissor.emitirNota({
  cliente: {
    nome: 'Maria',
  },

  itens: [
    {
      nome: 'Notebook',

      quantidade: 1,

      precoUnitario: 4500,
    },

    {
      nome: 'Mouse',

      quantidade: 2,

      precoUnitario: 150,
    },
  ],
});

console.log(`Nota emitida: ${numero}`);
```

---

# Fluxo de execução (passo a passo)

## Passo 1

O cliente cria apenas a Fachada.

```ts
const emissor = new EmissorNotaFiscal();
```

Ele não precisa instanciar:

- Validador;
- Calculadora;
- Gerador;
- Enviador;
- Repositório.

---

## Passo 2

O cliente chama apenas:

```ts
emissor.emitirNota(dados);
```

---

## Passo 3

A Fachada valida os dados recebidos.

```
Cliente

↓

Facade

↓

Validador
```

---

## Passo 4

Os valores da nota são calculados.

```
Facade

↓

Calculadora
```

---

## Passo 5

É gerado o número da Nota Fiscal.

```
Facade

↓

Gerador
```

---

## Passo 6

A nota é enviada para a SEFAZ.

```
Facade

↓

Enviador
```

---

## Passo 7

A nota é armazenada no banco de dados.

```
Facade

↓

Repositório
```

---

## Passo 8

A Fachada devolve apenas o número da nota.

```
Cliente

↓

NF-17546894321
```

Observe que o cliente nunca interagiu diretamente com nenhuma das classes internas.

---

# Fluxo interno das chamadas

```text
Cliente

↓

EmissorNotaFiscal (Facade)

↓

ValidadorNotaFiscal

↓

CalculadoraTotal

↓

GeradorNotaFiscal

↓

EnviadorNotaFiscal

↓

RepositorioNotas

↓

Resultado
```

Toda essa sequência acontece internamente, sendo invisível para o cliente.

---

# Como reconhecer que é Facade?

Alguns sinais indicam o uso desse padrão:

- Existe um único ponto de entrada para um subsistema complexo.
- O cliente utiliza apenas uma classe principal.
- A fachada coordena diversas operações internas.
- O cliente não conhece a ordem das chamadas.
- O subsistema continua existindo, mas fica oculto.

---

# Vantagens

## Simplifica o uso

O cliente chama apenas um método.

---

## Reduz acoplamento

O cliente deixa de depender diretamente das classes internas.

---

## Facilita manutenção

Mudanças no subsistema raramente afetam o cliente.

---

## Organiza responsabilidades

Cada classe continua especializada em uma única tarefa.

---

## Melhora a legibilidade

O código do cliente torna-se muito mais simples e expressivo.

---

# Desvantagens

## Pode crescer demais

Se todas as responsabilidades forem adicionadas à Fachada, ela pode se transformar em uma classe muito grande, conhecida como **God Object**.

---

## Pode esconder funcionalidades avançadas

Alguns clientes podem precisar acessar diretamente o subsistema para utilizar recursos específicos que a Fachada não expõe.

---

## Pode exigir atualização

Sempre que novas funcionalidades precisarem ser disponibilizadas de forma simplificada, a Fachada poderá precisar ser expandida.

---

# Quando utilizar

Utilize Facade quando:

- existir um subsistema complexo;
- várias classes precisarem ser utilizadas em conjunto;
- o cliente não deve conhecer detalhes internos;
- desejar fornecer uma API simples;
- quiser reduzir o acoplamento entre cliente e subsistema.

Exemplos comuns:

- Emissão de Nota Fiscal;
- Pagamentos;
- Sistemas bancários;
- Frameworks;
- Bibliotecas;
- APIs de terceiros;
- Sistemas de autenticação;
- Processamento de imagens;
- Envio de e-mails.

---

# Quando evitar

Evite utilizar Facade quando:

- o sistema possuir apenas uma ou duas classes simples;
- o cliente realmente precisar controlar cada etapa individualmente;
- a abstração adicionaria mais complexidade do que benefícios.

---

# Relação com os princípios SOLID

## S — Single Responsibility Principle (SRP)

Cada serviço do subsistema possui uma responsabilidade específica.

A Fachada possui apenas a responsabilidade de coordenar essas operações.

---

## O — Open/Closed Principle (OCP)

Novos serviços podem ser adicionados ao subsistema sem alterar significativamente o código cliente.

---

## L — Liskov Substitution Principle (LSP)

O padrão não depende diretamente desse princípio, mas qualquer implementação equivalente da Fachada pode substituir outra sem afetar os clientes.

---

## I — Interface Segregation Principle (ISP)

O cliente utiliza uma interface simples, sem depender de métodos que não precisa.

---

## D — Dependency Inversion Principle (DIP)

A Fachada pode depender de abstrações (interfaces) em vez de implementações concretas, facilitando testes e substituições.

---

# Anti-exemplo

Um erro comum é fazer o cliente controlar manualmente todas as etapas do processo.

```ts
const validador = new ValidadorNotaFiscal();

const calculadora = new CalculadoraTotal();

const gerador = new GeradorNotaFiscal();

const enviador = new EnviadorNotaFiscal();

const repositorio = new RepositorioNotas();

validador.validar(dados);

const total = calculadora.calcular(dados);

const numero = gerador.gerarNumero();

enviador.enviar(numero);

repositorio.salvar(numero);
```

Além de aumentar o acoplamento, qualquer alteração na ordem das operações exigirá modificações em todos os clientes.

---

# Exercício proposto

Implemente um sistema de reserva de viagens utilizando o padrão Facade.

### Requisitos

Crie os seguintes serviços:

- `ServicoHotel`
- `ServicoPassagemAerea`
- `ServicoAluguelCarro`
- `ServicoPagamento`
- `ServicoEmail`

Depois implemente a fachada:

```ts
AgenciaViagensFacade;
```

Ela deverá possuir um método:

```ts
reservarPacote();
```

Esse método deverá:

1. Reservar o hotel;
2. Reservar a passagem aérea;
3. Reservar o carro;
4. Processar o pagamento;
5. Enviar um e-mail de confirmação.

O cliente deverá realizar toda a operação utilizando apenas:

```ts
agencia.reservarPacote(...)
```

### Desafio extra

Adicione um novo serviço:

```text
ServicoSeguroViagem
```

Modifique apenas a Fachada para incluir automaticamente a contratação do seguro durante a reserva.

O código do cliente **não deverá sofrer nenhuma alteração**, demonstrando um dos principais benefícios do padrão Facade: **esconder a complexidade do subsistema e oferecer uma interface simples, organizada e de fácil utilização.**
