
# MeuDinheiro

Aplicação web de controle financeiro pessoal desenvolvida para a atividade **Operação Deploy - Do código à produção**, da disciplina de **Gerência de Configuração e Manutenção de Software** da UTFPR, Campus Cornélio Procópio.

O projeto foi utilizado para exercitar desenvolvimento colaborativo, controle de versão, testes automatizados, integração contínua e publicação de uma aplicação estática.

**Aplicação publicada:** https://marianagpalacios.github.io/meu-dinheiro/  
**Repositório:** https://github.com/marianagpalacios/meu-dinheiro

## Funcionalidades

- Cadastro de receitas com descrição e valor.
- Cadastro de despesas com descrição e valor.
- Validação de descrições e valores informados.
- Cálculo automático dos totais de receitas, despesas e saldo.
- Histórico de movimentações, com os lançamentos mais recentes primeiro.
- Persistência dos lançamentos no `localStorage` do navegador.
- Interface responsiva para diferentes tamanhos de tela.

Os lançamentos ficam armazenados localmente no navegador utilizado. A aplicação não exige cadastro de usuário nem possui integração bancária.

## Tecnologias

| Tecnologia | Utilização |
|---|---|
| HTML5 | Estrutura da página. |
| CSS3 | Estilização e responsividade. |
| JavaScript (ES Modules) | Interface, validações e regras financeiras. |
| `localStorage` | Armazenamento das movimentações no navegador. |
| Node.js 22 | Ambiente utilizado pelo pipeline para executar os testes. |
| `node:test` e `node:assert/strict` | Testes automatizados sem dependências externas. |
| Git e GitHub | Versionamento, branches, commits e pull requests. |
| GitHub Actions | Execução automatizada de testes, preparação do artefato e deploy. |
| GitHub Pages | Hospedagem da aplicação estática. |

## Como executar a aplicação

### Versão publicada

Acesse diretamente:

https://marianagpalacios.github.io/meu-dinheiro/

### Execução local

Não é necessário instalar dependências para executar a interface. Para trabalhar com os módulos JavaScript no navegador, recomenda-se iniciar um servidor HTTP local.

1. Clone o repositório e entre na pasta:

   ```bash
   git clone https://github.com/marianagpalacios/meu-dinheiro.git
   cd meu-dinheiro
   ```

2. No Windows, com Python instalado, inicie o servidor:

   ```powershell
   py -m http.server 8000 --bind 127.0.0.1 --directory src
   ```

   Em outros ambientes com Python 3, um comando equivalente é:

   ```bash
   python3 -m http.server 8000 --bind 127.0.0.1 --directory src
   ```

3. Abra no navegador:

   http://127.0.0.1:8000/

Para interromper o servidor, pressione `Ctrl + C` no terminal. Abrir o HTML diretamente via `file://` pode causar restrições no carregamento dos módulos JavaScript; por isso, prefira o servidor HTTP.

## Testes automatizados

O projeto contém **sete testes automatizados** em `tests/finance.test.js`, implementados com os módulos nativos do Node.js.

