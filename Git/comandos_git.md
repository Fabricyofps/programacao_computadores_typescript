# Git e GitHub - Guia Prático de Comandos Essenciais

> **Objetivo:** Este documento apresenta os principais comandos do Git utilizados no dia a dia de desenvolvimento de software utilizando GitHub.
>
> Os exemplos utilizam um projeto **Node.js/TypeScript**, mas os comandos são os mesmos para qualquer linguagem.

---

# O que é Git?

O **Git** é um Sistema de Controle de Versão (Version Control System - VCS).

Ele permite:

- controlar todas as alterações do projeto;
- trabalhar em equipe sem sobrescrever o trabalho de outras pessoas;
- recuperar versões antigas do código;
- criar funcionalidades isoladas através de branches;
- revisar código utilizando Pull Requests.

---

# Conceitos importantes

Antes de aprender os comandos, é importante entender alguns conceitos.

## Repositório (Repository)

É a pasta do projeto controlada pelo Git.

Pode ser:

- **Local:** existe apenas no computador do desenvolvedor.
- **Remoto:** hospedado em serviços como GitHub, GitLab ou Bitbucket.

---

## Commit

Um **commit** representa um ponto na história do projeto.

Cada commit deve representar uma alteração lógica, por exemplo:

- corrigir um bug;
- adicionar uma funcionalidade;
- alterar documentação.

Evite commits muito grandes ou com várias mudanças diferentes.

---

## Branch

Uma **branch** é uma linha de desenvolvimento independente.

Exemplo:

```
main
 │
 ├───────────────► Produção
 │
 └──── feature/login
             │
             └── desenvolvimento
```

Isso permite desenvolver novas funcionalidades sem alterar a versão principal do projeto.

---

## Merge

O **merge** une duas branches.

Exemplo:

```
main
 │
 ├───────────────┐
 │               │
 └── feature ----┘
```

---

## Pull Request (PR)

É uma solicitação para que uma branch seja integrada à outra.

No GitHub, o Pull Request permite:

- revisão de código;
- comentários;
- aprovação;
- execução automática de testes (GitHub Actions).

---

# Fluxo de trabalho recomendado

```
Clonar repositório

↓

Criar branch

↓

Desenvolver

↓

git add

↓

git commit

↓

git push

↓

Pull Request

↓

Code Review

↓

Merge na main
```

---

# Parte A — Configuração inicial

## Configurar nome do usuário

```bash
git config --global user.name "Seu Nome"
```

Verificar configuração:

```bash
git config user.name
```

---

## Configurar e-mail

```bash
git config --global user.email "email@empresa.com"
```

Verificar:

```bash
git config user.email
```

---

## Ver todas as configurações

```bash
git config --list
```

---

# Parte B — Clonar um repositório

Baixa uma cópia completa do projeto.

```bash
git clone https://github.com/USUARIO/NOME-REPOSITORIO.git
```

Entrar na pasta:

```bash
cd NOME-REPOSITORIO
```

---

# Parte C — Consultando informações

## Ver status

Mostra:

- arquivos modificados;
- arquivos novos;
- arquivos preparados para commit.

```bash
git status
```

Exemplo:

```
modified: app.ts

Untracked files:
README.md
```

---

## Ver histórico

```bash
git log
```

Versão resumida:

```bash
git log --oneline
```

Mais utilizada:

```bash
git log --oneline --decorate --graph --all
```

Exemplo:

```
* 5c8ad91 feat: adicionar login
* 4fd7821 fix: corrigir validação
* 12a93fd docs: atualizar README
```

---

## Ver diferenças

Comparar alterações ainda não adicionadas:

```bash
git diff
```

Comparar alterações já adicionadas ao staging:

```bash
git diff --staged
```

---

# Parte D — Trabalhando com branches

## Ver branches

```bash
git branch
```

---

## Criar uma nova branch

```bash
git branch feature/calcular-frete
```

---

## Criar e trocar para a branch

Mais utilizado:

```bash
git checkout -b feature/calcular-frete
```

ou (Git moderno)

```bash
git switch -c feature/calcular-frete
```

---

## Trocar de branch

```bash
git checkout main
```

ou

```bash
git switch main
```

---

## Excluir uma branch

Após realizar o merge:

```bash
git branch -d feature/calcular-frete
```

Forçar remoção:

```bash
git branch -D feature/calcular-frete
```

---

# Parte E — Preparando alterações

Adicionar um arquivo:

```bash
git add app.ts
```

Adicionar vários:

```bash
git add arquivo1 arquivo2
```

Adicionar tudo:

```bash
git add .
```

Verificar:

```bash
git status
```

---

# Parte F — Criando commits

Criar commit:

```bash
git commit -m "feat: adicionar cálculo de frete"
```

---

## Convenção de commits (Conventional Commits)

