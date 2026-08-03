# 05) Singleton (Instância Única)

## Categoria

**Padrão Criacional (Creational Pattern)**

---

# O que é o Singleton?

O **Singleton** é um padrão de projeto **criacional** cujo objetivo é **garantir que apenas uma única instância de uma classe exista durante toda a execução da aplicação**, além de fornecer um ponto único de acesso a essa instância.

Em outras palavras, independentemente da quantidade de vezes que uma classe for solicitada, sempre será retornado o **mesmo objeto**.

Ao contrário da maioria das classes, que permitem criar quantas instâncias forem necessárias utilizando `new`, uma classe Singleton controla completamente sua própria criação.

Isso é possível porque:

- o construtor é privado;
- apenas a própria classe pode criar sua instância;
- existe um método estático responsável por devolver sempre o mesmo objeto.

Esse padrão é extremamente utilizado quando existe um recurso que deve ser compartilhado por toda a aplicação.

---

# Problema que o Singleton resolve

Imagine uma aplicação que possui um sistema de logs.

Diversas partes do sistema registram informações:

- autenticação;
- cadastro de usuários;
- pagamentos;
- estoque;
- pedidos.

Se cada módulo criar seu próprio logger, teremos vários problemas.

```ts
const logger1 = new Logger();

const logger2 = new Logger();

const logger3 = new Logger();
```

Agora imagine que cada logger possua:

- configurações próprias;
- arquivo próprio;
- nível de log diferente;
- cache próprio.

A aplicação passa a ter diversos estados independentes.

Isso pode causar:

- duplicação de memória;
- inconsistência nas configurações;
- dificuldade para manutenção;
- comportamento inesperado.

O Singleton resolve esse problema garantindo que todos utilizem exatamente o mesmo objeto.

---

# Motivação (Cenário Real)

Imagine uma empresa onde existe apenas um diretor financeiro.

Sempre que um funcionário precisa aprovar um pagamento, ele procura o diretor.

Não importa quantas pessoas façam solicitações.

Todas conversam com a mesma pessoa.

Não faz sentido criar um novo diretor sempre que alguém precisar de uma autorização.

O Singleton segue exatamente essa ideia.

Existe apenas uma instância compartilhada.

---

# Analogia do Mundo Real

Imagine uma torre de controle em um aeroporto.

Diversos aviões precisam solicitar autorização para:

- pousar;
- decolar;
- mudar de rota.

Entretanto existe apenas **uma torre de controle** responsável por coordenar tudo.

Seria um desastre se cada avião criasse sua própria torre.

Da mesma forma, algumas classes da aplicação também devem existir apenas uma vez.

---

# Estrutura do Padrão

```text
                 Cliente
                    │
                    ▼
          Logger.getInstance()
                    │
                    ▼
             +------------------+
             |     Logger       |
             |------------------|
             | - instance       |
             | - constructor()  |
             | + getInstance()  |
             +------------------+
```

O cliente nunca utiliza `new`.

Toda criação passa pelo método `getInstance()`.

---

# Participantes

## 1. Singleton

É a classe responsável por controlar sua própria criação.

Ela possui:

- construtor privado;
- instância estática;
- método de acesso.

---

## 2. Instância Única

É um atributo estático responsável por armazenar o único objeto existente.

Exemplo:

```ts
private static instancia: Logger | null = null;
```

---

## 3. Método de Acesso

Normalmente chamado de:

```ts
getInstance();
```

Ele verifica:

- se o objeto já existe, retorna a instância existente;
- caso contrário, cria a instância e a armazena.

---

## 4. Cliente

O cliente nunca cria o objeto diretamente.

Sempre utiliza:

```ts
Logger.obterInstancia();
```

---

# Implementação Completa em TypeScript

```ts
/**
 * ============================================================
 * SINGLETON
 * ============================================================
 *
 * Esta classe garante que apenas uma única instância
 * possa existir durante toda a execução da aplicação.
 */

class Logger {
  /**
   * Armazena a única instância existente.
   *
   * Inicialmente ela não existe.
   */

  private static instancia: Logger | null = null;

  /**
   * O construtor é privado.
   *
   * Isso impede que qualquer classe faça:
   *
   * new Logger();
   */

  private constructor(private nivel: 'INFO' | 'DEBUG' = 'INFO') {}

  /**
   * Método responsável por devolver
   * sempre a mesma instância.
   */

  public static obterInstancia(): Logger {
    /**
     * Caso ainda não exista,
     * ela será criada.
     */

    if (Logger.instancia === null) {
      console.log('Criando Logger...');
      Logger.instancia = new Logger();
    }

    /**
     * Nas próximas chamadas,
     * retornamos exatamente o mesmo objeto.
     */

    return Logger.instancia;
  }

  /**
   * Registra mensagens informativas.
   */

  public info(mensagem: string): void {
    if (this.nivel === 'INFO' || this.nivel === 'DEBUG') {
      console.log('[INFO]', mensagem);
    }
  }

  /**
   * Registra mensagens de depuração.
   */

  public debug(mensagem: string): void {
    if (this.nivel === 'DEBUG') {
      console.log('[DEBUG]', mensagem);
    }
  }
}

/**
 * ============================================================
 * CLIENTE
 * ============================================================
 */

const logger1 = Logger.obterInstancia();

const logger2 = Logger.obterInstancia();

/**
 * Ambos apontam para exatamente
 * o mesmo objeto.
 */

console.log(logger1 === logger2);

/**
 * Utilizando normalmente.
 */

logger1.info('Sistema iniciado.');

logger2.info('Usuário autenticado.');
```

