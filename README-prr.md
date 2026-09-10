# Painel PRR — configuração

Painel de acompanhamento de projetos PRR: unidade, estado, taxa de execução do projeto, taxa de execução financeira, investimento, dependências, riscos e próximos passos. Página estática (`prr-dashboard.html`), sem build step, hospedada no GitHub Pages deste repositório (`prrstatus`).

Link público: `https://brunomigueltrigo-design.github.io/prrstatus/prr-dashboard.html`

## Persistência dos dados

Por omissão, os dados vêm embutidos no próprio `prr-dashboard.html` e qualquer edição feita na página só dura enquanto a página não é recarregada.

Para persistir as alterações entre sessões, o painel pode ligar-se diretamente a este repositório e guardar os dados no ficheiro `projects.json`, usando a API do GitHub — sem qualquer backend ou base de dados externa:

1. Cria um **token de acesso fine-grained**: [github.com/settings/tokens?type=beta](https://github.com/settings/tokens?type=beta) → "Generate new token" → restringe o "Repository access" a este repositório (`prrstatus`) → em "Permissions", dá **"Contents: Read and write"**.
2. Abre o painel e clica em **"Ligar ao GitHub"** (canto superior direito).
3. Preenche:
   - **Utilizador/Organização**: `brunomigueltrigo-design`
   - **Repositório**: `prrstatus`
   - **Ficheiro de dados**: `projects.json`
   - **Branch**: `main`
   - **Token**: o token criado no passo 1
4. Clica em "Ligar". O painel carrega os projetos atuais de `projects.json` e, a partir daí, cada criação/edição/remoção de projeto (e cada importação de Excel) fica automaticamente gravada nesse ficheiro, **como um commit novo no repositório** — com histórico completo de alterações.

O token fica guardado apenas no `localStorage` do browser onde ligaste — nunca é enviado para mais lado nenhum além da API do GitHub. Cada pessoa que precise de editar (não só ver) o painel no seu próprio browser tem de repetir este passo com o seu próprio token.

Sem ligação ao GitHub, o painel continua a funcionar normalmente em modo "só nesta sessão" — útil para testar ou para quem só precisa de consultar.

## Importar dados de Excel

O botão **"Importar Excel"** lê um ficheiro no formato "Ponto de Situação Projeto PRR" (folha `PDS PRR`, com cabeçalhos como "Unidade", "N. Ficha Projecto", "Nome do Projetos", "Estado", "Taxa de execução projeto", "Investimento total", "Taxa de execução financeira", "Dependências", "Riscos", "Próximos Passos", "Gestor de Projeto") e substitui todos os projetos atuais pelos do ficheiro.

- As taxas podem vir em fração (`0.7`) ou já em percentagem (`70`) — o painel deteta automaticamente.
- O campo "Estado" aceita as variações do Excel de origem ("em atraso", "em execução", "por iniciar"/"por inciar", "concluído") e mapeia para os quatro estados do painel.
- Se estiver ligado ao GitHub, a importação é logo gravada como commit; caso contrário fica só na sessão.
- A operação pede confirmação antes de substituir os dados, porque é destrutiva.

## O que o painel mostra

- **Resumo no topo**: projetos em execução, concluídos, em atraso, taxa média de execução do projeto, investimento total, taxa média de execução financeira.
- **Filtros por estado**: em atraso, em execução, por iniciar, concluído.
- **Cartões agrupados por unidade** (UIA, UPACE, UID, URN), com ponto de cor por estado, barras de execução do projeto e financeira, investimento total e caixa de riscos em destaque.
- **Exportar PowerPoint**: gera um `.pptx` (via PptxGenJS, no browser) com um slide de visão global para a Direção e um slide por unidade.

## Nota de segurança

`projects.json` guarda tudo em texto simples neste repositório privado — nada fica em serviços externos. O único dado sensível introduzido pelo utilizador é o token do GitHub, que fica só no browser local (nunca em `projects.json` nem em nenhum commit).
