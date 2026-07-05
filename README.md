<h1 align="center">MVP de Detecção de Anomalias em Fundos de Investimento</h1>

<p align="center">
<p align="center">
  <a href="https://shields.io/">
    <img src="https://img.shields.io/badge/Status-Conclu%C3%ADdo-green.svg" alt="Status">
  </a>
  <a href="https://en.wikipedia.org/wiki/Anomaly_detection">
    <img src="https://img.shields.io/badge/Detec%C3%A7%C3%A3o%20de%20Anomalias-blue" alt="Detecção de Anomalias">
  </a>
  <a href="https://colab.research.google.com/">
    <img src="https://img.shields.io/badge/Google%20Colab-Ambiente-F9AB00?style=flat&logo=google-colab&logoColor=white" alt="Google Colab">
  </a>
  <a href="https://dados.cvm.gov.br/dataset/fi-doc-inf_diario">
    <img src="https://img.shields.io/badge/CVM%20Dados%20Abertos-Fonte-003366?style=flat" alt="CVM Dados Abertos">
  </a>
  <a href="https://pandas.pydata.org/">
    <img src="https://img.shields.io/badge/Pandas-Analise-purple?style=flat&logo=pandas&logoColor=white" alt="Pandas">
  </a>
  <a href="https://numpy.org/">
    <img src="https://img.shields.io/badge/NumPy-Numerico-777BB4?style=flat&logo=numpy&logoColor=white" alt="NumPy">
  </a>
  <a href="https://matplotlib.org/">
    <img src="https://img.shields.io/badge/Matplotlib-Graficos-000000?style=flat&logo=python&logoColor=white" alt="Matplotlib">
  </a>
  <a href="https://seaborn.pydata.org/">
    <img src="https://img.shields.io/badge/Seaborn-Graficos-lightblue?style=flat" alt="Seaborn">
  </a>
  <a href="https://scikit-learn.org/">
    <img src="https://img.shields.io/badge/Scikit--Learn-Modelagem-F7931E?style=flat&logo=scikit-learn&logoColor=white" alt="Scikit-learn">
  </a>
</p>

<p align="center">
  <em>Projeto acadêmico da Pós-graduação em Data Science & Analytics (PUC-Rio) com foco em detecção de anomalias não supervisionada, aplicado a dados públicos de fundos de investimento da CVM.</em>
</p>


<h2 align="center">Visão Geral</h2

O Brasil tem mais de 20 mil fundos de investimento ativos. Nenhuma equipe consegue monitorar todos individualmente. Um fundo em dificuldade costuma mostrar sinais combinados: queda de cota, perda de cotistas e redução de patrimônio ao mesmo tempo. Um indicador isolado pode ser normal; a combinação de vários sinais negativos indica risco.

O modelo lê o histórico diário de cada fundo, calcula indicadores de performance, fluxo e tendência, e sinaliza os fundos que mais destoam do padrão do mercado. O objetivo é gerar uma lista priorizada para due diligence, não substituir a análise humana.

