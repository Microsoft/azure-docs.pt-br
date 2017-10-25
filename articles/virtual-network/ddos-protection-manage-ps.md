---
title: "Gerenciar a Proteção contra DDoS do Azure Standard usando o Azure PowerShell | Microsoft Docs"
description: "Saiba como gerenciar a Proteção contra DDoS do Azure Standard usando o Azure PowerShell."
services: virtual-network
documentationcenter: na
author: kumudD
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2017
ms.author: kumud
ms.openlocfilehash: a1a3688d4ff215d05d2f78cdfa7d402e3fc20be2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-azure-ddos-protection-standard-using-azure-powershell"></a>Gerenciar a Proteção contra DDoS do Azure Standard usando o Azure PowerShell

>[!IMPORTANT]
>A Proteção contra DDoS do Azure Standard (Proteção contra DDoS) está atualmente em versão prévia. O suporte à Proteção contra DDoS só é dado por um número limitado de recursos do Azure e em um número seleto de regiões. Você precisa [registrar-se para o serviço](http://aka.ms/ddosprotection) durante a versão prévia limitada para habilitar a Proteção contra DDoS para a sua assinatura. Você é contatado pela equipe de DDoS do Azure após o registro para lhe orientar durante o processo de habilitação. A Proteção contra DDoS está disponível nas regiões Leste dos EUA, Oeste dos EUA e Centro-oeste dos EUA. Durante a versão prévia, você não será cobrado pelo uso do serviço.

Este artigo lhe mostra como usar o Azure PowerShell para habilitar a Proteção contra DDoS, como desabilitá-la e como usar a telemetria para mitigar um ataque.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. Se você precisa instalar ou atualizar o Azure PowerShell, confira [Instalar o módulo do Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Faça logon na sua assinatura do Azure com o comando `Login-AzureRmAccount` e siga as instruções na tela.

```powershell
Login-AzureRmAccount
```

## <a name="enable-ddos-protection"></a>Habilitar Proteção contra DDoS

### <a name="create-a-new-virtual-network-and-enable-ddos-protection"></a>Criar uma nova rede virtual e habilitar a Proteção contra DDoS

Para criar uma rede virtual com a Proteção contra DDoS habilitada, execute o exemplo a seguir:

```powershell
New-AzureRmResourceGroup -Name <ResourceGroupName> -Location westcentralus 
$frontendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name <frontendSubnet> -AddressPrefix "10.0.1.0/24" 
$backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name <backendSubnet> -AddressPrefix "10.0.2.0/24" 
New-AzureRmVirtualNetwork -Name <MyVirtualNetwork> -ResourceGroupName <ResourceGroupName>  -Location westcentralus  -AddressPrefix "10.0.0.0/16" -Subnet $frontendSubnet,$backendSubnet -DnsServer 10.0.1.5,10.0.1.6 -EnableDDoSProtection
```

Esse exemplo cria uma rede virtual com duas sub-redes e dois servidores DNS. O efeito de especificar os servidores DNS na rede virtual é que as NICs/VMs que são implantadas na rede virtual herdam esses servidores DNS como padrões. A Proteção contra DDoS é habilitada para todos os recursos protegidos na rede virtual.

### <a name="enable-ddos-protection-on-an-existing-virtual-network"></a>Habilitar a Proteção contra DDoS em uma rede virtual existente

Para habilitar a Proteção contra DDoS em uma rede virtual existente, execute o exemplo a seguir:

```powershell
$vnetProps = (Get-AzureRmResource -ResourceType "Microsoft.Network/virtualNetworks" -ResourceGroup <ResourceGroupName> -ResourceName <ResourceName>).Properties
$vnetProps.enableDdosProtection = $true
Set-AzureRmResource -PropertyObject $vnetProps -ResourceGroupName "ResourceGroupName" -ResourceName "ResourceName" -ResourceType Microsoft.Network/virtualNetworks
```

## <a name="disable-ddos-protection-on-a-virtual-network"></a>Desabilitar a Proteção contra DDoS em uma rede virtual

Para desabilitar a Proteção contra DDoS em uma rede virtual existente, execute o exemplo a seguir:

```powershell
$vnetProps = (Get-AzureRmResource -ResourceType "Microsoft.Network/virtualNetworks" -ResourceGroup <ResourceGroupName> -ResourceName <ResourceName>).Properties
$vnetProps.enableDdosProtection = $false
Set-AzureRmResource -PropertyObject $vnetProps -ResourceGroupName <RessourceGroupName> -ResourceName <ResourceName> -ResourceType "Microsoft.Network/virtualNetworks"
```

## <a name="review-the-ddos-protection-status-of-virtual-networks"></a>Examinar o status da Proteção contra DDoS de redes virtuais 

```powershell
$vnetProps = (Get-AzureRmResource -ResourceType "Microsoft.Network/virtualNetworks" -ResourceGroup <ResourceGroupName> -ResourceName <ResourceName>).Properties
$vnetProps
```

## <a name="use-ddos-protection-telemetry"></a>Usar a telemetria da Proteção contra DDoS

A telemetria de um ataque é fornecida por meio do Azure Monitor em tempo real. A telemetria está disponível somente durante o tempo pelo qual um endereço IP público está sob mitigação. Você não verá telemetria antes nem depois de um ataque ser mitigado.

### <a name="configure-alerts-on-ddos-protection-metrics"></a>Configurar alertas em métricas de Proteção contra DDoS

Aproveitando a configuração de alerta do Azure Monitor, você pode selecionar qualquer uma das métricas de Proteção contra DDoS disponíveis para alertar quando há uma mitigação ativa durante um ataque.

#### <a name="configure-email-alert-rules-via-azure-powershell"></a>Configurar regras de alerta de email por meio do Azure PowerShell

1. Obter uma lista das inscrições disponíveis. Verifique se você está trabalhando com a assinatura correta. Se não estiver, defina-a para a correta usando a saída de Get-AzureRmSubscription. 

    ```powershell
    Get-AzureRmSubscription 
    Get-AzureRmContext 
    Set-AzureRmContext -SubscriptionId <subscriptionid>
    ```

2. Para listar as regras existentes em um grupo de recursos, use o seguinte comando: 

    ```powershell
    Get-AzureRmAlertRule -ResourceGroup <myresourcegroup> -DetailedOutput
    ```

3. Para criar uma regra, primeiro precisa ter várias informações importantes. 

    - A ID de Recurso do recurso para o qual você quer definir um alerta.
    - As definições de métricas disponíveis para esse recurso. Uma maneira de obter a ID de recurso é usar o portal do Azure. Supondo que o recurso já foi criado, selecione-o no Portal do Azure. Na próxima página, selecione *Propriedades* na seção *Configurações*. A **ID DE RECURSO** é um campo na página seguinte. Outra maneira é usar o [Azure Resource Manager](https://resources.azure.com/). Um exemplo de ID de Recurso para um IP público é: `/subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Network/publicIPAddresses/mypublicip`

    O exemplo a seguir configura um alerta em um IP público associado a uma rede virtual. A métrica para a qual criar um alerta é **Sob ataque de DDoS ou não**. Esse é um valor booliano 1 ou 0. Um **1** significa que você está sob ataque. Um **0** significa que você não está sob ataque. O alerta é criado quando você está sob o ataque pelos últimos 5 minutos.

    Para criar um webhook ou enviar email quando um alerta é criado, primeiro crie o email e/ou os webhooks. Em seguida, crie imediatamente a regra com a marca -Actions,e conforme mostra o exemplo a seguir. Você não pode associar webhook ou emails a regras já criadas usando o PowerShell.

    ```powershell
    $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com 
    Add-AzureRmMetricAlertRule -Name <myMetricRuleWithEmail> -Location "West Central US" -ResourceGroup <myresourcegroup> -TargetResourceId /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroup/providers/Microsoft.Network/publicIPAddresses/mypublicip -MetricName "IfUnderDDoSAttack" -Operator GreaterThan -Threshold 0 -WindowSize 00:05:00 -TimeAggregationOperator Total -Actions $actionEmail-Description "Under DDoS Attack" 
    ```

4. Verificar se os alertas foram criados corretamente examinando regras individuais.

    ```powershell
    Get-AzureRmAlertRule -Name myMetricRuleWithEmail -ResourceGroup myresourcegroup -DetailedOutput 
    Get-AzureRmAlertRule -Name myLogAlertRule -ResourceGroup myresourcegroup -DetailedOutput
    ```

Você também pode aprender mais sobre [configuração de webhooks](../monitoring-and-diagnostics/insights-webhooks-alerts.md) e [aplicativos lógicos](../logic-apps/logic-apps-what-are-logic-apps.md) para criação de alertas.

## <a name="configure-logging-on-ddos-protection-metrics"></a>Configurar o registro em log para métricas de Proteção contra DDoS

Consulte as [amostras de início rápido do PowerShell](../monitoring-and-diagnostics/insights-powershell-samples.md) para ajudá-lo a acessar e configurar o log de diagnósticos do Azure por meio do PowerShell.

## <a name="next-steps"></a>Próximas etapas

- [Saiba mais sobre os Logs de Diagnóstico do Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)
- [Analisar logs do Armazenamento do Azure com o Log Analytics](../log-analytics/log-analytics-azure-storage.md)
- [Introdução aos Hubs de Evento](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)