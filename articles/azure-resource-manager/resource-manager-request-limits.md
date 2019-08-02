---
title: Limites de solicitação - Azure Resource Manager
description: Descreve como usar a limitação com solicitações Azure Resource Manager quando os limites de assinatura tiverem sido atingidos.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: tomfitz
ms.custom: seodec18
ms.openlocfilehash: f457b316d9f499f2cab02452c1b03ad07a9aef27
ms.sourcegitcommit: af58483a9c574a10edc546f2737939a93af87b73
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68302824"
---
# <a name="throttling-resource-manager-requests"></a>Restrição de solicitações do Resource Manager

Para cada assinatura e inquilino do Azure, o Gerenciador de Recursos permite até 12.000 solicitações de leitura por hora e 1.200 solicitações de gravação por hora. Esses limites têm como escopo a entidade de segurança (usuário ou aplicativo) que faz as solicitações e a ID da assinatura ou a ID do locatário. Se suas solicitações vierem de mais de uma entidade de segurança, seu limite entre a assinatura ou o locatário será maior que 12.000 e 1.200 por hora.

As solicitações são aplicadas à sua assinatura ou ao seu inquilino. As solicitações de assinatura são aquelas que envolvem a passagem da sua ID de assinatura, como a recuperação dos grupos de recursos em sua assinatura. As solicitações de inquilino não incluem seu ID de inscrição, como a recuperação de locais válidos do Azure.

Esses limites se aplicam a cada instância do Azure Resource Manager. Há várias instâncias em todas as regiões do Azure e o Azure Resource Manager é implantado em todas as regiões do Azure.  Portanto, na prática, os limites são efetivamente muito maiores do que esses, pois as solicitações do usuário são geralmente atendidas por muitas instâncias diferentes.

Se seu aplicativo ou script atingir esses limites, será necessário restringir suas solicitações. Este artigo mostra como determinar as solicitações restantes que você tem antes de atingir o limite e como responder quando atingir o limite.

Quando você alcança o limite, recebe o código de status HTTP **429 Excesso de solicitações**.

