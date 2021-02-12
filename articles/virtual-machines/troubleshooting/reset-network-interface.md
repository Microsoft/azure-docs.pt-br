---
title: Como redefinir o adaptador de rede de uma VM Windows do Azure | Microsoft Docs
description: Mostra como redefinir o adaptador de rede de uma VM Windows do Azure
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 11/16/2018
ms.author: genli
ms.openlocfilehash: 6c4e507df0f112934979d4e59778b667743cf623
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96022897"
---
# <a name="how-to-reset-network-interface-for-azure-windows-vm"></a>Como redefinir o adaptador de rede de uma VM Windows do Azure 

Este artigo mostra como redefinir o adaptador de rede para uma VM do Windows no Azure para resolver problemas quando você não pode se conectar à VM (Máquina Virtual) do Windows no Microsoft Azure após:

* A desabilitação da NIC (adaptador de rede) padrão. 
* A definição manual de um IP estático para a NIC. 

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="reset-network-interface"></a>Redefinir o adaptador de rede

### <a name="for-vms-deployed-in-resource-group-model"></a>Para as VMs implantadas no modelo de Grupo de recursos

1.  Vá para o [Portal do Azure](https://ms.portal.azure.com).
2.  Selecione a Máquina Virtual afetada.
3.  Selecione **Rede** e, em seguida, selecione o Adaptador de rede da VM.

    ![Localização do adaptador de rede](./media/reset-network-interface/select-network-interface-vm.png)
    
4.  Selecione **Configurações de IP**.
5.  Selecione o IP. 
6.  Se a **atribuição de IP privado**  não for  **estática**, altere-a para **estático**.
7.  Altere o **endereço IP** para outro endereço IP que está disponível na Sub-rede.
8. A máquina virtual será reiniciada para inicializar a nova NIC no sistema.
9.  Tente executar o RDP no computador. Se for bem-sucedido, você poderá alterar o endereço IP Privado novamente para o original, se desejar. Caso contrário, você poderá mantê-lo. 

#### <a name="use-azure-powershell"></a>Usar PowerShell do Azure

1. Verifique se você tem [o mais recente Azure PowerShell](/powershell/azure/) instalado
2. Abra uma sessão privilegiada do Azure PowerShell (Executar como administrador). Execute os seguintes comandos:

    ```powershell
    #Set the variables 
    $SubscriptionID = "<Subscription ID>"
    $VM = "<VM Name>"
    $ResourceGroup = "<Resource Group>"
    $VNET = "<Virtual Network>"
    $IP = "NEWIP"

    #Log in to the subscription 
    Add-AzAccount
    Select-AzSubscription -SubscriptionId $SubscriptionId 
    
    #Check whether the new IP address is available in the virtual network.
    Test-AzureStaticVNetIP –VNetName $VNET –IPAddress  $IP

    #Add/Change static IP. This process will not change MAC address
    Get-AzVM -ResourceGroupName $ResourceGroup -Name $VM | Set-AzureStaticVNetIP -IPAddress $IP | Update-AzVM
    ```
3. Tente executar o RDP no computador.  Se for bem-sucedido, você poderá alterar o endereço IP Privado novamente para o original, se desejar. Caso contrário, você poderá mantê-lo.

### <a name="for-classic-vms"></a>Para VMs Clássicas

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Para redefinir o adaptador de rede, siga estas etapas:

#### <a name="use-azure-portal"></a>Usar o portal do Azure

1.  Vá para o [Portal do Azure]( https://ms.portal.azure.com).
2.  Selecione **Máquinas Virtuais (Clássicas)**.
3.  Selecione a Máquina Virtual afetada.
4.  Selecione **Endereços IP**.
5.  Se a **atribuição de IP privado**  não for  **estática**, altere-a para **estático**.
6.  Altere o **endereço IP** para outro endereço IP que está disponível na Sub-rede.
7.  Clique em **Salvar**.
8.  A máquina virtual será reiniciada para inicializar a nova NIC no sistema.
9.  Tente executar o RDP no computador. Se for bem-sucedido, você poderá optar por reverter o Endereço IP privado para o original.  

#### <a name="use-azure-powershell"></a>Usar PowerShell do Azure

1. Verifique se você tem [as Azure PowerShell mais recentes](/powershell/azure/) instaladas.
2. Abra uma sessão privilegiada do Azure PowerShell (Executar como administrador). Execute os seguintes comandos:

    ```powershell
    #Set the variables 
    $SubscriptionID = "<Subscription ID>"
    $VM = "<VM Name>"
    $CloudService = "<Cloud Service>"
    $VNET = "<Virtual Network>"
    $IP = "NEWIP"

    #Log in to the subscription 
    Add-AzureAccount
    Select-AzureSubscription -SubscriptionId $SubscriptionId 

    #Check whether the new IP address is available in the virtual network.
    Test-AzureStaticVNetIP –VNetName $VNET –IPAddress  $IP
    
    #Add/Change static IP. This process will not change MAC address
    Get-AzureVM -ResourceGroupName $CloudService -Name $VM | Set-AzureStaticVNetIP -IPAddress $IP |Update-AzureVM
    ```
3. Tente executar o RDP no computador. Se for bem-sucedido, você poderá alterar o endereço IP Privado novamente para o original, se desejar. Caso contrário, você poderá mantê-lo. 

## <a name="delete-the-unavailable-nics"></a>Excluir as NICs não disponíveis
Depois que você conseguir conectar a área de trabalho remota ao computador, deverá excluir as NICs antigas para evitar o problema potencial:

1.  Abra o Gerenciador de Dispositivos.
2.  Selecione **Exibir**  >  **Mostrar dispositivos ocultos**.
3.  Selecione **adaptadores de rede**. 
4.  Verifique os adaptadores nomeados como “Adaptador de Rede do Microsoft Hyper-V”.
5.  Você pode ver um adaptador indisponível que está esmaecido. Clique com o botão direito do mouse no adaptador e selecione Desinstalar.

    ![a imagem da NIC](media/reset-network-interface/nicpage.png)

    > [!NOTE]
    > Desinstale somente os adaptadores não disponíveis que têm o nome “Adaptador de Rede do Microsoft Hyper-V”. Se você desinstalar um dos outros adaptadores ocultos, isso poderá causar outros problemas.
    >
    >

6.  Agora, todos os adaptadores não disponíveis deverão ser limpos do sistema.
