---
title: 'Tutorial: Detecção de anomalias com os Serviços Cognitivos'
description: Saiba como usar os Serviços Cognitivos para detecção de anomalias no Azure Synapse Analytics.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: c54300bf37f6f4526c525b1502d902e5f4336ed7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98943500"
---
# <a name="tutorial-anomaly-detection-with-cognitive-services-preview"></a>Tutorial: Detecção de anomalias com os Serviços Cognitivos (versão prévia)

Neste tutorial, você aprenderá a enriquecer facilmente seus dados no Azure Synapse Analytics com os [Serviços Cognitivos do Azure](../../cognitive-services/index.yml). Você usará o [Detector de Anomalias](../../cognitive-services/anomaly-detector/index.yml) para encontrar anomalias. Um usuário no Azure Synapse pode simplesmente selecionar uma tabela para enriquecer a detecção de anomalias.

Este tutorial abrange:

> [!div class="checklist"]
> - Etapas para obter um conjunto de dados de tabela do Spark que contém dados de série temporal.
> - O uso de uma experiência de assistente no Azure Synapse para enriquecer dados usando o Detector de Anomalias nos Serviços Cognitivos.

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita antes de começar](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Pré-requisitos

- [Workspace do Azure Synapse Analytics](../get-started-create-workspace.md) com uma conta de armazenamento do Azure Data Lake Storage Gen2 configurada como o armazenamento padrão. Você precisa ser *Colaborador de Dados do Storage Blob* do sistema de arquivos Data Lake Storage Gen2 com o qual você trabalha.
- Pool do Spark no workspace do Azure Synapse Analytics. Para obter detalhes, confira [Criar um Pool do Spark no Azure Synapse](../quickstart-create-sql-pool-studio.md).
- Conclusão das etapas de pré-configuração no tutorial [Configurar os Serviços Cognitivos no Azure Synapse](tutorial-configure-cognitive-services-synapse.md).

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [portal do Azure](https://portal.azure.com/).

## <a name="create-a-spark-table"></a>Criar uma tabela do Spark

Você precisa de uma tabela do Spark para este tutorial.

1. Baixe o seguinte arquivo de notebook que contém um código para gerar uma tabela do Spark: [prepare_anomaly_detector_data.ipynb](https://go.microsoft.com/fwlink/?linkid=2149577).

1. Carregue o arquivo para seu workspace do Azure Synapse.

   ![Captura de tela que mostra seleções para carregamento de um notebook.](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00a.png)

1. Abra o arquivo do notebook e selecione **Executar Tudo** para executar todas as células.

   ![Captura de tela que mostra seleções para criação de uma tabela do Spark.](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00b.png)

1. Uma tabela do Spark com o nome **anomaly_detector_testing_data** agora deve aparecer no banco de dados do Spark padrão.

## <a name="open-the-cognitive-services-wizard"></a>Abrir o assistente dos Serviços Cognitivos

1. Clique com o botão direito do mouse na tabela do Spark criada na etapa anterior. Selecione **Machine Learning** > **Enriquecer com o modelo existente** para abrir o assistente.

   ![Captura de tela que mostra seleções para abrir o assistente de pontuação.](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00g.png)

2. Um painel de configuração é exibido e você é solicitado a selecionar um modelo dos Serviços Cognitivos. Selecione **Detector de Anomalias**.

   ![Captura de tela que mostra a seleção do Detector de Anomalias como um modelo.](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00c.png)

## <a name="provide-authentication-details"></a>Fornecer detalhes de autenticação

Para fazer a autenticação nos Serviços Cognitivos, você precisa fazer referência ao segredo para o seu cofre de chaves. As entradas a seguir dependem das [etapas de pré-requisito](tutorial-configure-cognitive-services-synapse.md) que você deve ter concluído antes desta etapa.

- **Assinatura do Azure**: selecione a assinatura do Azure à qual seu cofre de chaves pertence.
- **Conta dos Serviços Cognitivos**: insira o recurso de Análise de Texto ao qual você se conectará.
- **Serviço vinculado do Azure Key Vault**: como parte das etapas de pré-requisitos, você criou um serviço vinculado para o recurso Análise de Texto. Selecione-o aqui.
- **Nome do segredo**: Insira o nome do segredo no cofre de chaves que contém a chave para fazer a autenticação no recurso dos Serviços Cognitivos.

![Captura de tela que mostra os detalhes de autenticação para um cofre de chaves.](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00d.png)

## <a name="configure-anomaly-detector"></a>Configurar o Detector de Anomalias

Forneça os seguintes detalhes para configurar o Detector de Anomalias:

- **Granularidade**: a taxa na qual os dados são amostrados. Escolha **mensalmente**. 

- **Coluna de carimbo de data/hora**: a coluna que representa a hora da série. Escolha **carimbo de data/hora (string)** .

- **Coluna de valor da série temporal**: a coluna que representa o valor da série na hora especificada pela coluna Carimbo de data/hora. Escolha **valor (double)** .

- **Coluna de agrupamento**: a coluna que agrupa a série. Ou seja, todas as linhas que têm o mesmo valor nesta coluna devem formar uma série temporal. Escolha **grupo (string)** .

Quando terminar, selecione **Abrir notebook**. Isso vai gerar um notebook para você com o código do PySpark que usa os Serviços Cognitivos do Azure para detectar anomalias.

![Captura de tela que mostra os detalhes de configuração para o Detector de Anomalias.](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00e.png)

## <a name="run-the-notebook"></a>Executar o notebook

O notebook que você acabou de abrir usa a [Biblioteca MMLSpark](https://github.com/Azure/mmlspark) para se conectar aos Serviços Cognitivos. Os detalhes do Azure Key Vault fornecidos permitem que você referencie com segurança seus segredos dessa experiência sem revelá-los.

Agora você pode executar todas as células para realizar a detecção de anomalias. Selecione **Executar Tudo**. [Saiba mais sobre o Detector de Anomalias nos Serviços Cognitivos](../../cognitive-services/anomaly-detector/index.yml).

![Captura de tela que mostra a detecção de anomalias.](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00f.png)

## <a name="next-steps"></a>Próximas etapas

- [Tutorial: Análise de sentimento com os Serviços Cognitivos do Azure](tutorial-cognitive-services-sentiment.md)
- [Tutorial: Pontuação do modelo de machine learning em pools de SQL dedicados do Azure Synapse](tutorial-sql-pool-model-scoring-wizard.md)
- [Funcionalidades do Machine Learning no Azure Synapse Analytics](what-is-machine-learning.md)
