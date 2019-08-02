---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 394b242ab46da7821f77e8d008836753f4e358e2
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171871"
---
Nesta etapa, você cria manualmente o ouvinte do grupo de disponibilidade no Gerenciador de Cluster de Failover e no Server Management Studio.

1. Abra o Gerenciador de Cluster de Failover a partir do nó que hospeda a réplica primária.

2. Selecione o nó **Redes** e observe o nome da rede do cluster. Esse nome é usado na variável $ClusterNetworkName no script do PowerShell.

3. Expanda o nome do cluster e, em seguida, clique em **Funções**.

4. No painel **Funções**, clique com o botão direito do mouse no nome do grupo de disponibilidade e, em seguida, selecione **Adicionar recurso** > **Ponto de acesso para o cliente**.
   
    ![Adicionar ponto de acesso para cliente para o grupo de disponibilidade](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678769.gif)

5. Na caixa **Nome**, crie um nome para este novo ouvinte, clique em **Avançar** duas vezes e, em seguida, clique em **Concluir**.  
    Não coloque o ouvinte ou o recurso online neste momento.

6. Clique na guia **Recursos** e, em seguida, expanda o ponto de acesso do cliente que você acabou de criar. 
    O recurso de endereço IP para cada uma das redes de cluster no seu cluster é exibido. Se essa é uma solução somente no Azure, somente um recurso de endereço IP é exibido.

7. Faça uma das opções a seguir:
   
   * Para configurar uma solução híbrida:
     
        a. Clique no botão direito do mouse no recurso de endereço IP que corresponde à sua sub-rede local e selecione **Propriedades**. Observe o nome do endereço IP e o nome da rede.
   
        b. Selecione o **Endereço IP estático**, atribua um endereço IP não utilizado e clique em **OK**.
 
   * Para configurar uma solução somente no Azure:

        a. Clique no botão direito do mouse no recurso de endereço IP que corresponde à sua sub-rede do Azure e selecione **Propriedades**.
       
       > [!NOTE]
       > Se o ouvinte posteriormente não ficar online, devido a um endereço IP problemático selecionado pelo DHCP, você pode configurar um endereço IP estático válido nessa janela de propriedades.
       > 
       > 

       b. Na mesma janela de propriedades **Endereço IP**, alterar o **Nome do Endereço IP**.  
        Esse nome é usado na variável $IPResourceName do script do PowerShell. Se sua solução abrange diversas VNets do Azure, repita essa etapa para cada recurso IP.

