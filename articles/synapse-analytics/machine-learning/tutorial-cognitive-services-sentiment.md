---
title: 'Tutorial: Análise de sentimento com os Serviços Cognitivos'
description: Saiba como usar os Serviços Cognitivos para análise de sentimento no Azure Synapse Analytics
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 08d5e53facce172c2287c2e341895f0ee38571f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98943710"
---
# <a name="tutorial-sentiment-analysis-with-cognitive-services-preview"></a>Tutorial: Análise de sentimento com os Serviços Cognitivos (versão prévia)

Neste tutorial, você aprenderá a enriquecer facilmente seus dados no Azure Synapse Analytics com os [Serviços Cognitivos do Azure](../../cognitive-services/index.yml). Usaremos as funcionalidades de [Análise de Texto](../../cognitive-services/text-analytics/index.yml) para executar a análise de sentimento. 

Um usuário no Azure Synapse pode simplesmente selecionar uma tabela que contém uma coluna de texto para enriquecimento com sentimentos. Esses sentimentos podem ser positivos, negativos, mistos ou neutros. Uma probabilidade também será retornada.

Este tutorial abrange:

> [!div class="checklist"]
> - As etapas para obter um conjunto de dados de uma tabela do Spark que contém uma coluna de texto para análise de sentimento.
> - O uso de uma experiência de assistente no Azure Synapse para enriquecer dados usando o Serviço Cognitivo de Análise de Texto.

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita antes de começar](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Pré-requisitos

- [Workspace do Azure Synapse Analytics](../get-started-create-workspace.md) com uma conta de armazenamento do Azure Data Lake Storage Gen2 configurada como o armazenamento padrão. Você precisa ser *Colaborador de Dados do Storage Blob* do sistema de arquivos Data Lake Storage Gen2 com o qual você trabalha.
- Pool do Spark no workspace do Azure Synapse Analytics. Para obter detalhes, confira [Criar um Pool do Spark no Azure Synapse](../quickstart-create-sql-pool-studio.md).
- Etapas de pré-configuração descritas no tutorial [Configurar Serviços Cognitivos no Azure Synapse](tutorial-configure-cognitive-services-synapse.md).

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [portal do Azure](https://portal.azure.com/).

## <a name="create-a-spark-table"></a>Criar uma tabela do Spark

Você precisará de uma tabela do Spark para esse tutorial.

1. Baixe o arquivo [FabrikamComments.csv](https://github.com/Kaiqb/KaiqbRepo0731190208/blob/master/CognitiveServices/TextAnalytics/FabrikamComments.csv), que contém um conjunto de dados para análise de texto. 

1. Carregue o arquivo em sua conta de armazenamento do Azure Synapse no Data Lake Storage Gen2.
  
   ![Captura de tela que mostra seleções para carregamento de dados.](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00a.png)

1. Crie uma tabela do Spark baseada no arquivo .csv clicando com o botão direito do mouse no arquivo e selecionando **Novo Notebook** > **Criar tabela do Spark**.

   ![Captura de tela que mostra seleções para criação de uma tabela do Spark.](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00b.png)

1. Nomeie a tabela na célula de código e execute o notebook em um Pool do Spark. Lembre-se de definir `header=True`.

   ![Captura de tela que mostra a execução de um notebook.](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00c.png)

   ```python
   %%pyspark
   df = spark.read.load('abfss://default@azuresynapsesa.dfs.core.windows.net/data/FabrikamComments.csv', format='csv'
   ## If a header exists, uncomment the line below
   , header=True
   )
   df.write.mode("overwrite").saveAsTable("default.YourTableName")
   ```

## <a name="open-the-cognitive-services-wizard"></a>Abrir o assistente dos Serviços Cognitivos

1. Clique com o botão direito do mouse na tabela do Spark criada no procedimento anterior. Selecione **Machine Learning** > **Enriquecer com o modelo existente** para abrir o assistente.

   ![Captura de tela que mostra seleções para abrir o assistente de pontuação.](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00d.png)

2. Um painel de configuração é exibido e você é solicitado a selecionar um modelo dos Serviços Cognitivos. Selecione **Análise de texto – Análise de Sentimento**.

   ![Captura de tela que mostra a seleção de um modelo dos Serviços Cognitivos.](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00e.png)

## <a name="provide-authentication-details"></a>Fornecer detalhes de autenticação

Para fazer a autenticação nos Serviços Cognitivos, você precisa fazer referência ao segredo para o seu cofre de chaves. As entradas a seguir dependem das [etapas de pré-requisito](tutorial-configure-cognitive-services-synapse.md) que você deve ter concluído antes desta etapa.

- **Assinatura do Azure**: selecione a assinatura do Azure à qual seu cofre de chaves pertence.
- **Conta dos Serviços Cognitivos**: insira o recurso de Análise de Texto ao qual você se conectará.
- **Serviço vinculado do Azure Key Vault**: como parte das etapas de pré-requisitos, você criou um serviço vinculado para o recurso Análise de Texto. Selecione-o aqui.
- **Nome do segredo**: Insira o nome do segredo no cofre de chaves que contém a chave para fazer a autenticação no recurso dos Serviços Cognitivos.

![Captura de tela que mostra os detalhes de autenticação para um cofre de chaves.](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00f.png)

## <a name="configure-sentiment-analysis"></a>Configurar a Análise de Sentimento

Em seguida, configure a análise de sentimento. Selecione os seguintes detalhes:
- **Idioma**: selecione **Português** como o idioma do texto no qual deseja executar a análise de sentimento.
- **Coluna de texto**: selecione **comentário (cadeia de caracteres)** como a coluna de texto no conjunto de dados que você deseja analisar para determinar o sentimento.

Quando terminar, selecione **Abrir notebook**. Isso gera um notebook para você com o código PySpark que executa a análise de sentimento usando os Serviços Cognitivos do Azure.

![Captura de tela que mostra seleções para configurar a análise de sentimento.](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00g.png)

## <a name="run-the-notebook"></a>Executar o notebook

O notebook que você acabou de abrir usa a [Biblioteca MMLSpark](https://github.com/Azure/mmlspark) para se conectar aos Serviços Cognitivos. Os detalhes do Azure Key Vault fornecidos permitem que você referencie com segurança seus segredos dessa experiência sem revelá-los.

Agora, você pode executar todas as células para enriquecer os dados com sentimentos. Selecione **Executar tudo**. 

Os sentimentos são retornados como **positivos**, **negativos**, **neutros** ou **mistos**. Você também obtém as probabilidades de cada sentimento. [Saiba mais sobre a análise de sentimento nos Serviços Cognitivos](../../cognitive-services/text-analytics/how-tos/text-analytics-how-to-sentiment-analysis.md).

![Captura de tela que mostra a análise de sentimentos.](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00h.png)

## <a name="next-steps"></a>Próximas etapas
- [Tutorial: Detecção de anomalias com os Serviços Cognitivos do Azure](tutorial-cognitive-services-sentiment.md)
- [Tutorial: Pontuação do modelo de machine learning em pools de SQL dedicados do Azure Synapse](tutorial-sql-pool-model-scoring-wizard.md)
- [Funcionalidades do Machine Learning no Azure Synapse Analytics](what-is-machine-learning.md)