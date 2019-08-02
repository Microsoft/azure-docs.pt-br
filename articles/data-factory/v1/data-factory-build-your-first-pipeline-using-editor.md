---
title: Criar seu primeiro data factory (portal do Azure) | Microsoft Docs
description: Neste tutorial, crie um pipeline de exemplo do Azure Data Factory usando o Data Factory Editor no portal do Azure.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: ''
editor: ''
ms.assetid: d5b14e9e-e358-45be-943c-5297435d402d
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 2a7e2f9e5018bdad2a1ed2c6edcb727a2ffdcddd
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839123"
---
# <a name="tutorial-build-your-first-data-factory-by-using-the-azure-portal"></a>Tutorial: Criar seu primeiro data factory usando o portal do Azure
> [!div class="op_single_selector"]
> * [Visão geral e pré-requisitos](data-factory-build-your-first-pipeline.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Modelo do Azure Resource Manager](data-factory-build-your-first-pipeline-using-arm.md)
> * [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)


> [!NOTE]
> Este artigo se aplica à versão 1 do Azure Data Factory, que geralmente está disponível. Caso esteja usando a versão atual do serviço Data Factory, confira [Início Rápido: Criar um data factory usando o Data Factory](../quickstart-create-data-factory-dot-net.md).

> [!WARNING]
> O editor de JSON no portal do Azure para criação e implantação de pipelines do ADF v1 será DESATIVADO em 31 de julho de 2019. Após o dia 31 de julho de 2019, você poderá continuar usando [cmdlets do PowerShell para o ADF v1](https://docs.microsoft.com/powershell/module/az.datafactory/?view=azps-2.4.0&viewFallbackFrom=azps-2.3.2), o [SDK do .NET para o ADF v1](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.datafactories.models?view=azure-dotnet) e as [APIs REST do ADF v1](https://docs.microsoft.com/rest/api/datafactory/) para criar e implantar seus pipelines do ADF v1.

Neste artigo, você aprende a usar o [portal do Azure](https://portal.azure.com/) para criar seu primeiro data factory. Para fazer o tutorial usando outras ferramentas/SDKs, escolha uma das opções da lista suspensa. 

O pipeline neste tutorial tem uma atividade: atividade de Hive do Azure HDInsight. Esta atividade executa um script de Hive em um cluster do HDInsight que transforma os dados de entrada para gerar dados de saída. O pipeline é agendado para ser executado uma vez por mês entre os horários de início e término especificados. 

> [!NOTE]
> O pipeline de dados neste tutorial transforma os dados de entrada para gerar dados de saída. Para obter um tutorial sobre como copiar dados usando o Data Factory, confira [Tutorial: Copiar dados do Armazenamento de Blobs do Azure para o Banco de Dados SQL do Azure](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
> 
> Um pipeline pode ter mais de uma atividade. E você pode encadear duas atividades (executar uma atividade após a outra) definindo o conjunto de dados de saída de uma atividade como o conjunto de dados de entrada da outra atividade. Para saber mais, confira [Agendamento e execução no Data Factory](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline).

## <a name="prerequisites"></a>Pré-requisitos
Leia [Visão geral do tutorial](data-factory-build-your-first-pipeline.md) e siga as etapas na seção "Pré-requisitos".

Este artigo não fornece uma visão geral conceitual do serviço do Data Factory. Para obter mais informações sobre o serviço, leia [Introdução ao Azure Data Factory](data-factory-introduction.md).  

## <a name="create-a-data-factory"></a>Criar uma data factory
Uma fábrica de dados pode ter um ou mais pipelines. Um pipeline em um data factory pode ter uma ou mais atividades. Por exemplo, uma atividade de cópia para copiar dados de uma fonte para um armazenamento de dados de destino. Outro exemplo é uma atividade de Hive do HDInsight que executa um script de Hive para transformar os dados de entrada para produzir dados de saída. 

Para criar um data factory, siga estas etapas:

1. Entre no [Portal do Azure](https://portal.azure.com/).

1. Selecione **Novo** > **Dados + Análise** > **Data Factory**.

   ![Folha Criar](./media/data-factory-build-your-first-pipeline-using-editor/create-blade.png)

1. Na folha **Novo data factory**, insira **GetStartedDF** como o **Nome**.

   ![Folha Nova data factory](./media/data-factory-build-your-first-pipeline-using-editor/new-data-factory-blade.png)

   > [!IMPORTANT]
   > O nome do data factory deve ser globalmente exclusivo. Se a mensagem de erro "O nome do data factory GetStartedDF não está disponível” for exibida, altere o nome do data factory. Por exemplo, use yournameGetStartedDF e crie o data factory novamente. Para obter mais informações sobre regras de nomenclatura, confira [Data Factory: Regras de nomenclatura](data-factory-naming-rules.md).
   >
   > O nome do data factory pode ser registrado futuramente como um nome DNS e tornar-se visível publicamente.
   >
   >
1. Em **Assinatura**, escolha a assinatura do Azure onde você deseja que o data factory seja criado.

1. Escolha um grupo de recursos existente ou crie um grupo de recursos. Para o tutorial, crie um grupo de recursos chamado: **ADFGetStartedRG**.

1. Em **Local**, informe uma localização para o data factory. Apenas as regiões com suporte pelo serviço Data Factory são mostradas na lista suspensa.

1. Marque a caixa de seleção **Fixar no painel**.

1. Selecione **Criar**.

   > [!IMPORTANT]
   > Para criar instâncias do Data Factory, você deve ser um membro da função [Colaborador do Data Factory](../../role-based-access-control/built-in-roles.md#data-factory-contributor) no nível de assinatura/grupo de recursos.
   >
   >
1. No painel, o bloco com o seguinte status é exibido: **Implantando o Data Factory**:    

   ![Status: Implantando Data Factory](./media/data-factory-build-your-first-pipeline-using-editor/creating-data-factory-image.png)

1. Após o data factory ter sido criado, a página do **Data factory** será exibida com seu conteúdo.     

    ![Folha Data factory](./media/data-factory-build-your-first-pipeline-using-editor/data-factory-blade.png)

Antes de criar um pipeline no data factory, primeiro você precisará criar algumas entidades do Data Factory. Primeiro, crie serviços vinculados para vincular armazenamentos de dados/computadores ao armazenamento de dados. Em seguida, defina conjuntos de dados de entrada e saída para representar dados de entrada/saída em armazenamentos de dados vinculados. Finalmente, crie o pipeline com uma atividade que usa esses conjuntos de dados.

## <a name="create-linked-services"></a>Criar serviços vinculados
Nesta etapa, você vinculará sua conta de armazenamento do Azure e um cluster do HDInsight sob demanda ao data factory. A conta de armazenamento manterá os dados de entrada e de saída para o pipeline neste exemplo. O serviço vinculado do HDInsight é usado para executar um script do Hive especificado na atividade do pipeline neste exemplo. Identifique quais [serviços de computação](data-factory-data-movement-activities.md)/[ de armazenamento de dados](data-factory-compute-linked-services.md) são usados no cenário. Em seguida, vincule esses serviços ao data factory criando serviços vinculados.  

### <a name="create-a-storage-linked-service"></a>Criar um serviço vinculado do Armazenamento
Nesta etapa, você vincula sua conta de armazenamento ao data factory. Neste tutorial, use a mesma conta de armazenamento para armazenar dados de entrada/saída e o arquivo de script do HQL.

1. Clique na folha **Data factory** para **GetStartedDF**, escolha **Criar e implantar**. Você vê o Data Factory Editor.

   ![Bloco Criar e implantar](./media/data-factory-build-your-first-pipeline-using-editor/data-factory-author-deploy.png)

1. Clique em **Novo armazenamento de dados** e escolha **Armazenamento do Azure**.

   ![Folha Novo armazenamento de dados](./media/data-factory-build-your-first-pipeline-using-editor/new-data-store-azure-storage-menu.png)

1. Você verá o script JSON para criar um serviço de armazenamento vinculado no editor.

   ![Serviço vinculado de armazenamento](./media/data-factory-build-your-first-pipeline-using-editor/azure-storage-linked-service.png)

1. Substitua o **nome da conta** pelo nome da sua conta de armazenamento. Substitua a **chave de conta** com a chave de acesso da conta de armazenamento. Para saber como conseguir sua chave de acesso de armazenamento, confira as informações sobre como exibir, copiar e regenerar chaves de acesso de armazenamento em [Gerenciar sua conta de armazenamento](../../storage/common/storage-account-manage.md#access-keys).

1. Clique em **Implantar** na barra de comandos para implantar o serviço vinculado.

    ![Botão Implantar](./media/data-factory-build-your-first-pipeline-using-editor/deploy-button.png)

   Depois que o serviço vinculado é implantado com êxito, a janela Rascunho 1 desaparece. Você verá **AzureStorageLinkedService** no modo de exibição de árvore à esquerda.

    ![AzureStorageLinkedService](./media/data-factory-build-your-first-pipeline-using-editor/StorageLinkedServiceInTree.png)    

### <a name="create-an-hdinsight-linked-service"></a>Como criar um serviço vinculado do HDInsight
Nesta etapa, você vincula um cluster do HDInsight sob demanda ao seu data factory. O cluster do HDInsight é criado automaticamente no tempo de execução. O cluster é excluído após a conclusão do processamento e se mantém ocioso durante o período especificado.

1. No Data Factory Editor, selecione **Mais** > **Nova computação** > **Cluster do HDInsight sob demanda**.

    ![Nova computação](./media/data-factory-build-your-first-pipeline-using-editor/new-compute-menu.png)

1. Copie e cole o snippet a seguir na janela de Rascunho-1. O snippet JSON descreve as propriedades usadas para criar o cluster do HDInsight sob demanda.

    ```JSON
    {
        "name": "HDInsightOnDemandLinkedService",
        "properties": {
            "type": "HDInsightOnDemand",
            "typeProperties": {
                "version": "3.5",
                "clusterSize": 1,
                "timeToLive": "00:05:00",
                "osType": "Linux",
                "linkedServiceName": "AzureStorageLinkedService"
            }
        }
    }
    ```

    A tabela a seguir fornece descrições das propriedades de JSON usadas no snippet de código:

   | Propriedade | DESCRIÇÃO |
   |:--- |:--- |
   | clusterSize |Especifica o tamanho do cluster HDInsight. |
   | timeToLive | Especifica o tempo ocioso do cluster do HDInsight antes de ser excluído. |
   | linkedServiceName | Especifica a conta de armazenamento usada para armazenar os logs gerados pelo HDInsight. |

    Observe os seguintes pontos:

     a. O data factory cria um cluster do HDInsight baseado no Linux para você com as propriedades do JSON. Para obter mais informações, confira [Serviço vinculado do HDInsight sob demanda](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service).

     b. Você usa seu próprio cluster do HDInsight em vez de usar um cluster do HDInsight sob demanda. Para obter mais informações, confira [Serviço vinculado do HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service).

     c. O cluster do HDInsight cria um contêiner padrão no armazenamento de blobs especificado na propriedade (**linkedServiceName**) do JSON. O HDInsight não exclui esse contêiner quando o cluster é excluído. Este comportamento ocorre por design. Com o serviço vinculado HDInsight sob demanda, um cluster do HDInsight é criado sempre que uma fatia é processada, a menos que haja um cluster ativo existente (**timeToLive**). O cluster será excluído automaticamente quando o processamento for concluído.

     Quanto mais fatias forem processadas, mais contêineres você verá no armazenamento de blobs. Se você não precisa deles para solução de problemas dos trabalhos, convém excluí-los para reduzir o custo de armazenamento. Os nomes desses contêineres seguem um padrão: "adf**yourdatafactoryname**-**linkedservicename**-datetimestamp." Use ferramentas como o [Gerenciador de Armazenamento do Azure](https://storageexplorer.com/) para excluir contêineres do armazenamento de blobs.

     Para obter mais informações, confira [Serviço vinculado do HDInsight sob demanda](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service).

1. Clique em **Implantar** na barra de comandos para implantar o serviço vinculado.

    ![Opções de Implantação](./media/data-factory-build-your-first-pipeline-using-editor/ondemand-hdinsight-deploy.png)

1. Confirme que você vê **AzureStorageLinkedService** e **HDInsightOnDemandLinkedService** no modo de exibição de árvore à esquerda.

    ![Modo de exibição de árvore com serviços vinculados](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-linked-services.png)

## <a name="create-datasets"></a>Criar conjuntos de dados
Nesta etapa, você cria conjuntos de dados para representar dados de entrada e de saída para o processamento do Hive. Esses conjuntos de dados se referem ao AzureStorageLinkedService que você criou anteriormente neste tutorial. O serviço vinculado aponta para uma conta de armazenamento. Os conjuntos de dados especificam o contêiner, a pasta e o nome do arquivo no armazenamento que contém a entrada e a saída de dados.   

### <a name="create-the-input-dataset"></a>Como criar o conjunto de dados de entrada
1. No Data Factory Editor, selecione **Mais** > **Novo conjunto de dados** > **Armazenamento de Blobs do Azure**.

    ![Novo conjunto de dados](./media/data-factory-build-your-first-pipeline-using-editor/new-data-set.png)

1. Copie e cole o snippet a seguir na janela de Rascunho-1. No snippet JSON, você cria um conjunto de dados chamado **AzureBlobInput** que representa os dados de entrada para uma atividade no pipeline. Além disso, você especifica que os dados de entrada estão localizados no contêiner de blobs denominado **adfgetstarted** e na pasta denominada **inputdata**.

    ```JSON
    {
        "name": "AzureBlobInput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "fileName": "input.log",
                "folderPath": "adfgetstarted/inputdata",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability": {
                "frequency": "Month",
                "interval": 1
            },
            "external": true,
            "policy": {}
        }
    }
    ```
    A tabela a seguir fornece descrições das propriedades de JSON usadas no snippet de código:

   | Propriedade | Aninhado em | DESCRIÇÃO |
   |:--- |:--- |:--- |
   | type | properties |A propriedade type é definida como **AzureBlob** porque os dados residem no armazenamento de blobs. |
   | linkedServiceName | format |Refere-se ao AzureStorageLinkedService que você criou anteriormente. |
   | folderPath | typeProperties | Especifica o contêiner de blobs e a pasta que contém blobs de entrada. | 
   | fileName | typeProperties |Essa propriedade é opcional. Se você omitir essa propriedade, todos os arquivos de folderPath serão selecionados. Neste tutorial, somente o arquivo input.log é processado. |
   | type | format |Os arquivos de log estão em formato de texto. Utilize **TextFormat**. |
   | columnDelimiter | format |As colunas nos arquivos de log são delimitadas pelo caractere de vírgula (`,`). |
   | frequency/interval | availability |A frequência é definida como **Mês** e o intervalo como **1**, o que significa que as fatias de entrada estão disponíveis mensalmente. |
   | external | properties | Essa propriedade será definida como **true** se os dados de entrada não forem gerados por esse pipeline. Neste tutorial, o arquivo input.log não é gerado por esse pipeline, portanto, definimos a propriedade como **true**. |

    Para saber mais informações sobre essas propriedades JSON, confira [Conector do Blob do Azure](data-factory-azure-blob-connector.md#dataset-properties).

1. Clique em **Implantar** na barra de comandos para implantar o conjunto de dados recém-criado. Confira o conjunto de dados no modo de exibição de árvore à esquerda.

### <a name="create-the-output-dataset"></a>Como criar um conjunto de dados de saída
Você cria o conjunto de dados de saída para representar os dados de saída armazenados no armazenamento de blobs.

1. No Data Factory Editor, selecione **Mais** > **Novo conjunto de dados** > **Armazenamento de Blobs do Azure**.

1. Copie e cole o snippet a seguir na janela de Rascunho-1. No snippet JSON, você cria um conjunto de dados chamado **AzureBlobOutput** e especifica a estrutura dos dados produzidos pelo script de Hive. Além disso, você especifica que os resultados estão armazenados no contêiner de blobs denominado **adfgetstarted** e na pasta denominada **partitioneddata**. A seção **availability** especifica que o conjunto de dados de saída é produzido mensalmente.

    ```JSON
    {
      "name": "AzureBlobOutput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
          "folderPath": "adfgetstarted/partitioneddata",
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ","
          }
        },
        "availability": {
          "frequency": "Month",
          "interval": 1
        }
      }
    }
    ```
    Confira a seção "Como criar o conjunto de dados de entrada" para obter descrições dessas propriedades. Você não define a propriedade externa em um conjunto de dados de saída porque o conjunto de dados é produzido pelo serviço Data Factory.

1. Clique em **Implantar** na barra de comandos para implantar o conjunto de dados recém-criado.

1. Verifique se o conjunto de dados foi criado com êxito.

    ![Modo de exibição de árvore com serviços vinculados](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-data-set.png)

## <a name="create-a-pipeline"></a>Criar um pipeline
Nesta etapa, você cria seu primeiro pipeline com a atividade de Hive do HDInsight. A fatia de entrada está disponível mensalmente (frequência é o mês e o intervalo é 1). A fatia de saída é produzida mensalmente. A propriedade scheduler para a atividade também é definida como mensal. As configurações para o conjunto de dados de saída e o agendador de atividades devem corresponder. Atualmente, o conjunto de dados de saída é o que aciona a agenda. Assim, você deve criar um conjunto de dados de saída, mesmo que a atividade não produza qualquer saída. Se a atividade não receber entradas, ignore a criação de conjunto de dados de entrada. As propriedades usadas no snippet em JSON a seguir são explicadas no final desta seção.

1. No Data Factory Editor, selecione **Mais** > **Novo pipeline**.

    ![Opção Novo pipeline](./media/data-factory-build-your-first-pipeline-using-editor/new-pipeline-button.png)

1. Copie e cole o snippet a seguir na janela de Rascunho-1.

   > [!IMPORTANT]
   > Substitua **storageaccountname** pelo nome da sua conta de armazenamento no snippet em JSON.
   >
   >

    ```JSON
    {
        "name": "MyFirstPipeline",
        "properties": {
            "description": "My first Azure Data Factory pipeline",
            "activities": [
                {
                    "type": "HDInsightHive",
                    "typeProperties": {
                        "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                        "scriptLinkedService": "AzureStorageLinkedService",
                        "defines": {
                            "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
                            "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
                        }
                    },
                    "inputs": [
                        {
                            "name": "AzureBlobInput"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobOutput"
                        }
                    ],
                    "policy": {
                        "concurrency": 1,
                        "retry": 3
                    },
                    "scheduler": {
                        "frequency": "Month",
                        "interval": 1
                    },
                    "name": "RunSampleHiveActivity",
                    "linkedServiceName": "HDInsightOnDemandLinkedService"
                }
            ],
            "start": "2017-07-01T00:00:00Z",
            "end": "2017-07-02T00:00:00Z",
            "isPaused": false
        }
    }
    ```

    No snippet de JSON, você cria um pipeline que consiste de uma única atividade que usa o Hive para processar dados em um cluster do HDInsight.

    O arquivo de script de Hive, **partitionweblogs.hql**, armazenado na conta de armazenamento, é especificada pelo scriptLinkedService que é denominado **AzureStorageLinkedService**. Você pode encontrá-lo na pasta **script** no contêiner **adfgetstarted**.

    A seção **defines** é usada para especificar as configurações em tempo de execução passadas para o script de Hive como valores de configuração de Hive. Os exemplos são ${hiveconf: inputtable} e ${hiveconf:partitionedtable}.

    As propriedades **start** e **end** do pipeline especificam o período ativo do pipeline.

    Na atividade do JSON, especifique que o script do Hive é executado na computação especificada pelo **linkedServiceName**: **HDInsightOnDemandLinkedService**.

   > [!NOTE]
   > Para obter mais informações sobre as propriedades JSON usadas no exemplo, confira a seção “JSON do pipeline" [Pipelines e atividades no Data Factory](data-factory-create-pipelines.md).
   >
   >
1. Confirme o seguinte:

   a. O arquivo **input.log** existe na pasta **inputdata** do contêiner **adfgetstarted** no armazenamento de blobs.

   b. O arquivo **partitionweblogs.hql** existe na pasta **script** do contêiner **adfgetstarted** no armazenamento de blobs. Se não encontrar esses arquivos, siga as etapas na seção "Pré-requisitos" [Visão geral do Tutorial](data-factory-build-your-first-pipeline.md).

   c. Confirme se você substituiu **storageaccountname** pelo nome da sua conta de armazenamento no JSON do pipeline.

1. Clique em **Implantar** na barra de comandos para implantar o pipeline. Como os tempos de**início** e de **término** são definidos no passado e **isPaused** está definido como **false**, o pipeline (a atividade no pipeline) é imediatamente executado após a implantação.

1. Confirme que você vê o pipeline no modo de exibição de árvore.

    ![Modo de exibição de árvore com pipeline](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-pipeline.png)



## <a name="monitor-a-pipeline"></a>Como monitorar um pipeline
### <a name="monitor-a-pipeline-by-using-the-diagram-view"></a>Monitorar um pipeline usando o modo de exibição de diagrama
1. Na folha **Data factory**, selecione **Diagrama**.

    ![Bloco do diagrama](./media/data-factory-build-your-first-pipeline-using-editor/diagram-tile.png)

1. Na visão **Diagrama**, você tem uma visão geral dos pipelines e dos conjuntos de dados usados neste tutorial.

    ![Modo de Exibição de Diagrama](./media/data-factory-build-your-first-pipeline-using-editor/diagram-view-2.png)

1. Para exibir todas as atividades no pipeline, clique com o botão direito do mouse no pipeline no diagrama e clique em **Abrir pipeline**.

    ![Menu do pipeline aberto](./media/data-factory-build-your-first-pipeline-using-editor/open-pipeline-menu.png)

1. Confirme se você vê a **Atividade de Hive** no pipeline.

    ![Abrir a exibição do pipeline](./media/data-factory-build-your-first-pipeline-using-editor/open-pipeline-view.png)

    Para voltar à exibição anterior, selecione **Data factory** no menu na parte superior.

1. Na exibição **Diagrama**, clique duas vezes no conjunto de dados **AzureBlobInput**. Confirme se a fatia está no estado **Pronto**. Pode levar alguns minutos para que a fatia apareça como **Pronto**. Se não aparecer depois de algum tempo, confira se o arquivo de entrada (**input.log**) está posicionado no contêiner correto (**adfgetstarted**) e na pasta (**inputdata**).

   ![Fatia de entrada no estado Pronto](./media/data-factory-build-your-first-pipeline-using-editor/input-slice-ready.png)

1. Feche a folha **AzureBlobInput**.

1. Na exibição **Diagrama**, clique duas vezes no conjunto de dados **AzureBlobOutput**. Você verá a fatia que está sendo processada.

   ![Conjunto de dados de processamento em andamento](./media/data-factory-build-your-first-pipeline-using-editor/dataset-blade.png)

1. Após a conclusão do processamento, você verá a fatia no estado **Pronto**.

   ![Conjunto de dados no estado Pronto](./media/data-factory-build-your-first-pipeline-using-editor/dataset-slice-ready.png)  

   > [!IMPORTANT]
   > A criação de um cluster do HDInsight sob demanda geralmente leva 20 minutos. Portanto, espere que o pipeline demore cerca de 30 minutos para processar a fatia.
   >
   >

1. Quando a fatia estiver no estado **Pronto**, verifique a pasta **partitioneddata** no contêiner **adfgetstarted** em seu armazenamento de blobs para os dados de saída.  

   ![Dados de saída](./media/data-factory-build-your-first-pipeline-using-editor/three-ouptut-files.png)

1. Selecione a fatia para ver mais informações sobre ela em uma folha **Fatia de dados**.

    ![Informações sobre a fatia de dados](./media/data-factory-build-your-first-pipeline-using-editor/data-slice-details.png)

1. Na lista **Atividades em execução**, selecione uma atividade em execução para obter mais informações sobre ela. (Neste cenário, é uma atividade de Hive.) As informações são exibidas na folha **Detalhes da atividade em execução**.   

    ![Janela Detalhes da atividade em execução](./media/data-factory-build-your-first-pipeline-using-editor/activity-window-blade.png)    

   Nos arquivos de log, confira a consulta do Hive que foi executada e as informações de status. Esses logs são úteis para solucionar problemas.
   Para obter mais informações, confira [Como monitorar e gerenciar pipelines usando folhas de portal do Azure](data-factory-monitor-manage-pipelines.md).

> [!IMPORTANT]
> O arquivo de entrada é excluído quando a fatia é processada com êxito. Portanto, se você quiser executar novamente a fatia ou refazer o tutorial, carregue o arquivo de entrada (**input.log**) para a pasta **inputdata** do contêiner **adfgetstarted**.
>
>

### <a name="monitor-a-pipeline-by-using-the-monitor--manage-app"></a>Como monitorar um pipeline usando o aplicativo Monitorar e Gerenciar
Você também pode usar o aplicativo Monitorar e Gerenciar para monitorar os pipelines. Para obter mais informações sobre como usar este aplicativo, confira [Como monitorar e gerenciar os pipelines do Data Factory usando o aplicativo Monitorar e Gerenciar](data-factory-monitor-manage-app.md).

1. Clique no bloco **Monitorar e Gerenciar** na home page do seu data factory.

    ![Bloco Monitorar e Gerenciar](./media/data-factory-build-your-first-pipeline-using-editor/monitor-and-manage-tile.png)

1. No aplicativo Monitorar e Gerenciar, altere a **Hora de início** e a **Hora de término** para corresponder aos horários de início e de término do pipeline. Escolha **Aplicar**.

    ![Aplicativo Monitorar e Gerenciar](./media/data-factory-build-your-first-pipeline-using-editor/monitor-and-manage-app.png)

1. Selecione uma janela de atividade na lista de **Janelas de Atividade** para ver detalhes sobre ela.

    ![Lista Janelas de Atividades](./media/data-factory-build-your-first-pipeline-using-editor/activity-window-details.png)

## <a name="summary"></a>Resumo
Neste tutorial, você criou um data factory para processar dados executando um script de Hive em um cluster hadoop do HDInsight. Você usou o Data Factory Editor no portal do Azure para executar as seguintes etapas:  

* Criar um data factory.
* Criar dois serviços vinculados:
   * Um serviço de armazenamento vinculado para vincular seu armazenamento de blobs que contém os arquivos de entrada/saída para o data factory.
   * Um serviço vinculado do HDInsight sob demanda para vincular um cluster Hadoop do HDInsight sob demanda ao data factory. O Data Factory cria um cluster Hadoop do HDInsight no momento de processar dados de entrada e gerar dados de saída.
* Foram criados dois conjuntos de dados que descrevem dados de entrada e de saída para a atividade de Hive do HDInsight no pipeline.
* Foi criado um pipeline com uma atividade de Hive do HDInsight.

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você criou um pipeline com uma atividade de transformação (atividade do HDInsight) que executa um script de Hive em um cluster do HDInsight sob demanda. Para ver como usar uma Atividade de cópia para copiar dados do armazenamento de blobs para um banco de dados SQL, confira [Tutorial: Copiar dados do armazenamento de Blobs para o Banco de Dados SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="see-also"></a>Consulte também
| Tópico | DESCRIÇÃO |
|:--- |:--- |
| [Pipelines](data-factory-create-pipelines.md) |Este artigo o ajuda a compreender pipelines e atividades no Data Factory e como usá-los para criar fluxos de trabalho controlado por dados de ponta a ponta para seu cenário ou negócio. |
| [Conjunto de dados](data-factory-create-datasets.md) |Este artigo o ajuda a entender os conjuntos de dados no Data Factory. |
| [Agendamento e execução](data-factory-scheduling-and-execution.md) |Este artigo explica os aspectos de agendamento e execução do modelo de aplicativo do Data Factory. |
| [Monitorar e gerenciar pipelines usando o aplicativo Monitorar e Gerenciar](data-factory-monitor-manage-app.md) |Este artigo descreve como monitorar, gerenciar e depurar seus pipelines usando o aplicativo Monitorar e Gerenciar. |