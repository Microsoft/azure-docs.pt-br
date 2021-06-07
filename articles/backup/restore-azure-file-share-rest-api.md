---
title: Restaurar compartilhamentos de arquivos do Azure com a API REST
description: Saiba como usar a API REST para restaurar compartilhamentos de arquivos do Azure ou arquivos específicos de um ponto de restauração criado pelo backup do Azure
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 60c73caa5db684e38b94b4d5786f2fd24aa65d08
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "88761790"
---
# <a name="restore-azure-file-shares-using-rest-api"></a>Restaurar compartilhamentos de arquivos do Azure usando a API REST

Este artigo explica como restaurar um compartilhamento de arquivos inteiro ou arquivos específicos de um ponto de restauração criado pelo [backup do Azure](./backup-overview.md) usando a API REST.

Ao final deste artigo, você aprenderá a executar as seguintes operações usando a API REST:

* Exibir pontos de restauração para um compartilhamento de arquivos do Azure de backup.
* Restaure um compartilhamento de arquivos completo do Azure.
* Restaurar arquivos ou pastas individuais.

## <a name="prerequisites"></a>Pré-requisitos

Supomos que você já tem um compartilhamento de arquivos com backup que deseja restaurar. Se você não fizer isso, marque [backup de compartilhamento de arquivos do Azure usando a API REST](backup-azure-file-share-rest-api.md) para saber como criar um.

Para este artigo, usaremos os seguintes recursos:

* **RecoveryServicesVault**: *azurefilesvault*
* **Grupo de recursos**: *azurefiles*
* **Conta de armazenamento**: *afsaccount*
* **Compartilhamento de arquivos**: *azurefiles*

## <a name="fetch-containername-and-protecteditemname"></a>Buscar ContainerName e ProtectedItemName

Para a maioria das chamadas de API relacionadas à restauração, você precisa passar valores para os parâmetros de URI {ContainerName} e {protectedItemName}. Use o atributo ID no corpo da resposta da operação [Get backupprotectableitems](/rest/api/backup/protecteditems/get) para recuperar valores para esses parâmetros. Em nosso exemplo, a ID do compartilhamento de arquivos que desejamos proteger é:

`"/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;afsaccount/protectableItems/azurefileshare;azurefiles`

Portanto, os valores são convertidos da seguinte maneira:

* {ContainerName}- *storagecontainer; armazenamento; azurefiles; afsaccount*
* {protectedItemName}- *azurefileshare; azurefiles*

## <a name="fetch-recovery-points-for-backed-up-azure-file-share"></a>Buscar pontos de recuperação para backup do compartilhamento de arquivos do Azure

