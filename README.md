# Mapa das Equipes de Campo

Mapa interativo das equipes de montagem da **Asbranor Irrigação**: mostra onde cada colaborador mora, em qual obra está e calcula a distância, o tempo de viagem e a hora de chegada na folga semanal.

Página única em HTML, CSS e JavaScript, sem servidor. Abre direto no navegador (computador ou celular) e pode ser publicada no **GitHub Pages**.

## Funcionalidades

- Mapa do Nordeste com residências, obras e a linha de deslocamento casa → obra
- Lista e **tabela editável** de colaboradores e obras (no celular a tabela vira cartões)
- Busca de municípios (base IBGE com coordenadas): ao escolher a cidade, o ponto vai para o mapa
- **Cálculo automático** de distância, tempo de viagem e chegada no sábado (saída às 12h), com aviso de viagem à noite
- Criar, editar, excluir e **pausar/reativar obras**
- Filtros por equipe e busca por nome, cidade ou obra
- Botão **Baixar HTML** para gerar uma cópia com os dados atuais
- Tema claro e escuro

## Como usar

**Rápido:** baixe o `index.html` e abra no navegador. É preciso internet para carregar a biblioteca de mapas (Leaflet).

**GitHub Pages:**
1. Envie este repositório para o GitHub.
2. Em *Settings → Pages*, escolha *Deploy from a branch*, branch `main`, pasta `/ (root)`.
3. O site fica em `https://SEU-USUARIO.github.io/NOME-DO-REPOSITORIO/`.

## Onde os dados ficam salvos

| Onde o programa é aberto | Como salva |
|---|---|
| Dentro do Claude (artifact) | Banco de dados online do Claude, compartilhado entre computador e celular |
| `index.html` local ou GitHub Pages | Só no navegador do aparelho (localStorage). Use **Baixar HTML** para levar os dados junto |

Para salvar online fora do Claude é preciso ligar o programa a um banco próprio (Firebase, Supabase, planilha Google etc.). A função `salvar()` em `src/app.js` é o ponto de ligação.

## Estrutura

```
index.html                 programa completo (gerado pelo build, arquivo único)
src/
  template.html            estrutura da página com marcadores {{...}}
  estilo.css               visual (inclui o CSS do Leaflet 1.9.4)
  app.js                   lógica: mapa, edição, busca de municípios, cálculo, salvar
dados/
  dados.json               colaboradores, obras, regras de folga e parâmetros do cálculo
  estados-brasil.geojson   contorno dos estados (Natural Earth)
  municipios.txt           municípios do Brasil: nome|UF|latitude|longitude (IBGE)
scripts/
  build.py                 junta tudo em index.html
  gerar_powerbi.py         gera a planilha Excel pronta para o Power BI
```

## Editar e gerar de novo

```bash
# depois de mudar algo em src/ ou dados/
python scripts/build.py            # recria o index.html

# planilha para o Power BI
pip install openpyxl
python scripts/gerar_powerbi.py    # cria Mapa-Equipes-PowerBI.xlsx
```

Não edite o `index.html` à mão: ele é sobrescrito pelo `build.py`.

## Como o deslocamento é calculado

Não é rota real de estrada. É uma estimativa calibrada com a planilha de folga da empresa:

- **Distância** = distância em linha reta entre os municípios × **1,28** (fator de estrada)
- **Tempo** = distância ÷ **72 km/h** + **10 min**
- **Chegada** = sábado 12:00 + tempo de viagem; depois das 18:00 conta como viagem à noite

Os três valores podem ser mudados na aba *Regras e cálculo* do programa ou em `dados/dados.json` (campo `calc`). Cada colaborador também aceita ajuste manual de km e tempo.

## Fontes e bibliotecas

- [Leaflet 1.9.4](https://leafletjs.com) (BSD-2-Clause), carregado do cdnjs
- Contorno dos estados: [Natural Earth](https://www.naturalearthdata.com) (domínio público), via Highcharts Map Collection
- Municípios e coordenadas: [kelvins/municipios-brasileiros](https://github.com/kelvins/municipios-brasileiros) (base IBGE)
- Fontes: Archivo, Public Sans e JetBrains Mono (Google Fonts)

## Privacidade

`dados/dados.json` e o `index.html` contêm **nomes de colaboradores e cidades onde moram**. Se o repositório for público, essas informações ficam visíveis para qualquer pessoa. Use um **repositório privado** ou troque os dados por exemplos antes de publicar.
