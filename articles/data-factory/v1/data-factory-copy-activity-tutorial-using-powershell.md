---
title: 'Tutorial: Criar um pipeline para mover dados usando o Azure PowerShell | Microsoft Docs'
description: Neste tutorial, você cria um pipeline do Azure Data Factory com uma Atividade de Cópia usando o Azure PowerShell.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: 71087349-9365-4e95-9847-170658216ed8
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: ce9566618870419e22dc9c420e77ef2ed6cd3c81
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836547"
---
# <a name="tutorial-create-a-data-factory-pipeline-that-moves-data-by-using-azure-powershell"></a>Tutorial: Criar um pipeline do Data Factory que move dados usando o Azure PowerShell
> [!div class="op_single_selector"]
> * [Visão geral e pré-requisitos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Modelo do Azure Resource Manager](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [API REST](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [API do .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço Data Factory, consulte o [tutorial de atividade de cópia](../quickstart-create-data-factory-powershell.md). 

Neste artigo, você aprenderá a usar o PowerShell para criar um data factory com um pipeline que copia dados de um armazenamento de blobs do Azure para um Banco de Dados SQL do Azure. Se você ainda está se familiarizando com o Azure Data Factory, leia o artigo [Introdução ao Azure Data Factory](data-factory-introduction.md) antes de fazer este tutorial.   

Neste tutorial, você criará um pipeline com uma atividade: Atividade de Cópia. A atividade de cópia copia dados de um armazenamento de dados com suporte para um armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados com suporte como origens e coletores, confira [Armazenamentos de dados com suporte](data-factory-data-movement-activities.md#supported-data-stores-and-formats). A atividade é habilitada por um serviço globalmente disponível que pode copiar dados entre vários repositórios de dados de forma segura, confiável e escalonável. Para saber mais sobre a atividade de cópia, confira [Atividades de movimentação de dados](data-factory-data-movement-activities.md).

Um pipeline pode ter mais de uma atividade. E você pode encadear duas atividades (executar uma atividade após a outra) definindo o conjunto de dados de saída de uma atividade como o conjunto de dados de entrada da outra atividade. Para saber mais, confira [Várias atividades em um pipeline](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline).

> [!NOTE]
> Este artigo não cobre todos os cmdlets de Data Factory. Consulte a [Referência de Cmdlet do Data Factory](/powershell/module/az.datafactory) para ter uma documentação completa sobre esses cmdlets.
> 
> O pipeline de dados neste tutorial copia os dados de um armazenamento de dados de origem para um armazenamento de dados de destino. Para obter um tutorial sobre como transformar dados usando o Azure Data Factory, confira [Tutorial: Criar um pipeline para transformar dados usando um cluster Hadoop](data-factory-build-your-first-pipeline.md).

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

- Conclua os pré-requisitos listados no artigo [Pré-requisitos do tutorial](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
- Instale o **Azure PowerShell**. Siga as instruções em [Como instalar e configurar o Azure PowerShell](/powershell/azure/install-Az-ps).

## <a name="steps"></a>Etapas
Eis as etapas executadas como parte deste tutorial:

1. Crie um **data factory** do Azure. Nesta etapa, você cria um data factory denominado ADFTutorialDataFactoryPSH. 
1. Crie **serviços vinculados** no data factory. Nesta etapa, você criará dois serviços vinculados dos tipos: Armazenamento do Azure e Banco de Dados SQL do Azure. 
    
    O AzureStorageLinkedService vincula sua conta do armazenamento do Azure ao data factory. Você criou um contêiner e carregou dados nessa conta de armazenamento como parte dos [pré-requisitos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).   

    O AzureSqlLinkedService vincula seu banco de dados SQL do Azure ao data factory. Os dados copiados do armazenamento de blobs são armazenados no banco de dados. Você criou uma tabela SQL no banco de dados como parte dos [pré-requisitos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).   
1. Crie **conjuntos de dados** de entrada e saída no data factory.  
    
    O serviço vinculado do Armazenamento do Azure especifica a cadeia de conexão que o serviço Data Factory usa no tempo de execução para se conectar à sua conta do Armazenamento do Azure. E o conjunto de dados de blob especifica o contêiner e a pasta que contém os dados.  

    Da mesma forma, o serviço vinculado do Banco de Dados SQL especifica a cadeia de conexão que o serviço Data Factory usa no tempo de execução para se conectar ao seu Banco de Dados SQL do Azure. Além disso, o conjunto de dados da tabela SQL de saída especifica a tabela no banco de dados na qual os dados do armazenamento de blobs são copiados.
1. Crie um **pipeline** na fábrica de dados. Nesta etapa, você cria um pipeline com a atividade de cópia.   
    
    A atividade de cópia copia dados de um blob no armazenamento de blobs do Azure em uma tabela no Banco de Dados SQL do Azure. Você pode usar uma atividade de cópia em um pipeline para copiar dados de qualquer fonte com suporte para qualquer destino com suporte. Para obter uma lista de armazenamentos de dados com suporte, confira o artigo [Atividades de movimentação de dados](data-factory-data-movement-activities.md#supported-data-stores-and-formats). 
1. Monitore o pipeline. Nesta etapa, você **monitora** fatias de conjuntos de dados de entrada e de saída usando o PowerShell.

## <a name="create-a-data-factory"></a>Criar uma data factory
> [!IMPORTANT]
> Complete os [pré-requisitos do tutorial](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) se você ainda não fez isso.   

Uma fábrica de dados pode ter um ou mais pipelines. Um pipeline em um data factory pode ter uma ou mais atividades. Por exemplo, uma Atividade de Cópia para copiar dados de um armazenamento de dados de origem para um de destino e uma atividade do Hive do HDInsight para executar um script do Hive para transformar os dados de entrada em dados de saída do produto. Vamos começar com a criação do data factory nesta etapa.

1. Inicie o **PowerShell**. Mantenha o Azure PowerShell aberto até o fim deste tutorial. Se você fechar e reabrir, precisará executar os comandos novamente.

    Execute o comando a seguir e insira o nome de usuário e senha usados para entrar no portal do Azure:

    ```powershell
    Connect-AzAccount
    ```   
   
    Execute o comando abaixo para exibir todas as assinaturas dessa conta:

    ```powershell
    Get-AzSubscription
    ```

    Execute o comando a seguir para selecionar a assinatura com a qual deseja trabalhar. Substitua **&lt;NameOfAzureSubscription**&gt; pelo nome da sua assinatura do Azure:

    ```powershell
    Get-AzSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzContext
    ```
1. Crie um grupo de recursos do Azure denominado **ADFTutorialResourceGroup** executando o comando a seguir:

    ```powershell
    New-AzResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"
    ```
    
    Algumas das etapas neste tutorial supõem que você utiliza o grupo de recursos denominado **ADFTutorialResourceGroup**. Se você utilizar um grupo de recursos diferente, será necessário usá-lo no lugar de ADFTutorialResourceGroup neste tutorial.
1. Execute o cmdlet **New-AzDataFactory** para criar um data factory denominado **ADFTutorialDataFactoryPSH**:  

    ```powershell
    $df=New-AzDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH –Location "West US"
    ```
    Esse nome pode já ter sido usado. Portanto, torne o nome do data factory exclusivo adicionando um prefixo ou um sufixo (por exemplo: ADFTutorialDataFactoryPSH05152017) e execute o comando novamente.  

Observe os seguintes pontos:

* O nome da data factory do Azure deve ser globalmente exclusivo. Se você receber o seguinte erro, altere o nome (por exemplo, yournameADFTutorialDataFactoryPSH). Use esse nome em vez de ADFTutorialFactoryPSH ao executar as etapas neste tutorial. Consulte o tópico [Data Factory - regras de nomenclatura](data-factory-naming-rules.md) para ver os artefatos do Data Factory.

    ```
    Data factory name “ADFTutorialDataFactoryPSH” is not available
    ```
* Para criar instâncias do Data Factory, você precisa ser um colaborador ou administrador da assinatura do Azure.
* O nome do data factory pode ser registrado como um nome DNS no futuro e ficar visível publicamente.
* Você poderá receber o seguinte erro: "**Esta assinatura não está registrada para usar o namespace Microsoft.DataFactory.** " Faça o seguinte e tente publicar novamente:

  * No Azure PowerShell, execute o comando a seguir para registrar o provedor do Data Factory:

    ```powershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```

    Execute o comando abaixo para confirmar se o provedor do Data Factory está registrado:

    ```powershell
    Get-AzResourceProvider
    ```
  * Entre usando sua assinatura do Azure no [portal do Azure](https://portal.azure.com). Vá para a folha Data Factory ou crie um data factory no portal do Azure. Essa ação registra automaticamente o provedor para você.

## <a name="create-linked-services"></a>Criar serviços vinculados
Os serviços vinculados são criados em um data factory para vincular seus armazenamentos de dados e serviços de computação ao data factory. Neste tutorial, você não usa serviços de computação, como o Azure HDInsight ou o Azure Data Lake Analytics. Você usa dois armazenamentos de dados do tipo Armazenamento do Azure (origem) e o banco de dados SQL do Azure (destino). 

Portanto, você criará dois serviços vinculados chamados AzureStorageLinkedService e AzureSqlLinkedService dos tipos: AzureStorage e AzureSqlDatabase.  

O AzureStorageLinkedService vincula sua conta do armazenamento do Azure ao data factory. Essa conta de armazenamento é aquela na qual você criou um contêiner e carregou os dados como parte dos [pré-requisitos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).   

O AzureSqlLinkedService vincula seu banco de dados SQL do Azure ao data factory. Os dados copiados do armazenamento de blobs são armazenados no banco de dados. Você criou a tabela emp no banco de dados como parte dos [pré-requisitos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). 

### <a name="create-a-linked-service-for-an-azure-storage-account"></a>Criar um serviço vinculado para uma conta de armazenamento do Azure
Nesta etapa, você vincula a conta do Armazenamento do Azure ao data factory.

1. Crie um arquivo JSON chamado **AzureStorageLinkedService.json** na pasta **C:\ADFGetStartedPSH** com o seguinte conteúdo: (Crie a pasta ADFGetStartedPSH se ela ainda não existir.)

    > [!IMPORTANT]
    > Substitua &lt;nome da conta&gt; e &lt;chave da conta&gt; pelo nome e pela chave da sua conta de armazenamento do Azure antes de salvar o arquivo. 

    ```json
    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        }
     }
    ``` 
1. No **Azure PowerShell**, alterne para a pasta **ADFGetStartedPSH**.
1. Execute o cmdlet **New-AzDataFactoryLinkedService** para criar o serviço vinculado: **AzureStorageLinkedService**. Esse cmdlet e outros cmdlets de Data Factory que você usa neste tutorial exigem que os valores sejam passados aos parâmetros **ResourceGroupName** e **DataFactoryName**. Como alternativa, você pode passar o objeto DataFactory retornado pelo cmdlet New-AzDataFactory sem precisar digitar ResourceGroupName e DataFactoryName sempre que executar um cmdlet. 

    ```powershell
    New-AzDataFactoryLinkedService $df -File .\AzureStorageLinkedService.json
    ```
    Veja o exemplo de saída:

    ```
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    Properties        : Microsoft.Azure.Management.DataFactories.Models.LinkedServiceProperties
    ProvisioningState : Succeeded
    ``` 

    Outra maneira de criar esse serviço vinculado é especificar o nome do grupo de recursos e o nome do data factory em vez de especificar o objeto DataFactory.  

    ```powershell
    New-AzDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName <Name of your data factory> -File .\AzureStorageLinkedService.json
    ```

### <a name="create-a-linked-service-for-an-azure-sql-database"></a>Criar um serviço vinculado para um banco de dados SQL do Azure
Nesta etapa, você vincula o banco de dados SQL do Azure ao data factory.

1. Crie um arquivo JSON denominado AzureSqlLinkedService.json na pasta C:\ADFGetStartedPSH, com o seguinte conteúdo:

    > [!IMPORTANT]
    > Substitua &lt;servername&gt;, &lt;databasename&gt;, &lt;username@servername&gt; e &lt;password&gt; pelos nomes de seu servidor SQL do Azure, banco de dados, conta de usuário e senha.
    
    ```json
    {
        "name": "AzureSqlLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": "Server=tcp:<server>.database.windows.net,1433;Database=<databasename>;User ID=<user>@<server>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        }
     }
    ```
1. Execute o comando abaixo para criar um serviço vinculado:

    ```powershell
    New-AzDataFactoryLinkedService $df -File .\AzureSqlLinkedService.json
    ```
    
    Veja o exemplo de saída:

    ```
    LinkedServiceName : AzureSqlLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    Properties        : Microsoft.Azure.Management.DataFactories.Models.LinkedServiceProperties
    ProvisioningState : Succeeded
    ```

   Confirme se a configuração **Permitir acesso aos serviços do Azure** está ativada para o servidor do banco de dados SQL. Para verificar e ativar essa configuração, execute as seguintes etapas:

    1. Faça logon no [Portal do Azure](https://portal.azure.com)
    1. Clique em **Mais serviços >** à esquerda e clique em **Servidores SQL** na categoria **BANCOS DE DADOS**.
    1. Selecione o servidor na lista de servidores SQL.
    1. Na folha do servidor SQL, clique no link **Mostrar configurações de firewall**.
    1. Na folha **Configurações de Firewall**, clique em **ATIVADO** para **Permitir acesso aos serviços do Azure**.
    1. Clique em **Salvar** na barra de ferramentas. 

## <a name="create-datasets"></a>Criar conjuntos de dados
Na etapa anterior, você criou serviços vinculados para vincular a conta do Armazenamento do Azure e um banco de dados SQL do Azure ao data factory. Nesta etapa, você define dois conjuntos de dados, chamados InputDataset e OutputDataset, que representam os dados de entrada e saída armazenados nos repositórios de dados referidos por AzureStorageLinkedService e AzureSqlLinkedService, respectivamente.

O serviço vinculado do Armazenamento do Azure especifica a cadeia de conexão que o serviço Data Factory usa no tempo de execução para se conectar à sua conta do Armazenamento do Azure. E o conjunto de dados de blob de entrada (InputDataset) especifica o contêiner e a pasta que contém os dados.  

Da mesma forma, o serviço vinculado do Banco de Dados SQL especifica a cadeia de conexão que o serviço Data Factory usa no tempo de execução para se conectar ao seu Banco de Dados SQL do Azure. Além disso, o conjunto de dados da tabela SQL de saída (OutputDataset) especifica a tabela no banco de dados na qual os dados do armazenamento de blobs são copiados. 

### <a name="create-an-input-dataset"></a>Criar um conjunto de dados de entrada
Nesta etapa, você criará um conjunto de dados denominado InputDataset que aponta para um arquivo de blob (emp.txt) na pasta raiz de um contêiner de blob (adftutorial) no Armazenamento do Azure representado pelo serviço vinculado AzureStorageLinkedService. Se você não especificar um valor para fileName (ou ignorá-lo), os dados de todos os blobs na pasta de entrada serão copiados no destino. Neste tutorial, você deve especificar um valor para o fileName.  

1. Crie um arquivo JSON denominado **InputDataset.json** na pasta **C:\ADFGetStartedPSH** com o seguinte conteúdo:

    ```json
    {
        "name": "InputDataset",
        "properties": {
            "structure": [
                {
                    "name": "FirstName",
                    "type": "String"
                },
                {
                    "name": "LastName",
                    "type": "String"
                }
            ],
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "fileName": "emp.txt",
                "folderPath": "adftutorial/",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "external": true,
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
     }
    ```

    A tabela a seguir fornece descrições das propriedades de JSON usadas no snippet de código:

    | Propriedade | DESCRIÇÃO |
    |:--- |:--- |
    | type | A propriedade type é definida como **AzureBlob** porque os dados residem no armazenamento de blobs do Azure. |
    | linkedServiceName | Refere-se ao **AzureStorageLinkedService** que você criou anteriormente. |
    | folderPath | Especifica o **contêiner** e a **pasta** de blob que contém blobs de entrada. Neste tutorial, adftutorial é o contêiner de blob e pasta é a pasta raiz. | 
    | fileName | Essa propriedade é opcional. Se você omitir essa propriedade, todos os arquivos de folderPath serão selecionados. Neste tutorial, **emp.txt** é especificado como fileName e, portanto, apenas esse arquivo é selecionado para processamento. |
    | format -> type |O arquivo de entrada está no formato de texto e, portanto, usamos **TextFormat**. |
    | columnDelimiter | As colunas no arquivo de entrada são delimitadas por **caractere de vírgula (`,`)** . |
    | frequency/interval | A frequência é definida como **Hora** e o intervalo é definido como **1**, o que significa que as fatias de entrada ficam disponíveis **a cada hora**. Melhor dizendo, o serviço Data Factory procurará dados de entrada a cada hora na pasta raiz do contêiner de blob (**adftutorial**) especificado. Ele procura os dados nas horas de início e término do pipeline, não antes ou depois delas.  |
    | external | Essa propriedade é definida como **true** se os dados não são gerados pelo pipeline. Os dados de entrada neste tutorial estão no arquivo emp.txt, que não é gerado pelo pipeline e, portanto, definimos essa propriedade como true. |

    Para saber mais sobre essas propriedades JSON, confira o [artigo sobre o conector do Blob do Azure](data-factory-azure-blob-connector.md#dataset-properties).
1. Execute o comando a seguir para criar o conjunto de dados de Data Factory.

    ```powershell  
    New-AzDataFactoryDataset $df -File .\InputDataset.json
    ```
    Veja o exemplo de saída:

    ```
    DatasetName       : InputDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    Availability      : Microsoft.Azure.Management.DataFactories.Common.Models.Availability
    Location          : Microsoft.Azure.Management.DataFactories.Models.AzureBlobDataset
    Policy            : Microsoft.Azure.Management.DataFactories.Common.Models.Policy
    Structure         : {FirstName, LastName}
    Properties        : Microsoft.Azure.Management.DataFactories.Models.DatasetProperties
    ProvisioningState : Succeeded
    ```

### <a name="create-an-output-dataset"></a>Criar um conjunto de dados de saída
Nesta parte da etapa, você cria um conjunto de dados de saída denominado **OutputDataset**. Esse conjunto de dados aponta para uma tabela SQL no banco de dados SQL do Azure representada por **AzureSqlLinkedService**. 

1. Crie um arquivo JSON denominado **OutputDataset.json** na pasta **C:\ADFGetStartedPSH** com o seguinte conteúdo:

    ```json
    {
        "name": "OutputDataset",
        "properties": {
            "structure": [
                {
                    "name": "FirstName",
                    "type": "String"
                },
                {
                    "name": "LastName",
                    "type": "String"
                }
            ],
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": {
                "tableName": "emp"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
    ```

    A tabela a seguir fornece descrições das propriedades de JSON usadas no snippet de código:

    | Propriedade | DESCRIÇÃO |
    |:--- |:--- |
    | type | A propriedade type é definida como **AzureSqlTable** porque os dados são copiados para uma tabela em um banco de dados SQL do Azure. |
    | linkedServiceName | Refere-se ao **AzureSqlLinkedService** que você criou anteriormente. |
    | tableName | Especifica a **tabela** para a qual os dados são copiados. | 
    | frequency/interval | A frequência é definida como **Hora** e o intervalo é de **1**, o que significa que as fatias de saída são produzidas **a cada hora** entre as horas de início e término do pipeline, não antes ou depois delas.  |

    Há três colunas (**ID**, **FirstName** e **LastName**) na tabela emp no banco de dados. ID é uma coluna de identidade. Portanto, você precisa especificar somente **FirstName** e **LastName** aqui.

    Para saber mais sobre essas propriedades JSON, confira o [artigo sobre o conector do SQL](data-factory-azure-sql-connector.md#dataset-properties).
1. Execute o comando a seguir para criar o conjunto de dados do data factory.

    ```powershell   
    New-AzDataFactoryDataset $df -File .\OutputDataset.json
    ```

    Veja o exemplo de saída:

    ```
    DatasetName       : OutputDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    Availability      : Microsoft.Azure.Management.DataFactories.Common.Models.Availability
    Location          : Microsoft.Azure.Management.DataFactories.Models.AzureSqlTableDataset
    Policy            :
    Structure         : {FirstName, LastName}
    Properties        : Microsoft.Azure.Management.DataFactories.Models.DatasetProperties
    ProvisioningState : Succeeded
    ```

## <a name="create-a-pipeline"></a>Criar uma pipeline
Nesta etapa, você cria um pipeline com uma **atividade de cópia** que usa **InputDataset** como entrada e **OutputDataset** como saída.

Atualmente, o conjunto de dados de saída é o que conduz o agendamento. Neste tutorial, o conjunto de dados de saída está configurado para produzir uma fatia uma vez a cada hora. O pipeline tem uma hora de início e uma hora de término com um dia de diferença, o que significa 24 horas. Portanto, 24 fatias de conjunto de dados de saída são produzidas pelo pipeline. 


1. Crie um arquivo JSON denominado **ADFTutorialPipeline.json** na pasta **C:\ADFGetStartedPSH**, com o conteúdo a seguir:

    ```json   
    {
      "name": "ADFTutorialPipeline",
      "properties": {
        "description": "Copy data from a blob to Azure SQL table",
        "activities": [
          {
            "name": "CopyFromBlobToSQL",
            "type": "Copy",
            "inputs": [
              {
                "name": "InputDataset"
              }
            ],
            "outputs": [
              {
                "name": "OutputDataset"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "SqlSink",
                "writeBatchSize": 10000,
                "writeBatchTimeout": "60:00:00"
              }
            },
            "Policy": {
              "concurrency": 1,
              "executionPriorityOrder": "NewestFirst",
              "retry": 0,
              "timeout": "01:00:00"
            }
          }
        ],
        "start": "2017-05-11T00:00:00Z",
        "end": "2017-05-12T00:00:00Z"
      }
    } 
    ```
    Observe os seguintes pontos:
   
   - Na seção de atividades, há apenas uma atividade cujo **tipo** é definido como **Copy**. Para saber mais sobre a atividade de cópia, confira [Atividades de movimentação de dados](data-factory-data-movement-activities.md). Nas soluções de Data Factory, você também pode usar [atividades de transformação de dados](data-factory-data-transformation-activities.md).
   - A entrada da atividade é definida como **InputDataset** e a saída da atividade é definida como **OutputDataset**. 
   - Na seção **typeProperties**, **BlobSource** é especificado como o tipo de origem e **SqlSink** é especificado como o tipo de coletor. Para ver a lista completa dos armazenamentos de dados com suporte pela atividade de cópia, como origens e coletores, confira [Armazenamentos de dados com suporte](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Para aprender a usar um armazenamento de dados com suporte específico como fonte/coletor, clique no link na tabela.  
     
     Substitua o valor da propriedade **start** pelo dia atual e o valor de **end** pelo dia seguinte. Você pode especificar apenas a parte da data e ignorar a parte de hora do valor de data/hora. Por exemplo, "2016-02-03", que é equivalente a "2016-02-03T00:00:00Z"
     
     Ambos os valores de data/hora de início e de término devem estar no [formato ISO](https://en.wikipedia.org/wiki/ISO_8601). Por exemplo:  2016-10-14T16:32:41Z. A hora **final** é opcional, mas nós a usaremos neste tutorial. 
     
     Se você não especificar o valor para a propriedade **end**, ele será calculado como "**início + 48 horas**". Para executar o pipeline indefinidamente, especifique **9999-09-09** como o valor para a propriedade **end**.
     
     No exemplo anterior, há 24 fatias de dados, pois cada fatia de dados é produzida a cada hora.

     Para obter descrições das propriedades JSON em uma definição de pipeline, consulte o artigo [Criar pipelines](data-factory-create-pipelines.md). Para obter descrições das propriedades JSON em uma definição de atividade de cópia, consulte [Atividades de movimentação de dados](data-factory-data-movement-activities.md). Para obter descrições das propriedades JSON com suporte pelo BlobSource, consulte o [artigo sobre o conector de blobs do Azure](data-factory-azure-blob-connector.md). Para obter descrições das propriedades JSON com suporte pelo SqlSink, consulte o [artigo sobre o conector do Banco de Dados SQL do Azure](data-factory-azure-sql-connector.md).
1. Execute o comando a seguir para criar a tabela do data factory.

    ```powershell   
    New-AzDataFactoryPipeline $df -File .\ADFTutorialPipeline.json
    ```

    Veja o exemplo de saída: 

    ```
    PipelineName      : ADFTutorialPipeline
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    Properties        : Microsoft.Azure.Management.DataFactories.Models.PipelinePropertie
    ProvisioningState : Succeeded
    ```

**Parabéns!** Você criou com êxito um data factory do Azure, com um pipeline que copia dados do Armazenamento de Blobs do Azure para o Banco de Dados SQL do Azure. 

## <a name="monitor-the-pipeline"></a>Monitorar o Pipeline
Nesta etapa, você usa o Azure PowerShell para monitorar o que está acontecendo em um Azure Data Factory.

1. Substitua &lt;DataFactoryName&gt; pelo nome do data factory, execute **Get-AzDataFactory** e atribua o resultado a uma variável $df.

    ```powershell  
    $df=Get-AzDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name <DataFactoryName>
    ```

    Por exemplo:
    ```powershell
    $df=Get-AzDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH0516
    ```
    
    Em seguida, imprima o conteúdo de $df para ver a seguinte saída: 
    
    ```
    PS C:\ADFGetStartedPSH> $df
    
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    DataFactoryId     : 6f194b34-03b3-49ab-8f03-9f8a7b9d3e30
    ResourceGroupName : ADFTutorialResourceGroup
    Location          : West US
    Tags              : {}
    Properties        : Microsoft.Azure.Management.DataFactories.Models.DataFactoryProperties
    ProvisioningState : Succeeded
    ```
1. Execute **Get-AzDataFactorySlice** para obter detalhes sobre todas as fatias do **OutputDataset**, que é o conjunto de dados de saída do pipeline.  

    ```powershell   
    Get-AzDataFactorySlice $df -DatasetName OutputDataset -StartDateTime 2017-05-11T00:00:00Z
    ```

   Essa configuração deve corresponder ao valor de **Start** no pipeline de JSON. Você deve ver 24 fatias, uma para cada hora desde 12h do dia atual até 12h do dia seguinte.

   Aqui estão três fatias de exemplo da saída: 

    ``` 
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    DatasetName       : OutputDataset
    Start             : 5/11/2017 11:00:00 PM
    End               : 5/12/2017 12:00:00 AM
    RetryCount        : 0
    State             : Ready
    SubState          :
    LatencyStatus     :
    LongRetryCount    : 0

    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    DatasetName       : OutputDataset
    Start             : 5/11/2017 9:00:00 PM
    End               : 5/11/2017 10:00:00 PM
    RetryCount        : 0
    State             : InProgress
    SubState          :
    LatencyStatus     :
    LongRetryCount    : 0   

    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    DatasetName       : OutputDataset
    Start             : 5/11/2017 8:00:00 PM
    End               : 5/11/2017 9:00:00 PM
    RetryCount        : 0
    State             : Waiting
    SubState          : ConcurrencyLimit
    LatencyStatus     :
    LongRetryCount    : 0
    ```
1. Execute **Get-AzDataFactoryRun** para obter os detalhes de execuções de atividade para uma fatia **específica**. Copie o valor de data e hora da saída do comando anterior para especificar o valor do parâmetro StartDateTime. 

    ```powershell  
    Get-AzDataFactoryRun $df -DatasetName OutputDataset -StartDateTime "5/11/2017 09:00:00 PM"
    ```

   Veja o exemplo de saída: 

    ```
    Id                  : c0ddbd75-d0c7-4816-a775-704bbd7c7eab_636301332000000000_636301368000000000_OutputDataset
    ResourceGroupName   : ADFTutorialResourceGroup
    DataFactoryName     : ADFTutorialDataFactoryPSH0516
    DatasetName         : OutputDataset
    ProcessingStartTime : 5/16/2017 8:00:33 PM
    ProcessingEndTime   : 5/16/2017 8:01:36 PM
    PercentComplete     : 100
    DataSliceStart      : 5/11/2017 9:00:00 PM
    DataSliceEnd        : 5/11/2017 10:00:00 PM
    Status              : Succeeded
    Timestamp           : 5/16/2017 8:00:33 PM
    RetryAttempt        : 0
    Properties          : {}
    ErrorMessage        :
    ActivityName        : CopyFromBlobToSQL
    PipelineName        : ADFTutorialPipeline
    Type                : Copy  
    ```

Para obter uma documentação abrangente sobre os cmdlets de Data Factory, consulte [Referência de cmdlet de Data Factory](/powershell/module/az.datafactory).

## <a name="summary"></a>Resumo
Neste tutorial, você criou uma data factory do Azure para copiar dados de um blob do Azure para um banco de dados SQL do Azure. Você utilizou o PowerShell para criar a data factory, os serviços vinculados, os conjuntos de dados e um pipeline. Aqui estão as etapas de alto nível executadas nesse tutorial:  

1. Foi criado um **data factory**do Azure.
1. Foram criados **serviços vinculados**:

   a. Um serviço vinculado do **Armazenamento do Azure** para vincular sua conta de armazenamento do Azure que mantém os dados de entrada.     
   b. Um serviço vinculado do **SQL Azure** para vincular o banco de dados SQL que mantém os dados de saída.
1. Foram criados **conjuntos de dados** que descrevem os dados de entrada e de saída para os pipelines.
1. Foi criado um **pipeline** com a **Atividade de Cópia**, com **BlobSource** como a origem e **SqlSink** como o coletor.

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você usou o armazenamento de blobs do Azure como um armazenamento de dados de origem e um banco de dados SQL do Azure como um armazenamento de dados de destino em uma operação de cópia. A tabela a seguir fornece uma lista de armazenamentos de dados com suporte como origens ou destinos na atividade de cópia: 

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

Para saber mais sobre como copiar dados de/para um armazenamento de dados, clique no link para o armazenamento de dados na tabela. 

