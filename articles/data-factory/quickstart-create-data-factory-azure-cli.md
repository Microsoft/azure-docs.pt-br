---
title: 'Início Rápido: Criar um Azure Data Factory usando a CLI do Azure'
description: Este início rápido cria um Azure Data Factory, incluindo um serviço vinculado, conjuntos de dados e um pipeline. Execute o pipeline para realizar uma ação de cópia de arquivo.
author: linda33wj
ms.author: jingwang
ms.service: azure-cli
ms.topic: quickstart
ms.date: 03/24/2021
ms.custom:
- template-quickstart
- devx-track-azurecli
ms.openlocfilehash: 9af5f276e49e9eb2756dc544db353c75c99bc5a9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105937942"
---
# <a name="quickstart-create-an-azure-data-factory-using-azure-cli"></a>Início Rápido: Criar um Azure Data Factory usando a CLI do Azure

Este início rápido descreve como usar a CLI do Azure para criar um Azure Data Factory. O pipeline que você cria nesse data factory copia dados de uma pasta para outra em um Armazenamento de Blobs do Azure. Para obter informações sobre como transformar dados usando o Azure Data Factory, confira [Transformar dados no Azure data Factory](transform-data.md).

Para obter uma introdução do serviço do Azure Data Factory, consulte [Introdução ao Azure Data Factory](introduction.md).

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

[!INCLUDE [azure-cli-prepare-your-environment](../../includes/azure-cli-prepare-your-environment.md)]

