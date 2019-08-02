---
title: Criar um Azure Data Factory usando a API REST | Microsoft Docs
description: Crie um Azure Data Factory para copiar dados de um local em um Armazenamento de Blobs do Azure para outro local.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: rest-api
ms.topic: quickstart
ms.date: 06/10/2019
ms.author: jingwang
ms.openlocfilehash: 96f65ef17f25e1ff6ee3847507b33492642af5e8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67067477"
---
# <a name="quickstart-create-an-azure-data-factory-and-pipeline-by-using-the-rest-api"></a>Início Rápido: Crie um pipeline e um Azure Data Factory usando a API REST

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Versão atual](quickstart-create-data-factory-rest-api.md)

O Azure Data Factory é um serviço de integração de dados baseado em nuvem que permite que você crie fluxos de trabalho controlados por dados na nuvem para orquestrar e automatizar a movimentação e a transformação de dados. Usando o Azure Data Factory, você pode criar e agendar fluxos de trabalho orientados a dados (chamados de pipelines) que podem ingerir dados de repositórios de dados diferentes, processar/transformr os dados usando serviços de computação como o Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics e Azure Machine Learning e publicar os dados de saída em repositórios de dados como o SQL Data Warehouse do Azure para consumo pelos aplicativos de business intelligence (BI).

