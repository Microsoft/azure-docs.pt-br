---
title: Redimensionar uma VM em um laboratório no Azure DevTest Labs
description: Saiba como alterar o tamanho de uma VM (máquina virtual) em Azure DevTest Labs com base em suas necessidades de desempenho de CPU, rede ou disco.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 592be0862bc33ead86fe43d37753fb72fde120c6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85482013"
---
# <a name="resize-a-vm-in-a-lab-in-azure-devtest-labs"></a>Redimensionar uma VM em um laboratório no Azure DevTest Labs
Um dos recursos importantes das máquinas virtuais do Azure é o que permite alterar o tamanho de uma VM (máquina virtual) com base em suas necessidades para CPU, rede ou desempenho do disco. Agora o Azure DevTest Labs dá suporte a esse recurso para VMs em um laboratório. O recurso de redimensionamento cumpre a política de laboratório para tamanhos de VM permitidos no laboratório. Ou seja, é possível alterar o tamanho de uma VM para apenas tamanhos permitidos no laboratório. 


## <a name="steps-to-resize-a-vm-in-a-lab"></a>Etapas para redimensionar uma VM em um laboratório 
Para redimensionar uma VM em um laboratório no Azure DevTest Labs, siga estas etapas: 

> [!NOTE]
> Se estiver conectado à VM por meio de uma sessão de área de trabalho remota (RDP), salve seu trabalho e desconecte-se da VM antes de redimensioná-la.

1. Entre no [portal do Azure](https://portal.azure.com).
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