---

# Fluxo de Execução (Passo a Passo)

### Passo 1

O cliente solicita uma instância.

```ts
const logger = Logger.obterInstancia();
```

↓

### Passo 2

O método verifica:

```ts
Logger.instancia === null;
```

↓

### Passo 3

Como ainda não existe nenhuma instância, ela é criada.

```ts
new Logger();
```

↓

### Passo 4

A instância é armazenada.

```ts
Logger.instancia = ...
```

↓

### Passo 5

O objeto é retornado ao cliente.

↓

### Passo 6

Outro cliente solicita uma instância.

```ts
Logger.obterInstancia();
```

↓

### Passo 7

Agora o método encontra uma instância existente.

Nenhum novo objeto é criado.

↓

### Passo 8

O mesmo objeto é retornado novamente.

---

# Diagrama UML Simplificado

```text
                +----------------------------+
                |          Logger            |
                |----------------------------|
                | - instancia : Logger       |
                | - nivel                    |
                |----------------------------|
                | - constructor()            |
                | + obterInstancia()         |
                | + info()                   |
                | + debug()                  |
                +-------------▲--------------+
                              |
                              |
                          Cliente
```

---

# Como reconhecer um Singleton?

Existem algumas características muito fáceis de identificar.

- construtor privado;
- atributo estático armazenando a instância;
- método estático que retorna sempre o mesmo objeto;
- não existe uso de `new` fora da própria classe;
- todos os clientes compartilham exatamente a mesma instância.

---

# Exemplo de Memória

```text
Primeira chamada

Cliente
   │
   ▼

obterInstancia()

   │

Logger #1
```

Depois:

```text
Cliente A ───────┐
                 │
Cliente B ───────┤
                 ▼
            Logger #1
                 ▲
Cliente C ───────┘
```

Todos utilizam exatamente o mesmo objeto.

---

# Casos Reais de Uso

O Singleton aparece frequentemente em:

- Logger
- Configurações globais
- Cache
- Gerenciador de conexões
- Pool de conexões
- Sistema de autenticação
- Gerenciador de sessões
- Registro de serviços (Service Registry)

---

# Vantagens

## Apenas uma instância

Evita múltiplas cópias desnecessárias.

---

## Economia de memória

Como existe apenas um objeto, o consumo pode ser menor.

---

## Compartilhamento de estado

Todos utilizam exatamente os mesmos dados.

---

## Ponto único de acesso

Qualquer parte da aplicação consegue acessar o objeto.

---

## Controle da criação

A própria classe controla quando será criada.

---

# Desvantagens

## Estado Global

Todos compartilham os mesmos dados.

Alterações em um local podem impactar todo o sistema.

---

## Dificulta testes unitários

Como existe apenas uma instância, os testes podem influenciar uns aos outros caso o estado não seja restaurado corretamente.

---

## Alto acoplamento

Classes podem passar a depender diretamente do Singleton.

Isso dificulta substituições futuras.

---

## Viola parcialmente alguns princípios

Embora seja muito útil, seu uso excessivo pode aproximar o código de variáveis globais.

---

# Quando utilizar

Utilize Singleton quando:

- apenas uma instância faz sentido;
- o estado deve ser compartilhado;
- diversos módulos utilizam o mesmo recurso;
- deseja centralizar configurações;
- deseja controlar o acesso a um recurso único.

---

# Quando evitar

Evite Singleton quando:

- múltiplas instâncias podem existir;
- o estado precisa ser isolado;
- o sistema utiliza Injeção de Dependências (Dependency Injection);
- testes unitários são prioridade;
- o objeto não representa um recurso global.

Em aplicações modernas, muitos frameworks preferem utilizar **containers de Injeção de Dependências (DI)** para gerenciar o ciclo de vida dos objetos, reduzindo a necessidade de Singletons implementados manualmente.

---

# Relação com os Princípios SOLID

## S — Single Responsibility Principle (SRP)

