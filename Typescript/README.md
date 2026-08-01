# Introdução completa ao TypeScript (Aula Técnica)

> **Objetivo:** fazer você entender o que é **TypeScript**, por que ele existe, quais problemas ele resolve e como usar seus principais conceitos no dia a dia com exemplos práticos, comentários didáticos, `console.log` e foco em consulta para estudo.

---

## 1) O que é TypeScript?

TypeScript é uma linguagem de programação desenvolvida pela Microsoft que estende o JavaScript, adicionando **tipagem estática** e recursos de linguagem mais avançados. Isso significa que, diferentemente do JavaScript puro, o TypeScript permite declarar explicitamente os tipos de variáveis, funções, objetos, etc.

Na prática:

- você escreve arquivos `.ts`;
- o TypeScript analisa os tipos em tempo de desenvolvimento;
- depois ele compila/transpila o código para JavaScript.

### Por que usar TypeScript?

Ele ajuda a:

1. Detecção de erros em tempo de desenvolvimento: evita erros comuns ao detectar problemas de tipos antes da execução.

2. Melhora ferramentas de desenvolvimento: autocomplete, navegação, refatoração mais inteligente no editor.

3. Código mais legível e previsível: contratos claros de tipos facilitam entender o que uma função ou objeto deve receber.

4. Facilita manutenção e refatoração: mudanças podem ser feitas com maior segurança.

5. Contratos explícitos: declarações de tipos explicitas tornam o código mais confiável.

### Exemplo comparativo rápido

Se você tentar passar um argumento errado, o compilador aponta o erro antes de executar.

```ts
function somar(a: number, b: number): number {
  return a + b;
}

console.log("Soma correta:", somar(10, 20));
// console.log(somar("10", 20));
// Erro: string não pode ser usada onde se espera number.
```

---

## 2) Passo a passo inicial para começar com TypeScript

Se o aluno estiver começando do zero, este é um fluxo inicial simples.

### 2.1 Criar uma pasta do projeto

```bash
mkdir meu-projeto-ts
cd meu-projeto-ts
```

### 2.2 Inicializar o projeto Node.js

```bash
npm init -y
```

Esse comando cria o arquivo `package.json` com uma configuração inicial.

### 2.3 Instalar o TypeScript e Dependências

```bash
npm install typescript --save-dev
```

```bash
npm install ts-node
```

- `typescript`: instala o compilador TypeScript.
- `--save-dev`: indica que essa dependência será usada no desenvolvimento.
- `ts-node`: permite rodar arquivos .ts direto, sem precisar gerar .js manualmente.

### 2.4 Criar o arquivo de configuração `tsconfig.json`

A forma mais comum é usar:

```bash
npx tsc --init
```

Esse comando gera automaticamente um arquivo `tsconfig.json` com várias opções comentadas.

### 2.5 Ajustar o `tsconfig.json`

Depois de gerar o arquivo, você pode deixar uma versão inicial mais simples assim:

```json
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "commonjs",
    "strict": true
  }
}
```

### 2.6 O que significa cada opção?

#### `target: "ES2020"`

Define para qual versão do JavaScript o código será compilado.

- Se você usa recursos mais modernos da linguagem, um target mais novo ajuda.
- `ES2020` é uma configuração bastante comum e moderna.

#### `module: "commonjs"`

Define o sistema de módulos usado na saída gerada.

- `commonjs` é muito comum em projetos Node.js.
- Em projetos backend iniciais, costuma ser uma escolha simples e prática.

#### `strict: true`

Ativa um conjunto de verificações mais rigorosas.

Essa é uma das configurações mais importantes para aprender bem TypeScript, porque ela força o código a ser mais seguro.

### 2.7 Outras opções úteis do `tsconfig.json`

Depois que você já entendeu o básico, vale conhecer algumas opções muito usadas.

#### `rootDir`

Define a pasta de origem do código TypeScript.

```json
{
  "compilerOptions": {
    "rootDir": "./src"
  }
}
```

#### `outDir`

Define a pasta onde o JavaScript compilado será gerado.

```json
{
  "compilerOptions": {
    "outDir": "./dist"
  }
}
```

