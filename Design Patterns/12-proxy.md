# 12) Proxy (Representante)

> **Categoria:** Estrutural

---

# O que é o padrão Proxy?

O **Proxy (Representante)** é um padrão de projeto estrutural que fornece **um objeto substituto (intermediário)** para controlar o acesso a outro objeto.

Em vez do cliente conversar diretamente com o objeto real, ele conversa com um **Proxy**, que possui exatamente a mesma interface.

O Proxy decide o que fazer com cada solicitação.

Ele pode, por exemplo:

- verificar permissões;
- criar o objeto apenas quando necessário (_Lazy Loading_);
- armazenar resultados em cache;
- registrar logs;
- controlar acesso concorrente;
- limitar requisições;
- acessar objetos remotos.

Em outras palavras:

> **O Proxy fica entre o cliente e o objeto real, interceptando todas as chamadas antes que elas cheguem ao destino.**

---

# Problema que o Proxy resolve

Imagine um sistema de e-commerce.

Sempre que um usuário acessa um produto, o sistema consulta um serviço externo.

```
Cliente

↓

API Produtos

↓

Banco de Dados
```

Cada consulta leva aproximadamente:

```
500ms
```

Agora imagine que milhares de usuários consultam constantemente o mesmo produto.

Sem Proxy:

```
Cliente

↓

API

↓

Banco

↓

Resposta

Cliente

↓

API

↓

Banco

↓

Resposta

Cliente

↓

API

↓

Banco

↓

Resposta
```

Mesmo quando o produto já foi consultado anteriormente, o sistema continua realizando novas consultas.

Isso aumenta:

- consumo de rede;
- processamento;
- carga do banco de dados;
- tempo de resposta.

O Proxy pode armazenar os resultados em memória.

Assim:

Primeira consulta:

```
Cliente

↓

Proxy

↓

API

↓

Banco
```

Segunda consulta:

```
Cliente

↓

Proxy

↓

Cache
```

Nenhuma nova requisição é realizada.

---

# Motivação (cenário real)

Imagine que você mora em um condomínio.

Quem controla o acesso ao prédio?

O porteiro.

O visitante não entra diretamente.

Primeiro ele conversa com o porteiro.

O porteiro pode:

- verificar identidade;
- registrar horário;
- autorizar ou negar entrada;
- telefonar para o morador.

O porteiro representa exatamente o papel do Proxy.

O morador é o objeto real.

---

# Analogia do mundo real

Imagine um cartão de crédito.

Você compra um produto.

Você não entrega dinheiro diretamente ao estabelecimento.

O cartão funciona como um intermediário.

Ele:

- verifica saldo;
- valida senha;
- registra a compra;
- aprova ou rejeita a transação.

O vendedor nunca conversa diretamente com o banco.

Existe um representante.

Esse representante é um Proxy.

---

# Tipos mais comuns de Proxy

## Proxy Virtual (Virtual Proxy)

Cria objetos pesados apenas quando realmente forem necessários.

Exemplo:

Uma imagem de 200 MB.

Ela só será carregada quando o usuário realmente abrir a foto.

---

## Proxy de Proteção (Protection Proxy)

Controla permissões.

Exemplo:

```
Administrador

↓

Pode excluir usuários

-------------------

Visitante

↓

Não pode excluir usuários
```

---

## Proxy Remoto (Remote Proxy)

Representa um objeto localizado em outro computador.

Exemplo:

- API REST
- gRPC
- SOAP

---

## Proxy de Cache (Caching Proxy)

Armazena resultados para evitar consultas repetidas.

É o exemplo que implementaremos.

---

## Proxy Inteligente (Smart Proxy)

Executa tarefas adicionais antes ou depois da chamada.

Exemplos:

- logs;
- auditoria;
- métricas;
- monitoramento.

---

# Estrutura do padrão

```text
                Cliente
                   │
                   ▼
             +------------+
             |   Proxy    |
             +------------+
             | controla   |
             +------------+
                   │
                   ▼
            +---------------+
            | Objeto Real   |
            +---------------+
```

---

# Participantes

## 1. Subject (Interface)

É a interface comum.

Cliente, Proxy e Objeto Real utilizam exatamente o mesmo contrato.

No exemplo:

```ts
ServicoCatalogo;
```

---

## 2. RealSubject (Objeto Real)

É quem realiza o trabalho de verdade.

No exemplo:

```ts
CatalogoReal;
```

---

## 3. Proxy

Implementa a mesma interface.

Recebe todas as chamadas.

Pode:

- bloquear;
- registrar;
- armazenar;
- reutilizar;
- criar o objeto sob demanda.

---

## 4. Cliente

Conhece apenas a interface.

