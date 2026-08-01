# Guia Completo do GitHub (do básico ao prático)

Este guia explica o **GitHub** de forma simples e prática, com foco em:

- o que é
- principais funcionalidades
- como usar no dia a dia (com exemplos)

> **Pré-requisito mental:** GitHub usa **Git** para versionar arquivos. Aqui vou focar no **GitHub**, mas os conceitos de “commit/branch” aparecem porque são a base do uso.

---

## 1) O que é GitHub?

O **GitHub** é uma plataforma web para:

- armazenar repositórios de código (**repos**)
- controlar versões com **Git**
- colaborar com outras pessoas (pull requests, issues)
- automatizar fluxos com **GitHub Actions**

### Mundo real

Pense como uma “pasta de código” online, onde você:

- guarda versões do trabalho
- permite que várias pessoas mexam com controle e histórico
- revisa mudanças antes de aceitar no projeto

---

## 2) Termos essenciais (para não se perder)

- **Repository (Repositório):** onde seu código fica.
- **Commit:** “foto” do estado do código (com mensagem).
- **Branch (Ramo):** linha alternativa de desenvolvimento.
- **Pull Request (PR):** proposta de mudanças para entrar em uma branch.
- **Issue:** registro de tarefa/problema/discussão.
- **Fork:** cópia de um repositório para você contribuir.

---

## 3) Principais funcionalidades do GitHub (com uso prático)

## 3.1 Repositórios (Repositories)

### O que você faz ali

- hospeda o código
- define colaboração (quem pode acessar)
- organiza projetos e documentação

### Como usar (passo a passo)

1. Clique em **New repository**
2. Dê um nome ao repo
3. Escolha visibilidade (**Public/Private**)
4. (Opcional) marque **Add README**
5. Crie o repositório

### Quando usar

- sempre que você começar um projeto que terá evolução

---

## 3.2 README (documentação do projeto)

### O que é

Arquivo `README.md` com:

- objetivo do projeto
- como rodar
- como contribuir

### Exemplo de README (estrutura mínima)

```md
# Meu Projeto

## Como rodar

1. Instale dependências
2. Configure variáveis
3. Rode o servidor

## Como contribuir

- Abra uma issue
- Crie uma branch
- Envie um pull request
```

> No GitHub, o README aparece como “cartão de visita” do repositório.

---

## 3.3 Issues

### O que são

Um lugar para:

- registrar bugs
- pedir melhorias
- planejar tarefas

### Como usar de forma prática

- Use título claro: `Erro ao calcular frete quando peso negativo`
- Descreva:
  - contexto
  - passos para reproduzir
  - resultado esperado x obtido

### Organização extra

- use **labels** (ex.: bug, enhancement, question)
- use **milestones** (prazo/versão)

---

## 3.4 Pull Requests (PRs) — o coração da colaboração

### O que é

Um PR é:

- uma branch com alterações
- pedindo para alguém revisar e **mesclar** (merge)

### Fluxo recomendado

1. Criar branch para a mudança
2. Comitar mudanças com mensagem clara
3. Abrir PR para `main` (ou branch principal)
4. Revisar código (review)
5. Corrigir feedback
6. Merge

### Checklist prático de um PR bom

- título: “Adicionar cálculo de frete por região”
- descrição: o que mudou e por quê
- links: issue relacionada
- evidências: prints/testes

---

## 3.5 Branches e estratégia de trabalho

### Estratégias simples (para iniciantes)

- **main:** sempre estável
- **feature/\*:** novas funcionalidades
- **fix/\*:** correções

Exemplos:

- `feature/calcular-frete`
- `fix/erro-validacao-peso`

---

## 3.6 Actions (Automação)

### O que são

**GitHub Actions** executa tarefas automaticamente, por exemplo:

- rodar testes a cada PR
- compilar código
- checar lint
- publicar artefatos

### Exemplo de fluxo (conceitual)

- Evento: “Pull request aberta”
- Ação: rodar `npm test`
- Resultado: status aparece no PR

> Você define tudo via arquivo `.github/workflows/xxx.yml`.

---

## 3.7 Releases

### O que são

Versões “oficiais” do software (ex.: v1.0.0).

### Quando usar

- quando você solta uma versão estável
- ao final de uma série de PRs

---

## 3.8 Projects (Quadros)

### O que é

Organiza trabalho com:

- colunas (To do / Doing / Done)
- cards vinculados a issues e PRs

### Uso prático

Para turmas/projetos pequenos, você pode:

- ter um card por tarefa
- mover status conforme progresso

---

## 3.9 Wiki

### O que é

Documentação em formato de páginas.

### Uso prático

- guia do projeto
- regras de contribuição
- documentação de APIs

---

## 3.10 Insights (Métricas)

### O que você encontra

- commits
- frequência de PRs
- contribuições

Útil para:

- entender ritmo do time
- avaliar saúde do repositório

---

## 3.11 Segurança e permissões (dependendo do plano)

### O que é importante

- controle de acesso (privado/public)
- branch protection
- auditoria e alertas

> Se você trabalha em projetos reais, branch protection + revisão por PR é um “básico forte”.

---

## 4) Como usar o GitHub de forma prática (roteiro simplificado)

### Cenário: você vai desenvolver uma funcionalidade

1. Crie uma branch
2. Faça alterações no código
3. Faça commits locais com mensagens claras
4. Envie para o GitHub
5. Abra um Pull Request
6. Peça revisão (ou revise você mesmo se for solo)
7. Corrija feedback e atualize a branch
8. Merge após validação

### Exemplo real de mensagem de commit

- `feat: add frete regional calculation`
- `fix: validate pesoKg before computing`
- `docs: update README with run instructions`

---

## 5) Guia de “boas práticas” (muito importante)

### 5.1 Use mensagens claras

- `feat:` (nova funcionalidade)
- `fix:` (correção)
- `docs:` (documentação)
- `refactor:` (refatoração sem mudar comportamento)

### 5.2 PR pequeno é melhor

- PR muito grande dificulta revisão

### 5.3 Descreva o problema em Issues

- isso acelera o entendimento da equipe

### 5.4 Proteja a branch principal

- ex.: exigir PR e testes passando

---

## 6) Mini-template de Pull Request

Copie e use:

```md
## O que foi feito

- ...

## Por que foi necessário

- ...

## Como testar

- Passo 1
- Passo 2

## Checklist

- [ ] Código compila
- [ ] Testes executados
- [ ] Documentação atualizada (se necessário)
```

---

## 7) Atividade sugerida

1. Criar repositório com README
2. Abrir 2 issues:
   - uma tarefa
   - um bug fictício
3. Criar 2 branches:
   - `feature/...`
   - `fix/...`
4. Abrir PRs separadas
5. Atualizar o README com “como rodar”
6. Verificar status do PR (simular via Actions se quiser)