O grafo de recursos do Azure limita o número de solicitações para suas operações. As etapas neste artigo para determinar as solicitações restantes e como responder quando o limite é atingido também se aplicam ao grafo de recursos. No entanto, o grafo de recursos define seu próprio limite e a taxa de redefinição. Para obter mais informações, consulte [limitação no grafo de recursos do Azure](../governance/resource-graph/overview.md#throttling).

## <a name="remaining-requests"></a>Solicitações restantes
Você pode determinar o número de solicitações restantes ao examinar cabeçalhos de resposta. As solicitações de leitura retornam um valor no cabeçalho para o número de solicitações de leitura restantes. As solicitações de gravação incluem um valor para o número de solicitações de gravação restantes. A tabela a seguir descreve os cabeçalhos de resposta que você pode examinar em busca desses valores:

| Cabeçalho de resposta | DESCRIÇÃO |
| --- | --- |
| x-ms-ratelimit-remaining-subscription-reads |Leituras no escopo da assinatura restantes. Esse valor é retornado em operações de leitura. |
| x-ms-ratelimit-remaining-subscription-writes |Gravações no escopo da assinatura restantes. Esse valor é retornado em operações de gravação. |
| x-ms-ratelimit-remaining-tenant-reads |Leituras no escopo do locatário restantes |
| x-ms-ratelimit-remaining-tenant-writes |Gravações no escopo do locatário restantes |
| x-ms-ratelimit-remaining-subscription-resource-requests |Solicitações de tipo de recurso no escopo da assinatura restantes.<br /><br />Esse valor de cabeçalho só será retornado se um serviço tiver substituído o limite padrão. O Resource Manager adiciona esse valor em vez das leituras ou gravações da assinatura. |
| x-ms-ratelimit-remaining-subscription-resource-entities-read |Solicitações de coletas de tipo de recurso no escopo da assinatura restantes.<br /><br />Esse valor de cabeçalho só será retornado se um serviço tiver substituído o limite padrão. Esse valor fornece o número de solicitações de coleta restantes (listar recursos). |
| x-ms-ratelimit-remaining-tenant-resource-requests |Solicitações de tipo de recurso no escopo do locatário restantes.<br /><br />Esse cabeçalho será adicionado somente para solicitações em nível de locatário e somente se um serviço tiver substituído o limite padrão. O Resource Manager adiciona esse valor em vez das leituras ou gravações do locatário. |
| x-ms-ratelimit-remaining-tenant-resource-entities-read |Solicitações de coletas de tipo de recurso no escopo do locatário restantes.<br /><br />Esse cabeçalho será adicionado somente para solicitações em nível de locatário e somente se um serviço tiver substituído o limite padrão. |

## <a name="retrieving-the-header-values"></a>Recuperar os valores de cabeçalho
Recuperar esses valores de cabeçalho no seu código ou script não é diferente de recuperar um outro valor de cabeçalho qualquer. 

Por exemplo, em **C#** , recupere o valor de cabeçalho de um objeto **HttpWebResponse** chamado **response** com o código a seguir:

```cs
response.Headers.GetValues("x-ms-ratelimit-remaining-subscription-reads").GetValue(0)
```

No **PowerShell**, você recupera o valor de cabeçalho de uma operação Invoke-WebRequest.

```powershell
$r = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/{guid}/resourcegroups?api-version=2016-09-01 -Method GET -Headers $authHeaders
$r.Headers["x-ms-ratelimit-remaining-subscription-reads"]
```

Para um exemplo completo do PowerShell, consulte [Verificar os limites do gerenciador de recursos para uma assinatura](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI).

Se você deseja ver as solicitações restantes de depuração, forneça o parâmetro **-Debug** no cmdlet do **PowerShell**.

```powershell
Get-AzResourceGroup -Debug
```

Que retorna muitos valores, incluindo o seguinte valor de resposta:

```powershell
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
OK

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-reads: 11999
```

Para obter limites de gravação, use uma operação de gravação: 

```powershell
New-AzResourceGroup -Name myresourcegroup -Location westus -Debug
```

Que retorna muitos valores, incluindo os valores a seguir:

```powershell
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
Created

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-writes: 1199
```

Na **CLI do Azure**, você recupera o valor do cabeçalho usando a opção mais detalhada.

```azurecli
az group list --verbose --debug
```

Que retorna muitos valores, incluindo os valores a seguir:

```azurecli
msrest.http_logger : Response status: 200
msrest.http_logger : Response headers:
msrest.http_logger :     'Cache-Control': 'no-cache'
msrest.http_logger :     'Pragma': 'no-cache'
msrest.http_logger :     'Content-Type': 'application/json; charset=utf-8'
msrest.http_logger :     'Content-Encoding': 'gzip'
msrest.http_logger :     'Expires': '-1'
msrest.http_logger :     'Vary': 'Accept-Encoding'
msrest.http_logger :     'x-ms-ratelimit-remaining-subscription-reads': '11998'
```

Para obter limites de gravação, use uma operação de gravação: 

```azurecli
az group create -n myresourcegroup --location westus --verbose --debug
```

Que retorna muitos valores, incluindo os valores a seguir:

```azurecli
msrest.http_logger : Response status: 201
msrest.http_logger : Response headers:
msrest.http_logger :     'Cache-Control': 'no-cache'
msrest.http_logger :     'Pragma': 'no-cache'
msrest.http_logger :     'Content-Length': '163'
msrest.http_logger :     'Content-Type': 'application/json; charset=utf-8'
msrest.http_logger :     'Expires': '-1'
msrest.http_logger :     'x-ms-ratelimit-remaining-subscription-writes': '1199'
```

## <a name="waiting-before-sending-next-request"></a>Aguardando antes de enviar a próxima solicitação
Quando você alcançar o limite de solicitação, o Resource Manager retorna o código de status HTTP **429** e um valor **Retry-After** no cabeçalho. O valor **Retry-After** especifica o número de segundos que o aplicativo deve aguardar (ou ficar suspenso) antes de enviar a próxima solicitação. Se você enviar uma solicitação antes do valor de repetição, sua solicitação não será processada e um novo valor de repetição será retornado.

## <a name="next-steps"></a>Próximas etapas

* Para um exemplo completo do PowerShell, consulte [Verificar os limites do gerenciador de recursos para uma assinatura](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI).
* Para obter mais informações sobre limites e cotas, confira [Assinatura do Azure e limite de serviços, cotas e restrições](../azure-subscription-service-limits.md).
* Para saber mais sobre como lidar com solicitações assíncronas de REST, confira [Track asynchronous Azure operations](resource-manager-async-operations.md) (Rastrear operações assíncronas do Azure).
