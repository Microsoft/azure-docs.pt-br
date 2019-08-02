---
title: Coletar dados em seus modelos de produção
titleSuffix: Azure Machine Learning service
description: Aprenda a coletar dados do modelo de entrada do Aprendizado no Azure Machine em um armazenamento de Blobs do Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: marthalc
author: marthalc
ms.date: 07/15/2019
ms.custom: seodec18
ms.openlocfilehash: 81ffe8618e07f2e49e4439ea57e254e5d37974f9
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227956"
---
# <a name="collect-data-for-models-in-production"></a>Coletar dados para modelos em produção

Neste artigo, você pode aprender como coletar dados de modelo de entrada dos serviços de Aprendizado de Máquina do Azure que você implantou no Azure Kubernetes Cluster (AKS) em um armazenamento de Blob do Azure. 

Depois de ativado, esses dados coletados ajudam você a:
* [Monitorar](how-to-monitor-data-drift.md) descompassos de dados como dados de produção insira seu modelo

* Tome melhores decisões sobre quando treinar ou otimizar seu modelo

* Treine novamente seu modelo com os dados coletados

## <a name="what-is-collected-and-where-does-it-go"></a>O que é coletado e para onde vai?

Os dados a seguir podem ser coletados:
* Dados de **entrada** do modelo  de serviços da Web implantados no Azure Kubernetes Cluster (AKS) (voz, imagens e vídeo **não** coletadas) 
  
* Predições de modelo usando dados de entrada de produção.

> [!Note]
> Pré-agregação ou pré-cálculos sobre esses dados não fazem parte do serviço no momento.   

A saída é salva em um Blob do Azure. Como os dados são adicionados a um Blob do Azure, você pode escolher sua ferramenta favorita para executar a análise. 

O caminho para os dados de saída no blob segue esta sintaxe:

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<identifier>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```

## <a name="prerequisites"></a>Pré-requisitos

- Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Teste hoje mesmo a [versão gratuita ou paga do Serviço do Azure Machine Learning](https://aka.ms/AMLFree).

- Um workspace do serviço do Azure Machine Learning, um diretório local que contém seus scripts, e o SDK do Azure Machine Learning para Python instalado. Aprenda como obter esses pré-requisitos usando o documento [Como configurar um ambiente de desenvolvimento](how-to-configure-environment.md).

- Um modelo de aprendizado de máquina treinado para ser implantado para o serviço de Kubernetes do Azure (AKS). Se você não tiver um, consulte o tutorial [modelo de classificação de imagens de trem](tutorial-train-models-with-aml.md).

- Um cluster do Serviço de Kubernetes do Azure. Para obter informações sobre como criar e implantar um, confira o documento [Como e onde implantar](how-to-deploy-and-where.md).

- [Configurar seu ambiente](how-to-configure-environment.md) e instale o [SDK monitoramento](https://aka.ms/aml-monitoring-sdk).

## <a name="enable-data-collection"></a>Habilitar coleta de dados
A coleta de dados pode ser ativada independentemente do modelo que está sendo implantado por meio do Serviço de Aprendizado de Máquina do Azure ou de outras ferramentas. 

Para habilitá-lo, você precisa:

1. Abra o arquivo de pontuação. 

1. Adicione o [seguinte código](https://aka.ms/aml-monitoring-sdk) na parte superior do arquivo:

   ```python 
   from azureml.monitoring import ModelDataCollector
   ```

2. Declare suas variáveis de coleta de dados na sua função `init()`:

    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector("best_model", identifier="inputs", feature_names=["feat1", "feat2", "feat3". "feat4", "feat5", "feat6"])
    prediction_dc = ModelDataCollector("best_model", identifier="predictions", feature_names=["prediction1", "prediction2"])
    ```

    *CorrelationId* é um parâmetro opcional, você não precisa configurá-lo se seu modelo não exigir isso. Ter uma correlação em vigor ajuda você a mapear com mais facilidade outros dados. (Os exemplos incluem: LoanNumber, CustomerId etc.)
    
    *Identificador* é usado mais tarde para construir a estrutura de pastas no seu Blob, ele pode ser usado para dividir dados "brutos" versus "processados".

3.  Adicione as seguintes linhas de código à função `run(input_df)`:

    ```python
    data = np.array(data)
    result = model.predict(data)
    inputs_dc.collect(data) #this call is saving our input data into Azure Blob
    prediction_dc.collect(result) #this call is saving our input data into Azure Blob
    ```

4. A coleta de dados é **não** automaticamente definida como **verdadeiro** quando você implanta um serviço no AKS, portanto, você deve atualizar seu arquivo de configuração, como: 

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True)
    ```
    O AppInsights para monitoramento de serviço também pode ser ativado alterando essa configuração:
    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True, enable_app_insights=True)
    ``` 

5. Para criar uma nova imagem e implantar o serviço, confira o documento [Como e onde implantar](how-to-deploy-and-where.md).


Se você já tiver um serviço com as dependências instaladas no **arquivo de ambiente** e no **arquivo de pontuação**, ative a coleta de dados:

