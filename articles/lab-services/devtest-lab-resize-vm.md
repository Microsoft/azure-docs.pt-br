---
title: Redimensionar uma VM em um laboratório no Azure DevTest Labs | Microsoft Docs
description: Saiba como redimensionar uma máquina virtual no Azure DevTest Labs
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 8460f09e-482f-48ba-a57a-c95fe8afa001
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: spelluru
ms.openlocfilehash: a0bc618a9c0a02aae884d8be359df6bdbf4c0d2a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60868041"
---
# <a name="resize-a-vm-in-a-lab-in-azure-devtest-labs"></a>Redimensionar uma VM em um laboratório no Azure DevTest Labs
Um dos recursos importantes das máquinas virtuais do Azure é o que permite alterar o tamanho de uma VM (máquina virtual) com base em suas necessidades para CPU, rede ou desempenho do disco. Agora o Azure DevTest Labs dá suporte a esse recurso para VMs em um laboratório. O recurso de redimensionamento cumpre a política de laboratório para tamanhos de VM permitidos no laboratório. Ou seja, é possível alterar o tamanho de uma VM para apenas tamanhos permitidos no laboratório. 


## <a name="steps-to-resize-a-vm-in-a-lab"></a>Etapas para redimensionar uma VM em um laboratório 
Para redimensionar uma VM em um laboratório no Azure DevTest Labs, siga estas etapas: 

> [!NOTE]
> Se estiver conectado à VM por meio de uma sessão de área de trabalho remota (RDP), salve seu trabalho e desconecte-se da VM antes de redimensioná-la.

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Selecione **Todos os Serviços** e selecione **Laboratórios de Desenvolvimento/Teste** na lista.
3. Na lista de laboratórios, selecione o laboratório que inclui a VM que você deseja redimensionar.  
4. No painel esquerdo, selecione **Minhas Máquinas Virtuais**. 
5. Na lista de VMs, selecione uma VM.
6. Selecione **Interromper** na barra de ferramentas se a VM estiver em execução. Verificar o status da operação na janela **Notificações**. Aguarde até que a VM seja interrompida e, em seguida, feche a janela **Notificações**. 

    ![Pare a VM.](media/devtest-lab-resize-vm/stop-vm.png)
1. Na página Máquina Virtual de sua VM, selecione **Tamanho** nas **CONFIGURAÇÕES** no menu esquerdo.

    ![Menu de tamanho](media/devtest-lab-resize-vm/size-menu.png)
1. Na janela **Escolher um tamanho**, procure e selecione um tamanho da sua VM e clique em **Selecionar**.     
1. Verifique o status da operação de redimensionamento na janela **Notificações**.

    ![Status do redimensionamento](media/devtest-lab-resize-vm/resize-status.png)
10. Depois que a operação de redimensionamento for bem-sucedida, feche a janela **Notificações**. 
11. Selecione **Visão geral** no menu à esquerda e selecione **Reiniciar** na barra de ferramentas para reiniciar a VM. 

## <a name="next-steps"></a>Próximas etapas
Para obter informações detalhadas sobre o recurso de redimensionamento compatível com as máquinas virtuais do Azure, confira [Redimensionar máquinas virtuais](https://azure.microsoft.com/blog/resize-virtual-machines/).


