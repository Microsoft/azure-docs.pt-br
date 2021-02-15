---
title: Integrar o Azure Stream Analytics com o Azure Machine Learning
description: Este artigo descreve como integrar um trabalho do Azure Stream Analytics com os modelos do Azure Machine Learning.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/21/2020
ms.custom: devx-track-js
ms.openlocfilehash: c35d5d2f63f4a7abe80a0ff19e5994013355c386
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98020512"
---
# <a name="integrate-azure-stream-analytics-with-azure-machine-learning-preview"></a>Integrar o Azure Stream Analytics com o Azure Machine Learning (versão prévia)

Você pode implementar modelos de aprendizado de máquina como um UDF (função definida pelo usuário) em seus trabalhos do Azure Stream Analytics para fazer previsões e estimativas em tempo real em seus dados de entrada de streaming. O [Azure Machine Learning](../machine-learning/overview-what-is-azure-ml.md) permite que você use qualquer ferramenta de open-source popular, como Tensorflow, scikit-learn ou PyTorch, para preparar, treinar e implantar modelos.

## <a name="prerequisites"></a>Pré-requisitos

Conclua as etapas a seguir antes de adicionar um modelo de machine learning como uma função do seu trabalho de Stream Analytics:

1. Use o Azure Machine Learning para [implantar seu modelo como um serviço Web](../machine-learning/how-to-deploy-and-where.md).

2. Seu script de pontuação deve ter [entradas e saídas de exemplos](../machine-learning/how-to-deploy-and-where.md) que são usadas pelo Azure Machine Learning para gerar uma especificação de esquema. O Stream Analytics usa o esquema para entender a assinatura de função do serviço Web. Você pode usar esta [definição de Swagger de exemplo](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/AzureML/swagger-example.json) como uma referência para garantir que você a configurou corretamente.

3. Verifique se o serviço Web aceita e retorna dados serializados JSON.

