---
title: Como redefinir o adaptador de rede de uma VM Windows do Azure | Microsoft Docs
description: Mostra como redefinir o adaptador de rede de uma VM Windows do Azure
services: virtual-machines-windows, azure-resource-manager
documentationcenter: 
author: genlin
manager: willchen
editor: 
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 06/26/2017
ms.author: genli
ms.openlocfilehash: b8eecb9d546d5922844ede3744d80b951334b5d3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-reset-network-interface-for-azure-windows-vm"></a>Como redefinir o adaptador de rede de uma VM Windows do Azure 

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Você não consegue se conectar à VM (Máquina Virtual) Windows do Microsoft Azure depois de desabilitar a NIC (Adaptador de Rede) padrão ou definir um IP estático manualmente para a NIC. Este artigo mostra como redefinir o adaptador de rede de uma VM Windows do Azure, o que resolverá o problema de conexão remota.

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]
## <a name="reset-network-interface"></a>Redefinir o adaptador de rede

### <a name="for-classic-vms"></a>Para VMs Clássicas

Para redefinir o adaptador de rede, siga estas etapas:

1.  Vá para o [Portal do Azure]( https://ms.portal.azure.com).
2.  Selecione **Máquinas Virtuais (Clássicas)**.
3.  Selecione a Máquina Virtual afetada.
4.  Selecione **Endereços IP**.
5.  Se a **Atribuição de IP privado** não for **Estática**, altere-a para **Estática**.
6.  Altere o **endereço IP** para outro endereço IP que está disponível na Sub-rede.
7.  Selecione Salvar.
8.  A máquina virtual será reiniciada para inicializar a nova NIC no sistema.
9.  Tente executar o RDP no computador. Se for bem-sucedido, você poderá alterar o endereço IP Privado novamente para o original, se desejar. Caso contrário, você poderá mantê-lo. 

### <a name="for-vms-deployed-in-resource-group-model"></a>Para as VMs implantadas no modelo de Grupo de recursos

1.  Vá para o [Portal do Azure]( https://ms.portal.azure.com).
2.  Selecione a Máquina Virtual afetada.
3.  Selecione **Adaptadores de Rede**.
4.  Selecione o Adaptador de Rede associado ao computador
5.  Selecione **Configurações de IP**.
6.  Selecione o IP. 
7.  Se a **Atribuição de IP privado** não for **Estática**, altere-a para **Estática**.
8.  Altere o **endereço IP** para outro endereço IP que está disponível na Sub-rede.
9. A máquina virtual será reiniciada para inicializar a nova NIC no sistema.
10. Tente executar o RDP no computador. Se for bem-sucedido, você poderá alterar o endereço IP Privado novamente para o original, se desejar. Caso contrário, você poderá mantê-lo. 

## <a name="delete-the-unavailable-nics"></a>Excluir as NICs não disponíveis
Depois que você conseguir conectar a área de trabalho remota ao computador, deverá excluir as NICs antigas para evitar o problema potencial:

1.  Abra o Gerenciador de Dispositivos.
2.  Selecione **Exibir** > **Mostrar dispositivos ocultos**.
3.  Selecione **Adaptadores de Rede**. 
4.  Verifique os adaptadores nomeados como “Adaptador de Rede do Microsoft Hyper-V”.
5.  Talvez você veja um adaptador não disponível que está esmaecido. Clique com o botão direito do mouse no adaptador e, em seguida, selecione Desinstalar.

    ![a imagem da NIC](media/reset-network-interface/nicpage.png)

    > [!NOTE]
    > Desinstale somente os adaptadores não disponíveis que têm o nome “Adaptador de Rede do Microsoft Hyper-V”. Se você desinstalar um dos outros adaptadores ocultos, isso poderá causar outros problemas.
    >
    >

6.  Agora todos os adaptadores não disponíveis deverão ser limpos do sistema.