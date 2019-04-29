---
title: Conectar-se à sua máquina virtual do Microsoft Azure| Microsoft Docs
description: Explica como conectar-se à nova máquina virtual criada no Azure.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/19/2018
ms.author: pbutlerm
ms.openlocfilehash: fd68846b9144c3efcc71dec369d64119427758a3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60744461"
---
# <a name="connect-to-your-azure-based-virtual-machine"></a>Conectar-se à máquina virtual baseada no Azure

Este artigo explica como se conectar e entrar com as máquinas virtuais (VMs) que você criou no Azure.  Depois que você se conectou com êxito, você pode trabalhar com a VM como se tivesse efetuado localmente seu servidor de host. 

## <a name="connect-to-a-windows-based-vm"></a>Conectar em uma VM baseada em Windows

Você usará o cliente de área de trabalho remota para se conectar à VM com base em Windows hospedado no Azure.  A maioria das versões do Windows contêm suporte para o protocolo de área de trabalho remota (RDP).  Para outras máquinas, você pode encontrar mais informações sobre os clientes nos [clientes de Área de Trabalho remota](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients).  

O artigo a seguir fornece detalhes sobre como usar o suporte interno do Windows RDP para se conectar à sua VM: [Como se conectar e fazer logon em uma máquina virtual do Azure executando Windows](../../../virtual-machines/windows/connect-logon.md).  

>[!TIP]
> Você pode receber avisos de segurança durante o processo, por exemplo, que o arquivo. rdp é proveniente de um editor desconhecido ou que suas credenciais de usuário não podem ser verificados.  É seguro ignorar esses avisos.


## <a name="connect-to-a-linux-based-vm"></a>Conectar-se a uma máquina virtual baseada em Linux

Para conectar-se a VM com base em Linux, é necessário um cliente do protocolo (SSH) de um shell seguro.  Esta discussão usará a versão gratuita do terminal [PuTTY](https://www.ssh.com/ssh/putty/) SHH.

1. Em **Máquinas Virtuais** folha do [portal do Azure](https://ms.portal.azure.com), selecione a VM que você deseja se conectar.  
2. **Iniciar** VM se ainda não estiver sendo executada.
3. Clique no nome da VM para abrir sua página de **Visão geral**.
4. Observe o endereço IP público e o nome DNS da sua VM.  (se esses valores não são definidos e, em seguida, você deve [Criar uma interface de rede](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#create-a-network-interface)

   ![Configurações de visão geral da VM](./media/publishvm_019.png)
 
5. Abra o aplicativo PuTTY.  
6. Na caixa de diálogo de configuração do PuTTY, insira o endereço IP ou nome DNS da sua VM. 

   ![Configurações de terminal do PuTTY](./media/publishvm_020.png)
 
7. Clique em **Abrir** para abrir um terminal do PuTTY.  
8. Quando você for solicitado, insira o nome da conta e a senha da sua conta de VM do Linux. 

   Se você estiver tendo problemas de conexão, consulte a documentação do cliente SSH, por exemplo [capítulo 10: Mensagens de erro comuns](https://www.ssh.com/ssh/putty/putty-manuals/0.68/Chapter10.html#errors).

Para obter mais informações, incluindo como adicionar uma área de trabalho a uma VM Linux provisionada, consulte [Instalar e configurar a área de trabalho remota para se conectar a uma VM do Linux no Azure](../../../virtual-machines/linux/use-remote-desktop.md).


## <a name="stop-unused-vms"></a>Parar VMs não utilizadas
As cobranças do Azure para hospedagem de VM quando uma máquina virtual está em execução *ou ociosa*.  Portanto, é uma prática recomendada para parar as VMs que não estão sendo usadas atualmente.  Por exemplo, VMs de teste, backup, ou obsoletas são candidatas para o desligamento. Para desligar uma VM, execute as etapas a seguir:

1. Sobre a folha de **Máquinas virtuais**, selecione a VM que você deseja interromper. 
2. Na barra de ferramentas na parte superior da página, clique no botão **Parar**.

   ![Parar uma VM](./media/publishvm_018.png)

Azure interrompe rapidamente a VM em um processo chamado *desalocação*, que não só desliga o sistema operacional na máquina virtual, mas também libera os recursos de hardware e rede provisionados anteriormente para ele.

Se você deseja reativar posteriormente uma VM parada, selecione-o e clique no botão **Iniciar**.


## <a name="next-steps"></a>Próximas etapas

Depois que você estiver conectado remotamente, você estará pronto para [Configurar sua VM](./cpp-configure-vm.md).