4. Implante seu modelo no [Serviço de Kubernetes do Azure](../machine-learning/how-to-deploy-and-where.md#choose-a-compute-target) para implantações de produção em grande escala. Se o serviço Web não puder lidar com o número de solicitações provenientes de seu trabalho, o desempenho do seu trabalho do Stream Analytics será degradado, o que afeta a latência. Modelos implantados em Instâncias de Contêiner do Azure têm suporte apenas quando você usa o portal do Azure. Os modelos criados usando o [Designer de Azure Machine Learning](../machine-learning/concept-designer.md) ainda não têm suporte no Stream Analytics.

## <a name="add-a-machine-learning-model-to-your-job"></a>Adicionar um modelo de machine learning ao seu trabalho

Você pode adicionar Azure Machine Learning funções ao seu trabalho de Stream Analytics diretamente do portal do Azure ou Visual Studio Code.

### <a name="azure-portal"></a>Portal do Azure

1. Navegue até o trabalho de Stream Analytics no portal do Azure e selecione **Funções** em **Topologia de trabalho**. Em seguida, selecione **serviço Azure Machine Learning** no menu suspenso **+ Adicionar** .

   ![Adicionar Azure Machine Learning UDF](./media/machine-learning-udf/add-azure-machine-learning-udf.png)

2. Preencha o formulário **Função de serviço do Azure Machine Learning** com os seguintes valores de propriedade:

   ![Configurar UDF Azure Machine Learning](./media/machine-learning-udf/configure-azure-machine-learning-udf.png)

### <a name="visual-studio-code"></a>Visual Studio Code

1. Abra seu projeto Stream Analytics no Visual Studio Code e clique com o botão direito do mouse na pasta **funções** . Em seguida, escolha **Adicionar função**. Selecione **Machine Learning UDF** na lista suspensa.

   :::image type="content" source="media/machine-learning-udf/visual-studio-code-machine-learning-udf-add-function.png" alt-text="Adicionar UDF no VS Code":::

   :::image type="content" source="media/machine-learning-udf/visual-studio-code-machine-learning-udf-add-function-2.png" alt-text="Adicionar Azure Machine Learning UDF no VS Code":::

2. Insira o nome da função e preencha as configurações no arquivo de configuração usando **selecionar em suas assinaturas** no CodeLens.

   :::image type="content" source="media/machine-learning-udf/visual-studio-code-machine-learning-udf-function-name.png" alt-text="Selecione Azure Machine Learning UDF no VS Code":::

   :::image type="content" source="media/machine-learning-udf/visual-studio-code-machine-learning-udf-configure-settings.png" alt-text="Configurar o UDF Azure Machine Learning no VS Code":::

A tabela a seguir descreve cada propriedade de Azure Machine Learning funções de serviço no Stream Analytics.

|Propriedade|Descrição|
|--------|-----------|
|Alias da função|Insira um nome para invocar a função em sua consulta.|
|Subscription|Sua assinatura do Azure..|
|Workspace do Azure Machine Learning|O workspace do Azure Machine Learning usado para implantar seu modelo como um serviço Web.|
|Implantações|O serviço Web que hospeda seu modelo.|
|Assinatura de função|A assinatura do serviço Web inferida da especificação de esquema da API. Se a sua assinatura não for carregada, verifique se você forneceu a entrada e a saída de exemplo em seu script de pontuação para gerar automaticamente o esquema.|
|Número de solicitações paralelas por partição|Essa é uma configuração avançada para otimizar a taxa de transferência de alta escala. Esse número representa as solicitações simultâneas enviadas de cada partição do seu trabalho para o serviço Web. Trabalhos com seis SUs (unidades de streaming) ou menos têm uma partição. Trabalhos com 12 SUs têm duas partições, com 18 SUs tem três partições e assim por diante.<br><br> Por exemplo, se seu trabalho tiver duas partições e você definir esse parâmetro como quatro, haverá oito solicitações simultâneas de seu trabalho para o serviço Web. Neste momento da versão prévia pública, esse valor usa como padrão 20 e não pode ser atualizado.|
|Contagem máxima do lote|Essa é uma configuração avançada para otimização da taxa de transferência de alta escala. Esse número representa o número máximo de eventos que serão agrupados em lote em uma única solicitação enviada ao serviço Web.|

## <a name="supported-input-parameters"></a>Parâmetros de entrada compatíveis

Quando a consulta de Stream Analytics invoca um UDF do Azure Machine Learning, o trabalho cria uma solicitação JSON serializada para o serviço Web. A solicitação é baseada em um esquema específico do modelo. Você precisa fornecer um exemplo de entrada e saída em seu script de pontuação para [gerar automaticamente um esquema](../machine-learning/how-to-deploy-and-where.md). O esquema permite que o Stream Analytics construa a solicitação JSON serializada para qualquer um dos tipos de dados com suporte, como numpy, pandas e PySpark. Vários eventos de entrada podem ser agrupados em lote em uma única solicitação.

A consulta do Stream Analytics a seguir é um exemplo de como invocar um UDF do Azure Machine Learning:

```SQL
SELECT udf.score(<model-specific-data-structure>)
INTO output
FROM input
```

O Stream Analytics só dá suporte à passagem de um parâmetro para funções do Azure Machine Learning. Talvez seja necessário preparar seus dados antes de passá-los como uma entrada para o UDF de aprendizado de máquina. Você deve garantir que a entrada para UDF de ML não seja nula, pois entradas nulas farão com que o trabalho falhe.

## <a name="pass-multiple-input-parameters-to-the-udf"></a>Passar vários parâmetros de entrada para o UDF

Os exemplos mais comuns de entradas para modelos de aprendizado de máquina são matrizes e DataFrames de numpy. Você pode criar uma matriz usando um UDF JavaScript e criar um DataFrame serializado em JSON usando a cláusula `WITH`.

### <a name="create-an-input-array"></a>Crie uma matriz de entrada

Você pode criar um UDF de JavaScript que aceita *N* números de entradas e cria uma matriz que pode ser usada como entrada para o UDF do Azure Machine Learning.

```javascript
function createArray(vendorid, weekday, pickuphour, passenger, distance) {
    'use strict';
    var array = [vendorid, weekday, pickuphour, passenger, distance]
    return array;
}
```

Depois de adicionar o UDF do JavaScript ao seu trabalho, você pode invocar seu UDF do Azure Machine Learning usando a seguinte consulta:

```SQL
WITH 
ModelInput AS (
#use JavaScript UDF to construct array that will be used as input to ML UDF
SELECT udf.createArray(vendorid, weekday, pickuphour, passenger, distance) as inputArray
FROM input
)

SELECT udf.score(inputArray)
INTO output
FROM ModelInput
#validate inputArray is not null before passing it to ML UDF to prevent job from failing
WHERE inputArray is not null
```

O JSON a seguir é uma solicitação de exemplo:

```JSON
{
    "data": [
        ["1","Mon","12","1","5.8"],
        ["2","Wed","10","2","10"]
    ]
}
```

### <a name="create-a-pandas-or-pyspark-dataframe"></a>Criar um DataFrame do Pandas ou PySpark

Você pode usar a cláusula `WITH` para criar um DataFrame JSON serializado que pode ser passado como entrada para o UDF do Azure Machine Learning, conforme mostrado abaixo.

A consulta a seguir cria um DataFrame selecionando os campos necessários e usa o DataFrame como entrada para o UDF do Azure Machine Learning.

```SQL
WITH 
Dataframe AS (
SELECT vendorid, weekday, pickuphour, passenger, distance
FROM input
)

SELECT udf.score(Dataframe)
INTO output
FROM Dataframe
```

O JSON a seguir é uma solicitação de exemplo da consulta anterior:

```JSON
{
    "data": [{
            "vendorid": "1",
            "weekday": "Mon",
            "pickuphour": "12",
            "passenger": "1",
            "distance": "5.8"
        }, {
            "vendorid": "2",
            "weekday": "Tue",
            "pickuphour": "10",
            "passenger": "2",
            "distance": "10"
        }
    ]
}
```

## <a name="optimize-the-performance-for-azure-machine-learning-udfs"></a>Otimizar o desempenho dos UDFs do Azure Machine Learning

Ao implantar seu modelo no Serviço de Kubernetes do Azure, você pode [fazer o perfil do seu modelo para determinar a utilização de recursos](../machine-learning/how-to-deploy-profile-model.md). Você também pode [habilitar o App Insights nas suas implantações](../machine-learning/how-to-enable-app-insights.md) para entender as taxas de solicitação, os tempos de resposta e as tarifas de falha.

Se você tiver um cenário com alta taxa de transferência de eventos, talvez seja necessário alterar os seguintes parâmetros no Stream Analytics para obter o desempenho ideal com baixas latências de ponta a ponta:

1. Contagem de lote máxima.
2. Número de solicitações paralelas por partição.

### <a name="determine-the-right-batch-size"></a>Determinar o tamanho do lote certo

Depois de implantar seu serviço Web, envie uma solicitação de exemplo com tamanhos de lote variados iniciando em 50 e aumentando-o na ordem de centenas. Por exemplo, 200, 500, 1000, 2000 e assim por diante. Você observará que, após um determinado tamanho de lote, aumentará a latência da resposta. O ponto após o qual aumenta a latência de resposta deve ser a contagem de lotes máxima para o seu trabalho.

### <a name="determine-the-number-of-parallel-requests-per-partition"></a>Determinar o número de solicitações paralelas por partição

No dimensionamento ideal, seu trabalho no Stream Analytics deve ser capaz de enviar várias solicitações paralelas para o serviço Web e obter uma resposta em poucos milissegundos. A latência da resposta do serviço Web pode afetar diretamente a latência e o desempenho de seu trabalho no Stream Analytics. Se a chamada do seu trabalho para o serviço Web demorar muito tempo, você provavelmente verá um aumento no atraso da marca-d'água e também poderá ver um aumento no número de eventos de entrada de lista de pendências.

Para evitar essa latência, verifique se o cluster AKS (Serviço de Kubernetes do Azure) foi provisionado com o [número correto de nós e réplicas](../machine-learning/how-to-deploy-azure-kubernetes-service.md#using-the-cli). É fundamental que seu serviço Web esteja altamente disponível e retorne respostas com êxito. Se seu trabalho receber uma resposta de serviço indisponível (503) de seu serviço Web, ele será repetido continuamente com a retirada exponencial. Qualquer resposta diferente de sucesso (200) e serviço indisponível (503) fará com que seu trabalho vá para um estado de falha.

## <a name="next-steps"></a>Próximas etapas

* [Tutorial: Funções definidas pelo usuário do JavaScript do Azure Stream Analytics](stream-analytics-javascript-user-defined-functions.md)
* [Dimensionar seu trabalho do Stream Analytics com funções do Azure Machine Learning Studio (clássico)](stream-analytics-scale-with-machine-learning-functions.md)
