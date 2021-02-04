---
title: Configurar o modo de distribuição do Azure Load Balancer
titleSuffix: Azure Load Balancer
description: Neste artigo, comece Configurando o modo de distribuição para Azure Load Balancer para dar suporte à afinidade de IP de origem.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2021
ms.author: allensu
ms.openlocfilehash: 7f2525b89f03e8bc1a2c3166b46c40b4dbb6ff17
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99562003"
---
# <a name="configure-the-distribution-mode-for-azure-load-balancer"></a>Configurar o modo de distribuição para o Azure Load Balancer

O Azure Load Balancer dá suporte a dois modos de distribuição para distribuir o tráfego para seus aplicativos:

* Com base em hash
* Afinidade do IP de origem

Neste artigo, você aprenderá a configurar o modo de distribuição para seu Azure Load Balancer.


## <a name="configure-distribution-mode"></a>Configurar o modo de distribuição

---

# <a name="azure-portal"></a>[**Portal do Azure**](#tab/azure-portal)

Você pode alterar a configuração do modo de distribuição modificando a regra de balanceamento de carga no Portal.

1. Entre no portal do Azure e localize o grupo de recursos que contém o balanceador de carga que você deseja alterar clicando em **grupos de recursos**.
2. Na tela Visão geral do balanceador de carga, selecione **regras de balanceamento de carga** em **configurações**.
3. Na tela regras de balanceamento de carga, selecione a regra de balanceamento de carga para a qual você deseja alterar o modo de distribuição.
4. Sob a regra, o modo de distribuição é alterado alterando a caixa suspensa **persistência da sessão** . 

As seguintes opções estão disponíveis: 

* **Nenhum (baseado em hash)** – especifica que as solicitações sucessivas do mesmo cliente podem ser tratadas por qualquer máquina virtual.
* **IP do cliente (afinidade de IP de origem 2-tupla)** – especifica que as solicitações sucessivas do mesmo endereço IP do cliente serão tratadas pela mesma máquina virtual.
* **IP do cliente e protocolo (afinidade de IP de origem 3-tupla)** – especifica que solicitações sucessivas do mesmo endereço IP do cliente e combinação de protocolos serão manipuladas pela mesma máquina virtual.

5. Escolha o modo de distribuição e, em seguida, selecione **salvar**.

:::image type="content" source="./media/load-balancer-distribution-mode/session-persistence.png" alt-text="Altere a persistência da sessão na regra do balanceador de carga." border="true":::


# <a name="powershell"></a>[**PowerShell**](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Use o PowerShell para alterar as configurações de distribuição do balanceador de carga em uma regra de balanceamento de carga existente. O seguinte comando atualiza o modo de distribuição: 

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name MyLoadBalancer -ResourceGroupName MyResourceGroupLB
$lb.LoadBalancingRules[0].LoadDistribution = 'sourceIp'
Set-AzLoadBalancer -LoadBalancer $lb
```

Defina o valor do `LoadDistribution` elemento para a quantidade de balanceamento de carga necessária. 

Especifique **sourceIP** para balanceamento de carga de duas tuplas (IP de origem e IP de destino). 

Especifique **sourceIPProtocol** para o balanceamento de carga de três tuplas (IP de origem, IP de destino e tipo de protocolo). 

Especifique o **padrão** para o comportamento padrão do balanceamento de carga de cinco tuplas.

---

## <a name="next-steps"></a>Próximas etapas

* [Visão geral do Azure Load Balancer](load-balancer-overview.md)
* [Introdução à configuração de um balanceador de carga para a Internet](quickstart-load-balancer-standard-public-powershell.md)
* [Definir configurações de tempo limite de TCP ocioso para o balanceador de carga](load-balancer-tcp-idle-timeout.md)