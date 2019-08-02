---
title: Restaurar um disco de VM com o Backup do Azure
description: Saiba como restaurar um disco e criar uma VM de recuperação no Azure com serviços de backup e recuperação.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: tutorial
ms.date: 01/31/2019
ms.author: dacurwin
ms.custom: mvc
ms.openlocfilehash: 70431870027cc27d886995b0bf7f47108ad767fa
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67273946"
---
# <a name="restore-a-disk-and-create-a-recovered-vm-in-azure"></a>Restaurar um disco e criar uma VM recuperada no Azure
O Backup do Azure cria pontos de recuperação que são armazenados em cofres de recuperação com redundância geográfica. Ao restaurar de um ponto de recuperação, você pode restaurar a VM inteira ou arquivos individuais. Este artigo explica como restaurar uma VM completa usando a CLI. Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Liste e selecione os pontos de recuperação
> * Restaurar um disco de um ponto de recuperação
> * Criar uma VM do disco restaurado

Para obter informações sobre como usar o PowerShell para restaurar um disco e criar uma VM recuperada, confira [Backup e restauração de VMs do Azure com o PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este tutorial exigirá que você esteja executando a CLI do Azure versão 2.0.18 ou posterior. Execute `az --version` para encontrar a versão. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure]( /cli/azure/install-azure-cli). 


## <a name="prerequisites"></a>Pré-requisitos
Este tutorial requer uma VM do Linux que tenha sido protegida com o Backup do Azure. Para simular um processo de exclusão acidental e recuperação de VM, você pode criar uma VM de um disco em um ponto de recuperação. Se você precisar de uma VM do Linux que tenha sido protegida com o Backup do Azure, consulte [Back up a virtual machine in Azure with the CLI](quick-backup-vm-cli.md) (Fazer backup de uma máquina virtual no Azure com a CLI).


## <a name="backup-overview"></a>Visão geral do backup
Quando o Azure inicia um backup, a extensão de backup na VM cria um instantâneo pontual. A extensão de backup é instalada na VM quando o primeiro backup é solicitado. O Backup do Azure também poderá criar um instantâneo do armazenamento subjacente se a VM não estiver em execução quando o backup ocorrer.

Por padrão, o Backup do Azure usa um backup consistente com o sistema de arquivos. Depois que o Backup do Azure cria o instantâneo, os dados são transferidos para o cofre dos Serviços de Recuperação. Para maximizar a eficiência, o Backup do Azure identifica e transfere apenas os blocos de dados que foram alterados desde o backup anterior.

Quando a transferência de dados é concluída, o instantâneo é removido e um ponto de recuperação é criado.


## <a name="list-available-recovery-points"></a>Listar os pontos de recuperação disponíveis
Para restaurar um disco, selecione um ponto de recuperação como a origem dos dados de recuperação. Como a política padrão cria um ponto de recuperação por dia e os retém por 30 dias, você pode manter um conjunto de pontos de recuperação que permite selecionar um ponto específico para recuperação. 

Para ver uma lista de pontos de recuperação disponíveis, use a [az backup recoverypoint list](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-list). O **nome** do ponto de recuperação é usado para recuperar os discos. Neste tutorial, queremos o ponto de recuperação mais recente disponível. O parâmetro `--query [0].name` seleciona o nome do ponto de recuperação mais recente da seguinte maneira:

```azurecli-interactive
az backup recoverypoint list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --container-name myVM \
    --item-name myVM \
    --query [0].name \
    --output tsv
```


## <a name="restore-a-vm-disk"></a>Restaurar um disco de VM
Para restaurar o disco do ponto de recuperação, primeiro crie uma conta de armazenamento do Azure. Essa conta de armazenamento é usada para armazenar o disco restaurado. Nas etapas adicionais, o disco restaurado é usado para criar uma VM.

1. Para criar uma conta de armazenamento, use [az storage account create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create). O nome da conta de armazenamento deve conter apenas letras minúsculas e ser exclusivo globalmente. Substitua *mystorageaccount* pelo seu próprio nome exclusivo:

    ```azurecli-interactive
    az storage account create \
        --resource-group myResourceGroup \
        --name mystorageaccount \
        --sku Standard_LRS
    ```

2. Restaure o disco do seu ponto de recuperação com [az backup restore restore-disks](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-disks). Substitua *mystorageaccount* pelo nome da conta de armazenamento que você criou no comando anterior. Substitua *myRecoveryPointName* pelo nome do ponto de recuperação obtido na saída do comando [az backup recoverypoint list](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-list) anterior:

    ```azurecli-interactive
    az backup restore restore-disks \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --storage-account mystorageaccount \
        --rp-name myRecoveryPointName
    ```