#### `noEmitOnError`

Impede a geração de arquivos JavaScript quando existem erros de tipagem.

```json
{
  "compilerOptions": {
    "noEmitOnError": true
  }
}
```

#### `esModuleInterop`

Ajuda na compatibilidade entre diferentes estilos de importação.

```json
{
  "compilerOptions": {
    "esModuleInterop": true
  }
}
```

### 2.8 Exemplo de `tsconfig.json` um pouco mais completo

```json
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "commonjs",
    "strict": true,
    "rootDir": "./src",
    "outDir": "./dist",
    "noEmitOnError": true,
    "esModuleInterop": true
  }
}
```

### 2.9 Estrutura simples de projeto

```text
meu-projeto-ts/
├─ src/
│  └─ index.ts
├─ dist/
├─ package.json
└─ tsconfig.json
```

### 2.10 Criar seu primeiro arquivo TypeScript

Crie `src/index.ts`:

```ts
const mensagem: string = "Olá, TypeScript";
console.log(mensagem);
```

### 2.11 Rodar o projeto

- Nesse passo, garanta que o terminal esteja no mesmo diretório do arquivo para sua correta execução.

```bash
npx ts-node index.ts
```

### 2.12 Resumo do fluxo inicial

1. Criar pasta do projeto.
2. Rodar `npm init -y`.
3. Instalar typeScript e ts-node.
4. Rodar `npx tsc --init`.
5. Ajustar o `tsconfig.json`.
6. Criar a pasta `src`.
7. Escrever arquivos `.ts`.
8. Compilar com `npx ts-node nomeArquivo.ts`.
9. Executar com Node.js.

---

## 3) Como o TypeScript funciona

TypeScript não é executado diretamente pelo navegador ou pelo Node.js da forma como você escreve. Antes, ele é convertido para JavaScript.

Fluxo:

1. você escreve `.ts`;
2. o compilador TypeScript analisa tipos;
3. ele gera `.js`;
4. o JavaScript é executado.

### Exemplo simples

```ts
const linguagem: string = "TypeScript";
console.log("Estou estudando:", linguagem);
```

---

## 4) Tipos básicos

### 4.1 `number`

```ts
let idade: number = 25;
let altura: number = 1.75;

console.log("Idade:", idade);
console.log("Altura:", altura);
```

### 4.2 `string`

```ts
let nome: string = "Ana";
let saudacao: string = `Olá, ${nome}!`;

console.log(saudacao);
```

### 4.3 `boolean`

```ts
let aprovado: boolean = true;
console.log("Está aprovado?", aprovado);
```

### 4.4 `null` e `undefined`

```ts
let resposta: null = null;
let valorIndefinido: undefined = undefined;

console.log("Resposta:", resposta);
console.log("Valor indefinido:", valorIndefinido);
```

### 4.5 `any`

`any` desativa parte importante da proteção de tipos.

```ts
let dado: any = "texto";
console.log("Valor atual:", dado);

dado = 123;
console.log("Novo valor:", dado);

dado = true;
console.log("Novo valor novamente:", dado);
```

> Use `any` com cuidado.

### 4.6 `unknown`

Mais seguro que `any`, porque exige validação antes do uso.

```ts
let entrada: unknown = "42";

if (typeof entrada === "string") {
  console.log("A entrada é texto e tem", entrada.length, "caracteres.");
}
```

### 4.7 `void`

```ts
function exibirMensagem(texto: string): void {
  console.log("Mensagem:", texto);
}

exibirMensagem("Bem-vindo ao TypeScript");
```

### 4.8 `never`

```ts
function lancarErro(mensagem: string): never {
  throw new Error(mensagem);
}

// lancarErro("Erro proposital");
```

---

## 5) Inferência de tipos

Muitas vezes o TypeScript descobre o tipo automaticamente.

```ts
let cidade = "São Paulo";
let temperatura = 28;
let ativo = false;

console.log("Cidade:", cidade);
console.log("Temperatura:", temperatura);
console.log("Ativo:", ativo);
```

Se você tentar trocar por um tipo incompatível, o compilador avisa.

```ts
let total = 100;
// total = "cem";
// Erro: string não pode ser atribuída a number.
```

