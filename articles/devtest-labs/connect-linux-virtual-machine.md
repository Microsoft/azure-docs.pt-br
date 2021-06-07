---
title: Conectar-se às suas máquinas virtuais Linux no Azure DevTest Labs
description: Saiba como se conectar à sua máquina virtual Linux em um laboratório (Azure DevTest Labs)
ms.topic: how-to
ms.date: 07/17/2020
ms.openlocfilehash: 52fe245f85034a4c6300615ad8fb6040c1168298
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "86530950"
---
# <a name="connect-to-a-linux-vm-in-your-lab-azure-devtest-labs"></a>Conectar-se a uma VM do Linux em seu laboratório (Azure DevTest Labs)
Este artigo mostra como se conectar à VM do Linux em seu laboratório. 

## <a name="connect-to-a-linux-vm"></a>Conectar-se a uma VM do Linux
1. Entre no [portal do Azure](https://portal.azure.com).
1. Na barra de pesquisa, procure e selecione **DevTest Labs**. 

    :::image type="content" source="./media/connect-linux-virtual-machine/search-select.png" alt-text="Pesquise e selecione DevTest Labs":::    
1. Na lista de laboratórios, selecione seu **laboratório**.

    :::image type="content" source="./media/connect-linux-virtual-machine/select-lab.png" alt-text="Selecione seu laboratório":::            
1. No home page para seu laboratório, selecione sua VM do Linux na lista **minhas máquinas virtuais** . 

    :::image type="content" source="./media/connect-linux-virtual-machine/select-linux-vm.png" alt-text="Selecione sua VM do Linux":::        
5. Na página **visão geral** , você pode ver o FQDN (nome de domínio totalmente qualificado) ou o endereço IP da VM. Você também pode ver a porta, conforme mostrado na imagem a seguir.

    :::image type="content" source="./media/connect-linux-virtual-machine/vm-overview.png" alt-text="Nome de domínio totalmente qualificado para a VM":::    

    Observe que o botão **conectar** está acinzentado, embora a VM seja iniciada. Isso é por design.
6.  Use o SSH para se conectar à VM Linux. O exemplo a seguir conecta-se à VM com `mydtl07172452621450000.eastus.cloudapp.azure.com` o FQDN, com o nome de usuário `vmuser` e a porta `51637` . Insira a senha do usuário para se conectar à VM. 

    ```bash
    ssh vmuser@mydtl07172452621450000.eastus.cloudapp.azure.com -p 51637
    ```

    Você pode usar ferramentas [como de saída ou qualquer](https://www.putty.org/) outro cliente SSH para se conectar à VM. 

    Depois de se conectar usando o SSH, você pode instalar e configurar um ambiente de área de trabalho ([Xfce](https://www.xfce.org)) e a área de trabalho remota ([xrdp](http://xrdp.org)).  Para obter informações detalhadas, consulte [instalar e configurar o área de trabalho remota para se conectar a uma VM do Linux no Azure](../virtual-machines/linux/use-remote-desktop.md). 

## <a name="next-steps"></a>Próximas etapas
[Como conectar-se a uma VM do Windows](connect-windows-virtual-machine.md)
