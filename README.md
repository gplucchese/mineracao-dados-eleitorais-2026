# Mineração de Dados Eleitorais 2026

Projeto de disciplina: da análise descritiva à detecção de anomalias, usando dados reais e abertos do TSE (Eleições 2026).

## Estrutura do projeto

| Fase | Notebook | O que faz |
|---|---|---|
| 0 | `00_preparacao_dados.ipynb` | Lê os zips do TSE (baixados manualmente para `dados/`), filtra por cargo/UF, limpa e salva um dataset pronto em `dados/` |
| 1 | `01_analise_descritiva.ipynb` | Exploração ampla e sem viés: distribuições, outliers, proporções e dispersão de todas as variáveis relevantes (candidatos e bens) |
| 2 | `02_clusterizacao.ipynb` | K-Means, DBSCAN, hierárquico (hclust) e Bisecting K-Means |
| 3 | `03_regras_associacao.ipynb` | Regras de associação (Apriori) |
| 4 | `04_deteccao_anomalias.ipynb` | Detecção de anomalias |

Cada fase parte do resultado da anterior — todas leem/escrevem em `dados/`, então não é preciso repetir a Fase 0 dentro de cada notebook.

### Cronograma de liberação

- ✅ **Fase 0 e Fase 1** — liberadas.
- ⏳ Fase 2 (clusterização) — em breve.
- ⏳ Fase 3 (regras de associação) — em breve.
- ⏳ Fase 4 (detecção de anomalias) — em breve.

### Professor x alunos

O professor publica a análise **nível Brasil**, cargo **Governador**. Os alunos replicam o mesmo pipeline em **Senador ou Deputado Federal**, recortado pela **UF** do seu trabalho. A única coisa que muda é a célula de configuração no topo da Fase 0:

```python
CARGO = "GOVERNADOR"   # ou "SENADOR", "DEPUTADO FEDERAL"
UF = None                # None = Brasil inteiro; ou a sigla da UF, ex.: "SP", "BA", "PE"
ANO_ELEICAO = 2026
```

## Baixando os dados do TSE (é manual, de propósito)

O download automático pelo CDN do TSE é bloqueado por um firewall (Akamai) que rejeita requisições que não vêm de um navegador de verdade — acontece tanto no Colab quanto localmente, em qualquer rede, e não é intermitente (tentar de novo não resolve). Por isso o fluxo é manual:

1. Baixe pelo navegador:
   - `https://cdn.tse.jus.br/estatistica/sead/odsele/consulta_cand/consulta_cand_2026.zip`
   - `https://cdn.tse.jus.br/estatistica/sead/odsele/bem_candidato/bem_candidato_2026.zip`
2. Salve os dois dentro de `dados/`, com os nomes `consulta_cand_2026.zip` e `bem_candidato_2026.zip`.
3. Rode a Fase 0 normalmente — ela confere se os arquivos estão no lugar certo antes de descompactar.

## Sobre "congelar" a versão dos dados

O arquivo-fonte do TSE pode ser atualizado por eles a qualquer momento (novas candidaturas, correções, impugnações). Para não depender disso:

- Os zips baixados manualmente ficam parados em `dados/` — eles só mudam quando alguém baixar uma versão nova de propósito e sobrescrever.
- O que fica **versionado no repositório** é o resultado da Fase 0 (`dados/*.csv`), não o zip bruto do TSE. Esse CSV é a versão "congelada": todo mundo que der `git pull` usa exatamente os mesmos dados, mesmo que o TSE tenha atualizado o arquivo original depois. Atualizar é sempre um gesto intencional: baixar um zip novo, rodar a Fase 0 de novo e commitar o novo CSV por cima.
- O próprio arquivo do TSE carrega as colunas `DT_GERACAO`/`HH_GERACAO`, com o timestamp de quando eles geraram aquele extrato — a Fase 0 exibe esse valor, então dá pra sempre rastrear qual snapshot está em uso.

## Como rodar

### Google Colab (recomendado para os alunos)

Basta abrir o notebook direto do GitHub — sem instalar nada:

```
https://colab.research.google.com/github/<seu-usuario>/mineracao-dados-eleitorais-2026/blob/main/00_preparacao_dados.ipynb
```

O Colab já vem com quase todas as bibliotecas prontas; se algo faltar (normalmente só `mlxtend`, usado na Fase 3), rode no topo do notebook:

```python
!pip install -q -r requirements.txt
```

### Local, com VS Code

1. Crie o ambiente virtual do projeto (uma vez só):
   ```bash
   python3 -m venv .venv
   ./.venv/bin/pip install -r requirements.txt
   ```
2. Registre o kernel Jupyter:
   ```bash
   ./.venv/bin/python -m ipykernel install --user --name=mineracao-eleitoral-2026 --display-name="Python (mineração eleitoral 2026)"
   ```
3. Abra a pasta do projeto no VS Code, abra qualquer `.ipynb` e, no canto superior direito, selecione o kernel **"Python (mineração eleitoral 2026)"** (ou o interpretador `.venv` do projeto, que o VS Code detecta automaticamente).

O `.venv/` não é versionado (está no `.gitignore`) — cada pessoa cria o seu localmente a partir do `requirements.txt`.

## Fonte dos dados

Portal de dados abertos do TSE — Consulta de Candidatos e Bens de Candidatos, Eleições 2026 (`https://cdn.tse.jus.br/estatistica/sead/odsele/`).