1. Vá para [Portal do Azure](https://portal.azure.com).

1. Abra seu workspace.

1. Vá para **Implantações** -> **Selecione o serviço** -> **Edite**.

   ![Editar serviço](media/how-to-enable-data-collection/EditService.PNG)

1. Em **Configurações avançadas**, desmarque **Ativar coleta de dados do modelo**. 

    [![Rápida verificação de dados](media/how-to-enable-data-collection/CheckDataCollection.png)](./media/how-to-enable-data-collection/CheckDataCollection.png#lightbox)

   Nesta janela, você também pode escolher "Ativar diagnósticos de Appinsights" para rastrear a integridade de seu serviço.  

1. Selecione **atualização** para aplicar a alteração.


## <a name="disable-data-collection"></a>Desativar coleta de dados
Você pode parar de coletar dados a qualquer momento. Use o código Python ou o portal do Azure para desabilitar a coleta de dados.

+ Opção 1 - desabilitar no portal do Azure: 
  1. Entre no [Portal do Azure](https://portal.azure.com).

  1. Abra seu workspace.

  1. Vá para **Implantações** -> **Selecione o serviço** -> **Edite**.

     [![Opções de edição](media/how-to-enable-data-collection/EditService.PNG)](./media/how-to-enable-data-collection/EditService.PNG#lightbox)

  1. Em **Configurações avançadas**, desmarque **Ativar coleta de dados do modelo**. 

     [![Desmarque a opção de coleta de dados](media/how-to-enable-data-collection/UncheckDataCollection.png)](./media/how-to-enable-data-collection/UncheckDataCollection.png#lightbox)

  1. Selecione **Atualizar** para aplicar a alteração.

+ Opção 2 - Use o Python para desabilitar a coleta de dados:

  ```python 
  ## replace <service_name> with the name of the web service
  <service_name>.update(collect_model_data=False)
  ```

## <a name="validate-your-data-and-analyze-it"></a>Validar seus dados e analisá-los
Você pode escolher qualquer ferramenta de sua preferência para analisar os dados coletados para o Blob do Azure. 

Para acessar rapidamente os dados do blob:
1. Entre no [Portal do Azure](https://portal.azure.com).

1. Abra seu workspace.
1. Clique em **Armazenamento**.

    [![Armazenamento](media/how-to-enable-data-collection/StorageLocation.png)](./media/how-to-enable-data-collection/StorageLocation.png#lightbox)

1. Siga o caminho para os dados de saída no blob com esta sintaxe:

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<identifier>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```


### <a name="analyzing-model-data-through-power-bi"></a>Analisando dados do modelo por meio do Power BI

1. Faça o download e abra o [PowerBi Desktop](https://www.powerbi.com)

1. Selecione **Obter Dados** e clique em [**Armazenamento de Blobs do Azure**](https://docs.microsoft.com/power-bi/desktop-data-sources).

    [![Instalação de Blob do PBI](media/how-to-enable-data-collection/PBIBlob.png)](./media/how-to-enable-data-collection/PBIBlob.png#lightbox)


1. Adicione o nome da sua conta de armazenamento e insira sua chave de armazenamento. Você pode encontrar esta informação em **Configurações** >> Chaves de acesso do blob. 

1. Selecione o contêiner **modeldata** e clique em **Editar**. 

    [![Navegador PBI](media/how-to-enable-data-collection/pbiNavigator.png)](./media/how-to-enable-data-collection/pbiNavigator.png#lightbox)

1. No editor de consultas, clique na coluna "Nome" e adicione sua conta de armazenamento 1. Caminho do modelo para o filtro. Nota: se você quiser apenas examinar arquivos de um ano ou mês específico, basta expandir o caminho do filtro. Por exemplo, basta examinar dados de março: / modeldata/subscriptionid > / resourcegroupname > / workspacename > / webservicename > / modelname > / modelversion > / identificador > / ano > / 3

1. Filtre os dados relevantes para você com base no **Nome**. Se você armazenou **previsões** e **entradas** você precisará criar uma consulta de cada um.

1. Clique na seta dupla à parte da coluna **Conteúdo** para combinar os arquivos. 

    [![Conteúdo do PBI](media/how-to-enable-data-collection/pbiContent.png)](./media/how-to-enable-data-collection/pbiContent.png#lightbox)

1. Clique em OK e os dados serão pré-carregados.

    [![pbiCombine](media/how-to-enable-data-collection/pbiCombine.png)](./media/how-to-enable-data-collection/pbiCombine.png#lightbox)

1. Agora você pode clicar **fechar e aplicar** .

1.  Se você adicionou entradas e previsões, suas tabelas serão correlacionadas automaticamente por **RequestId**.

1. Comece a criar relatórios personalizados em seus dados de modelo.


### <a name="analyzing-model-data-using-databricks"></a>Analisando dados do modelo usando Databricks

1. Criar uma [espaço de trabalho do Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal). 

1. Vá para seu espaço de trabalho do Databricks. 

1. Na área de trabalho do seu banco de dados, selecione **Upload Data**.

    [![Carregamento de banco de dados](media/how-to-enable-data-collection/dbupload.png)](./media/how-to-enable-data-collection/dbupload.png#lightbox)

1. Crie uma nova tabela e selecione **Outras fontes de dados** -> Armazenamento de Blobs do Azure -> Criar Tabela no Notebook.

    [![Tabela de banco de dados](media/how-to-enable-data-collection/dbtable.PNG)](./media/how-to-enable-data-collection/dbtable.PNG#lightbox)

1. Atualize o local dos seus dados. Veja um exemplo:

    ```
    file_location = "wasbs://mycontainer@storageaccountname.blob.core.windows.net/modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/*/*/data.csv" 
    file_type = "csv"
    ```
 
    [![DBsetup](media/how-to-enable-data-collection/dbsetup.png)](./media/how-to-enable-data-collection/dbsetup.png#lightbox)

1. Siga as etapas no modelo para exibir e analisar seus dados. 

## <a name="example-notebook"></a>Caderno de exemplo

O notebook [how-to-use-azureml/deployment/enable-data-collection-for-models-in-aks/enable-data-collection-for-models-in-aks.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-data-collection-for-models-in-aks/enable-data-collection-for-models-in-aks.ipynb) demonstra os conceitos neste artigo.  

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]
