---
title: Configurar uma regra de limite de taxa de firewall do aplicativo Web para a porta frontal-Azure PowerShell
description: Saiba como configurar uma regra de limite de taxa para um ponto de extremidade de porta frontal existente.
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: kumud
ms.reviewer: tyao
ms.openlocfilehash: 99af39e996aaadd572603f63d019ff929b679550
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67846253"
---
# <a name="configure-a-web-application-firewall-rate-limit-rule-using-azure-powershell"></a>Configurar uma regra de limite de taxa de firewall do aplicativo Web usando Azure PowerShell
A regra de limite de taxa do firewall do aplicativo Web do Azure (WAF) para o Azure front door controla o número de solicitações permitidas de um único IP de cliente durante uma duração de um minuto.
Este artigo mostra como configurar uma regra de limite de taxa WAF que controla o número de solicitações permitidas de um único cliente para um aplicativo Web que contém */promo* na URL usando Azure PowerShell.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar a configurar uma política de limite de taxa, configure o ambiente do PowerShell e crie um perfil de porta frontal.
### <a name="set-up-your-powershell-environment"></a>Configurar o ambiente do PowerShell
O Azure PowerShell fornece um conjunto de cmdlets que usa o modelo do [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) para gerenciar os recursos do Azure. 

Você pode instalar o [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) no computador local e usá-lo em qualquer sessão do PowerShell. Siga as instruções na página para entrar com suas credenciais do Azure e instalar o módulo AZ PowerShell.

#### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Conecte-se ao Azure com um diálogo interativo para entrar
```
Connect-AzAccount

```
Antes de instalar o módulo Front Door, verifique se você tem a versão atual do PowerShellGet instalada. Execute o comando abaixo e reabra o PowerShell.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

#### <a name="install-azfrontdoor-module"></a>Instalar o módulo Az.FrontDoor 

```
Install-Module -Name Az.FrontDoor
```
### <a name="create-a-front-door-profile"></a>Criar um perfil de Front Door
Crie um perfil de Front Door seguindo as instruções descritas no [Guia de Início Rápido: Criar um perfil de porta de front-end](quickstart-create-front-door.md)

## <a name="define-url-match-conditions"></a>Definir condições de correspondência de URL
Defina uma condição de correspondência de URL (URL contém/promo) usando [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject).
O exemplo a seguir corresponde a */promo* como o valor da variável *RequestUri* :

```powershell-interactive
   $promoMatchCondition = New-AzFrontDoorWafMatchConditionObject `
     -MatchVariable RequestUri `
     -OperatorProperty Contains `
     -MatchValue "/promo"
```
## <a name="create-a-custom-rate-limit-rule"></a>Criar uma regra de limite de taxa personalizada
Defina um limite de taxa usando [New-AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject). No exemplo a seguir, o limite é definido como 1000. As solicitações de qualquer cliente para a página promocional excedendo 1000 durante um minuto são bloqueadas até o próximo minuto ser iniciado.

```powershell-interactive
   $promoRateLimitRule = New-AzFrontDoorWafCustomRuleObject `
     -Name "rateLimitRule" `
     -RuleType RateLimitRule `
     -MatchCondition $promoMatchCondition `
     -RateLimitThreshold 1000 `
     -Action Block -Priority 1
```


## <a name="configure-a-security-policy"></a>Configurar uma política de segurança

Encontre o nome do grupo de recursos que contém o perfil de Front Door usando `Get-AzureRmResourceGroup`. Em seguida, configure uma política de segurança com uma regra de limite de taxa personalizada usando [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) no grupo de recursos especificado que contém o perfil de porta frontal.

O exemplo abaixo usa o nome do Grupo de Recursos *myResourceGroupFD1* supondo que você criou o perfil de Front Door usando as instruções fornecidas no [Guia de Início Rápido: Criar um artigo de Front Door](quickstart-create-front-door.md).

 usando [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy).

```powershell-interactive
   $ratePolicy = New-AzFrontDoorWafPolicy `
     -Name "RateLimitPolicyExamplePS" `
     -resourceGroupName myResourceGroupFD1 `
     -Customrule $promoRateLimitRule `
     -Mode Prevention `
     -EnabledState Enabled
```
## <a name="link-policy-to-a-front-door-front-end-host"></a>Política de link para um host front-end da porta frontal
Vincule o objeto de política de segurança a um host front-end de porta frontal existente e atualize as propriedades da porta frontal. Primeiro, recupere o objeto de porta frontal usando o comando [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor) .
Em seguida, defina a propriedade *WebApplicationFirewallPolicyLink* de front-end como o ResourceId do "$ratePolicy" criado na etapa anterior usando o comando [set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor) . 

O exemplo abaixo usa o nome do Grupo de Recursos *myResourceGroupFD1* supondo que você criou o perfil de Front Door usando as instruções fornecidas no [Guia de Início Rápido: Criar um artigo de Front Door](quickstart-create-front-door.md). Além disso, no exemplo abaixo, substitua $frontDoorName pelo nome do perfil de porta frontal. 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $ratePolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> Você só precisa definir a propriedade *WebApplicationFirewallPolicyLink* uma vez para vincular uma política de segurança a um front-end de porta frontal. As atualizações de política subsequentes são aplicadas automaticamente ao front-end.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre a [porta frontal](front-door-overview.md) 