---

## 6) Arrays

### 6.1 Sintaxe com `tipo[]`

```ts
let numeros: number[] = [10, 20, 30, 40];
console.log("Números:", numeros);
```

### 6.2 Sintaxe com `Array<tipo>`

```ts
let nomes: Array<string> = ["Ana", "Carlos", "Marina"];
console.log("Nomes:", nomes);
```

### 6.3 Exemplo prático: média de notas

```ts
let notas: number[] = [8.5, 7.0, 9.2, 6.8];
let somaNotas = 0;

for (const nota of notas) {
  somaNotas += nota;
  console.log("Somando nota:", nota, "| Soma parcial:", somaNotas);
}

const media = somaNotas / notas.length;
console.log("Média final:", media);
```

### 6.4 Exemplo prático: maior número do array

```ts
function maiorNumero(valores: number[]): number {
  let maior = valores[0];

  for (const valor of valores) {
    if (valor > maior) {
      console.log("Novo maior encontrado:", valor);
      maior = valor;
    }
  }

  return maior;
}

console.log("Maior número:", maiorNumero([5, 18, 3, 42, 11]));
```

---

## 7) Tuplas

Tuplas são arrays com posições fixas e tipos definidos para cada posição.

```ts
let pessoa: [string, number] = ["Lucas", 30];

console.log("Nome:", pessoa[0]);
console.log("Idade:", pessoa[1]);
```

### Exemplo prático

```ts
let produto: [number, string, number] = [1, "Teclado", 199.9];

console.log("ID:", produto[0]);
console.log("Nome do produto:", produto[1]);
console.log("Preço:", produto[2]);
```

---

## 8) Objetos tipados

```ts
type Usuario = {
  nome: string;
  idade: number;
  ativo: boolean;
};

const usuario: Usuario = {
  nome: "Fernanda",
  idade: 27,
  ativo: true,
};

console.log("Usuário:", usuario);
console.log("Nome do usuário:", usuario.nome);
```

### 8.1 Propriedades opcionais

```ts
type Endereco = {
  rua: string;
  numero: number;
  complemento?: string; // aqui o complemento é opcional
};

const endereco1: Endereco = {
  rua: "Rua A",
  numero: 100,
};

const endereco2: Endereco = {
  rua: "Rua B",
  numero: 250,
  complemento: "Apartamento 12",
};

console.log("Endereço 1:", endereco1);
console.log("Endereço 2:", endereco2);
```

---

## 9) Interfaces

Interfaces também descrevem estruturas de objetos.

```ts
interface Produto {
  id: number;
  nome: string;
  preco: number;
  emEstoque: boolean;
}

const teclado: Produto = {
  id: 1,
  nome: "Teclado Mecânico",
  preco: 250,
  emEstoque: true,
};

console.log("Produto:", teclado);
```

### 9.1 Exemplo prático: total do carrinho

```ts
interface ItemCarrinho {
  nome: string;
  preco: number;
  quantidade: number;
}

function calcularTotalCarrinho(itens: ItemCarrinho[]): number {
  let total = 0;

  for (const item of itens) {
    const subtotal = item.preco * item.quantidade;
    console.log(
      "Item:",
      item.nome,
      "| Quantidade:",
      item.quantidade,
      "| Subtotal:",
      subtotal,
    );

    total += subtotal;
  }

  return total;
}

const carrinho: ItemCarrinho[] = [
  { nome: "Notebook", preco: 3500, quantidade: 1 },
  { nome: "Mouse", preco: 120, quantidade: 2 },
];

console.log("Total do carrinho:", calcularTotalCarrinho(carrinho));
```

---

## 10) Funções tipadas

### 10.1 Função simples

Uma função simples é um bloco de código reutilizável, com uma entrada, processamento e saída definidos claramente, ajudando a organizar melhor seu código e facilitar tarefas repetitivas.

```ts
function somar(a: number, b: number): number {
  return a + b;
}

console.log("Resultado da soma:", somar(5, 7));
```

### 10.2 Função com valor padrão

Uma função com valor padrão permite definir valores padrão para seus argumentos, tornando o uso da função mais flexível e prática, especialmente quando alguns argumentos costumam ter o mesmo valor na maioria das chamadas.

