---
title: Configurar respostas personalizadas para o WAF (firewall do aplicativo Web) com a porta frontal do Azure
description: Saiba como configurar um código de resposta personalizado e uma mensagem quando o WAF bloqueia uma solicitação.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 06/10/2020
ms.author: victorh
ms.reviewer: tyao
ms.openlocfilehash: 8fc6e71494df36cd6f823661b18e4a3d8ce2938c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94563674"
---
# <a name="configure-a-custom-response-for-azure-web-application-firewall-waf"></a>Configurar uma resposta personalizada para o Firewall do aplicativo Web do Azure (WAF)

Por padrão, quando o WAF bloqueia uma solicitação devido a uma regra correspondente, ele retorna um código de status 403 com **a solicitação é mensagem bloqueada** . A mensagem padrão também inclui a cadeia de caracteres de referência de rastreamento que pode ser usada para vincular [entradas de log](./waf-front-door-monitor.md) para a solicitação.  Você pode configurar um código de status de resposta personalizado e uma mensagem personalizada com uma cadeia de caracteres de referência para seu caso de uso. Este artigo descreve como configurar uma página de resposta personalizada quando uma solicitação é bloqueada pelo WAF.

## <a name="configure-custom-response-status-code-and-message-use-portal"></a>Configurar o código de status de resposta personalizado e o portal de uso de mensagens

Você pode configurar um código de status de resposta personalizado e o corpo em "configurações de política" no portal do WAF.

:::image type="content" source="../media/waf-front-door-configure-custom-response-code/custom-response-settings.png" alt-text="Configurações de política de WAF":::

No exemplo acima, mantivemos o código de resposta como 403 e configuramos uma mensagem curta "entre em contato conosco", conforme mostrado na imagem abaixo:

:::image type="content" source="../media/waf-front-door-configure-custom-response-code/custom-response.png" alt-text="Exemplo de resposta personalizada":::

"{{Azure-ref}}" insere a cadeia de caracteres de referência exclusiva no corpo da resposta. O valor corresponde ao campo TrackingReference nos `FrontdoorAccessLog` logs e `FrontdoorWebApplicationFirewallLog` .

## <a name="configure-custom-response-status-code-and-message-use-powershell"></a>Configurar o código de status de resposta personalizado e a mensagem usar o PowerShell

### <a name="set-up-your-powershell-environment"></a>Configurar o ambiente do PowerShell

O Azure PowerShell fornece um conjunto de cmdlets que usa o modelo do [Azure Resource Manager](../../azure-resource-manager/management/overview.md) para gerenciar os recursos do Azure. 

Você pode instalar o [Azure PowerShell](/powershell/azure/) no computador local e usá-lo em qualquer sessão do PowerShell. Siga as instruções na página para entrar com suas credenciais Azure e instale o módulo Az PowerShell.

### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Conecte-se ao Azure com um diálogo interativo para entrar

```
Connect-AzAccount
Install-Module -Name Az

```
Verifique se tem a versão atual do PowerShellGet instalada. Execute o comando abaixo e reabra o PowerShell.
```
Install-Module PowerShellGet -Force -AllowClobber
``` 
### <a name="install-azfrontdoor-module"></a>Instalar o módulo Az.FrontDoor 

```
Install-Module -Name Az.FrontDoor
```

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

No Azure, você pode alocar recursos relacionados a um grupo de recursos. Aqui, criamos um grupo de recursos usando [New-AzResourceGroup](/powershell/module/Az.resources/new-Azresourcegroup).

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupWAF
```

### <a name="create-a-new-waf-policy-with-custom-response"></a>Criar uma nova política de WAF com resposta personalizada 

Abaixo está um exemplo de como criar uma nova política de WAF com o código de status de resposta personalizado definido como 405, e a mensagem para **você está bloqueada.** usando [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy)

```azurepowershell
# WAF policy setting
New-AzFrontDoorWafPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-EnabledState enabled `
-Mode Detection `
-CustomBlockResponseStatusCode 405 `
-CustomBlockResponseBody "<html><head><title>You are blocked.</title></head><body></body></html>"
```

Modifique as configurações do código de resposta personalizado ou do corpo da resposta de uma política WAF existente, usando [Update-AzFrontDoorFireWallPolicy](/powershell/module/az.frontdoor/Update-AzFrontDoorWafPolicy).

```azurepowershell
# modify WAF response code
Update-AzFrontDoorFireWallPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-EnabledState enabled `
-Mode Detection `
-CustomBlockResponseStatusCode 403
```

```azurepowershell
# modify WAF response body
Update-AzFrontDoorFireWallPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-CustomBlockResponseBody "<html><head><title>Forbidden</title></head><body>{{azure-ref}}</body></html>"
```

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre o [Firewall do aplicativo Web com a porta frontal do Azure](../afds/afds-overview.md)