| Tipo     | Descrição               |
| -------- | ----------------------- |
| feat     | Nova funcionalidade     |
| fix      | Correção de erro        |
| docs     | Documentação            |
| style    | Formatação              |
| refactor | Refatoração             |
| test     | Testes                  |
| chore    | Tarefas internas        |
| perf     | Melhoria de desempenho  |
| build    | Alterações de build     |
| ci       | Alterações em pipelines |

Exemplos:

```text
feat: adicionar autenticação JWT

fix: corrigir cálculo do imposto

docs: atualizar README

test: adicionar testes unitários

refactor: simplificar serviço de pagamento
```

---

# Parte G — Enviando alterações para o GitHub

Primeiro envio:

```bash
git push -u origin feature/calcular-frete
```

Próximos envios:

```bash
git push
```

---

# Parte H — Atualizando o projeto

Atualizar a branch principal:

```bash
git checkout main

git pull origin main
```

Voltar para a branch da funcionalidade:

```bash
git checkout feature/calcular-frete
```

Trazer alterações da main:

```bash
git merge main
```

---

# Parte I — Resolvendo conflitos

Durante um merge, podem ocorrer conflitos.

Exemplo:

```
<<<<<<< HEAD

console.log("Minha alteração")

=======

console.log("Alteração do colega")

>>>>>>> main
```

Passos:

1. editar o arquivo;
2. remover os marcadores;
3. manter o código correto;
4. salvar;
5. adicionar novamente:

```bash
git add .
```

Finalizar:

```bash
git commit
```

---

# Parte J — Pull Request

No GitHub:

1. acessar o repositório;
2. clicar em **Compare & Pull Request**;
3. escolher:

```
base: main

compare: feature/calcular-frete
```

4. revisar alterações;
5. escrever título e descrição;
6. clicar em **Create Pull Request**.

Após aprovação:

- Merge Pull Request
- Delete Branch

---

# Parte K — Comandos úteis

Atualizar repositório:

```bash
git pull
```

Buscar alterações sem aplicar:

```bash
git fetch
```

Ver repositórios remotos:

```bash
git remote -v
```

Ver histórico resumido:

```bash
git log --oneline
```

Desfazer alterações locais (não commitadas):

```bash
git restore arquivo.ts
```

Remover arquivo do staging:

```bash
git restore --staged arquivo.ts
```

Cancelar o último commit mantendo as alterações:

```bash
git reset --soft HEAD~1
```

Cancelar completamente o último commit:

```bash
git reset --hard HEAD~1
```

> **Atenção:** `git reset --hard` remove alterações locais de forma permanente.

---

# Parte L — Arquivo .gitignore

O `.gitignore` define arquivos que não devem ser versionados.

Exemplo para Node.js:

```text
node_modules/

dist/

coverage/

.env

.env.local

*.log
```

---

# Parte M — Fluxo completo do dia a dia

```bash
# Atualizar projeto
git checkout main
git pull origin main

# Criar nova funcionalidade
git checkout -b feature/login

# Desenvolver...

git status

git diff

git add .

git commit -m "feat: adicionar tela de login"

git push -u origin feature/login

# Abrir Pull Request no GitHub

# Após aprovação
git checkout main
git pull
git branch -d feature/login
```

---

# Boas práticas

- Faça commits pequenos e frequentes.
- Utilize mensagens de commit claras.
- Sempre atualize a `main` antes de iniciar uma nova funcionalidade.
- Evite trabalhar diretamente na `main`.
- Revise o código antes de abrir um Pull Request.
- Nunca envie senhas, tokens ou arquivos `.env` para o repositório.
- Utilize `.gitignore` para arquivos gerados automaticamente.
- Antes de executar `git push`, confira as alterações com `git status` e `git diff`.

---

# Resumo dos principais comandos

| Comando                             | Função                                 |
| ----------------------------------- | -------------------------------------- |
| `git clone`                         | Clona um repositório                   |
| `git status`                        | Mostra o estado atual                  |
| `git log`                           | Exibe o histórico                      |
| `git diff`                          | Mostra diferenças                      |
| `git branch`                        | Lista branches                         |
| `git switch -c` / `git checkout -b` | Cria uma nova branch                   |
| `git switch` / `git checkout`       | Troca de branch                        |
| `git add`                           | Adiciona arquivos ao staging           |
| `git commit`                        | Registra alterações                    |
| `git push`                          | Envia alterações ao repositório remoto |
| `git pull`                          | Atualiza a branch local                |
| `git fetch`                         | Busca alterações sem aplicá-las        |
| `git merge`                         | Une branches                           |
| `git restore`                       | Descarta alterações locais             |
| `git reset`                         | Desfaz commits                         |
| `git remote -v`                     | Mostra os repositórios remotos         |

---

# Conclusão

O Git é uma ferramenta essencial para o desenvolvimento moderno de software. Dominar os comandos apresentados neste guia permite trabalhar de forma organizada, colaborar com outras pessoas, manter o histórico do projeto e integrar alterações com segurança por meio de Pull Requests e pipelines de integração contínua, como o GitHub Actions.
