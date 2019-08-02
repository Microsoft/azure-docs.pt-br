---
title: Usar uma VM Windows de solução de problemas no portal do Azure | Microsoft Docs
description: Saiba como solucionar problemas de máquinas virtuais Windows no Azure conectando o disco do sistema operacional a uma VM de recuperação usando o portal do Azure
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: gwallace
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/13/2018
ms.author: genli
ms.openlocfilehash: 8ab6fc75475cd99e3d803450476880175f12d2b6
ms.sourcegitcommit: 1b7b0e1c915f586a906c33d7315a5dc7050a2f34
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67881188"
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-the-azure-portal"></a>Solucionar problemas de uma VM Windows anexando o disco do sistema operacional a uma VM de recuperação usando o portal do Azure
Se ocorrer um erro de disco ou de inicialização na VM (máquina virtual) Windows no Azure, talvez você precise realizar etapas de solução de problemas no próprio disco rígido virtual. Um exemplo comum seria uma atualização de aplicativo com falha que impede a inicialização bem-sucedida da VM. Este artigo fornece detalhes sobre como usar o portal do Azure para conectar o disco rígido virtual a outra VM Windows para corrigir erros e, em seguida, recriar a VM original. 

## <a name="recovery-process-overview"></a>Visão geral do processo de recuperação
O processo de solução de problemas é o seguinte:

1. Exclua a VM que tem problemas, mantendo os discos rígidos virtuais.
2. Anexe e monte o disco rígido virtual em outra VM Windows para fins de solução de problemas.
3. Conecte-se à VM de solução de problemas. Edite os arquivos ou execute as ferramentas para corrigir os problemas no disco rígido virtual original.
4. Desmonte e desanexe o disco rígido virtual da VM de solução de problemas.
5. Crie uma VM usando o disco rígido virtual original.

Para a VM, esse disco gerenciado de usos, podemos agora pode usar o Azure PowerShell para alterar o disco do sistema operacional para uma máquina virtual. Não precisamos mais excluir e recriar a VM. Para obter mais informações, consulte [solucionar problemas de uma VM Windows anexando o disco do sistema operacional a uma VM usando o PowerShell do Azure de recuperação](troubleshoot-recovery-disks-windows.md).

> [!NOTE]
> Este artigo não se aplica à VM com disco não gerenciado.

## <a name="determine-boot-issues"></a>Determinar problemas de inicialização
Para determinar por que a VM não pode ser inicializada corretamente, examine a captura de tela da VM do diagnóstico de inicialização. Um exemplo comum seria uma atualização de aplicativo com falha ou um disco rígido virtual subjacente que está sendo excluído ou movido.

Selecione a VM no portal e role para baixo até a seção **Suporte + Solução de problemas**. Clique em **Diagnóstico de inicialização** para exibir a captura de tela. Observe códigos de erro e mensagens de erro específicos para ajudar a determinar por que a VM está com um problema.

![Exibindo os logs do console do diagnóstico de inicialização da VM](./media/troubleshoot-recovery-disks-portal-windows/screenshot-error.png)

Clique também em **Baixar captura de tela** para baixar uma captura de tela da VM.

## <a name="view-existing-virtual-hard-disk-details"></a>Exibir detalhes do disco rígido virtual existente
Antes de anexar o disco rígido virtual a outra VM, você precisa identificar o nome do VHD (disco rígido virtual).

Selecione a VM que tem o problema e, em seguida, selecione **discos**. Registre o nome do disco do sistema operacional como no exemplo a seguir:

![Selecionar os blobs de armazenamento](./media/troubleshoot-recovery-disks-portal-windows/view-disk.png)

