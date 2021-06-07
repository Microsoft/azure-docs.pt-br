---
title: Fazer backup de VMs do Azure usando a API REST
description: Neste artigo, saiba como configurar, iniciar e gerenciar operações de backup do backup de VM do Azure usando a API REST.
ms.topic: conceptual
ms.date: 08/03/2018
ms.assetid: b80b3a41-87bf-49ca-8ef2-68e43c04c1a3
ms.openlocfilehash: 9ba22c51c7a6c26a232ed20aec21fc83d2c54b37
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92171455"
---
# <a name="back-up-an-azure-vm-using-azure-backup-via-rest-api"></a>Fazer backup de uma VM do Azure usando o Backup do Azure por meio da API REST

Este artigo descreve como gerenciar backups para uma VM do Azure usando o Backup do Azure por meio da API REST. Configure a proteção pela primeira vez para uma VM do Azure desprotegida anteriormente, dispare um backup sob demanda para uma VM do Azure protegida e modifique as propriedades de backup de uma VM com backup por meio da API REST, conforme explicado aqui.

Veja os tutoriais [criar cofre](backup-azure-arm-userestapi-createorupdatevault.md) e [criar política](backup-azure-arm-userestapi-createorupdatepolicy.md) da API REST para criar novos cofres e políticas.

Digamos que você queira proteger a VM "testVM" no grupo de recursos "testRG" em um cofre dos Serviços de Recuperação "testVault", presente no grupo de recursos "testVaultRG", com a política padrão (denominada "DefaultPolicy").

## <a name="configure-backup-for-an-unprotected-azure-vm-using-rest-api"></a>Configurar o backup para uma VM do Azure desprotegida usando a API REST

### <a name="discover-unprotected-azure-vms"></a>Descobrir VMs do Azure desprotegidas

Primeiro, o cofre deve ser capaz de identificar a VM do Azure. Isso é disparado usando a [operação de atualização](/rest/api/backup/protectioncontainers/refresh). É uma operação *post*  assíncrona que garante que o cofre obtenha a lista mais recente de todas as VMs desprotegidas na assinatura atual e os ' caches '. Após a VM ser “armazenada em cache”, os Serviços de Recuperação poderão acessar a VM e protegê-la.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupname}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/refreshContainers?api-version=2016-12-01
```

O URI POST tem os parâmetros `{subscriptionId}`, `{vaultName}`, `{vaultresourceGroupName}` e `{fabricName}`. O `{fabricName}` é "Azure". De acordo com nosso exemplo, `{vaultName}` é "testVault" e `{vaultresourceGroupName}` é "testVaultRG". Como todos os parâmetros necessários são fornecidos no URI, não há necessidade de um corpo de solicitação separado.

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/refreshContainers?api-version=2016-12-01
```

#### <a name="responses-to-refresh-operation"></a>Respostas para a operação de atualização

A operação “atualizar” é uma [operação assíncrona](../azure-resource-manager/management/async-operations.md). Isso significa que essa operação cria outra operação que precisa ser rastreada separadamente.

Ele retorna duas respostas: 202 (Aceito) quando outra operação é criada e, em seguida, 200 (OK) quando a operação é concluída.

|Nome  |Tipo  |Descrição  |
|---------|---------|---------|
|204 Sem Conteúdo     |         |  OK sem conteúdo retornado      |
|202 Aceito     |         |     Aceito    |

##### <a name="example-responses-to-refresh-operation"></a>Respostas de exemplo para a operação de atualização

Após a solicitação *POST* ser enviada, uma resposta 202 (Aceito) será retornada.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
X-Content-Type-Options: nosniff
x-ms-request-id: 43cf550d-e463-421c-8922-37e4766db27d
x-ms-client-request-id: 4910609f-bb9b-4c23-8527-eb6fa2d3253f; 4910609f-bb9b-4c23-8527-eb6fa2d3253f
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 43cf550d-e463-421c-8922-37e4766db27d
x-ms-routing-request-id: SOUTHINDIA:20180521T105701Z:43cf550d-e463-421c-8922-37e4766db27d
Cache-Control: no-cache
Date: Mon, 21 May 2018 10:57:00 GMT
Location: https://management.azure.com/subscriptions//00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/operationResults/aad204aa-a5cf-4be2-a7db-a224819e5890?api-version=2019-05-13
X-Powered-By: ASP.NET
```

Rastreie a operação resultante usando o cabeçalho de “Localização” com um comando *GET* simples

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/operationResults/aad204aa-a5cf-4be2-a7db-a224819e5890?api-version=2019-05-13
```

