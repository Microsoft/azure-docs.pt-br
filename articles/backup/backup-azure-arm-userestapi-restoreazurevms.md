---
title: Restaurar VMs do Azure usando a API REST
description: Neste artigo, saiba como gerenciar operações de restauração do backup de máquina virtual do Azure usando a API REST.
ms.topic: conceptual
ms.date: 09/12/2018
ms.assetid: b8487516-7ac5-4435-9680-674d9ecf5642
ms.openlocfilehash: da6b4cd6134f0cd1fd3d6e04e814bbf8aec9b07d
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2021
ms.locfileid: "102452145"
---
# <a name="restore-azure-virtual-machines-using-rest-api"></a>Restaurar máquinas virtuais do Azure usando a API REST

Depois que o backup de uma máquina virtual do Azure usando o backup do Azure for concluído, será possível restaurar as máquinas virtuais ou os discos ou arquivos inteiros do Azure da mesma cópia de backup. Este artigo descreve como restaurar uma VM ou discos do Azure usando a API REST.

Para qualquer operação de restauração, é necessário identificar primeiro o ponto de recuperação relevante.

## <a name="select-recovery-point"></a>Selecione um ponto de recuperação

Os pontos de recuperação disponíveis de um item de backup podem ser listados usando a [API REST do ponto de recuperação da lista](/rest/api/backup/recoverypoints/list). É uma operação *Get* simples com todos os valores relevantes.

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints?api-version=2019-05-13
```

O `{containerName}` e `{protectedItemName}` são como construídos [aqui](backup-azure-arm-userestapi-backupazurevms.md#example-responses-to-get-operation). `{fabricName}` é o "Azure".

O URI *GET* tem todos os parâmetros necessários. Não há necessidade de um corpo de solicitação adicional.

### <a name="responses"></a>Respostas

|Nome  |Type  |Descrição  |
|---------|---------|---------|
|200 OK     |   [RecoveryPointResourceList](/rest/api/backup/recoverypoints/list#recoverypointresourcelist)      |       OK  |

#### <a name="example-response"></a>Exemplo de resposta

Depois que o *GET* URI for enviado, uma resposta 200 (OK) será retornada.

```http
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: 03453538-2f8d-46de-8374-143ccdf60f40
x-ms-client-request-id: c48f4436-ce3f-42da-b537-12710d4d1a24; c48f4436-ce3f-42da-b537-12710d4d1a24
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14998
x-ms-correlation-request-id: 03453538-2f8d-46de-8374-143ccdf60f40
x-ms-routing-request-id: SOUTHINDIA:20180604T071851Z:03453538-2f8d-46de-8374-143ccdf60f40
Cache-Control: no-cache
Date: Mon, 04 Jun 2018 07:18:51 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/VM;testRG;testVM/recoveryPoints/20982486783671",
      "name": "20982486783671",
      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints",
      "properties": {
        "objectType": "IaasVMRecoveryPoint",
        "recoveryPointType": "AppConsistent",
        "recoveryPointTime": "2018-06-04T06:06:00.5121087Z",
        "recoveryPointAdditionalInfo": "",
        "sourceVMStorageType": "NormalStorage",
        "isSourceVMEncrypted": false,
        "isInstantIlrSessionActive": false,
        "recoveryPointTierDetails": [
          {
            "type": 1,
            "status": 1
          },
          {
            "type": 2,
            "status": 1
          }
        ],
        "isManagedVirtualMachine": true,
        "virtualMachineSize": "Standard_A1_v2",
        "originalStorageAccountOption": false
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/VM;testRG;testVM/recoveryPoints/23358112038108",
      "name": "23358112038108",
      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints",
      "properties": {
        "objectType": "IaasVMRecoveryPoint",
        "recoveryPointType": "CrashConsistent",
        "recoveryPointTime": "2018-06-03T06:20:56.3043878Z",
        "recoveryPointAdditionalInfo": "",
        "sourceVMStorageType": "NormalStorage",
        "isSourceVMEncrypted": false,
        "isInstantIlrSessionActive": false,
        "recoveryPointTierDetails": [
          {
            "type": 1,
            "status": 1
          },
          {
            "type": 2,
            "status": 1
          }
        ],
        "isManagedVirtualMachine": true,
        "virtualMachineSize": "Standard_A1_v2",
        "originalStorageAccountOption": false
      }
    },