## <a name="delete-existing-vm"></a>Excluir VM existente
Discos rígidos virtuais e VMs são dois recursos distintos no Azure. Um disco rígido virtual é o local em que o próprio sistema operacional, os aplicativos e as configurações são armazenados. A VM em si são apenas os metadados que definem o tamanho ou a localização e que faz referência a recursos como um disco rígido virtual ou a NIC (placa de interface de rede) virtual. Cada disco rígido virtual tem uma concessão atribuída quando anexado a uma VM. Embora os discos de dados possam ser anexados e desanexados mesmo durante a execução da VM, o disco do sistema operacional não pode ser desanexado, a menos que o recurso de VM seja excluído. A concessão continua associando o disco do sistema operacional a uma VM mesmo quando a VM está em um estado parado e desalocado.

A primeira etapa para recuperar a VM é excluir o recurso de VM em si. A exclusão da VM deixa os discos rígidos virtuais na conta de armazenamento. Depois que a VM for excluída, você anexa o disco rígido virtual a outra VM para solucionar os erros.

Selecione a VM no portal e clique em **Excluir**:

![Captura de tela do diagnóstico de inicialização da VM mostrando um erro de inicialização](./media/troubleshoot-recovery-disks-portal-windows/stop-delete-vm.png)

Aguarde até que a VM tenha concluído a exclusão antes de anexar o disco rígido virtual a outra VM. A concessão no disco rígido virtual que o associa à VM precisa ser liberada antes da anexação do disco rígido virtual a outra VM.

## <a name="attach-existing-virtual-hard-disk-to-another-vm"></a>Anexar um disco rígido virtual existente a outra VM
Para as próximas etapas, você pode usar outra VM para fins de solução de problemas. Você anexa o disco rígido virtual existente a essa VM de solução de problemas para poder procurar e editar o conteúdo do disco. Esse processo permite que você corrija os erros de configuração ou examine arquivos de aplicativo ou de log do sistema adicionais, por exemplo. Escolha ou crie outra VM a ser usada para fins de solução de problemas.

1. Selecione o grupo de recursos no portal e a VM de solução de problemas. Selecione **discos**, selecione **Editar**e, em seguida, clique em **adicionar disco de dados**:

    ![Anexar um disco existente no portal](./media/troubleshoot-recovery-disks-portal-windows/attach-existing-disk.png)

2. Na lista **discos de dados** , selecione o disco do sistema operacional da VM que você identificou. Se você não vir o disco do sistema operacional, verifique se a solução de problemas da VM e do disco do sistema operacional estão na mesma região (local).
3. Selecione **salvar** para aplicar as alterações.

## <a name="mount-the-attached-data-disk"></a>Montar o disco de dados anexado

1. Abra uma conexão de Área de Trabalho Remota para a VM. Selecione a VM no portal e clique em **Conectar**. Baixe e abra o arquivo de conexão RDP. Insira suas credenciais para entrar em sua VM da seguinte maneira:

    ![Entrar em sua VM usando a área de trabalho remota](./media/troubleshoot-recovery-disks-portal-windows/open-remote-desktop.png)

2. Abra **Gerenciador do Servidor** e selecione **Serviços de Arquivo e Armazenamento**. 

    ![Selecionar Serviços de Arquivo e Armazenamento no Gerenciador do Servidor](./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png)

3. O disco de dados é detectado e anexado automaticamente. Para ver uma lista dos discos conectados, selecione **Discos**. É possível selecionar o disco de dados para exibir informações de volume, incluindo a letra da unidade. O seguinte exemplo mostra o disco de dados anexado e usando **F:** :

    ![Disco anexado e informações de volume no Gerenciador do Servidor](./media/troubleshoot-recovery-disks-portal-windows/server-manager-disk-attached.png)


## <a name="fix-issues-on-original-virtual-hard-disk"></a>Corrigir problemas no disco rígido virtual original
Com o disco rígido virtual existente montado, agora você pode realizar as etapas de manutenção e solução de problemas necessárias. Depois de resolver os problemas, continue com as próximas etapas.


## <a name="unmount-and-detach-original-virtual-hard-disk"></a>Desmontar e desanexar o disco rígido virtual original
Depois de resolver os erros, desanexe o disco rígido virtual existente da VM de solução de problemas. Não é possível usar o disco rígido virtual com nenhuma outra VM até que a concessão que anexa o disco rígido virtual à VM de solução de problemas seja liberada.

