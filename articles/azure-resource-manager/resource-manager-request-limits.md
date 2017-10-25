---
title: "Limites de solicitação do Azure Resource Manager | Microsoft Docs"
description: "Descreve como usar a limitação com solicitações Azure Resource Manager quando os limites de assinatura tiverem sido atingidos."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: e1047233-b8e4-4232-8919-3268d93a3824
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/11/2017
ms.author: tomfitz
ms.openlocfilehash: 6d7eeaf460674c3ab98425a5412ffa465b9ffd1d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="throttling-resource-manager-requests"></a>Restrição de solicitações do Resource Manager
Para cada assinatura e locatário, os limites do Resource Manager limita as solicitações de leitura para 15.000 por hora e solicitações de gravação para 1.200 por hora. Esses limites se aplicam a cada instância do Azure Resource Manager. Há várias instâncias em todas as regiões do Azure e o Azure Resource Manager é implantado em todas as regiões do Azure.  Portanto, na prática, os limites são efetivamente muito maiores do que aqueles listados acima, pois as solicitações do usuário são geralmente atendidas por muitas instâncias diferentes.

Se seu aplicativo ou script atingir esses limites, será necessário restringir suas solicitações. Este tópico mostra como determinar as solicitações restantes que você tem antes de atingir o limite e como responder quando você tiver atingido o limite.

Quando você alcança o limite, recebe o código de status HTTP **429 Excesso de solicitações**.

O número de solicitações é no escopo da sua assinatura ou de seu locatário. Se você tiver vários aplicativos simultâneos fazendo solicitações em sua assinatura, as solicitações desses aplicativos serão adicionadas juntas para determinar o número de solicitações restantes.

As solicitações no escopo da assinatura são aquelas que envolvem a passagem da ID de assinatura, assim como a recuperação dos grupos de recursos em sua assinatura. Solicitações no escopo do locatário não incluem sua ID de assinatura, assim como a recuperação de locais do Azure válidos.

## <a name="remaining-requests"></a>Solicitações restantes
Você pode determinar o número de solicitações restantes ao examinar cabeçalhos de resposta. Cada solicitação inclui os valores para o número de solicitações de leitura e gravação restantes. A tabela a seguir descreve os cabeçalhos de resposta que você pode examinar em busca desses valores:

| Cabeçalho de resposta | Descrição |
| --- | --- |
| x-ms-ratelimit-remaining-subscription-reads |Leituras no escopo da assinatura restantes |
| x-ms-ratelimit-remaining-subscription-writes |Gravações no escopo da assinatura restantes |
| x-ms-ratelimit-remaining-tenant-reads |Leituras no escopo do locatário restantes |
| x-ms-ratelimit-remaining-tenant-writes |Gravações no escopo do locatário restantes |
| x-ms-ratelimit-remaining-subscription-resource-requests |Solicitações de tipo de recurso no escopo da assinatura restantes.<br /><br />Esse valor de cabeçalho só será retornado se um serviço tiver substituído o limite padrão. O Resource Manager adiciona esse valor em vez das leituras ou gravações da assinatura. |
| x-ms-ratelimit-remaining-subscription-resource-entities-read |Solicitações de coletas de tipo de recurso no escopo da assinatura restantes.<br /><br />Esse valor de cabeçalho só será retornado se um serviço tiver substituído o limite padrão. Esse valor fornece o número de solicitações de coleta restantes (listar recursos). |
| x-ms-ratelimit-remaining-tenant-resource-requests |Solicitações de tipo de recurso no escopo do locatário restantes.<br /><br />Esse cabeçalho será adicionado somente para solicitações em nível de locatário e somente se um serviço tiver substituído o limite padrão. O Resource Manager adiciona esse valor em vez das leituras ou gravações do locatário. |
| x-ms-ratelimit-remaining-tenant-resource-entities-read |Solicitações de coletas de tipo de recurso no escopo do locatário restantes.<br /><br />Esse cabeçalho será adicionado somente para solicitações em nível de locatário e somente se um serviço tiver substituído o limite padrão. |

## <a name="retrieving-the-header-values"></a>Recuperar os valores de cabeçalho
Recuperar esses valores de cabeçalho no seu código ou script não é diferente de recuperar um outro valor de cabeçalho qualquer. 

Por exemplo, em **C#**, recupere o valor de cabeçalho de um objeto **HttpWebResponse** chamado **response** com o código a seguir:

```cs
response.Headers.GetValues("x-ms-ratelimit-remaining-subscription-reads").GetValue(0)
```

No **PowerShell**, você recupera o valor de cabeçalho de uma operação Invoke-WebRequest.

```powershell
$r = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/{guid}/resourcegroups?api-version=2016-09-01 -Method GET -Headers $authHeaders
$r.Headers["x-ms-ratelimit-remaining-subscription-reads"]
```

Ou, se quiser ver as solicitações restantes para depuração, você pode fornecer o parâmetro **-Debug** em seu cmdlet **PowerShell**.

```powershell
Get-AzureRmResourceGroup -Debug
```

Que retorna muitos valores, incluindo o seguinte valor de resposta:

```powershell
...
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
OK

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-reads: 14999
...
```

Na **CLI do Azure**, você recupera o valor do cabeçalho usando a opção mais detalhada.

```azurecli
azure group list -vv --json
```

Que retorna muitos valores, incluindo o seguinte objeto:

```azurecli
...
silly: returnObject
{
  "statusCode": 200,
  "header": {
    "cache-control": "no-cache",
    "pragma": "no-cache",
    "content-type": "application/json; charset=utf-8",
    "expires": "-1",
    "x-ms-ratelimit-remaining-subscription-reads": "14998",
    ...
```

## <a name="waiting-before-sending-next-request"></a>Aguardando antes de enviar a próxima solicitação
Quando você alcançar o limite de solicitação, o Resource Manager retorna o código de status HTTP **429** e um valor **Retry-After** no cabeçalho. O valor **Retry-After** especifica o número de segundos que o aplicativo deve aguardar (ou ficar suspenso) antes de enviar a próxima solicitação. Se você enviar uma solicitação antes que o valor de repetição tenha decorrido, sua solicitação não será processada e um novo valor de repetição será retornado.

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre limites e cotas, confira [Assinatura do Azure e limite de serviços, cotas e restrições](../azure-subscription-service-limits.md).
* Para saber mais sobre como lidar com solicitações assíncronas de REST, confira [Track asynchronous Azure operations](resource-manager-async-operations.md) (Rastrear operações assíncronas do Azure).
