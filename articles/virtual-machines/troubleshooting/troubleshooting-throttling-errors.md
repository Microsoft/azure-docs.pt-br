---
title: Solução de problemas de erros de limitação no Azure | Microsoft Docs
description: Erros de limitação, novas tentativas e retiradas na Computação do Azure.
services: virtual-machines
documentationcenter: ''
author: changov
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.service: virtual-machines
ms.devlang: na
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: changov
ms.reviewer: vashan, rajraj
ms.openlocfilehash: 6ae14edb7fa6b44f7c3bb961ffbcceb26eb9dee3
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67875454"
---
# <a name="troubleshooting-api-throttling-errors"></a>Solução de problemas de erros de limitação de API 

Solicitações de computação do Azure podem ser limitadas a uma assinatura e por região para ajudar no desempenho geral do serviço. Garantimos que todas as chamadas para o CRP (Provedor de Recursos de Computação) do Azure, que gerencia os recursos no namespace Microsoft.Compute, não excederão a taxa máxima de solicitação de API permitida. Este documento descreve a limitação de API, detalhes de como solucionar problemas de limitação e melhores práticas para evitar limitação.  

## <a name="throttling-by-azure-resource-manager-vs-resource-providers"></a>Limitação pelo Azure Resource Manager vs. provedores de recursos  

Como a porta de entrada para o Azure, o Azure Resource Manager faz a validação de autenticação e de primeira ordem e a limitação de todas as solicitações de API recebidas. Os limites de taxa de chamada do Azure Resource Manager e os cabeçalhos HTTP de resposta de diagnóstico relacionados são descritos [aqui](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-request-limits).
 
Quando um cliente da API do Azure recebe um erro de limitação, o status HTTP é 429 Solicitações Demais. Para entender se a limitação da solicitação é feita pelo Azure Resource Manager ou um provedor de recursos subjacente, como CRP, inspecione `x-ms-ratelimit-remaining-subscription-reads` quanto a solicitações GET e os cabeçalhos de resposta `x-ms-ratelimit-remaining-subscription-writes` quanto a solicitações não GET. Se a contagem de chamada restante está se aproximando de 0, o limite de chamada geral da assinatura definido pelo Azure Resource Manager foi atingido. As atividades de todos os clientes de assinatura são contadas juntas. Caso contrário, a limitação é proveniente de provedor de recursos de destino (aquele tratado pelo segmento `/providers/<RP>` da URL da solicitação). 

## <a name="call-rate-informational-response-headers"></a>Cabeçalhos de resposta informativa de taxa de chamada 

| Cabeçalho                            | Formato de valor                           | Exemplo                               | DESCRIÇÃO                                                                                                                                                                                               |
|-----------------------------------|----------------------------------------|---------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| x-ms-ratelimit-remaining-resource |```<source RP>/<policy or bucket>;<count>```| Microsoft.Compute/HighCostGet3Min;159 | Contagem de chamadas à API restante para a política de limitação abrangendo o grupo de operações ou o bucket de recursos, incluindo o destino dessa solicitação                                                                   |
| x-ms-request-charge               | ```<count>```                             | 1                                     | O número de contagens de chamadas "cobradas" para essa solicitação HTTP para o limite da política aplicável. Geralmente é 1. Solicitações em lote, por exemplo, para dimensionar um conjunto de dimensionamento de máquinas virtuais, podem cobrar várias contagens. |


Observe que uma solicitação de API pode estar sujeita a várias políticas de limitação. Haverá um cabeçalho `x-ms-ratelimit-remaining-resource` separado para cada política. 

Este é um exemplo de resposta para excluir uma solicitação de conjunto de dimensionamento de máquinas virtuais.

```
x-ms-ratelimit-remaining-resource: Microsoft.Compute/DeleteVMScaleSet3Min;107 
x-ms-ratelimit-remaining-resource: Microsoft.Compute/DeleteVMScaleSet30Min;587 
x-ms-ratelimit-remaining-resource: Microsoft.Compute/VMScaleSetBatchedVMRequests5Min;3704 
x-ms-ratelimit-remaining-resource: Microsoft.Compute/VmssQueuedVMOperations;4720 
```

## <a name="throttling-error-details"></a>Detalhes do erro de limitação

O status HTTP 429 é comumente usado para rejeitar uma solicitação porque um limite de taxa de chamada é atingido. Uma resposta típica de erro de limitação do Provedor de Recursos de Computação se parecerá com o exemplo a seguir (somente os cabeçalhos pertinentes são mostrados):

