---
title: Como gerenciar interfaces de rede de VMs em seu Azure Stack Edge pro por meio do portal do Azure
description: Saiba como gerenciar interfaces de rede em VMs que são implantadas em sua Azure Stack GPU pro Edge via portal do Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/23/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to manage network interfaces on an Azure Stack Edge Pro device so that I can use it to run applications using Edge compute before sending it to Azure.
ms.openlocfilehash: 84077f174fabd02afcd5171e8d365e8cbd3a52c2
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105027559"
---
# <a name="use-the-azure-portal-to-manage-network-interfaces-on-the-vms-on-your-azure-stack-edge-pro-gpu"></a>Usar o portal do Azure para gerenciar interfaces de rede nas VMs em seu Azure Stack Edge pro GPU

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Você pode criar e gerenciar máquinas virtuais (VMs) em um dispositivo Azure Stack Edge usando portal do Azure, modelos, Azure PowerShell cmdlets e por meio de scripts de CLI do Azure/Python. Este artigo descreve como gerenciar as interfaces de rede em uma VM em execução no dispositivo Azure Stack Edge usando o portal do Azure. 

Ao criar uma VM, você especifica uma interface de rede virtual a ser criada. Talvez você queira adicionar uma ou mais interfaces de rede à máquina virtual após sua criação. Talvez você também queira alterar as configurações de adaptador de rede padrão para uma interface de rede existente.

Este artigo explica como adicionar um adaptador de rede a uma VM existente, alterar as configurações existentes, como o tipo de IP (estático versus dinâmico) e, por fim, remover ou desanexar uma interface existente. 

        
## <a name="about-network-interfaces-on-vms"></a>Sobre interfaces de rede em VMs

Uma interface de rede permite que uma VM (máquina virtual) em execução em seu dispositivo Azure Stack Edge pro se comunique com o Azure e com recursos locais. Quando você habilita uma porta para a rede de computação em seu dispositivo, um comutador virtual é criado nessa interface de rede. Esse comutador virtual é usado para implantar cargas de trabalho de computação, como VMs ou aplicativos em contêineres em seu dispositivo. 

Seu dispositivo dá suporte a apenas um comutador virtual, mas várias interfaces de rede virtual. Cada interface de rede em sua VM tem um endereço IP estático ou dinâmico atribuído a ele. Com endereços IP atribuídos a várias interfaces de rede em sua VM, determinados recursos são habilitados em sua VM. Por exemplo, sua VM pode hospedar vários sites ou serviços com diferentes endereços IP e certificados SSL em um único servidor. Uma VM em seu dispositivo pode servir como uma solução de virtualização de rede, como um firewall ou um balanceador de carga. <!--Is it possible to do that on ASE?-->

<!--There is a limit to how many virtual network interfaces can be created on the virtual switch on your device. See the Azure Stack Edge Pro limits article for details.--> 


## <a name="prerequisites"></a>Pré-requisitos

Antes de começar a gerenciar as VMs em seu dispositivo por meio do portal do Azure, verifique se:

1. Você habilitou uma interface de rede para computação em seu dispositivo. Essa ação cria um comutador virtual nessa interface de rede em sua VM. 
    1. Na interface do usuário local do seu dispositivo, vá para **computação**. Selecione o adaptador de rede que será usado para criar um comutador virtual.

        > [!IMPORTANT] 
        > Você só pode configurar uma porta para computação.

    1. Habilite a computação no adaptador de rede. Azure Stack GPU pro Edge cria e gerencia um comutador virtual correspondente a esse adaptador de rede.

1. Você tem pelo menos uma VM implantada em seu dispositivo. Para criar essa VM, consulte as instruções em [implantar VM em seu Azure Stack Edge pro por meio do portal do Azure](azure-stack-edge-gpu-deploy-virtual-machine-portal.md).

1. Sua VM deve estar no estado **parado** . Para interromper a VM, vá para **máquinas virtuais > visão geral** e selecione a VM que você deseja parar. Na página Propriedades da VM, selecione **parar** e, em seguida, selecione **Sim** quando a confirmação for solicitada. Antes de adicionar, editar ou excluir interfaces de rede, você deve parar a VM.

    ![Página de propriedades parar VM da VM](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/stop-vm-2.png)


