# 07) Bridge (Ponte)

## Categoria

**Padrão Estrutural (Structural Pattern)**

---

# O que é o Bridge?

O **Bridge (Ponte)** é um padrão de projeto **estrutural** cujo objetivo é **separar uma abstração da sua implementação**, permitindo que ambas possam evoluir de forma independente.

Em outras palavras, quando um sistema possui **duas (ou mais) dimensões que podem variar de forma independente**, o Bridge evita que seja necessário criar uma classe para cada combinação possível.

Em vez de utilizar uma enorme hierarquia de herança, o Bridge utiliza **composição**, fazendo com que uma classe possua uma referência para outra.

A ideia principal pode ser resumida em uma frase:

> **"Prefira composição em vez de multiplicar heranças."**

Esse padrão é extremamente útil quando percebemos que o número de classes cresce rapidamente devido às combinações possíveis entre diferentes características.

---

# Problema que o Bridge resolve

Imagine um sistema de geração de relatórios.

Existem dois tipos de relatório:

- Resumo
- Detalhado

Além disso, existem dois estilos de apresentação:

- Claro
- Escuro

Uma solução utilizando apenas herança poderia gerar as seguintes classes:

```text
RelatorioResumoClaro

RelatorioResumoEscuro

RelatorioDetalhadoClaro

RelatorioDetalhadoEscuro
```

Até aqui são apenas quatro classes.

Agora imagine que o cliente solicite um novo estilo:

- Azul

Teríamos:

```text
RelatorioResumoAzul

RelatorioDetalhadoAzul
```

Depois surgem novos tipos:

- Financeiro
- Comercial
- Estoque

Agora a quantidade de classes cresce rapidamente.

Essa situação é conhecida como **explosão de classes** (_Class Explosion_).

Quanto mais dimensões variam, maior será o número de combinações.

O Bridge resolve esse problema separando essas dimensões.

No nosso exemplo:

**Primeira dimensão**

- Tipo do relatório

**Segunda dimensão**

- Estilo do relatório

Cada dimensão evolui independentemente da outra.

---

# Motivação (Cenário Real)

Imagine um aplicativo de streaming.

Ele possui vários tipos de dispositivos:

- TV
- Smartphone
- Tablet
- Computador

Além disso, existem diversos sistemas operacionais:

- Android
- iOS
- Windows
- Linux

Se utilizarmos apenas herança, rapidamente teremos dezenas de classes.

Por exemplo:

```text
TVAndroid

TVLinux

TVWindows

TabletAndroid

TabletIOS

ComputadorWindows

...
```

Em vez disso, podemos separar:

**Dispositivo**

e

**Sistema Operacional**

Cada um evolui separadamente.

Esse é exatamente o objetivo do Bridge.

---

# Analogia do Mundo Real

Imagine uma caneta.

Ela possui duas partes principais:

- o corpo;
- o refil.

Você pode trocar apenas o refil.

Ou apenas o corpo.

Não é necessário comprar uma caneta completamente nova para cada combinação.

Você pode ter:

- corpo azul + refil preto;
- corpo vermelho + refil azul;
- corpo metálico + refil verde.

Essas duas partes evoluem de forma independente.

O Bridge segue exatamente essa ideia.

---

# Estrutura do Padrão

```text
                Abstração
            (Relatório)
                 │
                 │ possui
                 ▼
          Implementação
            (Estilo)

      RelatórioResumo ─────► EstiloClaro

      RelatórioResumo ─────► EstiloEscuro

      RelatórioDetalhado ─► EstiloClaro

      RelatórioDetalhado ─► EstiloEscuro
```

Observe que não existe uma classe para cada combinação.

Existe apenas uma ligação entre elas.

---

# Participantes

## 1. Abstração (Abstraction)

Representa o conceito principal do sistema.

No nosso exemplo:

```text
Relatorio
```

Ela define o comportamento geral.

---

## 2. Abstração Refinada (Refined Abstraction)

São especializações da abstração.

Exemplo:

- RelatorioResumo
- RelatorioDetalhado

---

## 3. Implementação (Implementor)

Define a interface responsável por uma segunda dimensão.

Exemplo:

```ts
interface Estilo
```

---

## 4. Implementações Concretas (Concrete Implementors)

São as implementações reais.

Exemplo:

- EstiloClaro
- EstiloEscuro

---

## 5. Cliente

O cliente escolhe qual abstração utilizar e qual implementação combinar.

---

# Implementação Completa em TypeScript (Totalmente Comentada)

