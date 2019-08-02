---
title: Abrir portas para uma VM usando o Portal do Azure | Microsoft Azure
description: Saiba como abrir uma porta/criar um ponto de extremidade para sua VM do Windows usando o modelo de implantação do Resource Manager no Portal do Azure
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
ms.assetid: f7cf0319-5ee7-435e-8f94-c484bf5ee6f1
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/27/2018
ms.author: cynthn
ms.openlocfilehash: cf85bfd8be146cb599ced61eaa2126ee9e12538b
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723060"
---
# <a name="how-to-open-ports-to-a-virtual-machine-with-the-azure-portal"></a>Como abrir portas para uma máquina virtual com o Portal do Azure
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]


## <a name="sign-in-to-azure"></a>Entrar no Azure
Entre no Portal do Azure em https://portal.azure.com.

## <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

1. Pesquise e selecione o grupo de recursos para a VM, escolha **Adicionar** e, em seguida, pesquise e selecione **Grupo de segurança de rede**.

2. Selecione **Criar**.

    A janela **Criar grupo de segurança de rede** é aberta.

    ![Criar um grupo de segurança de rede](./media/nsg-quickstart-portal/create-nsg.png)

2. Insira um nome para o grupo de segurança de rede. 

3. Selecione ou crie um grupo de recursos e, então, selecione um local.

4. Selecione **Criar** para criar o grupo de segurança de rede.

## <a name="create-an-inbound-security-rule"></a>Criar uma regra de segurança de entrada

1. Selecione o novo Grupo de Segurança de Rede. 

2. Selecione **Regras de segurança de entrada** e, então, selecione **Adicionar**.

    ![Adicionar regra de entrada](./media/nsg-quickstart-portal/add-inbound-rule.png)

3. Selecione **Avançado**. 

4. Escolha um **Serviço** comum no menu suspenso, como **HTTP**. Você também poderá selecionar **Personalizado** caso queira fornecer uma porta específica a ser usada. 

5. Como alternativa, altere a **Prioridade** ou o **Nome**. A prioridade afeta a ordem na qual as regras são aplicadas: quanto menor for o valor numérico, mais cedo a regra será aplicada.

6. Selecione **Adicionar** para criar a regra.

## <a name="associate-your-network-security-group-with-a-subnet"></a>Associe seu grupo de segurança de rede à sua sub-rede

A etapa final é associar o grupo de segurança de rede com uma sub-rede ou uma interface de rede específica. Neste exemplo, associaremos o grupo de segurança de rede a uma sub-rede. 

1. Selecione **Sub-redes** e, em seguida, selecione **Associar**.

    ![Associar um Grupo de Segurança de Rede a uma sub-rede](./media/nsg-quickstart-portal/associate-subnet.png)

2. Selecione sua rede virtual e selecione a sub-rede apropriada.

    ![Associar um Grupo de Segurança de Rede à rede virtual](./media/nsg-quickstart-portal/select-vnet-subnet.png)

    Todas as VMs que você conectar a essa sub-rede estarão acessíveis na porta 80.

## <a name="additional-information"></a>Informações adicionais

Você também pode [executar as etapas neste artigo usando o Azure PowerShell](nsg-quickstart-powershell.md).

Os comandos descritos neste artigo permitem que você rapidamente obtenha tráfego para sua VM. Os Grupos de Segurança de Rede fornecem muitos recursos excelentes e granularidade para controlar o acesso aos recursos. Para obter mais informações, consulte [Filtrar o tráfego de rede com grupos de segurança de rede](../../virtual-network/tutorial-filter-network-traffic.md).

Para aplicativos Web altamente disponíveis, considere colocar suas VMs atrás do Azure Load Balancer. O balanceador de carga distribui o tráfego para VMs, com um Grupo de Segurança de rede que fornece filtragem. Para obter mais informações, veja [Balancear carga de máquinas virtuais do Windows no Azure para criar um aplicativo altamente disponível](tutorial-load-balancer.md).

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você criou um grupo de segurança de rede, criou uma regra de entrada que permite o tráfego HTTP na porta 80 e, então, associou essa regra a uma sub-rede. 

Você pode encontrar informações sobre a criação de ambientes mais detalhados nos seguintes artigos:
- [Visão geral do Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md)
- [Grupos de segurança](../../virtual-network/security-overview.md)