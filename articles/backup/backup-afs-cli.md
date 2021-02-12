---
title: Fazer backup de compartilhamentos de arquivos do Azure com CLI do Azure
description: Saiba como usar CLI do Azure para fazer backup de compartilhamentos de arquivos do Azure no cofre dos serviços de recuperação
ms.topic: conceptual
ms.date: 01/14/2020
ms.openlocfilehash: 34eea8daa6a0a8920c842178664055838b06a78a
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94565884"
---
# <a name="back-up-azure-file-shares-with-azure-cli"></a>Fazer backup de compartilhamentos de arquivos do Azure com CLI do Azure

A CLI (interface de linha de comando) do Azure fornece uma experiência de linha de comando para gerenciar recursos do Azure. É uma excelente ferramenta para a criação de automação personalizada para usar os recursos do Azure. Este artigo fornece detalhes sobre como fazer backup de compartilhamentos de arquivos do Azure com CLI do Azure. Você também pode executar essas etapas usando o [Azure PowerShell](./backup-azure-afs-automation.md) ou o [portal do Azure](backup-afs.md).

Ao final deste tutorial, você aprenderá a executar as operações abaixo com CLI do Azure:

* Criar um cofre dos Serviços de Recuperação
* Habilitar backup para compartilhamentos de arquivos do Azure
* Disparar um backup sob demanda para compartilhamentos de arquivos

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Este tutorial requer a versão 2.0.18 ou posterior do CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação

Um cofre dos serviços de recuperação é uma entidade que oferece um recurso de gerenciamento e exibição consolidado em todos os itens de backup. Quando o trabalho de backup para um recurso protegido é executado, ele cria um ponto de recuperação no cofre dos Serviços de Recuperação. Você pode usar um desses pontos de recuperação para restaurar dados para um determinado ponto no tempo.

Siga estas etapas para criar um cofre dos serviços de recuperação:

1. Um cofre é colocado em um grupo de recursos. Se você não tiver um grupo de recursos existente, crie um novo com [AZ Group Create](/cli/azure/group#az-group-create) . Neste tutorial, criamos o novo grupo de recursos *azurefiles* na região leste dos EUA.

    ```azurecli-interactive
    az group create --name AzureFiles --location eastus --output table
    ```

    ```output
    Location    Name
    ----------  ----------
    eastus      AzureFiles
    ```

1. Use o cmdlet [AZ backup Vault Create](/cli/azure/backup/vault#az-backup-vault-create) para criar o cofre. Especifique o mesmo local para o cofre usado para o grupo de recursos.

    O exemplo a seguir cria um cofre dos serviços de recuperação chamado *azurefilesvault* na região leste dos EUA.

    ```azurecli-interactive
    az backup vault create --resource-group azurefiles --name azurefilesvault --location eastus --output table
    ```

    ```output
    Location    Name                ResourceGroup
    ----------  ----------------    ---------------
    eastus      azurefilesvault     azurefiles
    ```

## <a name="enable-backup-for-azure-file-shares"></a>Habilitar backup para compartilhamentos de arquivos do Azure

Esta seção pressupõe que você já tenha um compartilhamento de arquivos do Azure para o qual deseja configurar o backup. Se você não tiver uma, crie um compartilhamento de arquivos do Azure usando o comando [AZ Storage share Create](/cli/azure/storage/share#az-storage-share-create) .

Para habilitar o backup de compartilhamentos de arquivos, você precisa criar uma política de proteção que define quando um trabalho de backup é executado e por quanto tempo os pontos de recuperação são armazenados. Você pode criar uma política de backup usando o cmdlet [AZ backup Policy Create](/cli/azure/backup/policy#az-backup-policy-create) .

O exemplo a seguir usa o cmdlet [AZ backup Protection enable-for-azurefileshare](/cli/azure/backup/protection#az-backup-protection-enable-for-azurefileshare) para habilitar o backup para o compartilhamento de arquivos *azurefiles* na conta de armazenamento *afsaccount* usando a política de backup do *Schedule 1* :

```azurecli-interactive
az backup protection enable-for-azurefileshare --vault-name azurefilesvault --resource-group  azurefiles --policy-name schedule1 --storage-account afsaccount --azure-file-share azurefiles  --output table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
0caa93f4-460b-4328-ac1d-8293521dd928  azurefiles
```

O atributo **Name** na saída corresponde ao nome do trabalho que é criado pelo serviço de backup para a operação de **habilitar backup** . Para acompanhar o status do trabalho, use o cmdlet [AZ backup Job show](/cli/azure/backup/job#az-backup-job-show) .

## <a name="trigger-an-on-demand-backup-for-file-share"></a>Disparar um backup sob demanda para compartilhamento de arquivos

Se você quiser disparar um backup sob demanda para seu compartilhamento de arquivos em vez de aguardar que a política de backup execute o trabalho no horário agendado, use o cmdlet [AZ backup Protection Backup-Now](/cli/azure/backup/protection#az-backup-protection-backup-now) .

Você precisa definir os seguintes parâmetros para disparar um backup sob demanda:

* **--container-Name** é o nome da conta de armazenamento que hospeda o compartilhamento de arquivos. Para recuperar o **nome** ou **nome amigável** do seu contêiner, use o comando [AZ backup container List](/cli/azure/backup/container#az-backup-container-list) .
* **--Item-Name** é o nome do compartilhamento de arquivos para o qual você deseja disparar um backup sob demanda. Para recuperar o **nome** ou **nome amigável** de seu item de backup, use o comando [AZ backup item List](/cli/azure/backup/item#az-backup-item-list) .
* **--Retain-until** especifica a data até o momento em que você deseja manter o ponto de recuperação. O valor deve ser definido no formato de hora UTC (dd-mm-aaaa).

O exemplo a seguir dispara um backup sob demanda para o FileShare *azurefiles* na conta de armazenamento *afsaccount* com retenção até *20-01-2020*.

```azurecli-interactive
az backup protection backup-now --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --retain-until 20-01-2020 --output table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
9f026b4f-295b-4fb8-aae0-4f058124cb12  azurefiles
```

O atributo **Name** na saída corresponde ao nome do trabalho que é criado pelo serviço de backup para a operação de "backup sob demanda". Para acompanhar o status de um trabalho, use o cmdlet [AZ backup Job show](/cli/azure/backup/job#az-backup-job-show) .

## <a name="next-steps"></a>Próximas etapas

* Saiba como [restaurar compartilhamentos de arquivos do Azure com a CLI](restore-afs-cli.md)
* Saiba como [gerenciar backups de compartilhamento de arquivos do Azure com a CLI](manage-afs-backup-cli.md)
