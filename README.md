# MeuDinheiro

Aplicação web simples de controle financeiro pessoal, desenvolvida como projeto da disciplina **Gerência de Configuração e Manutenção de Software**.

## Objetivo

Permitir o registro de receitas e despesas e apresentar o total de entradas, o total de saídas e o saldo financeiro.

## Escopo definido pela equipe

- Cadastro de receitas com descrição e valor.
- Cadastro de despesas com descrição e valor.
- Cálculo de receitas, despesas e saldo.
- Histórico de movimentações.
- Persistência dos registros no `localStorage` do navegador.

**Fora do escopo:** login, backend, banco de dados, categorias, filtros, edição e exclusão de lançamentos. Os dados ficam armazenados somente no navegador utilizado.

## Tecnologias

- HTML, CSS e JavaScript: interface e funcionalidades.
- Node.js com `node:test`: execução dos testes automatizados.
- `localStorage`: armazenamento local dos lançamentos.
- Git e GitHub: versionamento e colaboração.
- GitHub Actions: automação de testes e publicação.
- GitHub Pages: hospedagem da aplicação.

## Como executar

Não há dependências externas nem etapa de instalação nesta versão.

1. Baixe ou clone o repositório.
2. Abra o arquivo `src/index.html` em um navegador.

Opcionalmente, para executar por um servidor local com Python:

```bash
python -m http.server 8000 --directory src
```

Depois, acesse `http://localhost:8000`.

### Requisito para executar os testes

