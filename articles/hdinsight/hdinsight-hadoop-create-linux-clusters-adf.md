---
title: "Criar clusters Hadoop sob demanda usando o Data Factory – Azure HDInsight | Microsoft Docs"
description: Saiba como criar clusters Hadoop sob demanda usando o Azure Data Factory.
services: hdinsight
documentationcenter: 
tags: azure-portal
author: spelluru
manager: jhubbard
editor: cgronlun
ms.assetid: 1f3b3a78-4d16-4d99-ba6e-06f7bb185d6a
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/20/2017
ms.author: spelluru
ms.openlocfilehash: c1061811d205494969047fa3f91cbf449a25d8ab
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-on-demand-hadoop-clusters-in-hdinsight-using-azure-data-factory"></a>Criar clusters Hadoop sob demanda usando o Azure Data Factory
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

[Azure Data Factory](../data-factory/introduction.md) é um serviço de integração de dados baseado em nuvem que orquestra e automatiza a movimentação e a transformação dos dados. Ele pode criar um cluster HDInsight Hadoop just-in-time para processar uma fatia de entrada de dados e excluir o cluster quando o processamento for concluído. Estes são alguns dos benefícios do uso de um cluster HDInsight Hadoop sob demanda:

- Você só paga pelo tempo em que o trabalho está em execução no cluster HDInsight Hadoop (além de um curto período ocioso configurável). A cobrança dos clusters do HDInsight será proporcional por minuto, independentemente de eles estarem sendo usados ou não. Quando você usa um serviço vinculado do HDInsight sob demanda no Data Factory, os clusters são criados sob demanda. E os clusters são excluídos automaticamente após a conclusão dos trabalhos. Portanto, você paga apenas pelo tempo de execução do trabalho e pelo tempo ocioso breve (configuração de vida útil).
- Você pode criar um fluxo de trabalho usando um pipeline do Data Factory. Por exemplo, você pode ter o pipeline para copiar dados de um SQL Server local para um armazenamento de blobs do Azure, processar os dados executando um script Hive e um script Pig em um cluster Hadoop do HDInsight sob demanda. Em seguida, copie os dados de resultado para um Azure SQL Data Warehouse para que aplicativos de BI o consumam.
- Você pode agendar o fluxo de trabalho para ser executado periodicamente (por hora, diariamente, semanalmente, mensalmente etc.).

No Azure Data Factory, um data factory pode ter um ou mais pipelines de dados. Um pipeline de dados tem uma ou mais atividades. Há dois tipos de atividades: [Atividades de Movimentação de Dados](../data-factory/copy-activity-overview.md) e [Atividades de Transformação de Dados](../data-factory/transform-data.md). Você pode usar as atividades de movimentação de dados (atualmente, apenas Copiar Atividade) para mover dados de um repositório de dados de origem para um repositório de dados de destino. Você pode usar as atividades de transformação de dados para transformar/processar dados. A atividade do HDInsight Hive é uma das atividades de transformação com suporte pelo Data Factory. Você pode usar a atividade de transformação do Hive neste tutorial.

Você pode configurar uma atividade hive para usar seu próprio cluster HDInsight Hadoop ou um cluster HDInsight Hadoop sob demanda. Neste tutorial, a atividade do Hive no pipeline do data factory é configurada para usar um cluster HDInsight sob demanda. Portanto, quando a atividade é executada para processar uma fatia de dados, aqui está o que acontece:

1. Um cluster Hadoop do HDInsight é criado automaticamente para você just-in-time para processar a fatia.  
2. Os dados de entrada são processados executando um script HiveQL no cluster.
3. O cluster HDInsight Hadoop é excluído depois que o processamento é concluído e o cluster está ocioso pelo período de tempo configurado (configuração timeToLive). Se a próxima fatia de dados estiver disponível para processamento nesse tempo ocioso timeToLive, o mesmo cluster será usado para processar a fatia.  