Quando utilizado corretamente, o Singleton possui uma única responsabilidade: gerenciar um recurso compartilhado.

Entretanto, é comum ver Singletons acumulando diversas responsabilidades, tornando-se grandes classes "faz-tudo". Esse é um erro frequente.

---

## O — Open/Closed Principle (OCP)

O padrão em si não interfere diretamente nesse princípio.

Entretanto, se muitas classes dependerem diretamente do Singleton, futuras mudanças poderão exigir alterações em vários pontos do sistema.

---

## L — Liskov Substitution Principle (LSP)

Caso o Singleton implemente uma interface, ele poderá ser substituído por outra implementação sem alterar o comportamento esperado.

---

## I — Interface Segregation Principle (ISP)

É recomendável que clientes dependam de uma interface, e não diretamente da implementação Singleton.

Exemplo:

```ts
interface ILogger {
  info(mensagem: string): void;
}
```

Assim, durante os testes, podemos substituir facilmente o logger por uma implementação simulada (_Mock_).

---

## D — Dependency Inversion Principle (DIP)

Este é o princípio que mais merece atenção.

Quando escrevemos:

```ts
Logger.obterInstancia();
```

o código passa a depender diretamente da implementação concreta.

Uma alternativa mais flexível seria receber um `ILogger` por Injeção de Dependências.

Por isso, em arquiteturas modernas, muitas vezes o Singleton é implementado pelo próprio container de DI, e não manualmente pela classe.

---

# Anti-exemplo (O que NÃO fazer)

Permitir que qualquer parte do sistema crie novas instâncias do logger.

```ts
const logger1 = new Logger();

const logger2 = new Logger();

const logger3 = new Logger();
```

Problemas:

- várias instâncias independentes;
- configurações diferentes;
- desperdício de memória;
- perda do estado compartilhado;
- comportamento inconsistente.

Outro erro comum é utilizar Singletons para armazenar qualquer tipo de dado global, transformando a aplicação em um conjunto de variáveis globais disfarçadas.

---

# Singleton x Variáveis Globais

É comum confundir Singleton com uma variável global.

Existe uma diferença importante.

Uma variável global:

- pode ser modificada livremente;
- normalmente não controla acesso;
- não encapsula comportamento.

Já um Singleton:

- encapsula estado e comportamento;
- controla sua própria criação;
- fornece uma interface organizada para acesso aos dados.

Mesmo assim, seu uso excessivo pode produzir problemas semelhantes aos de variáveis globais.

---

# Exercício Proposto

Você foi contratado para desenvolver um sistema de configuração de uma aplicação.

Essas configurações incluem:

- nome da aplicação;
- idioma;
- tema;
- URL da API.

## Requisitos

Crie uma classe chamada:

```text
ConfiguracoesDoSistema
```

Ela deverá ser implementada como Singleton.

Adicione os seguintes métodos:

```text
obterInstancia()

obterConfiguracao()

alterarConfiguracao()
```

Depois:

1. Solicite a instância em três locais diferentes.
2. Altere o idioma em apenas um deles.
3. Verifique se os demais objetos enxergam a mesma alteração.
4. Utilize o operador `===` para confirmar que todas as referências apontam para o mesmo objeto.

### Desafio Extra

Implemente um contador interno indicando quantas vezes a instância foi solicitada.

Exemplo:

```text
Primeira chamada...

Instância criada.

Solicitação nº 1

Solicitação nº 2

Solicitação nº 3
```

Depois responda:

- Quantas instâncias realmente foram criadas?
- Todos os objetos possuem o mesmo endereço de memória?
- O que aconteceria se o construtor fosse público?
- Em quais cenários um Singleton pode causar problemas?

Se você conseguiu responder a essas perguntas, então compreendeu o verdadeiro objetivo do **Singleton**: **garantir uma única instância compartilhada de um recurso durante toda a execução da aplicação, oferecendo um ponto centralizado e controlado de acesso**.

---

# Comparação com os Padrões Criacionais Anteriores

Até aqui, cada padrão criacional resolve um problema diferente relacionado à criação de objetos.

| Padrão             | Principal objetivo                                                |
| ------------------ | ----------------------------------------------------------------- |
| **Factory Method** | Decide **como** um objeto será criado.                            |
| **Prototype**      | Decide **de onde** um objeto será criado (a partir de uma cópia). |
| **Singleton**      | Decide **quantas instâncias** podem existir (apenas uma).         |

Perceba que os três padrões tratam da criação de objetos, mas cada um resolve um problema completamente diferente:

- **Factory Method** reduz o acoplamento durante a criação.
- **Prototype** evita reconstruções desnecessárias reutilizando um objeto existente.
- **Singleton** controla o ciclo de vida de uma instância compartilhada.

Entender essa diferença é fundamental para escolher o padrão correto em cada situação.
