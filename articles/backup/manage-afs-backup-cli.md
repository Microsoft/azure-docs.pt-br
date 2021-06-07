---
title: Gerenciar backups de compartilhamento de arquivos do Azure com o CLI do Azure
description: Saiba como usar o CLI do Azure para gerenciar e monitorar compartilhamentos de arquivos do Azure com backup feito pelo backup do Azure.
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: 5a8a785016845b836a102663a959e4b2f28696b6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94566445"
---
# <a name="manage-azure-file-share-backups-with-the-azure-cli"></a>Gerenciar backups de compartilhamento de arquivos do Azure com o CLI do Azure

O CLI do Azure fornece uma experiência de linha de comando para gerenciar recursos do Azure. É uma excelente ferramenta para a criação de automação personalizada para usar os recursos do Azure. Este artigo explica como executar tarefas para gerenciar e monitorar os compartilhamentos de arquivos do Azure cujo backup é feito pelo [backup do Azure](./backup-overview.md). Você também pode executar essas etapas com o [portal do Azure](https://portal.azure.com/).

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você já tenha um compartilhamento de arquivos do Azure com backup feito pelo [backup do Azure](./backup-overview.md). Se você não tiver um, consulte [fazer backup de compartilhamentos de arquivos do Azure com a CLI](backup-afs-cli.md) para configurar o backup de seus compartilhamentos de arquivos. Para este artigo, você usa os seguintes recursos:
   -  **Grupo de recursos**: *azurefiles*
   -  **RecoveryServicesVault**: *azurefilesvault*
   -  **Conta de armazenamento**: *afsaccount*
   -  **Compartilhamento de arquivos**: *azurefiles*
  
  [!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]
   - Este tutorial exige a versão 2.0.18 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="monitor-jobs"></a>Monitorar trabalhos

Quando você dispara operações de backup ou restauração, o serviço de backup cria um trabalho para acompanhamento. Para monitorar trabalhos concluídos ou em execução no momento, use o cmdlet [AZ backup Job List](/cli/azure/backup/job#az-backup-job-list) . Com a CLI, você também pode [suspender um trabalho em execução no momento](/cli/azure/backup/job#az-backup-job-stop) ou [aguardar até que um trabalho seja concluído](/cli/azure/backup/job#az-backup-job-wait).

O exemplo a seguir exibe o status dos trabalhos de backup para o cofre dos serviços de recuperação do *azurefilesvault* :

```azurecli-interactive
az backup job list --resource-group azurefiles --vault-name azurefilesvault
```

```output
[
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/d477dfb6-b292-4f24-bb43-6b14e9d06ab5",
    "location": null,
    "name": "d477dfb6-b292-4f24-bb43-6b14e9d06ab5",
    "properties": {
      "actionsInfo": null,
      "activityId": "3cef43ed-0af4-43e2-b9cb-1322c496ccb4",
      "backupManagementType": "AzureStorage",
      "duration": "0:00:29.718011",
      "endTime": "2020-01-13T08:05:29.180606+00:00",
      "entityFriendlyName": "azurefiles",
      "errorDetails": null,
      "extendedInfo": null,
      "jobType": "AzureStorageJob",
      "operation": "Backup",
      "startTime": "2020-01-13T08:04:59.462595+00:00",
      "status": "Completed",
      "storageAccountName": "afsaccount",
      "storageAccountVersion": "MicrosoftStorage"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupJobs"
  },
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/1b9399bf-c23c-4caa-933a-5fc2bf884519",
    "location": null,
    "name": "1b9399bf-c23c-4caa-933a-5fc2bf884519",
    "properties": {
      "actionsInfo": null,
      "activityId": "2663449c-94f1-4735-aaf9-5bb991e7e00c",
      "backupManagementType": "AzureStorage",
      "duration": "0:00:28.145216",
      "endTime": "2020-01-13T08:05:27.519826+00:00",
      "entityFriendlyName": "azurefilesresource",
      "errorDetails": null,
      "extendedInfo": null,
      "jobType": "AzureStorageJob",
      "operation": "Backup",
      "startTime": "2020-01-13T08:04:59.374610+00:00",
      "status": "Completed",
      "storageAccountName": "afsaccount",
      "storageAccountVersion": "MicrosoftStorage"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupJobs"
  }
]
```

## <a name="modify-policy"></a>Modificar a política

Você pode modificar uma política de backup para alterar a frequência de backup ou o período de retenção usando [AZ backup Item Set-Policy](/cli/azure/backup/item#az-backup-item-set-policy).

Para alterar a política, defina os seguintes parâmetros:

* **--container-Name**: o nome da conta de armazenamento que hospeda o compartilhamento de arquivos. Para recuperar o **nome** ou **nome amigável** do seu contêiner, use o comando [AZ backup container List](/cli/azure/backup/container#az-backup-container-list) .
* **--Name**: o nome do compartilhamento de arquivos para o qual você deseja alterar a política. Para recuperar o **nome** ou **nome amigável** de seu item de backup, use o comando [AZ backup item List](/cli/azure/backup/item#az-backup-item-list) .
* **--Policy-Name**: o nome da política de backup que você deseja definir para o compartilhamento de arquivos. Você pode usar a [lista de políticas de backup AZ](/cli/azure/backup/policy#az-backup-policy-list) para exibir todas as políticas para seu cofre.

O exemplo a seguir define a política de backup *schedule2* para o compartilhamento de arquivos *azurefiles* presente na conta de armazenamento *afsaccount* .

```azurecli-interactive
az backup item set-policy --policy-name schedule2 --name azurefiles --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --name "AzureFileShare;azurefiles" --backup-management-type azurestorage --out table
```

Você também pode executar o comando anterior usando os nomes amigáveis para o contêiner e o item, fornecendo os dois parâmetros adicionais a seguir:

* **--Gerenciamento de backup-tipo**: *AzureStorage*
* **--tipo de carga de trabalho**: *azurefileshare*

```azurecli-interactive
az backup item set-policy --policy-name schedule2 --name azurefiles --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --name azurefiles --backup-management-type azurestorage --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
fec6f004-0e35-407f-9928-10a163f123e5  azurefiles
```

O atributo **Name** na saída corresponde ao nome do trabalho que é criado pelo serviço de backup para a operação de alteração da política. Para acompanhar o status do trabalho, use o cmdlet [AZ backup Job show](/cli/azure/backup/job#az-backup-job-show) .

## <a name="stop-protection-on-a-file-share"></a>Interromper a proteção em um compartilhamento de arquivo

Há duas maneiras de interromper a proteção de compartilhamentos de arquivos do Azure:

* Pare todos os trabalhos de backup futuros e *exclua* todos os pontos de recuperação.
* Pare todos os trabalhos de backup futuros, mas *deixe* os pontos de recuperação.

Pode haver um custo associado à saída dos pontos de recuperação no armazenamento, pois os instantâneos subjacentes criados pelo backup do Azure serão mantidos. O benefício de deixar os pontos de recuperação é a opção de restaurar o compartilhamento de arquivos posteriormente, se desejar. Para obter informações sobre o custo de deixar os pontos de recuperação, confira os [detalhes de preços](https://azure.microsoft.com/pricing/details/storage/files). Se você optar por excluir todos os pontos de recuperação, não poderá restaurar o compartilhamento de arquivos.

Para interromper a proteção do compartilhamento de arquivos, defina os seguintes parâmetros:

* **--container-Name**: o nome da conta de armazenamento que hospeda o compartilhamento de arquivos. Para recuperar o **nome** ou **nome amigável** do seu contêiner, use o comando [AZ backup container List](/cli/azure/backup/container#az-backup-container-list) .
* **--Item-Name**: o nome do compartilhamento de arquivos para o qual você deseja interromper a proteção. Para recuperar o **nome** ou **nome amigável** de seu item de backup, use o comando [AZ backup item List](/cli/azure/backup/item#az-backup-item-list) .

### <a name="stop-protection-and-retain-recovery-points"></a>Interromper a proteção e manter os pontos de recuperação

Para interromper a proteção enquanto mantém os dados, use o cmdlet [AZ backup Protection Disable](/cli/azure/backup/protection#az-backup-protection-disable) .

O exemplo a seguir interrompe a proteção para o compartilhamento de arquivos *azurefiles* , mas retém todos os pontos de recuperação.

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name “AzureFileShare;azurefiles” --out table
```

Você também pode executar o comando anterior usando o nome amigável para o contêiner e o item fornecendo os dois parâmetros adicionais a seguir:

* **--Gerenciamento de backup-tipo**: *AzureStorage*
* **--tipo de carga de trabalho**: *azurefileshare*

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
fec6f004-0e35-407f-9928-10a163f123e5  azurefiles
```

O atributo **Name** na saída corresponde ao nome do trabalho que é criado pelo serviço de backup para a operação de interrupção de proteção. Para acompanhar o status do trabalho, use o cmdlet [AZ backup Job show](/cli/azure/backup/job#az-backup-job-show) .

### <a name="stop-protection-without-retaining-recovery-points"></a>Interromper a proteção sem reter pontos de recuperação

Para interromper a proteção sem reter pontos de recuperação, use o cmdlet [AZ backup Protection Disable](/cli/azure/backup/protection#az-backup-protection-disable) com a opção **DELETE-backup-data** definida como **true**.

O exemplo a seguir interrompe a proteção para o compartilhamento de arquivos *azurefiles* sem reter pontos de recuperação.

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name “AzureFileShare;azurefiles” --delete-backup-data true --out table
```

Você também pode executar o comando anterior usando o nome amigável para o contêiner e o item fornecendo os dois parâmetros adicionais a seguir:

* **--Gerenciamento de backup-tipo**: *AzureStorage*
* **--tipo de carga de trabalho**: *azurefileshare*

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --delete-backup-data true --out table
```

## <a name="resume-protection-on-a-file-share"></a>Retomar a proteção em um compartilhamento de arquivo

Se você interrompeu a proteção para um compartilhamento de arquivos do Azure, mas manteve pontos de recuperação, você pode retomar a proteção mais tarde. Se você não mantiver os pontos de recuperação, não poderá retomar a proteção.

Para retomar a proteção para o compartilhamento de arquivos, defina os seguintes parâmetros:

* **--container-Name**: o nome da conta de armazenamento que hospeda o compartilhamento de arquivos. Para recuperar o **nome** ou **nome amigável** do seu contêiner, use o comando [AZ backup container List](/cli/azure/backup/container#az-backup-container-list) .
* **--Item-Name**: o nome do compartilhamento de arquivos para o qual você deseja retomar a proteção. Para recuperar o **nome** ou **nome amigável** de seu item de backup, use o comando [AZ backup item List](/cli/azure/backup/item#az-backup-item-list) .
* **--Policy-Name**: o nome da política de backup para a qual você deseja retomar a proteção para o compartilhamento de arquivos.

O exemplo a seguir usa o cmdlet [AZ backup Protection resume](/cli/azure/backup/protection#az-backup-protection-resume) para retomar a proteção para o compartilhamento de arquivos *azurefiles* usando a política de backup *schedule1* .

```azurecli-interactive
az backup protection resume --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --policy-name schedule2 --out table
```

Você também pode executar o comando anterior usando o nome amigável para o contêiner e o item fornecendo os dois parâmetros adicionais a seguir:

* **--Gerenciamento de backup-tipo**: *AzureStorage*
* **--tipo de carga de trabalho**: *azurefileshare*

```azurecli-interactive
az backup protection resume --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --policy-name schedule2 --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
75115ab0-43b0-4065-8698-55022a234b7f  azurefiles
```

O atributo de **nome** na saída corresponde ao nome do trabalho criado pelo serviço de backup para a operação de retomada de proteção. Para acompanhar o status do trabalho, use o cmdlet [AZ backup Job show](/cli/azure/backup/job#az-backup-job-show) .

## <a name="unregister-a-storage-account"></a>Cancelar o registro de uma conta de armazenamento

Se você quiser proteger seus compartilhamentos de arquivos em uma determinada conta de armazenamento usando um cofre de serviços de recuperação diferente, primeiro [interrompa a proteção para todos os compartilhamentos de arquivos](#stop-protection-on-a-file-share) nessa conta de armazenamento. Em seguida, cancele o registro da conta do cofre dos serviços de recuperação usado atualmente para proteção.

Você precisa fornecer um nome de contêiner para cancelar o registro da conta de armazenamento. Para recuperar o **nome** ou o **nome amigável** do contêiner, use o comando [AZ backup container List](/cli/azure/backup/container#az-backup-container-list) .

O exemplo a seguir cancela o registro da conta de armazenamento *afsaccount* do *azurefilesvault* usando o cmdlet [AZ backup container Unregister](/cli/azure/backup/container#az-backup-container-unregister) .

```azurecli-interactive
az backup container unregister --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --out table
```

Você também pode executar o cmdlet anterior usando o nome amigável para o contêiner, fornecendo o seguinte parâmetro adicional:

* **--Gerenciamento de backup-tipo**: *AzureStorage*

```azurecli-interactive
az backup container unregister --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --backup-management-type azurestorage --out table
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte [Troubleshoot Azure File shares backup](troubleshoot-azure-files.md).
