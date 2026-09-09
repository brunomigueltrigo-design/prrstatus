[README-prr.md](https://github.com/user-attachments/files/32004432/README-prr.md)
# Painel PRR — configuração

Painel de acompanhamento de projetos PRR: estado/fase, execução financeira, execução funcional, prazos e desafios. Hospedado no GitHub Pages, guarda os dados diretamente num ficheiro do repositório.

## Passos (iguais ao "Registo de Horas", ficheiro de dados diferente)

1. **Repositório**: cria um repositório privado novo (ex: `painel-prr`) e faz upload de `prr-dashboard.html`, `projects.json` e este README.
   - Podes reutilizar o mesmo repositório do "Registo de Horas" se preferires — basta fazer upload destes ficheiros lá também, com nomes diferentes (`data.json` já está a ser usado, por isso este usa `projects.json`).
2. **GitHub Pages**: Settings → Pages → Deploy from a branch → `main` → `/ (root)`. O link fica algo como:
   `https://o-teu-user.github.io/painel-prr/prr-dashboard.html`
3. **Token**: um fine-grained PAT restrito a este repositório, com "Contents: Read and write".
4. **Ligar a app**: abre o link, clica em "Ligar ao GitHub", preenche utilizador, repositório, `projects.json` como caminho, e o token.
5. **Criar o primeiro projeto**: clica em "+ Novo projeto" e preenche estado, execução financeira (verba atribuída vs. executada), execução funcional (%), próximo prazo/marco, e desafios/riscos.

## O que o painel mostra

- **Resumo no topo**: nº total de projetos, quantos em execução, execução financeira média, quantos têm desafios registados.
- **Filtros por estado**: candidatura, aprovado, em execução, concluído, suspenso.
- **Cada cartão de projeto**: barra de execução financeira (€ executado vs. atribuído), barra de execução funcional (%), próximo marco/prazo, e uma caixa destacada com os desafios/riscos atuais.
- **Editar/remover**: qualquer projeto pode ser atualizado — cada alteração fica registada como commit no repositório, com histórico completo.

## Nota

O ficheiro `projects.json` guarda tudo em texto simples dentro do teu repositório privado — nada fica em bases de dados externas. Se precisares de relatórios (ex: exportar para Excel para uma entidade financiadora), o mais simples é abrir o `projects.json` diretamente no GitHub ou pedires-me depois um script/skill de exportação a partir desse ficheiro.
