# SOLID com TypeScript

> Este módulo apresenta os cinco princípios **SOLID**, um conjunto de boas práticas para o desenvolvimento de software orientado a objetos.
>
> O objetivo é compreender como escrever códigos mais organizados, reutilizáveis, desacoplados e fáceis de manter, utilizando exemplos simples em **TypeScript**.

---

# O que é SOLID?

**SOLID** é um conjunto de cinco princípios de design orientado a objetos que auxiliam na construção de sistemas mais flexíveis e sustentáveis ao longo do tempo.

Esses princípios foram propostos para reduzir problemas comuns em projetos de software, como:

- alto acoplamento entre classes;
- dificuldade para realizar manutenção;
- duplicação de código;
- baixa reutilização;
- dificuldade para testar componentes;
- excesso de responsabilidades em uma única classe.

Embora tenham sido definidos para a Programação Orientada a Objetos (POO), seus conceitos também influenciam arquiteturas modernas, APIs, microsserviços e frameworks.

---

# Origem do SOLID

Os princípios foram introduzidos por Robert C. Martin, conhecido mundialmente como **Uncle Bob**, ao longo dos anos 1990 e consolidados em seus livros e artigos sobre Engenharia de Software.

O acrônimo **SOLID** foi posteriormente organizado por Michael Feathers, utilizando a primeira letra de cada princípio.

Hoje, SOLID é considerado um dos pilares do desenvolvimento orientado a objetos e serve como base para diversas arquiteturas modernas, incluindo Clean Architecture e muitos Design Patterns.

---

# O significado da palavra SOLID

| Letra | Princípio                       |
| ----- | ------------------------------- |
| **S** | Single Responsibility Principle |
| **O** | Open/Closed Principle           |
| **L** | Liskov Substitution Principle   |
| **I** | Interface Segregation Principle |
| **D** | Dependency Inversion Principle  |

Cada princípio resolve um problema específico relacionado ao design de software.

---

# S — Single Responsibility Principle (SRP)

## Definição

> **Uma classe deve possuir apenas um único motivo para mudar.**

Isso significa que cada classe deve possuir apenas uma responsabilidade bem definida.

---

## Exemplo incorreto

A classe faz duas coisas diferentes:

- calcula salário;
- envia e-mail.

```typescript
class FuncionarioService {
  calcularSalario() {
    console.log('Calculando salário...');
  }

  enviarEmail() {
    console.log('Enviando e-mail...');
  }
}
```

Se mudar a regra do cálculo ou a forma de enviar e-mails, a mesma classe precisará ser modificada.

---

## Exemplo correto

Cada classe possui apenas uma responsabilidade.

```typescript
class CalculadoraSalario {
  calcular() {
    console.log('Calculando salário...');
  }
}

class EmailService {
  enviar() {
    console.log('Enviando e-mail...');
  }
}
```

### Benefícios

- código mais organizado;
- manutenção mais simples;
- maior reutilização;
- testes mais fáceis.

---

# O — Open/Closed Principle (OCP)

## Definição

> **Classes devem estar abertas para extensão, mas fechadas para modificação.**

Sempre que possível, novas funcionalidades devem ser adicionadas sem alterar código já existente.

---

## Exemplo incorreto

```typescript
class Desconto {
  calcular(tipo: string) {
    if (tipo === 'cliente') return 10;

    if (tipo === 'vip') return 20;

    return 0;
  }
}
```

Sempre que surgir um novo tipo de desconto será necessário alterar essa classe.

---

## Exemplo correto

```typescript
interface Desconto {
  calcular(): number;
}

class Cliente implements Desconto {
  calcular() {
    return 10;
  }
}

class Vip implements Desconto {
  calcular() {
    return 20;
  }
}
```

Agora basta criar uma nova classe para novos descontos.

Nenhum código existente precisa ser alterado.

---

# L — Liskov Substitution Principle (LSP)

## Definição

> **Uma classe derivada deve poder substituir sua classe base sem alterar o comportamento esperado do programa.**

Em outras palavras, uma subclasse deve respeitar o contrato definido pela classe pai.

---

## Exemplo incorreto

```typescript
class Ave {
  voar() {
    console.log('Voando...');
  }
}

class Pinguim extends Ave {
  voar() {
    throw new Error('Pinguins não voam.');
  }
}
```

O programa espera que toda ave consiga voar, mas isso não é verdade.