Para executá-los, instale o [Node.js](https://nodejs.org/) (preferencialmente a versão 22, utilizada no CI) e rode, na raiz do repositório:

```bash
npm test
```

O comando executa `node --test`, conforme definido em `package.json`. Não há pacotes externos para instalar.

Os testes verificam:

1. Cadastro de receita válida, com identificação, descrição, tipo e valor.
2. Preservação da lista original, aceitação de vírgula decimal e rejeição de entradas inválidas para receitas.
3. Cadastro de despesa válida.
4. Rejeição de descrições vazias e valores inválidos para despesas.
5. Cálculo de saldo como **receitas menos despesas**.
6. Resumo de uma lista vazia.
7. Saldo negativo e arredondamento de centavos.

Exemplo verificado: receita de R$ 2.500,00 e despesa de R$ 100,00 produzem saldo de **R$ 2.400,00**.

## Organização do código

A aplicação separa as regras financeiras das operações de interface:

- `src/js/finance.js`: criação e validação dos lançamentos, cadastro de receitas e despesas e cálculo do resumo. Não manipula HTML nem acessa `localStorage`.
- `src/js/app.js`: eventos do formulário, conversão dos valores digitados, renderização dos cartões e do histórico e persistência no navegador.
- `src/index.html`: estrutura da interface.
- `src/css/style.css`: aparência e layout responsivo.

Cada lançamento contém os campos `id`, `type`, `description` e `amount`. O campo `type` assume `income` (receita) ou `expense` (despesa), e `amount` representa um valor positivo.

As funções `registerIncome` e `registerExpense` **retornam uma nova lista**, sem alterar a lista recebida. O resumo é calculado pela função `calculateSummary`.

## Estrutura do repositório

```text
meu-dinheiro/
├── .github/
│   └── workflows/
│       └── ci.yml
├── docs/
│   └── evidencias/
│       ├── 01-testes-passando.txt
│       ├── 02-testes-falhando-simulacao.txt
│       ├── 03-testes-passando-apos-correcao.txt
│       ├── 04-pipeline-sucesso.png
│       ├── 05-pipeline-falha.png
│       ├── 06-pipeline-apos-correcao.png
│       └── 07-pr-falha-e-correcao.png
├── src/
│   ├── index.html
│   ├── css/
│   │   └── style.css
│   └── js/
│       ├── app.js
│       └── finance.js
├── tests/
│   └── finance.test.js
├── package.json
└── README.md
```

## Controle de versão e colaboração

A equipe utilizou branches e pull requests para integrar contribuições à `main`, mantendo histórico dos commits e das mudanças.

| Pull request | Contribuição |
|---|---|
| [#1](https://github.com/marianagpalacios/meu-dinheiro/pull/1) | Interface e integração com as regras financeiras. |
| [#2](https://github.com/marianagpalacios/meu-dinheiro/pull/2) | Testes automatizados. |
| [#4](https://github.com/marianagpalacios/meu-dinheiro/pull/4) | Pipeline de testes, build e deploy. |
| [#5](https://github.com/marianagpalacios/meu-dinheiro/pull/5) | Simulação de falha no cálculo do saldo e correção. |
| [#6](https://github.com/marianagpalacios/meu-dinheiro/pull/6) | Documentação do pipeline no README. |

O PR #3 foi encerrado sem merge. O pipeline integrado à `main` corresponde ao PR #4.

[Consultar o histórico de commits](https://github.com/marianagpalacios/meu-dinheiro/commits/main).

## Pipeline de CI/CD

O workflow está em [`.github/workflows/ci.yml`](.github/workflows/ci.yml) e é executado pelo GitHub Actions em:

- Pull requests direcionados à `main`.
- Pushes realizados na `main`.

A execução possui três jobs dependentes:

1. **`testes`:** prepara o Node.js 22 no Ubuntu e executa `npm test`.
2. **`build`:** depende de `testes` e envia o conteúdo da pasta `src/` como artefato do GitHub Pages, usando `actions/upload-pages-artifact@v3`. Como a aplicação é estática, não há compilação tradicional.
3. **`deploy`:** depende de `build` e publica com `actions/deploy-pages@v4` **somente em eventos de push na `main`**.

Fluxo: `testes` → `build` → `deploy` (o último job apenas nos pushes da `main`).

Se os testes falharem, o `build` não é executado. Em pull requests, o job `deploy` permanece pulado pela condição do workflow, inclusive quando os testes passam. Isso permite validar as branches sem publicar versões intermediárias.

### Demonstração de falha e recuperação

No [PR #5](https://github.com/marianagpalacios/meu-dinheiro/pull/5), a equipe introduziu intencionalmente um erro em `src/js/finance.js`: a fórmula do saldo passou a **somar** receitas e despesas em vez de **subtrair** as despesas.

- **Falha:** os testes de saldo detectaram o erro; o job `testes` falhou e o `build` foi pulado. [Execução reprovada](https://github.com/marianagpalacios/meu-dinheiro/actions/runs/45478647120).
- **Correção:** a subtração foi restaurada; `testes` e `build` passaram na verificação do pull request. [Execução após a correção](https://github.com/marianagpalacios/meu-dinheiro/actions/runs/45481123227).
- **Publicação:** após o merge do PR #5 na `main`, os jobs `testes`, `build` e `deploy` passaram. [Execução com deploy aprovado](https://github.com/marianagpalacios/meu-dinheiro/actions/runs/45484900759).

Também estão disponíveis a [primeira execução aprovada na `main`](https://github.com/marianagpalacios/meu-dinheiro/actions/runs/45473888267) e uma [execução posterior aprovada](https://github.com/marianagpalacios/meu-dinheiro/actions/runs/45486789783).

### Evidências

Os arquivos da pasta [`docs/evidencias/`](docs/evidencias/) documentam os testes e a execução do pipeline:

| Arquivo | Conteúdo |
|---|---|
| `01-testes-passando.txt` | Execução local inicial: sete testes aprovados. |
| `02-testes-falhando-simulacao.txt` | Execução local com erro intencional no saldo. |
| `03-testes-passando-apos-correcao.txt` | Nova execução local após a correção. |
| `04-pipeline-sucesso.png` | Execução inicial aprovada com testes, build e deploy. |
| `05-pipeline-falha.png` | Testes reprovados e jobs dependentes pulados. |
| `06-pipeline-apos-correcao.png` | Execução aprovada na `main` após a correção, com deploy. |
| `07-pr-falha-e-correcao.png` | Histórico dos commits de falha e correção no PR #5. |

## Equipe

| Integrante | Responsabilidade principal |
|---|---|
| Mariana Gasparotto Palácios | Organização do repositório, integração, evidências e relatório técnico. |
| Mauricio Albuquerque Christakis | Regras financeiras, cálculo do saldo e validações. |
| Thiago Henrique Almeida dos Santos | Interface HTML/CSS e integração com o módulo financeiro. |
| Sabrina Cavalcante de Queiroz | Testes automatizados e registros das execuções locais. |
| Letícia Bento Pinto | Configuração do GitHub Actions, CI/CD e GitHub Pages. |

As atividades foram integradas e revisadas de forma colaborativa.

## Limitações

- Os lançamentos são armazenados somente no navegador, sem sincronização entre dispositivos.
- Não há autenticação, backend, banco de dados remoto ou integração com instituições financeiras.
- Não foram implementados filtros, categorias, edição ou exclusão de lançamentos.
- Em situações excepcionais de falha do `localStorage`, o feedback de salvamento pode ser aprimorado.

O MeuDinheiro tem finalidade acadêmica e demonstra o processo de integração, validação e entrega de uma aplicação web simples.
