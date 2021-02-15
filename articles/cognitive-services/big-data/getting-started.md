---
title: Introdução aos Serviços Cognitivos para Big Data
description: Configure seu pipeline do MMLSpark com Serviços Cognitivos no Azure Databricks e execute um exemplo.
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.topic: quickstart
ms.date: 07/06/2020
ms.author: marhamil
ms.openlocfilehash: 095f2c3ed17042bb616fb091d1af52a64c913709
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96460506"
---
# <a name="getting-started"></a>Introdução

Configurar seu ambiente é a primeira etapa para criar um pipeline para seus dados. Depois que o ambiente estiver pronto, a execução de um exemplo é rápida e fácil.

Neste artigo, executaremos estas etapas para começar:

1. [Criar um recurso dos Serviços Cognitivos](#create-a-cognitive-services-resource)
1. [Criar u-m cluster do Apache Spark](#create-an-apache-spark-cluster)
1. [Experimente um exemplo](#try-a-sample)

## <a name="create-a-cognitive-services-resource"></a>Criar um recurso dos Serviços Cognitivos

Para usar os Serviços Cognitivos de Big Data, primeiro devemos criar um Serviço Cognitivo para nosso fluxo de trabalho. Há dois tipos principais de Serviços Cognitivos: serviços de nuvem hospedados em serviços do Azure e em contêineres gerenciados pelos usuários. É recomendável começar com os Serviços Cognitivos baseados em nuvem mais simples.

### <a name="cloud-services"></a>Serviços de Nuvem

Os Serviços Cognitivos baseados em nuvem são algoritmos inteligentes hospedados no Azure. Esses serviços estão prontos para uso sem treinamento, você só precisa de uma conexão com a Internet. Você pode [criar um Serviço Cognitivo no portal do Azure](../cognitive-services-apis-create-account.md?tabs=multiservice%2Cwindows) ou com a [CLI do Azure](../cognitive-services-apis-create-account-cli.md?tabs=windows).

### <a name="containerized-services-optional"></a>Serviços em contêineres (opcional)

Caso o seu aplicativo ou sua carga de trabalho use conjuntos de dados grandes, exija uma rede privada ou não possa entrar em contato com a nuvem, a comunicação com os serviços de nuvem pode ser impossível. Nessa situação, os Serviços Cognitivos em contêineres têm estes benefícios:

* **Baixa Conectividade**: você pode implantar Serviços Cognitivos em contêineres em qualquer ambiente computacional, tanto na nuvem quanto fora dela. Se o seu aplicativo não puder entrar em contato com a nuvem, considere implantar Serviços Cognitivos em contêineres em seu aplicativo.

* **Baixa Latência**: como os serviços em contêineres não exigem a comunicação de ida e volta para a nuvem, as respostas são retornadas com latências muito menores.

* **Privacidade e Segurança de Dados**: você pode implantar serviços em contêineres em redes privadas, para que os dados confidenciais não saiam da rede.

* **Alta Escalabilidade**: os serviços em contêineres não têm "limites de taxa" e são executados em computadores gerenciados pelo usuário. Portanto, você pode dimensionar Serviços Cognitivos sem fim para lidar com cargas de trabalho muito maiores.

Siga [este guia](../cognitive-services-container-support.md?tabs=luis) para criar um Serviços Cognitivos em contêineres.

## <a name="create-an-apache-spark-cluster"></a>Criar um cluster do Apache Spark

O [Apache Spark&trade;](http://spark.apache.org/) é uma estrutura de computação distribuída projetada para processamento de dados de Big Data. Os usuários podem trabalhar com o Apache Spark no Azure com serviços como Azure Databricks, Azure Synapse Analytics, HDInsight e Serviços de Kubernetes do Azure. Para usar os Serviços Cognitivos de Big Data, primeiro devemos criar um cluster. Se você já tiver um cluster do Spark, sinta-se à vontade para experimentar um exemplo.

### <a name="azure-databricks"></a>Azure Databricks

O Azure Databricks é uma plataforma de análise baseada em Apache Spark com uma instalação com um clique, fluxos de trabalho simplificados e um workspace interativo. Geralmente, ele é usado para colaborar entre cientistas de dados, engenheiros e analistas de negócios. Para usar os Serviços Cognitivos de Big Data no Azure Databricks, siga estas etapas:

1. [Crie um workspace do Azure Databricks](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal#create-an-azure-databricks-workspace)
1. [Criar um cluster Spark no Databricks](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal#create-a-spark-cluster-in-databricks)
1. Instalar os Serviços Cognitivos de Big Data
    * Criar uma biblioteca em seu workspace do Databricks  
       <img src="media/create-library.png" alt="Create library" width="50%"/>
    * Insira as seguintes coordenadas de coordenadas do Maven:  `com.microsoft.ml.spark:mmlspark_2.11:1.0.0-rc3` Repositório:`https://mmlspark.azureedge.net/maven`  
      <img src="media/library-coordinates.png" alt="Library Coordinates" width="50%"/>
    * instalar a biblioteca em um cluster  
      <img src="media/install-library.png" alt="Install Library on Cluster" width="50%"/>

### <a name="azure-synapse-analytics-optional"></a>Azure Synapse Analytics (opcional)

Opcionalmente, você pode usar o Synapse Analytics para criar um cluster do Spark. O Azure Synapse Analytics reúne o data warehousing e a análise de Big Data empresariais. Ele oferece a liberdade para consultar dados da forma que você quiser, usando recursos sob demanda sem servidor ou provisionados em escala. Para começar a usar o Azure Synapse Analytics, siga estas etapas:

1. [Criar um Workspace do Synapse (versão prévia)](../../synapse-analytics/quickstart-create-workspace.md).
1. [Criar um Pool sem servidor do Apache Spark (versão prévia) usando o portal do Azure](../../synapse-analytics/quickstart-create-apache-spark-pool-portal.md).

No Azure Synapse Analytics, o Big Data para Serviços Cognitivos está instalado por padrão.

### <a name="azure-kubernetes-service"></a>Serviço de Kubernetes do Azure

Se você estiver usando Serviços Cognitivos em contêineres, uma opção popular para implantar o Spark junto com contêineres é o Serviço de Kubernetes do Azure.

Para começar a usar o Serviço de Kubernetes do Azure, siga estas etapas:

1. [Implantar um cluster do AKS (Serviço de Kubernetes do Azure) usando o portal do Azure](../../aks/kubernetes-walkthrough-portal.md)
1. [Instalar o gráfico do helm do Apache Spark 2.4.0](https://hub.helm.sh/charts/microsoft/spark)
1. [Instalar um contêiner de Serviços Cognitivos usando Helm](../computer-vision/deploy-computer-vision-on-premises.md)

## <a name="try-a-sample"></a>Experimente um exemplo

Depois de configurar o cluster e o ambiente do Spark, podemos executar um breve exemplo. Esta seção demonstra como usar Big Data para Serviços Cognitivos no Azure Databricks.

Primeiro, podemos criar um notebook no Azure Databricks. Para outros provedores de cluster do Spark, use seus notebooks ou o Spark Submit.

1. Crie um notebook do Databricks escolhendo **Novo notebook** no menu **Azure Databricks**.

    <img src="media/new-notebook.png" alt="Create a new notebook" width="50%"/>

1. Na caixa de diálogo **Criar Notebook**, insira um nome, escolha **Python** como a linguagem e escolha o cluster Spark criado anteriormente.

    <img src="media/databricks-notebook-details.jpg" alt="New notebook details" width="50%"/>

    Selecione **Criar**.

1. Cole este snippet de código no novo notebook.

```python
from mmlspark.cognitive import *
from pyspark.sql.functions import col

# Add your subscription key from Text Analytics (or a general Cognitive Service key)
service_key = "ADD-SUBSCRIPTION-KEY-HERE"

df = spark.createDataFrame([
  ("I am so happy today, its sunny!", "en-US"),
  ("I am frustrated by this rush hour traffic", "en-US"),
  ("The cognitive services on spark aint bad", "en-US"),
], ["text", "language"])

sentiment = (TextSentiment()
    .setTextCol("text")
    .setLocation("eastus")
    .setSubscriptionKey(service_key)
    .setOutputCol("sentiment")
    .setErrorCol("error")
    .setLanguageCol("language"))

results = sentiment.transform(df)

# Show the results in a table
display(results.select("text", col("sentiment")[0].getItem("score").alias("sentiment")))

```

1. Obtenha sua chave de assinatura no menu **Chaves e Ponto de Extremidade** de seu painel de Análise de Texto no portal do Azure.
1. Substitua o espaço reservado da chave de assinatura no código do notebook do Databricks pela sua chave de assinatura.
1. Selecione o símbolo de reprodução ou triângulo no canto superior direito da célula do notebook para executar o exemplo. Opcionalmente, selecione **Executar Tudo** na parte superior do notebook para executar todas as células. As respostas serão exibidas abaixo da célula em uma tabela.

### <a name="expected-results"></a>Resultados esperados

| text                                      |   sentimento |
|:------------------------------------------|------------:|
| Estou tão feliz hoje, está ensolarado!           |   0,978959  |
| Estou frustrado com esse trânsito da horas do rush |   0,0237956 |
| Os serviços cognitivos no Spark não são ruins  |   0,888896  |

## <a name="next-steps"></a>Próximas etapas

- [Exemplos breves do Python](samples-python.md)
- [Exemplos breves do Scala](samples-scala.md)
- [Receita: Manutenção Preditiva](recipes/anomaly-detection.md)
- [Receita: Exploração de Arte Inteligente](recipes/art-explorer.md)