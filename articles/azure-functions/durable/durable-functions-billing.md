---
title: Cobrança das Durable Functions – Azure Functions
description: Saiba mais sobre os comportamentos internos das Durable Functions e como eles afetam a cobrança do Azure Functions.
author: cgillum
ms.topic: overview
ms.date: 08/31/2019
ms.author: azfuncdf
ms.openlocfilehash: 2ec1b080c195a47caafd0120240b5fb61ede062b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97932275"
---
# <a name="durable-functions-billing"></a>Cobrança das Durable Functions

As [Durable Functions](durable-functions-overview.md) são cobradas da mesma forma que o Azure Functions. Para saber mais, confira [Preços do Azure Functions](https://azure.microsoft.com/pricing/details/functions/).

Ao executar funções de orquestrador no [plano de Consumo](../consumption-plan.md) do Azure Functions, é necessário ter conhecimento de alguns comportamentos de cobrança. As seções a seguir descrevem esses comportamentos e seu efeito mais detalhadamente.

## <a name="orchestrator-function-replay-billing"></a>Cobrança de reprodução da função funções de orquestrador

As [funções de orquestrador](durable-functions-orchestrations.md) podem ser reproduzidas várias vezes durante o tempo de vida de uma orquestração. Cada reprodução é exibida pelo Azure Functions Runtime como uma invocação de função distinta. Por esse motivo, no plano de Consumo do Azure Functions, você será cobrado por cada reprodução da função de orquestrador. Outros tipos de plano não são cobrados pela reprodução da função do orquestrador.

## <a name="awaiting-and-yielding-in-orchestrator-functions"></a>Aguardar e gerar em funções de orquestrador

Quando uma função de orquestrador aguarda a conclusão de uma ação assíncrona usando **await** em C# ou **yield** em JavaScript, o runtime considera essa execução específica concluída. A cobrança da função de orquestrador é interrompida nesse momento. Ele não é retomado até a próxima reprodução da função de orquestrador. Você não é cobrado por nenhum tempo gasto esperando ou gerando em uma função funções de orquestrador.

> [!NOTE]
> As funções que chamam outras funções são consideradas um antipadrão por algumas pessoas. Isso ocorre devido a um problema conhecido como _cobrança dupla_. Quando uma função chama outra função diretamente, as duas são executadas ao mesmo tempo. A função chamada está executando ativamente o código, enquanto a função chamadora está aguardando uma resposta. Nesse caso, é necessário pagar pelo tempo que a função chamadora gasta aguardando a execução da função chamada.
>
> Não há dupla cobrança em funções de orquestrador. Uma cobrança da função de orquestrador é interrompida enquanto aguarda o resultado de uma função de atividade ou suborquestração.

## <a name="durable-http-polling"></a>Sondagem HTTP durável

As funções de orquestrador podem realizar chamadas HTTP de longa execução a pontos de extremidade externos, conforme descrito no [artigo recursos HTTP](durable-functions-http-features.md). O método **CallHttpAsync** em C# e o método **callHttp** em JavaScript pode sondar internamente um ponto de extremidade HTTP ao seguir o [padrão assíncrono 202](durable-functions-http-features.md#http-202-handling).

No momento, não há cobrança direta para operações internas de sondagem HTTP. No entanto, a sondagem interna pode fazer a função de orquestrador ser reproduzida periodicamente. Você será cobrado pelas cobranças padrão dessas repetições de função internas.

## <a name="azure-storage-transactions"></a>Transações do Armazenamento do Microsoft Azure

As Durable Functions usam o Armazenamento do Azure por padrão para manter o estado persistente, processar mensagens e gerenciar partições por meio de concessões de blob. Como você é proprietário desta conta de armazenamento, os custos de transação são cobrados de sua assinatura do Azure. Para obter mais informações sobre os artefatos do Armazenamento do Azure usados pelas Durable Functions, confira o [artigo Hubs de tarefas](durable-functions-task-hubs.md).

Vários fatores contribuem para os custos reais do Armazenamento do Azure incorridos pelo seu aplicativo Durable Functions:

* Um único aplicativo de funções é associado a um único hub de tarefas, que compartilha um conjunto de recursos do Armazenamento do Azure. Esses recursos são usados por todas as Durable Functions em um aplicativo de funções. O número real de funções no aplicativo de funções não tem efeito sobre os custos de transação do Armazenamento do Azure.
* Cada instância do aplicativo de funções sonda internamente várias filas na conta de armazenamento usando um algoritmo de sondagem de retirada exponencial. Uma instância de aplicativo ociosa sonda as filas com menos frequência do que um aplicativo ativo, o que resulta em menos custos de transação. Para obter mais informações sobre o comportamento de sondagem de fila das Durable Functions, confira a [seção de sondagem de fila do artigo Desempenho e Escala](durable-functions-perf-and-scale.md#queue-polling).
* Ao executar nos planos Consumo ou Premium do Azure Functions, o [controlador de escala do Azure Functions](../event-driven-scaling.md) sonda regularmente todas as filas do hub de tarefas em segundo plano. Se um aplicativo de funções estiver em escala leve a moderada, apenas uma instância do controlador de escala única sondará essas filas. Se o aplicativo de funções for expandido para um grande número de instâncias, mais instâncias de controlador de escala poderão ser adicionadas. Essas instâncias adicionais do controlador de escala podem aumentar os custos totais da transação de fila.
* Cada instância do aplicativo de funções compete para um conjunto de concessões de blob. Essas instâncias farão chamadas periodicamente para o serviço Blob do Azure para renovar as concessões mantidas ou tentar adquirir novas concessões. A contagem de partições configurada do hub de tarefas determina o número de concessões de blob. Expandir para um número maior de instâncias do aplicativo de funções provavelmente aumenta os custos de transação do Armazenamento do Azure associados a essas operações de concessão.

Você pode encontrar mais informações sobre os preços do Armazenamento do Azure na documentação de [preços do Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/). 

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba mais sobre os preços do Azure Functions](https://azure.microsoft.com/pricing/details/functions/)
