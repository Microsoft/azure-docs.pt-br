---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 5490bdd3934b438a683ce4271fbec20b3d13735d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60770849"
---
Se ocorrer um erro de disco ou de inicialização na VM (máquina virtual) no Azure, talvez você precise realizar etapas de solução de problemas no próprio disco rígido virtual. Um exemplo comum seria uma atualização de aplicativo com falha que impede a inicialização bem-sucedida da VM. Este artigo descreve como usar o portal do Azure para conectar o disco rígido virtual a outra VM para corrigir erros e recriar a VM original.


## <a name="recovery-process-overview"></a>Visão geral do processo de recuperação
O processo de solução de problemas é o seguinte:

1. Exclua a máquina virtual que está tendo problemas, mas mantenha os discos rígidos virtuais.
2. Anexe e monte o disco rígido virtual em outra VM para solucionar problemas.
3. Conecte-se à VM de solução de problemas. Edite os arquivos ou execute as ferramentas para corrigir erros no disco rígido virtual original.
4. Desmonte e desanexe o disco rígido virtual da VM de solução de problemas.
5. Crie uma VM usando o disco rígido virtual original.

## <a name="delete-the-original-vm"></a>Excluir a VM original
Discos rígidos virtuais e VMs são dois recursos distintos no Azure. Um disco rígido virtual é o local em que o sistema operacional, os aplicativos e as configurações são armazenados. A VM é apenas os metadados que definem o tamanho ou a localização e que fazem referência a recursos como um disco rígido virtual ou a NIC (placa de interface de rede) virtual. Cada disco rígido virtual recebe a atribuição de uma concessão quando o disco é anexado a uma máquina virtual. Embora os discos de dados possam ser anexados e desanexados mesmo durante a execução da VM, o disco do sistema operacional não pode ser desanexado, a menos que o recurso de VM seja excluído. A concessão continua associando o disco do sistema operacional a uma VM mesmo quando a VM está em um estado parado e desalocado.

A primeira etapa para recuperar a VM é excluir o recurso de VM em si. A exclusão da VM deixa os discos rígidos virtuais na conta de armazenamento. Depois que a VM for excluída, você pode anexar o disco rígido virtual a outra VM para solucionar os erros. 

1. Entre no [Portal do Azure](https://portal.azure.com). 
2. No menu à esquerda, clique em **Máquinas Virtuais (clássicas)**.
3. Selecione a máquina virtual que tem o problema, clique em **Discos** e identifique o nome do disco rígido virtual. 
4. Selecione o disco rígido virtual do sistema operacional e verifique o **Local** para identificar a conta de armazenamento que contém o disco rígido virtual. No exemplo a seguir, a cadeia de caracteres imediatamente antes de “.blob.core.windows.net“ é o nome da conta de armazenamento.

    ```
    https://portalvhds73fmhrw5xkp43.blob.core.windows.net/vhds/SCCM2012-2015-08-28.vhd
    ```

    ![A imagem sobre o local da VM](./media/virtual-machines-classic-recovery-disks-portal/vm-location.png)

5. Clique com o botão direito do mouse na máquina virtual e selecione **Excluir**. Verifique se os discos não estão selecionados ao excluir a máquina virtual.
6. Crie uma nova VM de recuperação. Essa VM deve estar na mesma região e no mesmo grupo de recursos (Serviço de Nuvem) da VM com problemas.
7. Selecione a VM de recuperação e clique em **Discos** > **Anexar Existente**.
8. Para selecionar o disco rígido virtual existente, clique em **Arquivo VHD**:

    ![Procurar VHD existente](./media/virtual-machines-classic-recovery-disks-portal/select-vhd-location.png)

9. Selecione a conta de armazenamento > contêiner VHD > disco rígido virtual e clique no botão **Selecionar** para confirmar sua escolha.

    ![Selecionar o VHD existente](./media/virtual-machines-classic-recovery-disks-portal/select-vhd.png)

10. Com o VHD agora selecionado, clique e **OK** para anexar o disco rígido virtual existente.
11. Depois de alguns segundos, o painel **Discos** da VM exibe o disco rígido virtual existente conectado como um disco de dados:

    ![Disco rígido virtual existente anexado como um disco de dados](./media/virtual-machines-classic-recovery-disks-portal/attached-disk.png)

## <a name="fix-issues-on-the-original-virtual-hard-disk"></a>Corrigir problemas no disco rígido virtual original
Quando o disco rígido virtual existente é montado, você pode realizar as etapas de manutenção e solução de problemas necessárias. Depois de resolver os problemas, continue com as próximas etapas.

## <a name="unmount-and-detach-the-original-virtual-hard-disk"></a>Desmontar e desanexar o disco rígido virtual original
Depois de resolver os erros, desmonte e desanexe o disco rígido virtual existente da VM de solução de problemas. Não é possível usar o disco rígido virtual com nenhuma outra VM até que a concessão que anexa o disco rígido virtual à VM de solução de problemas seja liberada.  

1. Entre no [Portal do Azure](https://portal.azure.com). 
2. No menu à esquerda, selecione **Máquinas Virtuais (clássicas)**.
3. Localize a VM de recuperação. Selecione os discos, clique com o botão direito do mouse no disco e clique em **Desanexar**.

## <a name="create-a-vm-from-the-original-hard-disk"></a>Criar uma máquina virtual do disco rígido original

Para criar uma VM com base no disco rígido virtual original, use o [Portal do Azure](https://portal.azure.com).

1. Entre no [Portal do Azure](https://portal.azure.com).
2. No canto superior esquerdo do portal, selecione **Criar um recurso** > **Computação** > **Máquina Virtual** > **Da Galeria**.
3. Na seção **Escolher uma Imagem**, clique em **Meus discos** e selecione o disco rígido virtual original. Verifique as informações de local. É a região em que a máquina virtual deve ser implantada. Clique no botão Avançar.
4. Na seção **Configuração de máquina Virtual**, digite o nome da VM e selecione um tamanho para a máquina virtual.
