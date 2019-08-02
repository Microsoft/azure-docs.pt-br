---
title: Conectar-se a uma VM do Windows usando o Azure bastiões | Microsoft Docs
description: Neste artigo, saiba como se conectar a uma máquina Virtual Azure executando Windows usando o Azure bastião.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: cherylmc
ms.openlocfilehash: 376b7042a513dd50647dc8f88bf1de70f65bb21c
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478413"
---
# <a name="connect-to-a-windows-virtual-machine-using-azure-bastion-preview"></a>Conectar-se a uma máquina virtual do Windows usando o Azure bastião (visualização)

Este artigo mostra como com simplicidade e RDP para suas VMs do Windows no Azure virtual de rede usando o Azure bastião. É possível se conectar a uma VM diretamente do portal do Azure. Usando o Azure Bastion, as VMs não precisam de um cliente, de um agente nem de software adicional. Para obter mais informações sobre bastiões do Azure, consulte a [visão geral](bastion-overview.md).

> [!IMPORTANT]
> Essa versão prévia pública é fornecida sem um SLA e não deve ser usada para cargas de trabalho de produção. Determinados recursos podem não ter suporte, podem ter restrição ou podem não estar disponíveis em todos os locais do Azure. Veja os [Termos de Uso Adicionais para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter detalhes.
>

## <a name="before-you-begin"></a>Antes de começar

Certifique-se de que você tiver configurado um host bastião do Azure para a rede virtual no qual reside a VM. Para obter mais informações, consulte [criar um host bastião Azure](bastion-create-host-portal.md). Depois que o serviço de bastião é provisionado e implantado em sua rede virtual, você pode usá-lo para se conectar a qualquer VM nessa rede virtual. Nesta visualização, bastiões pressupõe que você está usando o RDP para se conectar a uma VM do Windows e SSH para se conectar às suas VMs do Linux. Para obter informações sobre a conexão a uma VM Linux, consulte [conectar-se a uma VM - Linux](bastion-connect-vm-ssh.md).

Para fazer uma conexão, são necessárias as seguintes funções:

* Função de leitor na máquina virtual
* Função de leitor na placa de interface de rede com endereço IP privado da máquina virtual
* Função de leitor no recurso do Azure Bastion

## <a name="rdp"></a>Conectar-se usando o RDP

1. Use [esse link](https://aka.ms/BastionHost) para abrir a página de portal de visualização de bastiões do Azure. Navegue até a máquina virtual que você deseja se conectar e clique em **Connect**. A VM deve ser uma máquina virtual do Windows ao usar uma conexão de RDP.

    ![Conectar-se de VM](./media/bastion-connect-vm-rdp/connect.png)

1. Depois de clicar em conectar, será exibida uma barra lateral tem três guias – bastiões, SSH e RDP. Se bastiões foi provisionado para a rede virtual, a guia de bastiões estiver ativa por padrão. Se você não o tiver provisionado bastiões para a rede virtual, você pode clicar no link para configurar bastião. Para obter instruções de configuração, consulte [bastiões configurar](bastion-create-host-portal.md). Se você não vir **bastiões** listado, você não abriu o portal de visualização. Abra o portal usando esse [link visualização](https://aka.ms/BastionHost).

    ![Conectar-se de VM](./media/bastion-connect-vm-rdp/bastion.png)

1. Na guia bastiões, o nome de usuário e a senha para sua máquina virtual, em seguida, clique em **Connect**. A conexão de RDP para essa máquina virtual por meio de bastiões serão abertos diretamente no portal do Azure (acima do HTML5) usando a porta 443 e o serviço de bastiões.

    ![Conectar-se de VM](./media/bastion-connect-vm-rdp/443rdp.png)
 
## <a name="next-steps"></a>Próximas etapas

Leia o [bastiões perguntas Frequentes](bastion-faq.md)
