---
title: Criar seu primeiro data factory (PowerShell) | Microsoft Docs
description: Neste tutorial, você cria um pipeline de exemplo do Azure Data Factory usando o Azure PowerShell.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: ''
editor: ''
ms.assetid: 22ec1236-ea86-4eb7-b903-0e79a58b90c7
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 0f18fc8a18cbcf5d85b68ed40790eda36d653812
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836585"
---
# <a name="tutorial-build-your-first-azure-data-factory-using-azure-powershell"></a>Tutorial: Criar seu primeiro Azure data factory usando o Azure PowerShell
> [!div class="op_single_selector"]
> * [Visão geral e pré-requisitos](data-factory-build-your-first-pipeline.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Modelo do Resource Manager](data-factory-build-your-first-pipeline-using-arm.md)
> * [API REST](data-factory-build-your-first-pipeline-using-rest-api.md)
>
>


> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Caso esteja usando a versão atual do serviço Data Factory, consulte [Início Rápido: Criar um data factory usando o Azure Data Factory](../quickstart-create-data-factory-powershell.md).

Neste artigo, você usa o Azure PowerShell para criar seu primeiro data factory do Azure. Para fazer o tutorial usando outras ferramentas/SDKs, selecione uma das opções da lista suspensa.

O pipeline deste tutorial tem uma atividade: **Atividade do Hive no HDInsight**. Esta atividade executa um script de hive em um cluster do HDInsight do Azure que transforma os dados de entrada para gerar dados de saída. O pipeline é agendado para ser executado uma vez por mês entre os horários de início e término especificados. 