```ts
function aplicarDesconto(preco: number, desconto: number = 0.1): number {
  return preco - preco * desconto;
}

console.log("Valor com desconto padrão:", aplicarDesconto(200));
console.log("Valor com desconto personalizado:", aplicarDesconto(200, 0.25));
```

### 10.3 Arrow function

Uma Arrow Function é uma forma moderna e compacta de criar funções, facilitando o código em situações onde funções pequenas e rápidas são necessárias.

```ts
const dividir = (a: number, b: number): number => {
  return a / b;
};

console.log("Divisão:", dividir(10, 2));
```

### 10.4 Rest parameters

O Rest Parameters permite criar funções que aceitam um número variável de argumentos, agrupando-os em um array para fácil manipulação.

```ts
function somarVarios(...valores: number[]): number {
  let total = 0;

  for (const valor of valores) {
    total += valor;
  }

  return total;
}

console.log("Soma total:", somarVarios(1, 2, 3, 4, 5));
```

### 10.5 Exemplo prático: IMC

```ts
type Pessoa = {
  nome: string;
  alturaM: number;
  pesoKg: number;
};

function calcularIMC(p: Pessoa): number {
  return p.pesoKg / (p.alturaM * p.alturaM);
}

const pessoaExemplo: Pessoa = { nome: "João", alturaM: 1.75, pesoKg: 72 };
console.log("Pessoa analisada:", pessoaExemplo.nome);
console.log("IMC calculado:", calcularIMC(pessoaExemplo));
```

---

## 11) Union types

Union types permitem mais de um tipo possível.

```ts
let codigo: string | number;

codigo = 123;
console.log("Código numérico:", codigo);

codigo = "ABC-999";
console.log("Código textual:", codigo);
```

### 11.1 Exemplo prático

```ts
function exibirId(id: number | string): void {
  console.log("ID recebido:", id);
}

exibirId(10);
exibirId("USR-20");
```

---

## 12) Narrowing

Narrowing é o processo de reduzir um tipo mais amplo para um tipo mais específico com base em verificações.

### 12.1 Usando `typeof`

```ts
function formatarValor(valor: string | number): string {
  console.log("Valor recebido:", valor);

  if (typeof valor === "string") {
    console.log("O TypeScript entendeu que é string.");
    return valor.toUpperCase();
  }

  console.log("O TypeScript entendeu que é number.");
  return valor.toFixed(2);
}

console.log(formatarValor("typescript"));
console.log(formatarValor(19.456));
```

### 12.2 Usando `in`

```ts
type PessoaFisica = {
  nome: string;
  cpf: string;
};

type PessoaJuridica = {
  razaoSocial: string;
  cnpj: string;
};

function exibirDocumento(pessoa: PessoaFisica | PessoaJuridica): void {
  if ("cpf" in pessoa) {
    console.log("Pessoa física identificada:", pessoa.nome);
    console.log("CPF:", pessoa.cpf);
  } else {
    console.log("Pessoa jurídica identificada:", pessoa.razaoSocial);
    console.log("CNPJ:", pessoa.cnpj);
  }
}

exibirDocumento({ nome: "Maria", cpf: "123.456.789-00" });
exibirDocumento({ razaoSocial: "Empresa X", cnpj: "12.345.678/0001-99" });
```

### 12.3 Usando `instanceof`

```ts
class Boleto {
  pagar(): void {
    console.log("Pagamento realizado com boleto.");
  }
}

class Cartao {
  pagar(): void {
    console.log("Pagamento realizado com cartão.");
  }
}

function processarPagamento(metodo: Boleto | Cartao): void {
  if (metodo instanceof Boleto) {
    console.log("Método identificado: boleto");
  } else {
    console.log("Método identificado: cartão");
  }

  metodo.pagar();
}

processarPagamento(new Boleto());
processarPagamento(new Cartao());
```

---

## 13) Estruturas de controle

As estruturas de controle determinam o fluxo do programa.

### 13.1 `if`, `else if` e `else`

