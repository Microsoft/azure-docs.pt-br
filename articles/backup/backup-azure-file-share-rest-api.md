---
title: Fazer backup de compartilhamentos de arquivos do Azure com a API REST
description: Saiba como usar a API REST para fazer backup de compartilhamentos de arquivos do Azure no cofre dos serviços de recuperação
ms.topic: conceptual
ms.date: 02/16/2020
ms.openlocfilehash: 8d2d8ed88da133986540a293185c8e37000ab87b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "88824858"
---
# <a name="backup-azure-file-share-using-azure-backup-via-rest-api"></a>Fazer backup do compartilhamento de arquivos do Azure usando o backup do Azure via API REST

Este artigo descreve como fazer backup de um compartilhamento de arquivos do Azure usando o backup do Azure por meio da API REST.

Este artigo pressupõe que você já criou um cofre de serviços de recuperação e uma política para configurar o backup para seu compartilhamento de arquivos. Se você ainda não fez isso, consulte os tutoriais [criar cofre](./backup-azure-arm-userestapi-createorupdatevault.md) e [criar política](./backup-azure-arm-userestapi-createorupdatepolicy.md) REST da API para criar novos cofres e políticas.

Para este artigo, usaremos os seguintes recursos:

- **RecoveryServicesVault**: *azurefilesvault*

- **Política:** *schedule1*

- **Grupo de recursos**: *azurefiles*

- **Conta de armazenamento**: *testvault2*

- **Compartilhamento de arquivos**: *testshare*

## <a name="configure-backup-for-an-unprotected-azure-file-share-using-rest-api"></a>Configurar o backup de um compartilhamento de arquivos do Azure desprotegido usando a API REST

### <a name="discover-storage-accounts-with-unprotected-azure-file-shares"></a>Descobrir contas de armazenamento com compartilhamentos de arquivos do Azure desprotegidos

O cofre precisa descobrir todas as contas de armazenamento do Azure na assinatura com compartilhamentos de arquivos cujo backup pode ser feito no cofre dos serviços de recuperação. Isso é disparado usando a [operação de atualização](/rest/api/backup/protectioncontainers/refresh). É uma operação *post* assíncrona que garante que o cofre obtenha a lista mais recente de todos os compartilhamentos de arquivos do Azure desprotegidos na assinatura atual e os ' caches '. Depois que o compartilhamento de arquivos é ' armazenado em cache ', os serviços de recuperação podem acessar o compartilhamento de arquivos e protegê-lo.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupname}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/refreshContainers?api-version=2016-12-01&$filter={$filter}
```

O URI da postagem tem os `{subscriptionId}` `{vaultName}` parâmetros,, `{vaultresourceGroupName}` e `{fabricName}` . Em nosso exemplo, o valor para os diferentes parâmetros será o seguinte:

- `{fabricName}` é o *Azure*

- `{vaultName}` é *azurefilesvault*

- `{vaultresourceGroupName}` é *azurefiles*

- $filter = backupManagementType EQ ' AzureStorage '

Como todos os parâmetros necessários são fornecidos no URI, não há necessidade de um corpo de solicitação separado.

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/refreshContainers?api-version=2016-12-01&$filter=backupManagementType eq 'AzureStorage'
```

#### <a name="responses-to-the-refresh-operation"></a>Respostas para a operação de atualização

A operação “atualizar” é uma [operação assíncrona](../azure-resource-manager/management/async-operations.md). Isso significa que essa operação cria outra operação que precisa ser rastreada separadamente.

Ele retorna duas respostas: 202 (aceito) quando outra operação é criada e 200 (OK) quando a operação é concluída.

##### <a name="example-responses-to-the-refresh-operation"></a>Respostas de exemplo para a operação de atualização

Após a solicitação *POST* ser enviada, uma resposta 202 (Aceito) será retornada.

