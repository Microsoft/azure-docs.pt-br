---
title: Configurar a alta disponibilidade de portas para o Azure Load Balancer | Microsoft Docs
description: "Saiba como usar as portas de alta disponibilidade para o tráfego interno de balanceamento de carga em todas as portas"
services: load-balancer
documentationcenter: na
author: rdhillon
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/26/2017
ms.author: kumud
ms.openlocfilehash: 7256548b988812c64ca9a9f8a84fec377646635d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-configure-high-availability-ports-for-internal-load-balancer"></a>Como configurar a alta disponibilidade de portas para o Load Balancer Interno

Este artigo fornece um exemplo de implantação de portas de HA (alta disponibilidade) em um Load Balancer Interno. Para configurações específicas de dispositivos virtuais de rede, consulte os sites do provedor correspondente.

>[!NOTE]
> O recurso de Portas de alta disponibilidade está atualmente em versão prévia. Durante a versão prévia, o recurso pode não ter o mesmo nível de disponibilidade e confiabilidade que os recursos que estão na versão de disponibilidade geral. Para obter mais informações, consulte [Termos de Uso Complementares do Microsoft Azure para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A Figura 1 ilustra a configuração a seguir do exemplo de implantação descrito neste artigo:
- Os NVAs são implantados no pool de back-end de um Load Balancer Interno por trás da configuração de portas de HA. 
- A UDR (rota definida pelo usuário) aplicada à sub-rede DMZ roteia todo o tráfego para o <?>, tornando o próximo salto o IP Virtual do Load Balancer Interno. 
- O Load Balancer Interno distribui o tráfego para um dos NVAs ativos de acordo com o algoritmo de balanceamento de carga.
- O NVA processa o tráfego e o encaminha para o destino original na sub-rede de back-end.
- O caminho de retorno também pode usar a mesma rota se uma UDR correspondente é configurada na sub-rede de back-end. 

![exemplo de implantação de portas de HA](./media/load-balancer-configure-ha-ports/haports.png)

Figura 1 – dispositivos de rede Virtual implantados atrás de um Load Balancer Interno com portas de alta disponibilidade 

## <a name="preview-sign-up"></a>Inscrição na versão prévia

Para participar da versão prévia do recurso de portas de HA no SKU do Load Balancer Standard, registre sua assinatura para obter acesso usando o PowerShell ou CLI do Azure 2.0.

- Inscrever-se usando o PowerShell

   ```powershell
   Register-AzureRmProviderFeature -FeatureName AllowILBAllPortsRule -ProviderNamespace Microsoft.Network
    ```

- Inscrever-se usando a CLI do Azure 2.0

    ```cli
  az feature register --name AllowILBAllPortsRule --namespace Microsoft.Network  
    ```

## <a name="configuring-ha-ports"></a>Configurando portas de HA

A configuração das portas de HA envolve a configuração de um Load Balancer Interno, com os NVAs no pool de back-end, uma configuração de investigação de integridade de balanceador de carga correspondente para detectar a integridade do NVA e a regra do Load Balancer com portas de HA. A configuração relacionada geral do Load Balancer é abordada em [Introdução](load-balancer-get-started-ilb-arm-portal.md). Este artigo realça a configuração de portas de HA.

A configuração envolve essencialmente a definição do valor da porta de back-end e da porta de front-end para **0** e o valor de protocolo para **Todos**. Este artigo descreve como configurar portas de alta disponibilidade usando o Portal do Azure, o PowerShell e a CLI do Azure 2.0.

### <a name="configure-ha-ports-load-balancer-rule-with-the-azure-portal"></a>Configurar regra de balanceador de carga de portas de HA com o Portal do Azure

O Portal do Azure inclui a opção **Portas de HA** por meio de uma caixa de seleção para essa configuração. Quando selecionada, a configuração de porta e de protocolo relacionada é populada automaticamente. 

![configuração de portas de HA por meio do Portal do Azure](./media/load-balancer-configure-ha-ports/haports-portal.png)

Figura 2 – configuração de portas de HA por meio do Portal

### <a name="configure-ha-ports-load-balancer-rule-with-powershell"></a>Configurar regra de balanceador de carga de portas de HA com o PowerShell

Use o comando a seguir para criar a regra de Load Balancer de Portas de HA, criando simultaneamente o Load Balancer Interno com o PowerShell:

```powershell
lbrule = New-AzureRmLoadBalancerRuleConfig -Name "HAPortsRule" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol "All" -FrontendPort 0 -BackendPort 0
```

### <a name="configure-ha-ports-load-balancer-rule-with-azure-cli-20"></a>Configurar regra de balanceador de carga de portas de HA com a CLI do Azure 2.0

Na Etapa 4 de [Criando um conjunto de Load Balancer Interno](load-balancer-get-started-ilb-arm-cli.md), use o comando a seguir para criar a regra de Load Balancer de portas de HA.

```azurecli
azure network lb rule create --resource-group contoso-rg --lb-name contoso-ilb --name haportsrule --protocol all --frontend-port 0 --backend-port 0 --frontend-ip-name feilb --backend-address-pool-name beilb
```

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [portas de alta disponibilidade](load-balancer-ha-ports-overview.md)
