---
title: Amostra de script do Azure PowerShell – Criar regras personalizadas do WAF
description: Amostra de script do Azure PowerShell – Criar regras personalizadas do firewall do aplicativo Web
author: vhorne
ms.service: application-gateway
ms.topic: sample
ms.date: 6/7/2019
ms.author: victorh
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 312f052671036d8153dd19fcf4e559e825fd8464
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93396984"
---
# <a name="create-web-application-firewall-waf-custom-rules-with-azure-powershell"></a>Criar funções personalizadas do WAF (Firewall do Aplicativo Web) com o Azure PowerShell

Esse script cria um firewall do aplicativo Web do Gateway de Aplicativo que usa regras personalizadas. A regra personalizada bloqueia o tráfego se o cabeçalho da solicitação contém o *evilbot* User-Agent.

## <a name="prerequisites"></a>Prerequisites

### <a name="azure-powershell-module"></a>Módulo do Azure PowerShell

Se você optar por instalar e usar o Azure PowerShell localmente, esse script exigirá o módulo do Azure PowerShell versão 2.1.0 ou posterior.

1. Para saber qual é a versão, execute `Get-Module -ListAvailable Az`. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps).
2. Para criar uma conexão com o Azure execute `Connect-AzAccount`.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemplo de script

[!code-powershell[main](../../../powershell_scripts/application-gateway/waf-rules/waf-custom-rules.ps1 "Custom WAF rules")]

## <a name="clean-up-deployment"></a>Limpar a implantação

Execute o comando a seguir para remover o grupo de recurso, o gateway de aplicativo, e todos os recursos relacionados.

```powershell
Remove-AzResourceGroup -Name CustomRulesTest
```

## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os seguintes comandos para criar a implantação. Cada item em que a tabela contém links para a documentação específica do comando.

| Comando | Observações |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Cria as configurações de sub-rede. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Cria a rede virtual usando com as configurações de sub-rede. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Cria o endereço IP público para o gateway do aplicativo. |
| [New-AzApplicationGatewayIPConfiguration](/powershell/module/az.network/new-azapplicationgatewayipconfiguration) | Cria a configuração que associa uma sub-rede com o application gateway. |
| [New-AzApplicationGatewayFrontendIPConfig](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig) | Cria a configuração que associa um endereço IP público com o gateway de aplicativo. |
| [New-AzApplicationGatewayFrontendPort](/powershell/module/az.network/new-azapplicationgatewayfrontendport) | Atribui uma porta a ser usada para acessar o gateway de aplicativo. |
| [New-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool) | Cria um pool de back-end para o gateway de aplicativo. |
| [New-AzApplicationGatewayBackendHttpSettings](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsetting) | Define as configurações para um pool de back-end. |
| [New-AzApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener) | Cria um ouvinte. |
| [New-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule) | Cria uma regra de roteamento. |
| [New-AzApplicationGatewaySku](/powershell/module/az.network/new-azapplicationgatewaysku) | Especifique a camada e a capacidade de um gateway de aplicativo. |
| [New-AzApplicationGateway](/powershell/module/az.network/new-azapplicationgateway) | Criar um gateway de aplicativo. |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Remove um grupo de recursos e todos os recursos contidos nele. |
|[New-AzApplicationGatewayAutoscaleConfiguration](/powershell/module/az.network/New-AzApplicationGatewayAutoscaleConfiguration)|Cria uma configuração de dimensionamento automático para o Gateway de Aplicativo.|
|[New-AzApplicationGatewayFirewallMatchVariable](/powershell/module/az.network/New-AzApplicationGatewayFirewallMatchVariable)|Cria uma variável de correspondência para a condição do firewall.|
|[New-AzApplicationGatewayFirewallCondition](/powershell/module/az.network/New-AzApplicationGatewayFirewallCondition)|Cria uma condição de correspondência para a regra personalizada.|
|[New-AzApplicationGatewayFirewallCustomRule](/powershell/module/az.network/New-AzApplicationGatewayFirewallCustomRule)|Cria uma regra personalizada para a política de firewall do gateway de aplicativo.|
|[New-AzApplicationGatewayFirewallPolicy](/powershell/module/az.network/New-AzApplicationGatewayFirewallPolicy)|Cria uma política de firewall do gateway de aplicativo.|
|[New-AzApplicationGatewayWebApplicationFirewallConfiguration](/powershell/module/az.network/New-AzApplicationGatewayWebApplicationFirewallConfiguration)|Cria uma configuração do WAF para um gateway de aplicativo.|

## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações sobre as regras personalizadas do WAF, confira [Regras personalizadas para o firewall do aplicativo Web](../../web-application-firewall/ag/custom-waf-rules-overview.md)
- Para obter mais informações sobre o módulo do Azure PowerShell, confira [Documentação do Azure PowerShell](/powershell/azure/).
- Exemplos de script PowerShell de máquinas virtuais adicionais podem ser encontrados na [documentação de Gateway de Aplicativo do Azure](../powershell-samples.md).