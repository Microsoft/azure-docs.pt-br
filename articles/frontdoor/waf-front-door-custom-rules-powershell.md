---
title: Configurar uma política de WAF (firewall) do aplicativo web com regras personalizadas e padrão Ruse definida para a porta da frente - Azure PowerShell
description: Saiba como configurar um WAF política consiste em regras personalizadas e gerenciadas para um ponto de extremidade de porta de entrada existente.
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2019
ms.author: kumud;tyao
ms.openlocfilehash: 7d024dd958e6b29b52f095a9a55a67154bf6cde6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61459786"
---
# <a name="configure-a-web-application-firewall-policy-using-azure-powershell"></a>Configurar uma política de firewall de aplicativo web usando o Azure PowerShell
Política de WAF (firewall) de aplicativos web do Azure define inspeções necessárias quando uma solicitação chega na frente.
Este artigo mostra como configurar uma política de WAF que consiste em algumas regras personalizadas e com gerenciados pelo Azure Ruse conjunto padrão habilitado.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar a configurar uma política de limite de taxa, configure o ambiente do PowerShell e criar um perfil de porta da frente.
### <a name="set-up-your-powershell-environment"></a>Configurar o ambiente do PowerShell
O Azure PowerShell fornece um conjunto de cmdlets que usa o modelo do [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) para gerenciar os recursos do Azure. 

Você pode instalar o [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) no computador local e usá-lo em qualquer sessão do PowerShell. Siga as instruções na página para entrar com suas credenciais do Azure e instale o módulo do PowerShell do Az.

#### <a name="sign-in-to-azure"></a>Entrar no Azure
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
Crie um perfil de Front Door seguindo as instruções descritas no [Guia de Início Rápido: Criar um perfil de porta da frente](quickstart-create-front-door.md)

## <a name="custom-rule-based-on-http-parameters"></a>Regra personalizada com base nos parâmetros de http

O exemplo a seguir mostra como configurar uma regra personalizada com duas condições de correspondência usando [New-AzFrontDoorMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoormatchconditionobject). As solicitações são de um site especificado, conforme definido por referenciador e cadeia de caracteres de consulta não contém "senha". 

```powershell-interactive
$referer = New-AzFrontDoorMatchConditionObject -MatchVariable RequestHeader -OperatorProperty Equal -Selector "Referer" -MatchValue "www.mytrustedsites.com/referpage.html"
$password = New-AzFrontDoorMatchConditionObject -MatchVariable QueryString -OperatorProperty Contains -MatchValue "password"
$AllowFromTrustedSites = New-AzFrontDoorCustomRuleObject -Name "AllowFromTrustedSites" -RuleType MatchRule -MatchCondition $referer,$password -Action Allow -Priority 1
```

## <a name="custom-rule-based-on-http-request-method"></a>Regra personalizada com base no método de solicitação http
Criar uma regra de bloqueio "PUT" usando o método [New-AzFrontDoorCustomRuleObject](/powershell/module/Az.FrontDoor/New-AzFrontDoorCustomRuleObject) da seguinte maneira:

```powershell-interactive
$put = New-AzFrontDoorMatchConditionObject -MatchVariable RequestMethod -OperatorProperty Equal -MatchValue PUT
$BlockPUT = New-AzFrontDoorCustomRuleObject -Name "BlockPUT" -RuleType MatchRule -MatchCondition $put -Action Block -Priority 2
```

## <a name="create-a-custom-rule-based-on-size-constraint"></a>Criar uma regra personalizada com base na restrição de tamanho

O exemplo a seguir cria uma regra de bloqueio de solicitações de URL que é maior que 100 caracteres, usando o Azure PowerShell:
```powershell-interactive
$url = New-AzFrontDoorMatchConditionObject -MatchVariable RequestUri -OperatorProperty GreaterThanOrEqual -MatchValue 100
$URLOver100 = New-AzFrontDoorCustomRuleObject -Name "URLOver100" -RuleType MatchRule -MatchCondition $url -Action Block -Priority 3
```
## <a name="add-managed-default-rule-set"></a>Adicionar regra gerenciado padrão do conjunto

O exemplo a seguir cria um padrão regra conjunto gerenciado usando o Azure PowerShell:
```powershell-interactive
$managedRules = New-AzFrontDoorManagedRuleObject -Type DefaultRuleSet -Version "preview-0.1"
```
## <a name="configure-a-security-policy"></a>Configurar uma política de segurança

Encontre o nome do grupo de recursos que contém o perfil de Front Door usando `Get-AzResourceGroup`. Em seguida, configure uma política de segurança com as regras criadas nas etapas anteriores usando [New-AzFrontDoorFireWallPolicy](/powershell/module/az.frontdoor/new-azfrontdoorfirewallPolicy) no grupo de recursos especificado que contém o perfil de porta da frente.

```powershell-interactive
$myWAFPolicy=New-AzFrontDoorFireWallPolicy -Name $policyName -ResourceGroupName $resourceGroupName -Customrule $AllowFromTrustedSites,$BlockPUT,$URLOver100 -ManagedRule $managedRules -EnabledState Enabled -Mode Prevention
```

## <a name="link-policy-to-a-front-door-front-end-host"></a>Política de link para um host de front-end de porta da frente
Vincular o objeto de diretiva de segurança em um host de front-end existente da frente e atualizar propriedades de porta da frente. Primeiro, recupere o objeto de porta da frente usando [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor).
Em seguida, defina o front-end *WebApplicationFirewallPolicyLink* propriedade para o *resourceId* de "$myWAFPolicy$" criado na etapa anterior usando [Set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor). 

O exemplo abaixo usa o nome do Grupo de Recursos *myResourceGroupFD1* supondo que você criou o perfil de Front Door usando as instruções fornecidas no [Guia de Início Rápido: Criar um artigo de Front Door](quickstart-create-front-door.md). Além disso, no exemplo abaixo, substitua $frontDoorName pelo nome do seu perfil de porta da frente. 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $myWAFPolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> Você só precisa definir *WebApplicationFirewallPolicyLink* propriedade uma vez para vincular uma política de segurança a uma porta da frente front-end. Atualizações de política subsequentes são aplicadas automaticamente para o front-end.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [da frente](front-door-overview.md) 
- Saiba mais sobre [WAF para frente](waf-overview.md)
