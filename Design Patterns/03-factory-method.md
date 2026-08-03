# 03) Factory Method (Método Fábrica)

## Categoria

**Padrão Criacional (Creational Pattern)**

---

# O que é o Factory Method?

O **Factory Method (Método Fábrica)** é um padrão de projeto **criacional** cujo objetivo é **delegar a responsabilidade pela criação de objetos para subclasses**, ao invés de permitir que o código cliente crie diretamente esses objetos.

Em outras palavras, ao invés de utilizar `new` em diversos pontos do sistema, criamos um **método especializado (Factory Method)** responsável por decidir qual implementação concreta deverá ser instanciada.

A principal vantagem dessa abordagem é que o código cliente deixa de depender de classes concretas e passa a trabalhar apenas com **abstrações**, tornando o sistema muito mais flexível, extensível e desacoplado.

Em sistemas reais, esse padrão aparece frequentemente quando diferentes objetos possuem o mesmo comportamento geral, mas implementações diferentes.

Alguns exemplos:

- envio de notificações (E-mail, SMS, Push Notification)
- geração de documentos (PDF, Excel, Word)
- gateways de pagamento (Stripe, Mercado Pago, PayPal)
- bancos de dados (MySQL, PostgreSQL, SQL Server)
- sistemas de armazenamento (Local, AWS S3, Azure Blob)

---

# Problema que o Factory Method resolve

Imagine que sua aplicação envie notificações.

Inicialmente existe apenas e-mail.

```ts
const notificacao = new NotificacaoEmail();
notificacao.enviar(...);
```

Tudo funciona perfeitamente.

Meses depois o cliente solicita:

- SMS
- Push Notification
- WhatsApp
- Telegram

Agora seu código começa a ficar assim:

```ts
if (tipo === "EMAIL") {
    new NotificacaoEmail().enviar(...);
}

if (tipo === "SMS") {
    new NotificacaoSms().enviar(...);
}

if (tipo === "PUSH") {
    new NotificacaoPush().enviar(...);
}

if (tipo === "WHATSAPP") {
    new NotificacaoWhatsApp().enviar(...);
}
```

Logo aparecem problemas importantes.

- muito `if` e `switch`
- alto acoplamento
- dificuldade para adicionar novos tipos
- quebra do princípio Aberto/Fechado (Open/Closed Principle)

Sempre que surgir uma nova notificação será necessário alterar diversos pontos do sistema.

O Factory Method elimina esse problema concentrando toda a lógica de criação em um único local.

---

# Motivação (Cenário Real)

Imagine um sistema de e-commerce.

Quando um pedido muda de status, uma notificação deve ser enviada.

Dependendo da preferência do cliente:

- alguns desejam receber E-mail;
- outros preferem SMS;
- outros utilizam Push Notification.

O processo de envio é praticamente o mesmo:

1. localizar o destinatário;
2. preparar a mensagem;
3. enviar.

A única coisa que muda é **qual canal será utilizado**.

O Factory Method permite alterar apenas a criação do canal de comunicação, mantendo todo o restante do algoritmo exatamente igual.

---

# Analogia do Mundo Real

Imagine uma pizzaria.

O cliente faz um pedido.

O atendente não prepara a pizza.

Ele apenas solicita ao pizzaiolo.

Dependendo do pedido, o pizzaiolo cria:

- Pizza Calabresa
- Pizza Portuguesa
- Pizza Quatro Queijos

O cliente não sabe quem preparou a pizza.

Ele apenas recebe uma pizza pronta.

No Factory Method acontece exatamente isso.

O cliente solicita um objeto.

Quem decide qual objeto concreto será criado é a fábrica.

---

# Estrutura do Padrão

O Factory Method normalmente possui quatro participantes principais.

```
Cliente
   │
   ▼
Criador Abstrato
   │
Factory Method()
   │
   ├───────────────┐
   ▼               ▼
CriadorEmail   CriadorSms
   │               │
   ▼               ▼
Email             Sms
```

---

# Participantes

## 1. Produto (Product)

Define a interface comum para todos os objetos criados.

No nosso exemplo:

```ts
interface Notificacao
```

Todos os tipos de notificação precisam seguir esse contrato.

---

## 2. Produtos Concretos (Concrete Products)

São as implementações reais.

Exemplo:

- NotificacaoEmail
- NotificacaoSms

Cada uma implementa o envio da sua maneira.

---

## 3. Criador (Creator)

É uma classe abstrata responsável por definir o Factory Method.

Ela conhece o algoritmo principal, mas não sabe qual objeto concreto será utilizado.

---

## 4. Criadores Concretos (Concrete Creators)

Cada subclasse decide qual objeto criar.

Exemplo:

- CriadorEmail
- CriadorSms

---

## 5. Cliente

É quem utiliza o sistema.

O cliente nunca cria diretamente um Email ou SMS.

Ele apenas conversa com o Criador.

