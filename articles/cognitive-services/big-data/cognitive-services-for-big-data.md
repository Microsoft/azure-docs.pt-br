---
title: Serviços Cognitivos para Big data
description: Saiba como aproveitar os Serviços Cognitivos do Azure em conjuntos de dados grandes usando Python, Java e Scala. Com os Serviços Cognitivos para Big Data, você pode inserir continuamente modelos inteligentes e aprimorados diretamente no Apache Spark&trade; e em cálculos de SQL.
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.topic: overview
ms.date: 07/09/2020
ms.author: marhamil
ms.openlocfilehash: aaade03edbbb109656fb7371a063cdc2512c5a20
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96461628"
---
# <a name="azure-cognitive-services-for-big-data"></a>Serviços Cognitivos do Azure para Big Data

![Serviços Cognitivos para Big data](media/cognitive-services-big-data-overview.svg)

Os Serviços Cognitivos do Azure para Big Data permitem que os usuários canalizem terabytes de dados por Serviços Cognitivos usando o [Apache Spark&trade;](/dotnet/spark/what-is-spark). Com os Serviços Cognitivos para Big Data, é fácil criar aplicativos inteligentes de grande escala com qualquer armazenamento de dados.

Com os Serviços Cognitivos para Big Data, você pode inserir continuamente modelos inteligentes e aprimorados diretamente no Apache Spark&trade; e em cálculos de SQL. Essas ferramentas liberam os desenvolvedores de detalhes de rede de nível baixo para que eles possam se concentrar na criação de aplicativos inteligentes e distribuídos.

## <a name="features-and-benefits"></a>Características e benefícios

Os Serviços Cognitivos para Big Data podem usar serviços de qualquer região do mundo, bem como [Serviços Cognitivos em contêineres](../cognitive-services-container-support.md). Os contêineres dão suporte a implantações de conectividade baixa ou nenhuma com respostas de latência ultrabaixa. Os Serviços Cognitivos em contêineres podem ser executados localmente, direto nos nós de trabalho do seu cluster Spark ou em um orquestrador externo como o Kubernetes.

## <a name="supported-services"></a>Serviços com suporte

Os [Serviços Cognitivos](../index.yml), acessados por meio de APIs e SDKs, ajudam os desenvolvedores a criar aplicativos inteligentes sem ter habilidades de IA ou ciência de dados. Com Serviços Cognitivos, você pode fazer com que seus aplicativos vejam, ouçam, falem, entendam e raciocinem. Para usar os Serviços Cognitivos, seu aplicativo deve enviar dados para o serviço pela rede. Depois de recebido, o serviço envia uma resposta inteligente no retorno. Os seguintes serviços estão disponíveis para cargas de trabalho de Big Data:

### <a name="vision"></a>Visão

|Nome do Serviço|Descrição do Serviço|
|:-----------|:------------------|
|[Pesquisa Visual Computacional](../computer-vision/index.yml "Visual Computacional")| O serviço de Pesquisa Visual Computacional fornece a você acesso a algoritmos avançados para processar imagens e retornar informações. |
|[Detecção Facial](../face/index.yml "Face")| O serviço de Detecção Facial fornece acesso a algoritmos avançados de detecção facial, habilitando a detecção e o reconhecimento de atributos faciais. |

### <a name="speech"></a>Fala

|Nome do Serviço|Descrição do Serviço|
|:-----------|:------------------|
|[Serviço de Fala](../speech-service/index.yml "Serviço de Fala")|O serviço de Fala fornece acesso a recursos como reconhecimento de fala, síntese de fala, tradução de fala e identificação e verificação do locutor.|

### <a name="decision"></a>Decisão

|Nome do Serviço|Descrição do Serviço|
|:-----------|:------------------|
|[Detector de Anomalias](../anomaly-detector/index.yml "Detector de Anomalias") | O serviço Detector de Anomalias (versão prévia) permite que você monitore e detecte anormalidades em seus dados de série temporal.|

### <a name="language"></a>Linguagem

|Nome do Serviço|Descrição do Serviço|
|:-----------|:------------------|
|[Análise de Texto](../text-analytics/index.yml "Análise de texto")| O serviço Análise de Texto oferece processamento de linguagem natural sobre texto bruto para análise de sentimento, extração de frases-chave e detecção de idioma.|

