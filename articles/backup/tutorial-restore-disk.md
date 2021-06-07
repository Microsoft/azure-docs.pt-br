---
title: Tutorial – Restaurar uma VM com a CLI do Azure
description: Saiba como restaurar um disco e criar uma VM de recuperação no Azure com serviços de backup e recuperação.
ms.topic: tutorial
ms.date: 01/31/2019
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 7f4d70f43f76c3a72cd8e53037d06d32e61c3cdb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768486"
---
# <a name="restore-a-vm-with-azure-cli"></a>Restaurar uma VM com a CLI do Azure

O Backup do Azure cria pontos de recuperação que são armazenados em cofres de recuperação com redundância geográfica. Ao restaurar de um ponto de recuperação, você pode restaurar a VM inteira ou arquivos individuais. Este artigo explica como restaurar uma VM completa usando a CLI. Neste tutorial, você aprenderá a:

> [!div class="checklist"]
>
> * Liste e selecione os pontos de recuperação
> * Restaurar um disco de um ponto de recuperação
> * Criar uma VM do disco restaurado

Para obter informações sobre como usar o PowerShell para restaurar um disco e criar uma VM recuperada, confira [Backup e restauração de VMs do Azure com o PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Este tutorial exige a versão 2.0.18 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

 - Este tutorial requer uma VM do Linux que tenha sido protegida com o Backup do Azure. Para simular um processo de exclusão acidental e recuperação de VM, você pode criar uma VM de um disco em um ponto de recuperação. Se você precisar de uma VM do Linux que tenha sido protegida com o Backup do Azure, consulte [Back up a virtual machine in Azure with the CLI](quick-backup-vm-cli.md) (Fazer backup de uma máquina virtual no Azure com a CLI).

## <a name="backup-overview"></a>Visão geral do backup

Quando o Azure inicia um backup, a extensão de backup na VM cria um instantâneo pontual. A extensão de backup é instalada na VM quando o primeiro backup é solicitado. O Backup do Azure também poderá criar um instantâneo do armazenamento subjacente se a VM não estiver em execução quando o backup ocorrer.

Por padrão, o Backup do Azure usa um backup consistente com o sistema de arquivos. Depois que o Backup do Azure cria o instantâneo, os dados são transferidos para o cofre dos Serviços de Recuperação. Para maximizar a eficiência, o Backup do Azure identifica e transfere apenas os blocos de dados que foram alterados desde o backup anterior.

Quando a transferência de dados é concluída, o instantâneo é removido e um ponto de recuperação é criado.

## <a name="list-available-recovery-points"></a>Listar os pontos de recuperação disponíveis

Para restaurar um disco, selecione um ponto de recuperação como a origem dos dados de recuperação. Como a política padrão cria um ponto de recuperação por dia e os retém por 30 dias, você pode manter um conjunto de pontos de recuperação que permite selecionar um ponto específico para recuperação.

Para ver uma lista de pontos de recuperação disponíveis, use a [az backup recoverypoint list](/cli/azure/backup/recoverypoint#az_backup_recoverypoint_list). O **nome** do ponto de recuperação é usado para recuperar os discos. Neste tutorial, queremos o ponto de recuperação mais recente disponível. O parâmetro `--query [0].name` seleciona o nome do ponto de recuperação mais recente da seguinte maneira:

```azurecli-interactive
az backup recoverypoint list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --backup-management-type AzureIaasVM \
    --container-name myVM \
    --item-name myVM \
    --query [0].name \
    --output tsv
```

## <a name="restore-a-vm-disk"></a>Restaurar um disco de VM

> [!IMPORTANT]
> É altamente recomendável usar a CLI do Az versão 2.0.74 ou posterior para obter todos os benefícios de uma restauração rápida, incluindo a restauração de disco gerenciado. Será melhor se você sempre usar a versão mais recente.

### <a name="managed-disk-restore"></a>Restauração de disco gerenciado

Se a VM cujo backup foi realizado tiver discos gerenciados e se a intenção for restaurar discos gerenciados no ponto de recuperação, você primeiro fornecerá uma conta de armazenamento do Azure. Essa conta de armazenamento é usada para armazenar a configuração da VM e o modelo de implantação, que pode ser usado posteriormente para implantar a VM dos discos restaurados. Em seguida, você também fornece um grupo de recursos de destino para os discos gerenciados nos quais gravar o conteúdo restaurado.

1. Para criar uma conta de armazenamento, use [az storage account create](/cli/azure/storage/account#az_storage_account_create). O nome da conta de armazenamento deve conter apenas letras minúsculas e ser exclusivo globalmente. Substitua *mystorageaccount* pelo seu próprio nome exclusivo:

    ```azurecli-interactive
    az storage account create \
        --resource-group myResourceGroup \
        --name mystorageaccount \
        --sku Standard_LRS
    ```

2. Restaure o disco do seu ponto de recuperação com [az backup restore restore-disks](/cli/azure/backup/restore#az_backup_restore_restore_disks). Substitua *mystorageaccount* pelo nome da conta de armazenamento que você criou no comando anterior. Substitua *myRecoveryPointName* pelo nome do ponto de recuperação obtido na saída do comando [az backup recoverypoint list](/cli/azure/backup/recoverypoint#az_backup_recoverypoint_list) anterior. ***Forneça também o grupo de recursos de destino para os discos gerenciados nos quais gravar o conteúdo restaurado***.

    ```azurecli-interactive
    az backup restore restore-disks \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --storage-account mystorageaccount \
        --rp-name myRecoveryPointName \
        --target-resource-group targetRG
    ```

    > [!WARNING]
    > Se o **target-resource-group** não for fornecido, os discos gerenciados serão restaurados como discos não gerenciados para a conta de armazenamento informada. Isso terá consequências significativas no tempo de restauração, pois o tempo necessário para restaurar os discos depende totalmente da conta de armazenamento fornecida. Você terá o benefício da restauração instantânea somente quando o parâmetro target-resource-group for fornecido. Se a intenção for restaurar discos gerenciados como não gerenciados, não forneça o parâmetro **target-resource-group**; forneça, em vez disso, o parâmetro **restore-as-unmanaged-disk**, conforme mostrado abaixo. Esse parâmetro está disponível no az 3.4.0 e em versões posteriores.

    ```azurecli-interactive
    az backup restore restore-disks \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --container-name myVM \
    --item-name myVM \
    --storage-account mystorageaccount \
    --rp-name myRecoveryPointName \
    --restore-as-unmanaged-disk
    ```

Isso vai restaurar os discos gerenciados como discos não gerenciados na conta de armazenamento fornecida e não aproveitará a funcionalidade de restauração "instantânea". Em versões futuras da CLI, será obrigatório fornecer o parâmetro **target-resource-group** ou o parâmetro **restore-as-unmanaged-disk**.

### <a name="unmanaged-disks-restore"></a>Restauração de discos não gerenciados

Se a VM cujo backup foi realizado tiver discos não gerenciados e se a intenção for restaurar discos no ponto de recuperação, você primeiro fornecerá uma conta de armazenamento do Azure. Essa conta de armazenamento é usada para armazenar a configuração da VM e o modelo de implantação, que pode ser usado posteriormente para implantar a VM dos discos restaurados. Por padrão, os discos não gerenciados serão restaurados para as respectivas contas de armazenamento originais. Se você quiser restaurar todos os discos não gerenciados para um só local, a conta de armazenamento fornecida também poderá ser usada como um local de preparo para esses discos.

Nas etapas adicionais, o disco restaurado é usado para criar uma VM.

1. Para criar uma conta de armazenamento, use [az storage account create](/cli/azure/storage/account#az_storage_account_create). O nome da conta de armazenamento deve conter apenas letras minúsculas e ser exclusivo globalmente. Substitua *mystorageaccount* pelo seu próprio nome exclusivo:

    ```azurecli-interactive
    az storage account create \
        --resource-group myResourceGroup \
        --name mystorageaccount \
        --sku Standard_LRS
    ```

2. Restaure o disco do seu ponto de recuperação com [az backup restore restore-disks](/cli/azure/backup/restore#az_backup_restore_restore_disks). Substitua *mystorageaccount* pelo nome da conta de armazenamento que você criou no comando anterior. Substitua *myRecoveryPointName* pelo nome do ponto de recuperação obtido na saída do comando [az backup recoverypoint list](/cli/azure/backup/recoverypoint#az_backup_recoverypoint_list) anterior:

    ```azurecli-interactive
    az backup restore restore-disks \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --storage-account mystorageaccount \
        --rp-name myRecoveryPointName
    ```

Conforme mencionado acima, os discos não gerenciados serão restaurados para as respectivas contas de armazenamento originais. Isso fornece o melhor desempenho de restauração. Mas se todos os discos não gerenciados precisarem ser restaurados para a conta de armazenamento fornecida, use o sinalizador relevante, conforme mostrado abaixo.

```azurecli-interactive
    az backup restore restore-disks \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --storage-account mystorageaccount \
        --rp-name myRecoveryPointName \
        --restore-to-staging-storage-account
    ```

## Monitor the restore job

To monitor the status of restore job, use [az backup job list](/cli/azure/backup/job#az_backup_job_list):

```azurecli-interactive
az backup job list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --output table
```

O resultado é semelhante ao exemplo a seguir, que mostra que o trabalho de restauração está *InProgress*:

```output
Name      Operation        Status      Item Name    Start Time UTC       Duration
--------  ---------------  ----------  -----------  -------------------  --------------
7f2ad916  Restore          InProgress  myvm         2017-09-19T19:39:52  0:00:34.520850
a0a8e5e6  Backup           Completed   myvm         2017-09-19T03:09:21  0:15:26.155212
fe5d0414  ConfigureBackup  Completed   myvm         2017-09-19T03:03:57  0:00:31.191807
```

Quando o *Status* do trabalho de restauração for relatado como *Concluído*, as informações necessárias (a configuração da VM e o modelo de implantação) terão sido restauradas para a conta de armazenamento.

## <a name="create-a-vm-from-the-restored-disk"></a>Criar uma VM do disco restaurado

A etapa final é criar uma VM dos discos restaurados. Você pode usar o modelo de implantação baixado para a conta de armazenamento fornecida a fim de criar a VM.

### <a name="fetch-the-job-details"></a>Buscar os detalhes do trabalho

Os detalhes do trabalho resultante fornecem o URI do modelo, o qual pode ser consultado e implantado. Use o comando job show para obter mais detalhes sobre o trabalho restaurado disparado.

```azurecli-interactive
az backup job show \
    -v myRecoveryServicesVault \
    -g myResourceGroup \
    -n 1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414
```

A saída dessa consulta fornecerá todos os detalhes, mas estamos interessados apenas no conteúdo da conta de armazenamento. Podemos usar a [funcionalidade de consulta](/cli/azure/query-azure-cli) da CLI do Azure para buscar os detalhes relevantes

```azurecli-interactive
az backup job show \
    -v myRecoveryServicesVault \
    -g myResourceGroup \
    -n 1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414 \
    --query properties.extendedInfo.propertyBag

{
  "Config Blob Container Name": "myVM-daa1931199fd4a22ae601f46d8812276",
  "Config Blob Name": "config-myVM-1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414.json",
  "Config Blob Uri": "https://mystorageaccount.blob.core.windows.net/myVM-daa1931199fd4a22ae601f46d8812276/config-appvm8-1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json",
  "Job Type": "Recover disks",
  "Recovery point time ": "12/25/2019 10:07:11 PM",
  "Target Storage Account Name": "mystorageaccount",
  "Target resource group": "mystorageaccountRG",
  "Template Blob Uri": "https://mystorageaccount.blob.core.windows.net/myVM-daa1931199fd4a22ae601f46d8812276/azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json"
}
```

### <a name="fetch-the-deployment-template"></a>Buscar o modelo de implantação

O modelo não pode ser acessado diretamente, pois está sob a conta de armazenamento de um cliente e o contêiner fornecido. Precisamos da URL completa (junto com um token SAS temporário) para acessar esse modelo.

Primeiro, extraia o URI do blob de modelo dos detalhes do trabalho

```azurecli-interactive
az backup job show \
    -v myRecoveryServicesVault \
    -g myResourceGroup \
    -n 1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414 \
    --query properties.extendedInfo.propertyBag."""Template Blob Uri"""

"https://mystorageaccount.blob.core.windows.net/myVM-daa1931199fd4a22ae601f46d8812276/azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json"
```

O URI do blob de modelo será desse formato e extrairá o nome do modelo

```https
https://<storageAccountName.blob.core.windows.net>/<containerName>/<templateName>
```

Portanto, o nome do modelo do exemplo acima será ```azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json``` e o nome do contêiner será ```myVM-daa1931199fd4a22ae601f46d8812276```

Agora, obtenha o token SAS para esse contêiner e modelo, conforme detalhado [aqui](../azure-resource-manager/templates/secure-template-with-sas-token.md?tabs=azure-cli#provide-sas-token-during-deployment)

```azurecli-interactive
expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
    --resource-group mystorageaccountRG \
    --name mystorageaccount \
    --query connectionString)
token=$(az storage blob generate-sas \
    --container-name myVM-daa1931199fd4a22ae601f46d8812276 \
    --name azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
url=$(az storage blob url \
   --container-name myVM-daa1931199fd4a22ae601f46d8812276 \
    --name azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json \
    --output tsv \
    --connection-string $connection)
```

### <a name="deploy-the-template-to-create-the-vm"></a>Implantar o modelo para criar a VM

Agora, implante o modelo para criar a VM, conforme explicado [aqui](../azure-resource-manager/templates/deploy-cli.md).

```azurecli-interactive
az deployment group create \
  --resource-group ExampleGroup \
  --template-uri $url?$token
```

Para confirmar que a VM foi criada usando o disco recuperado, liste as VMs em seu grupo de recursos com [az vm list](/cli/azure/vm#az_vm_list) da seguinte maneira:

```azurecli-interactive
az vm list --resource-group myResourceGroup --output table
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você restaurou um disco de ponto de recuperação e, em seguida, criou uma VM do disco. Você aprendeu a:

> [!div class="checklist"]
>
> * Liste e selecione os pontos de recuperação
> * Restaurar um disco de um ponto de recuperação
> * Criar uma VM do disco restaurado

Avance para o próximo tutorial para saber mais sobre como restaurar arquivos individuais de um ponto de recuperação.

> [!div class="nextstepaction"]
> [Restaurar arquivos para uma máquina virtual no Azure](tutorial-restore-files.md)
