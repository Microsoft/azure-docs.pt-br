---
title: 'Tutorial: Assistente de pontuação do modelo de machine learning para pools de SQL dedicados'
description: Tutorial sobre como usar o assistente de pontuação do modelo de machine learning para enriquecer dados em pools de SQL dedicados.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 09/25/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: d8db9257ad6eed98b39cd2c9a52351f013453365
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98935226"
---
# <a name="tutorial-machine-learning-model-scoring-wizard-preview-for-dedicated-sql-pools"></a>Tutorial: Assistente de pontuação do modelo de machine learning (versão prévia) para pools de SQL dedicados

Saiba como enriquecer facilmente seus dados em pools de SQL dedicados com modelos de machine learning preditivos. Os modelos que os cientistas de dados criam agora podem ser facilmente acessados por profissionais de dados para análise preditiva. Um profissional de dados no Azure Synapse Analytics pode simplesmente selecionar um modelo do registro de modelos do Azure Machine Learning para implantação em pools de SQL do Synapse e iniciar previsões para enriquecer os dados.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> - Treinar um modelo de machine learning preditivo e registrar o modelo no registro de modelos do Azure Machine Learning.
> - Usar o assistente de pontuação do SQL para iniciar previsões em um pool de SQL dedicado.

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita antes de começar](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Pré-requisitos

- [Workspace do Azure Synapse Analytics](../get-started-create-workspace.md) com uma conta de armazenamento do Azure Data Lake Storage Gen2 configurada como o armazenamento padrão. Você precisa ser *Colaborador de Dados do Storage Blob* do sistema de arquivos Data Lake Storage Gen2 com o qual você trabalha.
- O pool de SQL dedicado em seu workspace do Azure Synapse Analytics. Para obter detalhes, confira [Criar um pool de SQL dedicado](../quickstart-create-sql-pool-studio.md).
- Serviço vinculado do Azure Machine Learning em seu workspace do Azure Synapse Analytics. Para obter mais detalhes, confira [Criar um serviço vinculado do Azure Machine Learning no Azure Synapse](quickstart-integrate-azure-machine-learning.md).

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [portal do Azure](https://portal.azure.com/).

## <a name="train-a-model-in-azure-machine-learning"></a>Treinar um modelo no Azure Machine Learning

Antes de começar, verifique se sua versão do sklearn é a 0.20.3.

Antes de você executar todas as células no notebook, verifique se a instância de computação está em execução.

![Captura de tela que mostra a verificação da computação do Azure Machine Learning.](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00b.png)

1. Acesse o Workspace do Azure Machine Learning.

1. Baixe [Predict NYC Taxi Tips.ipynb](https://go.microsoft.com/fwlink/?linkid=2144301).

1. Abra o Workspace do Azure Machine Learning no [Estúdio do Azure Machine Learning](https://ml.azure.com).

1. Acesse **Notebooks** > **Carregar arquivos**. Em seguida, selecione o arquivo **Predict NYC Taxi Tips.ipynb** que você baixou e carregou.
   ![Captura de tela do botão para carregar um arquivo.](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00a.png)

1. Depois que o notebook for carregado e aberto, selecione **Executar todas as células**.

   Uma das células pode falhar e solicitar que você se autentique no Azure. Fique atento a isso nas saídas de célula e faça a autenticação no navegador seguindo o link e inserindo o código. Em seguida, execute novamente o notebook.

1. O notebook treinará um modelo ONNX e o registrará com o MLflow. Acesse **Modelos** para verificar se o novo modelo está registrado corretamente.
   ![Captura de tela que mostra o modelo no registro.](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00c.png)

1. A execução do notebook também exportará os dados de teste para um arquivo CSV. Baixe o arquivo CSV em seu sistema local. Posteriormente, você importará o arquivo CSV em um pool de SQL dedicado e usará os dados para testar o modelo.

   O arquivo CSV é criado na mesma pasta que o arquivo do notebook. Clique em **Atualizar** no Explorador de Arquivos se você não o vir imediatamente.

   ![Captura de tela que mostra o arquivo CSV.](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00d.png)

## <a name="launch-predictions-with-the-sql-scoring-wizard"></a>Iniciar previsões com o assistente de pontuação do SQL

1. Abra o workspace do Azure Synapse com o Synapse Studio.

1. Acesse **Dados** > **Vinculado** > **Contas de Armazenamento**. Carregue `test_data.csv` na conta de armazenamento padrão.

   ![Captura de tela que mostra seleções para carregamento de dados.](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00a.png)

1. Acesse **Desenvolver** > **Scripts SQL**. Crie um script de SQL para carregar `test_data.csv` em seu pool de SQL dedicado.

   > [!NOTE]
   > Atualize a URL do arquivo neste script antes de executá-lo.

   ```SQL
   IF NOT EXISTS (SELECT * FROM sys.objects WHERE NAME = 'nyc_taxi' AND TYPE = 'U')
   CREATE TABLE dbo.nyc_taxi
   (
       tipped int,
       fareAmount float,
       paymentType int,
       passengerCount int,
       tripDistance float,
       tripTimeSecs bigint,
       pickupTimeBin nvarchar(30)
   )
   WITH
   (
       DISTRIBUTION = ROUND_ROBIN,
       CLUSTERED COLUMNSTORE INDEX
   )
   GO
   
   COPY INTO dbo.nyc_taxi
   (tipped 1, fareAmount 2, paymentType 3, passengerCount 4, tripDistance 5, tripTimeSecs 6, pickupTimeBin 7)
   FROM '<URL to linked storage account>/test_data.csv'
   WITH
   (
       FILE_TYPE = 'CSV',
       ROWTERMINATOR='0x0A',
       FIELDQUOTE = '"',
       FIELDTERMINATOR = ',',
       FIRSTROW = 2
   )
   GO
   
   SELECT TOP 100 * FROM nyc_taxi
   GO
   ```

   ![Carregar dados no pool do SQL dedicado](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00b.png)

1. Acesse **Dados** > **Workspace**. Abra o assistente de pontuação do SQL clicando com o botão direito do mouse na tabela do pool de SQL dedicado. Selecione **Machine Learning** > **Enriquecer com o modelo existente**.

   > [!NOTE]
   > A opção de machine learning não é exibida a menos que você tenha um serviço vinculado criado para o Azure Machine Learning. (Confira [Pré-requisitos](#prerequisites) no início deste tutorial.)

   ![Captura de tela que mostra a opção de machine learning.](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00c.png)

1. Selecione um Workspace do Azure Machine Learning vinculado na caixa suspensa. Essa etapa carrega uma lista de modelos de machine learning do registro de modelos do Workspace do Azure Machine Learning escolhido. Atualmente, há suporte apenas para modelos ONNX, portanto, essa etapa exibirá apenas modelos ONNX.

1. Selecione o modelo que você acabou de treinar e escolha **Continuar**.

   ![Captura de tela que mostra a seleção do modelo do Azure Machine Learning.](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00d.png)

1. Em seguida, mapeie as colunas da tabela para as entradas do modelo e especifique as saídas do modelo. Se o modelo for salvo no formato MLflow e a assinatura do modelo for populada, o mapeamento será feito automaticamente para você usando uma lógica com base na similaridade de nomes. A interface também dá suporte ao mapeamento manual.

   Selecione **Continuar**.

   ![Captura de tela que mostra o mapeamento de tabela para modelo.](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00e.png)

1. O código T-SQL gerado é encapsulado dentro de um procedimento armazenado. É por isso que você precisa fornecer um nome de procedimento armazenado. O binário do modelo, incluindo metadados (versão, descrição e outras informações), será copiado fisicamente do Azure Machine Learning para uma tabela de pool de SQL dedicado. Portanto, você precisa especificar em qual tabela salvar o modelo. 

   Você pode escolher **Tabela existente** ou **Criar**. Depois de terminar, selecione **Implantar modelo + abrir script** para implantar o modelo e gerar um script de previsão do T-SQL.

   ![Captura de tela que mostra seleções para criação de um procedimento armazenado.](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00f.png)

1. Depois que o script for gerado, selecione **Executar** para executar a pontuação e obter previsões.

   ![Captura de tela que mostra a pontuação e as previsões.](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00g.png)

## <a name="next-steps"></a>Próximas etapas

- [Início Rápido: Criar um serviço vinculado do Azure Machine Learning no Azure Synapse](quickstart-integrate-azure-machine-learning.md)
- [Funcionalidades do Machine Learning no Azure Synapse Analytics](what-is-machine-learning.md)
