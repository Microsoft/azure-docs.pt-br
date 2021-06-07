---
title: Como testar seu aplicativo no Azure | Microsoft Docs
description: Saiba como criar um compartilhamento de arquivos em um laboratório e montá-lo em seu computador local e uma máquina virtual no ambiente de laboratório e, em seguida, implantar aplicativos de área de trabalho/Web para o compartilhamento de arquivos e testá-los.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: b2dbbf349da4e352fe20a22db03cc9063d801990
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87282238"
---
# <a name="test-your-app-in-azure"></a>Teste seu aplicativo no Azure 
Este artigo apresenta as etapas para testar seu aplicativo no Azure usando o DevTest Labs. Primeiro, configure um compartilhamento de arquivo em um laboratório e monte-o como uma unidade em seu computador de desenvolvimento local e uma VM dentro de um laboratório. Em seguida, use o Visual Studio 2019 para implantar seu aplicativo no compartilhamento de arquivos para que você possa executar o aplicativo na VM no laboratório.  

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos 
1. Escolha [Criar uma assinatura do Azure](https://azure.microsoft.com/free/) se você ainda não tiver uma e entre no [portal do Azure](https://portal.azure.com).
2. Siga as instruções [neste artigo](devtest-lab-create-lab.md) para criar um laboratório usando o Azure DevTest Labs. Fixe o laboratório em seu painel para que possa localizá-lo facilmente na próxima vez que entrar. O Azure DevTest Labs permite criar recursos no Azure rapidamente, minimizando o desperdício e controlando os custos. Para saber mais sobre o DevTest Labs, confira a [Visão geral](devtest-lab-overview.md). 
3. Crie uma conta de Armazenamento do Azure no grupo de recursos do laboratório seguindo as instruções no artigo [Criar uma conta de armazenamento](../storage/common/storage-account-create.md). Na página **Criar conta de armazenamento**, selecione **Usar existente** para **Grupo de recursos** e selecione o **grupo de recursos do laboratório**. 
4. Crie um compartilhamento de arquivos no Armazenamento do Azure seguindo as instruções no artigo [Criar um compartilhamento de arquivos no arquivos do Azure](../storage/files/storage-how-to-create-file-share.md). 

## <a name="mount-the-file-share-on-your-local-machine"></a>Montar o compartilhamento de arquivos em seu computador local
1. No computador local, use o script na seção [montar o compartilhamento de arquivos do Azure](../storage/files/storage-how-to-use-files-windows.md#mount-the-azure-file-share) de [usar um compartilhamento de arquivos do Azure com o Windows](../storage/files/storage-how-to-use-files-windows.md) . 
2. Em seguida, use o comando `net use` para montar o compartilhamento de arquivos em seu computador. Aqui está o comando de exemplo: especifique o nome do seu Armazenamento do Azure e o nome do compartilhamento de arquivo antes de executar o comando. 

    `net use Z: \\<YOUR AZURE STORAGE NAME>.file.core.windows.net\<YOUR FILE SHARE NAME> /persistent:yes`

## <a name="create-a-vm-in-the-lab"></a>Criar uma VM no laboratório
1. Na página **Compartilhamento de arquivos**, selecione **grupo de recursos** no menu de navegação estrutural na parte superior. Você vê a página **Grupo de recursos**. 
    
    ![Selecione o grupo de recursos no menu de navegação estrutural](media/test-app-in-azure/select-resource-group-bread-crump.png)
2. Na página **Grupo de recursos**, selecione o **laboratório** criado no DevTest Labs.

    ![Selecione o laboratório](media/test-app-in-azure/select-devtest-lab-in-resource-group.png)
3. Na página **DevTest Lab** para seu laboratório, selecione **+Adicionar** na barra de ferramentas. 

    ![Adicionar botão para o laboratório](media/test-app-in-azure/add-button-in-lab.png)
4. Na página **Escolher uma base**, pesquise **smalldisk** e selecione **[smalldisk] Windows Server 2016 Data Center**. 

    ![Escolha o Windows Server de disco pequeno](media/test-app-in-azure/choose-small-disk-windows-server.png)
5. Na página **Máquina Virtual**, especifique **nome da máquina virtual**, **nome de usuário**, **senha** e selecione **Criar**.    
    
    ![Criar página da máquina virtual](media/test-app-in-azure/create-virtual-machine-page.png)    

## <a name="mount-the-file-share-on-your-vm"></a>Montar o compartilhamento de arquivos em sua VM
1. Depois de criar a máquina virtual com êxito, selecione a **máquina virtual** na lista.    

    ![Selecione a VM do laboratório](media/test-app-in-azure/select-lab-vm.png)
2. Selecione **Conectar** na barra de ferramentas para conectar-se à VM. 
3. [Instale o Azure PowerShell](/powershell/azure/install-az-ps).
4. Siga as instruções na seção Montar o compartilhamento de arquivos. 

## <a name="publish-your-app-from-visual-studio"></a>Publique seu aplicativo do Visual Studio
Nesta seção, você publica seu aplicativo do Visual Studio para uma VM de teste na nuvem.

1. Crie um aplicativo Web/desktop usando o Visual Studio 2019.
2. Compile seu aplicativo.
3. Para publicar seu aplicativo, clique com o botão direito do mouse no projeto em **Gerenciador de Soluções**, e selecione **Publicar**. 
4. No **Assistente de publicação**, insira a **unidade** mapeada para o compartilhamento de arquivos.

    **Aplicativo de desktop:**

    ![Aplicativo da área de trabalho](media/test-app-in-azure/desktop-app.png)

    **Aplicativo Web:**

    ![Aplicativo Web](media/test-app-in-azure/web-app.png)

1. Selecione **Avançar** para concluir o fluxo de trabalho de publicação e, em seguida, selecione **Concluir**. Quando você concluir as etapas do assistente, o Visual Studio compilará seu aplicativo e publicará o compartilhamento de arquivos. 


## <a name="test-the-app-on-your-test-vm-in-the-lab"></a>Testar o aplicativo em sua VM de teste no laboratório

1. Navegue até a página da máquina virtual para sua VM no laboratório. 
2. Selecione **Iniciar** na barra de ferramentas para iniciar a VM se ela estiver no estado parado. Você pode configurar as políticas de início automático e o desligamento automático para a sua VM para evitar iniciar e interromper a cada vez. 
3. Selecione **Conectar**.

    ![Página da máquina virtual](media/test-app-in-azure/virtual-machine-page.png)
4. Na máquina virtual, inicialize o **Explorador de Arquivos** e selecione **Este PC** para localizar seu compartilhamento de arquivos.

    ![Localizar compartilhamento na VM](media/test-app-in-azure/find-share-on-vm.png)

    > [!NOTE]
    > Se, por algum motivo, você não conseguir localizar o compartilhamento de arquivos em sua máquina virtual ou em seu computador local, você poderá remontá-lo executando o comando `net use`. Você pode encontrar o comando `net use` no Assistente **Connect** de seu **Compartilhamento de Arquivos** no portal do Azure.
1. Abra o compartilhamento de arquivos e confirme que você vê o aplicativo implantado do Visual Studio. 

    ![Abra o compartilhamento na VM](media/test-app-in-azure/open-file-share.png)

    Agora você pode acessar e testar seu aplicativo no teste de VM criado no Azure.

## <a name="next-steps"></a>Próximas etapas
Veja os seguintes artigos para aprender a usar as VMs em um laboratório. 

- [Adicionar uma VM a um laboratório](devtest-lab-add-vm.md)
- [Reiniciar uma VM de laboratório](devtest-lab-restart-vm.md)
- [Redimensionar uma VM de laboratório](devtest-lab-resize-vm.md)