```ts
/**
 * ============================================================
 * IMPLEMENTAÇÃO (IMPLEMENTOR)
 * ============================================================
 *
 * Define o contrato para todos os estilos.
 */

interface Estilo {
  formatarTitulo(titulo: string): string;

  formatarValor(valor: number): string;
}

/**
 * ============================================================
 * IMPLEMENTAÇÃO CONCRETA
 * ============================================================
 */

class EstiloClaro implements Estilo {
  public formatarTitulo(titulo: string): string {
    return `===== ${titulo.toUpperCase()} =====`;
  }

  public formatarValor(valor: number): string {
    return `R$ ${valor.toFixed(2)}`;
  }
}

/**
 * ============================================================
 * IMPLEMENTAÇÃO CONCRETA
 * ============================================================
 */

class EstiloEscuro implements Estilo {
  public formatarTitulo(titulo: string): string {
    return `*** ${titulo} ***`;
  }

  public formatarValor(valor: number): string {
    return `[VALOR]: ${valor.toFixed(2)}`;
  }
}

/**
 * ============================================================
 * ABSTRAÇÃO
 * ============================================================
 *
 * Observe que Relatorio NÃO conhece
 * nenhuma implementação concreta.
 *
 * Ele trabalha apenas com a interface Estilo.
 */

abstract class Relatorio {
  constructor(protected estilo: Estilo) {}

  /**
   * Método comum para geração
   * do relatório.
   */

  public gerar(
    titulo: string,

    valor: number,
  ): string {
    const tituloFormatado = this.estilo.formatarTitulo(titulo);

    const valorFormatado = this.estilo.formatarValor(valor);

    return `${tituloFormatado}

${valorFormatado}`;
  }
}

/**
 * ============================================================
 * ABSTRAÇÃO REFINADA
 * ============================================================
 */

class RelatorioResumo extends Relatorio {}

/**
 * ============================================================
 * ABSTRAÇÃO REFINADA
 * ============================================================
 */

class RelatorioDetalhado extends Relatorio {
  public gerar(
    titulo: string,

    valor: number,
  ): string {
    return (
      super.gerar(
        titulo,

        valor,
      ) +
      `\n\nInformações adicionais:
- Dados completos
- Estatísticas
- Histórico`
    );
  }
}

/**
 * ============================================================
 * CLIENTE
 * ============================================================
 */

const resumoClaro = new RelatorioResumo(new EstiloClaro());

console.log(
  resumoClaro.gerar(
    'Vendas',

    1234.5,
  ),
);

const detalhadoEscuro = new RelatorioDetalhado(new EstiloEscuro());

console.log(
  detalhadoEscuro.gerar(
    'Vendas',

    1234.5,
  ),
);
```

---

# Fluxo de Execução (Passo a Passo)

### Passo 1

O cliente escolhe um tipo de relatório.

```ts
new RelatorioResumo(...)
```

↓

### Passo 2

Escolhe também um estilo.

```ts
new EstiloClaro();
```

↓

### Passo 3

O relatório recebe o estilo no construtor.

↓

### Passo 4

Quando o relatório precisa formatar um título:

```ts
this.estilo.formatarTitulo(...)
```

↓

### Passo 5

Quem decide como o título será exibido é o estilo.

↓

### Passo 6

O relatório continua responsável apenas pela lógica de geração.

↓

### Passo 7

O cliente pode trocar o estilo sem alterar o tipo do relatório.

---

# Diagrama UML Simplificado

```text
                  <<interface>>
                     Estilo
              --------------------
              + formatarTitulo()
              + formatarValor()
                     ▲
        ┌────────────┴─────────────┐
        │                          │
        │                          │
    EstiloClaro               EstiloEscuro


                Relatorio
          -----------------------
          - estilo : Estilo
          + gerar()
                 ▲
      ┌──────────┴───────────┐
      │                      │
      │                      │
    RelatorioResumo    RelatorioDetalhado
```

---

# Como reconhecer um Bridge?

Existem alguns sinais bastante característicos.

- existem duas dimensões independentes;
- a quantidade de subclasses começa a crescer rapidamente;
- uma classe possui outra por composição;
- ambas podem evoluir separadamente.

Sempre que perceber uma explosão de subclasses causada por combinações, considere utilizar o Bridge.

---

# Vantagens

## Evita explosão de classes

Não precisamos criar uma classe para cada combinação possível.

---

## Favorece composição

O Bridge utiliza composição em vez de herança excessiva.

---

## Facilita manutenção

Cada dimensão possui sua própria responsabilidade.

---

## Permite evolução independente

Podemos criar novos relatórios sem alterar os estilos.

Ou criar novos estilos sem alterar os relatórios.

---

## Maior reutilização

Os estilos podem ser reutilizados por diversos tipos de relatório.

---

# Desvantagens

## Mais abstrações

O padrão adiciona novas interfaces e classes.

---

## Curva de aprendizado

Para iniciantes, a separação entre abstração e implementação pode parecer confusa.

---

## Pode ser exagerado

Se houver apenas uma dimensão de variação, talvez o Bridge seja desnecessário.

---

# Quando utilizar

Utilize Bridge quando:

- existirem duas dimensões independentes;
- houver explosão de subclasses;
- desejar trocar implementações em tempo de execução;
- quiser reduzir dependência entre abstração e implementação.

---

# Quando evitar

Evite utilizar quando:

