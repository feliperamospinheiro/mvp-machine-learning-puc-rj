# MVP Proposto: Clusterizacao de Fundos por Perfil de Risco e Performance

## Contexto

Este documento organiza uma proposta de MVP de Machine Learning e Analytics alinhada ao seu dia a dia em uma gestora de fundos de investimento de uma instituicao financeira.

A ideia e construir uma solucao simples, util e executavel com dados publicos, sem depender de informacoes internas ou restritas. A proposta e tratar o problema como uma tarefa de clusterizacao, com foco em segmentar fundos com comportamento semelhante em termos de retorno, volatilidade, drawdown, patrimonio e liquidez.

O resultado esperado nao e apenas um exercicio academico. A proposta precisa fazer sentido para rotina de analise, comparacao de fundos, leitura de risco e apoio a tomada de decisao.

---

## 1. Escolha da base de dados

### Base principal recomendada

A base principal pode ser montada a partir de dados publicos da CVM sobre fundos de investimento, combinados com series de mercado publicas para enriquecer a analise.

### Desenho exato do dataset

O recorte mais simples e consistente para o MVP e usar:

- a serie diaria mensal da CVM em `FI/DOC/INF_DIARIO/DADOS/`;
- o cadastro de fundos em `FI/CAD/DADOS/cad_fi.csv`;
- opcionalmente, uma serie de benchmark publico para comparacao, como Ibovespa, CDI ou Selic.

Arquivos recomendados da CVM para o periodo de 2024-01 a 2026-06:

- `https://dados.cvm.gov.br/dados/FI/DOC/INF_DIARIO/DADOS/inf_diario_fi_202401.zip`
- `https://dados.cvm.gov.br/dados/FI/DOC/INF_DIARIO/DADOS/inf_diario_fi_202402.zip`
- `https://dados.cvm.gov.br/dados/FI/DOC/INF_DIARIO/DADOS/inf_diario_fi_202403.zip`
- `https://dados.cvm.gov.br/dados/FI/DOC/INF_DIARIO/DADOS/inf_diario_fi_202404.zip`
- `https://dados.cvm.gov.br/dados/FI/DOC/INF_DIARIO/DADOS/inf_diario_fi_202405.zip`
- `https://dados.cvm.gov.br/dados/FI/DOC/INF_DIARIO/DADOS/inf_diario_fi_202406.zip`
- `https://dados.cvm.gov.br/dados/FI/DOC/INF_DIARIO/DADOS/inf_diario_fi_202407.zip`
- `https://dados.cvm.gov.br/dados/FI/DOC/INF_DIARIO/DADOS/inf_diario_fi_202408.zip`
- `https://dados.cvm.gov.br/dados/FI/DOC/INF_DIARIO/DADOS/inf_diario_fi_202409.zip`
- `https://dados.cvm.gov.br/dados/FI/DOC/INF_DIARIO/DADOS/inf_diario_fi_202410.zip`
- `https://dados.cvm.gov.br/dados/FI/DOC/INF_DIARIO/DADOS/inf_diario_fi_202411.zip`
- `https://dados.cvm.gov.br/dados/FI/DOC/INF_DIARIO/DADOS/inf_diario_fi_202412.zip`
- `https://dados.cvm.gov.br/dados/FI/DOC/INF_DIARIO/DADOS/inf_diario_fi_202501.zip`
- `https://dados.cvm.gov.br/dados/FI/DOC/INF_DIARIO/DADOS/inf_diario_fi_202502.zip`
- `https://dados.cvm.gov.br/dados/FI/DOC/INF_DIARIO/DADOS/inf_diario_fi_202503.zip`
- `https://dados.cvm.gov.br/dados/FI/DOC/INF_DIARIO/DADOS/inf_diario_fi_202504.zip`
- `https://dados.cvm.gov.br/dados/FI/DOC/INF_DIARIO/DADOS/inf_diario_fi_202505.zip`
- `https://dados.cvm.gov.br/dados/FI/DOC/INF_DIARIO/DADOS/inf_diario_fi_202506.zip`
- `https://dados.cvm.gov.br/dados/FI/CAD/DADOS/cad_fi.csv`

Essa janela de 18 meses e suficiente para criar estatisticas consolidadas por fundo e tambem observar comportamento temporal recente.

### Como montar a tabela final