```http
HTTP/1.1 202 Accepted
'Pragma': 'no-cache'
'Expires': '-1'
'Location': ‘https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/ResourceGroups
/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/operationResults/
cca47745-12d2-42f9-b3a4-75335f18fdf6?api-version=2016-12-01’
'Retry-After': '60'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '6cc12ceb-90a2-430d-a1ec-9b6b6fdea92b'
'x-ms-client-request-id': ‘3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61’
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-reads': '11996'
'x-ms-correlation-request-id': '6cc12ceb-90a2-430d-a1ec-9b6b6fdea92b'
'x-ms-routing-request-id': CENTRALUSEUAP:20200203T091326Z:6cc12ceb-90a2-430d-a1ec-9b6b6fdea92b'
'Date': 'Mon, 03 Feb 2020 09:13:25 GMT'
```

Rastreie a operação resultante usando o cabeçalho de “Localização” com um comando *GET* simples

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/operationResults/cca47745-12d2-42f9-b3a4-75335f18fdf6?api-version=2016-12-01
```

Depois que todas as contas de armazenamento do Azure forem descobertas, o comando GET retornará uma resposta 200 (sem conteúdo). O cofre agora é capaz de descobrir qualquer conta de armazenamento com compartilhamentos de arquivos cujo backup pode ser feito na assinatura.

```http
HTTP/1.1 200 NoContent
Cache-Control  : no-cache
Pragma   : no-cache
X-Content-Type-Options  : nosniff
x-ms-request-id    : d9bdb266-8349-4dbd-9688-de52f07648b2
x-ms-client-request-id  : 3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61
Strict-Transport-Security  : max-age=31536000; includeSubDomains
X-Powered-By    : ASP.NET
x-ms-ratelimit-remaining-subscription-reads: 11933
x-ms-correlation-request-id   : d9bdb266-8349-4dbd-9688-de52f07648b2
x-ms-routing-request-id  : CENTRALUSEUAP:20200127T105304Z:d9bdb266-8349-4dbd-9688-de52f07648b2
Date   : Mon, 27 Jan 2020 10:53:04 GMT
```

### <a name="get-list-of-storage-accounts-with-file-shares-that-can-be-backed-up-with-recovery-services-vault"></a>Obter lista de contas de armazenamento com compartilhamentos de arquivos cujo backup pode ser feito com o cofre dos serviços de recuperação

Para confirmar se o "caching" está concluído, liste todas as contas de armazenamento na assinatura com compartilhamentos de arquivos cujo backup pode ser feito com o cofre dos serviços de recuperação. Em seguida, localize a conta de armazenamento desejada na resposta. Isso é feito usando a operação [Get ProtectableContainers](/rest/api/backup/protectablecontainers/list) .

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectableContainers?api-version=2016-12-01&$filter=backupManagementType eq 'AzureStorage'
```

O URI *GET* tem todos os parâmetros necessários. Nenhum corpo da solicitação adicional é necessário.

Exemplo de corpo da resposta:

```json
{

  "value": [

    {

      "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers
 /Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/

protectableContainers/StorageContainer;Storage;AzureFiles;testvault2",

      "name": "StorageContainer;Storage;AzureFiles;testvault2",

      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectableContainers",

      "properties": {

        "friendlyName": "testvault2",

        "backupManagementType": "AzureStorage",

        "protectableContainerType": "StorageContainer",

        "healthStatus": "Healthy",

        "containerId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/
 AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2"

      }

    }

  ]

}
```

Como podemos localizar a conta de armazenamento *testvault2* no corpo da resposta com o nome amigável, a operação de atualização executada acima foi bem-sucedida. O cofre dos serviços de recuperação agora pode descobrir com êxito as contas de armazenamento com compartilhamentos de arquivos desprotegidos na mesma assinatura.

### <a name="register-storage-account-with-recovery-services-vault"></a>Registrar conta de armazenamento com o cofre dos serviços de recuperação