---

## Exemplo correto

```typescript
class Ave {}

class AveVoadora extends Ave {
  voar() {
    console.log('Voando...');
  }
}

class Pinguim extends Ave {}
```

Agora cada classe representa corretamente seu comportamento.

---

# I — Interface Segregation Principle (ISP)

## Definição

> **Nenhuma classe deve ser obrigada a implementar métodos que não utiliza.**

Interfaces muito grandes dificultam manutenção e implementação.

---

## Exemplo incorreto

```typescript
interface Animal {
  correr(): void;
  nadar(): void;
  voar(): void;
}
```

Nem todo animal voa ou nada.

---

## Exemplo correto

```typescript
interface Corredor {
  correr(): void;
}

interface Nadador {
  nadar(): void;
}

interface Voador {
  voar(): void;
}

class Cachorro implements Corredor {
  correr() {
    console.log('Correndo...');
  }
}
```

Cada classe implementa apenas aquilo que realmente utiliza.

---

# D — Dependency Inversion Principle (DIP)

## Definição

> **Módulos de alto nível não devem depender de módulos de baixo nível. Ambos devem depender de abstrações.**

Na prática, significa depender de interfaces, e não de implementações concretas.

---

## Exemplo incorreto

```typescript
class MySQL {
  salvar() {
    console.log('Salvando no MySQL');
  }
}

class UsuarioService {
  banco = new MySQL();

  salvarUsuario() {
    this.banco.salvar();
  }
}
```

O serviço depende diretamente do MySQL.

---

## Exemplo correto

```typescript
interface BancoDados {
  salvar(): void;
}

class MySQL implements BancoDados {
  salvar() {
    console.log('Salvando no MySQL');
  }
}

class PostgreSQL implements BancoDados {
  salvar() {
    console.log('Salvando no PostgreSQL');
  }
}

class UsuarioService {
  constructor(private banco: BancoDados) {}

  salvarUsuario() {
    this.banco.salvar();
  }
}

const service = new UsuarioService(new PostgreSQL());

service.salvarUsuario();
```

Agora o serviço pode trabalhar com qualquer banco que implemente a interface.

---

# Resumo dos princípios

| Princípio | Objetivo                                                    |
| --------- | ----------------------------------------------------------- |
| **SRP**   | Uma classe deve ter apenas uma responsabilidade.            |
| **OCP**   | Estender funcionalidades sem modificar código existente.    |
| **LSP**   | Subclasses devem substituir corretamente suas superclasses. |
| **ISP**   | Interfaces pequenas e específicas.                          |
| **DIP**   | Depender de abstrações, não de implementações concretas.    |

---

# SOLID e Design Patterns

Grande parte dos Design Patterns foi criada justamente para facilitar a aplicação dos princípios SOLID.

Alguns exemplos:

| Design Pattern   | Princípios relacionados |
| ---------------- | ----------------------- |
| Factory Method   | OCP, DIP                |
| Abstract Factory | OCP, DIP                |
| Builder          | SRP                     |
| Strategy         | OCP, DIP                |
| Observer         | OCP                     |
| Decorator        | OCP                     |
| Adapter          | DIP                     |
| Proxy            | SRP                     |
| Facade           | SRP                     |
| Command          | SRP, OCP                |
| State            | OCP                     |

Isso significa que, ao estudar Design Patterns, você perceberá que muitos deles são formas práticas de aplicar um ou mais princípios SOLID em situações recorrentes do desenvolvimento de software.

---

# Boas práticas

- Dê a cada classe uma responsabilidade clara.
- Prefira composição em vez de herança quando fizer sentido.
- Utilize interfaces para reduzir o acoplamento.
- Evite classes "gigantes" com muitas responsabilidades.
- Escreva código pensando em futuras extensões.
- Programe contra abstrações, e não contra implementações concretas.

---

# Conclusão

Os princípios SOLID representam um conjunto de boas práticas para a construção de software orientado a objetos. Eles não são regras rígidas, mas diretrizes que ajudam a produzir sistemas mais organizados, flexíveis e fáceis de evoluir.

Ao aplicar esses princípios, o código tende a ser mais reutilizável, testável e desacoplado. Além disso, muitos dos Design Patterns estudados nos próximos módulos utilizam SOLID como fundamento, tornando esse conhecimento essencial para compreender arquiteturas modernas e desenvolver aplicações de qualidade.