As regras financeiras usam módulos JavaScript (ES Modules). Para executar os testes automatizados com `node:test`, instale uma versão LTS do [Node.js](https://nodejs.org/). Não é necessário instalar pacotes adicionais.

Os testes ficam em `tests/` e são executados na pasta do projeto com:

```bash
npm test
```

## Módulo financeiro — responsabilidade de Maurício

As regras de cadastro, validação e cálculo estão em `src/js/finance.js`. O módulo é independente da interface: ele recebe dados e devolve resultados, sem manipular elementos HTML ou acessar `localStorage`.

### Estrutura de um lançamento

Cada receita ou despesa é representada por um objeto com esta estrutura:

```js
{
  id: "identificador-unico",
  type: "income", // ou "expense"
  description: "Salário",
  amount: 2500.00
}
```

Os valores são armazenados como números positivos. O tipo indica se o valor será somado às receitas ou às despesas.

### Funções disponíveis

| Função | Finalidade |
|---|---|
| `createTransaction(type, description, amount)` | Cria e valida um lançamento. |
| `registerIncome(transactions, description, amount)` | Retorna uma nova lista com uma receita. |
| `registerExpense(transactions, description, amount)` | Retorna uma nova lista com uma despesa. |
| `calculateSummary(transactions)` | Calcula receitas, despesas, saldo e quantidade de lançamentos. |
| `validateDescription(description)` | Impede descrições vazias. |
| `validateAmount(amount)` | Impede valores vazios, inválidos, zero ou negativos. |

Exemplo de uso em outro módulo JavaScript:

```js
import {
  registerIncome,
  registerExpense,
  calculateSummary,
} from "./finance.js";

let transactions = [];
transactions = registerIncome(transactions, "Salário", 2500);
transactions = registerExpense(transactions, "Internet", 100);

const summary = calculateSummary(transactions);
console.log(summary);
// { totalIncome: 2500, totalExpenses: 100, balance: 2400,
//   transactionCount: 2 }
```

**Importante para a integração:** `registerIncome` e `registerExpense` não modificam a lista original. Elas retornam uma nova lista. Portanto, a interface deve guardar o retorno, por exemplo: `transactions = registerIncome(transactions, descricao, valor)`. A persistência no `localStorage` e a atualização da tela ficam a cargo da integração com `app.js`.

## Organização do projeto

```text
meu-dinheiro/
├── src/
│   ├── index.html
│   ├── css/
│   │   └── style.css
│   └── js/
│       ├── app.js
│       └── finance.js
├── tests/
│   └── .gitkeep
├── .github/
│   └── workflows/
│       └── ci-cd.yml
├── docs/
│   └── evidencias/
│       └── .gitkeep
├── package.json
├── README.md
└── .gitignore
```

## Responsabilidades da equipe

| Integrante | Responsabilidade |
|---|---|
| Mariana | Repositório, estrutura inicial, README, integração e relatório técnico |
| Maurício | Estrutura dos lançamentos, cadastro de receitas e despesas, cálculo e validações financeiras |
| Thiago | Interface HTML/CSS e interação com as funções financeiras |
| Sabrina | Testes automatizados com `node:test` e evidências de qualidade |
| Letícia | Workflow do GitHub Actions, CI/CD e publicação no GitHub Pages |

Cada integrante deverá trabalhar em sua própria branch, realizar commits claros e abrir um pull request para integração, conforme a organização definida pelo grupo.

## Testes, pipeline e publicação

### Testes automatizados (responsável: Sabrina)

Os testes usam `node:test` e `node:assert` (sem dependências externas) e estão em `tests/finance.test.js`. Eles cobrem:

- **Cadastro de receita:** lançamento válido, vírgula decimal, lista original preservada e rejeição de valor/descrição inválidos.
- **Cadastro de despesa:** lançamento válido e rejeição de descrição vazia e valor inválido.
- **Cálculo do saldo:** receitas menos despesas, lista vazia, saldo negativo e arredondamento de centavos.

Evidências de execução em `docs/evidencias/`:

| Arquivo | Conteúdo |
|---|---|
| `01-testes-passando.txt` | Execução inicial: 7 testes, todos passando. |
| `02-testes-falhando-simulacao.txt` | Simulação de falha (saldo calculado com `+` no lugar de `-`): 2 testes falham. |
| `03-testes-passando-apos-correcao.txt` | Após a correção: 7 testes passando novamente. |

### Pipeline de CI/CD (responsável: Letícia)

O pipeline está em `.github/workflows/ci-cd.yml` (GitHub Actions) e tem três jobs encadeados:

| Job | Quando roda | O que faz |
|---|---|---|
| `testes` | Todo pull request para a `main` e todo push na `main` | Instala o Node.js 22 e executa `npm test`. |
| `build` | Só se `testes` passar (`needs: testes`) | Empacota a pasta `src/` como artefato do GitHub Pages. |
| `deploy` | Só em push na `main` e só se `build` passar | Publica o artefato no GitHub Pages. |

**Bloqueio da entrega:** se qualquer teste falhar, o job `testes` termina com erro e os jobs `build` e `deploy` são pulados (*skipped*). Nada é publicado e a versão anterior continua no ar. Em pull requests, a falha também marca o PR com ❌; com a regra de proteção da `main` exigindo o check **Testes automatizados**, o merge fica bloqueado até a correção.

**Configuração necessária no GitHub (uma vez, por quem administra o repositório):**

1. *Settings → Pages → Build and deployment → Source:* **GitHub Actions**.
2. *Settings → Branches → Add branch ruleset/protection rule* para `main`: marcar **Require a pull request before merging** e **Require status checks to pass**, selecionando o check **Testes automatizados**.

O pipeline também pode ser executado manualmente em *Actions → CI/CD - testes e deploy → Run workflow*.

## Aplicação publicada

_Ainda não publicada. O link será inserido após a configuração do GitHub Pages._

## Integrantes

- Mariana
- Maurício
- Thiago
- Sabrina
- Letícia

## Limitações previstas

- Os dados ficam somente no `localStorage` do navegador utilizado.
- Não há sincronização entre dispositivos.
- Não há integração com bancos ou serviços financeiros.
- A aplicação tem finalidade acadêmica e de organização financeira básica.
