---
title: Usar uma VM de solução de problemas do Linux no Portal do Azure | Microsoft Docs
description: Saiba como solucionar problemas da máquina virtual do Linux conectando o disco do sistema operacional a uma VM de recuperação usando o portal do Azure
services: virtual-machines-linux
documentationCenter: ''
author: genlin
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/14/2016
ms.author: genli
ms.openlocfilehash: 160e45ad5bf83f44bed2314ee5103825e265467c
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67709383"
---
# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-the-azure-portal"></a>Solucionar problemas de uma VM do Linux anexando o disco do sistema operacional a uma VM de recuperação usando o portal do Azure
Se a VM (máquina virtual) do Linux tiver um erro de disco ou de inicialização, talvez você precise realizar etapas de solução de problemas no próprio disco rígido virtual. Um exemplo comum seria uma entrada inválida em `/etc/fstab` que impede que a VM possa ser inicializada corretamente. Este artigo fornece detalhes sobre como usar o portal do Azure para conectar o disco rígido virtual a outra VM do Linux para corrigir erros e recriar a VM original.

## <a name="recovery-process-overview"></a>Visão geral do processo de recuperação
O processo de solução de problemas é o seguinte:

1. Exclua a VM que tem problemas, mantendo os discos rígidos virtuais.
2. Anexe e monte o disco rígido virtual em outra VM do Linux para fins de solução de problemas.
3. Conecte-se à VM de solução de problemas. Edite os arquivos ou execute as ferramentas para corrigir os problemas no disco rígido virtual original.
4. Desmonte e desanexe o disco rígido virtual da VM de solução de problemas.
5. Crie uma VM usando o disco rígido virtual original.