Neste tutorial, o script HiveQL associado à atividade hive executa as seguintes ações:

1. Cria uma tabela externa que faz referência a dados de log da Web brutos armazenados em um armazenamento de Blobs do Azure.
2. Particiona os dados brutos por ano e mês.
3. Armazena os dados particionados no armazenamento de blobs do Azure.

Neste tutorial, o script HiveQL associado à atividade hive cria uma tabela externa que faz referência a dados de log da Web brutos armazenados no Armazenamento de Blobs do Azure. Veja as linhas de exemplo para cada mês no arquivo de entrada.

```
2014-01-01,02:01:09,SAMPLEWEBSITE,GET,/blogposts/mvc4/step2.png,X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,53175,871
2014-02-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
2014-03-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
```

O script HiveQL particiona os dados brutos por ano e mês. Ele cria três pastas de saída com base na entrada anterior. Cada pasta contém um arquivo com entradas de cada mês.

```
adfgetstarted/partitioneddata/year=2014/month=1/000000_0
adfgetstarted/partitioneddata/year=2014/month=2/000000_0
adfgetstarted/partitioneddata/year=2014/month=3/000000_0
```

Para obter uma lista das atividades de transformação de dados do Data Factory, além da atividade do Hive, confira [Transformar e analisar usando o Azure Data Factory](../data-factory/transform-data.md).

> [!NOTE]
> No momento, você só pode criar o cluster HDInsight versão 3.2 do Azure Data Factory.

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar a seguir as instruções neste artigo, você deve ter o seguinte:

* [Assinatura do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* PowerShell do Azure.

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell.md)]

### <a name="prepare-storage-account"></a>Preparar a conta de armazenamento
Você pode usar até três contas de armazenamento neste cenário:

- conta de armazenamento padrão para o cluster HDInsight
- conta de armazenamento para os dados de entrada
- conta de armazenamento para os dados de saída

Para simplificar o tutorial, você usará uma conta de armazenamento para atender aos três objetivos. O exemplo de script do Azure PowerShell encontrado nesta seção executa as seguintes tarefas:

1. Fazer logon no Azure.
2. Crie um grupo de recursos do Azure.
3. Criar uma conta do Armazenamento do Azure.
4. Criar um contêiner de Blob na conta de armazenamento
5. Copiar os dois arquivos a seguir no contêiner de Blob:

   * Arquivo de dados de entrada: [https://hditutorialdata.blob.core.windows.net/adfhiveactivity/inputdata/input.log](https://hditutorialdata.blob.core.windows.net/adfhiveactivity/inputdata/input.log)
   * Script HiveQL: [https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql](https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql)

     Os dois arquivos são armazenados em um contêiner de Blob público.


**Para preparar o armazenamento e copiar os arquivos usando o Azure PowerShell:**
> [!IMPORTANT]
> Especifique nomes para o grupo de recursos do Azure e a conta de armazenamento do Azure que será criada pelo script.
> Anote o **nome do grupo de recursos**, o **nome da conta de armazenamento**, e a **chave da conta de armazenamento** produzidos pelo script. Você precisa deles na próxima seção.

```powershell
$resourceGroupName = "<Azure Resource Group Name>"
$storageAccountName = "<Azure Storage Account Name>"
$location = "East US 2"

$sourceStorageAccountName = "hditutorialdata"  
$sourceContainerName = "adfhiveactivity"

$destStorageAccountName = $storageAccountName
$destContainerName = "adfgetstarted" # don't change this value.

####################################
# Connect to Azure
####################################
#region - Connect to Azure subscription
Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
try{Get-AzureRmContext}
catch{Login-AzureRmAccount}
#endregion

####################################
# Create a resource group, storage, and container
####################################

#region - create Azure resources
Write-Host "`nCreating resource group, storage account and blob container ..." -ForegroundColor Green

New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
New-AzureRmStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $destStorageAccountName `
    -type Standard_LRS `
    -Location $location

$destStorageAccountKey = (Get-AzureRmStorageAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $destStorageAccountName)[0].Value

$sourceContext = New-AzureStorageContext `
    -StorageAccountName $sourceStorageAccountName `
    -Anonymous
$destContext = New-AzureStorageContext `
    -StorageAccountName $destStorageAccountName `
    -StorageAccountKey $destStorageAccountKey

New-AzureStorageContainer -Name $destContainerName -Context $destContext
#endregion

####################################
# Copy files
####################################
#region - copy files
Write-Host "`nCopying files ..." -ForegroundColor Green

$blobs = Get-AzureStorageBlob `
    -Context $sourceContext `
    -Container $sourceContainerName

$blobs|Start-AzureStorageBlobCopy `
    -DestContext $destContext `
    -DestContainer $destContainerName

Write-Host "`nCopied files ..." -ForegroundColor Green
Get-AzureStorageBlob -Context $destContext -Container $destContainerName
#endregion

Write-host "`nYou will use the following values:" -ForegroundColor Green
write-host "`nResource group name: $resourceGroupName"
Write-host "Storage Account Name: $destStorageAccountName"
write-host "Storage Account Key: $destStorageAccountKey"

Write-host "`nScript completed" -ForegroundColor Green
```

Se você precisar de ajuda com o script do PowerShell, confira [Como usar o Azure PowerShell com o Armazenamento do Azure](../storage/common/storage-powershell-guide-full.md). Se quiser usar a CLI do Azure em vez disso, confira a seção [Apêndice](#appendix) do script da CLI do Azure.

**Para examinar a conta de armazenamento e o conteúdo**

1. Entre no [portal do Azure](https://portal.azure.com).
2. Clique em **Grupos de recursos** no painel esquerdo.
3. Clique duas vezes no nome do grupo de recursos criado em seu script da CLI ou do PowerShell. Use o filtro se houver muitos grupos de recursos listados.
4. No bloco **Recursos** , você deverá ter um recurso listado, a menos que compartilhe o grupo de recursos com outros projetos. Esse recurso é a conta de armazenamento com o nome especificado anteriormente. Clique no nome da conta de armazenamento.
5. Clique nos blocos **Blobs** .
6. Clique no contêiner **adfgetstarted** . Você verá duas pastas: **inputdata** e **script**.
7. Abra a pasta e verifique os arquivos nas pastas. O inputdata contém o arquivo input.log com dados de entrada, e a pasta de script contém o arquivo de script HiveQL.

## <a name="create-a-data-factory-using-resource-manager-template"></a>Criar um data factory usando o modelo do Resource Manager
Com a conta de armazenamento, os dados de entrada e o script HiveQL preparados, você está pronto para criar um data factory do Azure. Há vários métodos para criar um data factory. Neste tutorial, você cria um data factory implantando um modelo do Azure Resource Manager usando o portal do Azure. Você também pode implantar um modelo do Resource Manager usando a [CLI do Azure](../azure-resource-manager/resource-group-template-deploy-cli.md) e o [Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md#deploy-local-template). Para conferir outros métodos de criação de data factory, consulte [Tutorial: compilar seu primeiro data factory](../data-factory/quickstart-create-data-factory-dot-net.md).

1. Clique na imagem a seguir para entrar no Azure e abra o modelo do Resource Manager no Portal do Azure. O modelo está localizado em https://hditutorialdata.blob.core.windows.net/adfhiveactivity/data-factory-hdinsight-on-demand.json. Confira a seção [Entidades de Data Factory no modelo](#data-factory-entities-in-the-template) para obter informações detalhadas sobre as entidades definidas no modelo. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fadfhiveactivity%2Fdata-factory-hdinsight-on-demand.json" target="_blank"><img src="./media/hdinsight-hadoop-create-linux-clusters-adf/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. Selecione a opção **Usar existente** para a configuração **Grupo de recursos** e selecione o nome do grupo de recursos que você criou na etapa anterior (usando o script do PowerShell).
3. Insira um nome para o data factory (**Nome do Data Factory**). Esse nome deve ser globalmente exclusivo.
4. Insira o **nome da conta de armazenamento** e a **chave da conta de armazenamento** que você anotou na etapa anterior.
5. Selecione **Concordo com os termos e condições** indicado acima, após a leitura dos **termos e condições**.
6. Selecione a opção **Fixar no painel**.
6. Clique em **Comprar/Criar**. Você verá um bloco no Painel chamado **Implantação do Modelo de implantação**. Aguarde até que a folha de **Grupo de recursos** para seu grupo de recursos seja aberta. Você também pode clicar no bloco intitulado como o nome do grupo de recursos para abrir a folha de grupo de recursos.
6. Clique no bloco para abrir o grupo de recursos, se a folha de grupo de recursos ainda não estiver aberta. Agora, você verá um recurso do data factory listado, além do recurso da conta de armazenamento.
7. Clique no nome do data factory (valor especificado para o parâmetro **Nome do Data Factory**).
8. Na folha Data Factory, clique no bloco **Diagrama**. O diagrama mostra uma atividade com um conjunto de dados de entrada, e um conjunto de dados de saída:

    ![Diagrama de pipeline de atividade do hive HDInsight sob demanda do Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-adf-pipeline-diagram.png)

    Os nomes são definidos no modelo do Resource Manager.
9. Clique duas vezes em **AzureBlobOutput**.
10. Nas **Fatias atualizadas recentemente**, você deverá ver uma fatia. Se o status for **Em andamento**, aguarde até que ele mude para **Pronto**. Normalmente, a criação de um cluster HDInsight demora cerca de **20 minutos**.

### <a name="check-the-data-factory-output"></a>Verificar a saída do data factory

1. Use o mesmo procedimento da última sessão para verificar os contêineres do contêiner adfgetstarted. Há dois novos contêineres além de **adfgetsarted**:

   * Um contêiner com nome que segue o padrão: `adf<yourdatafactoryname>-linkedservicename-datetimestamp`. Esse contêiner é o contêiner padrão para o cluster HDInsight.
   * adfjobs: esse é o contêiner para os logs do trabalho do ADF.

     A saída do data factory é armazenada em **afgetstarted**, conforme configurado no modelo do Resource Manager.
2. Clique em **adfgetstarted**.
3. Clique duas vezes em **partitioneddata**. Você vê uma pasta **ano=2014** , porque todos os logs da Web têm a data de 2014.

    ![Saída do pipeline de atividade do hive HDInsight sob demanda do Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-adf-output-year.png)

    Se você analisar detalhadamente a lista, deverá ver três pastas para janeiro, fevereiro e março. E há um log para cada mês.

    ![Saída do pipeline de atividade do hive HDInsight sob demanda do Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-adf-output-month.png)

## <a name="data-factory-entities-in-the-template"></a>Entidades do Data Factory no modelo
Veja a aparência do modelo de nível superior do Resource Manager para um data factory:

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "parameters": { ...
    },
    "variables": { ...
    },
    "resources": [
        {
            "name": "[parameters('dataFactoryName')]",
            "apiVersion": "[variables('apiVersion')]",
            "type": "Microsoft.DataFactory/datafactories",
            "location": "westus",
            "resources": [
                { ... },
                { ... },
                { ... },
                { ... }
            ]
        }
    ]
}
```

### <a name="define-data-factory"></a>Definir Data Factory
Você pode definir um Data Factory no modelo do Resource Manager, conforme mostrado no exemplo a seguir:  

```json
"resources": [
{
    "name": "[parameters('dataFactoryName')]",
    "apiVersion": "[variables('apiVersion')]",
    "type": "Microsoft.DataFactory/datafactories",
    "location": "westus",
}
```
O dataFactoryName é o nome do data factory que você especifica ao implantar o modelo. Atualmente, o data factory só tem suporte nas regiões leste dos EUA, oeste dos EUA e Europa Setentrional.

### <a name="defining-entities-within-the-data-factory"></a>Definindo entidades no data factory
As seguintes entidades de Data Factory são definidas no modelo JSON:

* [Serviço vinculado de armazenamento do Azure](#azure-storage-linked-service)
* [Serviço vinculado do HDInsight sob demanda](#hdinsight-on-demand-linked-service)
* [Conjunto de dados de entrada de Blob do Azure](#azure-blob-input-dataset)
* [Conjunto de dados de saída do blob do Azure](#azure-blob-output-dataset)
* [Pipeline com a Atividade de cópia](#data-pipeline)

#### <a name="azure-storage-linked-service"></a>Serviço vinculado de armazenamento do Azure
O serviço vinculado ao Armazenamento do Azure vincula sua conta de armazenamento do Azure ao data factory. Neste tutorial, a mesma conta de armazenamento é usada como a conta de armazenamento do HDInsight padrão, o armazenamento de dados de entrada e o armazenamento de dados de saída. Portanto, você define somente um serviço vinculado do Armazenamento do Azure. Na definição de serviço vinculado, você pode especificar o nome e a chave da conta de armazenamento do Azure. Confira [Serviço vinculado de armazenamento do Azure](../data-factory/connector-azure-blob-storage.md) para obter detalhes sobre os elementos JSON para definir um serviço vinculado de armazenamento do Azure.

```json
{
    "name": "[variables('storageLinkedServiceName')]",
    "type": "linkedservices",
    "dependsOn": [ "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]" ],
    "apiVersion": "[variables('apiVersion')]",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]"
        }
    }
}
```
A **connectionString** usa os parâmetros storageAccountName e storageAccountKey. Você pode especificar valores para esses parâmetros durante a implantação do modelo.  

#### <a name="hdinsight-on-demand-linked-service"></a>Serviço vinculado do HDInsight sob demanda
Na definição do serviço HDInsight vinculado sob demanda, você deve especificar valores para parâmetros de configuração que são usados pelo serviço Data Factory para criar um cluster HDInsight Hadoop em tempo de execução. Confira o artigo [Serviços vinculados de computação](../data-factory/compute-linked-services.md#azure-hdinsight-on-demand-linked-service) para obter detalhes sobre as propriedades JSON usadas para definir um serviço vinculado do HDInsight sob demanda.  

```json

{
    "type": "linkedservices",
    "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
    "dependsOn": [
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedservices/', variables('storageLinkedServiceName'))]"
    ],
    "apiVersion": "[variables('apiVersion')]",
    "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
            "version": "3.5",
            "clusterSize": 1,
            "timeToLive": "00:05:00",
            "osType": "Linux",
            "sshUserName": "myuser",                            
            "sshPassword": "MyPassword!",
            "linkedServiceName": "[variables('storageLinkedServiceName')]"
        }
    }
}
```
Observe os seguintes pontos:

* O Data Factory cria um cluster HDInsight **baseado em Linux** para você.
* O cluster HDInsight Hadoop é criado na mesma região que a conta de armazenamento.
* Perceba a configuração *timeToLive* . O data factory exclui o cluster automaticamente após ele ficar ocioso por 30 minutos.
* O cluster HDInsight cria um **contêiner padrão** no armazenamento de blobs especificado no JSON (**nomeServiçoVinculado**). O HDInsight não exclui esse contêiner quando o cluster é excluído. Este comportamento ocorre por design. Com o serviço vinculado HDInsight sob demanda, um cluster HDInsight é criado sempre que uma fatia precisa ser processada, a menos que haja um cluster ativo existente (**timeToLive**), e é excluído quando o processamento é concluído.

Confira [Serviço vinculado do HDInsight sob demanda](../data-factory/compute-linked-services.md#azure-hdinsight-on-demand-linked-service) para obter detalhes.

> [!IMPORTANT]
> Quanto mais fatias forem processadas, você verá muitos contêineres no armazenamento de blobs do Azure. Se você não precisa deles para solução de problemas dos trabalhos, convém excluí-los para reduzir o custo de armazenamento. Os nomes desses contêineres seguem um padrão: "adf**nomeseudatafactory**-**nomeserviçovinculado**- carimbodatahora". Use ferramentas como o [Gerenciador de Armazenamento da Microsoft](http://storageexplorer.com/) para excluir contêineres do armazenamento de blobs do Azure.

#### <a name="azure-blob-input-dataset"></a>Conjunto de dados de entrada de Blob do Azure
Na definição de conjunto de dados de entrada, você especifica os nomes do contêiner de blob, da pasta e do arquivo que contém os dados de entrada. Confira [Propriedades de conjunto de dados de Blob do Azure](../data-factory/connector-azure-blob-storage.md) para obter detalhes sobre os propriedades JSON usadas para definir um conjunto de dados de Blob do Azure.

```json

{
    "type": "datasets",
    "name": "[variables('blobInputDatasetName')]",
    "dependsOn": [
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]"
    ],
    "apiVersion": "[variables('apiVersion')]",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "[variables('storageLinkedServiceName')]",
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

Observe as seguintes configurações específicas na definição de JSON:

```json
"fileName": "input.log",
"folderPath": "adfgetstarted/inputdata",
```

#### <a name="azure-blob-output-dataset"></a>Conjunto de dados de saída de Blob do Azure
Na definição de conjunto de dados de saída, especifique os nomes de contêiner de blob e a pasta que contém os dados de saída. Confira [Propriedades de conjunto de dados de Blob do Azure](../data-factory/connector-azure-blob-storage.md) para obter detalhes sobre os propriedades JSON usadas para definir um conjunto de dados de Blob do Azure.  

```json

{
    "type": "datasets",
    "name": "[variables('blobOutputDatasetName')]",
    "dependsOn": [
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]"
    ],
    "apiVersion": "[variables('apiVersion')]",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "[variables('storageLinkedServiceName')]",
        "typeProperties": {
            "folderPath": "adfgetstarted/partitioneddata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Month",
            "interval": 1,
            "style": "EndOfInterval"
        }
    }
}
```

FolderPath especifica o caminho para a pasta que contém os dados de saída:

```json
"folderPath": "adfgetstarted/partitioneddata",
```

A configuração [disponibilidade do conjunto de dados](../data-factory/concepts-datasets-linked-services.md) é a seguinte:

```json
"availability": {
    "frequency": "Month",
    "interval": 1,
    "style": "EndOfInterval"
},
```

No Azure Data Factory, a disponibilidade do conjunto de dados de saída conduz o pipeline. Neste exemplo, a fatia é produzida mensalmente no último dia do mês (EndOfInterval). 

#### <a name="data-pipeline"></a>Pipeline de dados
Você define um pipeline que transforma dados executando o script Hive em um cluster do Azure HDInsight sob demanda. Confira [JSON de Pipeline](../data-factory/concepts-pipelines-activities.md) para obter descrições dos elementos JSON usados para definir um pipeline neste exemplo.

```json
{
    "type": "datapipelines",
    "name": "[parameters('dataFactoryName')]",
    "dependsOn": [
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedServices/', variables('hdInsightOnDemandLinkedServiceName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/datasets/', variables('blobInputDatasetName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/datasets/', variables('blobOutputDatasetName'))]"
    ],
    "apiVersion": "[variables('apiVersion')]",
    "properties": {
        "description": "Azure Data Factory pipeline with an Hadoop Hive activity",
        "activities": [
            {
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                    "scriptLinkedService": "[variables('storageLinkedServiceName')]",
                    "defines": {
                        "inputtable": "[concat('wasb://adfgetstarted@', parameters('storageAccountName'), '.blob.core.windows.net/inputdata')]",
                        "partitionedtable": "[concat('wasb://adfgetstarted@', parameters('storageAccountName'), '.blob.core.windows.net/partitioneddata')]"
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
                "name": "RunSampleHiveActivity",
                "linkedServiceName": "HDInsightOnDemandLinkedService"
            }
        ],
        "start": "2016-01-01T00:00:00Z",
        "end": "2016-01-31T00:00:00Z",
        "isPaused": false
    }
}
```

O pipeline contém uma atividade, a atividade HDInsightHive. Como as datas de início e término são em janeiro de 2016, os dados de apenas um mês (uma fatia) são processados. O *início* e o *término* da atividade têm uma data passada; portanto, o Data Factory processa os dados para o mês imediatamente. Se o valor de fim for uma data futura, o data factory cria outra fatia quando chegar o momento. Para saber mais, confira [Agendamento e execução do Data Factory](../data-factory/v1/data-factory-scheduling-and-execution.md).

## <a name="clean-up-the-tutorial"></a>Limpar o tutorial

### <a name="delete-the-blob-containers-created-by-on-demand-hdinsight-cluster"></a>Exclua os contêineres de blob criados pelo cluster HDInsight sob demanda
Com o serviço vinculado HDInsight sob demanda, um cluster HDInsight é criado sempre que uma fatia precisa ser processada, a menos que haja um cluster ativo existente (timeToLive), e o cluster seja excluído após o processamento. Para cada cluster, o Azure Data Factory cria um contêiner de blob no armazenamento de blobs do Azure usado como a conta de armazenamento padrão para o cluster. Mesmo que o cluster HDInsight seja excluído, o contêiner de armazenamento de blobs padrão e a conta de armazenamento associados não serão excluídos. Este comportamento ocorre por design. Quanto mais fatias forem processadas, você verá muitos contêineres no armazenamento de blobs do Azure. Se você não precisa deles para solução de problemas dos trabalhos, convém excluí-los para reduzir o custo de armazenamento. Os nomes desses contêineres seguem um padrão: `adfyourdatafactoryname-linkedservicename-datetimestamp`.

Exclua as pastas **adfjobs** e **adfyourdatafactoryname-linkedservicename-datetimestamp**. O contêiner adfjobs contém logs de trabalho do Azure Data Factory.

### <a name="delete-the-resource-group"></a>Exclua o grupo de recursos
O [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) é usado para implantar, gerenciar e monitorar sua solução como um grupo.  A exclusão de um grupo de recursos exclui todos os componentes dentro do grupo.  

1. Entre no [portal do Azure](https://portal.azure.com).
2. Clique em **Grupos de recursos** no painel esquerdo.
3. Clique no nome do grupo de recursos criado em seu script do PowerShell. Use o filtro se houver muitos grupos de recursos listados. Ele abre o grupo de recursos em uma nova folha.
4. No bloco **Recursos** , a conta de armazenamento padrão e o data factory deverão estar listados, a menos que você compartilhe o grupo de recursos com outros projetos.
5. Clique em **Excluir** na parte superior da folha. Isso exclui a conta de armazenamento e os dados armazenados na conta de armazenamento.
6. Insira o nome do grupo de recursos para confirmar a exclusão e clique em **Excluir**.

Caso você não queira excluir a conta de armazenamento ao excluir o grupo de recursos, considere a seguinte arquitetura, separando os dados de negócio da conta de armazenamento padrão. Nesse caso, você tem um grupo de recursos para a conta de armazenamento com os dados de negócio, e outro grupo de recursos para a conta de armazenamento padrão para o serviço vinculado do HDInsight e o data factory. Quando você exclui o segundo grupo de recursos, ele não afeta a conta de armazenamento de dados de negócios. Para fazer isso:

* Adicione o seguinte ao grupo de recursos de nível superior, juntamente com o recurso Microsoft.DataFactory/datafactories em seu modelo do Resource Manager. Ele cria uma conta de armazenamento:

    ```json
    {
        "name": "[parameters('defaultStorageAccountName')]",
        "type": "Microsoft.Storage/storageAccounts",
        "location": "[parameters('location')]",
        "apiVersion": "[variables('defaultApiVersion')]",
        "dependsOn": [ ],
        "tags": {

        },
        "properties": {
            "accountType": "Standard_LRS"
        }
    },
    ```
* Adicione um novo ponto de serviço vinculado à nova conta de armazenamento:

    ```json
    {
        "dependsOn": [ "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]" ],
        "type": "linkedservices",
        "name": "[variables('defaultStorageLinkedServiceName')]",
        "apiVersion": "[variables('apiVersion')]",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('defaultStorageAccountName'),';AccountKey=',listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('defaultStorageAccountName')), variables('defaultApiVersion')).key1)]"
            }
        }
    },
    ```
* Configure o serviço vinculado sob demanda do HDInsight com um dependsOn adicional, e um additionalLinkedServiceNames:

    ```json
    {
        "dependsOn": [
            "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]",
            "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedservices/', variables('defaultStorageLinkedServiceName'))]",
            "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedservices/', variables('storageLinkedServiceName'))]"

        ],
        "type": "linkedservices",
        "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
        "apiVersion": "[variables('apiVersion')]",
        "properties": {
            "type": "HDInsightOnDemand",
            "typeProperties": {
                "version": "3.5",
                "clusterSize": 1,
                "timeToLive": "00:05:00",
                "osType": "Linux",
                "sshUserName": "myuser",                            
                "sshPassword": "MyPassword!",
                "linkedServiceName": "[variables('storageLinkedServiceName')]",
                "additionalLinkedServiceNames": "[variables('defaultStorageLinkedServiceName')]"
            }
        }
    },            
    ```
## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu a usar o Azure Data Factory para criar o cluster HDInsight sob demanda, a fim de processar trabalhos de Hive. Para saber mais:

* [Tutorial do Hadoop: introdução ao uso do Hadoop baseado em Linux no HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md)
* [Criar clusters Hadoop baseados em Linux em HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
* [Documentação do HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/)
* [Documentação do data factory](https://azure.microsoft.com/documentation/services/data-factory/)

## <a name="appendix"></a>Apêndice

### <a name="azure-cli-script"></a>Script da CLI do Azure
Você pode usar a CLI do Azure, em vez de usar o Azure PowerShell, para executar o tutorial. Para usar a CLI do Azure, primeiro instale a CLI do Azure de acordo com as seguintes instruções:

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

#### <a name="use-azure-cli-to-prepare-the-storage-and-copy-the-files"></a>Usar a CLI do Azure para preparar o armazenamento e copiar os arquivos

```
azure login

azure config mode arm

azure group create --name "<Azure Resource Group Name>" --location "East US 2"

azure storage account create --resource-group "<Azure Resource Group Name>" --location "East US 2" --type "LRS" <Azure Storage Account Name>

azure storage account keys list --resource-group "<Azure Resource Group Name>" "<Azure Storage Account Name>"
azure storage container create "adfgetstarted" --account-name "<Azure Storage AccountName>" --account-key "<Azure Storage Account Key>"

azure storage blob copy start "https://hditutorialdata.blob.core.windows.net/adfhiveactivity/inputdata/input.log" --dest-account-name "<Azure Storage Account Name>" --dest-account-key "<Azure Storage Account Key>" --dest-container "adfgetstarted"
azure storage blob copy start "https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql" --dest-account-name "<Azure Storage Account Name>" --dest-account-key "<Azure Storage Account Key>" --dest-container "adfgetstarted"
```

O nome do contêiner é *adfgetstarted*. Mantenha-o como está. Caso contrário, é necessário atualizar o modelo do Resource Manager. Se você precisar de ajuda com esse script da CLI, confira [Como usar a CLI do Azure com o Armazenamento do Azure](../storage/common/storage-azure-cli.md).
