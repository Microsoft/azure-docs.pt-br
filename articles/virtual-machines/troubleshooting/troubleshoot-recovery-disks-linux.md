---
title: Usar uma VM de solução de problemas do Linux com a CLI do Azure | Microsoft Docs
description: Saiba como solucionar problemas de VM do Linux conectando o disco do sistema operacional a uma VM de recuperação usando a CLI do Azure
services: virtual-machines-linux
documentationCenter: ''
author: genlin
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/16/2017
ms.author: genli
ms.openlocfilehash: e1e91ec4393072a7da78c0de800cab26608c74d6
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67709331"
---
# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-with-the-azure-cli"></a>Solucionar problemas de uma VM do Linux anexando o disco do SO a uma VM de recuperação com a CLI do Azure
Se a VM (máquina virtual) do Linux tiver um erro de disco ou de inicialização, talvez você precise realizar etapas de solução de problemas no próprio disco rígido virtual. Um exemplo comum seria uma entrada inválida em `/etc/fstab` que impede que a VM possa ser inicializada corretamente. Este artigo fornece detalhes sobre como usar a CLI do Azure para conectar o disco rígido virtual a outra VM do Linux para corrigir erros e recriar a VM original. 


## <a name="recovery-process-overview"></a>Visão geral do processo de recuperação
O processo de solução de problemas é o seguinte:

1. Exclua a VM que tem problemas, mantendo os discos rígidos virtuais.
2. Anexe e monte o disco rígido virtual em outra VM do Linux para fins de solução de problemas.
3. Conecte-se à VM de solução de problemas. Edite os arquivos ou execute as ferramentas para corrigir os problemas no disco rígido virtual original.
4. Desmonte e desanexe o disco rígido virtual da VM de solução de problemas.
5. Crie uma VM usando o disco rígido virtual original.