Após todas as VMs do Azure serem descobertas, o comando GET retornará uma resposta 204 (Sem Conteúdo). Agora, o cofre pode descobrir qualquer VM dentro da assinatura.

```http
HTTP/1.1 204 NoContent
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: cf6cd73b-9189-4942-a61d-878fcf76b1c1
x-ms-client-request-id: 25bb6345-f9fc-4406-be1a-dc6db0eefafe; 25bb6345-f9fc-4406-be1a-dc6db0eefafe
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14997
x-ms-correlation-request-id: cf6cd73b-9189-4942-a61d-878fcf76b1c1
x-ms-routing-request-id: SOUTHINDIA:20180521T105825Z:cf6cd73b-9189-4942-a61d-878fcf76b1c1
Cache-Control: no-cache
Date: Mon, 21 May 2018 10:58:25 GMT
X-Powered-By: ASP.NET
```

### <a name="selecting-the-relevant-azure-vm"></a>Selecionar a VM do Azure relevante

 Você pode confirmar que o "armazenamento em cache" foi concluído [listando todos os itens que podem ser protegidos](/rest/api/backup/backupprotectableitems/list) na assinatura e localizando a VM desejada na resposta. [A resposta dessa operação](#example-responses-to-get-operation) também fornece informações sobre como os serviços de recuperação identificam uma VM.  Quando estiver familiarizado com o padrão, você poderá ignorar esta etapa e ir diretamente para a etapa de [habilitar a proteção](#enabling-protection-for-the-azure-vm).

Esta operação é uma operação *GET*.

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupProtectableItems?api-version=2016-12-01&$filter=backupManagementType eq 'AzureIaasVM'
```

O URI *GET* tem todos os parâmetros necessários. Nenhum corpo da solicitação adicional é necessário.

#### <a name="responses-to-get-operation"></a>Respostas para a operação de obtenção

|Nome  |Tipo  |Descrição  |
|---------|---------|---------|
|200 OK     | [WorkloadProtectableItemResourceList](/rest/api/backup/backupprotectableitems/list#workloadprotectableitemresourcelist)        |       OK |

#### <a name="example-responses-to-get-operation"></a>Respostas de exemplo para a operação get

Após uma solicitação *GET* ser enviada, uma resposta 200 (OK) será retornada.

```http
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: 7c2cf56a-e6be-4345-96df-c27ed849fe36
x-ms-client-request-id: 40c8601a-c217-4c68-87da-01db8dac93dd; 40c8601a-c217-4c68-87da-01db8dac93dd
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14979
x-ms-correlation-request-id: 7c2cf56a-e6be-4345-96df-c27ed849fe36
x-ms-routing-request-id: SOUTHINDIA:20180521T071408Z:7c2cf56a-e6be-4345-96df-c27ed849fe36
Cache-Control: no-cache
Date: Mon, 21 May 2018 07:14:08 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainerv2;testRG;testVM/protectableItems/vm;iaasvmcontainerv2;testRG;testVM",
      "name": "iaasvmcontainerv2;testRG;testVM",
      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
      "properties": {
        "virtualMachineId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
        "virtualMachineVersion": "Compute",
        "resourceGroup": "testRG",
        "backupManagementType": "AzureIaasVM",
        "protectableItemType": "Microsoft.Compute/virtualMachines",
        "friendlyName": "testVM",
        "protectionState": "NotProtected"
      }
    },……………..

```

> [!TIP]
> O número de valores em uma resposta *GET* é limitado a 200 por “página”. Use o campo “nextLink” para obter a URL para o conjunto de respostas seguinte.

A resposta contém a lista de todas as VMs do Azure desprotegidas e cada `{value}` contém as informações exigidas pelo Serviço de Recuperação do Azure para configurar o backup. Para configurar o backup, tome nota do campo `{name}` e do campo `{virtualMachineId}` na seção `{properties}`. Construa duas variáveis usando esses valores de campo, conforme mencionado abaixo.

- containerName = "iaasvmcontainer;"+`{name}`
- protectedItemName = "vm;"+ `{name}`
- `{virtualMachineId}` é usado posteriormente no [corpo da solicitação](#example-request-body)

No exemplo, os valores acima são traduzidos para:

- containerName = "iaasvmcontainer;iaasvmcontainerv2;testRG;testVM"
- protectedItemName = "vm;iaasvmcontainerv2;testRG;testVM"

### <a name="enabling-protection-for-the-azure-vm"></a>Habilitar a proteção para a VM do Azure

Após a VM relevante ser "armazenada em cache" e "identificada", selecione a política para proteção. Para saber mais sobre as políticas existentes no cofre, veja a [Lista de políticas de API](/rest/api/backup/backuppolicies/list). Em seguida, selecione a [política relevante](/rest/api/backup/protectionpolicies/get) referindo-se ao nome da política. Para criar políticas, veja o [tutorial de criação de políticas](backup-azure-arm-userestapi-createorupdatepolicy.md). "DefaultPolicy" é selecionado no exemplo abaixo.

A habilitação da proteção é uma operação *PUT* assíncrona que cria um “item protegido”.

```http
https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