Ele nem sabe se está utilizando um Proxy ou o objeto real.

---

# Diagrama UML simplificado

```text
               +--------------------------+
               |      ServicoCatalogo     |
               +--------------------------+
               | + buscarPorId()          |
               +------------▲-------------+
                            |
            +---------------+----------------+
            |                                |
+---------------------------+     +----------------------------+
| CatalogoReal              |     | CatalogoProxy             |
+---------------------------+     +----------------------------+
| + buscarPorId()           |     | - cache                   |
+---------------------------+     | - catalogoReal            |
                                  | + buscarPorId()           |
                                  +-------------+-------------+
                                                |
                                                ▼
                                      +---------------------+
                                      |  CatalogoReal       |
                                      +---------------------+
```

---

# Implementação completa em TypeScript

```ts
/**
 * ============================================================
 * PROXY
 * ============================================================
 *
 * Neste exemplo criaremos um Proxy responsável
 * por armazenar consultas em cache.
 *
 * Assim evitaremos consultar o catálogo
 * repetidamente.
 */

/**
 * Produto retornado pelo catálogo.
 */
type Produto = {
  id: string;

  nome: string;
};

/**
 * ============================================================
 * INTERFACE
 * ============================================================
 */

interface ServicoCatalogo {
  buscarProdutoPorId(id: string): Produto | null;
}

/**
 * ============================================================
 * OBJETO REAL
 * ============================================================
 *
 * Simula uma consulta cara
 * (API ou Banco de Dados).
 */
class CatalogoReal implements ServicoCatalogo {
  buscarProdutoPorId(id: string): Produto | null {
    console.log('Consultando banco de dados...');

    if (id === 'P1') {
      return {
        id: 'P1',

        nome: 'Notebook',
      };
    }

    if (id === 'P2') {
      return {
        id: 'P2',

        nome: 'Mouse Gamer',
      };
    }

    return null;
  }
}

/**
 * ============================================================
 * PROXY
 * ============================================================
 */

class CatalogoProxy implements ServicoCatalogo {
  /**
   * Cache das consultas.
   */
  private cache = new Map<string, Produto | null>();

  constructor(private catalogo: CatalogoReal) {}

  buscarProdutoPorId(id: string): Produto | null {
    /**
     * Verifica se já existe
     * no cache.
     */
    if (this.cache.has(id)) {
      console.log('Retornando produto do cache.');

      return this.cache.get(id)!;
    }

    /**
     * Caso contrário consulta
     * o objeto real.
     */
    const produto = this.catalogo.buscarProdutoPorId(id);

    /**
     * Guarda o resultado
     * para consultas futuras.
     */
    this.cache.set(
      id,

      produto,
    );

    return produto;
  }
}

/**
 * ============================================================
 * CLIENTE
 * ============================================================
 */

const servico: ServicoCatalogo = new CatalogoProxy(new CatalogoReal());

console.log(servico.buscarProdutoPorId('P1'));

console.log(servico.buscarProdutoPorId('P1'));

console.log(servico.buscarProdutoPorId('P2'));

console.log(servico.buscarProdutoPorId('P2'));
```

---

# Saída esperada

```text
Consultando banco de dados...

{ id: 'P1', nome: 'Notebook' }

Retornando produto do cache.

{ id: 'P1', nome: 'Notebook' }

Consultando banco de dados...

{ id: 'P2', nome: 'Mouse Gamer' }

Retornando produto do cache.

{ id: 'P2', nome: 'Mouse Gamer' }
```

Observe que a consulta ao banco ocorre apenas na primeira vez para cada produto.

---

# Fluxo de execução (passo a passo)

## Passo 1

O cliente cria apenas o Proxy.

```ts
const servico = new CatalogoProxy(new CatalogoReal());
```

---

## Passo 2

O cliente solicita um produto.

```ts
servico.buscarProdutoPorId('P1');
```

---

## Passo 3

O Proxy verifica o cache.

```
Produto existe?

↓

Não
```

---

## Passo 4

O Proxy encaminha a chamada para o objeto real.

```
Proxy

↓

CatalogoReal

↓

Banco de Dados
```

---

## Passo 5

O resultado é armazenado no cache.

```
Cache

↓

P1 → Notebook
```

---

## Passo 6

O cliente solicita novamente o mesmo produto.

```ts
servico.buscarProdutoPorId('P1');
```

---

## Passo 7

Agora o Proxy encontra o resultado no cache.

```
Produto existe?

↓

Sim

↓

Retorna imediatamente
```

Nenhuma consulta ao banco é realizada.

---

# Fluxo interno

