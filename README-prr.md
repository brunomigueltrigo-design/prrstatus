# Painel PRR — configuração

Painel de acompanhamento de projetos PRR: unidade, estado, taxa de execução do projeto, taxa de execução financeira, investimento, dependências, riscos e próximos passos. Página estática (`prr-dashboard.html`), sem build step, hospedada no GitHub Pages deste repositório (`prrstatus`).

Link público: `https://brunomigueltrigo-design.github.io/prrstatus/prr-dashboard.html`

## Persistência dos dados

O painel liga-se sozinho ao repositório e guarda os dados no ficheiro `projects.json`, usando a API do GitHub — sem qualquer backend ou base de dados externa, e **sem nenhum passo manual**: não há botão para ligar, o repositório de destino e o token de acesso estão ambos fixos no código (`GITHUB_CONFIG` e `GITHUB_TOKEN`, no `prr-dashboard.html`).

### ⚠️ Isto expõe o token a quem tiver o link da página

Como o GitHub Pages costuma ser publicamente acessível mesmo vindo de um repositório privado, **qualquer pessoa com o link da página consegue ver este token no código-fonte** (Ctrl+U / "Ver código-fonte da página") e usá-lo para escrever no repositório. Esta troca foi feita conscientemente, para não haver nenhum ecrã de ligação — mas implica cuidados:

- O token tem de ser um **fine-grained PAT restrito só a este repositório** (`prrstatus`), com a permissão mínima **"Contents: Read and write"** — nunca um token com acesso a outros repositórios ou à conta toda.
- Considera dar-lhe uma **expiração curta** (ex: 90 dias) e voltar a gerar um novo quando expirar (basta substituir o valor de `GITHUB_TOKEN` no código).
- Se o link da página alguma vez for partilhado com alguém que não deva poder editar os dados, **revoga o token imediatamente** em [github.com/settings/tokens?type=beta](https://github.com/settings/tokens?type=beta) — isso corta o acesso de escrita sem precisares de mudar mais nada.

### Como configurar o token (uma vez)

1. Cria um token fine-grained em [github.com/settings/tokens?type=beta](https://github.com/settings/tokens?type=beta) → "Generate new token" → "Repository access" restrito a `prrstatus` → em "Permissions", `Contents: Read and write`.
2. No `prr-dashboard.html`, procura a linha `const GITHUB_TOKEN = 'COLOCAR_TOKEN_AQUI';` e substitui `'COLOCAR_TOKEN_AQUI'` pelo token gerado.
3. Faz commit e push dessa alteração (ou pede para eu o fazer, colando-me o token diretamente).

Enquanto o `GITHUB_TOKEN` não estiver preenchido, o painel mostra um aviso no topo e as edições ficam só em memória do browser (perdem-se ao recarregar).

## Fluxo de trabalho: importar uma vez, depois só atualizar

O painel é **de atualização, não de criação**: não há botão para criar nem para remover projetos na aplicação. A lista de projetos é definida pelo Excel importado, e a partir daí só se editam os campos de projetos já existentes (estado, taxas, investimento, dependências, riscos, próximos passos, gestor).

1. **Carregar os dados** — botão **"Importar Excel"**, que lê um ficheiro no formato "Ponto de Situação Projeto PRR" (folha `PDS PRR`, com cabeçalhos como "Unidade", "N. Ficha Projecto", "Nome do Projetos", "Estado", "Taxa de execução projeto", "Investimento total", "Taxa de execução financeira", "Dependências", "Riscos", "Próximos Passos", "Gestor de Projeto") e substitui todos os projetos atuais pelos do ficheiro.
   - As taxas podem vir em fração (`0.7`) ou já em percentagem (`70`) — o painel deteta automaticamente.
   - O campo "Estado" aceita as variações do Excel de origem ("em atraso", "em execução", "por iniciar"/"por inciar", "concluído") e mapeia para os quatro estados do painel.
   - Se o `GITHUB_TOKEN` estiver configurado, a importação é logo gravada como commit; caso contrário fica só na sessão.
   - A operação pede confirmação antes de substituir os dados, porque é destrutiva — usa-se tipicamente uma vez, para semear ou repor a lista completa (ex: no início de um novo período de reporte).
2. **Atualizar no dia a dia** — botão "editar" em cada cartão. Não é preciso voltar a importar Excel para mudar o estado, as taxas ou os riscos de um projeto existente.

## O que o painel mostra

- **Resumo no topo**: projetos em execução, concluídos, em atraso, taxa média de execução do projeto, investimento total, taxa média de execução financeira.
- **Filtros por estado**: em atraso, em execução, por iniciar, concluído.
- **Cartões agrupados por unidade** (UIA, UPACE, UID, URN), com ponto de cor por estado, barras de execução do projeto e financeira, investimento total e caixa de riscos em destaque.
- **Exportar PowerPoint**: gera um `.pptx` (via PptxGenJS, no browser) com um slide de visão global para a Direção e um slide por unidade.

## Nota de segurança

`projects.json` guarda tudo em texto simples neste repositório. Ao contrário de versões anteriores deste painel, o token do GitHub agora fica escrito no próprio `prr-dashboard.html` (ver aviso na secção "Persistência dos dados" acima) — é uma troca deliberada para não haver ecrã de ligação, mas significa que o token deve ser tratado como público a partir do momento em que a página é publicada.
