---
title: Limitação e limites de solicitação
description: Descreve como usar a limitação com solicitações Azure Resource Manager quando os limites de assinatura tiverem sido atingidos.
ms.topic: conceptual
ms.date: 12/15/2020
ms.custom: seodec18
ms.openlocfilehash: c1fd707bf9f13ed88bf9acb5d2ad70d56c453a90
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105024618"
---
# <a name="throttling-resource-manager-requests"></a>Restrição de solicitações do Resource Manager

Este artigo descreve como Azure Resource Manager limita as solicitações. Ele mostra como acompanhar o número de solicitações que permanecem antes de atingir o limite e como responder quando você atingir o limite.

A limitação ocorre em dois níveis. Azure Resource Manager limita as solicitações para a assinatura e o locatário. Se a solicitação estiver sob os limites de limitação para a assinatura e o locatário, o Resource Manager roteia a solicitação para o provedor de recursos. O provedor de recursos aplica limites de limitação que são adaptados para suas operações. A imagem a seguir mostra como a limitação é aplicada, uma vez que uma solicitação passa do usuário para Azure Resource Manager e o provedor de recursos.

![Limitação de solicitação](./media/request-limits-and-throttling/request-throttling.svg)

## <a name="subscription-and-tenant-limits"></a>Limites de assinatura e locatário

Todas as operações de nível de assinatura e de locatário estão sujeitas a limites de limitação. As solicitações de assinatura são aquelas que envolvem a passagem da sua ID de assinatura, como a recuperação dos grupos de recursos em sua assinatura. As solicitações de inquilino não incluem seu ID de inscrição, como a recuperação de locais válidos do Azure.

Os limites de limitação padrão por hora são mostrados na tabela a seguir.

| Escopo | Operations | Limite |
| ----- | ---------- | ------- |
| Subscription | reads | 12000 |
| Subscription | deletes | 15000 |
| Subscription | writes | 1200 |
| Locatário | reads | 12000 |
| Locatário | writes | 1200 |

O escopo desses limites são a entidade de segurança (usuário ou aplicativo) que faz as solicitações e a ID da assinatura ou a ID do locatário. Se suas solicitações vierem de mais de uma entidade de segurança, seu limite na assinatura ou no locatário será maior que 12.000 e 1.200 por hora.

Esses limites se aplicam a cada instância do Azure Resource Manager. Há várias instâncias em todas as regiões do Azure e o Azure Resource Manager é implantado em todas as regiões do Azure.  Portanto, na prática, os limites são maiores que esses limites. As solicitações de um usuário geralmente são tratadas por instâncias diferentes do Azure Resource Manager.

## <a name="resource-provider-limits"></a>Limites do provedor de recursos

Os provedores de recursos aplicam seus próprios limites de limitação. Como o Resource Manager limita-se pela ID principal e por instância do Resource Manager, o provedor de recursos pode receber mais solicitações do que os limites padrão na seção anterior.

Esta seção aborda os limites de limitação de alguns provedores de recursos amplamente usados.

### <a name="storage-throttling"></a>Limitação de armazenamento

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

### <a name="network-throttling"></a>Limitação de rede

O provedor de recursos Microsoft. Network aplica os seguintes limites de limitação:

| Operação | Limite |
| --------- | ----- |
| gravação/exclusão (PUT) | 1000 por 5 minutos |
| leitura (GET) | 10000 por 5 minutos |

### <a name="compute-throttling"></a>Limitação de computação

Para obter informações sobre limites de limitação para operações de computação, consulte [Solucionando problemas de erros de limitação de API-Compute](/troubleshoot/azure/virtual-machines/troubleshooting-throttling-errors).

Para verificar as instâncias de máquina virtual em um conjunto de dimensionamento de máquinas virtuais, use as [operações de conjuntos de dimensionamento de máquinas virtuais](/rest/api/compute/virtualmachinescalesetvms). Por exemplo, use o [conjunto de dimensionamento de máquinas virtuais VMs-List](/rest/api/compute/virtualmachinescalesetvms/list) com parâmetros para verificar o estado de energia de instâncias de máquina virtual. Essa API reduz o número de solicitações.

### <a name="azure-resource-graph-throttling"></a>Limitação do grafo de recursos do Azure

