---
title: Configurar uma rede virtual no Azure DevTest Labs | Microsoft Docs
description: "Saiba como configurar uma rede virtual e sub-rede existente e usá-las em uma VM com o Azure DevTest Labs"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 6cda99c2-b87e-4047-90a0-5df10d8e9e14
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2017
ms.author: tarcher
ms.openlocfilehash: 19dbb1625f46f8864413dc538a96b2413bc6eea0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="configure-a-virtual-network-in-azure-devtest-labs"></a>Configurar uma rede virtual no Azure DevTest Labs
Conforme explicado no artigo [Adicionar uma VM com artefatos a um laboratório](devtest-lab-add-vm-with-artifacts.md), quando cria uma VM em um laboratório, você pode especificar uma rede virtual configurada. Um cenário no qual isso é possível é quando você precisa acessar os recursos da rede corporativa por meio de suas VMs usando a rede virtual configurada com o ExpressRoute ou a VPN site a site. As seções a seguir ilustram como adicionar sua rede virtual existente às configurações de Rede Virtual de um laboratório, para que ela esteja disponível para escolha durante a criação de suas VMs.

## <a name="configure-a-virtual-network-for-a-lab-using-the-azure-portal"></a>Configurar uma rede virtual para um laboratório usando o Portal do Azure
As etapas a seguir orientarão você pela adição de uma rede virtual (e sub-rede) existente a um laboratório, para que ela possa ser usada durante a criação de uma VM no mesmo Laboratório. 

1. Entre no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selecione **Mais Serviços** e selecione **Laboratórios de Desenvolvimento/Teste** na lista.
3. Na lista de laboratórios, selecione o laboratório desejado. 
4. Na folha do laboratório, selecione **Configuração e Políticas**.
5. Na folha **Configuração e políticas** do laboratório, selecione **Redes virtuais**.
6. Na folha **Redes virtuais** , você vê uma lista das redes virtuais configuradas para o laboratório atual, bem como a rede virtual padrão que é criada para o laboratório. 
7. Selecione **+ Adicionar**.
   
    ![Adicionar uma rede virtual existente ao seu laboratório](./media/devtest-lab-configure-vnet/lab-settings-vnet-add.png)
8. Na folha **Rede virtual**, selecione **[Selecionar rede virtual]**.
   
    ![Selecionar uma rede virtual existente](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet1.png)
9. Na folha **Escolher rede virtual** , selecione a rede virtual desejada. A folha mostra todas as redes virtuais que estão na mesma região da assinatura do que o laboratório.  
10. Depois de selecionar uma rede virtual, você retornará à **Rede virtual**. Clique na sub-rede na lista na parte inferior da folha.

    ![Lista de sub-redes](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet2.png)
    
    A Folha de Sub-rede do laboratório é exibida.

    ![Folha Sub-rede do laboratório](./media/devtest-lab-configure-vnet/lab-subnet.png)

11. Especifique um **nome da Sub-rede do laboratório**.
12. Para permitir o uso de uma sub-rede na criação da VM do laboratório, selecione **Usar na criação da máquina virtual**.
13. Para habilitar um [endereço IP público compartilhado](devtest-lab-shared-ip.md), selecione **Habilitar IP público compartilhado**.
14. Para permitir endereços IP públicos em uma sub-rede, selecione **Permitir criação de IP público**.
15. No campo **Máximo de máquinas virtuais por usuário**, especifique o número máximo de VMs por usuário para cada sub-rede. Se você quiser um número irrestrito de VMs, deixe esse campo em branco.
16. Selecione **OK** para fechar a folha Sub-rede do Laboratório.
17. Selecione **Salvar** para fechar a folha da Rede virtual.
18. Agora que a rede virtual está configurada, ela poderá ser selecionada durante a criação de uma VM. 
    Para saber como criar uma VM e especificar uma rede virtual, consulte o artigo [Adicionar uma VM com artefatos a um laboratório](devtest-lab-add-vm-with-artifacts.md). 

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Próximas etapas
Depois de adicionar a rede virtual desejada ao seu laboratório, a próxima etapa será [adicionar uma VM ao seu laboratório](devtest-lab-add-vm-with-artifacts.md).