> [!NOTE]
> O pipeline de dados neste tutorial transforma os dados de entrada para gerar dados de saída. Ele não copia dados de um armazenamento de dados de origem para um armazenamento de dados de destino. Para ver um tutorial sobre como copiar dados usando o Azure Data Factory, consulte [Tutorial: Copiar dados do Armazenamento de Blobs para o Banco de Dados SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
> 
> Um pipeline pode ter mais de uma atividade. E você pode encadear duas atividades (executar uma atividade após a outra) definindo o conjunto de dados de saída de uma atividade como o conjunto de dados de entrada da outra atividade. Para saber mais, confira [Agendamento e execução no Data Factory](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline).

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* Leia o artigo [Visão geral do tutorial](data-factory-build-your-first-pipeline.md) e concluir as etapas de **pré-requisito** .
* Siga as instruções do artigo [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview) para instalar a última versão do Azure PowerShell no computador.
* (opcional) Este artigo não cobre todos os cmdlets de Data Factory. Consulte [Referência de cmdlet de Data Factory](/powershell/module/az.datafactory) para obter uma documentação abrangente sobre os cmdlets de Data Factory.

## <a name="create-data-factory"></a>Criar um data factory
Nesta etapa, é possível usar o Azure PowerShell para criar um Azure Data Factory denominado **FirstDataFactoryPSH**. Uma fábrica de dados pode ter um ou mais pipelines. Um pipeline em um data factory pode ter uma ou mais atividades. Por exemplo, uma Atividade de Cópia para copiar dados de um armazenamento de dados de origem para um de destino e uma atividade do Hive do HDInsight para executar um script do Hive para transformar dados de entrada. Vamos começar com a criação do data factory nesta etapa.

1. Inicie o Azure PowerShell e execute o comando a seguir. Mantenha o Azure PowerShell aberto até o fim deste tutorial. Se você fechá-la e reabri-la, precisará executar esses comandos novamente.
   * Execute o comando a seguir e insira o nome de usuário e a senha que você usa para entrar no portal do Azure.
     ```PowerShell
     Connect-AzAccount
     ```    
   * Execute o comando a seguir para exibir todas as assinaturas dessa conta.
     ```PowerShell
     Get-AzSubscription  
     ```
   * Execute o comando a seguir para selecionar a assinatura com a qual deseja trabalhar. Esta assinatura deve ser igual à que você usou no portal do Azure.
     ```PowerShell
     Get-AzSubscription -SubscriptionName <SUBSCRIPTION NAME> | Set-AzContext
     ```     
2. Crie um grupo de recursos do Azure denominado **ADFTutorialResourceGroup** executando o comando a seguir:
    
    ```PowerShell
    New-AzResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"
    ```
    Algumas das etapas neste tutorial supõem que você utilize o grupo de recursos denominado ADFTutorialResourceGroup. Se você utilizar um grupo de recursos diferente, será necessário usá-lo no lugar de ADFTutorialResourceGroup neste tutorial.
3. Execute o cmdlet **New-AzDataFactory** para criar um data factory denominado **FirstDataFactoryPSH**.

    ```PowerShell
    New-AzDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name FirstDataFactoryPSH –Location "West US"
    ```
   Observe os seguintes pontos:

* O nome do Azure Data Factory deve ser globalmente exclusivo. Se você receber o erro: **O nome da data factory "FirstDataFactoryPSH" não está disponível**, altere o nome (por exemplo, seunomeFirstDataFactoryPSH). Use esse nome em vez de ADFTutorialFactoryPSH ao executar as etapas neste tutorial. Consulte o tópico [Data Factory - regras de nomenclatura](data-factory-naming-rules.md) para ver as regras de nomenclatura para artefatos de Data Factory.
* Para criar instâncias do Data Factory, você precisa ser um colaborador/administrador da assinatura do Azure
* O nome do data factory pode ser registrado futuramente como um nome DNS e tornar-se visível publicamente.
* Se você receber o erro "**Esta assinatura não está registrada para usar o namespace Microsoft.DataFactory**", siga um dos seguintes procedimentos e tente a publicação novamente:

  * No Azure PowerShell, execute o comando a seguir para registrar o provedor do Data Factory:

    ```PowerShell
    Register-AzResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```
      Você pode executar o comando a seguir para confirmar se o provedor do Data Factory está registrado:

    ```PowerShell
    Get-AzResourceProvider
    ```
  * Faça logon no [portal do Azure](https://portal.azure.com) usando a assinatura do Azure e navegue até uma folha do Data Factory (ou) crie um data factory no portal do Azure. Essa ação registra automaticamente o provedor para você.

Antes de criar um pipeline, primeiro você precisará criar algumas entidades do Data Factory. Primeiro você cria serviços vinculados para vincular serviços de armazenamento/computação de dados ao seu armazenamento de dados, define conjuntos de dados de entrada/saída para representar os dados em armazenamentos de dados vinculados e, em seguida, cria o pipeline com uma atividade que utilize esses conjuntos de dados.

## <a name="create-linked-services"></a>Criar serviços vinculados
Nesta etapa, você vinculará sua conta do Armazenamento do Azure e um cluster do HDInsight do Azure sob demanda ao data factory. A conta do Armazenamento do Azure manterá os dados de entrada e de saída para o pipeline neste exemplo. O serviço vinculado do HDInsight é usado para executar um script do Hive especificado na atividade do pipeline neste exemplo. Identifique quais serviços de armazenamento/computação de dados serão usados em seu cenário e vincular esses serviços ao data factory criando serviços vinculados.

### <a name="create-azure-storage-linked-service"></a>Criar o serviço vinculado do armazenamento do Azure
Nesta etapa, você vincula a conta do Armazenamento do Azure ao data factory. Você usa a mesma conta do Armazenamento do Azure para armazenar dados de entrada/saída e o arquivo do script do HQL.

1. Crie um arquivo JSON chamado StorageLinkedService.json na pasta C:\ADFGetStarted com o conteúdo a seguir. Crie a pasta ADFGetStarted se ela ainda não existir.

    ```json
    {
        "name": "StorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "description": "",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        }
    }
    ```
    Substitua o **nome da conta** pelo nome da conta do Armazenamento do Azure e a **chave de conta** pela chave de acesso da sua conta do Armazenamento do Azure. Para saber como conseguir sua chave de acesso de armazenamento, consulte as informações sobre como exibir, copiar e regenerar chaves de acesso de armazenamento em [Gerenciar sua conta de armazenamento](../../storage/common/storage-account-manage.md#access-keys).
2. No Azure PowerShell, alterne para a pasta ADFGetStarted.
3. Você pode usar o cmdlet **New-AzDataFactoryLinkedService** que cria um serviço vinculado. Esse cmdlet e outros cmdlets de Data Factory que você usa neste tutorial exigem que os valores sejam passados aos parâmetros *ResourceGroupName* e *DataFactoryName*. Como alternativa, você pode usar **Get-AzDataFactory** para obter um objeto **DataFactory** e passá-lo sem digitar *ResourceGroupName* e *DataFactoryName* sempre que executar um cmdlet. Execute o comando a seguir para atribuir a saída do cmdlet **Get-AzDataFactory** a uma variável **$df**.

    ```PowerShell
    $df=Get-AzDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name FirstDataFactoryPSH
    ```
4. Agora, execute o cmdlet **New-AzDataFactoryLinkedService** que cria o serviço **StorageLinkedService** vinculado.

    ```PowerShell
    New-AzDataFactoryLinkedService $df -File .\StorageLinkedService.json
    ```
    Se você não tivesse executado o cmdlet **Get-AzDataFactory** e atribuído a saída à variável **$df**, precisaria especificar valores para os parâmetros *ResourceGroupName* e *DataFactoryName*, conforme demonstrado a seguir.

    ```PowerShell
    New-AzDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName FirstDataFactoryPSH -File .\StorageLinkedService.json
    ```
    Se você fechar o Azure PowerShell no meio do tutorial, precisará executar o cmdlet **Get-AzDataFactory** na próxima vez que iniciar o Azure PowerShell para concluir o tutorial.

### <a name="create-azure-hdinsight-linked-service"></a>Criar o serviço vinculado do Azure HDInsight
Nesta etapa, você vincula um cluster do HDInsight sob demanda ao seu data factory. O cluster do HDInsight é automaticamente criado no tempo de execução e excluído após a conclusão do processamento, ficando ocioso durante o período especificado. Você pode usar seu próprio cluster do HDInsight em vez de usar um cluster do HDInsight sob demanda. Veja [Serviços vinculados de computação](data-factory-compute-linked-services.md) para obter detalhes.

1. Crie um arquivo JSON chamado **HDInsightOnDemandLinkedService**.json na pasta **C:\ADFGetStarted** com o conteúdo a seguir.

    ```json
    {
        "name": "HDInsightOnDemandLinkedService",
        "properties": {
            "type": "HDInsightOnDemand",
            "typeProperties": {
                "version": "3.5",
                "clusterSize": 1,
                "timeToLive": "00:05:00",
                "osType": "Linux",
                "linkedServiceName": "StorageLinkedService"
            }
        }
    }
    ```
    A tabela a seguir fornece descrições das propriedades de JSON usadas no snippet de código:

   | Propriedade | DESCRIÇÃO |
   |:--- |:--- |
   | clusterSize |Especifica o tamanho do cluster HDInsight. |
   | timeToLive |Especifica que o tempo ocioso do cluster HDInsight antes de ser excluído. |
   | linkedServiceName |Especifica a conta de armazenamento usada para armazenar os logs gerados pelo HDInsight |

    Observe os seguintes pontos:

   * O Data Factory cria um cluster HDInsight **baseado no Linux** para você com o JSON. Confira [Serviço vinculado do HDInsight sob demanda](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) para obter detalhes.
   * Você pode usar **seu próprio cluster do HDInsight** em vez de usar um cluster do HDInsight sob demanda. Confira [Serviço vinculado do HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) para obter detalhes.
   * O cluster HDInsight cria um **contêiner padrão** no armazenamento de blobs especificado no JSON (**nomeServiçoVinculado**). O HDInsight não exclui esse contêiner quando o cluster é excluído. Este comportamento ocorre por design. Com o serviço vinculado HDInsight sob demanda, um cluster HDInsight é criado sempre que uma fatia é processada, a menos que haja um cluster ativo existente (**timeToLive**). O cluster será excluído automaticamente quando o processamento for concluído.

       Quanto mais fatias forem processadas, você verá muitos contêineres no armazenamento de blobs do Azure. Se você não precisa deles para solução de problemas dos trabalhos, convém excluí-los para reduzir o custo de armazenamento. Os nomes desses contêineres seguem um padrão: "adf**nomeseudatafactory**-**nomeserviçovinculado**- carimbodatahora". Use ferramentas como o [Gerenciador de Armazenamento da Microsoft](https://storageexplorer.com/) para excluir contêineres do armazenamento de blobs do Azure.

     Confira [Serviço vinculado do HDInsight sob demanda](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) para obter detalhes.
2. Execute o cmdlet **New-AzDataFactoryLinkedService** que cria o serviço vinculado denominado HDInsightOnDemandLinkedService.
    
    ```PowerShell
    New-AzDataFactoryLinkedService $df -File .\HDInsightOnDemandLinkedService.json
    ```

## <a name="create-datasets"></a>Criar conjuntos de dados
Nesta etapa, você cria conjuntos de dados para representar dados de entrada e de saída para o processamento do Hive. Esses conjuntos de dados fazem referência ao **StorageLinkedService** que você criou anteriormente neste tutorial. O serviço vinculado aponta para uma conta do Armazenamento do Azure e os conjuntos de dados especificam o contêiner, a pasta e o nome do arquivo no armazenamento que contém os dados de entrada e de saída.

### <a name="create-input-dataset"></a>Criar conjunto de dados de entrada
1. Crie um arquivo JSON denominado **InputTable.json** na pasta **C:\ADFGetStarted** com o seguinte conteúdo:

    ```json
    {
        "name": "AzureBlobInput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
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
    O JSON define um conjunto de dados chamado **AzureBlobInput**, que representa dados de entrada para uma atividade no pipeline. Além disso, ele especifica que os dados de entrada estão localizados no contêiner de blob denominado **adfgetstarted** e na pasta chamada **inputdata**.

    A tabela a seguir fornece descrições das propriedades de JSON usadas no snippet de código:

   | Propriedade | DESCRIÇÃO |
   |:--- |:--- |
   | type |A propriedade type é definida como AzureBlob porque os dados residem no armazenamento de blobs do Azure. |
   | linkedServiceName |refere-se ao StorageLinkedService que você criou anteriormente. |
   | fileName |Essa propriedade é opcional. Se você omitir essa propriedade, todos os arquivos de folderPath serão selecionados. Nesse caso, somente o input.log será processado. |
   | type |Os arquivos de log estão em formato de texto, então utilizaremos TextFormat. |
   | columnDelimiter |as colunas nos arquivos de log são delimitadas pelo caractere de vírgula (,). |
   | frequency/interval |a frequência é definida como Mês e o intervalo como 1, o que significa que as fatias de entrada estão disponíveis mensalmente. |
   | external |essa propriedade será definida como true se os dados de entrada não forem gerados pelo serviço Data Factory. |
2. Execute o comando a seguir no Azure PowerShell para criar o conjunto de dados do Data Factory:

    ```PowerShell
    New-AzDataFactoryDataset $df -File .\InputTable.json
    ```

### <a name="create-output-dataset"></a>Criar conjunto de dados de saída
Agora, você cria o conjunto de dados de saída para representar os dados de saída armazenados no armazenamento de Blobs do Azure.

1. Crie um arquivo JSON denominado **OutputTable.json** na pasta **C:\ADFGetStarted** com o seguinte conteúdo:

    ```json
    {
      "name": "AzureBlobOutput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
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
    O JSON define um conjunto de dados chamado **AzureBlobOutput**, que representa dados de saída para uma atividade no pipeline. Além disso, ele especifica que os resultados são armazenados no contêiner de blob denominado **adfgetstarted** e na pasta chamada **partitioneddata**. A seção **availability** especifica que o conjunto de dados de saída é produzido mensalmente.
2. Execute o comando a seguir no Azure PowerShell para criar o conjunto de dados do Data Factory:

    ```PowerShell
    New-AzDataFactoryDataset $df -File .\OutputTable.json
    ```

## <a name="create-pipeline"></a>Criar um pipeline
Nesta etapa, você cria seu primeiro pipeline com a atividade **HDInsightHive** . A fatia de entrada está disponível mensalmente (frequência: Mês, intervalo: 1), a fatia de saída é produzida mensalmente e a propriedade do agendador para a atividade também está definida como mensalmente. As configurações para o conjunto de dados de saída e o agendador de atividades devem corresponder. Atualmente, o conjunto de dados de saída é o que aciona a agenda, então você deve criar um conjunto de dados de saída, mesmo que a atividade não produza qualquer saída. Se a atividade não receber entradas, ignore a criação de conjunto de dados de entrada. As propriedades usadas no JSON a seguir são explicadas no final desta seção.

1. Crie um arquivo JSON denominado MyFirstPipelinePSH.json na pasta C:\ADFGetStarted com o conteúdo a seguir:

   > [!IMPORTANT]
   > Substitua **storageaccountname** pelo nome da sua conta de armazenamento no JSON.
   >
   >

    ```json
    {
        "name": "MyFirstPipeline",
        "properties": {
            "description": "My first Azure Data Factory pipeline",
            "activities": [
                {
                    "type": "HDInsightHive",
                    "typeProperties": {
                        "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                        "scriptLinkedService": "StorageLinkedService",
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
    No snippet de JSON, você cria um pipeline que consiste de uma única atividade que usa o Hive para processar dados em um cluster HDInsight.

    O arquivo de script do Hive, **partitionweblogs.hql**, é armazenado na conta de armazenamento do Azure (especificada pelo scriptLinkedService chamado **StorageLinkedService**) e na pasta **script** no contêiner **adfgetstarted**.

    A seção **defines** é usada para especificar as configurações de tempo de execução passadas para o script do hive como valores de configuração do Hive (por exemplo, ${hiveconf:inputtable}, ${hiveconf:partitionedtable}).

    As propriedades **start** e **end** do pipeline especificam o período ativo do pipeline.

    Na atividade do JSON, você especifica que o script do Hive é executado na máquina especificada pelo **nomeServiçoVinculado** – **HDInsightOnDemandLinkedService**.

   > [!NOTE]
   > Veja "Pipeline JSON" em [Pipelines e atividades no Azure Data Factory](data-factory-create-pipelines.md) para obter detalhes sobre as propriedades JSON usadas no exemplo anterior.

2. Confirme que vê o arquivo **input.log** na pasta **adfgetstarted/inputdata** no armazenamento de blobs do Azure e execute o comando a seguir para implantar o pipeline. Como as horas de **início** e de **término** são definidas no passado e **isPaused** está definido como false, o pipeline (a atividade no pipeline) é imediatamente executado após a implantação.

    ```PowerShell
    New-AzDataFactoryPipeline $df -File .\MyFirstPipelinePSH.json
    ```
3. Parabéns, você criou com sucesso seu primeiro pipeline usando o Azure PowerShell!

## <a name="monitor-pipeline"></a>Monitorar o pipeline
Nesta etapa, você usa o Azure PowerShell para monitorar o que está acontecendo em um Azure Data Factory.

1. Execute **Get-AzDataFactory** e atribua a saída a uma variável **$df**.

    ```PowerShell
    $df=Get-AzDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name FirstDataFactoryPSH
    ```
2. Execute **Get-AzDataFactorySlice** para obter detalhes sobre todas as fatias do **EmpSQLTable**, que é a tabela de saída do pipeline.

    ```PowerShell
    Get-AzDataFactorySlice $df -DatasetName AzureBlobOutput -StartDateTime 2017-07-01
    ```
    Observe que o valor de StartDateTime especificado aqui é a mesma hora de início especificada no pipeline de JSON. Veja o exemplo de saída:

    ```PowerShell
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : FirstDataFactoryPSH
    DatasetName       : AzureBlobOutput
    Start             : 7/1/2017 12:00:00 AM
    End               : 7/2/2017 12:00:00 AM
    RetryCount        : 0
    State             : InProgress
    SubState          :
    LatencyStatus     :
    LongRetryCount    : 0
    ```
3. Execute **Get-AzDataFactoryRun** para obter os detalhes de execuções de atividade para uma fatia específica.

    ```PowerShell
    Get-AzDataFactoryRun $df -DatasetName AzureBlobOutput -StartDateTime 2017-07-01
    ```

    Veja o exemplo de saída: 

    ```PowerShell
    Id                  : 0f6334f2-d56c-4d48-b427-d4f0fb4ef883_635268096000000000_635292288000000000_AzureBlobOutput
    ResourceGroupName   : ADFTutorialResourceGroup
    DataFactoryName     : FirstDataFactoryPSH
    DatasetName         : AzureBlobOutput
    ProcessingStartTime : 12/18/2015 4:50:33 AM
    ProcessingEndTime   : 12/31/9999 11:59:59 PM
    PercentComplete     : 0
    DataSliceStart      : 7/1/2017 12:00:00 AM
    DataSliceEnd        : 7/2/2017 12:00:00 AM
    Status              : AllocatingResources
    Timestamp           : 12/18/2015 4:50:33 AM
    RetryAttempt        : 0
    Properties          : {}
    ErrorMessage        :
    ActivityName        : RunSampleHiveActivity
    PipelineName        : MyFirstPipeline
    Type                : Script
    ```
    Pode continuar executando este cmdlet até ver a fatia no estado **Pronto** ou **Falha**. Quando a fatia estiver no estado Pronto, verifique a pasta **partitioneddata** no contêiner **adfgetstarted** em seu armazenamento de blobs para os dados de saída.  A criação de um cluster do HDInsight sob demanda geralmente leva algum tempo.

    ![dados de saída](./media/data-factory-build-your-first-pipeline-using-powershell/three-ouptut-files.png)

> [!IMPORTANT]
> A criação de um cluster do HDInsight sob demanda geralmente leva algum tempo (20 minutos, aproximadamente). Portanto, espere que o pipeline demore **cerca de 30 minutos** para processar a fatia.
>
> O arquivo de entrada é excluído quando a fatia é processada com êxito. Portanto, se você quiser executar novamente a fatia ou fazer o tutorial novamente, carregue o arquivo de entrada (input.log) na pasta inputdata do contêiner adfgetstarted.
>
>

## <a name="summary"></a>Resumo
Neste tutorial, você criou uma data factory do Azure para processar dados ao executar o script Hive em um cluster hadoop do HDInsight. Você usou o Data Factory Editor no portal do Azure para executar as seguintes etapas:

1. Foi criada uma **data factory**do Azure.
2. Foram criados dois **serviços vinculados**:
   1. **Armazenamento do Azure** para vincular seu armazenamento de blobs do Azure que contém os arquivos de entrada/saída para a data factory.
   2. **Azure HDInsight** sob demanda para vincular um cluster Hadoop do HDInsight sob demanda à data factory. O Azure Data Factory cria um cluster Hadoop do HDInsight just-in-time para processar dados de entrada e gerar dados de saída.
3. Foram criados dois **conjuntos de dados**que descrevem dados de entrada e de saída para a atividade Hive do HDInsight no pipeline.
4. Foi criado um **pipeline** com uma atividade **Hive do HDInsight**.

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você criou um pipeline com uma atividade de transformação (atividade do HDInsight) que executa um script Hive em um cluster do HDInsight do Azure sob demanda. Para ver como usar uma Atividade de Cópia para copiar dados de um Blob do Azure para o SQL do Azure, confira [Tutorial: Copiar dados de um Blob do Azure para o SQL do Azure](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="see-also"></a>Veja também

| Tópico | DESCRIÇÃO |
|:--- |:--- |
| [Referência de cmdlet do Data Factory](/powershell/module/az.datafactory) |Consulte a documentação abrangente sobre os cmdlets do Data Factory |
| [Pipelines](data-factory-create-pipelines.md) |Este artigo o ajuda a compreender pipelines e atividades no Azure Data Factory e como usá-los para construir fluxos de trabalho orientados a dados de ponta a ponta para seu cenário ou negócio. |
| [Conjunto de dados](data-factory-create-datasets.md) |Este artigo o ajuda a entender os conjuntos de dados no Azure Data Factory. |
| [Planejamento e execução](data-factory-scheduling-and-execution.md) |Este artigo explica os aspectos de agendamento e execução do modelo de aplicativo do Azure Data Factory. |
| [Monitorar e gerenciar pipelines usando o Aplicativo de Monitoramento](data-factory-monitor-manage-app.md) |Este artigo descreve como monitorar, gerenciar e depurar seus pipelines usando o Aplicativo de Monitoramento e Gerenciamento. |