O [grafo de recursos do Azure](../../governance/resource-graph/overview.md) limita o número de solicitações para suas operações. As etapas neste artigo para determinar as solicitações restantes e como responder quando o limite é atingido também se aplicam ao grafo de recursos. No entanto, o grafo de recursos define seu próprio limite e a taxa de redefinição. Para obter mais informações, consulte [cabeçalhos de limitação de grafo de recursos](../../governance/resource-graph/concepts/guidance-for-throttled-requests.md#understand-throttling-headers).

### <a name="other-resource-providers"></a>Outros provedores de recursos

Para obter informações sobre a limitação em outros provedores de recursos, consulte:

* [Diretrizes de limitação do Azure Key Vault](../../key-vault/general/overview-throttling.md)
* [Solução de problemas do AKS](../../aks/troubleshooting.md#im-receiving-429---too-many-requests-errors)

## <a name="error-code"></a>Código do erro

Quando você alcança o limite, recebe o código de status HTTP **429 Excesso de solicitações**. A resposta inclui um valor **Retry-After** , que especifica o número de segundos que o aplicativo deve aguardar (ou dormir) antes de enviar a próxima solicitação. Se você enviar uma solicitação antes do valor de repetição, sua solicitação não será processada e um novo valor de repetição será retornado.

Depois de aguardar o tempo especificado, você também pode fechar e reabrir sua conexão com o Azure. Ao redefinir a conexão, você pode se conectar a uma instância diferente do Azure Resource Manager.

Se você estiver usando um SDK do Azure, o SDK poderá ter uma configuração de repetição automática. Para obter mais informações, consulte [diretrizes de repetição para serviços do Azure](/azure/architecture/best-practices/retry-service-specific).

Alguns provedores de recursos retornam 429 para relatar um problema temporário. O problema pode ser uma condição de sobrecarga que não é causada diretamente por sua solicitação. Ou pode representar um erro temporário sobre o estado do recurso de destino ou do recurso dependente. Por exemplo, o provedor de recursos de rede retorna 429 com o código de erro **RetryableErrorDueToAnotherOperation** quando o recurso de destino está bloqueado por outra operação. Para determinar se o erro é proveniente da limitação ou de uma condição temporária, exiba os detalhes do erro na resposta.

## <a name="remaining-requests"></a>Solicitações restantes

Você pode determinar o número de solicitações restantes ao examinar cabeçalhos de resposta. As solicitações de leitura retornam um valor no cabeçalho para o número de solicitações de leitura restantes. As solicitações de gravação incluem um valor para o número de solicitações de gravação restantes. A tabela a seguir descreve os cabeçalhos de resposta que você pode examinar em busca desses valores:

| Cabeçalho de resposta | Descrição |
| --- | --- |
| x-ms-ratelimit-remaining-subscription-reads |Leituras no escopo da assinatura restantes. Esse valor é retornado em operações de leitura. |
| x-ms-ratelimit-remaining-subscription-writes |Gravações no escopo da assinatura restantes. Esse valor é retornado em operações de gravação. |
| x-ms-ratelimit-remaining-tenant-reads |Leituras no escopo do locatário restantes |
| x-ms-ratelimit-remaining-tenant-writes |Gravações no escopo do locatário restantes |
| x-ms-ratelimit-remaining-subscription-resource-requests |Solicitações de tipo de recurso no escopo da assinatura restantes.<br /><br />Esse valor de cabeçalho só será retornado se um serviço tiver substituído o limite padrão. O Resource Manager adiciona esse valor em vez das leituras ou gravações da assinatura. |
| x-ms-ratelimit-remaining-subscription-resource-entities-read |Solicitações de coletas de tipo de recurso no escopo da assinatura restantes.<br /><br />Esse valor de cabeçalho só será retornado se um serviço tiver substituído o limite padrão. Esse valor fornece o número de solicitações de coleta restantes (listar recursos). |
| x-ms-ratelimit-remaining-tenant-resource-requests |Solicitações de tipo de recurso no escopo do locatário restantes.<br /><br />Esse cabeçalho será adicionado somente para solicitações em nível de locatário e somente se um serviço tiver substituído o limite padrão. O Resource Manager adiciona esse valor em vez das leituras ou gravações do locatário. |
| x-ms-ratelimit-remaining-tenant-resource-entities-read |Solicitações de coletas de tipo de recurso no escopo do locatário restantes.<br /><br />Esse cabeçalho será adicionado somente para solicitações em nível de locatário e somente se um serviço tiver substituído o limite padrão. |

O provedor de recursos também pode retornar cabeçalhos de resposta com informações sobre as solicitações restantes. Para obter informações sobre cabeçalhos de resposta retornados pelo provedor de recursos de computação, consulte [cabeçalhos de resposta informativo de taxa de chamada](/troubleshoot/azure/virtual-machines/troubleshooting-throttling-errors#call-rate-informational-response-headers).

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

Para um exemplo completo do PowerShell, consulte [Verificar os limites do gerenciador de recursos para uma assinatura](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI).

Se você deseja ver as solicitações restantes de depuração, forneça o parâmetro **-Debug** no cmdlet do **PowerShell**.

```powershell
Get-AzResourceGroup -Debug
```

Que retorna muitos valores, incluindo o seguinte valor de resposta:

```output
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

```output
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

```output
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

```output
msrest.http_logger : Response status: 201
msrest.http_logger : Response headers:
msrest.http_logger :     'Cache-Control': 'no-cache'
msrest.http_logger :     'Pragma': 'no-cache'
msrest.http_logger :     'Content-Length': '163'
msrest.http_logger :     'Content-Type': 'application/json; charset=utf-8'
msrest.http_logger :     'Expires': '-1'
msrest.http_logger :     'x-ms-ratelimit-remaining-subscription-writes': '1199'
```

## <a name="next-steps"></a>Próximas etapas

* Para um exemplo completo do PowerShell, consulte [Verificar os limites do gerenciador de recursos para uma assinatura](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI).
* Para obter mais informações sobre limites e cotas, confira [Assinatura do Azure e limite de serviços, cotas e restrições](../../azure-resource-manager/management/azure-subscription-service-limits.md).
* Para saber mais sobre como lidar com solicitações assíncronas de REST, confira [Track asynchronous Azure operations](async-operations.md) (Rastrear operações assíncronas do Azure).