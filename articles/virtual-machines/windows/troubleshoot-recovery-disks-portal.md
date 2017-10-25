---
title: "Usar uma VM Windows de solução de problemas no portal do Azure | Microsoft Docs"
description: "Saiba como solucionar problemas de máquinas virtuais Windows no Azure conectando o disco do sistema operacional a uma VM de recuperação usando o portal do Azure"
services: virtual-machines-windows
documentationCenter: 
authors: genlin
manager: timlt
editor: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/26/2017
ms.author: genli
ms.openlocfilehash: a4775b6b78b27a07cd39cc58f2088f67fd1b083b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
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


## <a name="determine-boot-issues"></a>Determinar problemas de inicialização
Para determinar por que a VM não pode ser inicializada corretamente, examine a captura de tela da VM do diagnóstico de inicialização. Um exemplo comum seria uma atualização de aplicativo com falha ou um disco rígido virtual subjacente que está sendo excluído ou movido.

Selecione a VM no portal e role para baixo até a seção **Suporte + Solução de problemas**. Clique em **Diagnóstico de inicialização** para exibir a captura de tela. Observe códigos de erro e mensagens de erro específicos para ajudar a determinar por que a VM está com um problema. O seguinte exemplo mostra uma VM aguardando a interrupção dos serviços:

![Exibindo os logs do console do diagnóstico de inicialização da VM](./media/troubleshoot-recovery-disks-portal/screenshot-error.png)

Também é possível clicar em **Captura de Tela** para baixar uma captura de tela da VM.


## <a name="view-existing-virtual-hard-disk-details"></a>Exibir detalhes do disco rígido virtual existente
Antes de anexar o disco rígido virtual a outra VM, você precisa identificar o nome do VHD (disco rígido virtual). 

Selecione o grupo de recursos no portal e sua conta de armazenamento. Clique em **Blobs**, como no seguinte exemplo:

![Selecionar os blobs de armazenamento](./media/troubleshoot-recovery-disks-portal/storage-account-overview.png)

Normalmente, você tem um contêiner chamado **vhds** que armazena os discos rígidos virtuais. Selecione o contêiner para exibir uma lista de discos rígidos virtuais. Anote o nome do VHD (o prefixo é geralmente o nome da VM):

![Identificar o VHD no contêiner de armazenamento](./media/troubleshoot-recovery-disks-portal/storage-container.png)

Selecione o disco rígido virtual existente na lista e copie a URL para uso nas próximas etapas:

![Copiar a URL do disco rígido virtual existente](./media/troubleshoot-recovery-disks-portal/copy-vhd-url.png)


## <a name="delete-existing-vm"></a>Excluir VM existente
Discos rígidos virtuais e VMs são dois recursos distintos no Azure. Um disco rígido virtual é o local em que o próprio sistema operacional, os aplicativos e as configurações são armazenados. A VM em si são apenas os metadados que definem o tamanho ou a localização e que faz referência a recursos como um disco rígido virtual ou a NIC (placa de interface de rede) virtual. Cada disco rígido virtual tem uma concessão atribuída quando anexado a uma VM. Embora os discos de dados possam ser anexados e desanexados mesmo durante a execução da VM, o disco do sistema operacional não pode ser desanexado, a menos que o recurso de VM seja excluído. A concessão continua associando o disco do sistema operacional a uma VM mesmo quando a VM está em um estado parado e desalocado.

A primeira etapa para recuperar a VM é excluir o recurso de VM em si. A exclusão da VM deixa os discos rígidos virtuais na conta de armazenamento. Depois que a VM for excluída, você anexa o disco rígido virtual a outra VM para solucionar os erros.

Selecione a VM no portal e clique em **Excluir**:

![Captura de tela do diagnóstico de inicialização da VM mostrando um erro de inicialização](./media/troubleshoot-recovery-disks-portal/stop-delete-vm.png)

Aguarde até que a VM tenha concluído a exclusão antes de anexar o disco rígido virtual a outra VM. A concessão no disco rígido virtual que o associa à VM precisa ser liberada antes da anexação do disco rígido virtual a outra VM.


## <a name="attach-existing-virtual-hard-disk-to-another-vm"></a>Anexar um disco rígido virtual existente a outra VM
Para as próximas etapas, você pode usar outra VM para fins de solução de problemas. Você anexa o disco rígido virtual existente a essa VM de solução de problemas para poder procurar e editar o conteúdo do disco. Esse processo permite que você corrija os erros de configuração ou examine arquivos de aplicativo ou de log do sistema adicionais, por exemplo. Escolha ou crie outra VM a ser usada para fins de solução de problemas.

1. Selecione o grupo de recursos no portal e a VM de solução de problemas. Selecione **Discos** e clique em **Anexar existente**:

    ![Anexar um disco existente no portal](./media/troubleshoot-recovery-disks-portal/attach-existing-disk.png)

2. Para selecionar o disco rígido virtual existente, clique em **Arquivo VHD**:

    ![Procurar VHD existente](./media/troubleshoot-recovery-disks-portal/select-vhd-location.png)

3. Selecione sua conta de armazenamento e seu contêiner e clique no VHD existente. Clique no botão **Selecionar** para confirmar sua escolha:

    ![Selecionar o VHD existente](./media/troubleshoot-recovery-disks-portal/select-vhd.png)