## <a name="add-a-network-interface"></a>Adicionar uma interface de rede

Siga estas etapas para adicionar uma interface de rede a uma máquina virtual implantada em seu dispositivo. 

1. Vá para a máquina virtual que você interrompeu e, em seguida, vá para a página de **Propriedades da VM** . Selecione **Rede**.
    
    ![Selecionar rede na página de propriedades da VM](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-1.png)

2. Na folha **rede** , na barra de comandos, selecione **+ Adicionar interface de rede**.

    ![Selecione Adicionar interface de rede](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-2.png)

3. Na folha **Adicionar interface de rede** , insira os seguintes parâmetros:

    
    |Column1  |Column2  |
    |---------|---------|
    |Nome     | Um nome exclusivo dentro do grupo de recursos. O nome não pode ser alterado após a criação do adaptador de rede. Para gerenciar várias interfaces de rede com facilidade, use as sugestões fornecidas nas [convenções de nomenclatura](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming).     |
    |Rede virtual| A rede virtual associada ao comutador virtual criado no seu dispositivo quando você habilitou a computação na interface de rede. Há apenas uma rede virtual associada ao seu dispositivo.         |         
    |Sub-rede     | Uma sub-rede dentro da rede virtual selecionada. Esse campo é preenchido automaticamente com a sub-rede associada à interface de rede na qual você habilitou a computação.         |       
    |Atribuição de IP   | Um IP estático ou dinâmico para a interface de rede. O IP estático deve ser um IP disponível e livre do intervalo de sub-rede especificado. Escolha dinâmico se houver um servidor DHCP no ambiente.        | 

    ![Adicionar uma folha de interface de rede](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-3.png)

4. Você verá uma notificação informando que a criação da interface de rede está em andamento.

    ![Notificação quando a interface de rede está sendo criada](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-4.png)

5.  Depois que a interface de rede é criada com êxito, a lista de interfaces de rede é atualizada para exibir a interface recém-criada.

    ![Lista atualizada de interfaces de rede](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-5.png)


## <a name="edit-a-network-interface"></a>Editar uma interface de rede

Siga estas etapas para editar uma interface de rede associada a uma máquina virtual implantada em seu dispositivo.

1. Vá para a máquina virtual que você interrompeu e vá para a página de **Propriedades da VM** . Selecione **Rede**.

1. Na lista de adaptadores de rede, selecione a interface que você deseja editar. Na extrema direita da interface de rede selecionada, selecione o ícone de edição (lápis).  

    ![Selecione uma interface de rede para editar](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/edit-nic-1.png)

1. Na folha **Editar interface de rede** , você só pode alterar a atribuição de IP da interface de rede. O nome, a rede virtual e a sub-rede associada ao adaptador de rede não podem ser alterados após sua criação. Altere a **atribuição de IP** para estática e salve as alterações.

    ![Alterar a atribuição de IP para a interface de rede](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/edit-nic-2.png)

1. A lista de atualizações da interface de rede para exibir a interface de rede atualizada.


## <a name="detach-a-network-interface"></a>Desanexar uma interface de rede

Siga estas etapas para desanexar ou remover uma interface de rede associada a uma máquina virtual implantada em seu dispositivo.

1. Vá para a máquina virtual que você interrompeu e vá para a página de **Propriedades da VM** . Selecione **Rede**.

1. Na lista de adaptadores de rede, selecione a interface que você deseja editar. Na extrema direita da interface de rede selecionada, selecione o ícone desanexar (desconectar).  

    ![Selecione uma interface de rede para desanexar](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/detach-nic-1.png)

1. Depois que a interface é completamente desanexada, a lista de interfaces de rede é atualizada para exibir as interfaces restantes.

## <a name="next-steps"></a>Próximas etapas

Para saber como implantar máquinas virtuais em seu dispositivo Azure Stack Edge pro, confira [implantar máquinas virtuais por meio do portal do Azure](azure-stack-edge-gpu-deploy-virtual-machine-portal.md).
