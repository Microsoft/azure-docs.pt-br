---
title: Conectar-se às suas máquinas virtuais do Windows no Azure DevTest Labs
description: Saiba como se conectar à sua máquina virtual do Windows em um laboratório (Azure DevTest Labs)
ms.topic: how-to
ms.date: 07/17/2020
ms.openlocfilehash: e1e786daa396548030976159d1b150caa4b24396
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86540639"
---
# <a name="connect-to-a-windows-vm-in-your-lab-azure-devtest-labs"></a>Conectar-se a uma VM do Windows em seu laboratório (Azure DevTest Labs)
Este artigo mostra como se conectar a uma VM do Windows em seu laboratório. 

## <a name="connect-to-a-windows-vm"></a>Conectar-se a uma VM do Windows
1. Entre no [portal do Azure](https://portal.azure.com).
1. Na barra de pesquisa, procure e selecione **DevTest Labs**. 

    :::image type="content" source="./media/connect-windows-virtual-machine/search-select.png" alt-text="Pesquise e selecione DevTest Labs":::    
1. Na lista de laboratórios, selecione seu **laboratório**.

    :::image type="content" source="./media/connect-windows-virtual-machine/select-lab.png" alt-text="Pesquise e selecione DevTest Labs":::            
1. No home page para seu laboratório, selecione sua VM do Windows na lista **minhas máquinas virtuais** . 

    :::image type="content" source="./media/connect-windows-virtual-machine/select-windows-vm.png" alt-text="Pesquise e selecione DevTest Labs":::                
1. Na página **máquina virtual** para sua VM, selecione **conectar** na barra de ferramentas. Se a VM for interrompida, selecione **Iniciar** primeiro para iniciar a VM.

    :::image type="content" source="./media/connect-windows-virtual-machine/select-connect.png" alt-text="Pesquise e selecione DevTest Labs":::                    
1. Se você estiver usando o navegador Edge, verá o link para o **arquivo RDP baixado** na parte inferior do navegador. 

    :::image type="content" source="./media/connect-windows-virtual-machine/rdp-download.png" alt-text="Pesquise e selecione DevTest Labs":::                        
1. Abra o arquivo RDP e insira suas credenciais de VM que você digitou ao criar a VM. Você deve estar conectado à VM do Windows agora. 

## <a name="next-steps"></a>Próximas etapas
[Como conectar-se a uma VM do Linux](connect-linux-virtual-machine.md)
