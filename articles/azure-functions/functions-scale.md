---
title: Escala e hospedagem no Azure Functions | Microsoft Docs
description: Saiba como escolher entre Azure Functions plano de consumo e o plano Premium.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: Azure functions, funções, plano de consumo, plano Premium, processamento de eventos, WebHooks, computação dinâmica, arquitetura sem servidor
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 03/27/2019
ms.author: glenga
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 94ef85836ef524b34cd1c51e4eda83695bc70507
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443946"
---
# <a name="azure-functions-scale-and-hosting"></a>Escala e hospedagem no Azure Functions

Ao criar um aplicativo de funções no Azure, você deve escolher um plano de hospedagem para seu aplicativo. Há três planos de hospedagem disponíveis para Azure Functions: Plano de [consumo](#consumption-plan), [plano Premium](#premium-plan)e [plano do serviço de aplicativo](#app-service-plan).

O plano de hospedagem que você escolher ditará os seguintes comportamentos:

* Como seu aplicativo de funções é dimensionado.
* Os recursos disponíveis para cada instância do aplicativo de funções.
* Suporte para recursos avançados, como conectividade VNET.

Os planos de consumo e Premium adicionam capacidade de computação automaticamente quando seu código está em execução. Seu aplicativo é escalado horizontalmente quando necessário para lidar com a carga e diminuído verticalmente quando o código para de ser executado. Para o plano de consumo, você também não precisa pagar por VMs ociosas ou reservar a capacidade antecipadamente.  

O plano Premium fornece recursos adicionais, como instâncias de computação Premium, a capacidade de manter instâncias quentes indefinidamente e conectividade VNet.

O plano do serviço de aplicativo permite que você aproveite a infraestrutura dedicada, que você gerencia. Seu aplicativo de funções não é dimensionado com base em eventos, o que significa que nunca é dimensionado para zero. (Requer que o [Always on](#always-on) esteja habilitado.)

> [!NOTE]
> Você pode alternar entre consumo e planos Premium alterando a propriedade Plan do recurso de aplicativo de funções.

## <a name="hosting-plan-support"></a>Suporte ao plano de hospedagem

O suporte a recursos se enquadra nas duas categorias a seguir:

* Disponibilidade _geral (GA)_ : suporte completo e aprovado para uso em produção.
* Versão _prévia_: ainda não há suporte total e aprovado para uso em produção.

A tabela a seguir indica o nível de suporte atual para os três planos de hospedagem, quando executado no Windows ou no Linux:

| | Plano de consumo | Plano Premium | Plano dedicado |
|-|:----------------:|:------------:|:----------------:|
| Windows | GA | visualização | GA |
| Linux | visualização | visualização | GA |

## <a name="consumption-plan"></a>Plano de consumo

Quando você estiver usando o plano de consumo, as instâncias do host Azure Functions serão adicionadas e removidas dinamicamente com base no número de eventos de entrada. Esse plano sem servidor escala automaticamente, e você é cobrado pelos recursos de computação apenas durante a execução de suas funções. Em um plano de consumo, a execução de uma função expire após um período configurável.

A cobrança baseia-se no número de execuções, no tempo de execução e na memória usada. A cobrança é agregada entre todas as funções em um aplicativo de funções. Para saber mais, confira a [página de preços do Azure Functions](https://azure.microsoft.com/pricing/details/functions/).

O plano de Consumo é o plano de hospedagem padrão e oferece os seguintes benefícios:

* Pague apenas quando suas funções forem executadas
* Escale horizontalmente de forma automática, mesmo durante períodos de carga alta

Os aplicativos de funções na mesma região podem ser atribuídos ao mesmo plano de consumo. Não há nenhuma desvantagem ou impacto para ter vários aplicativos em execução no mesmo plano de consumo. A atribuição de vários aplicativos ao mesmo plano de consumo não afeta a resiliência, a escalabilidade ou a confiabilidade de cada aplicativo.

## <a name="premium-plan"></a>Plano Premium (visualização)

Quando você estiver usando o plano Premium, as instâncias do host Azure Functions serão adicionadas e removidas com base no número de eventos de entrada, assim como o plano de consumo.  O plano Premium dá suporte aos seguintes recursos:

* Instâncias passivas perpétuas para evitar qualquer início frio
* Conectividade de VNet
* Duração de execução ilimitada
* Tamanhos de instância Premium (um núcleo, duas principais e quatro instâncias de núcleo)
* Preços mais previsíveis
* Alocação de aplicativo de alta densidade para planos com vários aplicativos de funções

Informações sobre como você pode configurar essas opções podem ser encontradas no [documento Azure Functions plano Premium](functions-premium-plan.md).

Em vez de cobrança por execução e memória consumida, a cobrança para o plano Premium baseia-se no número de segundos de núcleo, tempo de execução e memória usados nas instâncias necessárias e reservadas.  Pelo menos uma instância deve estar sempre em espera. Isso significa que há um custo mensal fixo por plano ativo, independentemente do número de execuções.

Considere o plano Azure Functions Premium nas seguintes situações:

* Os aplicativos de funções executam continuamente ou quase continuamente.
* Você precisa de mais opções de CPU ou memória do que o fornecido pelo plano de consumo.
* Seu código precisa ser executado por mais tempo do que o [máximo permitido](#timeout) no plano de consumo.
* Você precisa de recursos que estão disponíveis apenas em um plano Premium, como conectividade VNET/VPN.

Ao executar funções de JavaScript em um plano Premium, você deve escolher uma instância que tenha menos vCPUs. Para obter mais informações, consulte [escolher planos Premium de núcleo único](functions-reference-node.md#considerations-for-javascript-functions).  

## <a name="app-service-plan"></a>Plano dedicado (serviço de aplicativo)

Seus aplicativos de funções também podem ser executados nas mesmas VMs dedicadas que outros aplicativos do serviço de aplicativo (SKUs Basic, Standard, Premium e Isolated).

Considere um plano do serviço de aplicativo nas seguintes situações:

* Você tem VMs subutilizadas que já estão executando outras instâncias do Serviço de Aplicativo.
* Você deseja fornecer uma imagem personalizada na qual executar suas funções.

Você paga o mesmo para aplicativos de funções em um plano do serviço de aplicativo como faria para outros recursos do serviço de aplicativo, como aplicativos Web. Para obter detalhes sobre como o plano do Serviço de Aplicativo funciona, consulte [Visão geral detalhada de planos de Serviço de Aplicativo do Azure](../app-service/overview-hosting-plans.md).

Com um plano do serviço de aplicativo, você pode escalar horizontalmente manualmente Adicionando mais instâncias de VM. Você também pode habilitar o dimensionamento automático. Para saber mais, confira [Dimensionar a contagem de instâncias manual ou automaticamente](../azure-monitor/platform/autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json). Você também pode escalar verticalmente escolhendo um plano do Serviço de Aplicativo diferente. Para obter mais informações, consulte [Escalar verticalmente um aplicativo no Azure](../app-service/web-sites-scale.md). 

Ao executar funções JavaScript em um plano do Serviço de Aplicativo, você deve escolher um plano que tenha menos vCPUs. Para obter mais informações, consulte [escolher planos de serviço de aplicativo de núcleo único](functions-reference-node.md#choose-single-vcpu-app-service-plans). 
<!-- Note: the portal links to this section via fwlink https://go.microsoft.com/fwlink/?linkid=830855 --> 

### <a name="always-on"></a>Always On

Se executar em um plano do Serviço de Aplicativo, você deverá habilitar a configuração **Always On** para que o aplicativo de funções execute corretamente. Em um plano do Serviço de Aplicativo, o tempo de execução das funções ficará ocioso após alguns minutos de inatividade, portanto, apenas gatilhos HTTP "despertarão" suas funções. Always On está disponível apenas em um plano de Serviço de Aplicativo. Em um plano de Consumo, a plataforma ativa automaticamente os aplicativos de função.

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]


Mesmo com Always On habilitado, o tempo limite de execução para funções individuais é controlado pela configuração `functionTimeout` no arquivo de projeto [host.json](functions-host-json.md#functiontimeout).

## <a name="determine-the-hosting-plan-of-an-existing-application"></a>Determinar o plano de Hospedagem de um aplicativo existente

Para determinar o plano de hospedagem usado pelo aplicativo de funções, consulte **plano do Serviço de Aplicativo/tipo de preço** na guia **Visão geral** do aplicativo de funções no [portal do Azure](https://portal.azure.com). Para planos do Serviço de Aplicativo, o tipo de preço também é indicado.

![Exibir o plano de dimensionamento no portal](./media/functions-scale/function-app-overview-portal.png)

Também é possível usar a CLI do Azure para determinar o plano, da seguinte maneira:

```azurecli-interactive
appServicePlanId=$(az functionapp show --name <my_function_app_name> --resource-group <my_resource_group> --query appServicePlanId --output tsv)
az appservice plan list --query "[?id=='$appServicePlanId'].sku.tier" --output tsv
```  

Quando a saída desse comando for `dynamic`, o aplicativo de funções estará no plano de Consumo. Quando a saída desse comando for `ElasticPremium`, seu aplicativo de funções estará no plano Premium. Todos os outros valores indicam diferentes camadas de um plano do serviço de aplicativo.

## <a name="storage-account-requirements"></a>Requisitos da conta de armazenamento

Em qualquer plano, um aplicativo de funções requer uma conta de armazenamento do Azure geral, que dá suporte ao blob do Azure, à fila, aos arquivos e ao armazenamento de tabelas. Isso ocorre porque as funções dependem do armazenamento do Azure para operações como gerenciar gatilhos e registrar execuções de função, mas algumas contas de armazenamento não dão suporte a filas e tabelas. Essas contas, que incluem contas de armazenamento somente blob (incluindo armazenamento Premium) e contas de armazenamento para uso geral com replicação de armazenamento com redundância de zona, são filtradas das seleções da **Conta de Armazenamento** existente quando você cria um aplicativo de funções.

<!-- JH: Does using a Premium Storage account improve perf? -->

Para saber mais sobre os tipos de conta de armazenamento, consulte [Apresentando os serviços de armazenamento do Azure](../storage/common/storage-introduction.md#azure-storage-services).

## <a name="how-the-consumption-and-premium-plans-work"></a>Como funcionam os planos de consumo e Premium

Nos planos de consumo e Premium, a infraestrutura de Azure Functions escala recursos de CPU e memória adicionando instâncias adicionais do host do functions, com base no número de eventos em que suas funções são disparadas. Cada instância do host do Functions no plano de consumo é limitada a 1,5 GB de memória e uma CPU.  Uma instância do host é o aplicativo de funções inteiro, o que significa que todas as funções em um aplicativo de funções compartilham recursos dentro de uma instância e são dimensionadas ao mesmo tempo. Os aplicativos de funções que compartilham o mesmo plano de consumo são dimensionados de forma independente.  No plano Premium, o tamanho do plano determinará a memória disponível e a CPU para todos os aplicativos nesse plano nessa instância.  

Os arquivos de código de função são armazenados em compartilhamentos de arquivos do Azure na conta de armazenamento principal da função. Quando você exclui a conta de armazenamento principal do aplicativo de funções, os arquivos de código de função são excluídos e não podem ser recuperados.

> [!NOTE]
> Quando estiver usando um gatilho de blob em um plano de consumo, poderá haver um atraso de até 10 minutos no processamento de novos blobs. Esse atraso ocorre quando um aplicativo de funções torna-se ocioso. Depois que o aplicativo de funções estiver em execução, os blobs serão processados imediatamente. Para evitar esse atraso de inicialização a frio, use o plano Premium ou use o [gatilho de grade de eventos](functions-bindings-event-grid.md). Para obter mais informações, consulte [o artigo de referência de associação de gatilho de blob](functions-bindings-storage-blob.md#trigger).

### <a name="runtime-scaling"></a>Escalonamento de tempo de execução

O Azure Functions usa um componente chamado *controlador de escala* para monitorar a taxa de eventos e determinar se deve aumentar ou reduzir. O controlador de escala usa heurística para cada tipo de gatilho. Por exemplo, ao usar um gatilho do armazenamento de Filas do Azure, ele escala com base no tamanho da fila e na idade da mensagem em fila mais antiga.

A unidade de escala para Azure Functions é o aplicativo de funções. Quando o aplicativo de funções é dimensionado na horizontal, mais recursos são alocados para executar várias instâncias do host do Azure Functions. Em contrapartida, quando a demanda por computação é reduzida, o controlador de escala remove as instâncias do host de função. O número de instâncias é eventualmente reduzido a zero quando nenhuma função está em execução em um aplicativo de funções.

![Controlador de escala monitorando eventos e criando instâncias](./media/functions-scale/central-listener.png)

### <a name="understanding-scaling-behaviors"></a>Noções básicas dos comportamentos de dimensionamento

O dimensionamento pode variar em uma série de fatores e ser diferente com base no gatilho e na linguagem selecionada. Há algumas complexidades de comportamentos de dimensionamento a serem considerados:

* Um único aplicativo de funções será escalado verticalmente somente para um máximo de 200 instâncias. Uma única instância pode processar mais de uma mensagem ou solicitação por vez, portanto, não há um limite definido de número de execuções simultâneas.
* Para gatilhos HTTP, novas instâncias só serão alocadas no máximo uma vez a cada 1 segundo.
* Para gatilhos não HTTP, novas instâncias só serão alocadas no máximo uma vez a cada 30 segundos.

Gatilhos diferentes também podem ter diferentes limites de dimensionamento, como documentado abaixo:

* [Hub de Evento](functions-bindings-event-hubs.md#trigger---scaling)

### <a name="best-practices-and-patterns-for-scalable-apps"></a>Melhores práticas e padrões para aplicativos escalonáveis

Há muitos aspectos de um aplicativo de funções que afetarão a qualidade da escala, incluindo a configuração do host, o espaço de tempo de execução e a eficiência dos recursos.  Para obter mais informações, consulte a [seção de escalabilidade do artigo sobre considerações de desempenho](functions-best-practices.md#scalability-best-practices). Adicionalmente, é necessário que você saiba como as conexões se comportam na medida em que o aplicativo de funções é dimensionado. Para saber mais, confira [Como gerenciar conexões no Azure Functions](manage-connections.md).

### <a name="billing-model"></a>Modelo de cobrança

A cobrança pelos diferentes planos é descrita em detalhes na [página de preços de Azure Functions](https://azure.microsoft.com/pricing/details/functions/). O uso é agregado no nível do aplicativo de funções e conta apenas o tempo durante o qual o código de função é executado. Veja a seguir as unidades de cobrança:

* **Consumo de recursos em GB/s (gigabyte por segundo)** . Calculado como uma combinação do tamanho da memória e o tempo de execução para todas as funções dentro de um aplicativo de Funções. 
* **Execuções**. Contadas toda vez que uma função é executada em resposta a um gatilho de evento.

Consultas e informações úteis sobre como entender sua fatura de consumo podem ser encontradas [nas perguntas frequentes sobre cobrança](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ).

[Azure Functions pricing page]: https://azure.microsoft.com/pricing/details/functions

## <a name="service-limits"></a>Limites de serviço

A tabela a seguir indica os limites que se aplicam a aplicativos de funções ao serem executados em vários planos de hospedagem:

[!INCLUDE [functions-limits](../../includes/functions-limits.md)]