Isso reduz significativamente o acoplamento.

---

# Implementação Completa em TypeScript

```ts
/**
 * ============================================================
 * CONTRATO (PRODUCT)
 * ============================================================
 *
 * Toda notificação deve implementar esta interface.
 * Assim, qualquer implementação poderá ser utilizada
 * pelo restante do sistema.
 */

interface Notificacao {
  enviar(destinatario: string, mensagem: string): void;
}

/**
 * ============================================================
 * PRODUTO CONCRETO
 * ============================================================
 */

class NotificacaoEmail implements Notificacao {
  enviar(destinatario: string, mensagem: string): void {
    console.log('========== EMAIL ==========');

    console.log(`Destinatário: ${destinatario}`);

    console.log(`Mensagem: ${mensagem}`);

    console.log('E-mail enviado com sucesso!');
  }
}

/**
 * ============================================================
 * PRODUTO CONCRETO
 * ============================================================
 */

class NotificacaoSms implements Notificacao {
  enviar(destinatario: string, mensagem: string): void {
    console.log('========== SMS ==========');

    console.log(`Número: ${destinatario}`);

    console.log(`Mensagem: ${mensagem}`);

    console.log('SMS enviado com sucesso!');
  }
}

/**
 * ============================================================
 * CLASSE ABSTRATA (CREATOR)
 * ============================================================
 *
 * Esta classe conhece o algoritmo principal.
 *
 * Porém ela NÃO sabe qual implementação concreta será utilizada.
 *
 * Essa decisão será tomada pelas subclasses.
 */

abstract class CriadorDeNotificacao {
  /**
   * Factory Method
   *
   * Cada subclasse deverá implementar este método
   * retornando uma implementação diferente.
   */
  protected abstract criarNotificacao(): Notificacao;

  /**
   * Método comum.
   *
   * Observe que ele não conhece Email nem SMS.
   *
   * Ele apenas trabalha com a interface Notificacao.
   */
  public enviarNotificacao(destinatario: string, mensagem: string): void {
    console.log();

    console.log('Preparando envio da notificação...');

    const notificacao = this.criarNotificacao();

    notificacao.enviar(destinatario, mensagem);

    console.log('Processo finalizado.');
  }
}

/**
 * ============================================================
 * CRIADOR CONCRETO
 * ============================================================
 */

class CriadorDeEmail extends CriadorDeNotificacao {
  protected criarNotificacao(): Notificacao {
    return new NotificacaoEmail();
  }
}

/**
 * ============================================================
 * CRIADOR CONCRETO
 * ============================================================
 */

class CriadorDeSms extends CriadorDeNotificacao {
  protected criarNotificacao(): Notificacao {
    return new NotificacaoSms();
  }
}

/**
 * Tipos disponíveis.
 */

type TipoDeNotificacao = 'EMAIL' | 'SMS';

/**
 * ============================================================
 * FÁBRICA AUXILIAR
 * ============================================================
 *
 * Apenas escolhe qual criador concreto utilizar.
 */

function obterCriadorDeNotificacao(tipo: TipoDeNotificacao): CriadorDeNotificacao {
  switch (tipo) {
    case 'EMAIL':
      return new CriadorDeEmail();

    case 'SMS':
      return new CriadorDeSms();
  }
}

/**
 * ============================================================
 * CLIENTE
 * ============================================================
 */

const criador = obterCriadorDeNotificacao('EMAIL');

criador.enviarNotificacao(
  'ana@empresa.com',

  'Seu pedido foi aprovado.',
);
```

---

# Fluxo de Execução (Passo a Passo)

### Passo 1

O cliente solicita um criador.

```ts
const criador = obterCriadorDeNotificacao('EMAIL');
```

↓

### Passo 2

A fábrica retorna:

```ts
CriadorDeEmail;
```

↓

### Passo 3

O cliente chama:

```ts
criador.enviarNotificacao(...)
```

↓

### Passo 4

O método comum executa:

```ts
const notificacao = this.criarNotificacao();
```

↓

### Passo 5

Como o objeto é um `CriadorDeEmail`, será criado:

```ts
new NotificacaoEmail();
```

↓

### Passo 6

A notificação envia a mensagem.

```ts
notificacao.enviar(...)
```

↓

### Passo 7

O cliente nunca precisou conhecer a classe concreta.

---

# Diagrama UML Simplificado

```text
                    <<interface>>
                    Notificacao
                    --------------------
                    + enviar()

                         ▲
              ┌──────────┴──────────┐
              │                     │
              │                     │
    NotificacaoEmail          NotificacaoSms

--------------------------------------------------

              CriadorDeNotificacao
              ---------------------------
              + enviarNotificacao()
              # criarNotificacao()

                     ▲
          ┌──────────┴───────────┐
          │                      │
          │                      │
   CriadorDeEmail          CriadorDeSms
```

---

# Como reconhecer um Factory Method?