```
HTTP/1.1 429 Too Many Requests
x-ms-ratelimit-remaining-resource: Microsoft.Compute/HighCostGet3Min;46
x-ms-ratelimit-remaining-resource: Microsoft.Compute/HighCostGet30Min;0
Retry-After: 1200
Content-Type: application/json; charset=utf-8
{
  "code": "OperationNotAllowed",
  "message": "The server rejected the request because too many requests have been received for this subscription.",
  "details": [
    {
      "code": "TooManyRequests",
      "target": "HighCostGet30Min",
      "message": "{\"operationGroup\":\"HighCostGet30Min\",\"startTime\":\"2018-06-29T19:54:21.0914017+00:00\",\"endTime\":\"2018-06-29T20:14:21.0914017+00:00\",\"allowedRequestCount\":800,\"measuredRequestCount\":1238}"
    }
  ]
}

```

A política com a contagem de chamada restante de 0 é a responsável pelo retorno do erro de limitação. Neste caso, é `HighCostGet30Min`. O formato geral do corpo da resposta é o formato de erro geral da API do Azure Resource Manager (compatível com OData). O código de erro principal, `OperationNotAllowed`, é aquele que o Provedor de Recursos de Computação usa para relatar erros de limitação (entre outros tipos de erros de cliente). A propriedade `message` dos erros internos contém uma estrutura JSON serializada com os detalhes da violação de limitação.

Conforme ilustrado acima, todos os erros de limitação incluem o cabeçalho `Retry-After`, que fornece o número mínimo de segundos que o cliente deve aguardar antes de repetir a solicitação. 

## <a name="api-call-rate-and-throttling-error-analyzer"></a>Taxa de chamada da API e analisador de erros de limitação
Uma versão de visualização de um recurso de solução de problemas está disponível para a API do provedor de recursos de computação. Esses cmdlets do PowerShell fornecem estatísticas sobre a taxa de solicitações da API por intervalo de tempo por operação e as violações de limitação por grupo de operações (política):
-   [Export-AzLogAnalyticRequestRateByInterval](https://docs.microsoft.com/powershell/module/az.compute/export-azloganalyticrequestratebyinterval)
-   [Export-AzLogAnalyticThrottledRequest](https://docs.microsoft.com/powershell/module/az.compute/export-azloganalyticthrottledrequest)

As estatísticas de chamadas da API podem fornecer uma boa visão do comportamento do (s) cliente (s) de uma assinatura e permitir a fácil identificação de padrões de chamadas que causam limitação.

Uma limitação do analisador, por enquanto, é que ele não conta solicitações de tipos de recurso de disco e de instantâneo (em suporte a discos gerenciados). Como coleta dados da telemetria do CRP, também não pode ajudar na identificação de erros de limitação do ARM. Mas esses podem ser identificados facilmente com base nos cabeçalhos de resposta ARM distintos, conforme discutido anteriormente.

Os cmdlets do PowerShell usam uma API de serviço REST, que pode ser facilmente chamada diretamente pelos clientes (embora sem suporte formal ainda). Para ver o formato da solicitação HTTP, execute os cmdlets com - opção de depuração ou snoop em sua execução com o Fiddler.


## <a name="best-practices"></a>Práticas recomendadas 

- Não repita erros de API do serviço do Azure incondicionalmente e/ou imediatamente. Uma ocorrência comum é o código do cliente entrar em um rápido loop de repetição ao encontrar um erro que não é passível de repetição. As repetições acabarão esgotando o limite de chamada permitido para o grupo de operação de destino e afetarão outros clientes da assinatura. 
- Em casos de automação de API de alto volume, considere implementar autolimitação proativa no lado do cliente quando a contagem de chamadas disponíveis para um grupo de operações de destino cair abaixo de algum limite baixo. 
- Ao acompanhar operações assíncronas, respeite as dicas de cabeçalho Retry-After. 
- Se o código do cliente precisar obter informações sobre uma Máquina Virtual específica, consulte essa VM diretamente, em vez de listar todas as VMs no grupo de recursos contentor ou toda a assinatura para então escolher a VM necessária no lado do cliente. 
- Se o código de cliente precisar de VMs, discos e instantâneos de um local específico do Azure, use o formulário baseado em localização da consulta, em vez de consultar todas as VMs da assinatura para então filtrar por local no lado do cliente: `GET /subscriptions/<subId>/providers/Microsoft.Compute/locations/<location>/virtualMachines?api-version=2017-03-30` consulta pontos de extremidade regionais do Provedor de Recursos de Computação. 
-   Ao criar ou atualizar recursos de API em particular, VMs e conjuntos de dimensionamento de máquinas virtuais, é muito mais eficiente rastrear a operação assíncrona retornada para a conclusão do que fazer a sondagem na URL de recurso em si (com base no `provisioningState`).

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações orientação de novas tentativas para outros serviços no Azure, veja [Orientação de novas tentativas para serviços específicos](https://docs.microsoft.com/azure/architecture/best-practices/retry-service-specific)
