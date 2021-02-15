---
title: Configurar uma rede virtual no Azure DevTest Labs | Microsoft Docs
description: Saiba como configurar uma rede virtual e sub-rede existente e usá-las em uma VM com o Azure DevTest Labs
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 21fe23b6be6fdc924c5502ff97f9c03a3f3e759d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87283547"
---
# <a name="configure-a-virtual-network-in-azure-devtest-labs"></a>Configurar uma rede virtual no Azure DevTest Labs
Conforme explicado no artigo [Adicionar uma VM a um laboratório](devtest-lab-add-vm.md), quando cria uma VM em um laboratório, você pode especificar uma rede virtual configurada. Por exemplo, você pode precisar acessar os recursos da rede corporativa por meio de suas VMs usando a rede virtual configurada com o ExpressRoute ou a VPN site a site.

Este artigo explica como adicionar sua rede virtual existente às configurações de Rede Virtual de um laboratório, para que ela esteja disponível para escolha durante a criação de suas VMs.

> [!NOTE]
> Para saber mais sobre os custos associados ao serviço de rede virtual do Azure, consulte [preços da rede virtual do Azure](../virtual-network/virtual-networks-overview.md#pricing).

## <a name="configure-a-virtual-network-for-a-lab-using-the-azure-portal"></a>Configurar uma rede virtual para um laboratório usando o Portal do Azure
As etapas a seguir orientarão você pela adição de uma rede virtual (e sub-rede) existente a um laboratório, para que ela possa ser usada durante a criação de uma VM no mesmo Laboratório. 

1. Entre no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selecione **Todos os Serviços** e selecione **Laboratórios de Desenvolvimento/Teste** na lista.
1. Na lista de laboratórios, selecione o laboratório desejado. 
1. No painel principal do laboratório, selecione **Configuração e políticas**.

    ![Acessar as políticas e configuração do laboratório](./media/devtest-lab-configure-vnet/policies-menu.png)
1. Na seção **RECURSOS EXTERNOS**, selecione **Redes virtuais**. Uma lista das redes virtuais configuradas para o laboratório atual é exibida, bem como a rede virtual padrão criada para o laboratório. 
1. Selecione **+ Adicionar**.
   
    ![Adicionar uma rede virtual existente ao seu laboratório](./media/devtest-lab-configure-vnet/lab-settings-vnet-add.png)
1. No painel **Rede virtual**, selecione **[Selecionar rede virtual]**.
   
    ![Selecionar uma rede virtual existente](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet1.png)
1. No painel **Escolher rede virtual**, selecione a rede virtual desejada. A lista é exibida mostrando todas as redes virtuais que estão sob a mesma região na assinatura que o laboratório.
1. Após selecionar uma rede virtual, você retornará para o painel **Rede virtual**. Selecione a sub-rede na lista na parte inferior.

    ![Lista de sub-redes](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet2.png)
    
    O painel Sub-rede do Laboratório é exibido.

    ![Painel Sub-rede do laboratório](./media/devtest-lab-configure-vnet/lab-subnet.png)
     
   - Especifique um **nome da Sub-rede do laboratório**.
   - Para permitir que uma sub-rede seja usada na criação da VM do laboratório, selecione **usar na criação da máquina virtual**.
   - Para habilitar um [endereço IP público compartilhado](devtest-lab-shared-ip.md), selecione **Habilitar IP público compartilhado**.
   - Para permitir endereços IP públicos em uma sub-rede, selecione **Permitir criação de IP público**.
   - No campo **máximo de máquinas virtuais por usuário** , especifique o máximo de VMs por usuário para cada sub-rede. Se você quiser um número irrestrito de VMs, deixe esse campo em branco.
1. Selecione **OK** para fechar o painel Sub-rede do Laboratório.
1. Selecione **Salvar** para fechar o painel da Rede virtual.

Agora que a rede virtual está configurada, ela poderá ser selecionada durante a criação de uma VM. Para saber como criar uma VM e especificar uma rede virtual, consulte o artigo [Adicionar uma VM a um laboratório](devtest-lab-add-vm.md). 

A [Documentação de Rede Virtual](../virtual-network/index.yml) do Azure fornece mais informações sobre como usar VNets, incluindo como configurar e gerenciar uma VNet e conectá-la à sua rede local.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Próximas etapas
Depois de adicionar a rede virtual desejada ao seu laboratório, a próxima etapa será [adicionar uma VM ao seu laboratório](devtest-lab-add-vm.md).