4. Com o VHD agora selecionado, clique em **OK** para anexar o disco rígido virtual existente:

    ![Confirmar a anexação do disco rígido virtual existente](./media/troubleshoot-recovery-disks-portal/attach-disk-confirm.png)

5. Depois de alguns segundos, o painel **Discos** da VM lista o disco rígido virtual existente conectado como um disco de dados:

    ![Disco rígido virtual existente anexado como um disco de dados](./media/troubleshoot-recovery-disks-portal/attached-disk.png)


## <a name="mount-the-attached-data-disk"></a>Montar o disco de dados anexado

1. Abra uma conexão de Área de Trabalho Remota para a VM. Selecione a VM no portal e clique em **Conectar**. Baixe e abra o arquivo de conexão RDP. Insira suas credenciais para fazer logon na VM da seguinte maneira:

    ![Fazer logon na VM usando a Área de Trabalho Remota](./media/troubleshoot-recovery-disks-portal/open-remote-desktop.png)

2. Abra **Gerenciador do Servidor** e selecione **Serviços de Arquivo e Armazenamento**. 

    ![Selecionar Serviços de Arquivo e Armazenamento no Gerenciador do Servidor](./media/troubleshoot-recovery-disks-portal/server-manager-select-storage.png)

3. O disco de dados é detectado e anexado automaticamente. Para ver uma lista dos discos conectados, selecione **Discos**. É possível selecionar o disco de dados para exibir informações de volume, incluindo a letra da unidade. O seguinte exemplo mostra o disco de dados anexado e usando **F:**:

    ![Disco anexado e informações de volume no Gerenciador do Servidor](./media/troubleshoot-recovery-disks-portal/server-manager-disk-attached.png)


## <a name="fix-issues-on-original-virtual-hard-disk"></a>Corrigir problemas no disco rígido virtual original
Com o disco rígido virtual existente montado, agora você pode realizar as etapas de manutenção e solução de problemas necessárias. Depois de resolver os problemas, continue com as próximas etapas.


## <a name="unmount-and-detach-original-virtual-hard-disk"></a>Desmontar e desanexar o disco rígido virtual original
Depois de resolver os erros, desanexe o disco rígido virtual existente da VM de solução de problemas. Não é possível usar o disco rígido virtual com nenhuma outra VM até que a concessão que anexa o disco rígido virtual à VM de solução de problemas seja liberada.

1. Na sessão RDP da VM, abra **Gerenciador do Servidor** e selecione **Serviços de Arquivo e Armazenamento**:

    ![Selecionar Serviços de Arquivo e Armazenamento no Gerenciador do Servidor](./media/troubleshoot-recovery-disks-portal/server-manager-select-storage.png)

2. Selecione **Discos** e, em seguida, o disco de dados. Clique com o botão direito do mouse no disco de dados e selecione **Colocar Offline**:

    ![Definir o disco de dados como offline no Gerenciador do Servidor](./media/troubleshoot-recovery-disks-portal/server-manager-set-disk-offline.png)

3. Agora desanexe o disco rígido virtual da VM. Selecione a VM no portal do Azure e clique em **Discos**. Selecione o disco rígido virtual existente e clique em **Desanexar**:

    ![Desanexar um disco rígido virtual existente](./media/troubleshoot-recovery-disks-portal/detach-disk.png)

    Aguarde até que a VM tenha desanexo o disco de dados corretamente antes de continuar.

## <a name="create-vm-from-original-hard-disk"></a>Criar a VM com base no disco rígido original
Para criar uma VM com base no disco rígido virtual original, use [esse modelo do Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd-existing-vnet). O modelo implanta uma VM em uma rede virtual existente, usando a URL do VHD do comando anterior. Clique no botão **Implantar no Azure** da seguinte maneira:

![Implantar a VM com base no modelo do GitHub](./media/troubleshoot-recovery-disks-portal/deploy-template-from-github.png)

O modelo é carregado no portal do Azure para implantação. Insira os nomes da nova VM e dos recursos do Azure existentes e cole a URL no disco rígido virtual existente. Para iniciar a implantação, clique em **Comprar**:

![Implantar a VM com base no modelo](./media/troubleshoot-recovery-disks-portal/deploy-from-image.png)


## <a name="re-enable-boot-diagnostics"></a>Habilitar o diagnóstico de inicialização novamente
Ao criar a VM com base no disco rígido virtual existente, o diagnóstico de inicialização poderá não ser habilitado automaticamente. Para verificar o status do diagnóstico de inicialização e ativá-lo, se necessário, selecione a VM no portal. Em **Monitoramento**, clique em **Configurações de diagnóstico**. Verifique se o status é **Ativado** e se a marca de seleção ao lado de **Diagnóstico de inicialização** está marcada. Se fizer alguma alteração, clique em **Salvar**:

![Atualizar as configurações do diagnóstico de inicialização](./media/troubleshoot-recovery-disks-portal/reenable-boot-diagnostics.png)

## <a name="next-steps"></a>Próximas etapas
Se estiver tendo problemas para se conectar à VM, consulte [Troubleshoot RDP connections to an Azure VM](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Solucionar conexões RDP a uma VM do Azure). Para problemas com o acesso a aplicativos executados na VM, consulte [Solucionar problemas de conectividade do aplicativo em uma VM do Windows](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Para obter mais informações sobre como usar o Resource Manager, consulte [Visão geral do Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).