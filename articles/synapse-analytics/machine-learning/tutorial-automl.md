---
title: 'Tutorial: Treinar um modelo usando o machine learning automatizado'
description: Tutorial sobre como treinar um modelo de machine learning sem código no Azure Synapse Analytics.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: f3b0c5f1487951d05bc83973e5b4b9f3634a694b
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222251"
---
# <a name="tutorial-train-a-machine-learning-model-without-code"></a>Tutorial: Treinar um modelo de machine learning sem código

É possível enriquecer seus dados em tabelas do Spark com novos modelos de machine learning que você treina usando o [machine learning automatizado](../../machine-learning/concept-automated-ml.md). No Azure Synapse Analytics, você pode selecionar uma tabela do Spark no workspace do para ser usada como um conjunto de dados de treinamento para criar modelos de machine learning, o que pode ser feito em uma experiência sem código.

Neste tutorial, você aprenderá a treinar modelos de machine learning usando uma experiência sem código no Azure Synapse Analytics Studio. Você usa o machine learning automatizado no Azure Machine Learning, em vez de codificar a experiência manualmente. O tipo de modelo que você treina depende do problema que está tentando resolver.

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita antes de começar](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Pré-requisitos

- Um [workspace do Azure Synapse Analytics](../get-started-create-workspace.md). Verifique se ele tem a seguinte conta de armazenamento, configurada como o armazenamento padrão: Azure Data Lake Storage Gen2. Para o sistema de arquivos Data Lake Storage Gen2 com o qual você trabalha, verifique se você é o **Colaborador de dados de blob de armazenamento**.
- Um pool do Apache Spark no workspace do Azure Synapse Analytics. Para obter detalhes, confira [Início Rápido: Criar um pool de SQL dedicado usando o Azure Synapse Analytics Studio](../quickstart-create-sql-pool-studio.md).
- Um serviço vinculado do Azure Machine Learning em seu workspace do Azure Synapse Analytics. Para obter detalhes, confira [Início Rápido: Criar um serviço vinculado do Azure Machine Learning no Azure Synapse Analytics](quickstart-integrate-azure-machine-learning.md).

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [portal do Azure](https://portal.azure.com/).

## <a name="create-a-spark-table-for-training-dataset"></a>Criar uma tabela do Spark para o conjunto de dados de treinamento

Você precisará de uma tabela do Spark para este tutorial. O notebook a seguir cria um.

1. Baixe o notebook [Create-Spark-Table-NYCTaxi- Data.ipynb](https://go.microsoft.com/fwlink/?linkid=2149229).

1. Importe o notebook para o Azure Synapse Analytics Studio.
![Captura de tela do Azure Synapse Analytics, com a opção Importar realçada.](media/tutorial-automl-wizard/tutorial-automl-wizard-00a.png)

1. Selecione o pool do Spark que você deseja usar e clique em **Executar tudo**. Ao fazer isso, você obterá os dados de táxi de Nova York de um conjunto de dados aberto e os salvará no seu banco de dados do Spark padrão.
![Captura de tela do Azure Synapse Analytics, com a opção Executar tudo e o nome do banco de dados do Spark realçados.](media/tutorial-automl-wizard/tutorial-automl-wizard-00b.png)

1. Depois que a execução do notebook for concluída, você verá uma tabela do Spark no banco de dados do Spark padrão. Em **Dados**, localize a tabela denominada **nyc_taxi**.
![Captura de tela da guia dados do Azure Synapse Analytics, com a nova tabela realçada.](media/tutorial-automl-wizard/tutorial-automl-wizard-00c.png)

## <a name="launch-automated-machine-learning-wizard"></a>Iniciar o assistente de machine learning automatizado

Aqui está como:

1. Clique com o botão direito do mouse na tabela do Spark criada na etapa anterior. Para abrir o assistente, selecione **Machine Learning** > **Enriquecer com o novo modelo**.
![Captura de tela da tabela do Spark, com Machine Learning e Enriquecer com o novo modelo realçados.](media/tutorial-automl-wizard/tutorial-automl-wizard-00d.png)

1. Em seguida, você pode fornecer os detalhes de configuração para a criação de um experimento de machine learning automatizado executado no Azure Machine Learning. Essa execução treina vários modelos, e o melhor modelo de uma execução bem-sucedida é colocado no Registro de modelo do Azure Machine Learning.

   ![Captura de tela das especificações de configuração de Enriquecer com o novo modelo.](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00a.png)

    - **Workspace do Azure Machine Learning**: é necessário um Workspace do Azure Machine Learning para a criação de uma execução de experimento de machine learning automatizado. Você também precisa vincular seu workspace do Azure Synapse Analytics ao workspace do Azure Machine Learning usando um [serviço vinculado](quickstart-integrate-azure-machine-learning.md). Depois de cumprir todos os pré-requisitos, você poderá especificar o Workspace do Azure Machine Learning que deseja usar para essa execução automatizada.

    - **Nome do experimento**: especifique o nome do experimento. Quando você envia uma execução de machine learning automatizado, você pode fornecer um nome de experimento. As informações da execução são armazenadas nesse experimento no Workspace do Azure Machine Learning. Essa experiência cria um experimento por padrão e gera um nome proposto, mas você também pode fornecer um nome de um experimento existente.

    - **Melhor modelo**: especifique o nome do melhor modelo com base na execução automatizada. O melhor modelo receberá esse nome e será salvo no Registro de modelo do Azure Machine Learning automaticamente após essa execução. Uma execução de machine learning automatizado cria muitos modelos de machine learning. Com base na métrica primária que você selecionará em uma etapa posterior, esses modelos poderão ser comparados e o melhor modelo poderá ser selecionado.

    - **Coluna de destino**: isso é o que o modelo é treinado para prever. Escolha a coluna que você deseja prever. (Neste tutorial, selecionamos a coluna numérica `fareAmount` como a coluna de destino.)

    - **Pool do Spark**: o Pool do Spark que você deseja usar para a execução do experimento automatizado. Os cálculos serão executados no pool que você especificar.

    - **Detalhes de configuração do Spark**: além do Pool do Spark, você também tem a opção de fornecer detalhes de configuração de sessão.

1. Selecione **Continuar**.

## <a name="choose-task-type"></a>Escolher o tipo de tarefa

Selecione o tipo de modelo de machine learning para o experimento com base na pergunta que você está tentando responder. Como `fareAmount` é a coluna de destino e é um valor numérico, selecione **Regressão** aqui. Depois selecione **Continuar**.

![Captura de tela de Enriquecer com o novo modelo, com Regressão realçada.](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00b.png)

## <a name="additional-configurations"></a>Configurações adicionais

Se você selecionar **Regressão** ou **Classificação** como o tipo de modelo na seção anterior, as seguintes configurações estarão disponíveis:

- **Métrica principal**: a métrica usada para medir o desempenho do modelo. Essa é a métrica usada para comparar modelos diferentes criados na execução automatizada e determinar qual modelo teve melhor desempenho.

- **Tempo do trabalho de treinamento (horas)** : o período máximo de tempo, em horas, para que um experimento execute e treine modelos. Observe que você também pode fornecer valores menores que 1 (por exemplo, `0.5`).

- **Máximo de iterações simultâneas**: representa o número máximo de iterações executadas em paralelo.

- **Compatibilidade de modelo de ONNX**: se você habilitar essa opção, os modelos treinados pelo machine learning automatizado serão convertidos no formato ONNX. Isso será particularmente relevante se você quiser usar o modelo para pontuação em pools de SQL do Azure Synapse Analytics.

Todas essas configurações têm um valor padrão que você pode personalizar.
![Captura de tela das configurações adicionais de Enriquecer com o novo modelo.](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00c.png)

Depois que todas as configurações necessárias forem concluídas, você poderá iniciar a execução automatizada. Você pode escolher **Criar execução**, o que inicia a execução diretamente, sem código. Como alternativa, se você preferir usar código, poderá selecionar **Abrir no notebook**. Essa opção permite que você veja o código que cria a execução e execute o notebook.

>[!NOTE]
>Se você selecionar **Previsão de série temporal** como o tipo de modelo na seção anterior, precisará fazer configurações adicionais. A previsão também não dá suporte à compatibilidade de modelo ONNX.

### <a name="create-run-directly"></a>Criar execução diretamente

Para iniciar a execução do machine learning automatizado diretamente, selecione **Iniciar Execução**. Haverá uma notificação que indica que a execução está sendo iniciada. Em seguida, você verá outra notificação indicando êxito. Você também pode verificar o status no Azure Machine Learning selecionando o link na notificação.
![Captura de tela da publicação bem-sucedida.](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00d.png)

### <a name="create-run-with-notebook"></a>Criar execução com notebook

Para gerar um notebook, selecione **Abrir no Notebook**. Em seguida, selecione **Executar tudo**. Isso também dá a oportunidade de adicionar configurações adicionais à sua execução de machine learning automatizado.

![Captura de tela do Notebook, com Executar tudo realçado.](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00e.png)

Depois que você tiver enviado a execução, verá um link para a execução do experimento no Workspace do Azure Machine Learning na saída do notebook. Selecione o link para monitorar sua execução automatizada no Azure Machine Learning.
![Captura de tela do Azure Synapse Analytics, com o link realçado.](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00f.png)

## <a name="next-steps"></a>Próximas etapas

- [Tutorial: Assistente de pontuação do modelo de machine learning (versão prévia) para pools de SQL dedicados](tutorial-sql-pool-model-scoring-wizard.md)
- [Início Rápido: Criar um serviço vinculado do Azure Machine Learning no Azure Synapse Analytics](quickstart-integrate-azure-machine-learning.md)
- [Funcionalidades de machine learning no Azure Synapse Analytics](what-is-machine-learning.md)