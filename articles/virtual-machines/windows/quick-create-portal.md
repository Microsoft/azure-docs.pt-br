---
title: Início Rápido - Criar uma VM Windows no Portal do Azure | Microsoft Docs
description: Neste início rápido, você aprende a usar o portal do Azure para criar uma máquina virtual Windows
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 07/02/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: fed118ee78e6a11f3492060a55ac9847bb5b6a88
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67720047"
---
# <a name="quickstart-create-a-windows-virtual-machine-in-the-azure-portal"></a>Início Rápido: Criar uma máquina virtual do Windows no Portal do Azure

As máquinas virtuais (VM) do Azure podem ser criadas por meio do Portal do Azure. Esse método fornece uma interface de usuário baseada em navegador para criar as VMS seus recursos relacionados. Este início rápido mostra como usar portal do Azure para implantar uma VM (máquina virtual) no Azure que executa o Windows Server 2016. Para ver a VM em ação, você habilita o protocolo RDP na VM e instala o servidor Web do IIS.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em https://portal.azure.com.

## <a name="create-virtual-machine"></a>Criar máquina virtual

1. Escolha **Criar um recurso** no canto superior esquerdo do portal do Azure.

1. Na página **Novo**, em **Popular**, selecione **Datacenter do Windows Server 2016**.

1. Na guia **Básico**, em **Detalhes do projeto**, verifique se a assinatura correta está selecionada e, em seguida, escolha **Criar** grupo de recursos. Digite *myResourceGroup* para o nome. 

    ![Criar um grupo de recursos para sua VM](./media/quick-create-portal/project-details.png)

1. Em **Detalhes da instância**, digite *myVM* para o **Nome da máquina virtual** e escolha *Leste dos EUA* para **Localização**. Deixe os outros padrões.

    ![Seção de detalhes da instância](./media/quick-create-portal/instance-details.png)

1. Em **Conta de administrador**, forneça um nome de usuário, como *azureuser* e uma senha. A senha deve ter no mínimo 12 caracteres e atender a [requisitos de complexidade definidos](faq.md#what-are-the-password-requirements-when-creating-a-vm).

    ![Insira seu nome de usuário e sua senha](./media/quick-create-portal/administrator-account.png)

1. Em **Regras de porta de entrada**, escolha **Permitir portas selecionadas** e, em seguida, selecione **RDP (3389)** e **HTTP** na lista suspensa.

    ![Abrir portas para RDP e HTTP](./media/quick-create-portal/inbound-port-rules.png)

1. Deixe os padrões restantes e, em seguida, selecione o botão **Examinar + criar** na parte inferior da página.

    ![Examinar e criar](./media/quick-create-portal/review-create.png)


## <a name="connect-to-virtual-machine"></a>Conectar-se à máquina virtual

Inicie uma conexão da área de trabalho remota para a máquina virtual. Estas instruções ensinam a se conectar aàsua VM de um computador com Windows. Em um Mac, você precisa de um cliente RDP, como este [Cliente de Área de Trabalho Remota](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12) da Mac App Store.

1. Clique no botão **Conectar** na página de propriedades da máquina virtual. 

    ![Conecte-se a uma VM do Azure no portal](./media/quick-create-portal/portal-quick-start-9.png)
    
2. Na página **Conectar-se à máquina virtual**, mantenha as opções padrão para conectar-se ao nome DNS na porta 3389 e clique em **Realizar o download do arquivo RDP**.

2. Abra o arquivo RDP baixado e clique em **Conectar** quando solicitado. 

3. Na janela **Segurança do Windows**, selecione **Mais opções** e **Usar uma conta diferente**. Digite o nome do usuário como **localhost**\\*nomedeusuário*, insira a senha que você criou para a máquina virtual e, sem seguida, clique em **OK**.

4. Você pode receber um aviso do certificado durante o processo de logon. Clique em **Sim** ou em **Continuar** para criar a conexão.

## <a name="install-web-server"></a>Instalar servidor Web

Para ver a VM em ação, instale o servidor Web do IIS. Abra um prompt do PowerShell na VM e execute o seguinte comando:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Quando terminar, feche a conexão RDP com a VM.


## <a name="view-the-iis-welcome-page"></a>Exibir a página de boas-vindas do IIS

No portal, selecione a VM e na visão geral da VM, use o botão **Clique para copiar** à direita do endereço IP para copiá-lo e colá-lo em uma guia do navegador. A página de boas-vinda do IIS padrão será aberta e deve ter esta aparência:

![Site do IIS padrão](./media/quick-create-powershell/default-iis-website.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando o grupo de recursos, a máquina virtual e todos os recursos relacionados não forem mais necessários, exclua-os. Selecione o grupo de recursos da máquina virtual e, em seguida, selecione **Excluir**. Confirme o nome do grupo de recursos terminar de excluir os recursos.

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você implantou uma máquina virtual simples, abriu uma porta de rede para o tráfego da Web e instalou um servidor Web básico. Para saber mais sobre máquinas virtuais do Azure, continue o tutorial para VMs do Windows.

> [!div class="nextstepaction"]
> [Tutoriais de máquina virtual do Windows Azure](./tutorial-manage-vm.md)
