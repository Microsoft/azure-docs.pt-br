---
title: Reescrever cabeçalhos HTTP no Gateway de Aplicativo do Azure
description: Este artigo fornece informações sobre como reescrever cabeçalhos HTTP no gateway Aplicativo Azure usando Azure PowerShell
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: how-to
ms.date: 04/12/2019
ms.author: absha
ms.openlocfilehash: 6938ad55915286af397fee6d72a333e3bb39a1e6
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397908"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-powershell"></a>Reescrever cabeçalhos de solicitação e resposta HTTP com Aplicativo Azure gateway-Azure PowerShell

Este artigo descreve como usar Azure PowerShell para configurar uma instância de [SKU v2 do gateway de aplicativo](./application-gateway-autoscaling-zone-redundant.md) para regravar os cabeçalhos HTTP em solicitações e respostas.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="before-you-begin"></a>Antes de começar

- Você precisa executar Azure PowerShell localmente para concluir as etapas neste artigo. Você também precisa ter o módulo AZ versão 1.0.0 ou posterior instalado. Execute `Import-Module Az` e, em seguida, `Get-Module Az` para determinar a versão que você instalou. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps). Depois de verificar a versão do PowerShell, execute `Login-AzAccount` para criar uma conexão com o Azure.
- Você precisa ter uma instância de SKU do gateway de aplicativo v2. Não há suporte para a regravação de cabeçalhos na SKU v1. Se você não tiver a SKU v2, crie uma instância de [SKU do gateway de aplicativo v2](./tutorial-autoscale-ps.md) antes de começar.

## <a name="create-required-objects"></a>Criar objetos necessários

Para configurar a reescrita do cabeçalho HTTP, você precisa concluir estas etapas.

1. Crie os objetos que são necessários para a reescrita do cabeçalho HTTP:

   - **RequestHeaderConfiguration** : usado para especificar os campos de cabeçalho de solicitação que você pretende reescrever e o novo valor para os cabeçalhos.

   - **ResponseHeaderConfiguration** : usado para especificar os campos de cabeçalho de resposta que você pretende reescrever e o novo valor para os cabeçalhos.

   - **Actionset** : contém as configurações dos cabeçalhos de solicitação e resposta especificados anteriormente.

   - **Condição** : uma configuração opcional. As condições de regravação avaliam o conteúdo das solicitações e respostas HTTP (S). A ação de regravação ocorrerá se a solicitação ou resposta HTTP (S) corresponder à condição de regravação.

     Se você associar mais de uma condição a uma ação, a ação ocorrerá somente quando todas as condições forem atendidas. Em outras palavras, a operação é uma operação AND lógica.

   - **RewriteRule** : contém várias combinações de condição de ação de regravação/regravação.

   - **RuleSequence** : uma configuração opcional que ajuda a determinar a ordem na qual as regras de regravação são executadas. Essa configuração é útil quando você tem várias regras de regravação em um conjunto de regravação. Uma regra de regravação que tem um valor de sequência de regra mais baixo é executada primeiro. Se você atribuir o mesmo valor de sequência de regra a duas regras de reescrita, a ordem de execução será não determinística.

     Se você não especificar o RuleSequence explicitamente, um valor padrão de 100 será definido.

   - **RewriteRuleSet** : contém várias regras de regravação que serão associadas a uma regra de roteamento de solicitação.

2. Anexe o RewriteRuleSet a uma regra de roteamento. A configuração de regravação é anexada ao ouvinte de origem por meio da regra de roteamento. Quando você usa uma regra de roteamento básica, a configuração de reescrita de cabeçalho é associada a um ouvinte de origem e é uma reescrita de cabeçalho global. Quando você usa uma regra de roteamento com base em caminho, a configuração de reescrita de cabeçalho é definida no mapa de caminho de URL. Nesse caso, ele se aplica somente à área de caminho específica de um site.

Você pode criar vários conjuntos de regravação de cabeçalho HTTP e aplicar cada regravação definida a vários ouvintes. Mas você pode aplicar apenas uma regravação definida a um ouvinte específico.

## <a name="sign-in-to-azure"></a>Entrar no Azure

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="specify-the-http-header-rewrite-rule-configuration"></a>Especificar a configuração da regra de reescrita do cabeçalho HTTP

Neste exemplo, modificaremos uma URL de redirecionamento regravando o cabeçalho de local na resposta HTTP sempre que o cabeçalho Location contiver uma referência a azurewebsites.net. Para fazer isso, adicionaremos uma condição para avaliar se o cabeçalho Location na resposta contém azurewebsites.net. Usaremos o padrão `(https?):\/\/.*azurewebsites\.net(.*)$` . Vamos usar `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` como o valor do cabeçalho. Esse valor substituirá *azurewebsites.net* por *contoso.com* no cabeçalho de local.

```azurepowershell
$responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Location" -HeaderValue "{http_resp_Location_1}://contoso.com{http_resp_Location_2}"
$actionSet = New-AzApplicationGatewayRewriteRuleActionSet -RequestHeaderConfiguration $requestHeaderConfiguration -ResponseHeaderConfiguration $responseHeaderConfiguration
$condition = New-AzApplicationGatewayRewriteRuleCondition -Variable "http_resp_Location" -Pattern "(https?):\/\/.*azurewebsites\.net(.*)$" -IgnoreCase
$rewriteRule = New-AzApplicationGatewayRewriteRule -Name LocationHeader -ActionSet $actionSet
$rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name LocationHeaderRewrite -RewriteRule $rewriteRule
```

## <a name="retrieve-the-configuration-of-your-application-gateway"></a>Recuperar a configuração do seu gateway de aplicativo

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
```

## <a name="retrieve-the-configuration-of-your-request-routing-rule"></a>Recuperar a configuração da sua regra de roteamento de solicitação

```azurepowershell
$reqRoutingRule = Get-AzApplicationGatewayRequestRoutingRule -Name rule1 -ApplicationGateway $appgw
```

## <a name="update-the-application-gateway-with-the-configuration-for-rewriting-http-headers"></a>Atualizar o gateway de aplicativo com a configuração para reescrever cabeçalhos HTTP

```azurepowershell
Add-AzApplicationGatewayRewriteRuleSet -ApplicationGateway $appgw -Name LocationHeaderRewrite -RewriteRule $rewriteRuleSet.RewriteRules
Set-AzApplicationGatewayRequestRoutingRule -ApplicationGateway $appgw -Name rule1 -RuleType $reqRoutingRule.RuleType -BackendHttpSettingsId $reqRoutingRule.BackendHttpSettings.Id -HttpListenerId $reqRoutingRule.HttpListener.Id -BackendAddressPoolId $reqRoutingRule.BackendAddressPool.Id -RewriteRuleSetId $rewriteRuleSet.Id
Set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="delete-a-rewrite-rule"></a>Excluir uma regra de regravação

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
Remove-AzApplicationGatewayRewriteRuleSet -Name "LocationHeaderRewrite" -ApplicationGateway $appgw
$requestroutingrule= Get-AzApplicationGatewayRequestRoutingRule -Name "rule1" -ApplicationGateway $appgw
$requestroutingrule.RewriteRuleSet= $null
set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como configurar alguns casos de uso comuns, consulte [cenários de reescrita de cabeçalho comum](./rewrite-http-headers.md).