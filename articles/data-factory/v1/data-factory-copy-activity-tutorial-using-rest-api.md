---
title: 'Tutorial: Usar a API REST para criar um pipeline do Azure Data Factory | Microsoft Docs'
description: Neste tutorial, você usa a API REST para criar um pipeline do Azure Data Factory com uma atividade de cópia a fim de copiar dados de um armazenamento de blobs do Azure para um banco de dados SQL do Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: 1704cdf8-30ad-49bc-a71c-4057e26e7350
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 6b5698d94a09096d58b316ca3b23bead5b1a39a7
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839401"
---
# <a name="tutorial-use-rest-api-to-create-an-azure-data-factory-pipeline-to-copy-data"></a>Tutorial: Usar a API REST para criar um pipeline do Azure Data Factory para copiar dados 
> [!div class="op_single_selector"]
> * [Visão geral e pré-requisitos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Modelo do Azure Resource Manager](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [API REST](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [API do .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)
> 
> 

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço Data Factory, consulte o [tutorial de atividade de cópia](../quickstart-create-data-factory-rest-api.md). 

Neste artigo, você aprenderá a usar a API REST para criar um data factory com um pipeline que copia dados de um armazenamento de blobs do Azure para um Banco de Dados SQL do Azure. Se você ainda está se familiarizando com o Azure Data Factory, leia o artigo [Introdução ao Azure Data Factory](data-factory-introduction.md) antes de fazer este tutorial.   

Neste tutorial, você criará um pipeline com uma atividade: Atividade de Cópia. A atividade de cópia copia dados de um armazenamento de dados com suporte para um armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados com suporte como origens e coletores, confira [Armazenamentos de dados com suporte](data-factory-data-movement-activities.md#supported-data-stores-and-formats). A atividade é habilitada por um serviço globalmente disponível que pode copiar dados entre vários repositórios de dados de forma segura, confiável e escalonável. Para saber mais sobre a atividade de cópia, confira [Atividades de movimentação de dados](data-factory-data-movement-activities.md).

Um pipeline pode ter mais de uma atividade. E você pode encadear duas atividades (executar uma atividade após a outra) definindo o conjunto de dados de saída de uma atividade como o conjunto de dados de entrada da outra atividade. Para saber mais, confira [Várias atividades em um pipeline](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline).

> [!NOTE]
> Este artigo não cobre todas as APIs REST do Data Factory. Confira [Referência de API REST do Data Factory](/rest/api/datafactory/) para obter uma documentação abrangente sobre os cmdlets de Data Factory.
>  
> O pipeline de dados neste tutorial copia os dados de um armazenamento de dados de origem para um armazenamento de dados de destino. Para obter um tutorial sobre como transformar dados usando o Azure Data Factory, confira [Tutorial: Criar um pipeline para transformar dados usando um cluster Hadoop](data-factory-build-your-first-pipeline.md).

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* Percorra o artigo [Visão geral do tutorial](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) e conclua as etapas de **pré-requisito** .
* Instale o [Curl](https://curl.haxx.se/dlwiz/) em seu computador. Você pode usar a ferramenta Curl com comandos REST para criar um data factory. 
* Siga as instruções [deste artigo](../../active-directory/develop/howto-create-service-principal-portal.md) para: 
  1. Crie um aplicativo Web chamado **ADFCopyTutorialApp** no Azure Active Directory.
  2. Obtenha a **ID do cliente** e a **chave secreta**. 
  3. Obtenha a **ID do locatário**. 
  4. Atribua o aplicativo **ADFCopyTutorialApp** à função de **Colaborador do Data Factory**.  
* Instale o [Azure PowerShell](/powershell/azure/overview).  
* Inicie **PowerShell** e siga as etapas a seguir. Mantenha o Azure PowerShell aberto até o fim deste tutorial. Se você fechar e reabrir, precisará executar os comandos novamente.
  
  1. Execute o comando abaixo e insira o nome de usuário e a senha que você usa para entrar no portal do Azure:
    
     ```PowerShell 
     Connect-AzAccount
     ```   
  2. Execute o comando abaixo para exibir todas as assinaturas dessa conta:

     ```PowerShell     
     Get-AzSubscription
     ``` 
  3. Execute o comando a seguir para selecionar a assinatura com a qual deseja trabalhar. Substitua **&lt;NameOfAzureSubscription**&gt; pelo nome da sua assinatura do Azure. 
     
     ```PowerShell
     Get-AzSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzContext
     ```
  4. Crie um grupo de recursos do Azure denominado **ADFTutorialResourceGroup** executando o comando a seguir no PowerShell:  

     ```PowerShell     
      New-AzResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"
     ```
     
      Se o grupo de recursos já existe, especifique se deseja atualizá-lo (S) ou mantê-lo como (N). 
     
      Algumas das etapas neste tutorial supõem que você utilize o grupo de recursos denominado ADFTutorialResourceGroup. Se você utilizar um grupo de recursos diferente, precisará usar o nome do seu grupo de recursos no lugar de ADFTutorialResourceGroup neste tutorial.

## <a name="create-json-definitions"></a>Criar definições JSON
Crie os arquivos JSON a seguir na pasta onde curl.exe está localizado. 

### <a name="datafactoryjson"></a>datafactory.json
> [!IMPORTANT]
> O nome deve ser exclusivo e, portanto, convém colocar um prefixo/sufixo no ADFCopyTutorialDF para torná-lo um nome exclusivo. 
> 
> 

```JSON
{  
    "name": "ADFCopyTutorialDF",  
    "location": "WestUS"
}  
```

### <a name="azurestoragelinkedservicejson"></a>azurestoragelinkedservice.json
> [!IMPORTANT]
> Substitua **nome da conta** e **chave da conta** pelo nome e pela chave da sua conta de armazenamento do Azure. Para saber como obter sua chave de acesso de armazenamento, confira [Exibir, copiar e regenerar chaves de acesso de armazenamento](../../storage/common/storage-account-manage.md#access-keys).

```JSON
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

Para obter detalhes sobre as propriedades JSON, confira [Serviço vinculado do Armazenamento do Azure](data-factory-azure-blob-connector.md#azure-storage-linked-service).

### <a name="azuresqllinkedservicejson"></a>azuresqllinkedservice.json
> [!IMPORTANT]
> Substitua **servername**, **databasename**, **username** e **passwords** pelos nomes do servidor SQL do Azure e do banco de dados SQL, pela conta de usuário e pela senha da conta.  
> 
>

```JSON
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "description": "",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30"
        }
    }
}
```

Para obter detalhes sobre as propriedades JSON, confira [Serviço vinculado do Azure SQL](data-factory-azure-sql-connector.md#linked-service-properties).

### <a name="inputdatasetjson"></a>inputdataset.json

```JSON
{
  "name": "AzureBlobInput",
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
      "folderPath": "adftutorial/",
      "fileName": "emp.txt",
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

Para saber mais sobre essas propriedades JSON, confira o [artigo sobre o conector de blobs do Azure](data-factory-azure-blob-connector.md#dataset-properties).

### <a name="outputdatasetjson"></a>outputdataset.json

```JSON
{
  "name": "AzureSqlOutput",
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

### <a name="pipelinejson"></a>pipeline.json

```JSON
{
  "name": "ADFTutorialPipeline",
  "properties": {
    "description": "Copy data from a blob to Azure SQL table",
    "activities": [
      {
        "name": "CopyFromBlobToSQL",
        "description": "Push Regional Effectiveness Campaign data to Azure SQL database",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSqlOutput"
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
- A entrada da atividade é definida como **AzureBlobInput** e a saída da atividade é definida como **AzureSqlOutput**. 
- Na seção **typeProperties**, **BlobSource** é especificado como o tipo de origem e **SqlSink** é especificado como o tipo de coletor. Para ver a lista completa dos armazenamentos de dados com suporte pela atividade de cópia, como origens e coletores, confira [Armazenamentos de dados com suporte](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Para aprender a usar um armazenamento de dados com suporte específico como fonte/coletor, clique no link na tabela.  
 
Substitua o valor da propriedade **start** pelo dia atual e o valor de **end** pelo dia seguinte. Você pode especificar apenas a parte da data e ignorar a parte de hora do valor de data/hora. Por exemplo, "2017-02-03", que é equivalente a "2017-02-03T00:00:00Z"
 
Ambos os valores de data/hora de início e de término devem estar no [formato ISO](https://en.wikipedia.org/wiki/ISO_8601). Por exemplo:  2016-10-14T16:32:41Z. A hora **final** é opcional, mas nós a usaremos neste tutorial. 
 
Se você não especificar o valor para a propriedade **end**, ele será calculado como "**início + 48 horas**". Para executar o pipeline indefinidamente, especifique **9999-09-09** como o valor para a propriedade **end**.
 
No exemplo anterior, há 24 fatias de dados, pois cada fatia de dados é produzida a cada hora.

Para obter descrições das propriedades JSON em uma definição de pipeline, consulte o artigo [Criar pipelines](data-factory-create-pipelines.md). Para obter descrições das propriedades JSON em uma definição de atividade de cópia, consulte [Atividades de movimentação de dados](data-factory-data-movement-activities.md). Para obter descrições das propriedades JSON com suporte pelo BlobSource, consulte o [artigo sobre o conector de blobs do Azure](data-factory-azure-blob-connector.md). Para obter descrições das propriedades JSON com suporte pelo SqlSink, consulte o [artigo sobre o conector do Banco de Dados SQL do Azure](data-factory-azure-sql-connector.md).

## <a name="set-global-variables"></a>Definir variáveis globais
No Azure PowerShell, execute os comandos a seguir depois de substituir os valores pelos seus próprios:

> [!IMPORTANT]
> Veja a seção [Pré-requisitos](#prerequisites) para obter instruções sobre como obter a ID do cliente, o segredo do cliente, a ID do locatário e ID da assinatura.   
> 
> 

```JSON
$client_id = "<client ID of application in AAD>"
$client_secret = "<client key of application in AAD>"
$tenant = "<Azure tenant ID>";
$subscription_id="<Azure subscription ID>";

$rg = "ADFTutorialResourceGroup"
```

Execute o comando abaixo depois de atualizar o nome do data factory que você está usando: 

```
$adf = "ADFCopyTutorialDF"
```

## <a name="authenticate-with-aad"></a>Autenticar com o AAD
Execute o comando a seguir para autenticar com o AAD (Azure Active Directory): 

```PowerShell
$cmd = { .\curl.exe -X POST https://login.microsoftonline.com/$tenant/oauth2/token  -F grant_type=client_credentials  -F resource=https://management.core.windows.net/ -F client_id=$client_id -F client_secret=$client_secret };
$responseToken = Invoke-Command -scriptblock $cmd;
$accessToken = (ConvertFrom-Json $responseToken).access_token;

(ConvertFrom-Json $responseToken) 
```

## <a name="create-data-factory"></a>Criar um data factory
Nesta etapa, você criará um Azure Data Factory chamado **ADFCopyTutorialDF**. Uma fábrica de dados pode ter um ou mais pipelines. Um pipeline em um data factory pode ter uma ou mais atividades. Por exemplo, uma atividade de cópia para copiar dados de uma fonte para um armazenamento de dados de destino. Uma atividade do HDInsight Hive para executar um script Hive para transformar dados de entrada para produzir dados de saída. Execute os seguintes comandos para criar o data factory: 

1. Atribua o comando à variável chamada **cmd**. 
   
    > [!IMPORTANT]
    > Confirme se o nome do data factory especificado aqui (ADFCopyTutorialDF) corresponde ao nome especificado no **datafactory.json**. 
   
    ```PowerShell
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data “@datafactory.json” https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/ADFCopyTutorialDF0411?api-version=2015-10-01};
    ```
2. Execute o comando usando **Invoke-Command**.
   
    ```PowerShell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Exiba os resultados. Se o data factory foi criado com êxito, você verá o JSON para o data factory nos **resultados**. Caso contrário, você verá uma mensagem de erro.  
   
    ```
    Write-Host $results
    ```

Observe os seguintes pontos:

* O nome do Azure Data Factory deve ser globalmente exclusivo. Se o erro **O nome de data factory “ADFCopyTutorialDF” não está disponível** for exibido nos resultados, execute as seguintes etapas:  
  
  1. Altere o nome (por exemplo, yournameADFCopyTutorialDF) no arquivo **datafactory.json** .
  2. No primeiro comando em que a variável **$cmd** é atribuída um valor, substitua ADFCopyTutorialDF pelo novo nome e execute o comando. 
  3. Execute os próximos dois comandos para invocar a API REST a fim de criar o data factory e imprima os resultados da operação. 
     
     Veja o tópico [Data Factory - regras de nomenclatura](data-factory-naming-rules.md) para ver as regras de nomenclatura para artefatos do Data Factory.
* Para criar instâncias do Data Factory, você precisa ser um colaborador/administrador da assinatura do Azure
* O nome do data factory pode ser registrado futuramente como um nome DNS e tornar-se visível publicamente.
* Se você receber o erro "**Esta assinatura não está registrada para usar o namespace Microsoft.DataFactory**", siga um dos seguintes procedimentos e tente a publicação novamente: 
  
  * No Azure PowerShell, execute o comando a seguir para registrar o provedor do Data Factory: 

    ```PowerShell    
    Register-AzResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```
    Você pode executar o comando a seguir para confirmar se o provedor do Data Factory está registrado. 
    
    ```PowerShell
    Get-AzResourceProvider
    ```
  * Faça logon no [portal do Azure](https://portal.azure.com) usando a assinatura do Azure e navegue até uma folha do Data Factory (ou) crie um data factory no portal do Azure. Essa ação registra automaticamente o provedor para você.

Antes de criar um pipeline, primeiro você precisará criar algumas entidades do Data Factory. Primeiro, você cria serviços vinculados para vincular os repositórios de dados de origem e de destino a seu repositório de dados. Em seguida, defina conjuntos de dados de entrada e saída para representar dados em repositórios de dados vinculados. Finalmente, crie o pipeline com uma atividade que usa esses conjuntos de dados.

## <a name="create-linked-services"></a>Criar serviços vinculados
Os serviços vinculados são criados em um data factory para vincular seus armazenamentos de dados e serviços de computação ao data factory. Neste tutorial, você não usa serviços de computação, como o Azure HDInsight ou o Azure Data Lake Analytics. Você usa dois armazenamentos de dados do tipo Armazenamento do Azure (origem) e o banco de dados SQL do Azure (destino). Portanto, você criará dois serviços vinculados chamados AzureStorageLinkedService e AzureSqlLinkedService dos tipos: AzureStorage e AzureSqlDatabase.  

O AzureStorageLinkedService vincula sua conta do armazenamento do Azure ao data factory. Essa conta de armazenamento é aquela na qual você criou um contêiner e carregou os dados como parte dos [pré-requisitos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).   

O AzureSqlLinkedService vincula seu banco de dados SQL do Azure ao data factory. Os dados copiados do armazenamento de blobs são armazenados no banco de dados. Você criou a tabela emp no banco de dados como parte dos [pré-requisitos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).  

### <a name="create-azure-storage-linked-service"></a>Criar o serviço vinculado do armazenamento do Azure
Nesta etapa, você vincula a conta do Armazenamento do Azure ao data factory. Especifique o nome e a chave da sua conta de armazenamento do Azure nesta seção. Confira [Serviço vinculado de armazenamento do Azure](data-factory-azure-blob-connector.md#azure-storage-linked-service) para obter detalhes sobre os elementos JSON para definir um serviço vinculado de armazenamento do Azure.  

1. Atribua o comando à variável chamada **cmd**. 

    ```PowerShell   
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@azurestoragelinkedservice.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/AzureStorageLinkedService?api-version=2015-10-01};
    ```
2. Execute o comando usando **Invoke-Command**.

    ```PowerShell   
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Exiba os resultados. Se o serviço vinculado tiver sido criado com êxito, você verá o JSON do serviço vinculado nos **resultados**. Caso contrário, você verá uma mensagem de erro.

    ```PowerShell   
    Write-Host $results
    ```

### <a name="create-azure-sql-linked-service"></a>Criar serviço vinculado do Azure SQL
Nesta etapa, você vincula o banco de dados SQL do Azure ao data factory. Especifique o nome do SQL Server do Azure, nome do banco de dados, nome de usuário e senha de usuário nesta seção. Consulte [Serviço vinculado do SQL do Azure](data-factory-azure-sql-connector.md#linked-service-properties) para obter detalhes sobre os propriedades JSON usadas para definir um serviço vinculado do SQL do Azure.

1. Atribua o comando à variável chamada **cmd**. 
   
    ```PowerShell
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data “@azuresqllinkedservice.json” https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/AzureSqlLinkedService?api-version=2015-10-01};
    ```
2. Execute o comando usando **Invoke-Command**.
   
    ```PowerShell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Exiba os resultados. Se o serviço vinculado tiver sido criado com êxito, você verá o JSON do serviço vinculado nos **resultados**. Caso contrário, você verá uma mensagem de erro.
   
    ```PowerShell
    Write-Host $results
    ```

## <a name="create-datasets"></a>Criar conjuntos de dados
Na etapa anterior, você criou serviços vinculados para vincular a conta do Armazenamento do Azure e um banco de dados SQL do Azure ao data factory. Nesta etapa, você define dois conjuntos de dados, chamados AzureBlobInput e AzureSqlOutput, que representam os dados de entrada e saída armazenados nos repositórios de dados referidos por AzureStorageLinkedService e AzureSqlLinkedService, respectivamente.

O serviço vinculado do Armazenamento do Azure especifica a cadeia de conexão que o serviço Data Factory usa no tempo de execução para se conectar à sua conta do Armazenamento do Azure. E o conjunto de dados de blob de entrada (AzureBlobInput) especifica o contêiner e a pasta que contém os dados.  

Da mesma forma, o serviço vinculado do Banco de Dados SQL especifica a cadeia de conexão que o serviço Data Factory usa no tempo de execução para se conectar ao seu Banco de Dados SQL do Azure. Além disso, o conjunto de dados da tabela SQL de saída (OutputDataset) especifica a tabela no banco de dados na qual os dados do armazenamento de blobs são copiados. 

### <a name="create-input-dataset"></a>Criar conjunto de dados de entrada
Nesta etapa, você criará um conjunto de dados denominado AzureBlobInput que aponta para um arquivo de blob (emp.txt) na pasta raiz de um contêiner de blob (adftutorial) no Armazenamento do Azure representado pelo serviço vinculado AzureStorageLinkedService. Se você não especificar um valor para fileName (ou ignorá-lo), os dados de todos os blobs na pasta de entrada serão copiados no destino. Neste tutorial, você deve especificar um valor para o fileName. 

1. Atribua o comando à variável chamada **cmd**. 

    ```PowerSHell   
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@inputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureBlobInput?api-version=2015-10-01};
    ```
2. Execute o comando usando **Invoke-Command**.
   
    ```PowerShell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Exiba os resultados. Se o conjunto de dados tiver sido criado com êxito, você verá o JSON do conjunto de dados nos **resultados**. Caso contrário, verá uma mensagem de erro.
   
    ```PowerShell
    Write-Host $results
    ```

### <a name="create-output-dataset"></a>Criar conjunto de dados de saída
O serviço vinculado do Banco de Dados SQL especifica a cadeia de conexão que o serviço Data Factory usa no tempo de execução para se conectar ao seu Banco de Dados SQL do Azure. O conjunto de dados da tabela SQL de saída (OutputDataset) criado nesta etapa especifica a tabela no banco de dados na qual os dados do armazenamento de blobs são copiados.

1. Atribua o comando à variável chamada **cmd**.

    ```PowerShell   
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@outputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureSqlOutput?api-version=2015-10-01};
    ```
2. Execute o comando usando **Invoke-Command**.
    
    ```PowerShell   
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Exiba os resultados. Se o conjunto de dados tiver sido criado com êxito, você verá o JSON do conjunto de dados nos **resultados**. Caso contrário, verá uma mensagem de erro.
   
    ```PowerShell
    Write-Host $results
    ``` 

## <a name="create-pipeline"></a>Criar um pipeline
Nesta etapa, você cria um pipeline com uma **atividade de cópia** que usa **AzureBlobInput** como uma entrada e **AzureSqlOutput** como uma saída.

Atualmente, o conjunto de dados de saída é o que conduz o agendamento. Neste tutorial, o conjunto de dados de saída está configurado para produzir uma fatia uma vez a cada hora. O pipeline tem uma hora de início e uma hora de término com um dia de diferença, o que significa 24 horas. Portanto, 24 fatias de conjunto de dados de saída são produzidas pelo pipeline. 

1. Atribua o comando à variável chamada **cmd**.

    ```PowerShell   
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@pipeline.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datapipelines/MyFirstPipeline?api-version=2015-10-01};
    ```
2. Execute o comando usando **Invoke-Command**.

    ```PowerShell   
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Exiba os resultados. Se o conjunto de dados tiver sido criado com êxito, você verá o JSON do conjunto de dados nos **resultados**. Caso contrário, verá uma mensagem de erro.  

    ```PowerShell   
    Write-Host $results
    ```

**Parabéns!** Você criou com êxito um data factory do Azure, com um pipeline que copia dados do Armazenamento de Blobs do Azure para o Banco de Dados SQL do Azure.

## <a name="monitor-pipeline"></a>Monitorar o pipeline
Nesta etapa, você pode usar a API REST do Data Factory para monitorar fatias produzidas pelo pipeline.

```PowerShell
$ds ="AzureSqlOutput"
```

> [!IMPORTANT] 
> Verifique se as horas de início e de término especificadas no comando a seguir correspondem aos horários de início e de término do pipeline. 

```PowerShell
$cmd = {.\curl.exe -X GET -H "Authorization: Bearer $accessToken" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/$ds/slices?start=2017-05-11T00%3a00%3a00.0000000Z"&"end=2017-05-12T00%3a00%3a00.0000000Z"&"api-version=2015-10-01};
```

```PowerShell
$results2 = Invoke-Command -scriptblock $cmd;
```

```PowerShell
IF ((ConvertFrom-Json $results2).value -ne $NULL) {
    ConvertFrom-Json $results2 | Select-Object -Expand value | Format-Table
} else {
        (convertFrom-Json $results2).RemoteException
}
```

Execute Invoke-Command e o próximo até ver uma fatia no estado **Pronto** ou **Falha**. Quando a fatia estiver no estado Pronto, verifique os dados de saída na tabela **emp** no Banco de Dados SQL do Azure. 

Para cada fatia, duas linhas de dados do arquivo de origem são copiadas para a tabela emp no Banco de Dados SQL do Azure. Portanto, você verá 24 novos registros na tabela emp quando todas as fatias forem processadas com êxito (no estado Pronto). 

## <a name="summary"></a>Resumo
Neste tutorial, você usou uma API REST para criar um data factory do Azure e copiar dados de um blob do Azure para um banco de dados SQL do Azure. Aqui estão as etapas de alto nível executadas nesse tutorial:  

1. Foi criado um **data factory**do Azure.
2. Foram criados **serviços vinculados**:
   1. Um serviço vinculado do Armazenamento do Azure para vincular a conta do Armazenamento do Azure que contém os dados de entrada.     
   2. Um serviço vinculado do Azure SQL para vincular o banco de dados SQL do Azure que contém os dados de saída. 
3. Foram criados **conjuntos de dados**que descrevem os dados de entrada e de saída para os pipelines.
4. Foi criado um **pipeline** com uma Atividade de Cópia com BlobSource como origem e SqlSink como coletor. 

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você usou o armazenamento de blobs do Azure como um armazenamento de dados de origem e um banco de dados SQL do Azure como um armazenamento de dados de destino em uma operação de cópia. A tabela a seguir fornece uma lista de armazenamentos de dados com suporte como origens ou destinos na atividade de cópia: 

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

Para saber mais sobre como copiar dados de/para um armazenamento de dados, clique no link para o armazenamento de dados na tabela.