> [!NOTE]
> Para criar instâncias de Data Factory, a conta de usuário usada para entrar no Azure deve ser um membro das funções colaborador ou proprietário, ou um administrador da assinatura do Azure. Para obter mais informações, confira [funções do Azure](quickstart-create-data-factory-powershell.md#azure-roles).

## <a name="prepare-a-container-and-test-file"></a>Preparar um contêiner e um arquivo de teste

Este início rápido usa uma conta de Armazenamento do Azure, que inclui um contêiner com um arquivo.

1. Para criar um grupo de recursos chamado `ADFQuickStartRG`, use o comando [az group create](/cli/azure/group#az_group_create):

   ```azurecli
   az group create --name ADFQuickStartRG --location eastus
   ```

1. Crie uma conta de armazenamento usando o comando [az storage account create](/cli/azure/storage/container#az_storage_container_create):

   ```azurecli
   az storage account create --resource-group ADFQuickStartRG \
       --name adfquickstartstorage --location eastus
   ```

1. Crie um contêiner chamado `adftutorial` usando o comando [az storage container create](/cli/azure/storage/container#az_storage_container_create):

   ```azurecli
   az storage container create --resource-group ADFQuickStartRG --name adftutorial \
       --account-name adfquickstartstorage --auth-mode key
   ```

1. No diretório local, crie um arquivo chamado `emp.txt` para ser carregado. Se estiver trabalhando no Azure Cloud Shell, você poderá encontrar o diretório de trabalho atual usando o comando de Bash `echo $PWD`. Você pode usar comandos de Bash padrão, como `cat`, para criar um arquivo:

   ```console
   cat > emp.txt
   This is text.
   ```

   Use **Ctrl + D** para salvar o novo arquivo.

1. Para carregar o novo arquivo em seu contêiner de armazenamento do Azure, use o comando [az storage blob upload](/cli/azure/storage/blob#az_storage_blob_upload):

   ```azurecli
   az storage blob upload --account-name adfquickstartstorage --name input/emp.txt \
       --container-name adftutorial --file emp.txt --auth-mode key
   ```

   Esse comando carrega em uma nova pasta chamada `input`.

## <a name="create-a-data-factory"></a>Criar uma data factory

Para criar um data factory do Azure, execute o comando [az datafactory factory create](/cli/azure/ext/datafactory/datafactory/factory#ext_datafactory_az_datafactory_factory_create):

```azurecli
az datafactory factory create --resource-group ADFQuickStartRG \
    --factory-name ADFTutorialFactory
```

> [!IMPORTANT]
> Substitua `ADFTutorialFactory` por um nome de data factory globalmente exclusivo, por exemplo, ADFTutorialFactorySP1127.

Você pode ver o data factory criado usando o comando [az datafactory factory show](/cli/azure/ext/datafactory/datafactory/factory#ext_datafactory_az_datafactory_factory_show):

```azurecli
az datafactory factory show --resource-group ADFQuickStartRG \
    --factory-name ADFTutorialFactory
```

## <a name="create-a-linked-service-and-datasets"></a>Criar um serviço vinculado e conjuntos de dados

Em seguida, crie um serviço vinculado e dois conjuntos de dados.

1. Obtenha a cadeia de conexão da conta de armazenamento usando o comando [az storage account show-connection-string](/cli/azure/ext/datafactory/datafactory/factory#ext_datafactory_az_datafactory_factory_show):

   ```azurecli
   az storage account show-connection-string --resource-group ADFQuickStartRG \
       --name adfquickstartstorage --key primary
   ```

1. No diretório de trabalho, crie um arquivo JSON com esse conteúdo, que inclui sua cadeia de conexão da etapa anterior. Dê ao arquivo o nome `AzureStorageLinkedService.json`:

   ```json
   {
       "type":"AzureStorage",
           "typeProperties":{
           "connectionString":{
           "type": "SecureString",
           "value":"DefaultEndpointsProtocol=https;AccountName=adfquickstartstorage;AccountKey=K9F4Xk/EhYrMBIR98rtgJ0HRSIDU4eWQILLh2iXo05Xnr145+syIKNczQfORkQ3QIOZAd/eSDsvED19dAwW/tw==;EndpointSuffix=core.windows.net"
           }
       }
   }
   ```

1. Crie um serviço vinculado chamado `AzureStorageLinkedService` usando o comando [az datafactory linked-service create](/cli/azure/ext/datafactory/datafactory/linked-service#ext_datafactory_az_datafactory_linked_service_create):

   ```azurecli
   az datafactory linked-service create --resource-group ADFQuickStartRG \
       --factory-name ADFTutorialFactory --linked-service-name AzureStorageLinkedService \
       --properties @AzureStorageLinkedService.json
   ```

1. No diretório de trabalho, crie um arquivo JSON com esse conteúdo, chamado `InputDataset.json`:

   ```json
   {
       "type": 
           "AzureBlob",
           "linkedServiceName": {
               "type":"LinkedServiceReference",
               "referenceName":"AzureStorageLinkedService"
               },
           "annotations": [],
           "type": "Binary",
           "typeProperties": {
               "location": {
                   "type": "AzureBlobStorageLocation",
                   "fileName": "emp.txt",
                   "folderPath": "input",
                   "container": "adftutorial"
           }
       }
   }
   ```

1. Crie um conjunto de dados de entrada chamado `InputDataset` usando o comando [az datafactory dataset create](/cli/azure/ext/datafactory/datafactory/dataset#ext_datafactory_az_datafactory_dataset_create):

   ```azurecli
   az datafactory dataset create --resource-group ADFQuickStartRG \
       --dataset-name InputDataset --factory-name ADFQuickStartFactory \
       --properties @InputDataset.json
   ```

1. No diretório de trabalho, crie um arquivo JSON com esse conteúdo, chamado `OutputDataset.json`:

   ```json
   {
       "type": 
           "AzureBlob",
           "linkedServiceName": {
               "type":"LinkedServiceReference",
               "referenceName":"AzureStorageLinkedService"
               },
           "annotations": [],
           "type": "Binary",
           "typeProperties": {
               "location": {
                   "type": "AzureBlobStorageLocation",
                   "fileName": "emp.txt",
                   "folderPath": "output",
                   "container": "adftutorial"
           }
       }
   }
   ```

1. Crie um conjunto de dados de saída chamado `OutputDataset` usando o comando [az datafactory dataset create](/cli/azure/ext/datafactory/datafactory/dataset#ext_datafactory_az_datafactory_dataset_create):

   ```azurecli
   az datafactory dataset create --resource-group ADFQuickStartRG \
       --dataset-name OutputDataset --factory-name ADFQuickStartFactory \
       --properties @OutputDataset.json
   ```

## <a name="create-and-run-the-pipeline"></a>Criar e executar o pipeline

Por fim, crie e execute o pipeline.

1. No diretório de trabalho, crie um arquivo JSON com esse conteúdo chamado `Adfv2QuickStartPipeline.json`:

   ```json
   {
       "name": "Adfv2QuickStartPipeline",
       "properties": {
           "activities": [
               {
                   "name": "CopyFromBlobToBlob",
                   "type": "Copy",
                   "dependsOn": [],
                   "policy": {
                       "timeout": "7.00:00:00",
                       "retry": 0,
                       "retryIntervalInSeconds": 30,
                       "secureOutput": false,
                       "secureInput": false
                   },
                   "userProperties": [],
                   "typeProperties": {
                          "source": {
                           "type": "BinarySource",
                           "storeSettings": {
                               "type": "AzureBlobStorageReadSettings",
                               "recursive": true
                           }
                       },
                       "sink": {
                           "type": "BinarySink",
                           "storeSettings": {
                               "type": "AzureBlobStorageWriteSettings"
                           }
                       },
                       "enableStaging": false
                   },
                   "inputs": [
                       {
                           "referenceName": "InputDataset",
                           "type": "DatasetReference"
                       }
                   ],
                   "outputs": [
                       {
                           "referenceName": "OutputDataset",
                           "type": "DatasetReference"
                       }
                   ]
               }
           ],
           "annotations": []
       }
   }
   ```

1. Crie um pipeline chamado `Adfv2QuickStartPipeline` usando o comando [az datafactory pipeline create](/cli/azure/ext/datafactory/datafactory/pipeline#ext_datafactory_az_datafactory_pipeline_create):

   ```azurecli
   az datafactory pipeline create --resource-group ADFQuickStartRG \
       --factory-name ADFTutorialFactory --name Adfv2QuickStartPipeline \
       --pipeline @Adfv2QuickStartPipeline.json
   ```

1. Execute o pipeline usando o comando [az datafactory pipeline create-run](/cli/azure/ext/datafactory/datafactory/pipeline#ext_datafactory_az_datafactory_pipeline_create_run):

   ```azurecli
   az datafactory pipeline create-run --resource-group ADFQuickStartRG \
       --name Adfv2QuickStartPipeline --factory-name ADFTutorialFactory
   ```

   Esse comando retorna uma ID de execução. Copie-a para uso no próximo comando.

1. Verifique se a execução de pipeline foi bem-sucedida usando o comando [az datafactory pipeline-run show](/cli/azure/ext/datafactory/datafactory/pipeline-run#ext_datafactory_az_datafactory_pipeline_run_show):

   ```azurecli
   az datafactory pipeline-run show --resource-group ADFQuickStartRG \
       --factory-name ADFTutorialFactory --run-id 00000000-0000-0000-0000-000000000000
   ```

Você também pode verificar se o pipeline foi executado conforme o esperado usando o [portal do Azure](https://portal.azure.com/). Para obter mais informações, confira [Examinar recursos implantados](quickstart-create-data-factory-powershell.md#review-deployed-resources).

## <a name="clean-up-resources"></a>Limpar recursos

Todos os recursos neste início rápido fazem parte do mesmo grupo de recursos. Para remover todos eles, use o comando [az group delete](/cli/azure/group#az_group_delete):

```azurecli
az group delete --name ADFQuickStartRG
```

Se estiver usando esse grupo de recursos para alguma outra coisa, exclua os recursos individuais. Por exemplo, para remover o serviço vinculado, use o comando [az datafactory linked-service delete](/cli/azure/ext/datafactory/datafactory/linked-service#ext_datafactory_az_datafactory_linked_service_delete).

Neste início rápido, você criou os seguintes arquivos JSON:

- AzureStorageLinkedService.json
- InputDataset.json
- OutputDataset.json
- Adfv2QuickStartPipeline.json

Exclua-os usando comandos de Bash padrão.

## <a name="next-steps"></a>Próximas etapas

- [Pipelines e atividades no Azure Data Factory](concepts-pipelines-activities.md)
- [Serviços vinculados no Azure Data Factory](concepts-linked-services.md)
- [Conjuntos de dados no Azure Data Factory](concepts-datasets-linked-services.md)
- [Transformar dados no Azure Data Factory](transform-data.md)