Esta etapa só será necessária se você não tiver registrado a conta de armazenamento com o cofre anteriormente. Você pode registrar o cofre por meio da [operação ProtectionContainers-Register](/rest/api/backup/protectioncontainers/register).

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}?api-version=2016-12-01
```

Defina as variáveis para o URI da seguinte maneira:

- {resourceGroupName}- *azurefiles*
- {fabricname}- *Azure*
- {vaultname}- *azurefilesvault*
- {ContainerName}-este é o atributo de nome no corpo da resposta da operação GET ProtectableContainers.
   Em nosso exemplo, é *StorageContainer; Repositório AzureFiles; testvault2*

>[!NOTE]
> Sempre use o atributo Name da resposta e preencha-o nesta solicitação. Não codifique nem crie o formato de nome de contêiner. Se você criar ou embutir em código, a chamada à API falhará se o formato do nome do contêiner for alterado no futuro.

<br>

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2?api-version=2016-12-01
```

O corpo da solicitação de criação é o seguinte:

```json
{

 "properties": {


  "containerType": "StorageContainer",


  "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",


  "resourceGroup": "AzureFiles",


  "friendlyName": "testvault2",


  "backupManagementType": "AzureStorage"


 }
```

Para obter a lista completa de definições do corpo da solicitação e outros detalhes, consulte [ProtectionContainers-Register](/rest/api/backup/protectioncontainers/register#azurestoragecontainer).

Essa é uma operação assíncrona e retorna duas respostas: "202 aceito" quando a operação é aceita e "200 OK" quando a operação é concluída.  Para acompanhar o status da operação, use o cabeçalho Location para obter o status mais recente da operação.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/operationresults/1a3c8ee7-e0e5-43ed-b8b3-73cc992b6db9?api-version=2016-12-01
```

Exemplo de corpo de resposta quando a operação é concluída:

```json
{
    "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/
protectionContainers/StorageContainer;Storage;AzureFiles;testvault2",
    "name": "StorageContainer;Storage;AzureFiles;testvault2",
    "properties": {
        "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
        "protectedItemCount": 0,
        "friendlyName": "testvault2",
        "backupManagementType": "AzureStorage",
        "registrationStatus": "Registered",
        "healthStatus": "Healthy",
        "containerType": "StorageContainer",
        "protectableObjectType": "StorageContainer"
    }
}
```

Você pode verificar se o registro foi bem-sucedido do valor do parâmetro *registrationstatus* no corpo da resposta. Em nosso caso, ele mostra o status como registrado para *testvault2*, portanto, a operação de registro foi bem-sucedida.

### <a name="inquire-all-unprotected-files-shares-under-a-storage-account"></a>Consultar todos os compartilhamentos de arquivos desprotegidos em uma conta de armazenamento

Você pode consultar os itens que podem ser protegidos em uma conta de armazenamento usando os [contêineres de proteção-](/rest/api/backup/protectioncontainers/inquire) operação de consulta. É uma operação assíncrona e os resultados devem ser controlados usando o cabeçalho de local.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/inquire?api-version=2016-12-01
```

Defina as variáveis para o URI acima da seguinte maneira:

- {vaultname}- *azurefilesvault*
- {fabricname}- *Azure*
- {ContainerName}-consulte o atributo Name no corpo da resposta da operação GET ProtectableContainers. Em nosso exemplo, é *StorageContainer; Repositório AzureFiles; testvault2*

```http
https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/inquire?api-version=2016-12-01
```

Depois que a solicitação for bem-sucedida, ela retornará o código de status "OK"

```http
Cache-Control : no-cache
Pragma   : no-cache
X-Content-Type-Options: nosniff
x-ms-request-id  : 68727f1e-b8cf-4bf1-bf92-8e03a9d96c46
x-ms-client-request-id  : 3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61
Strict-Transport-Security: max-age=31536000; includeSubDomains
Server  : Microsoft-IIS/10.0
X-Powered-B : ASP.NET
x-ms-ratelimit-remaining-subscription-reads: 11932
x-ms-correlation-request-id  : 68727f1e-b8cf-4bf1-bf92-8e03a9d96c46
x-ms-routing-request-id   : CENTRALUSEUAP:20200127T105305Z:68727f1e-b8cf-4bf1-bf92-8e03a9d96c46
Date  : Mon, 27 Jan 2020 10:53:05 GMT
```

### <a name="select-the-file-share-you-want-to-back-up"></a>Selecione o compartilhamento de arquivos do qual você deseja fazer backup

Você pode listar todos os itens que podem ser protegidos na assinatura e localizar o compartilhamento de arquivos desejado para backup usando a operação [Get backupprotectableItems](/rest/api/backup/backupprotectableitems/list) .

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupProtectableItems?api-version=2016-12-01&$filter={$filter}
```

Construa o URI da seguinte maneira:

- {vaultname}- *azurefilesvault*
- {$filter}- *backupManagementType EQ ' AzureStorage '*

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupProtectableItems?$filter=backupManagementType eq 'AzureStorage'&api-version=2016-12-01
```

Exemplo de resposta:

```json
Status Code:200

{
    "value": [
        {
            "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;afaccount1/protectableItems/azurefileshare;azurefiles1",
            "name": "azurefileshare;azurefiles1",
            "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
            "properties": {
                "parentContainerFabricId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afaccount1",
                "parentContainerFriendlyName": "afaccount1",
                "azureFileShareType": "XSMB",
                "backupManagementType": "AzureStorage",
                "workloadType": "AzureFileShare",
                "protectableItemType": "AzureFileShare",
                "friendlyName": "azurefiles1",
                "protectionState": "NotProtected"
            }
        },
        {
            "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;afsaccount/protectableItems/azurefileshare;afsresource",
            "name": "azurefileshare;afsresource",
            "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
            "properties": {
                "parentContainerFabricId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afsaccount",
                "parentContainerFriendlyName": "afsaccount",
                "azureFileShareType": "XSMB",
                "backupManagementType": "AzureStorage",
                "workloadType": "AzureFileShare",
                "protectableItemType": "AzureFileShare",
                "friendlyName": "afsresource",
                "protectionState": "NotProtected"
            }
        },
        {
            "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;testvault2/protectableItems/azurefileshare;testshare",
            "name": "azurefileshare;testshare",
            "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
            "properties": {
                "parentContainerFabricId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
                "parentContainerFriendlyName": "testvault2",
                "azureFileShareType": "XSMB",
                "backupManagementType": "AzureStorage",
                "workloadType": "AzureFileShare",
                "protectableItemType": "AzureFileShare",
                "friendlyName": "testshare",
                "protectionState": "NotProtected"
            }
        }
    ]
}
```

A resposta contém a lista de todos os compartilhamentos de arquivos desprotegidos e contém todas as informações exigidas pelo serviço de recuperação do Azure para configurar o backup.

### <a name="enable-backup-for-the-file-share"></a>Habilitar o backup para o compartilhamento de arquivos

Depois que o compartilhamento de arquivos relevante for "identificado" com o nome amigável, selecione a política a ser protegida. Para saber mais sobre as políticas existentes no cofre, confira [API de política de lista](/rest/api/backup/backuppolicies/list). Em seguida, selecione a [política relevante](/rest/api/backup/protectionpolicies/get) referindo-se ao nome da política. Para criar políticas, veja o [tutorial de criação de políticas](./backup-azure-arm-userestapi-createorupdatepolicy.md).

Habilitar a proteção é uma operação *Put* assíncrona que cria um "item protegido".

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

Defina as variáveis **ContainerName** e **protecteditemname** usando o atributo ID no corpo da resposta da operação get backupprotectableitems.

Em nosso exemplo, a ID do compartilhamento de arquivos que desejamos proteger é:

```output
"/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;testvault2/protectableItems/azurefileshare;testshare
```

- {ContainerName}- *storagecontainer; armazenamento; azurefiles; testvault2*
- {protectedItemName}- *azurefileshare; testshare*

Ou você pode consultar o atributo **Name** do contêiner de proteção e as respostas de itens protegíveis.

>[!NOTE]
>Sempre use o atributo Name da resposta e preencha-o nesta solicitação. Não codifique ou crie o formato de nome de contêiner ou o formato de nome de item protegido. Se você criar ou embutir em código, a chamada à API falhará se o formato do nome do contêiner ou o formato do nome do item protegido for alterado no futuro.

<br>

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/protectedItems/azurefileshare;testshare?api-version=2016-12-01
```

Criar um corpo de solicitação:

O corpo da solicitação a seguir define as propriedades necessárias para criar um item protegido.

```json
{
  "properties": {
    "protectedItemType": "AzureFileShareProtectedItem",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
    "policyId": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupPolicies/schedule1"
  }
}
```

O **sourceResourceId** é o **parentcontainerFabricID** em resposta de Get backupprotectableItems.

Exemplo de Resposta

A criação de um item protegido é uma operação assíncrona, que cria outra operação que precisa ser controlada. Ele retorna duas respostas: 202 (aceito) quando outra operação é criada e 200 (OK) quando a operação é concluída.

Depois de enviar a solicitação *Put* para a criação ou atualização de itens protegidos, a resposta inicial será 202 (aceita) com um cabeçalho de local.

```http
HTTP/1.1 202 Accepted
Cache-Control  : no-cache
Pragma  : no-cache
Location : https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/protectedItems/azurefileshare;testshare/operationResults/c3a52d1d-0853-4211-8141-477c65740264?api-version=2016-12-01
Retry-Afte  : 60
Azure-AsyncOperation  : https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/protectedItems/azurefileshare;testshare/operationResults/c3a52d1d-0853-4211-8141-477c65740264?api-version=2016-12-01
X-Content-Type-Options : nosniff
x-ms-request-id : b55527fa-f473-4f09-b169-9cc3a7a39065
x-ms-client-request-id: 3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61
Strict-Transport-Security : max-age=31536000; includeSubDomains
X-Powered-By  : ASP.NET
x-ms-ratelimit-remaining-subscription-writes: 1198
x-ms-correlation-request-id : b55527fa-f473-4f09-b169-9cc3a7a39065
x-ms-routing-request-id  : CENTRALUSEUAP:20200127T105412Z:b55527fa-f473-4f09-b169-9cc3a7a39065
Date : Mon, 27 Jan 2020 10:54:12 GMT
```

Em seguida, acompanhe a operação resultante usando o cabeçalho de local ou o cabeçalho de Azure-AsyncOperation com um comando  *Get* .

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupOperations/c3a52d1d-0853-4211-8141-477c65740264?api-version=2016-12-01
```

Depois que a operação for concluída, ele retornará 200 (OK) com o conteúdo do item protegido no corpo da resposta.

Corpo da resposta de exemplo:

```json
{
    "id": "c3a52d1d-0853-4211-8141-477c65740264",
    "name": "c3a52d1d-0853-4211-8141-477c65740264",
    "status": "Succeeded",
    "startTime": "2020-02-03T18:10:48.296012Z",
    "endTime": "2020-02-03T18:10:48.296012Z",
    "properties": {
        "objectType": "OperationStatusJobExtendedInfo",
        "jobId": "e2ca2cf4-2eb9-4d4b-b16a-8e592d2a658b"
    }
}
```

Isso confirma que a proteção está habilitada para o compartilhamento de arquivos e o primeiro backup será disparado de acordo com o agendamento da política.

## <a name="trigger-an-on-demand-backup-for-file-share"></a>Disparar um backup sob demanda para compartilhamento de arquivos

Depois que um compartilhamento de arquivos do Azure é configurado para backup, os backups são executados de acordo com o agendamento da política. Você pode esperar pelo primeiro backup agendado ou disparar um backup sob demanda a qualquer momento.

Disparar um backup sob demanda é uma operação POST.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/backup?api-version=2016-12-01
```

{ContainerName} e {protectedItemName} estão conforme construídos acima ao habilitar o backup. Para nosso exemplo, isso é traduzido como:

```http
POST https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/backup?api-version=2017-07-01
```

### <a name="create-request-body"></a>Criar o corpo da solicitação

Para disparar um backup sob demanda, a seguir estão os componentes do corpo da solicitação.

| Nome       | Tipo                       | Descrição                       |
| ---------- | -------------------------- | --------------------------------- |
| Propriedades | AzurefilesharebackupReques | Propriedades de BackupRequestResource |

Para obter uma lista de definições de corpo da solicitação e outros detalhes, veja o [documento sobre como disparar backups de itens protegidos da API REST](/rest/api/backup/backups/trigger#request-body).

Exemplo de corpo de solicitação

```json
{

  "properties":{

   "objectType":"AzureFileShareBackupRequest",
    "recoveryPointExpiryTimeInUTC":"2020-03-07T18:29:59.000Z"
}

}
```

### <a name="responses-to-the-on-demand-backup-operation"></a>Respostas para a operação de backup sob demanda

Disparar um backup sob demanda é uma [operação assíncrona](../azure-resource-manager/management/async-operations.md). Isso significa que essa operação cria outra operação que precisa ser rastreada separadamente.

Ele retorna duas respostas: 202 (aceito) quando outra operação é criada e 200 (OK) quando a operação é concluída.

### <a name="example-responses-to-the-on-demand-backup-operation"></a>Respostas de exemplo para a operação de backup sob demanda

Depois de enviar a solicitação *POST* para um backup sob demanda, a resposta inicial é 202 (Aceito) com um cabeçalho de localização ou cabeçalho assíncrono do Azure.

```http
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Expires': '-1'
'Location': https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/operationResults/dc62d524-427a-4093-968d-e951c0a0726e?api-version=2017-07-01
'Retry-After': '60'
'Azure-AsyncOperation': https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/operationsStatus/dc62d524-427a-4093-968d-e951c0a0726e?api-version=2017-07-01
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '2e03b8d4-66b1-48cf-8094-aa8bff57e8fb'
'x-ms-client-request-id': 'a644712a-4895-11ea-ba57-0a580af42708, a644712a-4895-11ea-ba57-0a580af42708'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-writes': '1199'
'x-ms-correlation-request-id': '2e03b8d4-66b1-48cf-8094-aa8bff57e8fb'
'x-ms-routing-request-id': 'WESTEUROPE:20200206T040339Z:2e03b8d4-66b1-48cf-8094-aa8bff57e8fb'
'Date': 'Thu, 06 Feb 2020 04:03:38 GMT'
'Content-Length': '0'
```

Em seguida, acompanhe a operação resultante usando o cabeçalho de local ou o cabeçalho de Azure-AsyncOperation com um comando  *Get* .

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupOperations/dc62d524-427a-4093-968d-e951c0a0726e?api-version=2016-12-01
```

Depois que a operação for concluída, ela retornará 200 (OK) com a ID da tarefa de backup resultante no corpo da resposta.

#### <a name="sample-response-body"></a>Corpo da resposta de exemplo

```json
{
    "id": "dc62d524-427a-4093-968d-e951c0a0726e",
    "name": "dc62d524-427a-4093-968d-e951c0a0726e",
    "status": "Succeeded",
    "startTime": "2020-02-06T11:06:02.1327954Z",
    "endTime": "2020-02-06T11:06:02.1327954Z",
    "properties": {
        "objectType": "OperationStatusJobExtendedInfo",
        "jobId": "39282261-cb52-43f5-9dd0-ffaf66beeaef"
    }
}
```

Como a tarefa de backup é uma operação longa, ela precisa ser rastreada conforme explicado [no documento sobre monitoramento de trabalhos usando a API REST](./backup-azure-arm-userestapi-managejobs.md#tracking-the-job).

## <a name="next-steps"></a>Próximas etapas

- Saiba como [restaurar compartilhamentos de arquivos do Azure usando a API REST](restore-azure-file-share-rest-api.md).