Para restaurar qualquer arquivo ou compartilhamento de arquivos de backup, primeiro selecione um ponto de recuperação para executar a operação de restauração. Os pontos de recuperação disponíveis de um item de backup podem ser listados usando a chamada à API REST de [lista de pontos de recuperação](/rest/api/site-recovery/recoverypoints/listbyreplicationprotecteditems) . É uma operação GET com todos os valores relevantes.

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints?api-version=2019-05-13&$filter={$filter}
```

Defina os valores de URI da seguinte maneira:

* {fabricname}: *Azure*
* {vaultname}: *azurefilesvault*
* {ContainerName}: *storagecontainer; armazenamento; azurefiles; afsaccount*
* {protectedItemName}: *azurefileshare; azurefiles*
* {ResourceGroupName}: *azurefiles*

O URI GET tem todos os parâmetros necessários. Não há necessidade de um corpo de solicitação adicional.

```http
GET https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare;azurefiles/recoveryPoints?api-version=2019-05-13
```

### <a name="example-response-for-fetch-recovery-points"></a>Exemplo de resposta para buscar pontos de recuperação

Depois que o URI de obtenção é enviado, uma resposta 200 é retornada:

```http
HTTP/1.1" 200 None
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Transfer-Encoding': 'chunked'
'Content-Type': 'application/json'
'Content-Encoding': 'gzip'
'Expires': '-1'
'Vary': 'Accept-Encoding'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': 'd68d7951-7d97-4c49-9a2d-7fbaab55233a'
'x-ms-client-request-id': '4edb5a58-47ea-11ea-a27a-0a580af41908, 4edb5a58-47ea-11ea-a27a-0a580af41908'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'Server': 'Microsoft-IIS/10.0'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-reads': '11998'
'x-ms-correlation-request-id': 'd68d7951-7d97-4c49-9a2d-7fbaab55233a'
'x-ms-routing-request-id': 'WESTEUROPE:20200205T073708Z:d68d7951-7d97-4c49-9a2d-7fbaab55233a'
'Date': 'Wed, 05 Feb 2020 07:37:08 GMT'
{
“value”:[
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare;azurefiles/recoveryPoints/932881138555802864",
    "location": null,
    "name": "932881138555802864",
    "properties": {
      "fileShareSnapshotUri": "https://afsaccount.file.core.windows.net/azurefiles?sharesnapshot=2020-02-04T08:01:35.0000000Z",
      "objectType": "AzureFileShareRecoveryPoint",
      "recoveryPointSizeInGb": 1,
      "recoveryPointTime": "2020-02-04T08:01:35+00:00",
      "recoveryPointType": "FileSystemConsistent"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints"
  },
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare;azurefiles/recoveryPoints/932878582606969225",
    "location": null,
    "name": "932878582606969225",
    "properties": {
      "fileShareSnapshotUri": "https://afsaccount.file.core.windows.net/azurefiles?sharesnapshot=2020-02-03T08:05:30.0000000Z",
      "objectType": "AzureFileShareRecoveryPoint",
      "recoveryPointSizeInGb": 1,
      "recoveryPointTime": "2020-02-03T08:05:30+00:00",
      "recoveryPointType": "FileSystemConsistent"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints"
  },
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare;azurefiles/recoveryPoints/932890167574511261",
    "location": null,
    "name": "932890167574511261",
    "properties": {
      "fileShareSnapshotUri": "https://afsaccount.file.core.windows.net/azurefiles?sharesnapshot=2020-02-02T08:03:50.0000000Z",
      "objectType": "AzureFileShareRecoveryPoint",
      "recoveryPointSizeInGb": 1,
      "recoveryPointTime": "2020-02-02T08:03:50+00:00",
      "recoveryPointType": "FileSystemConsistent"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints"
  },
```

O ponto de recuperação é identificado com o campo {Name} na resposta acima.

## <a name="full-share-recovery-using-rest-api"></a>Recuperação de compartilhamento completa usando a API REST

Use essa opção de restauração para restaurar o compartilhamento de arquivos completo no local original ou alternativo.
Disparar a restauração é uma solicitação POST e você pode executar essa operação usando a API REST de [restauração do gatilho](/rest/api/backup/restores/trigger) .

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints/{recoveryPointId}/restore?api-version=2019-05-13
```

Os valores {ContainerName} e {protectedItemName} estão definidos [aqui](#fetch-containername-and-protecteditemname) e recoveryPointID é o campo {Name} do ponto de recuperação mencionado acima.

```http
POST https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare%3Bazurefiles/recoveryPoints/932886657837421071/restore?api-version=2019-05-13'
```

### <a name="create-request-body"></a>Criar o corpo da solicitação

Para disparar uma restauração para um compartilhamento de arquivos do Azure, veja a seguir os componentes do corpo da solicitação:

Nome |  Tipo   |   Descrição
--- | ---- | ----
Propriedades | AzureFileShareRestoreRequest | Propriedades de RestoreRequestResource

Para obter a lista completa de definições do corpo da solicitação e outros detalhes, consulte o [documento disparar a API REST de restauração](/rest/api/backup/restores/trigger#request-body).

### <a name="restore-to-original-location"></a>Restaurar no local original

#### <a name="request-body-example-for-restore-to-original-location"></a>Exemplo de corpo de solicitação para restaurar no local original

O corpo da solicitação a seguir define as propriedades necessárias para disparar uma restauração de compartilhamento de arquivos do Azure:

```json
{
   "properties":{
      "objectType":"AzureFileShareRestoreRequest",
      "recoveryType":"OriginalLocation",
      "sourceResourceId":"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afsaccount",
      "copyOptions":"Overwrite",
      "restoreRequestType":"FullShareRestore"
}
}
```

### <a name="restore-to-alternate-location"></a>Restauração em uma localização alternativa

Especifique os seguintes parâmetros para recuperação de local alternativo:

* **targetResourceId**: a conta de armazenamento para a qual o conteúdo de backup é restaurado. A conta de armazenamento de destino deve estar no mesmo local que o cofre.
* **nome**: o compartilhamento de arquivos na conta de armazenamento de destino para a qual o conteúdo de backup é restaurado.
* **targetFolderPath**: a pasta sob o compartilhamento de arquivos para o qual os dados são restaurados.

#### <a name="request-body-example-for-restore-to-alternate-location"></a>Exemplo de corpo de solicitação para restauração em local alternativo

O corpo da solicitação a seguir restaura o compartilhamento de arquivos *azurefiles* na conta de armazenamento *afsaccount* para o compartilhamento de arquivos *azurefiles1* na conta de armazenamento *afaccount1* .

```json
{
   "properties":{
      "objectType":"AzureFileShareRestoreRequest",
      "recoveryType":"AlternateLocation",
      "sourceResourceId":"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afsaccount",
      "copyOptions":"Overwrite",
      "restoreRequestType":"FullShareRestore",
      "restoreFileSpecs":[
         {
            "targetFolderPath":"restoredata"
}
],
      "targetDetails":{
         "name":"azurefiles1",
         "targetResourceId":"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afaccount1"
}
}
}
```

### <a name="response"></a>Resposta

O disparo de uma operação de restauração é uma [operação assíncrona](../azure-resource-manager/management/async-operations.md). Esta operação cria outra operação que precisa ser controlada separadamente.
Ele retorna duas respostas: 202 (aceito) quando outra operação é criada e 200 (OK) quando a operação é concluída.

#### <a name="response-example"></a>Exemplo de resposta

Depois de enviar o URI de *postagem* para disparar uma restauração, a resposta inicial é 202 (aceito) com um cabeçalho de local ou Azure-Async-header.

```http
HTTP/1.1" 202
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Expires': '-1'
'Location': 'https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare;azurefiles/operationResults/68ccfbc1-a64f-4b29-b955-314b5790cfa9?api-version=2019-05-13'
'Retry-After': '60'
'Azure-AsyncOperation': 'https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare;azurefiles/operationsStatus/68ccfbc1-a64f-4b29-b955-314b5790cfa9?api-version=2019-05-13'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '2426777d-c5ec-44b6-a324-384f8947460c'
'x-ms-client-request-id': '3c743096-47eb-11ea-ae90-0a580af41908, 3c743096-47eb-11ea-ae90-0a580af41908'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-writes': '1198'
'x-ms-correlation-request-id': '2426777d-c5ec-44b6-a324-384f8947460c'
'x-ms-routing-request-id': 'WESTEUROPE:20200205T074347Z:2426777d-c5ec-44b6-a324-384f8947460c'
'Date': 'Wed, 05 Feb 2020 07:43:47 GMT'
```

Em seguida, acompanhe a operação resultante usando o cabeçalho Location ou o cabeçalho Azure-AsyncOperation com um comando GET.

```http
GET https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupOperations/68ccfbc1-a64f-4b29-b955-314b5790cfa9?api-version=2016-12-01
```

Depois que a operação for concluída, ela retornará 200 (OK) com o ID da tarefa de restauração resultante no corpo da resposta.

```http
HTTP/1.1" 200
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Transfer-Encoding': 'chunked'
'Content-Type': 'application/json'
'Content-Encoding': 'gzip'
'Expires': '-1'
'Vary': 'Accept-Encoding'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '41ee89b2-3be4-40d8-8ff6-f5592c2571e3'
'x-ms-client-request-id': '3c743096-47eb-11ea-ae90-0a580af41908, 3c743096-47eb-11ea-ae90-0a580af41908'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'Server': 'Microsoft-IIS/10.0'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-reads': '11998'
'x-ms-correlation-request-id': '41ee89b2-3be4-40d8-8ff6-f5592c2571e3'
'x-ms-routing-request-id': 'WESTEUROPE:20200205T074348Z:41ee89b2-3be4-40d8-8ff6-f5592c2571e3'
'Date': 'Wed, 05 Feb 2020 07:43:47 GMT'
{
  "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/a7e97e42-4e54-4d4b-b449-26fcf946f42c",
  "location": null,
  "name": "a7e97e42-4e54-4d4b-b449-26fcf946f42c",
  "properties": {
    "actionsInfo": [
      "Cancellable"
    ],
    "activityId": "3c743096-47eb-11ea-ae90-0a580af41908",
    "backupManagementType": "AzureStorage",
    "duration": "0:00:01.863098",
    "endTime": null,
    "entityFriendlyName": "azurefiles",
    "errorDetails": null,
    "extendedInfo": {
      "dynamicErrorMessage": null,
      "propertyBag": {},
      "tasksList": []
    },
    "jobType": "AzureStorageJob",
    "operation": "Restore",
    "startTime": "2020-02-05T07:43:47.144961+00:00",
    "status": "InProgress",
    "storageAccountName": "afsaccount",
    "storageAccountVersion": "Storage"
  },
  "resourceGroup": "azurefiles",
  "tags": null,
  "type": "Microsoft.RecoveryServices/vaults/backupJobs"
}
```

Para recuperação de local alternativo, o corpo da resposta será assim:

```http
{
  "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/7e0ee41e-6e31-4728-a25c-98ff6b777641",
  "location": null,
  "name": "7e0ee41e-6e31-4728-a25c-98ff6b777641",
  "properties": {
    "actionsInfo": [
      "Cancellable"
    ],
    "activityId": "6077be6e-483a-11ea-a915-0a580af4ad72",
    "backupManagementType": "AzureStorage",
    "duration": "0:00:02.171965",
    "endTime": null,
    "entityFriendlyName": "azurefiles",
    "errorDetails": null,
    "extendedInfo": {
      "dynamicErrorMessage": null,
      "propertyBag": {
        "Data Transferred (in MB)": "0",
        "Job Type": "Recover to an alternate file share",
        "Number Of Failed Files": "0",
        "Number Of Restored Files": "0",
        "Number Of Skipped Files": "0",
        "RestoreDestination": "afaccount1/azurefiles1/restoredata",
        "Source File Share Name": "azurefiles",
        "Source Storage Account Name": "afsaccount",
        "Target File Share Name": "azurefiles1",
        "Target Storage Account Name": "afaccount1"
      },
      "tasksList": []
    },
    "jobType": "AzureStorageJob",
    "operation": "Restore",
    "startTime": "2020-02-05T17:10:18.106532+00:00",
    "status": "InProgress",
    "storageAccountName": "afsaccount",
    "storageAccountVersion": "ClassicCompute"
  },
  "resourceGroup": "azurefiles",
  "tags": null,
  "type": "Microsoft.RecoveryServices/vaults/backupJobs"
}
```

Como a tarefa de backup é uma operação longa, ela deve ser rastreada conforme explicado nas tarefas do monitor [usando o documento da API REST](./backup-azure-arm-userestapi-managejobs.md#tracking-the-job).

## <a name="item-level-recovery-using-rest-api"></a>Recuperação em nível de item usando a API REST

Você pode usar essa opção de restauração para restaurar arquivos ou pastas individuais no local original ou alternativo.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints/{recoveryPointId}/restore?api-version=2019-05-13
```

Os valores {ContainerName} e {protectedItemName} estão definidos [aqui](#fetch-containername-and-protecteditemname) e recoveryPointID é o campo {Name} do ponto de recuperação mencionado acima.

```http
POST https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare%3Bazurefiles/recoveryPoints/932886657837421071/restore?api-version=2019-05-13'
```

### <a name="create-request-body-for-item-level-recovery-using-rest-api"></a>Criar corpo da solicitação para recuperação em nível de item usando a API REST

Para disparar uma restauração para um compartilhamento de arquivos do Azure, veja a seguir os componentes do corpo da solicitação:

Nome |  Tipo   |   Descrição
--- | ---- | ----
Propriedades | AzureFileShareRestoreRequest | Propriedades de RestoreRequestResource

Para obter a lista completa de definições do corpo da solicitação e outros detalhes, consulte o [documento disparar a API REST de restauração](/rest/api/backup/restores/trigger#request-body).

### <a name="restore-to-original-location-for-item-level-recovery-using-rest-api"></a>Restaurar para o local original para recuperação em nível de item usando a API REST

O corpo da solicitação a seguir é restaurar o arquivo de *Restoretest.txt* no compartilhamento de arquivos *azurefiles* na conta de armazenamento *afsaccount* .

Criar corpo da solicitação

```json
{
   "properties":{
      "objectType":"AzureFileShareRestoreRequest",
      "copyOptions":"Overwrite",
      "recoveryType":"OriginalLocation",
      "restoreFileSpecs":[
         {
            "fileSpecType":"File",
            "path":"RestoreTest.txt",
            "targetFolderPath":null
}
],
      "restoreRequestType":"ItemLevelRestore",
      "sourceResourceId":"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.storage/storageAccounts/afsaccount",
      "targetDetails":null
}
}
```

### <a name="restore-to-alternate-location-for-item-level-recovery-using-rest-api"></a>Restaurar para um local alternativo para recuperação em nível de item usando a API REST

O corpo da solicitação a seguir é restaurar o arquivo de *Restoretest.txt* no compartilhamento de arquivos *azurefiles* na conta de armazenamento *afsaccount* para a pasta *restoredata* do compartilhamento de arquivos *azurefiles1* na conta de armazenamento *afaccount1* .

Criar o corpo da solicitação

```json
{
   "properties":{
      "objectType":"AzureFileShareRestoreRequest",
      "recoveryType":"AlternateLocation",
      "sourceResourceId":"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afsaccount",
      "copyOptions":"Overwrite",
      "restoreRequestType":"ItemLevelRestore",
      "restoreFileSpecs":[
         {
            "path":"Restore/RestoreTest.txt",
            "fileSpecType":"File",
            "targetFolderPath":"restoredata"
}
],
      "targetDetails":{
         "name":"azurefiles1",
         "targetResourceId":"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afaccount1"
}
}
}
```

A resposta deve ser tratada da mesma maneira como explicada acima para [restaurações completas de compartilhamento](#full-share-recovery-using-rest-api).

## <a name="next-steps"></a>Próximas etapas

* Saiba como [gerenciar o backup de compartilhamentos de arquivos do Azure usando a API REST](manage-azure-file-share-rest-api.md).