```ts
const notaFinal: number = 7.5;

if (notaFinal >= 9) {
  console.log("Excelente resultado.");
} else if (notaFinal >= 7) {
  console.log("Aluno aprovado.");
} else {
  console.log("Aluno reprovado.");
}
```

### 13.2 `for`

```ts
for (let i = 1; i <= 5; i++) {
  console.log("Contador do for:", i);
}
```

### 13.3 `while`

```ts
let contadorWhile = 1;

while (contadorWhile <= 3) {
  console.log("Executando while:", contadorWhile);
  contadorWhile++;
}
```

### 13.4 `do...while`

```ts
let contadorDoWhile = 1;

do {
  console.log("Executando do...while:", contadorDoWhile);
  contadorDoWhile++;
} while (contadorDoWhile <= 3);
```

### 13.5 `switch`

```ts
const perfil: string = "admin";

switch (perfil) {
  case "admin":
    console.log("Acesso total liberado.");
    break;
  case "editor":
    console.log("Acesso de edição liberado.");
    break;
  case "leitor":
    console.log("Acesso somente leitura liberado.");
    break;
  default:
    console.log("Perfil não reconhecido.");
}
```

### 13.6 `for...of`

Usado para percorrer os valores de iteráveis, como arrays.

```ts
const linguagens: string[] = ["TypeScript", "JavaScript", "Python"];

for (const linguagem of linguagens) {
  console.log("Linguagem encontrada:", linguagem);
}
```

### 13.7 `for...in`

Usado para percorrer as chaves de um objeto.

```ts
const aluno = {
  nome: "Paula",
  idade: 22,
  curso: "Sistemas de Informação",
};

for (const chave in aluno) {
  console.log("Chave:", chave, "| Valor:", aluno[chave as keyof typeof aluno]);
}
```

### 13.8 Exemplo prático combinando estruturas

```ts
const valores: number[] = [4, 7, 10, 3, 8];
let aprovados = 0;

for (const valor of valores) {
  if (valor >= 7) {
    aprovados++;
    console.log("Valor aprovado:", valor);
  } else {
    console.log("Valor abaixo da média:", valor);
  }
}

console.log("Quantidade de aprovados:", aprovados);
```

---

## 14) Enum

Enums permitem nomear conjuntos de valores constantes.

```ts
enum StatusPedido {
  Pendente,
  Processando,
  Enviado,
  Entregue,
}

const statusAtual: StatusPedido = StatusPedido.Processando;
console.log("Status atual:", statusAtual);
console.log("Nome do status:", StatusPedido[statusAtual]);
```

### Exemplo prático com enum textual

```ts
enum NivelAcesso {
  ADMIN = "ADMIN",
  USER = "USER",
  GUEST = "GUEST",
}

function mostrarPermissao(nivel: NivelAcesso): void {
  console.log("Nível recebido:", nivel);
}

mostrarPermissao(NivelAcesso.ADMIN);
```

---

## 15) Classes

Classes são moldes para criar objetos.

```ts
class ContaBancaria {
  titular: string;
  saldo: number;

  constructor(titular: string, saldoInicial: number) {
    this.titular = titular;
    this.saldo = saldoInicial;
  }

  depositar(valor: number): void {
    this.saldo += valor;
    console.log(`Depósito de ${valor} realizado. Saldo atual: ${this.saldo}`);
  }

  sacar(valor: number): void {
    if (valor > this.saldo) {
      console.log("Saldo insuficiente para saque.");
      return;
    }

    this.saldo -= valor;
    console.log(`Saque de ${valor} realizado. Saldo atual: ${this.saldo}`);
  }
}

const conta = new ContaBancaria("Carlos", 1000);
conta.depositar(250);
conta.sacar(300);
conta.sacar(2000);
```

### 15.1 Modificadores de acesso

Modificadores de acesso em classes definem a visibilidade e o acesso aos membros (atributos e métodos) de uma classe em TypeScript. Eles controlam quem pode acessar ou modificar esses membros, ajudando a garantir encapsulamento e segurança no código.

- `public`: padrão, acessível de qualquer lugar.

- `private`: acessível apenas dentro da própria classe.

- `protected`: acessível na classe e em subclasses.