......
```

O ponto de recuperação é identificado com o campo `{name}` na resposta acima.

## <a name="restore-operations"></a>Operações de restauração

Depois de selecionar o [ponto de restauração relevante](#select-recovery-point), vá para disparar a operação de restauração.

***Todas as operações de restauração no item de backup são executadas com a mesma API *post* . Somente o corpo da solicitação muda com os cenários de restauração.***

> [!IMPORTANT]
> Todos os detalhes sobre várias opções de restauração e suas dependências são mencionados [aqui](./backup-azure-arm-restore-vms.md#restore-options). Examine antes de continuar a disparar essas operações.

Disparar operações de restauração é uma solicitação *post* . Para saber mais sobre a API, consulte a [API REST "disparar Restore"](/rest/api/backup/restores/trigger).

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints/{recoveryPointId}/restore?api-version=2019-05-13
```

O `{containerName}` e `{protectedItemName}` são como construídos [aqui](backup-azure-arm-userestapi-backupazurevms.md#example-responses-to-get-operation). `{fabricName}` é "Azure" e `{recoveryPointId}` é o campo `{name}` do ponto de recuperação mencionado [ acima de ](#example-response).

Depois que o ponto de recuperação é obtido, precisamos construir o corpo da solicitação para o cenário de restauração relevante. As seções a seguir descrevem o corpo da solicitação para cada cenário.

- [Restaurar discos](#restore-disks)
- [Substituir discos](#replace-disks-in-a-backed-up-virtual-machine)
- [Restaurar como uma nova máquina virtual](#restore-as-another-virtual-machine)

### <a name="restore-response"></a>Resposta de restauração

O disparo de qualquer operação de restauração é uma [operação assíncrona](../azure-resource-manager/management/async-operations.md). Isso significa que essa operação cria outra operação que precisa ser rastreada separadamente.

Ele retorna duas respostas: 202 (Aceito) quando outra operação é criada e, em seguida, 200 (OK) quando a operação é concluída.

|Nome  |Type  |Descrição  |
|---------|---------|---------|
|202 Aceito     |         |     Aceito    |

#### <a name="example-responses"></a>Respostas de exemplo

Depois de enviar o URI *POST* para acionar os discos de restauração, a resposta inicial é 202 (Aceito) com um cabeçalho de local ou um cabeçalho assíncrono do Azure.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/781a0f18-e250-4d73-b059-5e9ffed4069e?api-version=2019-05-13
X-Content-Type-Options: nosniff
x-ms-request-id: 893fe372-8d6c-4c56-b589-45a95eeef95f
x-ms-client-request-id: a15ce064-25bd-4ac6-87e5-e3bc6ec65c0b; a15ce064-25bd-4ac6-87e5-e3bc6ec65c0b
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1198
x-ms-correlation-request-id: 893fe372-8d6c-4c56-b589-45a95eeef95f
x-ms-routing-request-id: SOUTHINDIA:20180604T130003Z:893fe372-8d6c-4c56-b589-45a95eeef95f
Cache-Control: no-cache
Date: Mon, 04 Jun 2018 13:00:03 GMT
Location: https://management.azure.com/subscriptions//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationResults/781a0f18-e250-4d73-b059-5e9ffed4069e?api-version=2019-05-13
X-Powered-By: ASP.NET
```

Em seguida, rastreie a operação resultante usando o cabeçalho de localização ou o cabeçalho Azure-AsyncOperation com um simples comando *GET*.

```http
GET https://management.azure.com/subscriptions//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationResults/781a0f18-e250-4d73-b059-5e9ffed4069e?api-version=2019-05-13
```

Depois que a operação for concluída, ela retornará 200 (OK) com o ID da tarefa de restauração resultante no corpo da resposta.

```http
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: ea2a8011-eb83-4a4b-9ed2-e694070a966a
x-ms-client-request-id: a7f3a144-ed80-41ee-bffe-ae6a90c35a2f; a7f3a144-ed80-41ee-bffe-ae6a90c35a2f
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14973
x-ms-correlation-request-id: ea2a8011-eb83-4a4b-9ed2-e694070a966a
x-ms-routing-request-id: SOUTHINDIA:20180604T130917Z:ea2a8011-eb83-4a4b-9ed2-e694070a966a
Cache-Control: no-cache
Date: Mon, 04 Jun 2018 13:09:17 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "id": "781a0f18-e250-4d73-b059-5e9ffed4069e",
  "name": "781a0f18-e250-4d73-b059-5e9ffed4069e",
  "status": "Succeeded",
  "startTime": "2018-06-04T13:00:03.8068176Z",
  "endTime": "2018-06-04T13:00:03.8068176Z",
  "properties": {
    "objectType": "OperationStatusJobExtendedInfo",
    "jobId": "3021262a-fb3a-4538-9b37-e3e97a386093"
  }
}
```

Como o trabalho de restauração é uma operação de execução demorada, ele deve ser acompanhado conforme explicado no [documento monitorar trabalhos usando a API REST](backup-azure-arm-userestapi-managejobs.md#tracking-the-job).

### <a name="restore-disks"></a>Restaurar discos

Se houver a necessidade de personalizar a criação de uma VM a partir dos dados de backup, você poderá apenas restaurar os discos em uma conta de armazenamento escolhida e criar uma VM a partir desses discos de acordo com seus requisitos. A conta de armazenamento deve estar na mesma região que o cofre dos serviços de recuperação e não deve ter redundância de zona. Os discos, bem como a configuração da VM de backup ("vmconfig.jsem"), serão armazenados na conta de armazenamento determinada. Conforme explicado [acima](#restore-operations), o corpo de solicitação relevante para discos de restauração é fornecido abaixo.

#### <a name="create-request-body"></a>Criar o corpo da solicitação

Para acionar uma restauração de disco a partir de um backup de VM do Azure, a seguir estão os componentes do corpo da solicitação.

|Nome  |Type  |Descrição  |
|---------|---------|---------|
|properties     | [IaaSVMRestoreRequest](/rest/api/backup/restores/trigger#iaasvmrestorerequest)        |    RestoreRequestResourceProperties     |

Para obter uma lista de definições de corpo da solicitação e outros detalhes, consulte [disparar o documento de API de REST restaurar](/rest/api/backup/restores/trigger#request-body).

##### <a name="example-request"></a>Solicitação de exemplo

O corpo de solicitação a seguir define as propriedades necessárias para disparar a restauração de um disco.

```json
{
  "properties": {
    "objectType": "IaasVMRestoreRequest",
    "recoveryPointId": "20982486783671",
    "recoveryType": "RestoreDisks",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "storageAccountId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Storage/storageAccounts/testAccount",
    "region": "westus",
    "createNewCloudService": false,
    "originalStorageAccountOption": false,
    "encryptionDetails": {
      "encryptionEnabled": false
    }
  }
}
```

### <a name="restore-disks-selectively"></a>Restaurar discos de forma seletiva

Se você estiver [fazendo backup seletivo de discos](backup-azure-arm-userestapi-backupazurevms.md#excluding-disks-in-azure-vm-backup), a lista de discos de backup atuais será fornecida no resumo do ponto de [recuperação](#select-recovery-point) e na [resposta detalhada](/rest/api/backup/recoverypoints/get). Você também pode restaurar discos seletivamente e mais detalhes são fornecidos [aqui](selective-disk-backup-restore.md#selective-disk-restore). Para restaurar seletivamente um disco entre a lista de discos de backup, localize o LUN do disco da resposta do ponto de recuperação e adicione a propriedade **restoreDiskLunList** ao corpo da [solicitação acima](#example-request) , conforme mostrado abaixo.

```json
{
    "properties": {
        "objectType": "IaasVMRestoreRequest",
        "recoveryPointId": "20982486783671",
        "recoveryType": "RestoreDisks",
        "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
        "storageAccountId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Storage/storageAccounts/testAccount",
        "region": "westus",
        "createNewCloudService": false,
        "originalStorageAccountOption": false,
        "encryptionDetails": {
          "encryptionEnabled": false
        },
        "restoreDiskLunList" : [0]
    }
}

```

Quando você acompanha a resposta conforme explicado [acima](#responses)e o trabalho de execução longa está concluído, os discos e a configuração da máquina virtual de backup ("VMConfig.jsem") estarão presentes na conta de armazenamento determinada.

### <a name="replace-disks-in-a-backed-up-virtual-machine"></a>Substituir discos em uma máquina virtual com backup

Enquanto os discos de restauração criam discos do ponto de recuperação, o Replace disks substitui os discos atuais da VM de backup pelos discos do ponto de recuperação. Conforme explicado [acima](#restore-operations), o corpo de solicitação relevante para a substituição de discos é fornecido abaixo.

#### <a name="create-request-body"></a>Criar o corpo da solicitação

Para disparar uma substituição de disco de um backup de VM do Azure, estes são os componentes do corpo da solicitação.

|Nome  |Type  |Descrição  |
|---------|---------|---------|
|properties     | [IaaSVMRestoreRequest](/rest/api/backup/restores/trigger#iaasvmrestorerequest)        |    RestoreRequestResourceProperties     |

Para obter uma lista de definições de corpo da solicitação e outros detalhes, consulte [disparar o documento de API de REST restaurar](/rest/api/backup/restores/trigger#request-body).

#### <a name="example-request"></a>Solicitação de exemplo

O corpo de solicitação a seguir define as propriedades necessárias para disparar a restauração de um disco.

```json
{
    "properties": {
        "objectType": "IaasVMRestoreRequest",
        "recoveryPointId": "20982486783671",
        "recoveryType": "OriginalLocation",
        "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
        "storageAccountId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Storage/storageAccounts/testAccount",  
        "region": "westus",
        "createNewCloudService": false,
        "originalStorageAccountOption": false,
        "affinityGroup": "",
        "diskEncryptionSetId": null,
        "subnetId": null,
        "targetDomainNameId": null,
        "targetResourceGroupId": null,
        "targetVirtualMachineId": null,
        "virtualNetworkId": null
     }
}

```

### <a name="restore-as-another-virtual-machine"></a>Restaurar como outra máquina virtual

Conforme explicado [acima](#restore-operations), o corpo da solicitação a seguir define as propriedades necessárias para disparar uma restauração de máquina virtual.

```json
{
  "properties": {
          "objectType":  "IaasVMRestoreRequest",
          "recoveryPointId": "348916168024334",
          "recoveryType": "AlternateLocation",
          "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
          "targetVirtualMachineId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG/providers/Microsoft.Compute/virtualmachines/targetVM",
          "targetResourceGroupId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG",
          "storageAccountId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Storage/storageAccounts/testingAccount",
          "virtualNetworkId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG/providers/Microsoft.Network/virtualNetworks/testNet",
          "subnetId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG/providers/Microsoft.Network/virtualNetworks/testNet/subnets/default",
          "region": "westus",
          "createNewCloudService": false,
          "originalStorageAccountOption": false,
          "encryptionDetails": {
            "encryptionEnabled": false
     }
 }
```

A resposta deve ser tratada da mesma forma como [explicado acima para restaurar discos](#responses).

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre as APIs REST do Backup do Azure, consulte os seguintes documentos:

- [API REST do provedor de serviços de recuperação do Azure](/rest/api/recoveryservices/)
- [Iniciar com a API REST do Azure](/rest/api/azure/)