1. Baixar todos os zips mensais da serie diaria.
2. Unificar os arquivos em uma unica base longitudinal, mantendo `CNPJ_FUNDO_CLASSE` e `DT_COMPTC`.
3. Ler o cadastro `cad_fi.csv` e fazer o join pelo CNPJ para trazer metadados como classe, rentabilidade, tipo de condominio, patrimonio e publico-alvo.
4. Agregar por fundo ou classe usando janelas como 3, 6 ou 12 meses.
5. Calcular as features finais do clustering.

### Campos principais da serie diaria

O arquivo `inf_diario_fi_YYYYMM.zip` traz, entre outros, estes campos:

- `TP_FUNDO_CLASSE`;
- `CNPJ_FUNDO_CLASSE`;
- `ID_SUBCLASSE`;
- `DT_COMPTC`;
- `VL_TOTAL`;
- `VL_QUOTA`;
- `VL_PATRIM_LIQ`;
- `CAPTC_DIA`;
- `RESG_DIA`;
- `NR_COTST`.

### Campos principais do cadastro

O arquivo `cad_fi.csv` traz, entre outros, estes campos:

- `CNPJ_FUNDO`;
- `DENOM_SOCIAL`;
- `DT_REG`;
- `DT_CONST`;
- `SIT`;
- `CLASSE`;
- `RENTAB_FUNDO`;
- `CONDOM`;
- `FUNDO_COTAS`;
- `PUBLICO_ALVO`;
- `TAXA_PERFM`;
- `TAXA_ADM`;
- `VL_PATRIM_LIQ`;
- `GESTOR`;
- `ADMIN`;
- `CUSTODIANTE`;
- `CLASSE_ANBIMA`.

Fontes sugeridas:

- CVM - dados publicos de fundos de investimento;
- Banco Central do Brasil - series do SGS, como Selic, IPCA e outras variaveis macro;
- yfinance - precos do Ibovespa, dolar, S&P 500 e outros benchmarks publicos;
- eventualmente ANBIMA ou outra fonte publica, se houver disponibilidade sem login.

### Estrategia pratica para o MVP

Uma versao simples e viavel pode usar apenas:

- cadastro de fundos da CVM;
- retorno diario ou mensal dos fundos, quando disponivel publicamente;
- series de benchmark para calcular medidas de comparacao;
- caracteristicas basicas do fundo, como tipo, classe, patrimonio e numero de cotistas.

### Criticos para a escolha da base

Esta base e adequada porque:

- e publica e pode ser carregada sem login ou token;
- tem aderencia direta a area de atuacao;
- permite construir features interpretaveis;
- suporta uma narrativa util para negocio;
- nao exige um problema supervisionado complexo para gerar valor.

### Limites esperados

- a qualidade da base pode variar por fundo;
- alguns fundos podem ter historico curto ou dados faltantes;
- certos campos podem exigir tratamento e padronizacao;
- a cobertura pode ser melhor para alguns tipos de fundo do que para outros.

---

## 2. Definicao do problema

### Problema escolhido

O problema proposto e realizar a clusterizacao de fundos de investimento com base no seu comportamento historico e em caracteristicas publicas.

### Objetivo do modelo

O objetivo e agrupar fundos semelhantes para:

- identificar perfis de risco e retorno;
- comparar fundos de forma mais objetiva;
- apoiar analise de pares;
- facilitar a segmentacao de uma lista grande de fundos;
- observar se os grupos encontrados batem com intuicoes de mercado, como fundos mais defensivos, mais volateis ou mais concentrados em risco.

### Variavel a ser analisada

Como se trata de clusterizacao, nao ha variavel-alvo supervisionada.

Em vez disso, o modelo vai trabalhar com variaveis como:

- retorno medio em uma janela de tempo;
- volatilidade;
- drawdown maximo;
- retorno acumulado;
- patrimonio liquido;
- numero de cotistas;
- captacao liquida;
- beta ou correlacao com benchmark;
- medidas de assimetria e curtose, se fizer sentido.

### Porque isso pode ser tratado como Machine Learning

O problema pode ser tratado como ML porque:

- envolve encontrar padroes ocultos nos dados;
- a separacao entre grupos nao e dada a priori;
- o algoritmo pode descobrir estruturas nao triviais entre os fundos;
- a solucao e baseada em dados e nao apenas em regras manuais.

### Premissas e restricoes

- os dados precisam ser publicos e reproduziveis;
- a interpretacao dos clusters deve ser financeiramente plausivel;
- as features precisam ser bem escaladas para nao distorcer a distancia entre fundos;
- a analise deve evitar conclusoes exageradas, pois clusterizacao nao prova causalidade;
- o modelo precisa ser explicavel o suficiente para ser usado como apoio a analise.

---

