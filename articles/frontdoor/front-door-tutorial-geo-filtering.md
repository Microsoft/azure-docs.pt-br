---
title: Tutorial – Configure a política de firewall do aplicativo Web de filtragem geográfica para o Azure Front Door Service
description: Neste tutorial, você aprenderá a criar uma política de filtragem geográfica simples e associar a política ao host de front-end Front Door existente
services: frontdoor
documentationcenter: ''
author: KumudD
manager: twooley
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: kumud
ms.reviewer: tyao
ms.openlocfilehash: 97a3d63f94ca7e60f57177703a98b60e3f6c42ec
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849186"
---
# <a name="how-to-set-up-a-geo-filtering-waf-policy-for-your-front-door"></a>Como configurar uma política de filtragem geográfica WAF para o Front Door
Este tutorial mostra como usar o Azure PowerShell para criar uma política de filtragem geográfica de exemplo e associar a política a seu host de front-end Front Door existente. Essa política de filtragem geográfica de exemplo bloqueará as solicitações de todos os outros países/regiões, exceto dos Estados Unidos.

Caso não tenha uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) agora.

## <a name="prerequisites"></a>Pré-requisitos
Antes de configurar uma política de filtragem geográfica, configure o ambiente do PowerShell e crie um perfil de Front Door.
### <a name="set-up-your-powershell-environment"></a>Configurar o ambiente do PowerShell
O Azure PowerShell fornece um conjunto de cmdlets que usa o modelo do [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) para gerenciar os recursos do Azure. 

Você pode instalar o [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) no computador local e usá-lo em qualquer sessão do PowerShell. Siga as instruções na página para entrar com suas credenciais Azure e instale o módulo Az PowerShell.

#### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Conecte-se ao Azure com um diálogo interativo para entrar
```
Connect-AzAccount
Install-Module -Name Az
```
Verifique se tem a versão atual do PowerShellGet instalada. Execute o comando abaixo e reabra o PowerShell.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 
#### <a name="install-azfrontdoor-module"></a>Instalar o módulo Az.FrontDoor 

```
Install-Module -Name Az.FrontDoor
```

### <a name="create-a-front-door-profile"></a>Criar um perfil de Front Door
Crie um perfil de Front Door seguindo as instruções descritas no [Guia de Início Rápido: Crie um perfil de Front Door](quickstart-create-front-door.md).

## <a name="define-geo-filtering-match-condition"></a>Definir condição de correspondência da filtragem geográfica

Crie uma condição de correspondência de exemplo que seleciona as solicitações não provenientes de "US" usando [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) nos parâmetros, ao criar uma condição de correspondência. Códigos de país de duas letras para o mapeamento de países são fornecidos [aqui](front-door-geo-filtering.md).

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
Encontre o nome do grupo de recursos que contém o perfil de Front Door usando `Get-AzResourceGroup`. Em seguida, crie um objeto de política `geoPolicy` contendo `nonUSBlockRule` usando [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) no grupo de recursos especificado que contém o perfil de Front Door. Você deve fornecer um nome exclusivo para a política geográfica. 

O exemplo abaixo usa o nome do Grupo de Recursos *myResourceGroupFD1* supondo que você criou o perfil de Front Door usando as instruções fornecidas no [Guia de Início Rápido: Criar um artigo de Front Door](quickstart-create-front-door.md). No exemplo abaixo, substitua o nome da política *geoPolicyAllowUSOnly* por um nome de política exclusivo.

```
$geoPolicy = New-AzFrontDoorWafPolicy `
-Name "geoPolicyAllowUSOnly" `
-resourceGroupName myResourceGroupFD1 `
-Customrule $nonUSBlockRule  `
-Mode Prevention `
-EnabledState Enabled
```

## <a name="link-waf-policy-to-a-front-door-frontend-host"></a>Vincular a política WAF a um host de front-end do Front Door
Vincule o objeto de política WAF ao host de front-end do Front Door e atualize as propriedades do Front Door. 

Para fazer isso, primeiro recupere o objeto de Front Door usando [Get-AzFrontDoor](/powershell/module/az.frontdoor/get-azfrontdoor). 

```
$geoFrontDoorObjectExample = Get-AzFrontDoor -ResourceGroupName myResourceGroupFD1
$geoFrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $geoPolicy.Id
```

Em seguida, defina a propriedade de front-end WebApplicationFirewallPolicyLink para a ID do recurso do `geoPolicy`usando [AzFrontDoor](/powershell/module/az.frontdoor/set-azfrontdoor).

```
Set-AzFrontDoor -InputObject $geoFrontDoorObjectExample[0]
```

> [!NOTE] 
> Você só precisará definir a propriedade WebApplicationFirewallPolicyLink uma vez para vincular uma política WAF a um host de front-end do Front Door. As atualizações subsequentes da política serão aplicadas automaticamente ao host de front-end.

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre o [Firewall do aplicativo Web do Azure](waf-overview.md).
- Saiba como [criar um Front Door](quickstart-create-front-door.md).