```text
Cliente

↓

CatalogoProxy

↓

Existe no cache?

↓

Não

↓

CatalogoReal

↓

Banco

↓

Resultado

↓

Cache

↓

Cliente
```

Na segunda chamada:

```text
Cliente

↓

CatalogoProxy

↓

Cache

↓

Cliente
```

---

# Como reconhecer que é Proxy?

Alguns sinais indicam claramente o uso desse padrão:

- Existe um objeto intermediário.
- Cliente e objeto real utilizam a mesma interface.
- O objeto intermediário intercepta chamadas.
- O Proxy adiciona comportamentos sem alterar o objeto real.
- O cliente nem percebe que existe um Proxy.

---

# Vantagens

## Controle de acesso

Permissões podem ser verificadas antes da execução.

---

## Cache

Evita consultas repetidas.

---

## Lazy Loading

Objetos pesados podem ser criados apenas quando necessários.

---

## Registro de logs

O Proxy pode registrar todas as operações realizadas.

---

## Segurança

Pode impedir acesso indevido ao objeto real.

---

## Menor acoplamento

O cliente não depende diretamente da implementação concreta.

---

# Desvantagens

## Mais uma camada

Existe um objeto adicional entre cliente e objeto real.

---

## Código mais complexo

Em sistemas pequenos pode ser desnecessário.

---

## Depuração

Às vezes é preciso percorrer Proxy e Objeto Real para entender o fluxo completo.

---

# Quando utilizar

Utilize Proxy quando:

- precisar implementar cache;
- quiser controlar permissões;
- precisar registrar logs;
- desejar criar objetos sob demanda (_Lazy Loading_);
- acessar objetos remotos;
- limitar chamadas para APIs externas.

Exemplos comuns:

- APIs REST;
- Banco de Dados;
- Sistemas de autenticação;
- Arquivos grandes;
- Imagens;
- Vídeos;
- Downloads;
- Microserviços;
- Serviços de pagamento.

---

# Quando evitar

Evite utilizar Proxy quando:

- o objeto real for extremamente simples;
- não existir necessidade de controle adicional;
- a camada extra apenas aumentará a complexidade.

---

# Relação com os princípios SOLID

## S — Single Responsibility Principle (SRP)

Cada classe possui uma responsabilidade específica.

- `CatalogoReal` consulta os dados.
- `CatalogoProxy` controla o acesso e gerencia o cache.

---

## O — Open/Closed Principle (OCP)

É possível criar novos tipos de Proxy (cache, log, segurança, lazy loading) sem alterar o objeto real.

---

## L — Liskov Substitution Principle (LSP)

O Proxy pode substituir o objeto real, pois ambos implementam a mesma interface.

---

## I — Interface Segregation Principle (ISP)

A interface `ServicoCatalogo` contém apenas os métodos necessários para consultar o catálogo.

---

## D — Dependency Inversion Principle (DIP)

O cliente depende da abstração (`ServicoCatalogo`), e não das implementações concretas (`CatalogoReal` ou `CatalogoProxy`).

---

# Anti-exemplo

Um erro comum é espalhar lógica de cache por toda a aplicação.

```ts
if (!cache.has(id)) {
  const produto = banco.buscar(id);

  cache.set(id, produto);
}

return cache.get(id);
```

Se vários módulos precisarem consultar produtos, todos acabarão duplicando essa lógica.

O Proxy centraliza essa responsabilidade em um único lugar.

---

# Exercício proposto

Implemente um sistema de consulta de usuários utilizando o padrão Proxy.

### Requisitos

Crie a interface:

```ts
ServicoUsuarios;
```

Implemente a classe:

```ts
RepositorioUsuarios;
```

Ela deverá simular uma consulta em um banco de dados.

Depois implemente um Proxy chamado:

```ts
RepositorioUsuariosProxy;
```

Ele deverá:

- armazenar usuários em cache;
- evitar consultas repetidas;
- registrar no console quando um dado vier do banco ou do cache.

### Exemplo esperado

```text
Consultando banco...

Usuário encontrado

------------------------

Consultando cache...

Usuário encontrado
```

---

### Desafio extra

Implemente um **Proxy de Proteção**.

Antes de permitir a consulta, o Proxy deverá verificar o perfil do usuário.

Exemplo:

```text
Administrador

↓

Permite consulta

--------------------

Visitante

↓

Acesso negado
```

O objeto `RepositorioUsuarios` **não deverá conhecer nenhuma regra de autorização**.

Toda a lógica de segurança deverá ficar dentro do Proxy, demonstrando uma das maiores vantagens desse padrão: **adicionar responsabilidades como cache, segurança, logs ou carregamento sob demanda sem modificar o objeto real e mantendo o cliente desacoplado.**