Este guia de início rápido descreve como usar a API REST para criar um Azure Data Factory. O pipeline nesse data factory copia dados de uma localização para outra em um Armazenamento de Blobs do Azure.

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* **Assinatura do Azure**. Se você não tiver uma assinatura, poderá criar uma conta de [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
* **Conta de Armazenamento do Azure**. Você usa o armazenamento de blobs como um armazenamento de dados de **origem** e de **coletor**. Se você não tiver uma conta de Armazenamento do Azure, veja o artigo [Criar uma conta de armazenamento](../storage/common/storage-quickstart-create-account.md) para conhecer as etapas para criar uma.
* Crie um **contêiner de blob** no Armazenamento de Blobs, crie uma **pasta** de entrada no contêiner e carregue alguns arquivos na pasta. Você pode usar ferramentas como o [Gerenciador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/) para se conectar ao armazenamento de Blobs do Azure, criar um contêiner de blobs, carregar arquivos de entrada e verificar os arquivos de saída.
* Instale o **Azure PowerShell**. Siga as instruções em [Como instalar e configurar o Azure PowerShell](/powershell/azure/install-Az-ps). Este guia de início rápido usa o PowerShell para invocar chamadas à API REST.
* **Crie um aplicativo no Azure Active Directory** seguindo [esta instrução](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application). Anote os valores a seguir, que você usará em etapas posteriores: **ID do aplicativo**, **chave de autenticação** e **ID do locatário**. Atribua o aplicativo à função "**Colaborador**".

## <a name="set-global-variables"></a>Definir variáveis globais

1. Inicie o **PowerShell**. Mantenha o Azure PowerShell aberto até o fim deste guia de início rápido. Se você fechar e reabrir, precisará executar os comandos novamente.

    Execute o comando a seguir e insira o nome de usuário e senha usados para entrar no portal do Azure:
    
    ```powershell
    Connect-AzAccount
    ```
    Execute o comando abaixo para exibir todas as assinaturas dessa conta:

    ```powershell
    Get-AzSubscription
    ```
    Execute o comando a seguir para selecionar a assinatura com a qual deseja trabalhar. Substitua **SubscriptionId** pela ID da assinatura do Azure:

    ```powershell
    Select-AzSubscription -SubscriptionId "<SubscriptionId>"
    ```
2. Execute os comandos a seguir depois de substituir os espaços reservados pelos seus próprios valores, para definir variáveis globais a serem usadas em etapas posteriores.

    ```powershell
    $tenantID = "<your tenant ID>"
    $appId = "<your application ID>"
    $authKey = "<your authentication key for the application>"
    $subsId = "<your subscription ID to create the factory>"
    $resourceGroup = "<your resource group to create the factory>"
    $dataFactoryName = "<specify the name of data factory to create. It must be globally unique.>"
    $apiVersion = "2018-06-01"
    ```

## <a name="authenticate-with-azure-ad"></a>Autenticar com o Azure AD

Execute os comandos a seguir para autenticar com o AAD (Azure Active Directory):

```powershell
$AuthContext = [Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext]"https://login.microsoftonline.com/${tenantId}"
$cred = New-Object -TypeName Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential -ArgumentList ($appId, $authKey)
$result = $AuthContext.AcquireTokenAsync("https://management.core.windows.net/", $cred).GetAwaiter().GetResult()
$authHeader = @{
'Content-Type'='application/json'
'Accept'='application/json'
'Authorization'=$result.CreateAuthorizationHeader()
}
```

## <a name="create-a-data-factory"></a>Criar uma data factory

Execute os seguintes comandos para criar um data factory:

```powershell
$request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}?api-version=${apiVersion}"
$body = @"
{
    "name": "$dataFactoryName",
    "location": "East US",
    "properties": {},
    "identity": {
        "type": "SystemAssigned"
    }
}
"@
$response = Invoke-RestMethod -Method PUT -Uri $request -Header $authHeader -Body $body
$response | ConvertTo-Json
```

Observe os seguintes pontos:

* O nome da data factory do Azure deve ser globalmente exclusivo. Se você receber o erro a seguir, altere o nome e tente novamente.

    ```
    Data factory name "ADFv2QuickStartDataFactory" is not available.
    ```
* Para obter uma lista de regiões do Azure no qual o Data Factory está disponível no momento, selecione as regiões que relevantes para você na página a seguir e, em seguida, expanda **Análise** para localizar **Data Factory**: [Produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/). Os armazenamentos de dados (Armazenamento do Azure, Banco de Dados SQL do Azure, etc.) e serviços de computação (HDInsight, etc.) usados pelo data factory podem estar em outras regiões.

Veja a resposta de exemplo:

```json
{
    "name": "<dataFactoryName>",
    "tags": {
    },
    "properties":  {
        "provisioningState":  "Succeeded",
        "loggingStorageAccountKey":  "**********",
        "createTime":  "2017-09-14T06:22:59.9106216Z",
        "version":  "2018-06-01"
    },
    "identity":  {
        "type":  "SystemAssigned",
        "principalId":  "<service principal ID>",
        "tenantId":  "<tenant ID>"
    },
    "id":  "dataFactoryName",
    "type":  "Microsoft.DataFactory/factories",
    "location":  "East US"
}
```

## <a name="create-linked-services"></a>Criar serviços vinculados

Os serviços vinculados são criados em um data factory para vincular seus armazenamentos de dados e serviços de computação ao data factory. Neste guia de início rápido, você só precisa criar um serviço vinculado do Armazenamento do Azure tanto como a origem da cópia quanto como o repositório de coletor, denominado "AzureStorageLinkedService" na amostra.

Execute os comandos a seguir para criar um serviço vinculado chamado **AzureStorageLinkedService**:

Antes de executar os comandos, substitua &lt;accountName&gt; e &lt;accountKey&gt; pelo nome e pela chave da sua conta de armazenamento do Azure, respectivamente.

```powershell
$request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/linkedservices/AzureStorageLinkedService?api-version=${apiVersion}"
$body = @"
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": {
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>",
                "type": "SecureString"
            }
        }
    }
}
"@
$response = Invoke-RestMethod -Method PUT -Uri $request -Header $authHeader -Body $body
$response | ConvertTo-Json
```

Veja o exemplo de saída:

```json
{
    "id":  "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>/linkedservices/AzureStorageLinkedService",
    "name":  "AzureStorageLinkedService",
    "properties":  {
        "type":  "AzureStorage",
        "typeProperties":  {
            "connectionString":  "@{value=**********; type=SecureString}"
        }
    },
    "etag":  "0000c552-0000-0000-0000-59b1459c0000"
}
```

## <a name="create-datasets"></a>Criar conjuntos de dados

Você define um conjunto de dados que representa os dados a copiar de uma origem para um coletor. Neste exemplo, esse conjunto de dados de Blob refere-se ao serviço vinculado do Armazenamento do Azure que você criou na etapa anterior. O conjunto de dados usa um parâmetro cujo valor é definido em uma atividade que consome o conjunto de dados. O parâmetro é usado para construir o "folderPath" que aponta para o local em que os dados residem/estão armazenados.

```powershell
$request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/datasets/BlobDataset?api-version=${apiVersion}"
$body = @"
{
    "name": "BlobDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": {
                "value": "@{dataset().path}",
                "type": "Expression"
            }
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "path": {
                "type": "String"
            }
        }
    }
}
"@
$response = Invoke-RestMethod -Method PUT -Uri $request -Header $authHeader -Body $body
$response | ConvertTo-Json
```

Veja o exemplo de saída:

```json
{
    "id":  "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>/datasets/BlobDataset",
    "name":  "BlobDataset",
    "properties":  {
        "type":  "AzureBlob",
        "typeProperties":  {
            "folderPath":  "@{value=@{dataset().path}; type=Expression}"
        },
        "linkedServiceName":  {
            "referenceName":  "AzureStorageLinkedService",
            "type":  "LinkedServiceReference"
        },
        "parameters":  {
            "path":  "@{type=String}"
        }
    },
    "etag":  "0000c752-0000-0000-0000-59b1459d0000"
}
```

## <a name="create-pipeline"></a>Criar um pipeline

Neste exemplo, este pipeline contém uma atividade e leva dois parâmetros – caminho do blob de entrada e caminho do blob de saída. Os valores para esses parâmetros são definidos quando o pipeline é disparado/executado. A atividade de cópia refere-se ao mesmo conjunto de dados de blob criado na etapa anterior como entrada e saída. Quando o conjunto de dados é usado como um conjunto de dados de entrada, o caminho de entrada é especificado. E quando o conjunto de dados é usado como um conjunto de dados de saída, o caminho de saída é especificado.

```powershell
$request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/pipelines/Adfv2QuickStartPipeline?api-version=${apiVersion}"
$body = @"
{
    "name": "Adfv2QuickStartPipeline",
    "properties": {
        "activities": [
            {
                "name": "CopyFromBlobToBlob",
                "type": "Copy",
                "inputs": [
                    {
                        "referenceName": "BlobDataset",
                        "parameters": {
                            "path": "@pipeline().parameters.inputPath"
                        },
                    "type": "DatasetReference"
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "BlobDataset",
                        "parameters": {
                            "path": "@pipeline().parameters.outputPath"
                        },
                        "type": "DatasetReference"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                }
            }
        ],
        "parameters": {
            "inputPath": {
                "type": "String"
            },
            "outputPath": {
                "type": "String"
            }
        }
    }
}
"@
$response = Invoke-RestMethod -Method PUT -Uri $request -Header $authHeader -Body $body
$response | ConvertTo-Json
```

Veja o exemplo de saída:

```json
{
    "id":  "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>/pipelines/Adfv2QuickStartPipeline",
    "name":  "Adfv2QuickStartPipeline",
    "properties":  {
        "activities":  [
            "@{name=CopyFromBlobToBlob; type=Copy; inputs=System.Object[]; outputs=System.Object[]; typeProperties=}"
        ],
        "parameters":  {
            "inputPath":  "@{type=String}",
            "outputPath":  "@{type=String}"
        }
    },
    "etag":  "0000c852-0000-0000-0000-59b1459e0000"
}
```

## <a name="create-pipeline-run"></a>Criar uma execução de pipeline

Nesta etapa, você define os valores dos parâmetros **inputPath** e **outputPath** especificados no pipeline com os valores reais dos caminhos de blob de origem e de coletor e então dispara uma execução de pipeline. A ID de execução de pipeline retornada no corpo da resposta é usado em monitoramento posterior de API.

Substitua o valor de **inputPath** e **outputPath** pelo caminho de blob de origem e de coletor de/para o qual copiar dados antes de salvar o arquivo.


```powershell
$request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/pipelines/Adfv2QuickStartPipeline/createRun?api-version=${apiVersion}"
$body = @"
{
    "inputPath": "<the path to existing blob(s) to copy data from, e.g. containername/path>",
    "outputPath": "<the blob path to copy data to, e.g. containername/path>"
}
"@
$response = Invoke-RestMethod -Method POST -Uri $request -Header $authHeader -Body $body
$response | ConvertTo-Json
$runId = $response.runId
```

Veja o exemplo de saída:

```json
{
    "runId":  "2f26be35-c112-43fa-9eaa-8ba93ea57881"
}
```

## <a name="monitor-pipeline"></a>Monitorar o pipeline

1. Execute o script a seguir para verificar continuamente o status da execução de pipeline até que ela termine de copiar os dados.

    ```powershell
    $request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/pipelineruns/${runId}?api-version=${apiVersion}"
    while ($True) {
        $response = Invoke-RestMethod -Method GET -Uri $request -Header $authHeader
        Write-Host  "Pipeline run status: " $response.Status -foregroundcolor "Yellow"

        if ($response.Status -eq "InProgress") {
            Start-Sleep -Seconds 15
        }
        else {
            $response | ConvertTo-Json
            break
        }
    }
    ```

    Veja o exemplo de saída:

    ```json
    {
        "key":  "000000000-0000-0000-0000-00000000000",
        "timestamp":  "2017-09-07T13:12:39.5561795Z",
        "runId":  "000000000-0000-0000-0000-000000000000",
        "dataFactoryName":  "<dataFactoryName>",
        "pipelineName":  "Adfv2QuickStartPipeline",
        "parameters":  [
            "inputPath: <inputBlobPath>",
            "outputPath: <outputBlobPath>"
        ],
        "parametersCount":  2,
        "parameterNames":  [
            "inputPath",
            "outputPath"
        ],
        "parameterNamesCount":  2,
        "parameterValues":  [
            "<inputBlobPath>",
            "<outputBlobPath>"
        ],
        "parameterValuesCount":  2,
        "runStart":  "2017-09-07T13:12:00.3710792Z",
        "runEnd":  "2017-09-07T13:12:39.5561795Z",
        "durationInMs":  39185,
        "status":  "Succeeded",
        "message":  ""
    }
    ```

2. Execute o script a seguir para recuperar os detalhes de execução da atividade de cópia, por exemplo, o tamanho dos dados lidos/gravados.

    ```powershell
    $request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/pipelineruns/${runId}/activityruns?api-version=${apiVersion}&startTime="+(Get-Date).ToString('yyyy-MM-dd')+"&endTime="+(Get-Date).AddDays(1).ToString('yyyy-MM-dd')+"&pipelineName=Adfv2QuickStartPipeline"
    $response = Invoke-RestMethod -Method GET -Uri $request -Header $authHeader
    $response | ConvertTo-Json
    ```

    Veja o exemplo de saída:

    ```json
    {
        "value":  [
            {
                "id":  "000000000-0000-0000-0000-00000000000",
                "timestamp":  "2017-09-07T13:12:38.4780542Z",
                "pipelineRunId":  "000000000-0000-00000-0000-0000000000000",
                "pipelineName":  "Adfv2QuickStartPipeline",
                "status":  "Succeeded",
                "failureType":  "",
                "linkedServiceName":  "",
                "activityName":  "CopyFromBlobToBlob",
                "activityType":  "Copy",
                "activityStart":  "2017-09-07T13:12:02.3299261Z",
                "activityEnd":  "2017-09-07T13:12:38.4780542Z",
                "duration":  36148,
                "input":  "@{source=; sink=}",
                "output":  "@{dataRead=331452208; dataWritten=331452208; copyDuration=22; throughput=14712.9; errors=System.Object[]; effectiveIntegrationRuntime=DefaultIntegrationRuntime (West US); usedDataIntegrationUnits=2; billedDuration=22}",
                "error":  "@{errorCode=; message=; failureType=; target=CopyFromBlobToBlob}"
            }
        ]
    }
    ```

## <a name="verify-the-output"></a>Verificar a saída

Use o Gerenciador de Armazenamento do Azure para verificar se os blobs são copiados de "inputBlobPath" para "outputBlobPath", conforme você especificou ao criar uma execução de pipeline.

## <a name="clean-up-resources"></a>Limpar recursos
Limpe os recursos criados no Guia de início rápido de duas maneiras. Você pode excluir o [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md), que inclui todos os recursos no grupo de recursos. Se desejar manter os outros recursos intactos, exclua apenas o data factory que você criou neste tutorial.

Execute o comando a seguir para excluir o grupo de recursos inteiro:
```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

Execute o comando a seguir para excluir somente o data factory:

```powershell
Remove-AzDataFactoryV2 -Name "<NameOfYourDataFactory>" -ResourceGroupName "<NameOfResourceGroup>"
```

## <a name="next-steps"></a>Próximas etapas
O pipeline nessa amostra copia dados de uma localização para outra em um Armazenamento de Blobs do Azure. Percorra os [tutoriais](tutorial-copy-data-dot-net.md) para saber mais sobre o uso do Data Factory em mais cenários.