Para a máquina virtual que usa o disco gerenciado, consulte [Solucionar problemas de uma VM de disco gerenciado anexando um novo disco de SO](#troubleshoot-a-managed-disk-vm-by-attaching-a-new-os-disk).

## <a name="determine-boot-issues"></a>Determinar problemas de inicialização
Examine o diagnóstico de inicialização e a captura de tela da VM para determinar por que a VM não pode ser inicializada corretamente. Um exemplo comum seria uma entrada inválida em `/etc/fstab` ou a exclusão ou movimentação do disco rígido virtual subjacente.

Selecione a VM no portal e role para baixo até a seção **Suporte + Solução de problemas**. Clique em **Diagnóstico de inicialização** para exibir as mensagens do console transmitidas pela VM. Examine os logs do console para ver se você pode determinar por que a VM está com problemas. O seguinte exemplo mostra uma VM parada no modo de manutenção que exige interação manual:

![Exibindo os logs do console do diagnóstico de inicialização da VM](./media/troubleshoot-recovery-disks-portal-linux/boot-diagnostics-error.png)

Também é possível clicar em **Captura de tela** na parte superior do log do diagnóstico de inicialização para baixar uma captura de tela da VM.


## <a name="view-existing-virtual-hard-disk-details"></a>Exibir detalhes do disco rígido virtual existente
Antes de anexar o disco rígido virtual a outra VM, você precisa identificar o nome do VHD (disco rígido virtual). 

Selecione o grupo de recursos no portal e sua conta de armazenamento. Clique em **Blobs**, como no seguinte exemplo:

![Selecionar os blobs de armazenamento](./media/troubleshoot-recovery-disks-portal-linux/storage-account-overview.png)

Normalmente, você tem um contêiner chamado **vhds** que armazena os discos rígidos virtuais. Selecione o contêiner para exibir uma lista de discos rígidos virtuais. Anote o nome do VHD (o prefixo é geralmente o nome da VM):

![Identificar o VHD no contêiner de armazenamento](./media/troubleshoot-recovery-disks-portal-linux/storage-container.png)

Selecione o disco rígido virtual existente na lista e copie a URL para uso nas próximas etapas:

![Copiar a URL do disco rígido virtual existente](./media/troubleshoot-recovery-disks-portal-linux/copy-vhd-url.png)


## <a name="delete-existing-vm"></a>Excluir VM existente
Discos rígidos virtuais e VMs são dois recursos distintos no Azure. Um disco rígido virtual é o local em que o próprio sistema operacional, os aplicativos e as configurações são armazenados. A VM em si são apenas os metadados que definem o tamanho ou a localização e que faz referência a recursos como um disco rígido virtual ou a NIC (placa de interface de rede) virtual. Cada disco rígido virtual tem uma concessão atribuída quando anexado a uma VM. Embora os discos de dados possam ser anexados e desanexados mesmo durante a execução da VM, o disco do sistema operacional não pode ser desanexado, a menos que o recurso de VM seja excluído. A concessão continua associando o disco do sistema operacional a uma VM mesmo quando a VM está em um estado parado e desalocado.

A primeira etapa para recuperar a VM é excluir o recurso de VM em si. A exclusão da VM deixa os discos rígidos virtuais na conta de armazenamento. Depois que a VM for excluída, você anexa o disco rígido virtual a outra VM para solucionar os erros.

Selecione a VM no portal e clique em **Excluir**:

![Captura de tela do diagnóstico de inicialização da VM mostrando um erro de inicialização](./media/troubleshoot-recovery-disks-portal-linux/stop-delete-vm.png)

Aguarde até que a VM tenha concluído a exclusão antes de anexar o disco rígido virtual a outra VM. A concessão no disco rígido virtual que o associa à VM precisa ser liberada antes da anexação do disco rígido virtual a outra VM.


## <a name="attach-existing-virtual-hard-disk-to-another-vm"></a>Anexar um disco rígido virtual existente a outra VM
Para as próximas etapas, você pode usar outra VM para fins de solução de problemas. Você anexa o disco rígido virtual existente a essa VM de solução de problemas para poder procurar e editar o conteúdo do disco. Esse processo permite que você corrija os erros de configuração ou examine arquivos de aplicativo ou de log do sistema adicionais, por exemplo. Escolha ou crie outra VM a ser usada para fins de solução de problemas.

1. Selecione o grupo de recursos no portal e a VM de solução de problemas. Selecione **Discos** e clique em **Anexar existente**:

    ![Anexar um disco existente no portal](./media/troubleshoot-recovery-disks-portal-linux/attach-existing-disk.png)

2. Para selecionar o disco rígido virtual existente, clique em **Arquivo VHD**:

    ![Procurar VHD existente](./media/troubleshoot-recovery-disks-portal-linux/select-vhd-location.png)

3. Selecione sua conta de armazenamento e seu contêiner e clique no VHD existente. Clique no botão **Selecionar** para confirmar sua escolha:

    ![Selecionar o VHD existente](./media/troubleshoot-recovery-disks-portal-linux/select-vhd.png)

4. Com o VHD agora selecionado, clique em **OK** para anexar o disco rígido virtual existente:

    ![Confirmar a anexação do disco rígido virtual existente](./media/troubleshoot-recovery-disks-portal-linux/attach-disk-confirm.png)

5. Depois de alguns segundos, o painel **Discos** da VM lista o disco rígido virtual existente conectado como um disco de dados:

    ![Disco rígido virtual existente anexado como um disco de dados](./media/troubleshoot-recovery-disks-portal-linux/attached-disk.png)


## <a name="mount-the-attached-data-disk"></a>Montar o disco de dados anexado

> [!NOTE]
> Os exemplos a seguir fornecem detalhes das etapas necessárias em uma VM do Ubuntu. Se você estiver usando outra distribuição Linux, como Red Hat Enterprise Linux ou SUSE, as localizações do arquivo de log e os comandos `mount` poderão ser um pouco diferentes. Consulte a documentação da distribuição específica para obter as alterações apropriadas aos comandos.

1. SSH para a VM de solução de problemas usando as credenciais apropriadas. Se esse disco for o primeiro disco de dados anexado à VM de solução de problemas, provavelmente ele estará conectado a `/dev/sdc`. Use `dmseg` para listar os discos anexados:

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
Depois de resolver os erros, desanexe o disco rígido virtual existente da VM de solução de problemas. Não é possível usar o disco rígido virtual com nenhuma outra VM até que a concessão que anexa o disco rígido virtual à VM de solução de problemas seja liberada.

1. Na sessão do SSH da VM de solução de problemas, desmonte o disco rígido virtual existente. Altere do diretório pai para o ponto de montagem primeiro:

    ```bash
    cd /
    ```

    Agora desmonte o disco rígido virtual existente. O seguinte exemplo desmonta o dispositivo em `/dev/sdc1`:

    ```bash
    sudo umount /dev/sdc1
    ```

2. Agora desanexe o disco rígido virtual da VM. Selecione a VM no portal e clique em **Discos**. Selecione o disco rígido virtual existente e clique em **Desanexar**:

    ![Desanexar um disco rígido virtual existente](./media/troubleshoot-recovery-disks-portal-linux/detach-disk.png)

    Aguarde até que a VM tenha desanexo o disco de dados corretamente antes de continuar.

## <a name="create-vm-from-original-hard-disk"></a>Criar a VM com base no disco rígido original
Para criar uma VM com base no disco rígido virtual original, use [esse modelo do Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd-existing-vnet). O modelo implanta uma VM em uma rede virtual existente, usando a URL do VHD do comando anterior. Clique no botão **Implantar no Azure** da seguinte maneira:

![Implantar a VM com base no modelo do GitHub](./media/troubleshoot-recovery-disks-portal-linux/deploy-template-from-github.png)

O modelo é carregado no portal do Azure para implantação. Insira os nomes da nova VM e dos recursos do Azure existentes e cole a URL no disco rígido virtual existente. Para iniciar a implantação, clique em **Comprar**:

![Implantar a VM com base no modelo](./media/troubleshoot-recovery-disks-portal-linux/deploy-from-image.png)


## <a name="re-enable-boot-diagnostics"></a>Habilitar o diagnóstico de inicialização novamente
Ao criar a VM com base no disco rígido virtual existente, o diagnóstico de inicialização poderá não ser habilitado automaticamente. Para verificar o status do diagnóstico de inicialização e ativá-lo, se necessário, selecione a VM no portal. Em **Monitoramento**, clique em **Configurações de diagnóstico**. Verifique se o status é **Ativado** e se a marca de seleção ao lado de **Diagnóstico de inicialização** está marcada. Se fizer alguma alteração, clique em **Salvar**:

![Atualizar as configurações do diagnóstico de inicialização](./media/troubleshoot-recovery-disks-portal-linux/reenable-boot-diagnostics.png)

## <a name="troubleshoot-a-managed-disk-vm-by-attaching-a-new-os-disk"></a>Solucionar problemas de uma VM de disco gerenciado anexando um novo disco de SO
1. Pare a VM afetada.
2. [Criar um instantâneo de disco gerenciado](../windows/snapshot-copy-managed-disk.md) do disco do sistema operacional da VM de disco gerenciado.
3. [Crie um novo disco gerenciado com base no instantâneo](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-snapshot.md).
4. [Anexar o disco gerenciado como um disco de dados da VM](../windows/attach-disk-ps.md).
5. [Alterar o disco de dados da etapa 4 para o disco do sistema operacional](../windows/os-disk-swap.md).

## <a name="next-steps"></a>Próximas etapas
Se você estiver tendo problemas para se conectar à VM, consulte [Solucionar problemas de conexões SSH com uma VM do Azure](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Para ver problemas com o acesso a aplicativos executados na VM, consulte [Solucionar problemas de conectividade do aplicativo em uma VM do Linux](../windows/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Para obter mais informações sobre como usar o Resource Manager, consulte [Visão geral do Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