`{containerName}` e `{protectedItemName}` têm a construção descrita acima. O `{fabricName}` é "Azure". Para nosso exemplo, isso é traduzido como:

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM?api-version=2019-05-13
```

#### <a name="create-the-request-body"></a>Criar o corpo da solicitação

Para criar um item protegido, confira a seguir os componentes do corpo da solicitação.

|Nome  |Tipo  |Descrição  |
|---------|---------|---------|
|properties     | AzureIaaSVMProtectedItem        |Propriedades do recurso ProtectedItem         |

Para obter uma lista de definições de corpo da solicitação e outros detalhes, veja o [documento sobre a criação de itens protegidos da API REST](/rest/api/backup/protecteditems/createorupdate#request-body).

##### <a name="example-request-body"></a>Exemplo do corpo de solicitação

O corpo da solicitação a seguir define as propriedades necessárias para criar um item protegido.

```json
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/DefaultPolicy"
  }
}
```

O `{sourceResourceId}` é o `{virtualMachineId}` mencionado acima da [resposta com a lista de itens que podem ser protegidos](#example-responses-to-get-operation).

#### <a name="responses-to-create-protected-item-operation"></a>Respostas para a operação Criar item protegido

A criação de um item protegido é uma [operação assíncrona](../azure-resource-manager/management/async-operations.md). Isso significa que essa operação cria outra operação que precisa ser rastreada separadamente.

Ele retorna duas respostas: 202 (Aceito) quando outra operação é criada e, em seguida, 200 (OK) quando a operação é concluída.

|Nome  |Tipo  |Descrição  |
|---------|---------|---------|
|200 OK     |    [ProtectedItemResource](/rest/api/backup/protecteditemoperationresults/get#protecteditemresource)     |  OK       |
|202 Aceito     |         |     Aceito    |

##### <a name="example-responses-to-create-protected-item-operation"></a>Respostas de exemplo para a operação Criar item protegido

Depois de enviar a solicitação *PUT* para criação ou atualização do item protegido, a resposta inicial é 202 (Aceito) com um cabeçalho de localização ou Azure-async-header.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2019-05-13
X-Content-Type-Options: nosniff
x-ms-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-client-request-id: e1f94eef-9b2d-45c4-85b8-151e12b07d03; e1f94eef-9b2d-45c4-85b8-151e12b07d03
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-routing-request-id: SOUTHINDIA:20180521T073907Z:db785be0-bb20-4598-bc9f-70c9428b170b
Cache-Control: no-cache
Date: Mon, 21 May 2018 07:39:06 GMT
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationResults/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2019-05-13
X-Powered-By: ASP.NET
```

Em seguida, rastreie a operação resultante usando o cabeçalho de localização ou o cabeçalho Azure-AsyncOperation com um simples comando *GET*.

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2019-05-13
```

Depois que a operação for concluída, ele retornará 200 (OK) com o conteúdo do item protegido no corpo da resposta.

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM",
  "name": "VM;testRG;testVM",
  "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems",
  "properties": {
    "friendlyName": "testVM",
    "virtualMachineId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "protectionStatus": "Healthy",
    "protectionState": "IRPending",
    "healthStatus": "Passed",
    "lastBackupStatus": "",
    "lastBackupTime": "2001-01-01T00:00:00Z",
    "protectedItemDataId": "17592691116891",
    "extendedInfo": {
      "recoveryPointCount": 0,
      "policyInconsistent": false
    },
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "backupManagementType": "AzureIaasVM",
    "workloadType": "VM",
    "containerName": "iaasvmcontainerv2;testRG;testVM",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/DefaultPolicy",
    "policyName": "DefaultPolicy"
  }
}
```

