---
title: Coletar dados em seus modelos de produção
titleSuffix: Azure Machine Learning
description: Saiba como coletar dados de um modelo de Azure Machine Learning implantado em um cluster do AKS (serviço de kubernetes do Azure).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: sgilley
ms.author: copeters
author: lostmygithubaccount
ms.date: 07/14/2020
ms.topic: conceptual
ms.custom: how-to, data4ml
ms.openlocfilehash: f42a2f9e606eaa0475f2e35c479e99545ecf8193
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102521893"
---
# <a name="collect-data-from-models-in-production"></a>Coletar dados de modelos em produção

Este artigo mostra como coletar dados de um modelo de Azure Machine Learning implantado em um cluster do AKS (serviço de kubernetes do Azure). Os dados coletados são então armazenados no armazenamento de BLOBs do Azure.

Quando a coleção estiver habilitada, os dados coletados ajudarão você a:

* [Monitore descompassos de dados](how-to-monitor-datasets.md) nos dados de produção coletados.

* Analisar os dados coletados usando [Power bi](#powerbi) ou [Azure Databricks](#databricks)

* Tome decisões melhores sobre quando treinar novamente ou otimizar seu modelo.

* Retreine seu modelo com os dados coletados.

## <a name="what-is-collected-and-where-it-goes"></a>O que é coletado e onde ele vai

Os dados a seguir podem ser coletados:

* Modelar dados de entrada de serviços Web implantados em um cluster AKS. Áudio de voz, imagens e vídeo *não* são coletados.
  
* Predições de modelo usando dados de entrada de produção.

>[!NOTE]
> A preagregação e os precálculos desses dados não fazem parte do serviço de coleta no momento.

A saída é salva no armazenamento de BLOBs. Como os dados são adicionados ao armazenamento de BLOBs, você pode escolher sua ferramenta favorita para executar a análise.

O caminho para os dados de saída no blob segue esta sintaxe:

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```

>[!NOTE]
> Em versões do SDK do Azure Machine Learning para Python anteriores à versão 0.1.0 A16, o `designation` argumento é nomeado `identifier` . Se você desenvolveu seu código com uma versão anterior, precisará atualizá-lo adequadamente.

## <a name="prerequisites"></a>Pré-requisitos

- Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://aka.ms/AMLFree) antes de começar.

- Um espaço de trabalho Azure Machine Learning, um diretório local que contém seus scripts e o SDK do Azure Machine Learning para Python deve ser instalado. Para saber como instalá-los, consulte [como configurar um ambiente de desenvolvimento](how-to-configure-environment.md).

- Você precisa de um modelo de aprendizado de máquina treinado para ser implantado no AKS. Se você não tiver um modelo, consulte o tutorial [treinar modelo de classificação de imagem](tutorial-train-models-with-aml.md) .

- Você precisa de um cluster AKS. Para obter informações sobre como criar um e implantá-lo, consulte [como implantar e onde](how-to-deploy-and-where.md).

- [Configure seu ambiente](how-to-configure-environment.md) e instale o [SDK de monitoramento de Azure Machine Learning](/python/api/overview/azure/ml/install).

## <a name="enable-data-collection"></a>Habilitar coleta de dados

Você pode habilitar a [coleta de dados](/python/api/azureml-monitoring/azureml.monitoring.modeldatacollector.modeldatacollector) independentemente do modelo implantado por meio de Azure Machine Learning ou outras ferramentas.

Para habilitar a coleta de dados, você precisa:

1. Abra o arquivo de pontuação.

1. Adicione o seguinte código na parte superior do arquivo:

   ```python 
   from azureml.monitoring import ModelDataCollector
   ```

1. Declare suas variáveis de coleta de dados na sua função `init`:

    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector("best_model", designation="inputs", feature_names=["feat1", "feat2", "feat3", "feat4", "feat5", "feat6"])
    prediction_dc = ModelDataCollector("best_model", designation="predictions", feature_names=["prediction1", "prediction2"])
    ```

    *CorrelationId* é um parâmetro opcional. Você não precisará usá-lo se seu modelo não exigir. O uso de *CorrelationId* o ajuda a mapear com mais facilidade com outros dados, como *LoanNumber* ou *CustomerID*.
    
    O parâmetro *Identifier* é usado posteriormente para criar a estrutura de pastas em seu BLOB. Você pode usá-lo para diferenciar dados brutos dos dados processados.

1. Adicione as seguintes linhas de código à função `run(input_df)`:

    ```python
    data = np.array(data)
    result = model.predict(data)
    inputs_dc.collect(data) #this call is saving our input data into Azure Blob
    prediction_dc.collect(result) #this call is saving our input data into Azure Blob
    ```

1. A coleta de dados *não* é definida automaticamente como **true** quando você implanta um serviço no AKs. Atualize seu arquivo de configuração, como no exemplo a seguir:

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True)
    ```

    Você também pode habilitar Application Insights para o monitoramento de serviço alterando essa configuração:

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True, enable_app_insights=True)
    ```

1. Para criar uma nova imagem e implantar o modelo de aprendizado de máquina, consulte [como implantar e onde](how-to-deploy-and-where.md).

