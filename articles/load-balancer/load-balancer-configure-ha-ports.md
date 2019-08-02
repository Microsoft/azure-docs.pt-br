---
title: Configure a alta disponibilidade de portas para o Azure Load Balancer
titlesuffix: Azure Load Balancer
description: Saiba como usar as portas de alta disponibilidade para o tráfego interno de balanceamento de carga em todas as portas
services: load-balancer
documentationcenter: na
author: rdhillon
manager: narayan
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2018
ms.author: allensu
ms.openlocfilehash: c0cf1eb62c8e01988c9014478ff72816e45ea64c
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68275627"
---
# <a name="configure-high-availability-ports-for-an-internal-load-balancer"></a>Configurar a alta disponibilidade de portas para o balanceador de carga interno

Este artigo fornece um exemplo de implantação de Portas de Alta Disponibilidade em um Load Balancer Interno. Para obter mais informações sobre configurações específicas de soluções de virtualização de rede (NVAs), consulte os sites do provedor correspondente.

>[!NOTE]
>O Azure Load Balancer é compatível com dois tipos diferentes: Básico e Standard. Este artigo discute o Load Balancer Standard. Para obter mais informações sobre o Load Balancer Basic, veja [Visão geral do Load Balancer](load-balancer-overview.md).

A ilustração mostra a configuração a seguir do exemplo de implantação descrito neste artigo:

- Os NVAs são implantados no pool de back-end de um Load Balancer Interno por trás da configuração das Portas de Alta Disponibilidade. 
- A rota definida pelo usuário (UDR) aplicada à sub-rede DMZ roteia todo o tráfego para os NVAs, tornando o próximo salto como o IP Virtual do Load Balancer Interno. 
- O Load Balancer Interno distribui o tráfego para um dos NVAs ativos de acordo com o algoritmo de balanceamento de carga.
- O NVA processa o tráfego e o encaminha para o destino original na sub-rede de back-end.
- O caminho de retorno pode usar a mesma rota se uma UDR correspondente for configurada na sub-rede de back-end. 

![Implantação de exemplo de Portas de Alta Disponibilidade](./media/load-balancer-configure-ha-ports/haports.png)

## <a name="configure-high-availability-ports"></a>Configurar as Portas de Alta Disponibilidade

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para configurar as Portas de Alta Disponibilidade, defina um balanceador de carga interno com os NVAs no pool de back-end. Defina uma configuração de sonda de integridade do balanceador de carga correspondente para detectar a integridade NVA e a regra de balanceador de carga com Portas de Alta Disponibilidade. As configurações gerais relacionadas ao Load Balancer são abordadas em [Introdução](load-balancer-get-started-ilb-arm-portal.md). Este artigo destaca a configuração das Portas de Alta Disponibilidade.

A configuração envolve essencialmente a definição do valor da porta de back-end e da porta de front-end em **0**. Defina o valor de protocolo para **Todos**. Este artigo descreve como configurar as Portas de Alta Disponibilidade usando o portal do Azure, o PowerShell e a CLI do Azure.

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-the-azure-portal"></a>Configurar a regra de balanceador de carga de Portas de Alta Disponibilidade com o portal do Azure

Para configurar as Portas de Alta Disponibilidade usando o portal do Azure, selecione a caixa de seleção **Portas HA**. Quando selecionada, a configuração de porta e de protocolo relacionada é populada automaticamente. 

![Configuração das Portas de Alta Disponibilidade pelo portal do Azure](./media/load-balancer-configure-ha-ports/haports-portal.png)

### <a name="configure-a-high-availability-ports-load-balancing-rule-via-the-resource-manager-template"></a>Configurar uma regra de balanceamento de carga das Portas de Alta Disponibilidade pelo modelo do Gerenciador de Recursos

É possível configurar as Portas de Alta Disponibilidade usando a versão da API 2017-08-01 para Microsoft.Network/loadBalancers no recurso do Load Balancer. O snippet de código JSON a seguir ilustra as alterações na configuração do Load Balancer para Portas de Alta Disponibilidade por meio da API REST:

```json
    {
        "apiVersion": "2017-08-01",
        "type": "Microsoft.Network/loadBalancers",
        ...
        "sku":
        {
            "name": "Standard"
        },
        ...
        "properties": {
            "frontendIpConfigurations": [...],
            "backendAddressPools": [...],
            "probes": [...],
            "loadBalancingRules": [
             {
                "properties": {
                    ...
                    "protocol": "All",
                    "frontendPort": 0,
                    "backendPort": 0
                }
             }
            ],
       ...
       }
    }
```

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-powershell"></a>Configurar a regra de balanceador de carga de Portas de Alta Disponibilidade com o PowerShell

Use o comando a seguir para criar a regra de balanceador de carga de Portas de Alta Disponibilidade criando simultaneamente o balanceador de carga interno com o PowerShell:

```powershell
lbrule = New-AzLoadBalancerRuleConfig -Name "HAPortsRule" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol "All" -FrontendPort 0 -BackendPort 0
```

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-azure-cli"></a>Configurar a regra de balanceador de carga de Portas de Alta Disponibilidade com a CLI do Azure

Na Etapa 4 de [Criar um conjunto de Load Balancer Interno](load-balancer-get-started-ilb-arm-cli.md), use o comando a seguir para criar a regra de balanceador de carga de Portas de Alta Disponibilidade:

```azurecli
azure network lb rule create --resource-group contoso-rg --lb-name contoso-ilb --name haportsrule --protocol all --frontend-port 0 --backend-port 0 --frontend-ip-name feilb --backend-address-pool-name beilb
```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as [Portas de Alta Disponibilidade](load-balancer-ha-ports-overview.md).