### <a name="search"></a>Search

|Nome do Serviço|Descrição do Serviço|
|:-----------|:------------------|
|[Pesquisa de Imagem do Bing](/azure/cognitive-services/bing-image-search "Pesquisa de Imagem do Bing")|O serviço Pesquisa de Imagem do Bing retorna uma exibição de imagens determinadas como relevantes para a consulta do usuário.|

## <a name="supported-programming-languages-for-cognitive-services-for-big-data"></a>Linguagens de programação com suporte para Serviços Cognitivos para Big Data

Os Serviços Cognitivos para Big Data são criados no Apache Spark. O Apache Spark é uma biblioteca de computação distribuída que dá suporte a Java, Scala, Python, R e muitas outras linguagens. Atualmente, há suporte para essas linguagens.

### <a name="python"></a>Python

Fornecemos uma API PySpark no namespace `mmlspark.cognitive` de [Microsoft ML para Apache Spark](https://aka.ms/spark). Para saber mais, confira a [API de Desenvolvedores do Python](https://mmlspark.blob.core.windows.net/docs/1.0.0-rc1/pyspark/mmlspark.cognitive.html). Para obter exemplos de uso, confira os [Exemplos de Python](samples-python.md).

### <a name="scala-and-java"></a>Scala e Java

Fornecemos uma API do Spark em Scala e em Java no namespace `com.microsoft.ml.spark.cognitive` de [Microsoft ML para Apache Spark](https://aka.ms/spark). Para obter mais informações, confira [API de Desenvolvedor do Scala](https://mmlspark.blob.core.windows.net/docs/1.0.0-rc1/scala/index.html#package). Para obter exemplos de uso, confira os [Exemplos de Scala](samples-scala.md).

## <a name="supported-platforms-and-connectors"></a>Plataformas e conectores com suporte

Os Serviços Cognitivos para Big Data exigem o Apache Spark. Há várias plataformas do Apache Spark que dão suporte aos Serviços Cognitivos para Big Data.

### <a name="azure-databricks"></a>Azure Databricks

O [Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks) é uma plataforma de análise baseada no Apache Spark otimizada para a plataforma de Serviços de Nuvem do Microsoft Azure. Ele fornece configuração de um clique, fluxos de trabalho simplificados e um workspace interativo que dá suporte à colaboração entre cientistas de dados, engenheiros de dados e analistas de negócios.

### <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

O [Azure Synapse Analytics](/azure/databricks/data/data-sources/azure/synapse-analytics) é um data warehouse empresarial que usa o processamento altamente paralelo. Com o Synapse Analytics, você pode executar rapidamente consultas complexas em petabytes de dados. O Azure Synapse Analytics fornece pools do Spark gerenciados para executar trabalhos do Spark com uma interface de Jupyter Notebook intuitiva.

### <a name="azure-kubernetes-service"></a>Serviço de Kubernetes do Azure

O [AKS (Serviço de Kubernetes do Azure)](../../aks/index.yml) orquestra contêineres e aplicativos distribuídos em grande escala. O AKS é uma oferta de Kubernetes gerenciado que simplifica o uso do Kubernetes no Azure. O Kubernetes pode habilitar o controle refinado de escala, latência e rede de Serviços Cognitivos. No entanto, recomendamos usar o Azure Databricks ou o Azure Synapse Analytics se você não está familiarizado com o Apache Spark.

### <a name="data-connectors"></a>Conectores de dados

Quando você tiver um cluster do Spark, a próxima etapa será conectar-se aos seus dados. O Apache Spark tem uma ampla coleção de conectores de banco de dados. Esses conectores permitem que os aplicativos trabalhem com grandes conjuntos de dados, independentemente do local em que estejam armazenados. Para obter mais informações sobre os bancos de dados e conectores com suporte, confira a [lista de fontes de dados com suporte para o Azure Databricks](/azure/databricks/data/data-sources/).

## <a name="concepts"></a>Conceitos

### <a name="spark"></a>Spark

O [Apache Spark&trade;](http://spark.apache.org/) é um mecanismo de análise unificado para processamento de dados em grande escala. Sua estrutura de processamento paralelo aumenta o desempenho de aplicativos de Big Data e análise. O Spark pode operar como um sistema de processamento de fluxo e lote sem alterar o código do aplicativo principal.

A base do Spark é o DataFrame: uma coleção tabular de dados distribuídos entre os nós de trabalho do Apache Spark. Um DataFrame do Spark é como uma tabela em um banco de dados relacional ou em um quadro em R/Python, mas com escala ilimitada. Os DataFrames podem ser construídos usando várias fontes, como: arquivos estruturados, tabelas no Hive ou bancos de dados externos. Depois que os dados estiverem em um DataFrame do Spark, você poderá:

   - Fazer computações de estilo SQL, como tabelas de junção e de filtro.
   - Aplicar funções a conjuntos de dados grandes usando o paralelismo no estilo MapReduce.
   - Aplicar Machine Learning distribuído usando o Microsoft Machine Learning para Apache Spark.
   - Usar os Serviços Cognitivos para Big Data para aprimorar seus dados com serviços inteligentes prontos para uso.

### <a name="microsoft-machine-learning-for-apache-spark-mmlspark"></a>MMLSpark (Microsoft Machine Learning para Apache Spark)

O MMLSpark ([Microsoft Machine Learning para Apache Spark](https://mmlspark.blob.core.windows.net/website/index.html#install)) é uma biblioteca de ML (machine learning) distribuída de software livre criada no Apache Spark. Os Serviços Cognitivos para Big Data estão incluídos neste pacote. Além disso, o MMLSpark contém várias outras ferramentas de ML para Apache Spark, como LightGBM, Vowpal Wabbit, OpenCV, LIME e muito mais. Com o MMLSpark, você pode criar poderosos modelos de previsão e analíticos usando qualquer fonte de dados do Spark.

### <a name="http-on-spark"></a>HTTP no Spark

Serviços Cognitivos para Big Data são um exemplo de como podemos integrar serviços Web inteligentes a Big Data. Os serviços Web alimentam muitos aplicativos em todo o mundo e a maioria dos serviços se comunica usando o protocolo HTTP. Para trabalhar com serviços Web *arbitrários* em grande escala, fornecemos HTTP no Spark. Com o HTTP no Spark, você pode passar terabytes de dados por meio de qualquer serviço Web. Nos bastidores, usamos essa tecnologia para alimentar Serviços Cognitivos para obter Big Data.

## <a name="developer-samples"></a>Exemplos do desenvolvedor

- [Receita: Manutenção Preditiva](recipes/anomaly-detection.md)
- [Receita: Exploração de Arte Inteligente](recipes/art-explorer.md)

## <a name="blog-posts"></a>Postagens no blog

- [Saiba mais sobre como os Serviços Cognitivos funcionam no Apache Spark&trade;](https://azure.microsoft.com/blog/dear-spark-developers-welcome-to-azure-cognitive-services/)
- [Salvando Leopardos-das-Neves com aprendizado profundo e Pesquisa Visual Computacional no Spark](http://www.datawizard.io/2017/06/27/saving-snow-leopards-with-deep-learning-and-computer-vision-on-spark/)
- [Podcast do Microsoft Research: MMLSpark, capacitando AI for Good com Mark Hamilton](https://blubrry.com/microsoftresearch/49485070/092-mmlspark-empowering-ai-for-good-with-mark-hamilton/)
- [White paper acadêmico: Microsserviços inteligentes em grande escala](https://arxiv.org/abs/2009.08044)

## <a name="webinars-and-videos"></a>Webinars e vídeos

- [Os Serviços Cognitivos do Azure no Spark: clusters com Serviços Inteligentes incorporados](https://databricks.com/session/the-azure-cognitive-services-on-spark-clusters-with-embedded-intelligent-services)
- [Palestra do Spark Summit: Scalable AI for Good](https://databricks.com/session_eu19/scalable-ai-for-good)
- [Os Serviços Cognitivos para Big Data no Cosmos DB](https://medius.studios.ms/Embed/Video-nc/B19-BRK3004?latestplayer=true&l=2571.208093)
- [Palestra relâmpago sobre microsserviços inteligentes em grande escala](https://www.youtube.com/watch?v=BtuhmdIy9Fk&t=6s)

## <a name="next-steps"></a>Próximas etapas

- [Introdução a Serviços Cognitivos para Big Data](getting-started.md)
- [Exemplos simples do Python](samples-python.md)
- [Exemplos simples do Scala](samples-scala.md)