---
title: Tutorial – Configurar a política do WAF de filtragem geográfica – Azure Front Door
description: Neste tutorial, você aprenderá a criar uma política do WAF de filtragem geográfica e associá-la ao host de front-end existente do Front Door.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/14/2020
ms.author: duau
ms.openlocfilehash: f9b5869f7dd472c50ffb2c1c0dce765200f53882
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91324018"
---
# <a name="tutorial-how-to-set-up-a-geo-filtering-waf-policy-for-your-front-door"></a>Tutorial: Como configurar uma política de filtragem geográfica WAF para o Front Door
Este tutorial mostra como usar o Azure PowerShell para criar uma política de filtragem geográfica de exemplo e associar a política a seu host de front-end Front Door existente. Essa política de filtragem geográfica de exemplo bloqueará as solicitações de todos os outros países/regiões, exceto dos Estados Unidos.

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> - Definir uma condição de correspondência de filtragem geográfica.
> - Adicionar uma condição de correspondência de filtragem geográfica a uma regra.
> - Adicionar regras a uma política.
> - Vincular uma política de WAF a um host de front-end do Front Door.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos
* Antes de configurar uma política de filtragem geográfica, configure o ambiente do PowerShell e crie um perfil de Front Door.
* Crie um perfil do Front Door seguindo as instruções descritas em [Início Rápido: Crie um perfil de Front Door](quickstart-create-front-door.md).

## <a name="define-geo-filtering-match-condition"></a>Definir condição de correspondência da filtragem geográfica

Crie uma condição de correspondência de exemplo que seleciona as solicitações não provenientes de "US" usando [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) nos parâmetros, ao criar uma condição de correspondência. Os códigos de duas letras do país/região para o mapeamento de países/regiões são fornecidos [aqui](front-door-geo-filtering.md).

```azurepowershell-interactive
$nonUSGeoMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable RemoteAddr `
-OperatorProperty GeoMatch `
-NegateCondition $true `
-MatchValue "US"
```
## <a name="add-geo-filtering-match-condition-to-a-rule-with-action-and-priority"></a>Adicionar condição de correspondência de filtragem geográfica a uma regra com Ação e Prioridade

Crie um objeto CustomRule `nonUSBlockRule` baseado na condição de correspondência, uma Ação e uma Prioridade, usando [New-AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject).  Uma CustomRule pode ter várias MatchCondition.  Neste exemplo, a Ação está definida como Bloquear e a Prioridade como 1, a prioridade mais alta.

```
$nonUSBlockRule = New-AzFrontDoorWafCustomRuleObject `
-Name "geoFilterRule" `
-RuleType MatchRule `
-MatchCondition $nonUSGeoMatchCondition `
-Action Block `
-Priority 1
```
## <a name="add-rules-to-a-policy"></a>Adicionar regras a uma política
Encontre o nome do grupo de recursos que contém o perfil de Front Door usando `Get-AzResourceGroup`. Em seguida, crie um objeto de política `geoPolicy` contendo `nonUSBlockRule` usando [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) no grupo de recursos especificado que contém o perfil de Front Door. Você deve fornecer um nome exclusivo para a política de filtragem geográfica. 

O exemplo abaixo usará o nome do Grupo de Recursos *FrontDoorQS_rg0* supondo que você criou um perfil do Front Door usando as instruções fornecidas no [Início Rápido: Criar um artigo de Front Door](quickstart-create-front-door.md). No exemplo abaixo, substitua o nome da política *geoPolicyAllowUSOnly* por um nome de política exclusivo.

```
$geoPolicy = New-AzFrontDoorWafPolicy `
-Name "geoPolicyAllowUSOnly" `
-resourceGroupName FrontDoorQS_rg0 `
-Customrule $nonUSBlockRule  `
-Mode Prevention `
-EnabledState Enabled
```
## <a name="link-waf-policy-to-a-front-door-frontend-host"></a>Vincular a política WAF a um host de front-end do Front Door
Vincule o objeto de política WAF ao host de front-end do Front Door e atualize as propriedades do Front Door. 

Para fazer isso, primeiro recupere o objeto de Front Door usando [Get-AzFrontDoor](/powershell/module/az.frontdoor/get-azfrontdoor). 

```
$geoFrontDoorObjectExample = Get-AzFrontDoor -ResourceGroupName FrontDoorQS_rg0
$geoFrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $geoPolicy.Id
```
Em seguida, defina a propriedade de front-end WebApplicationFirewallPolicyLink para a ID do recurso do `geoPolicy`usando [AzFrontDoor](/powershell/module/az.frontdoor/set-azfrontdoor).

```
Set-AzFrontDoor -InputObject $geoFrontDoorObjectExample[0]
```

> [!NOTE] 
> Você só precisará definir a propriedade WebApplicationFirewallPolicyLink uma vez para vincular uma política WAF a um host de front-end do Front Door. As atualizações subsequentes da política serão aplicadas automaticamente ao host de front-end.

## <a name="clean-up-resources"></a>Limpar os recursos

Nas etapas anteriores, você configurou uma regra de filtragem geográfica associada a uma política de WAF. Depois vinculou a política a um host de front-end do seu Front Door. Caso não precise mais da regra de filtragem geográfica ou da política de WAF, primeiro você deverá desassociar a política do host de front-end antes que a política de WAF possa ser excluída.

:::image type="content" source="media/front-door-geo-filtering/front-door-disassociate-policy.png" alt-text="Desassociar uma política de WAF":::

## <a name="next-steps"></a>Próximas etapas

Para saber como configurar um Firewall de Aplicativo Web para seu Front Door, prossiga para o próximo tutorial.

> [!div class="nextstepaction"]
> [Firewall do Aplicativo Web e Front Door](front-door-waf.md)