Link do projeto: [MVP_Deteccao_Anoalia_Fundos](https://colab.research.google.com/drive/119wF28wAZtBaVv6zMuJzkpyRGlZNUAGp#scrollTo=0tNfHPXSjTie)

<h2 align="center">Dataset</h2>

Fonte: [Informe Diário de Fundos de Investimento](https://dados.cvm.gov.br/dataset/fi-doc-inf_diario), publicado pela CVM em dados abertos. O notebook baixa os arquivos direto por URL pública, sem login nem upload manual.

- Período: dezembro de 2023 a junho de 2026, 30 meses, 630 pregões.
- Volume: 16.503.894 registros
- Escopo: fundos FIF (Fundo de Investimento Financeiro). O filtro exclui FII, FIP e FIDC, que têm regras e perfis de risco distintos.
- Complemento: cadastro de fundos da CVM, usado para nome, situação, categoria ANBIMA e público-alvo.

O recorte temporal começa em dezembro de 2023 por dois motivos:
- A Resolução CVM 175 introduziu o campo que identifica o tipo de fundo só a partir dessa data.
- O período evita o choque de juros de 2021 a 2022, quando a Selic subiu de 2% para 13,75% em 18 meses e distorceu o retorno de fundos de renda fixa por razões macroeconômicas e não operacionais.

<h2 align="center">Metodologia</h2>

**Tipo de problema:** detecção de anomalias, aprendizado não supervisionado. Os dados públicos da CVM não trazem uma lista de fundos problemáticos, então o projeto não treina um classificador supervisionado.

**Indicadores:** O pipeline transforma o painel diário em uma tabela de 10 indicadores por fundo: volatilidade, retorno total, max drawdown, sharpe, log do patrimônio, fluxo médio, proporção de dias com fluxo negativo, tendência de patrimônio, tendência de cotistas e log de cotistas. Fundos com menos de 500 observações no período saem da base, para garantir indicadores estatisticamente confiáveis.

**Pré-processamento:** Cada indicador é normalizado dentro do peer group do fundo, categoria ANBIMA cruzada com público-alvo, antes da padronização global. Sem essa etapa, um fundo de renda fixa com volatilidade de 1% ao ano recebe score alto ao lado de fundos de ações com 20% ao ano, mesmo sendo normal dentro da própria categoria.

**Modelos:** Três abordagens, comparadas lado a lado:

| Modelo | Critério | Fundos sinalizados | Suporta novos dados |
|---|---|---|---|
| Baseline (z-score) | Um indicador extremo isolado | 2.085 (12,8%) | Não |
| Isolation Forest | Score multivariado de isolabilidade | 163 (1,0%) | Sim |
| Local Outlier Factor | Densidade local | 163 (1,0%) | Não |

O Isolation Forest é o modelo final, pela capacidade de pontuar novos fundos via `predict()`. O LOF funciona como validação cruzada de metodologia.

**Validação:** Sem rótulos, a validação usa duas estratégias. Primeiro, um holdout retroativo: fundos já classificados como "Em Liquidação" ou "Cancelado" no cadastro CVM ficam fora do treino e são pontuados depois, para checar se o modelo os classifica como mais anômalos que a média. Segundo, uma análise de sensibilidade do hiperparâmetro `contamination`, que mostra como o volume de fundos sinalizados varia com o limiar escolhido.

<h2 align="center">Resultados</h2>

O modelo final usa `contamination = 0.01` e `n_estimators = 200`, sinalizando cerca de 160 fundos entre os cerca de 16 mil que passam pelos filtros de qualidade. Os fundos sinalizados se dividem em três tipos de estresse: performance, fluxo de caixa e declínio estrutural. Uma segunda lista separa outliers positivos, fundos com performance consistentemente acima da mediana, que merecem revisão de legitimidade em vez de due diligence de risco.

<h2 align="center">Limitações</h2>

- Sem rótulos verdadeiros, o modelo não calcula precisão nem recall. A avaliação é qualitativa.
- O hiperparâmetro `contamination` depende do julgamento do analista, não de uma métrica objetiva.
- A agregação em indicadores por fundo perde granularidade temporal: uma queda abrupta e um declínio gradual podem gerar indicadores parecidos.
- O LOF não classifica fundos novos sem re-treino completo.

<h2 align="center">Próximos Passos</h2>

Benchmark contra CDI e Ibovespa por regime de mercado; modelos de série temporal, como autoencoder LSTM, para capturar padrões dinâmicos; monitoramento mensal automatizado.

<h2 align="center">Como executar</h2>

O notebook `MVP_Deteccao_Anomalia_Fundos.ipynb` roda do início ao fim no Google Colab, sem configuração manual. Ele baixa os dados da CVM automaticamente e usa apenas bibliotecas padrão da stack científica Python: pandas, numpy, scikit-learn, matplotlib e seaborn.

Ao final, o notebook salva três artefatos em `cvm_mvp_data/artifacts/`:

- `pipeline_preproc.pkl`: pipeline de imputação e padronização.
- `isolation_forest.pkl`: modelo treinado, pronto para pontuar novos fundos.
- `fundos_com_scores.csv`: tabela completa com score de anomalia e tipo de anomalia por fundo.

<h2 align="center">Autor</h2>

**Felipe Pinheiro**

[Gmail](mailto:felipervmospinheiro@gmail.com) | [LinkedIn](https://www.linkedin.com/in/feliperamospinheiro)