## 3. Estrutura esperada do notebook

O notebook deve funcionar como um relatorio tecnico executavel, combinando codigo, resultados, visualizacoes e explicacoes textuais.

### 3.1. Apresentacao do problema

Nesta secao, descreva o contexto do problema, o objetivo do MVP e o tipo de tarefa de Machine Learning.

Texto sugerido:

Este MVP tem como objetivo segmentar fundos de investimento em grupos com comportamento semelhante de risco e performance. A tarefa escolhida e clusterizacao, pois nao ha uma variavel-alvo supervisionada definida. A ideia e usar dados publicos para construir uma analise util para comparacao de fundos e apoio a leitura de perfil de carteira.

### 3.2. Apresentacao dos dados

Nesta secao, apresente o dataset utilizado.

Inclua, quando aplicavel:

- fonte dos dados;
- quantidade de registros;
- quantidade de atributos;
- descricao das principais variaveis;
- possiveis limitacoes;
- criterios usados para escolher a base.

Sugestao de conteudo:

- fonte: CVM + series publicas de mercado;
- registros: fundos com historico suficiente para calcular estatisticas;
- atributos: metadados do fundo e features construidas a partir dos retornos;
- principais variaveis: patrimonio, volatilidade, retorno acumulado, drawdown, beta, cotistas, captacao liquida;
- limitacoes: fundos com pouco historico, campos faltantes e diferencas de periodicidade;
- criterio de escolha: utilidade pratica para uma gestora e facilidade de reproducao.

### 3.3. Analise exploratoria inicial

Inclua:

- primeiras linhas;
- tipos de variaveis;
- estatisticas descritivas;
- valores ausentes;
- distribuicao dos atributos numericos;
- analise de outliers;
- correlograma ou mapa de calor de correlacao;
- visualizacao da dispersao entre algumas features relevantes.

Sugestoes de perguntas de EDA:

- existem fundos muito concentrados em patrimonios altos?
- qual a distribuicao de volatilidade entre os fundos?
- ha relacao entre retorno e risco?
- ha grupos naturalmente mais defensivos e mais agressivos?

### 3.4. Preparacao dos dados

Inclua:

- tratamento de valores ausentes;
- filtragem de fundos com historico insuficiente;
- padronizacao ou normalizacao;
- engenharia de atributos;
- remocao de variaveis redundantes;
- eventual tratamento de outliers.

Sugestao de estrategia:

- substituir ou remover campos faltantes de acordo com a relevancia;
- criar features agregadas por janela de tempo;
- usar StandardScaler ou RobustScaler antes do clustering;
- testar se algumas variaveis precisam ser transformadas com log, como patrimonio liquido.

### 3.5. Divisao dos dados

Como o problema e nao supervisionado, nao ha treino e teste no sentido classico.

Mesmo assim, voce pode justificar a avaliacao assim:

- usar toda a base para treinar o algoritmo de clusterizacao;
- avaliar a qualidade dos grupos por metricas internas;
- se quiser dar mais robustez, repetir a analise com amostras ou com subconjuntos de tempo;
- comparar o comportamento dos clusters em diferentes periodos, se houver serie temporal suficiente.

Explique que, neste caso, a separacao tradicional treino/teste nao e o ponto central, pois o objetivo e descobrir estrutura nos dados e nao prever um alvo futuro.

---

## 4. Modelagem e treinamento

### Baseline simples

Uma boa baseline para esse problema pode ser:

- segmentacao manual por quantis de volatilidade ou retorno;
- ou K-Means com um numero pequeno de clusters e poucas features.

A baseline serve como referencia para verificar se a solucao final realmente trouxe uma estrutura melhor do que uma divisao ingenua.

### Modelos candidatos

Para atender ao requisito do MVP, voce pode comparar pelo menos duas abordagens, por exemplo:

1. K-Means;
2. Agglomerative Clustering;
3. Gaussian Mixture Model, se quiser uma terceira opcao;
4. DBSCAN, se os dados tiverem clusters com densidade diferente.

### Sugestao pratica

Uma combinacao simples e defensavel seria:

- baseline: segmentacao manual por quartis de volatilidade;
- modelo 1: K-Means;
- modelo 2: Agglomerative Clustering;
- opcional: DBSCAN para investigar ruido e fundos atipicos.

### Justificativa dos modelos

- K-Means e simples, rapido e facil de explicar;
- Agglomerative Clustering ajuda a verificar se a estrutura hierarquica da base faz sentido;
- DBSCAN pode identificar pontos atipicos sem obrigar todos os fundos a pertencer a um grupo;
- a comparacao entre abordagens melhora a qualidade da analise.