1. Adicione o pacote de Pip do ' Azure-Monitoring ' às Conda do ambiente do serviço Web:
  ```Python
    env = Environment('webserviceenv')
    env.python.conda_dependencies = CondaDependencies.create(conda_packages=['numpy'],pip_packages=['azureml-defaults','azureml-monitoring','inference-schema[numpy-support]'])
  ```


## <a name="disable-data-collection"></a>Desativar coleta de dados

Você pode interromper a coleta de dados a qualquer momento. Use o código Python para desabilitar a coleta de dados.

  ```python 
  ## replace <service_name> with the name of the web service
  <service_name>.update(collect_model_data=False)
  ```

## <a name="validate-and-analyze-your-data"></a>Validar e analisar seus dados

Você pode escolher uma ferramenta de sua preferência para analisar os dados coletados no armazenamento de BLOBs.

### <a name="quickly-access-your-blob-data"></a>Acesse rapidamente seus dados de BLOB

1. Entre no [Portal do Azure](https://portal.azure.com).

1. Abra seu workspace.

1. Selecione **Armazenamento**.

    [![Selecione a opção de armazenamento](./media/how-to-enable-data-collection/StorageLocation.png)](././media/how-to-enable-data-collection/StorageLocation.png#lightbox)

1. Siga o caminho para os dados de saída do blob com esta sintaxe:

   ```
   /modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
   # example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
   ```

### <a name="analyze-model-data-using-power-bi"></a><a id="powerbi"></a> Analisar dados de modelo usando Power BI

1. Baixe e abra [Power bi desktop](https://www.powerbi.com).

1. Selecione **obter dados** e selecione [**armazenamento de BLOBs do Azure**](/power-bi/desktop-data-sources).

    [![Power BI configuração de BLOB](./media/how-to-enable-data-collection/PBIBlob.png)](././media/how-to-enable-data-collection/PBIBlob.png#lightbox)

1. Adicione o nome da sua conta de armazenamento e insira sua chave de armazenamento. Você pode encontrar essas informações selecionando **configurações**  >  **chaves de acesso** em seu BLOB.

1. Selecione o contêiner de **dados do modelo** e selecione **Editar**.

    [![Navegador Power BI](./media/how-to-enable-data-collection/pbiNavigator.png)](././media/how-to-enable-data-collection/pbiNavigator.png#lightbox)

1. No editor de consultas, clique na coluna **nome** e adicione sua conta de armazenamento.

1. Insira o caminho do modelo no filtro. Se você quiser procurar apenas os arquivos de um ano ou mês específico, basta expandir o caminho do filtro. Por exemplo, para procurar apenas dados de março, use este caminho de filtro:

   /modeldata/ \<subscriptionid> / \<resourcegroupname> / \<workspacename> / \<webservicename> / \<modelname> / \<modelversion> / \<designation> / \<year> /3

1. Filtre os dados que são relevantes para você com base nos valores de **nome** . Se você armazenou previsões e entradas, precisará criar uma consulta para cada uma.

1. Selecione as setas duplas para baixo ao lado do título de coluna de **conteúdo** para combinar os arquivos.

    [![Conteúdo de Power BI](./media/how-to-enable-data-collection/pbiContent.png)](././media/how-to-enable-data-collection/pbiContent.png#lightbox)

1. Selecione **OK**. Os dados são pré-carregados.

    [![Power BI combinar arquivos](./media/how-to-enable-data-collection/pbiCombine.png)](././media/how-to-enable-data-collection/pbiCombine.png#lightbox)

1. Selecione **fechar e aplicar**.

1. Se você adicionou entradas e previsões, suas tabelas serão automaticamente ordenadas por valores **RequestId** .

1. Comece a criar relatórios personalizados em seus dados de modelo.

### <a name="analyze-model-data-using-azure-databricks"></a><a id="databricks"></a> Analisar dados de modelo usando Azure Databricks

1. Crie um [espaço de trabalho Azure Databricks](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal).

1. Vá para seu workspace do Databricks.

1. No espaço de trabalho do databricks, selecione **carregar dados**.

    [![Selecionando a opção de dados de carregamento do databricks](./media/how-to-enable-data-collection/dbupload.png)](././media/how-to-enable-data-collection/dbupload.png#lightbox)

1. Selecione **criar nova tabela** e selecione **outras fontes de dados**  >  **armazenamento de BLOBs do Azure**  >  **criar tabela no bloco de anotações**.

    [![Criação de tabela do databricks](./media/how-to-enable-data-collection/dbtable.PNG)](././media/how-to-enable-data-collection/dbtable.PNG#lightbox)

1. Atualize o local dos seus dados. Veja um exemplo:

    ```
    file_location = "wasbs://mycontainer@storageaccountname.blob.core.windows.net/modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/*/*/data.csv" 
    file_type = "csv"
    ```

    [![Configuração do databricks](./media/how-to-enable-data-collection/dbsetup.png)](././media/how-to-enable-data-collection/dbsetup.png#lightbox)

1. Siga as etapas no modelo para exibir e analisar seus dados.

## <a name="next-steps"></a>Próximas etapas

[Detectar descompasso de dados](how-to-monitor-datasets.md) nos dados coletados.