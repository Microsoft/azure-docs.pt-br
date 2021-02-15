---
title: Dimensionamento controlado por eventos no Azure Functions
description: Explica os comportamentos de dimensionamento do plano de consumo e dos aplicativos de função de plano Premium.
ms.date: 10/29/2020
ms.topic: conceptual
ms.service: azure-functions
ms.openlocfilehash: 8aca1ab6629f95ef9162e1247434bd3189d5a7d2
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97937557"
---
# <a name="event-driven-scaling-in-azure-functions"></a>Dimensionamento controlado por eventos no Azure Functions

Nos planos de consumo e Premium, Azure Functions dimensiona os recursos de CPU e memória adicionando instâncias adicionais do host do functions. O número de instâncias é determinado no número de eventos que disparam uma função. 

Cada instância do host do Functions no plano de consumo é limitada a 1,5 GB de memória e uma CPU.  Uma instância do host é o aplicativo de funções inteiro, o que significa que todas as funções em um aplicativo de funções compartilham recursos dentro de uma instância e são dimensionadas ao mesmo tempo. Aplicativos de funções que compartilham a mesma escala de plano de consumo de forma independente.  No plano Premium, o tamanho do plano determina a memória disponível e a CPU para todos os aplicativos nesse plano nessa instância.  

Os arquivos de código de função são armazenados em compartilhamentos de arquivos do Azure na conta de armazenamento principal da função. Quando você exclui a conta de armazenamento principal do aplicativo de funções, os arquivos de código de função são excluídos e não podem ser recuperados.

## <a name="runtime-scaling"></a>Escalonamento de runtime

O Azure Functions usa um componente chamado *controlador de escala* para monitorar a taxa de eventos e determinar se deve aumentar ou reduzir. O controlador de escala usa heurística para cada tipo de gatilho. Por exemplo, ao usar um gatilho do armazenamento de Filas do Azure, ele escala com base no tamanho da fila e na idade da mensagem em fila mais antiga.

A unidade de escala para Azure Functions é o aplicativo de funções. Quando o aplicativo de funções é dimensionado na horizontal, mais recursos são alocados para executar várias instâncias do host do Azure Functions. Em contrapartida, quando a demanda por computação é reduzida, o controlador de escala remove as instâncias do host de função. O número de instâncias é eventualmente "reduzido em" para zero quando nenhuma função está sendo executada em um aplicativo de funções.

![Controlador de escala monitorando eventos e criando instâncias](./media/functions-scale/central-listener.png)

## <a name="cold-start"></a>Inicialização a frio

Depois que seu aplicativo de funções ficar ocioso por alguns minutos, a plataforma poderá dimensionar o número de instâncias nas quais seu aplicativo é executado até zero. A próxima solicitação tem a latência adicional de dimensionamento de zero para um. Essa latência é conhecida como uma _inicialização a frio_. O número de dependências exigidas por seu aplicativo de funções pode afetar a hora de início frio. A inicialização a frio é mais um problema para operações síncronas, como gatilhos HTTP que devem retornar uma resposta. Se o Cold for iniciado, afetará suas funções, considere a execução em um plano Premium ou em um plano dedicado com a configuração **Always on** habilitada.   

## <a name="understanding-scaling-behaviors"></a>Noções básicas dos comportamentos de dimensionamento

O dimensionamento pode variar em uma série de fatores e ser diferente com base no gatilho e na linguagem selecionada. Há algumas complexidades de comportamentos de dimensionamento a serem considerados:

* **Máximo de instâncias:** Um único aplicativo de funções só pode ser dimensionado para um máximo de 200 instâncias. Uma única instância pode processar mais de uma mensagem ou solicitação por vez, portanto, não há um limite definido de número de execuções simultâneas.  Você pode [especificar um máximo inferior](#limit-scale-out) para limitar a escala, conforme necessário.
* **Nova taxa de instância:** Para gatilhos HTTP, novas instâncias são alocadas, no máximo, uma vez por segundo. Para gatilhos não HTTP, novas instâncias são alocadas, no máximo, uma vez a cada 30 segundos. O dimensionamento é mais rápido quando executado em um [plano Premium](functions-premium-plan.md).
* **Eficiência da escala:** Para gatilhos do barramento de serviço, use _gerenciar_ direitos em recursos para o dimensionamento mais eficiente. Com os direitos de _escuta_ , o dimensionamento não é tão preciso porque o comprimento da fila não pode ser usado para informar decisões de dimensionamento. Para saber mais sobre como definir direitos nas políticas de acesso do barramento de serviço, consulte [política de autorização de acesso compartilhado](../service-bus-messaging/service-bus-sas.md#shared-access-authorization-policies). Para gatilhos do hub de eventos, consulte as [diretrizes de dimensionamento](functions-bindings-event-hubs-trigger.md#scaling) no artigo de referência. 

## <a name="limit-scale-out"></a>Limitar escala horizontal

Talvez você queira restringir o número máximo de instâncias que um aplicativo usou para escalar horizontalmente.  Isso é mais comum para casos em que um componente downstream como um banco de dados tem taxa de transferência limitada.  Por padrão, as funções do plano de consumo são escaladas horizontalmente para até 200 instâncias, e as funções de plano Premium serão expandidas para tantas de 100 instâncias.  Você pode especificar um máximo inferior para um aplicativo específico modificando o `functionAppScaleLimit` valor.  O `functionAppScaleLimit` pode ser definido como `0` ou `null` para irrestrito ou um valor válido entre `1` e o máximo do aplicativo.

```azurecli
az resource update --resource-type Microsoft.Web/sites -g <RESOURCE_GROUP> -n <FUNCTION_APP-NAME>/config/web --set properties.functionAppScaleLimit=<SCALE_LIMIT>
```

## <a name="best-practices-and-patterns-for-scalable-apps"></a>Melhores práticas e padrões para aplicativos escalonáveis

Há muitos aspectos de um aplicativo de funções que impactam como ele é dimensionado, incluindo a configuração do host, o espaço de tempo de execução e a eficiência dos recursos.  Para obter mais informações, consulte a [seção de escalabilidade do artigo sobre considerações de desempenho](functions-best-practices.md#scalability-best-practices). Adicionalmente, é necessário que você saiba como as conexões se comportam na medida em que o aplicativo de funções é dimensionado. Para saber mais, confira [Como gerenciar conexões no Azure Functions](manage-connections.md).

Para obter mais informações sobre o dimensionamento em Python e Node.js, consulte [Azure Functions guia do desenvolvedor do Python – dimensionamento e simultaneidade](functions-reference-python.md#scaling-and-performance) e [Azure Functions Node.js guia do desenvolvedor – dimensionamento e simultaneidade](functions-reference-node.md#scaling-and-concurrency).

## <a name="billing-model"></a>Modelo de cobrança

A cobrança pelos diferentes planos é descrita em detalhes na [página de preços de Azure Functions](https://azure.microsoft.com/pricing/details/functions/). O uso é agregado no nível do aplicativo de funções e conta apenas o tempo durante o qual o código de função é executado. Veja a seguir as unidades de cobrança:

* **Consumo de recursos em GB/s (gigabyte por segundo)**. Calculado como uma combinação do tamanho da memória e o tempo de execução para todas as funções dentro de um aplicativo de Funções. 
* **Execuções**. Contadas toda vez que uma função é executada em resposta a um gatilho de evento.

Consultas e informações úteis sobre como entender sua fatura de consumo podem ser encontradas [nas perguntas frequentes sobre cobrança](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ).

[Azure Functions pricing page]: https://azure.microsoft.com/pricing/details/functions

## <a name="next-steps"></a>Próximas etapas

+ [Opções de Hospedagem de Azure Functions](functions-scale.md)