1. Na sessão RDP da VM, abra **Gerenciador do Servidor** e selecione **Serviços de Arquivo e Armazenamento**:

    ![Selecionar Serviços de Arquivo e Armazenamento no Gerenciador do Servidor](./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png)

2. Selecione **Discos** e, em seguida, o disco de dados. Clique com o botão direito do mouse no disco de dados e selecione **Colocar Offline**:

    ![Definir o disco de dados como offline no Gerenciador do Servidor](./media/troubleshoot-recovery-disks-portal-windows/server-manager-set-disk-offline.png)

3. Agora desanexe o disco rígido virtual da VM. Selecione a VM no portal do Azure e clique em **Discos**. 
4. Selecione **Editar**, selecione o disco do sistema operacional que você anexou e clique em desanexar:

    ![Desanexar um disco rígido virtual existente](./media/troubleshoot-recovery-disks-portal-windows/detach-disk.png)

    Aguarde até que a VM tenha desanexo o disco de dados corretamente antes de continuar.

## <a name="create-vm-from-original-hard-disk"></a>Criar a VM com base no disco rígido original

### <a name="method-1-use-azure-resource-manager-template"></a>Método 1 Use Azure Resource Manager modelo
Para criar uma VM com base no disco rígido virtual original, use [esse modelo do Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd-new-or-existing-vnet). O modelo implanta uma VM em uma rede virtual nova ou existente, usando a URL do VHD do comando anterior. Clique no botão **Implantar no Azure** da seguinte maneira:

![Implantar a VM com base no modelo do GitHub](./media/troubleshoot-recovery-disks-portal-windows/deploy-template-from-github.png)

O modelo é carregado no portal do Azure para implantação. Insira os nomes da nova VM e dos recursos do Azure existentes e cole a URL no disco rígido virtual existente. Para iniciar a implantação, clique em **Comprar**:

![Implantar a VM com base no modelo](./media/troubleshoot-recovery-disks-portal-windows/deploy-from-image.png)

### <a name="method-2-create-a-vm-from-the-disk"></a>Método 2 criar uma VM a partir do disco

1. No portal do Azure, selecione o grupo de recursos no portal e localize o disco do sistema operacional. Você também pode pesquisar o disco usando o nome do disco:

    ![Pesquisar disco de portal do Azure](./media/troubleshoot-recovery-disks-portal-windows/search-disk.png)
1. Selecione **visão geral**e, em seguida, selecione **criar VM**.
    ![Criar VM de um disco a partir de portal do Azure](./media/troubleshoot-recovery-disks-portal-windows/create-vm-from-disk.png)
1. Siga o assistente para criar a VM.

## <a name="re-enable-boot-diagnostics"></a>Habilitar o diagnóstico de inicialização novamente
Ao criar a VM com base no disco rígido virtual existente, o diagnóstico de inicialização poderá não ser habilitado automaticamente. Para verificar o status do diagnóstico de inicialização e ativá-lo, se necessário, selecione a VM no portal. Em **Monitoramento**, clique em **Configurações de diagnóstico**. Verifique se o status é **Ativado** e se a marca de seleção ao lado de **Diagnóstico de inicialização** está marcada. Se fizer alguma alteração, clique em **Salvar**:

![Atualizar as configurações do diagnóstico de inicialização](./media/troubleshoot-recovery-disks-portal-windows/reenable-boot-diagnostics.png)

## <a name="next-steps"></a>Próximas etapas
Se estiver tendo problemas para se conectar à VM, consulte [Troubleshoot RDP connections to an Azure VM](troubleshoot-rdp-connection.md) (Solucionar conexões RDP a uma VM do Azure). Para problemas com o acesso a aplicativos executados na VM, consulte [Solucionar problemas de conectividade do aplicativo em uma VM do Windows](troubleshoot-app-connection.md).

Para obter mais informações sobre como usar o Resource Manager, consulte [Visão geral do Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).