Para a máquina virtual que usa o disco gerenciado, consulte [Solucionar problemas de uma VM de disco gerenciado anexando um novo disco de SO](#troubleshoot-a-managed-disk-vm-by-attaching-a-new-os-disk).

Para realizar essas etapas de solução de problemas, é preciso ter a [CLI do Azure](/cli/azure/install-az-cli2) mais recente instalada e conectada a uma conta do Azure usando [az login](/cli/azure/reference-index).

Nos exemplos a seguir, substitua os nomes de parâmetro pelos seus próprios valores. Os nomes de parâmetro de exemplo incluem `myResourceGroup`, `mystorageaccount` e `myVM`.


## <a name="determine-boot-issues"></a>Determinar problemas de inicialização
Examine a saída serial para determinar por que a VM não pode ser inicializada corretamente. Um exemplo comum é uma entrada inválida em `/etc/fstab` ou a exclusão ou movimentação do disco rígido virtual subjacente.

Obtenha os logs de inicialização com [az vm boot-diagnostics get-boot-log](/cli/azure/vm/boot-diagnostics). O exemplo a seguir obtém a saída serial da VM chamada `myVM` no grupo de recursos chamado `myResourceGroup`:

```azurecli
az vm boot-diagnostics get-boot-log --resource-group myResourceGroup --name myVM
```

Examine a saída serial para determinar por que a VM não é inicializada corretamente. Se a saída serial não fornecer nenhuma indicação, talvez seja necessário examinar os arquivos de log em `/var/log` depois de conectar o disco rígido virtual a uma VM de solução de problemas.


## <a name="view-existing-virtual-hard-disk-details"></a>Exibir detalhes do disco rígido virtual existente
Antes de anexar o VHD (disco rígido virtual) a outra VM, você precisa identificar o URI do disco do sistema operacional. 

Exiba informações sobre a VM com [az vm show](/cli/azure/vm). Use o sinalizador `--query` para extrair o URI para o disco do sistema operacional. O exemplo a seguir obtém informações de disco da VM chamada `myVM` no grupo de recursos chamado `myResourceGroup`:

```azurecli
az vm show --resource-group myResourceGroup --name myVM \
    --query [storageProfile.osDisk.vhd.uri] --output tsv
```

O URI é semelhante a **https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd** .

## <a name="delete-existing-vm"></a>Excluir VM existente
Discos rígidos virtuais e VMs são dois recursos distintos no Azure. Um disco rígido virtual é o local em que o próprio sistema operacional, os aplicativos e as configurações são armazenados. A VM em si são apenas os metadados que definem o tamanho ou a localização e que faz referência a recursos como um disco rígido virtual ou a NIC (placa de interface de rede) virtual. Cada disco rígido virtual tem uma concessão atribuída quando anexado a uma VM. Embora os discos de dados possam ser anexados e desanexados mesmo durante a execução da VM, o disco do sistema operacional não pode ser desanexado, a menos que o recurso de VM seja excluído. A concessão continua associando o disco do sistema operacional a uma VM mesmo quando a VM está em um estado parado e desalocado.

A primeira etapa para recuperar a VM é excluir o recurso de VM em si. A exclusão da VM deixa os discos rígidos virtuais na conta de armazenamento. Depois que a VM for excluída, você anexa o disco rígido virtual a outra VM para solucionar os erros.

Exclua a VM com [az vm delete](/cli/azure/vm). O seguinte exemplo exclui a VM chamada `myVM` do grupo de recursos chamado `myResourceGroup`:

```azurecli
az vm delete --resource-group myResourceGroup --name myVM 
```

Aguarde até que a VM tenha concluído a exclusão antes de anexar o disco rígido virtual a outra VM. A concessão no disco rígido virtual que o associa à VM precisa ser liberada antes da anexação do disco rígido virtual a outra VM.


## <a name="attach-existing-virtual-hard-disk-to-another-vm"></a>Anexar um disco rígido virtual existente a outra VM
Para as próximas etapas, você pode usar outra VM para fins de solução de problemas. Você anexa o disco rígido virtual existente a essa VM de solução de problemas para procurar e editar o conteúdo do disco. Esse processo permite que você corrija os erros de configuração ou examine arquivos de aplicativo ou de log do sistema adicionais, por exemplo. Escolha ou crie outra VM a ser usada para fins de solução de problemas.

Anexe o disco rígido virtual existente com [az vm unmanaged-disk attach](/cli/azure/vm/unmanaged-disk). Ao anexar o disco rígido virtual existente, especifique o URI para o disco obtido no comando `az vm show` anterior. O seguinte exemplo anexa um disco rígido virtual existente à VM de solução de problemas chamada `myVMRecovery` no grupo de recursos chamado `myResourceGroup`:

```azurecli
az vm unmanaged-disk attach --resource-group myResourceGroup --vm-name myVMRecovery \
    --vhd-uri https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd
```


## <a name="mount-the-attached-data-disk"></a>Montar o disco de dados anexado

> [!NOTE]
> Os exemplos a seguir fornecem detalhes das etapas necessárias em uma VM do Ubuntu. Se você estiver usando outra distribuição Linux, como Red Hat Enterprise Linux ou SUSE, as localizações do arquivo de log e os comandos `mount` poderão ser um pouco diferentes. Consulte a documentação da distribuição específica para obter as alterações apropriadas aos comandos.

1. SSH para a VM de solução de problemas usando as credenciais apropriadas. Se esse disco for o primeiro disco de dados anexado à VM de solução de problemas, provavelmente ele estará conectado a `/dev/sdc`. Use `dmseg` para exibir os discos anexados:

    ```bash
    dmesg | grep SCSI
    ```

    A saída deverá ser semelhante ao seguinte exemplo:

    ```bash
    [    0.294784] SCSI subsystem initialized
    [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
    [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
    [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
    [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
    ```

    No exemplo anterior, o disco do sistema operacional está em `/dev/sda` e o disco temporário fornecido para cada VM está em `/dev/sdb`. Se você tiver vários discos de dados, eles deverão estar em `/dev/sdd`, `/dev/sde` e assim por diante.

2. Crie um diretório para montar o disco rígido virtual existente. O seguinte exemplo cria um diretório chamado `troubleshootingdisk`:

    ```bash
    sudo mkdir /mnt/troubleshootingdisk
    ```

3. Se você tiver várias partições no disco rígido virtual existente, monte a partição necessária. O seguinte exemplo monta a primeira partição primária em `/dev/sdc1`:

    ```bash
    sudo mount /dev/sdc1 /mnt/troubleshootingdisk
    ```

    > [!NOTE]
    > A prática recomendada é montar os discos de dados em VMs no Azure usando o UUID (identificador universal exclusivo) do disco rígido virtual. Para este cenário de solução de problemas breve, não é necessário montar o disco rígido virtual usando o UUID. No entanto, no uso normal, editar `/etc/fstab` para montar os discos rígidos virtuais usando o nome do dispositivo em vez do UUID poderá causar falha de inicialização da VM.


## <a name="fix-issues-on-original-virtual-hard-disk"></a>Corrigir problemas no disco rígido virtual original
Com o disco rígido virtual existente montado, agora você pode realizar as etapas de manutenção e solução de problemas necessárias. Depois de resolver os problemas, continue com as próximas etapas.


## <a name="unmount-and-detach-original-virtual-hard-disk"></a>Desmontar e desanexar o disco rígido virtual original
Depois de resolver os erros, desmonte e desanexe o disco rígido virtual existente da VM de solução de problemas. Não é possível usar o disco rígido virtual com nenhuma outra VM até que a concessão que anexa o disco rígido virtual à VM de solução de problemas seja liberada.

1. Na sessão do SSH da VM de solução de problemas, desmonte o disco rígido virtual existente. Altere do diretório pai para o ponto de montagem primeiro:

    ```bash
    cd /
    ```

    Agora desmonte o disco rígido virtual existente. O seguinte exemplo desmonta o dispositivo em `/dev/sdc1`:

    ```bash
    sudo umount /dev/sdc1
    ```

2. Agora desanexe o disco rígido virtual da VM. Saia da sessão do SSH da VM de solução de problemas. Liste os discos de dados anexados à VM de solução de problemas com [az vm unmanaged-disk list](/cli/azure/vm/unmanaged-disk). O seguinte exemplo lista os discos de dados anexados à VM chamada `myVMRecovery` no grupo de recursos chamado `myResourceGroup`:

    ```azurecli
    azure vm unmanaged-disk list --resource-group myResourceGroup --vm-name myVMRecovery \
        --query '[].{Disk:vhd.uri}' --output table
    ```

    Observe o nome do disco rígido virtual existente. Por exemplo, o nome de um disco com o URI de **https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd** é **myVHD**. 

    Desanexe o disco de dados da sua VM com [az vm unmanaged-disk detach](/cli/azure/vm/unmanaged-disk). O exemplo a seguir desanexa o disco chamado `myVHD` da VM chamada `myVMRecovery` no grupo de recursos chamado `myResourceGroup`:

    ```azurecli
    az vm unmanaged-disk detach --resource-group myResourceGroup --vm-name myVMRecovery \
        --name myVHD
    ```


## <a name="create-vm-from-original-hard-disk"></a>Criar a VM com base no disco rígido original
Para criar uma VM com base no disco rígido virtual original, use [esse modelo do Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd). O modelo JSON real está no seguinte link:

- https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-specialized-vhd-new-or-existing-vnet/azuredeploy.json

O modelo implanta uma VM usando o URI do VHD do comando anterior. Implante o modelo com [az group deployment create](/cli/azure/group/deployment). Forneça o URI para o VHD original e, em seguida, especifique o tipo do sistema operacional, o tamanho da VM e o nome da VM da seguinte maneira:

```azurecli
az group deployment create --resource-group myResourceGroup --name myDeployment \
  --parameters '{"osDiskVhdUri": {"value": "https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd"},
    "osType": {"value": "Linux"},
    "vmSize": {"value": "Standard_DS1_v2"},
    "vmName": {"value": "myDeployedVM"}}' \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-specialized-vhd/azuredeploy.json
```

## <a name="re-enable-boot-diagnostics"></a>Habilitar o diagnóstico de inicialização novamente
Ao criar a VM com base no disco rígido virtual existente, o diagnóstico de inicialização poderá não ser habilitado automaticamente. Habilite o diagnóstico de inicialização com [az vm boot-diagnostics enable](/cli/azure/vm/boot-diagnostics). O seguinte exemplo habilita a extensão de diagnóstico na VM chamada `myDeployedVM` no grupo de recursos chamado `myResourceGroup`:

```azurecli
az vm boot-diagnostics enable --resource-group myResourceGroup --name myDeployedVM
```

## <a name="troubleshoot-a-managed-disk-vm-by-attaching-a-new-os-disk"></a>Solucionar problemas de uma VM de disco gerenciado anexando um novo disco de SO
1. Pare a VM afetada.
2. [Criar um instantâneo de disco gerenciado](../linux/snapshot-copy-managed-disk.md) do disco do sistema operacional da VM de disco gerenciado.
3. [Crie um novo disco gerenciado com base no instantâneo](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-snapshot.md).
4. [Anexar o disco gerenciado como um disco de dados da VM](../windows/attach-disk-ps.md).
5. [Alterar o disco de dados da etapa 4 para o disco do sistema operacional](../windows/os-disk-swap.md).

## <a name="next-steps"></a>Próximas etapas
Se você estiver tendo problemas para se conectar à VM, consulte [Solucionar problemas de conexões SSH com uma VM do Azure](troubleshoot-ssh-connection.md). Para ver problemas com o acesso a aplicativos executados na VM, consulte [Solucionar problemas de conectividade do aplicativo em uma VM do Linux](troubleshoot-app-connection.md).