Isso confirma que a proteção está habilitada para a VM e o primeiro backup será disparado de acordo com o agendamento da política.

### <a name="excluding-disks-in-azure-vm-backup"></a>Excluindo discos no backup de VM do Azure

O backup do Azure também fornece uma maneira de fazer backup seletivo de um subconjunto de discos na VM do Azure. Mais detalhes são fornecidos [aqui](selective-disk-backup-restore.md). Se você quiser fazer backup seletivo de alguns discos durante a habilitação da proteção, o trecho de código a seguir deverá ser o [corpo da solicitação durante a habilitação da proteção](#example-request-body).

```json
{
"properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/DefaultPolicy",
    "extendedProperties":  {
      "diskExclusionProperties":{
          "diskLunList":[0,1],
          "isInclusionList":true
        }
    }
}
}
```

No corpo da solicitação acima, a lista de discos de backup é fornecida na seção Propriedades estendidas.

|Propriedade  |Valor  |
|---------|---------|
|diskLunList     | A lista de LUN de disco é uma lista de *LUNs de discos de dados*. O **disco do so sempre é submetido a backup e não precisa ser mencionado**.        |
|Isinclusõeslist     | Deve ser **verdadeiro** para que os LUNs sejam incluídos durante o backup. Se for **false**, os LUNs mencionados anteriormente serão excluídos.         |

Portanto, se o requisito for fazer backup apenas do disco do sistema operacional, _todos os_ discos de dados deverão ser excluídos. Uma maneira mais fácil é dizer que nenhum disco de dados deve ser incluído. Portanto, a lista de LUN de disco estará vazia e a **Isinclusõeslist** será **verdadeira**. Da mesma forma, imagine qual é a maneira mais fácil de selecionar um subconjunto: alguns discos devem ser sempre excluídos ou alguns discos devem ser sempre incluídos. Escolha a lista de LUNs e o valor da variável booliano de acordo.

## <a name="trigger-an-on-demand-backup-for-a-protected-azure-vm"></a>Disparar um backup sob demanda para uma VM do Azure protegida

Depois que uma VM do Azure é configurada para backup, os backups acontecem de acordo com o agendamento da política. Você pode esperar pelo primeiro backup agendado ou disparar um backup sob demanda a qualquer momento. A retenção dos backups sob demanda é separada da retenção da política de backup e pode ser especificada para uma determinada data e hora. Se não for especificada, presume-se que ela ocorra 30 dias a partir do dia em que o backup sob demanda foi disparado.

Disparar um backup sob demanda é uma operação *POST*.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/backup?api-version=2016-12-01
```

`{containerName}` e `{protectedItemName}` têm a construção descrita [acima](#responses-to-get-operation). O `{fabricName}` é "Azure". Para nosso exemplo, isso é traduzido como:

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM/backup?api-version=2016-12-01
```

### <a name="create-the-request-body-for-on-demand-backup"></a>Criar o corpo da solicitação para backup sob demanda

Para disparar um backup sob demanda, a seguir estão os componentes do corpo da solicitação.

|Nome  |Tipo  |Descrição  |
|---------|---------|---------|
|properties     | [IaaSVMBackupRequest](/rest/api/backup/backups/trigger#iaasvmbackuprequest)        |Propriedades de BackupRequestResource         |

Para obter uma lista de definições de corpo da solicitação e outros detalhes, veja o [documento sobre como disparar backups de itens protegidos da API REST](/rest/api/backup/backups/trigger#request-body).

#### <a name="example-request-body-for-on-demand-backup"></a>Corpo de solicitação de exemplo para backup sob demanda

O corpo da solicitação a seguir define as propriedades necessárias para disparar um backup para um item protegido. Se a retenção não for especificada, ela será retida por 30 dias a partir da hora do gatilho do trabalho de backup.

```json
{
   "properties": {
    "objectType": "IaasVMBackupRequest",
    "recoveryPointExpiryTimeInUTC": "2018-12-01T02:16:20.3156909Z"
  }
}
```

### <a name="responses-for-on-demand-backup"></a>Respostas para backup sob demanda

Disparar um backup sob demanda é uma [operação assíncrona](../azure-resource-manager/management/async-operations.md). Isso significa que essa operação cria outra operação que precisa ser rastreada separadamente.

Ele retorna duas respostas: 202 (Aceito) quando outra operação é criada e, em seguida, 200 (OK) quando a operação é concluída.

|Nome  |Tipo  |Descrição  |
|---------|---------|---------|
|202 Aceito     |         |     Aceito    |

#### <a name="example-responses-for-on-demand-backup"></a>Respostas de exemplo para backup sob demanda

Depois de enviar a solicitação *POST* para um backup sob demanda, a resposta inicial é 202 (Aceito) com um cabeçalho de localização ou cabeçalho assíncrono do Azure.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testVaultRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/b8daecaa-f8f5-44ed-9f18-491a9e9ba01f?api-version=2019-05-13
X-Content-Type-Options: nosniff
x-ms-request-id: 7885ca75-c7c6-43fb-a38c-c0cc437d8810
x-ms-client-request-id: 7df8e874-1d66-4f81-8e91-da2fe054811d; 7df8e874-1d66-4f81-8e91-da2fe054811d
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 7885ca75-c7c6-43fb-a38c-c0cc437d8810
x-ms-routing-request-id: SOUTHINDIA:20180521T083541Z:7885ca75-c7c6-43fb-a38c-c0cc437d8810
Cache-Control: no-cache
Date: Mon, 21 May 2018 08:35:41 GMT
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testVaultRG;testVM/protectedItems/vm;testRG;testVM/operationResults/b8daecaa-f8f5-44ed-9f18-491a9e9ba01f?api-version=2019-05-13
X-Powered-By: ASP.NET
```

Em seguida, rastreie a operação resultante usando o cabeçalho de localização ou o cabeçalho Azure-AsyncOperation com um simples comando *GET*.

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2019-05-13
```

Depois que a operação for concluída, ela retornará 200 (OK) com a ID da tarefa de backup resultante no corpo da resposta.

```json
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: a8b13524-2c95-445f-b107-920806f385c1
x-ms-client-request-id: 5a63209d-3708-4e69-a75f-9499f4c8977c; 5a63209d-3708-4e69-a75f-9499f4c8977c
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14995
x-ms-correlation-request-id: a8b13524-2c95-445f-b107-920806f385c1
x-ms-routing-request-id: SOUTHINDIA:20180521T083723Z:a8b13524-2c95-445f-b107-920806f385c1
Cache-Control: no-cache
Date: Mon, 21 May 2018 08:37:22 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "00000000-0000-0000-0000-000000000000",
  "status": "Succeeded",
  "startTime": "2018-05-21T08:35:40.9488967Z",
  "endTime": "2018-05-21T08:35:40.9488967Z",
  "properties": {
    "objectType": "OperationStatusJobExtendedInfo",
    "jobId": "7ddead57-bcb9-4269-ac31-6a1b57588700"
  }
}
```

Como a tarefa de backup é uma operação longa, ela precisa ser rastreada conforme explicado [no documento sobre monitoramento de trabalhos usando a API REST](backup-azure-arm-userestapi-managejobs.md#tracking-the-job).

## <a name="modify-the-backup-configuration-for-a-protected-azure-vm"></a>Modificar a configuração de backup para uma VM do Azure protegida

### <a name="changing-the-policy-of-protection"></a>Alterar a política de proteção

Para alterar a política com que a VM é protegida, você pode usar o mesmo formato que para [habilitar a proteção](#enabling-protection-for-the-azure-vm). Basta fornecer a ID da nova política no [corpo da solicitação](#example-request-body) e enviar a solicitação. Por exemplo: para alterar a política de testVM de ' DefaultPolicy ' para ' ProdPolicy ', forneça a ID ' ProdPolicy ' no corpo da solicitação.

```json
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/ProdPolicy"
  }
}
```

A resposta seguirá o mesmo formato mencionado [para habilitar a proteção](#responses-to-create-protected-item-operation)

#### <a name="excluding-disks-during-azure-vm-protection"></a>Excluindo discos durante a proteção de VM do Azure

Se o backup da VM do Azure já tiver sido feito, você poderá especificar a lista de discos para backup ou exclusão, alterando a política de proteção. Basta preparar a solicitação no mesmo formato que [excluir discos durante a habilitação da proteção](#excluding-disks-in-azure-vm-backup)

> [!IMPORTANT]
> O corpo da solicitação acima é sempre a cópia final dos discos de dados a ser excluída ou incluída. Isso não é *adicionado* à configuração anterior. Por exemplo: se você primeiro atualizar a proteção como "excluir disco de dados 1" e, em seguida, repetir com "excluir disco de dados 2", *somente o disco de dados 2 será excluído* nos backups subsequentes e o disco de dados 1 será incluído. Essa é sempre a lista final que será incluída/excluída nos backups subsequentes.

Para obter a lista atual de discos que são excluídos ou incluídos, obtenha as informações do item protegido, conforme mencionado [aqui](/rest/api/backup/protecteditems/get). A resposta fornecerá a lista de LUNs de disco de dados e indicará se eles estão incluídos ou excluídos.

### <a name="stop-protection-but-retain-existing-data"></a>Interromper a proteção, mas manter os dados existentes

Para remover a proteção em uma VM protegida, mas manter os dados dos quais já foi feito backup, remova a política no corpo da solicitação e envie a solicitação. Após a associação com a política ser removida, não serão disparados mais backups e não serão criados pontos de recuperação.

```http
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": ""
  }
}
```

A resposta seguirá o mesmo formato mencionado [para disparar um backup sob demanda](#example-responses-for-on-demand-backup). O trabalho resultante deve ser monitorado conforme explicado no [documento sobre monitoramento de trabalhos usando a API REST](backup-azure-arm-userestapi-managejobs.md#tracking-the-job).

### <a name="stop-protection-and-delete-data"></a>Interromper a proteção e excluir dados

Para remover a proteção em uma VM protegida e excluir os dados de backup, execute uma operação de exclusão, conforme detalhado [aqui](/rest/api/backup/protecteditems/delete).

Interromper a proteção e excluir dados é uma operação *DELETE*.

```http
DELETE https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

`{containerName}` e `{protectedItemName}` têm a construção descrita [acima](#responses-to-get-operation). `{fabricName}` é o "Azure". Para nosso exemplo, isso é traduzido como:

```http
DELETE https://management.azure.com//Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM?api-version=2019-05-13
```

#### <a name="responses-for-delete-protection"></a>Respostas para a proteção de exclusão

*EXCLUIR* a proteção é uma [operação assíncrona](../azure-resource-manager/management/async-operations.md). Isso significa que essa operação cria outra operação que precisa ser rastreada separadamente.

Ele retorna duas respostas: 202 (Aceito) quando outra operação é criada e, em seguida, 204 (NoContent) quando a operação é concluída.

|Nome  |Tipo  |Descrição  |
|---------|---------|---------|
|204 NoContent     |         |  NoContent       |
|202 Aceito     |         |     Aceito    |

> [!IMPORTANT]
> Para se proteger contra cenários de exclusão acidental, há um [recurso de exclusão reversível disponível](use-restapi-update-vault-properties.md#soft-delete-state) para o cofre dos serviços de recuperação. Se o estado de exclusão reversível do cofre for definido como habilitado, a operação de exclusão não excluirá imediatamente os dados. Ele será mantido por 14 dias e, em seguida, limpo permanentemente. Você não é cobrado pelo armazenamento durante esse período de 14 dias. Para desfazer a operação de exclusão, consulte a [seção desfazer-excluir](#undo-the-deletion).

### <a name="undo-the-deletion"></a>Desfazer a exclusão

Desfazer a exclusão acidental é semelhante à criação do item de backup. Depois de desfazer a exclusão, o item é retido, mas nenhum backup futuro é disparado.

Desfazer a exclusão é uma operação *Put* que é muito semelhante a [alterar a política](#changing-the-policy-of-protection) e/ou [habilitar a proteção](#enabling-protection-for-the-azure-vm). Basta fornecer a intenção de desfazer a exclusão com a variável *isRehydrate*  no [corpo da solicitação](#example-request-body) e enviar a solicitação. Por exemplo: para desfazer a exclusão de testVM, o corpo da solicitação a seguir deve ser usado.

```http
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "protectionState": "ProtectionStopped",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "isRehydrate": true
  }
}
```

A resposta seguirá o mesmo formato mencionado [para disparar um backup sob demanda](#example-responses-for-on-demand-backup). O trabalho resultante deve ser monitorado conforme explicado no [documento sobre monitoramento de trabalhos usando a API REST](backup-azure-arm-userestapi-managejobs.md#tracking-the-job).

## <a name="next-steps"></a>Próximas etapas

[Restaurar dados de um backup de máquina virtual do Azure](backup-azure-arm-userestapi-restoreazurevms.md).

Para obter mais informações sobre as APIs REST do Backup do Azure, consulte os seguintes documentos:

- [API REST do provedor de serviços de recuperação do Azure](/rest/api/recoveryservices/)
- [Iniciar com a API REST do Azure](/rest/api/azure/)