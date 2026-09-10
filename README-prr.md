# Painel PRR — configuração

Painel de acompanhamento de projetos PRR: unidade, estado, taxa de execução do projeto, taxa de execução financeira, investimento, dependências, riscos e próximos passos. Página estática (`prr-dashboard.html`), sem build step, hospedada no GitHub Pages deste repositório (`prrstatus`).

Link público: `https://brunomigueltrigo-design.github.io/prrstatus/prr-dashboard.html`

## Persistência dos dados

Por omissão, os dados vêm embutidos no próprio `prr-dashboard.html` e qualquer edição feita na página só dura enquanto a página não é recarregada.

Para persistir as alterações entre sessões, o painel pode ligar-se diretamente a este repositório e guardar os dados no ficheiro `projects.json`, usando a API do GitHub — sem qualquer backend ou base de dados externa.

O repositório de destino (`brunomigueltrigo-design/prrstatus`, ficheiro `projects.json`, branch `main`) está **fixo no código** do `prr-dashboard.html` (constante `GITHUB_CONFIG`) — não é algo que se preencha na página. A única coisa que se introduz no browser é o **token de acesso**, porque é uma credencial e nunca deve ficar escrita em código publicado:

1. Cria um **token de acesso fine-grained**: [github.com/settings/tokens?type=beta](https://github.com/settings/tokens?type=beta) → "Generate new token" → restringe o "Repository access" a este repositório (`prrstatus`) → em "Permissions", dá **"Contents: Read and write"**.
2. Abre o painel e clica em **"Ligar ao GitHub"** (canto superior direito).
3. Cola o token e clica em "Ligar".
4. O painel carrega os projetos atuais de `projects.json` e, a partir daí, cada edição de projeto (e cada importação de Excel) fica automaticamente gravada nesse ficheiro, **como um commit novo no repositório** — com histórico completo de alterações.

O token fica guardado apenas no `localStorage` do browser onde ligaste — nunca é enviado para mais lado nenhum além da API do GitHub, e nunca fica escrito no código da página. Cada pessoa que precise de editar (não só ver) o painel no seu próprio browser tem de repetir este passo com o seu próprio token.

**Sem ligar o token, os projetos criados/editados na página ficam apenas em memória do browser** (no array `state.projects`, dentro da sessão atual) — perdem-se ao recarregar a página. Não há nenhum outro sítio a guardar dados por omissão.

Sem ligação ao GitHub, o painel continua a funcionar normalmente em modo "só nesta sessão" — útil para testar ou para quem só precisa de consultar.

## Fluxo de trabalho: importar uma vez, depois só atualizar

O painel é **de atualização, não de criação**: não há botão para criar nem para remover projetos na aplicação. A lista de projetos é definida pelo Excel importado, e a partir daí só se editam os campos de projetos já existentes (estado, taxas, investimento, dependências, riscos, próximos passos, gestor).

1. **Carregar os dados** — botão **"Importar Excel"**, que lê um ficheiro no formato "Ponto de Situação Projeto PRR" (folha `PDS PRR`, com cabeçalhos como "Unidade", "N. Ficha Projecto", "Nome do Projetos", "Estado", "Taxa de execução projeto", "Investimento total", "Taxa de execução financeira", "Dependências", "Riscos", "Próximos Passos", "Gestor de Projeto") e substitui todos os projetos atuais pelos do ficheiro.
   - As taxas podem vir em fração (`0.7`) ou já em percentagem (`70`) — o painel deteta automaticamente.
   - O campo "Estado" aceita as variações do Excel de origem ("em atraso", "em execução", "por iniciar"/"por inciar", "concluído") e mapeia para os quatro estados do painel.
   - Se estiver ligado ao GitHub, a importação é logo gravada como commit; caso contrário fica só na sessão.
   - A operação pede confirmação antes de substituir os dados, porque é destrutiva — usa-se tipicamente uma vez, para semear ou repor a lista completa (ex: no início de um novo período de reporte).
2. **Atualizar no dia a dia** — botão "editar" em cada cartão. Não é preciso voltar a importar Excel para mudar o estado, as taxas ou os riscos de um projeto existente.

## O que o painel mostra

- **Resumo no topo**: projetos em execução, concluídos, em atraso, taxa média de execução do projeto, investimento total, taxa média de execução financeira.
- **Filtros por estado**: em atraso, em execução, por iniciar, concluído.
- **Cartões agrupados por unidade** (UIA, UPACE, UID, URN), com ponto de cor por estado, barras de execução do projeto e financeira, investimento total e caixa de riscos em destaque.
- **Exportar PowerPoint**: gera um `.pptx` (via PptxGenJS, no browser) com um slide de visão global para a Direção e um slide por unidade.

## Nota de segurança

`projects.json` guarda tudo em texto simples neste repositório privado — nada fica em serviços externos. O único dado sensível introduzido pelo utilizador é o token do GitHub, que fica só no browser local (nunca em `projects.json` nem em nenhum commit).