- existe apenas uma implementação;
- não há combinações entre dimensões;
- a herança simples resolve o problema;
- o projeto é pequeno e dificilmente crescerá.

---

# Relação com os Princípios SOLID

## S — Single Responsibility Principle (SRP)

Cada classe possui apenas uma responsabilidade.

O relatório gera informações.

O estilo apenas formata essas informações.

---

## O — Open/Closed Principle (OCP)

Podemos adicionar:

```text
EstiloAzul
```

ou

```text
RelatorioFinanceiro
```

sem alterar as classes existentes.

---

## L — Liskov Substitution Principle (LSP)

Qualquer implementação de `Estilo` pode substituir outra.

Da mesma forma, qualquer tipo de relatório pode substituir outro.

---

## I — Interface Segregation Principle (ISP)

A interface `Estilo` possui apenas os métodos necessários para formatação.

Nenhum método desnecessário é imposto às implementações.

---

## D — Dependency Inversion Principle (DIP)

A classe `Relatorio` depende da abstração:

```ts
interface Estilo
```

Ela não conhece `EstiloClaro` nem `EstiloEscuro`.

Isso reduz o acoplamento.

---

# Anti-exemplo (O que NÃO fazer)

Criar uma classe para cada combinação possível.

```text
RelatorioResumoClaro

RelatorioResumoEscuro

RelatorioResumoAzul

RelatorioDetalhadoClaro

RelatorioDetalhadoEscuro

RelatorioDetalhadoAzul

RelatorioFinanceiroClaro

RelatorioFinanceiroEscuro

RelatorioFinanceiroAzul

...
```

Imagine adicionar:

- 8 tipos de relatório;
- 6 estilos diferentes.

Teríamos **48 classes** apenas para representar combinações.

Esse é exatamente o problema que o Bridge evita.

---

# Bridge x Adapter

Esses dois padrões estruturais costumam ser confundidos.

Mas possuem objetivos completamente diferentes.

| Bridge                                      | Adapter                                              |
| ------------------------------------------- | ---------------------------------------------------- |
| Separa duas dimensões independentes.        | Traduz interfaces incompatíveis.                     |
| É planejado durante o projeto.              | Normalmente é criado para integrar código existente. |
| Utiliza composição para reduzir subclasses. | Utiliza composição para converter chamadas.          |

Resumo:

- **Bridge separa responsabilidades.**
- **Adapter conecta sistemas incompatíveis.**

---

# Casos Reais de Uso

O Bridge aparece frequentemente em:

- sistemas de relatórios;
- renderizadores gráficos (OpenGL, DirectX, Vulkan);
- interfaces gráficas com múltiplos temas;
- drivers de dispositivos;
- sistemas multiplataforma;
- exportadores de arquivos (PDF, Excel, Word);
- motores de jogos.

---

# Exercício Proposto

Você foi contratado para desenvolver um sistema de notificações.

Existem dois tipos de notificações:

- Simples
- Completa

Além disso, as mensagens podem ser enviadas por diferentes canais:

- E-mail
- SMS
- WhatsApp

## Requisitos

Crie a seguinte estrutura:

**Implementação**

```text
CanalEnvio
```

Implementações:

- CanalEmail
- CanalSms
- CanalWhatsApp

---

**Abstração**

```text
Notificacao
```

Especializações:

- NotificacaoSimples
- NotificacaoCompleta

Cada notificação deverá utilizar qualquer canal de envio.

Exemplo:

```ts
const notificacao = new NotificacaoCompleta(new CanalWhatsApp());
```

Sem criar classes como:

```text
NotificacaoCompletaWhatsApp

NotificacaoCompletaEmail

NotificacaoSimplesSms
```

---

### Desafio Extra

Adicione um novo canal:

```text
CanalTelegram
```

Depois adicione um novo tipo:

```text
NotificacaoUrgente
```

Responda:

- Quantas classes precisaram ser modificadas?
- Foi necessário alterar os tipos antigos?
- Foi necessário alterar os canais existentes?

Se a resposta foi **"nenhuma"**, então você aplicou corretamente o padrão Bridge.

---

# Comparação com os Padrões Anteriores

Até agora vimos dois padrões estruturais.

| Padrão      | Principal objetivo                                                       |
| ----------- | ------------------------------------------------------------------------ |
| **Adapter** | Permitir que interfaces incompatíveis trabalhem juntas.                  |
| **Bridge**  | Separar duas dimensões independentes para evitar explosão de subclasses. |

Observe a diferença:

O **Adapter** normalmente é utilizado para **integrar** sistemas que já existem.

O **Bridge**, por outro lado, é pensado **durante o projeto da arquitetura**, separando responsabilidades para permitir que diferentes partes evoluam de forma independente.

Enquanto o Adapter resolve um problema de **compatibilidade**, o Bridge resolve um problema de **escalabilidade da estrutura do código**.

Essa distinção é fundamental, pois ambos utilizam composição, mas possuem propósitos completamente diferentes.