```ts
class ProdutoEstoque {
  public nome: string;
  private quantidade: number;

  constructor(nome: string, quantidade: number) {
    this.nome = nome;
    this.quantidade = quantidade;
  }

  public adicionar(quantidade: number): void {
    this.quantidade += quantidade;
    console.log(`Estoque atualizado de ${this.nome}: ${this.quantidade}`);
  }

  public exibirQuantidade(): void {
    console.log(`Quantidade em estoque de ${this.nome}: ${this.quantidade}`);
  }
}

const itemEstoque = new ProdutoEstoque("Monitor", 10);
itemEstoque.adicionar(5);
itemEstoque.exibirQuantidade();
```

---

## 16) Herança e polimorfismo

Herança permite que uma classe reaproveite características de outra.
Polimorfismo permite comportamentos diferentes usando a mesma base.

```ts
class Animal {
  nome: string;

  constructor(nome: string) {
    this.nome = nome;
  }

  emitirSom(): void {
    console.log(`${this.nome} fez um som.`);
  }
}

class Cachorro extends Animal {
  emitirSom(): void {
    console.log(`${this.nome} latiu: au au!`);
  }
}

class Gato extends Animal {
  emitirSom(): void {
    console.log(`${this.nome} miou: miau!`);
  }
}

function fazerAnimalEmitirSom(animal: Animal): void {
  animal.emitirSom();
}

fazerAnimalEmitirSom(new Cachorro("Rex"));
fazerAnimalEmitirSom(new Gato("Mimi"));
```

---

## 17) Generics

Generics permitem criar código reutilizável sem perder tipagem.

### 17.1 Função genérica

```ts
function retornarValor<T>(valor: T): T {
  console.log("Valor recebido:", valor);
  return valor;
}

console.log(retornarValor<string>("Olá"));
console.log(retornarValor<number>(123));
```

### 17.2 Array genérico

```ts
function primeiroElemento<T>(lista: T[]): T {
  return lista[0];
}

console.log("Primeiro nome:", primeiroElemento(["Ana", "Bruno", "Caio"]));
console.log("Primeiro número:", primeiroElemento([10, 20, 30]));
```

### 17.3 Interface genérica

```ts
interface Caixa<T> {
  conteudo: T;
}

const caixaTexto: Caixa<string> = { conteudo: "Material didático" };
const caixaNumero: Caixa<number> = { conteudo: 2026 };

console.log("Caixa texto:", caixaTexto);
console.log("Caixa número:", caixaNumero);
```

---

## 18) Literal types

Literal types limitam um valor a opções específicas.

```ts
type Direcao = "esquerda" | "direita" | "frente" | "tras";

function mover(direcao: Direcao): void {
  console.log("Movendo para:", direcao);
}

mover("frente");
mover("direita");
// mover("cima");
// Erro: valor não permitido.
```

### Exemplo com status

```ts
type Status = "sucesso" | "erro" | "carregando";

function mostrarStatus(status: Status): void {
  console.log("Status atual:", status);
}

mostrarStatus("carregando");
mostrarStatus("sucesso");
```

---

## 19) Interseção vs união

### 19.1 União (`|`)

Na união, o valor pode ser um tipo ou outro.

```ts
type Codigo = string | number;

let codigoPedido: Codigo = 1001;
console.log("Código pedido:", codigoPedido);

codigoPedido = "PED-1001";
console.log("Código pedido textual:", codigoPedido);
```

### 19.2 Interseção (`&`)

Na interseção, o valor precisa atender aos dois tipos ao mesmo tempo.

```ts
type PessoaBase = {
  nome: string;
};

type FuncionarioBase = {
  cargo: string;
};

type PessoaFuncionaria = PessoaBase & FuncionarioBase;

const colaborador: PessoaFuncionaria = {
  nome: "Luciana",
  cargo: "Desenvolvedora",
};

console.log("Colaborador:", colaborador);
```

### Diferença prática

- **União:** aceita uma alternativa ou outra.
- **Interseção:** exige a combinação de características.

---

## 20) Módulos

Módulos ajudam a organizar o código em arquivos separados.

### 20.1 Exportando

Arquivo `matematica.ts`:

```ts
export function somar(a: number, b: number): number {
  return a + b;
}

export function multiplicar(a: number, b: number): number {
  return a * b;
}
```

### 20.2 Importando

Arquivo `app.ts`:

```ts
import { somar, multiplicar } from "./matematica";

console.log("Soma importada:", somar(2, 3));
console.log("Multiplicação importada:", multiplicar(4, 5));
```

### Vantagens dos módulos

- melhor organização;
- separação de responsabilidades;
- reutilização de código;
- manutenção mais simples.

---

## 21) Exemplos completos de algoritmos reais

### 21.1 Verificar se um número é par ou ímpar

```ts
function verificarParOuImpar(numero: number): string {
  if (numero % 2 === 0) {
    return "par";
  }

  return "ímpar";
}

console.log("10 é", verificarParOuImpar(10));
console.log("7 é", verificarParOuImpar(7));
```

### 21.2 Contar vogais em uma palavra

```ts
function contarVogais(texto: string): number {
  const vogais = "aeiouAEIOU";
  let contador = 0;

  for (const letra of texto) {
    if (vogais.includes(letra)) {
      contador++;
      console.log("Vogal encontrada:", letra);
    }
  }

  return contador;
}

console.log("Quantidade de vogais:", contarVogais("TypeScript"));
```

### 21.3 Calcular fatorial

```ts
function fatorial(numero: number): number {
  if (numero < 0) {
    throw new Error("Fatorial não é definido para números negativos.");
  }

  let resultado = 1;

  for (let i = 2; i <= numero; i++) {
    resultado *= i;
    console.log("Resultado parcial do fatorial:", resultado);
  }

  return resultado;
}

console.log("Fatorial de 5:", fatorial(5));
```

### 21.4 Encontrar o maior valor de uma lista

```ts
function encontrarMaior(lista: number[]): number {
  if (lista.length === 0) {
    throw new Error("A lista não pode ser vazia.");
  }

  let maior = lista[0];

  for (const numero of lista) {
    if (numero > maior) {
      maior = numero;
      console.log("Novo maior número identificado:", maior);
    }
  }

  return maior;
}

console.log("Maior valor:", encontrarMaior([12, 99, 3, 45, 67]));
```

### 21.5 Simulação simples de cadastro

```ts
type CadastroUsuario = {
  nome: string;
  email: string;
  idade: number;
};

function cadastrarUsuario(usuario: CadastroUsuario): void {
  if (!usuario.nome || !usuario.email) {
    throw new Error("Nome e email são obrigatórios.");
  }

  if (usuario.idade < 18) {
    console.log("Usuário menor de idade. Cadastro com restrições.");
  } else {
    console.log("Usuário maior de idade. Cadastro liberado.");
  }

  console.log("Usuário cadastrado:", usuario);
}

cadastrarUsuario({
  nome: "Aline",
  email: "aline@email.com",
  idade: 21,
});
```

---

## 22) Resumo final

Ao longo deste material, você viu que o TypeScript:

- adiciona tipagem estática ao JavaScript;
- melhora a previsibilidade do código;
- ajuda a detectar erros antes da execução;
- facilita leitura, manutenção e refatoração;
- trabalha muito bem com funções, objetos, classes, módulos e validações;
- não substitui a necessidade de validação em runtime.

Em outras palavras, TypeScript ajuda você a escrever código mais seguro e profissional.

---

## 23) Próximos passos

Depois de dominar esta introdução, vale estudar:

1. **Type aliases** com mais profundidade.
2. **Interfaces avançadas**.
3. **Generics avançados**.
4. **Utility Types** (`Partial`, `Pick`, `Omit`, `Readonly`).
5. **Manipulação de tipos**.
6. **Integração com Node.js**.
7. **Integração com React + TypeScript**.
8. **Validação com bibliotecas** como Zod ou Yup.
9. **Arquitetura e organização de projetos TypeScript**.

### Sugestão prática de estudo

Uma boa sequência de treino é:

- criar pequenos arquivos `.ts` para cada conceito;
- compilar com `npx tsc`;
- observar os erros de tipagem;
- testar os exemplos com `console.log`;
- depois juntar tudo em pequenos projetos.
