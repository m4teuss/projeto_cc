# projeto_cc — Automação de Testes com Cypress

Projeto de automação de testes end-to-end (E2E) utilizando o [Cypress](https://www.cypress.io/).

Repositório: https://github.com/m4teuss/projeto_cc

## Sobre o Cypress

O Cypress é uma ferramenta de testes front-end que roda diretamente no navegador, permitindo escrever e executar:

- **Testes E2E**: simulam o usuário navegando pela aplicação completa.
- **Testes de componente**: testam componentes isolados (React, Vue, Angular etc.).

Principais vantagens:

- Espera automática (*auto-retry*) por elementos e asserções, sem necessidade de `sleep`.
- *Time travel*: snapshots de cada comando no Test Runner para depuração.
- Controle de rede com `cy.intercept()` (stub e espera de requisições).
- Screenshots e vídeos automáticos na execução via linha de comando.

## Pré-requisitos

- [Node.js](https://nodejs.org/) (versão LTS recomendada)
- npm (instalado junto com o Node.js)
- Git

## Instalação

```bash
git clone https://github.com/m4teuss/projeto_cc.git
cd projeto_cc
npm init -y                       # caso ainda não exista package.json
npm install cypress --save-dev
```

## Executando os testes

| Comando | Descrição |
| --- | --- |
| `npx cypress open` | Abre o Cypress App (modo interativo) |
| `npx cypress open --e2e --browser chrome` | Abre direto nos testes E2E no Chrome |
| `npx cypress run` | Executa todos os testes em modo *headless* (ideal para CI) |
| `npx cypress run --spec "cypress/e2e/arquivo.cy.js"` | Executa um spec específico |
| `npx cypress run --browser chrome` | Executa em um navegador específico |

Sugestão de scripts no `package.json`:

```json
"scripts": {
  "cy:open": "cypress open",
  "cy:run": "cypress run"
}
```

## Estrutura do projeto

Estrutura padrão criada pelo Cypress na primeira execução:

```text
projeto_cc/
├── cypress/
│   ├── e2e/              # Arquivos de teste (*.cy.js)
│   ├── fixtures/         # Massa de dados estática (JSON)
│   └── support/
│       ├── commands.js   # Comandos customizados (Cypress.Commands.add)
│       └── e2e.js        # Carregado antes de cada spec E2E
├── cypress.config.js     # Configuração do Cypress
├── package.json
└── readme.md
```

## Configuração

Exemplo de `cypress.config.js`:

```js
const { defineConfig } = require('cypress')

module.exports = defineConfig({
  e2e: {
    baseUrl: 'http://localhost:3000',
  },
})
```

Com o `baseUrl` definido, use caminhos relativos: `cy.visit('/login')`.

## Exemplo de teste

```js
describe('Login', () => {
  beforeEach(() => {
    cy.visit('/login')
  })

  it('deve realizar login com sucesso', () => {
    cy.get('[data-cy=email]').type('usuario@teste.com')
    cy.get('[data-cy=senha]').type('123456')
    cy.get('[data-cy=entrar]').click()
    cy.url().should('include', '/home')
  })
})
```

## Boas práticas

- **Seletores**: prefira atributos dedicados como `data-cy` (`cy.get('[data-cy=submit]')`) em vez de `id`, `class` ou texto, que mudam com frequência.
- **Sem esperas fixas**: evite `cy.wait(5000)`. Use `cy.intercept()` + `cy.wait('@alias')` ou asserções — os comandos do Cypress já fazem retry.
- **Testes independentes**: cada teste deve passar sozinho (com `it.only`), sem depender do estado de outro teste.
- **Limpeza de estado** no `beforeEach`, não no `after`/`afterEach`.
- **Login programático**: faça login via `cy.request()` e use `cy.session()` para reaproveitar a sessão entre testes.
- **Comandos são assíncronos**: `const btn = cy.get('button')` não funciona; use `.then()`, `cy.wrap()` ou aliases (`.as()` / `cy.get('@alias')`).
- **Segredos fora do código**: use variáveis de ambiente `CYPRESS_*` no CI e nunca versione credenciais.
- **Teste apenas a aplicação que você controla**; para outros domínios use `cy.request()` ou `cy.origin()`.

## Documentação

- [Documentação oficial do Cypress](https://docs.cypress.io/)
- [Boas práticas](https://docs.cypress.io/app/core-concepts/best-practices)
- [Referência da API](https://docs.cypress.io/api/table-of-contents)