## <a name="monitor-the-restore-job"></a>Monitorar o trabalho de restauração
Para monitorar o status do trabalho de restauração, use [az backup job list](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-list):

```azurecli-interactive 
az backup job list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --output table
```

O resultado é semelhante ao exemplo a seguir, que mostra que o trabalho de restauração está *InProgress*:

```
Name      Operation        Status      Item Name    Start Time UTC       Duration
--------  ---------------  ----------  -----------  -------------------  --------------
7f2ad916  Restore          InProgress  myvm         2017-09-19T19:39:52  0:00:34.520850
a0a8e5e6  Backup           Completed   myvm         2017-09-19T03:09:21  0:15:26.155212
fe5d0414  ConfigureBackup  Completed   myvm         2017-09-19T03:03:57  0:00:31.191807
```

Quando o *Status* dos relatórios de trabalho de restauração for *Concluído*, o disco terá sido restaurado para a conta de armazenamento.


## <a name="convert-the-restored-disk-to-a-managed-disk"></a>Converter o disco restaurado em um disco gerenciado
O trabalho de restauração cria um disco não gerenciado. Para criar uma VM do disco, primeiro ele deve ser convertido em um disco gerenciado.

1. Obtenha as informações de conexão da sua conta de armazenamento com [az storage account show-connection-string](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-show-connection-string). Substitua *mystorageaccount* pelo nome da conta de armazenamento da seguinte maneira:
    
    ```azurecli-interactive
    export AZURE_STORAGE_CONNECTION_STRING=$( az storage account show-connection-string \
        --resource-group myResourceGroup \
        --output tsv \
        --name mystorageaccount )
    ```

2. O disco não gerenciado está protegido na conta de armazenamento. Os comandos a seguir obtêm informações sobre o disco não gerenciado e criam uma variável denominada *uri* que será usada na próxima etapa, quando você criar o disco gerenciado.

    ```azurecli-interactive
    container=$(az storage container list --query [0].name -o tsv)
    blob=$(az storage blob list --container-name $container --query [0].name -o tsv)
    uri=$(az storage blob url --container-name $container --name $blob -o tsv)
    ```

3. Agora você pode criar um disco gerenciado usando o disco recuperado com [az disk create](https://docs.microsoft.com/cli/azure/disk?view=azure-cli-latest#az-disk-create). A variável *uri* da etapa anterior é usada como a origem para o disco gerenciado.

    ```azurecli-interactive
    az disk create \
        --resource-group myResourceGroup \
        --name myRestoredDisk \
        --source $uri
    ```

4. Como agora você tem um disco gerenciado do disco restaurado, limpe a conta de armazenamento e o disco não gerenciado com [az storage account delete](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-delete). Substitua *mystorageaccount* pelo nome da conta de armazenamento da seguinte maneira:

    ```azurecli-interactive
    az storage account delete \
        --resource-group myResourceGroup \
        --name mystorageaccount
    ```


## <a name="create-a-vm-from-the-restored-disk"></a>Criar uma VM do disco restaurado
A etapa final é criar uma VM do disco gerenciado.

1. Crie uma VM do disco gerenciados com [az vm create](/cli/azure/vm?view=azure-cli-latest#az-vm-create) da seguinte maneira:

    ```azurecli-interactive
    az vm create \
        --resource-group myResourceGroup \
        --name myRestoredVM \
        --attach-os-disk myRestoredDisk \
        --os-type linux
    ```

2. Para confirmar que a VM foi criada usando o disco recuperado, liste as VMs em seu grupo de recursos com [az vm list](/cli/azure/vm?view=azure-cli-latest#az-vm-list) da seguinte maneira:

    ```azurecli-interactive
    az vm list --resource-group myResourceGroup --output table
    ```


## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você restaurou um disco de ponto de recuperação e, em seguida, criou uma VM do disco. Você aprendeu como:

> [!div class="checklist"]
> * Liste e selecione os pontos de recuperação
> * Restaurar um disco de um ponto de recuperação
> * Criar uma VM do disco restaurado

Avance para o próximo tutorial para saber mais sobre como restaurar arquivos individuais de um ponto de recuperação.

> [!div class="nextstepaction"]
> [Restaurar arquivos para uma máquina virtual no Azure](tutorial-restore-files.md)

