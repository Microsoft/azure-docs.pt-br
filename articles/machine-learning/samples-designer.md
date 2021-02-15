---
title: Pipelines de exemplo e conjuntos de dados para o designer
titleSuffix: Azure Machine Learning
description: Saiba como usar exemplos no designer do Azure Machine Learning para iniciar rapidamente seus pipelines de machine learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: likebupt
ms.author: keli19
ms.date: 10/14/2020
ms.custom: designer
ms.openlocfilehash: 4d594115139321c8a4ad2b3aa6b065474bd426fa
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/18/2021
ms.locfileid: "98556164"
---
# <a name="example-pipelines--datasets-for-azure-machine-learning-designer"></a>Pipelines e conjuntos de dados de exemplo para o designer do Azure Machine Learning

Use os exemplos internos do designer do Azure Machine Learning para começar rapidamente a criar os próprios pipelines de aprendizado de máquina. O [repositório GitHub](https://github.com/Azure/MachineLearningDesigner) do designer do Azure Machine Learning contém uma documentação detalhada para ajudar você a entender alguns cenários comuns de aprendizado de máquina.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tem uma assinatura do Azure, crie uma [conta gratuita](https://aka.ms/AMLFree)
* Um Workspace do Azure Machine Learning 

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="use-sample-pipelines"></a>Usar pipelines de exemplo

O designer salva uma cópia dos pipelines de exemplo no seu workspace do estúdio. Você pode editar o pipeline para adaptá-lo às suas necessidades e salvá-lo como o próprio. Use-os como um ponto de partida para iniciar rapidamente seus projetos.

Veja como usar um exemplo de designer:

1. Entre em <a href="https://ml.azure.com?tabs=jre" target="_blank">ml.azure.com</a> e selecione o workspace com o qual deseja trabalhar.

1. Selecione **Designer**.

1. Selecione um pipeline de exemplo na seção **Novo pipeline**.

    Selecione **Mostrar mais amostras** para obter uma lista completa de amostras.

1. Para executar um pipeline, primeiro defina o destino de computação padrão no qual executar o pipeline.

   1. No painel **Configurações** à direita da tela, selecione **Selecionar de destino de computação**.

   1. Na caixa de diálogo exibida, selecione um destino de computação existente ou crie um. Clique em **Salvar**.

   1. Selecione **Enviar** na parte superior da tela para enviar uma execução de pipeline.

   Dependendo do pipeline de exemplo e das configurações de computação, as execuções podem levar algum tempo para serem concluídas. As configurações de computação padrão têm um tamanho de nó mínimo de 0, o que significa que o designer precisa alocar recursos depois de ficar ocioso. Execuções de pipeline repetidas levarão menos tempo, já que os recursos de computação já estão alocados. Além disso, o designer usa resultados armazenados em cache para cada módulo para melhorar ainda mais a eficiência.


1. Depois que a execução do pipeline for concluída, você poderá examinar o pipeline e exibir a saída de cada módulo para saber mais. Use as seguintes etapas para exibir as saídas do módulo:

   1. Clique com o botão direito do mouse no módulo na tela cuja saída você gostaria de ver.
   1. Selecione **Visualizar**.


   Use os exemplos como pontos de partida para alguns dos cenários de aprendizado de máquina mais comuns.

## <a name="regression"></a>Regressão

Explore esses exemplos internos de regressão.

| Titulo do exemplo | Descrição | 
| --- | --- |
| [Regressão – previsão de preços de automóveis (básica)](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/regression-automobile-price-prediction-basic.md) | Preveja os preços de carros usando a regressão linear. |
| [Regressão – previsão de preço de automóveis (avançada)](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/regression-automobile-price-prediction-compare-algorithms.md) | Preveja os preços de carros usando a floresta de decisão e os regressores de árvore de decisão aumentada. Compare os modelos para encontrar o melhor algoritmo.

## <a name="classification"></a>classificação

Explore esses exemplos internos de classificação. Saiba mais sobre os exemplos abrindo-os e vendo os comentários do módulo no designer.

| Titulo do exemplo | Descrição | 
| --- | --- |
| [Classificação binária com seleção de recursos – previsão de receita](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/binary-classification-feature-selection-income-prediction.md) | Preveja a receita como alta ou baixa usando uma árvore de decisão aumentada de duas classes. Use a correlação de Pearson para selecionar recursos.
| [Classificação binária com script Python personalizado – previsão de risco de crédito](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/binary-classification-python-credit-prediction.md) | Classifique os pedidos de crédito como de alto ou baixo risco. Use o módulo Executar Script Python para ponderar seus dados.
| [Classificação binária – previsão de relacionamento com o cliente](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/binary-classification-customer-relationship-prediction.md) | Preveja a rotatividade de clientes usando árvores de decisão aumentadas de duas classes. Use o SMOTE para fazer a amostragem dos dados polarizados.
| [Classificação de textos – conjunto de dados SP 500 da Wikipédia](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/text-classification-wiki.md) | Classifique os tipos de empresas com base nos artigos da Wikipédia com a regressão logística multiclasse. |
| Classificação multiclasse – reconhecimento de letras | Crie um conjunto de classificadores binários para classificar letras escritas. |

## <a name="computer-vision"></a>Visual computacional

Explore esses exemplos de pesquisa visual computacional internos. Saiba mais sobre os exemplos abrindo-os e vendo os comentários do módulo no designer.

| Titulo do exemplo | Descrição | 
| --- | --- |
| Classificação de imagem usando DenseNet | Use módulos da pesquisa visual computacional para criar o modelo de classificação de imagem com base no PyTorch DenseNet.| 

## <a name="recommender"></a>Sistema de recomendação

Explore esses exemplos internos de sistema de recomendação. Saiba mais sobre os exemplos abrindo-os e vendo os comentários do módulo no designer.

| Titulo do exemplo | Descrição | 
| --- | --- |
| Recomendação baseada em Wide & Deep – previsão de classificação de restaurante | Crie um mecanismo de recomendação de restaurante usando avaliações e recursos do usuário do restaurante/usuário.|
| recomendação – tweets de classificação de filmes | Crie um mecanismo de recomendação de filmes com base nas classificações e recursos dos usuários/filmes.|

## <a name="utility"></a>Utilitário

Saiba mais sobre as amostras que descrevem os recursos e os utilitários de aprendizado de máquina. Saiba mais sobre os exemplos abrindo-os e vendo os comentários do módulo no designer.

| Titulo do exemplo | Descrição | 
| --- | --- |
| Classificação binária usando modelo do Vowpal Wabbit – previsão de renda adulta | O Vowpal Wabbit é um sistema de machine learning que vai além dos limites do machine learning com técnicas como aprendizado online, hashing, allreduce, reductions, learning2search, ativo e interativo. Este exemplo mostra como usar o modelo Vowpal Wabbit para criar um modelo de classificação binária. 
| [Usar um script R personalizado – previsão de atraso de voo](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/r-script-flight-delay-prediction.md) | Use o script do R personalizado para prever se um voo de passageiros agendado sofrerá um atraso superior a 15 minutos.
| validação cruzada para classificação binária – previsão de receita de adulto | Use a validação cruzada para criar um classificador binário para a receita de adulto.
| Importância do recurso de permuta | Use a importância do recurso de permuta para computar pontuações de importância para o conjunto de dados de teste. 
| ajustar parâmetros para classificação binária – previsão de receita de adulto | Use os hiperparâmetros do modelo de ajuste para encontrar hiperparâmetros ideais para a criação de um classificador binário. |

## <a name="datasets"></a>Conjunto de dados

Ao criar um pipeline no designer do Azure Machine Learning, diversos conjuntos de dados de exemplo são incluídos por padrão. Esses conjuntos de dados de exemplo são usados pelos pipelines de exemplo na home page do designer. 

Os conjuntos de dados de exemplo estão disponíveis na categoria **Conjuntos de Dados**-**Amostras**. Encontre-a na paleta do módulo à esquerda da tela do designer. Você pode usar qualquer um desses conjuntos de dados no próprio pipeline arrastando-o para a tela.

| Nome do&nbsp;conjunto de dados&nbsp;&nbsp;&nbsp;&nbsp;| Descrição do conjunto de dados |
|-------------|:--------------------|
| Conjunto de dados de classificação binária de receita no recenseamento adulto | Um subconjunto do banco de dados do recenseamento de 1994, usando adultos em fase de trabalho, com idade acima de 16 anos com um índice de receita ajustado de > 100.<br/>**Uso**: classificar pessoas usando dados demográficos para prever se uma pessoa recebe acima de 50 mil por ano.<br/> **Pesquisa relacionada**: Kohavi, R., Becker, B., (1996). [UCI Machine Learning Repository](https://archive.ics.uci.edu/ml). Irvine, CA: University of California, School of Information and Computer Science|
|Dados de preço de automóvel (Brutos)|Informações sobre automóveis por marca e modelo, incluindo o preço, recursos como número de cilindradas e MPG, bem como uma pontuação de risco de seguro.<br/> A pontuação de risco é inicialmente associada com preço automático. Depois ela é ajustada ao risco real em um processo conhecido pelos atuários como valor simbólico. Um valor de +3 indica que o automóvel apresenta risco e um valor de -3 indica que ele provavelmente é seguro.<br/>**Uso**: prever a pontuação de risco por recursos, usando a regressão ou a classificação multivariada.<br/>**Pesquisa relacionada**: Schlimmer, J.C. (1987). [UCI Machine Learning Repository](https://archive.ics.uci.edu/ml). Irvine, CA: University of California, School of Information and Computer Science. |
| Rótulos de apetência CRM compartilhados |Rótulos do desafio de previsão de relacionamento com o cliente KDD Cup 2009 ([orange_small_train_appetency.labels](http://www.sigkdd.org/site/2009/files/orange_small_train_appetency.labels)).|
|Rótulos de variação CRM compartilhados|Rótulos do desafio de previsão de relacionamento com o cliente KDD Cup 2009 ([orange_small_train_churn.labels](http://www.sigkdd.org/site/2009/files/orange_small_train_churn.labels)).|
|Conjunto de dados CRM compartilhado | Estes dados foram obtidos do desafio de previsão de relacionamento com o cliente KDD Cup 2009 ([orange_small_train.data.zip](http://www.sigkdd.org/site/2009/files/orange_small_train.data.zip)). <br/>Esse conjunto de dados contém 50 mil clientes da empresa francesa de telecomunicações Orange. Cada cliente possui 230 recursos anônimos, dos quais 190 são numéricos e 40 categóricos. Os recursos são muito esparsos. |
|Rótulos de vendas agregadas CRM compartilhados|Rótulos do desafio de previsão de relacionamento com o Cliente do KDD Cup 2009 ([orange_large_train_upselling.labels](http://www.sigkdd.org/site/2009/files/orange_large_train_upselling.labels)|
|Dados de atrasos de voo|Dados de desempenho pontual do voo do passageiro obtidos da coleção de dados TranStats do Departamento de Transportes dos EUA ([On-Time](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time)).<br/>O conjunto de dados abrange o período de abril a outubro de 2013. Antes do upload no designer, o conjunto de dados foi processado da seguinte forma: <br/>– O conjunto de dados foi filtrado para abranger somente os 70 aeroportos mais movimentados dos EUA continentais <br/>– Os voos cancelados foram rotulados como atrasados por mais de 15 minutos <br/>– Os voos desviados foram filtrados <br/>– As seguintes colunas foram selecionadas: Year, Month, DayofMonth, DayOfWeek, Carrier, OriginAirportID, DestAirportID, CRSDepTime, DepDelay, DepDel15, CRSArrTime, ArrDelay, ArrDel15, Canceled|
|Conjunto de dados do cartão de crédito alemão UCI|O conjunto de dados UCI Statlog (cartão de crédito alemão) ([Statlog+German+Credit+Data](https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data))), usando o arquivo german.data.<br/>O conjunto de dados classifica pessoas, descritas como um conjunto de atributos, como baixos ou altos riscos de crédito. Cada exemplo representa uma pessoa. Há 20 recursos, ambos numéricos e categóricos, e um rótulo binário (o valor de risco de crédito). Entradas de risco de crédito alto têm o rótulo = 2, entradas de risco de crédito baixo têm o rótulo = 1. O custo de classificar incorretamente um exemplo de risco baixo como alto é 1, considerando que o custo de classificar incorretamente um exemplo de risco alto como baixo é 5.|
|Títulos de filmes no IMDB|O conjunto de dados contém informações sobre filmes que foram classificados em tweets do Twitter: ID do filme no IMDB, nome do filme, gênero e ano de produção. Há 17 mil filmes no conjunto de dados. O conjunto de dados foi introduzido no artigo “S. Dooms, T. De Pessemier e L. Martens. MovieTweetings: um conjunto de dados de classificação de filmes coletado do Twitter. Oficina de crowdsourcing and computação humana para sistemas recomendados, CrowdRec em RecSys 2013."|
|Classificação de filmes|O conjunto de dados é uma versão estendida do conjunto de dados de Movie Tweetings. O conjunto de dados tem as classificações de 170 mil de filmes, extraídos de tweets bem estruturados no Twitter. Cada instância representa um tweet e é uma tupla: ID de usuário, ID de filme no IMDB, classificação, carimbo de data/hora, número de favoritos para este tweet e número de retweets deste tweet. O conjunto de dados foi disponibilizado por A. Said, S. Dooms, B. Loni e D. Tikk para desafio de sistemas de recomendação 2014.|
|Conjunto de dados de clima|Observações climáticas terrestres de hora em hora da NOAA ([dados mesclados de 201304 para 201310](https://az754797.vo.msecnd.net/data/WeatherDataset.csv)).<br/>Os dados de clima abrangem observações feitas de estações meteorológica de aeroportos, que abrangem o período de abril a outubro de 2013. Antes do upload no designer, o conjunto de dados foi processado da seguinte forma:    <br/> – As IDs das estações meteorológicas foram mapeadas para as IDs dos aeroportos correspondentes    <br/> – As estações meteorológicas não associadas aos 70 aeroportos mais movimentados foram filtradas    <br/> – A coluna Data foi dividida nas colunas separadas Ano, Mês e Dia    <br/> – As seguintes colunas foram selecionadas: AirportID, Year, Month, Day, Time, TimeZone, SkyCondition, Visibility, WeatherType, DryBulbFarenheit, DryBulbCelsius, WetBulbFarenheit, WetBulbCelsius, DewPointFarenheit, DewPointCelsius, RelativeHumidity, WindSpeed, WindDirection, ValueForWindCharacter, StationPressure, PressureTendency, PressureChange, SeaLevelPressure, RecordType, HourlyPrecip, Altimeter|
|Conjunto de dados da SP 500 da Wikipédia|Os dados foram extraídos da Wikipédia (https://www.wikipedia.org/), com base em artigos de cada empresa S&P 500, armazenados como dados XML.    <br/>Antes do upload no designer, o conjunto de dados foi processado da seguinte forma:    <br/> – Extração do conteúdo de texto de cada empresa específica    <br/> – Remoção da formatação do wiki    <br/> – Remoção de caracteres não alfanuméricos    <br/> – Conversão de todo o texto em minúsculas    <br/> – Adição de categorias de empresas conhecidas    <br/>Observe que, para algumas empresas, um artigo não foi encontrado, assim, o número de registros é menor que 500.|
|Dados de recurso de restaurante| Um conjunto de metadados sobre restaurantes e seus recursos, como tipo de comida, estilo de jantar e localização. <br/>**Uso**: use esse conjunto de dados, em combinação com os outros dois conjuntos de dados de restaurantes, para treinar e testar um sistema de recomendação.<br/> **Pesquisa relacionada**: Bache, K. e Lichman, M. (2013). [UCI Machine Learning Repository](https://archive.ics.uci.edu/ml). Irvine, CA: University of California, School of Information and Computer Science.|
|Classificação de restaurantes| Contém a classificação fornecida por usuários de restaurantes em uma escala de 0 a 2.<br/>**Uso**: use esse conjunto de dados, em combinação com os outros dois conjuntos de dados de restaurantes, para treinar e testar um sistema de recomendação. <br/>**Pesquisa relacionada**: Bache, K. e Lichman, M. (2013). [UCI Machine Learning Repository](https://archive.ics.uci.edu/ml). Irvine, CA: University of California, School of Information and Computer Science.|
|Dados do cliente dos restaurantes| Um conjunto de metadados sobre consumidores, incluindo demografia e preferências. <br/>**Uso**: use esse conjunto de dados, em combinação com os outros dois conjuntos de dados de restaurantes, para treinar e testar um sistema de recomendação. <br/> **Pesquisa relacionada**: Bache, K. e Lichman, M. (2013). [UCI Machine Learning Repository](https://archive.ics.uci.edu/ml) Irvine, CA: University of California, School of Information and Computer Science.|

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Conheça os conceitos fundamentais da análise preditiva e do machine learning com o [Tutorial: Prever preço de automóvel com o designer](tutorial-designer-automobile-price-train-score.md)