Existem alguns sinais clássicos:

- existe uma classe abstrata chamada de Criador (Creator);
- existe um método responsável apenas pela criação dos objetos;
- as subclasses escolhem qual implementação concreta será utilizada;
- o restante do algoritmo permanece exatamente igual;
- o cliente trabalha apenas com interfaces.

Se você observar essas características, provavelmente está diante de um Factory Method.

---

# Vantagens

## Baixo acoplamento

O cliente depende apenas da interface.

---

## Fácil extensão

Adicionar um novo tipo normalmente significa apenas criar uma nova classe.

---

## Código mais organizado

Toda lógica de criação fica concentrada.

---

## Maior reutilização

O algoritmo principal pode ser reaproveitado por diversos produtos.

---

## Melhor manutenção

Mudanças ficam localizadas.

---

# Desvantagens

## Mais classes

Cada novo produto geralmente exige um novo criador.

---

## Complexidade inicial

Para projetos pequenos pode parecer exagerado.

---

## Curva de aprendizado

Iniciantes podem estranhar a existência de tantas abstrações.

---

# Quando utilizar

Utilize Factory Method quando:

- deseja eliminar o uso excessivo de `new`;
- deseja diminuir acoplamento;
- novas implementações surgem frequentemente;
- diferentes objetos compartilham o mesmo comportamento geral;
- deseja seguir os princípios SOLID.

---

# Quando evitar

Evite utilizar quando:

- existe apenas uma implementação;
- dificilmente surgirão novos tipos;
- o projeto é extremamente simples;
- a criação do objeto não possui nenhuma variação.

Nesses casos, uma simples instanciação pode ser suficiente.

---

# Relação com os Princípios SOLID

## S — Single Responsibility Principle (SRP)

Cada classe possui uma responsabilidade bem definida.

- produtos enviam notificações;
- criadores criam objetos;
- cliente apenas utiliza o serviço.

---

## O — Open/Closed Principle (OCP)

É o princípio mais beneficiado.

Podemos adicionar:

- NotificacaoWhatsApp
- NotificacaoPush
- NotificacaoTelegram

sem alterar o código existente.

Basta criar novos produtos e novos criadores.

---

## L — Liskov Substitution Principle (LSP)

Qualquer implementação de `Notificacao` pode substituir outra.

```ts
NotificacaoEmail

↓

NotificacaoSms

↓

NotificacaoPush
```

O restante do sistema continua funcionando.

---

## I — Interface Segregation Principle (ISP)

A interface possui apenas o necessário.

```ts
interface Notificacao {

    enviar(...)

}
```

Nenhum método desnecessário foi imposto.

---

## D — Dependency Inversion Principle (DIP)

O cliente depende da abstração.

Nunca de:

```ts
new NotificacaoEmail();
```

Mas sim de:

```ts
Notificacao;
```

Isso reduz significativamente o acoplamento.

---

# Anti-exemplo (O que NÃO fazer)

Neste exemplo o cliente conhece todas as implementações concretas e decide qual instanciar.

```ts
function enviarNotificacao(
  tipo: 'EMAIL' | 'SMS',

  destinatario: string,

  mensagem: string,
) {
  if (tipo === 'EMAIL') {
    new NotificacaoEmail().enviar(
      destinatario,

      mensagem,
    );
  } else {
    new NotificacaoSms().enviar(
      destinatario,

      mensagem,
    );
  }
}
```

Problemas:

- viola o princípio Aberto/Fechado;
- aumenta o acoplamento;
- exige alterações sempre que um novo tipo surgir;
- dificulta testes unitários;
- espalha a lógica de criação pela aplicação.

---

# Exercício Proposto

Seu desafio é expandir o sistema adicionando um novo canal de comunicação.

## Requisitos

Crie uma nova implementação chamada:

```text
NotificacaoWhatsApp
```

Ela deve implementar a interface `Notificacao`.

Depois crie:

```text
CriadorDeWhatsApp
```

que retorna essa nova implementação.

Atualize a fábrica para aceitar:

```text
"WHATSAPP"
```

Ao final, o código abaixo deverá funcionar sem alterações no restante da aplicação:

```ts
const criador = obterCriadorDeNotificacao('WHATSAPP');

criador.enviarNotificacao(
  '(34) 99999-9999',

  'Seu pedido saiu para entrega.',
);
```

### Desafio Extra

Implemente também uma classe:

```text
NotificacaoPush
```

Depois reflita:

- Foi necessário alterar o método `enviarNotificacao()`?
- Foi necessário alterar as classes existentes de Email e SMS?
- Quantas linhas precisaram ser modificadas para adicionar um novo canal?

Se a resposta for **"quase nenhuma"**, então você compreendeu corretamente o principal objetivo do **Factory Method**: **permitir a extensão do sistema sem modificar seu comportamento principal**, tornando o código mais organizado, flexível e preparado para evoluir.
