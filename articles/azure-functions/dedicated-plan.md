---
title: Azure Functions hospedagem dedicada
description: Saiba mais sobre os benefícios da execução de Azure Functions em um plano de hospedagem do serviço de aplicativo dedicado.
ms.topic: conceptual
ms.date: 10/29/2020
ms.openlocfilehash: 0ebf83aa919d91f161b247539ae20873242a8ed8
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97937549"
---
# <a name="dedicated-hosting-plans-for-azure-functions"></a>Planos de hospedagem dedicados para Azure Functions

Este artigo é sobre a hospedagem de seu aplicativo de funções em um plano do serviço de aplicativo, incluindo em um Ambiente do Serviço de Aplicativo (ASE). Para outras opções de hospedagem, consulte o [artigo plano de hospedagem](functions-scale.md).

Um plano de serviço de aplicativo define um conjunto de recursos de computação para um aplicativo ser executado. Esses recursos de computação são análogos ao [_farm de servidores_](https://wikipedia.org/wiki/Server_farm) na hospedagem convencional. Um ou mais aplicativos de funções podem ser configurados para serem executados nos mesmos recursos de computação (plano do serviço de aplicativo) que outros aplicativos do serviço de aplicativo, como aplicativos Web. Esses planos incluem SKUs Basic, Standard, Premium e Isolated. Para obter detalhes sobre como o plano do Serviço de Aplicativo funciona, consulte [Visão geral detalhada de planos de Serviço de Aplicativo do Azure](../app-service/overview-hosting-plans.md).

Considere um plano do serviço de aplicativo nas seguintes situações:

* Você tem VMs subutilizadas que já estão executando outras instâncias do Serviço de Aplicativo.
* Você deseja fornecer uma imagem personalizada na qual executar suas funções.

## <a name="billing"></a>Cobrança

Você paga pelos aplicativos de funções em um plano do serviço de aplicativo como faria para outros recursos do serviço de aplicativo. Isso difere do [plano de consumo](consumption-plan.md) Azure Functions ou da Hospedagem de [plano Premium](functions-premium-plan.md) , que têm componentes de custo baseados em consumo. Você é cobrado apenas pelo plano, independentemente de quantos aplicativos de função ou aplicativos Web são executados no plano. Para saber mais, consulte a [página de preços do serviço de aplicativo](https://azure.microsoft.com/pricing/details/app-service/windows/). 

## <a name="always-on"></a><a name="always-on"></a> Always On

Se você executar em um plano do serviço de aplicativo, habilite a configuração **Always on** para que seu aplicativo de funções seja executado corretamente. Em um plano do Serviço de Aplicativo, o runtime das funções ficará ocioso após alguns minutos de inatividade, portanto, apenas gatilhos HTTP "despertarão" suas funções. A configuração **Always on** está disponível somente em um plano do serviço de aplicativo. Em um plano de Consumo, a plataforma ativa automaticamente os aplicativos de função.

Mesmo com Always On habilitado, o tempo limite de execução para funções individuais é controlado pela configuração `functionTimeout` no arquivo de projeto [host.json](functions-host-json.md#functiontimeout).

## <a name="scaling"></a>Dimensionamento

Usando um plano do serviço de aplicativo, você pode escalar horizontalmente manualmente Adicionando mais instâncias de VM. Você também pode habilitar o dimensionamento automático, embora o dimensionamento automático seja mais lento do que a escala elástica do plano Premium. Para saber mais, confira [Dimensionar a contagem de instâncias manual ou automaticamente](../azure-monitor/platform/autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json). Você também pode escalar verticalmente escolhendo um plano do Serviço de Aplicativo diferente. Para obter mais informações, consulte [Escalar verticalmente um aplicativo no Azure](../app-service/manage-scale-up.md). 

> [!NOTE] 
> Ao executar funções de JavaScript (Node.js) em um plano do serviço de aplicativo, você deve escolher um plano que tenha menos vCPUs. Para obter mais informações, consulte [escolher planos de serviço de aplicativo de núcleo único](functions-reference-node.md#choose-single-vcpu-app-service-plans). 
<!-- Note: the portal links to this section via fwlink https://go.microsoft.com/fwlink/?linkid=830855 --> 

## <a name="app-service-environments"></a>Ambientes de Serviço de Aplicativo

A execução em um [ambiente do serviço de aplicativo](../app-service/environment/intro.md) (ase) permite isolar totalmente suas funções e tirar proveito de números mais altos de instâncias do que um plano do serviço de aplicativo. Para começar, consulte .

Se você quiser apenas executar seu aplicativo de funções em uma rede virtual, poderá fazer isso usando o [plano Premium](functions-premium-plan.md). Para saber mais, confira [estabelecer Azure Functions acesso ao site privado](functions-create-private-site-access.md). 

## <a name="next-steps"></a>Próximas etapas

+ [Opções de Hospedagem de Azure Functions](functions-scale.md)
+ [Visão geral do plano do Serviço de Aplicativo do Azure](../app-service/overview-hosting-plans.md)