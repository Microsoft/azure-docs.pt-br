---
title: Abrir portas para uma VM usando o Portal do Azure
description: Saiba como abrir uma porta/criar um ponto de extremidade para sua VM usando o portal do Azure
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 05/27/2020
ms.author: cynthn
ms.openlocfilehash: 28b3ee98d44e1567d9e25cf9237015396975ab01
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102550443"
---
# <a name="how-to-open-ports-to-a-virtual-machine-with-the-azure-portal"></a>Como abrir portas para uma máquina virtual com o Portal do Azure
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]


## <a name="sign-in-to-azure"></a>Entrar no Azure
Entre no Portal do Azure em https://portal.azure.com.

## <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

1. Pesquise e selecione o grupo de recursos para a VM, escolha **Adicionar** e, em seguida, pesquise e selecione **Grupo de segurança de rede**.

1. Selecione **Criar**.

    A janela **Criar grupo de segurança de rede** é aberta.

    ![Criar um grupo de segurança de rede](./media/nsg-quickstart-portal/create-nsg.png)

1. Insira um nome para o grupo de segurança de rede. 

1. Selecione ou crie um grupo de recursos e, então, selecione um local.

1. Selecione **Criar** para criar o grupo de segurança de rede.

## <a name="create-an-inbound-security-rule"></a>Criar uma regra de segurança de entrada

1. Selecione o novo Grupo de Segurança de Rede. 

1. Selecione **Regras de segurança de entrada** no menu à esquerda e, então, selecione **Adicionar**.

    ![Alternar para a página Avançado](./media/nsg-quickstart-portal/advanced.png)

1. Na página **Adicionar uma regra de segurança de entrada**, alterne para **Avançado** de **Básico** na parte superior da página. 

1. Escolha um **Serviço** comum no menu suspenso, como **HTTP**. Você também poderá selecionar **Personalizado** caso queira fornecer uma porta específica a ser usada. 

1. Como alternativa, altere a **Prioridade** ou o **Nome**. A prioridade afeta a ordem na qual as regras são aplicadas: quanto menor for o valor numérico, mais cedo a regra será aplicada.

1. Selecione **Adicionar** para criar a regra.

## <a name="associate-your-network-security-group-with-a-subnet"></a>Associe seu grupo de segurança de rede à sua sub-rede

A etapa final é associar o grupo de segurança de rede com uma sub-rede ou uma interface de rede específica. Neste exemplo, associaremos o grupo de segurança de rede a uma sub-rede. 

1. Selecione **Sub-redes** no menu esquerdo e selecione **Associar**.

1. Selecione sua rede virtual e selecione a sub-rede apropriada.

    ![Associar um Grupo de Segurança de Rede à rede virtual](./media/nsg-quickstart-portal/select-vnet-subnet.png)

1. Quando terminar, selecione **OK**.

## <a name="additional-information"></a>Informações adicionais

Você também pode [executar as etapas neste artigo usando o Azure PowerShell](nsg-quickstart-powershell.md).

Os comandos descritos neste artigo permitem que você rapidamente obtenha tráfego para sua VM. Os Grupos de Segurança de Rede fornecem muitos recursos excelentes e granularidade para controlar o acesso aos recursos. Para obter mais informações, consulte [Filtrar o tráfego de rede com grupos de segurança de rede](../../virtual-network/tutorial-filter-network-traffic.md).

Para aplicativos Web altamente disponíveis, considere colocar suas VMs atrás do Azure Load Balancer. O balanceador de carga distribui o tráfego para VMs, com um Grupo de Segurança de rede que fornece filtragem. Para obter mais informações, veja [Balancear carga de máquinas virtuais do Windows no Azure para criar um aplicativo altamente disponível](tutorial-load-balancer.md).

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você criou um grupo de segurança de rede, criou uma regra de entrada que permite o tráfego HTTP na porta 80 e, então, associou essa regra a uma sub-rede. 

Você pode encontrar informações sobre a criação de ambientes mais detalhados nos seguintes artigos:
- [Visão geral do Azure Resource Manager](../../azure-resource-manager/management/overview.md)
- [Grupos de segurança](../../virtual-network/network-security-groups-overview.md)