### Visualizacoes sugeridas

- scatter plot com PCA em duas dimensoes;
- scatter plot com UMAP, se quiser um visual mais interessante;
- perfil medio de cada cluster por variavel;
- boxplots por cluster;
- tabela resumo com estatisticas de cada grupo.

---

## 5. Otimizacao de hiperparametros

### O que ajustar

Mesmo em clustering, e possivel fazer ajustes simples e bem justificados.

Exemplos:

- numero de clusters no K-Means;
- linkage no Agglomerative Clustering;
- eps e min_samples no DBSCAN;
- numero de componentes se usar PCA para visualizacao.

### Estrategia recomendada

Uma abordagem simples e adequada seria:

- testar diferentes valores de k no K-Means;
- usar elbow method e silhouette score como apoio;
- comparar com uma segunda tecnica de clusterizacao.

### O que justificar no texto

- por que esses hiperparametros foram escolhidos;
- como a melhor configuracao foi selecionada;
- se houve ganho de interpretabilidade ou de metricas internas;
- se a escolha final tambem faz sentido do ponto de vista de negocio.

---

## 6. Avaliacao dos resultados

### Metricias recomendadas

Como o problema e clusterizacao, use metricas apropriadas para avaliacao interna:

- silhouette score;
- Davies-Bouldin index;
- Calinski-Harabasz score;
- analise visual dos clusters em 2D;
- interpretacao economica ou financeira dos grupos.

### O que comparar

- baseline contra K-Means;
- K-Means contra Agglomerative;
- eventuais clusters com e sem outliers;
- estabilidade da segmentacao, se houver tempo para testar mais de uma configuracao.

### Como discutir os resultados

Explique:

- qual modelo gerou grupos mais consistentes;
- se os clusters sao interpretaveis;
- se os fundos de maior risco ficaram realmente juntos;
- se existe separacao clara entre fundos defensivos e fundos mais agressivos;
- se houve alguma sobreposicao relevante;
- se existem pontos muito atipicos que merecem atencao.

### Limites importantes

- metricas de clusterizacao nao garantem utilidade de negocio;
- clusters podem ser estatisticamente bons, mas pouco uteis na pratica;
- a interpretacao depende da qualidade das features;
- resultados podem mudar com outra janela temporal.

---

## 7. Conclusao do MVP

Ao final do notebook, escreva uma conclusao resumindo:

- o problema abordado;
- o dataset utilizado;
- os tratamentos realizados;
- os modelos avaliados;
- o melhor resultado encontrado;
- a justificativa para a escolha da melhor solucao;
- limitacoes do MVP;
- proximos passos.

Texto base sugerido:

Este MVP explorou a clusterizacao de fundos de investimento com base em dados publicos, combinando informacoes de mercado e caracteristicas de risco e retorno. A analise permitiu segmentar fundos com comportamentos semelhantes e criar uma leitura util para comparacao de pares e apoio a rotina de gestao. O melhor resultado foi obtido com o modelo que apresentou melhor equilibrio entre metricas internas e interpretabilidade. Como proximos passos, seria interessante expandir a base temporal, testar features adicionais e avaliar a estabilidade dos clusters ao longo do tempo.

---

## 8. Boas praticas esperadas

O notebook deve seguir boas praticas de organizacao, documentacao e reproducibilidade.

Recomendacoes:

- fixar seeds quando aplicavel;
- organizar o codigo em celulas claras;
- usar nomes de variaveis compreensiveis;
- evitar repeticao desnecessaria;
- criar funcoes para etapas repetidas;
- documentar decisoes importantes em texto;
- informar bibliotecas utilizadas;
- reportar tempo de execucao, quando relevante;
- garantir que o notebook rode do inicio ao fim sem erros.

---

## Sugestao de recorte final para o seu MVP

Se quiser manter o trabalho simples, defensavel e util, a versao mais enxuta e esta:

- tema: clusterizacao de fundos por risco e performance;
- dados: CVM + mercado publico;
- features: retorno, volatilidade, drawdown, patrimonio, cotistas e correlacao com benchmark;
- baseline: segmentacao manual por quartis;
- modelos: K-Means e Agglomerative Clustering;
- avaliacao: silhouette, Davies-Bouldin e analise visual;
- entrega: interpretacao de grupos e aplicacao pratica para a gestora.

Essa versao costuma ser forte para MVP porque e clara, executavel e conversa diretamente com a rotina de analise de investimentos.
