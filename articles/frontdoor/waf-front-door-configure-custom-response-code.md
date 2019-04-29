---
title: Configurar uma resposta personalizada para o firewall do aplicativo web na frente do Azure
description: Saiba como configurar um código de resposta personalizada e uma mensagem quando o firewall do aplicativo web (WAF) bloqueia uma solicitação.
services: frontdoor
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2019
ms.author: tyao;kumud
ms.openlocfilehash: 2d16893420f27caf4f8b00dc32069e3296d7c236
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61459769"
---
# <a name="configure-a-custom-response-for-azure-web-application-firewall"></a>Configurar uma resposta personalizada para o firewall do aplicativo web do Azure

Por padrão, quando o firewall do aplicativo web do Azure (WAF) de frente com o Azure bloqueia uma solicitação devido a uma regra correspondente, ele retorna um código de 403 status com **a solicitação é bloqueada** mensagem. Este artigo descreve como configurar um código de status de resposta personalizada e uma mensagem de resposta quando uma solicitação é bloqueada pelo WAF.

## <a name="set-up-your-powershell-environment"></a>Configurar o ambiente do PowerShell
O Azure PowerShell fornece um conjunto de cmdlets que usa o modelo do [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) para gerenciar os recursos do Azure. 

Você pode instalar o [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) no computador local e usá-lo em qualquer sessão do PowerShell. Siga as instruções na página para entrar com suas credenciais Azure e instale o módulo Az PowerShell.

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

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

No Azure, você pode alocar recursos relacionados a um grupo de recursos. Neste exemplo, você deve criar um grupo de recursos usando [New-AzResourceGroup](/powershell/module/Az.resources/new-Azresourcegroup).

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupWAF
```

## <a name="create-a-new-waf-policy-with-custom-response"></a>Criar uma nova política de WAF com resposta personalizada 

Abaixo está um exemplo de criação de uma nova política de WAF com código de status de resposta personalizada definido como 405 e a mensagem a ser **estão sendo bloqueados.** usando o [New-AzFrontDoorFireWallPolicy](/powershell/module/az.frontdoor/new-azfrontdoorfirewallPolicy).

```azurepowershell
# WAF policy setting
New-AzFrontDoorFireWallPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-EnabledState enabled `
-Mode Detection `
-CustomBlockResponseStatusCode 405 `
-CustomBlockResponseBody "<html><head><title>You are blocked.</title></head><body></body></html>"
```

Modificar o código de resposta personalizadas ou configurações de corpo de resposta de uma política existente do WAF, usando [AzFrontDoor conjunto](/powershell/module/az.frontdoor/set-azfrontdoor).

```azurepowershell
# modify WAF response code
Set-AzFrontDoorFireWallPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-EnabledState enabled `
-Mode Detection `
-CustomBlockResponseStatusCode 403
```

```azurepowershell
# modify WAF response body
Set-AzFrontDoorFireWallPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-CustomBlockResponseBody "<html><head><title> Forbidden</title></head><body></body></html>"
```

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre [da frente](front-door-overview.md)