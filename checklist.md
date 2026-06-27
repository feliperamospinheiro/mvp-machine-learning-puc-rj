# Checklist - MVP Machine Learning

## Definição do problema

- Qual é a descrição do problema?
- Qual é o objetivo do modelo?
- O problema é de classificação, regressão, clusterização, previsão de séries temporais ou outro tipo?
- Por que esse problema pode ser resolvido com Machine Learning?
- Você tem premissas ou hipóteses sobre o problema? Quais?
- Que restrições ou condições foram consideradas na escolha dos dados?
  
## Descrição dos dados

- Qual dataset foi utilizado?
- Qual é a fonte dos dados?
- Como os dados foram carregados no notebook?
- Quantos registros e atributos existem?
- Quais são os principais atributos?
- Existe variável-alvo? Se sim, qual?
- Há limitações conhecidas no dataset?
  
## Preparação dos dados

- Houve valores ausentes? Como foram tratados?
- Houve necessidade de remover ou transformar atributos?
- Foram criados novos atributos?
- Foram aplicadas normalização, padronização, codificação ou outras transformações?
- Houve preocupação com vazamento de dados?
- As transformações foram aplicadas de forma adequada à divisão treino/teste?
  
## Divisão dos dados

- Como os dados foram separados?
- Foi usada divisão treino/teste?
- Foi usada validação ou validação cruzada?
- A estratégia de divisão é adequada para o tipo de problema?
- No caso de séries temporais, a ordem temporal foi respeitada?
- No caso de clusterização, como a avaliação foi planejada?
  
## Modelagem

- Qual foi o baseline utilizado?
- Quais modelos foram treinados?
- Por que esses modelos foram escolhidos?
- Os modelos foram comparados de forma justa?
- Foi observado algum indício de underfitting?
- Algum modelo apresentou sinais de overfitting?
  
## Otimização

- Algum modelo teve hiperparâmetros ajustados?
- Quais hiperparâmetros foram escolhidos?
- Qual estratégia de busca foi usada?
- O ajuste trouxe melhora em relação à versão inicial?
- A otimização foi feita sem usar indevidamente os dados de teste?
  
## Avaliação

- Quais métricas foram utilizadas?
- Por que essas métricas são adequadas?
- Qual modelo teve melhor desempenho?
- Os resultados fazem sentido para o problema?
- Houve análise de erros?
- Quais são as principais limitações da solução?
  
## Conclusão

- Qual foi a melhor solução encontrada?
- Por que ela foi escolhida?
- O MVP cumpriu o objetivo definido no início?
- Quais seriam os próximos passos para melhorar o projeto?