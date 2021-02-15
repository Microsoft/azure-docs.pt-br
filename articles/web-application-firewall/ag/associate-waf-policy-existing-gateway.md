---
title: Associar uma política de firewall do aplicativo Web a um gateway de Aplicativo Azure existente
description: Saiba como associar uma política de firewall do aplicativo Web a um gateway de Aplicativo Azure existente.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 10/25/2019
ms.author: victorh
ms.openlocfilehash: 1ed2e0cf8cc8cd841d8779462d62ba4852774a3a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "74083899"
---
# <a name="associate-a-waf-policy-with-an-existing-application-gateway"></a>Associar uma política de WAF a um gateway de aplicativo existente

Você pode usar Azure PowerShell para [criar uma política de WAF](create-waf-policy-ag.md), mas talvez você já tenha um gateway de aplicativo e queira apenas associar uma política de WAF a ele. Neste artigo, você faz exatamente isso; você cria uma política de WAF e a associa a um gateway de aplicativo já existente. 

1. Obtenha o gateway de aplicativo e a política de firewall. Se você não tiver uma política de firewall existente, consulte a etapa 2. 

   ```azurepowershell-interactive
      Connect-AzAccount
      Select-AzSubscription -Subscription "<sub id>"`

      #Get Application Gateway and existing policy object or create new`
      $gw = Get-AzApplicationGateway -Name <Waf v2> -ResourceGroupName <RG name>`
      $policy = Get-AzApplicationGatewayFirewallPolicy -Name <policy name> -ResourceGroupName <RG name>`
   ```

2. Adicional Criar uma política de firewall.

   ```azurepowershell-interactive
      New-AzApplicationGatewayFirewallPolicy -Name <policy name> -ResourceGroupName <RG name>'
      $policy = Get-AzApplicationGatewayFirewallPolicy -Name <policy name> -ResourceGroupName <RG name>`
   ```
   > [!NOTE]
   > Se você estiver criando essa política de WAF para fazer a transição de uma configuração de WAF para uma política de WAF, a política precisará ser uma cópia exata de sua configuração antiga. Isso significa que cada exclusão, regra personalizada, grupo de regras desabilitado, etc., precisa ser exatamente o mesmo que está na configuração de WAF.
3. Adicional Você pode configurar a política de WAF para atender às suas necessidades. Isso inclui regras personalizadas, desabilitando regras/grupos de regra, exclusões, definindo limites de carregamento de arquivo, etc. Se você ignorar essa etapa, todos os padrões serão selecionados. 
   
4. Salve a política e anexe-a ao seu gateway de aplicativo. 
   
   ```azurepowershell-interactive
      #Save the policy itself
      Set-AzApplicationGatewayFirewallPolicy -InputObject $policy`
   
      #Attach the policy to an Application Gateway
      $gw.FirewallPolicy = $policy`
   
      #Save the Application Gateway
      Set-AzApplicationGateway -ApplicationGateway $gw`
   ```

## <a name="next-steps"></a>Próximas etapas
[Saiba mais sobre regras personalizadas.](configure-waf-custom-rules.md)
