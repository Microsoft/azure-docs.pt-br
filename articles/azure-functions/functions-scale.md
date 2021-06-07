---
title: Escala e hospedagem no Azure Functions
description: Saiba como escolher entre Azure Functions plano de consumo e o plano Premium.
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.topic: conceptual
ms.date: 08/17/2020
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 490fa46deabc822e416705fe9bf9c5cdb58f8cd6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97936747"
---
# <a name="azure-functions-hosting-options"></a>Opções de Hospedagem de Azure Functions

Ao criar um aplicativo de funções no Azure, você deve escolher um plano de hospedagem para seu aplicativo. Há três planos de hospedagem básicos disponíveis para Azure Functions: [plano de consumo](consumption-plan.md), [plano Premium](functions-premium-plan.md)e [plano dedicado (serviço de aplicativo)](dedicated-plan.md). Todos os planos de hospedagem estão geralmente disponíveis (GA) em máquinas virtuais Linux e Windows.

O plano de hospedagem que você escolher ditará os seguintes comportamentos:

* Como seu aplicativo de funções é dimensionado.
* Os recursos disponíveis para cada instância do aplicativo de funções.
* Suporte para funcionalidade avançada, como conectividade de rede virtual do Azure.

Este artigo fornece uma comparação detalhada entre os vários planos de hospedagem, juntamente com a hospedagem baseada em kubernetes.

## <a name="overview-of-plans"></a>Visão geral dos planos

Veja a seguir um resumo dos benefícios dos três principais planos de hospedagem para o Functions:

| | |
| --- | --- |  
|**[Plano de consumo](consumption-plan.md)**| Dimensione automaticamente e pague apenas pelos recursos de computação quando suas funções estiverem em execução.<br/><br/>No plano de consumo, as instâncias do host do Functions são adicionadas e removidas dinamicamente com base no número de eventos de entrada.<br/><br/> ✔ Plano de hospedagem padrão.<br/>✔ Pague somente quando suas funções estiverem em execução.<br/>✔ É dimensionado automaticamente, mesmo durante períodos de alta carga.|  
|**[Plano Premium](functions-premium-plan.md)**|Dimensiona automaticamente com base na demanda usando trabalhos pré-configurados que executam aplicativos sem atraso após estarem ociosos, é executado em instâncias mais poderosas e se conecta a redes virtuais. <br/><br/>Considere o plano Azure Functions Premium nas seguintes situações: <br/><br/>✔ Seus aplicativos de funções são executados continuamente ou quase continuamente.<br/>✔ Você tem um número alto de execuções pequenas e uma fatura de execução alta, mas com poucos GB de segundos no plano de consumo.<br/>✔ Você precisa de mais opções de CPU ou memória do que o fornecido pelo plano de consumo.<br/>✔ Seu código precisa ser executado por mais tempo do que o máximo permitido no plano de consumo.<br/>✔ Você precisa de recursos que não estão disponíveis no plano de consumo, como a conectividade de rede virtual.|  
|**[Plano dedicado](dedicated-plan.md)** |Execute suas funções em um plano do serviço de aplicativo em taxas regulares do [plano do serviço de aplicativo](https://azure.microsoft.com/pricing/details/app-service/windows/).<br/><br/>Melhor para cenários de execução longa em que [Durable Functions](durable/durable-functions-overview.md) não pode ser usado. Considere um plano do serviço de aplicativo nas seguintes situações:<br/><br/>✔ Você tem VMs subutilizadas existentes que já estão executando outras instâncias do serviço de aplicativo.<br/>✔ Você deseja fornecer uma imagem personalizada na qual executar suas funções. <br/>✔ O dimensionamento e os custos de previsão são necessários.|  

As tabelas de comparação neste artigo também incluem as seguintes opções de hospedagem, que fornecem a maior quantidade de controle e isolamento para executar seus aplicativos de funções.  

| | |
| --- | --- |  
|**[ASE](dedicated-plan.md)** | O Ambiente do Serviço de Aplicativo (ASE) é um recurso do serviço de aplicativo que fornece um ambiente totalmente isolado e dedicado para executar com segurança aplicativos do serviço de aplicativo em alta escala.<br/><br/>ASEs são apropriadas para cargas de trabalho de aplicativo que exigem: <br/><br/>✔ Escala muito alta.<br/>✔ Isolamento de computação completo e acesso seguro à rede.<br/>✔ O uso de memória alta.|  
| **[Kubernetes](functions-kubernetes-keda.md)** | O kubernetes fornece um ambiente totalmente isolado e dedicado em execução no topo da plataforma kubernetes.<br/><br/> Kubernetes é apropriado para cargas de trabalho de aplicativo que exigem: <br/>✔ Os requisitos de hardware personalizados.<br/>✔ Isolamento e acesso seguro à rede.<br/>✔ A capacidade de executar em um ambiente híbrido ou de várias nuvens.<br/>✔ Executado junto com os aplicativos e serviços kubernetes existentes.|  

As tabelas restantes neste artigo comparam os planos em vários recursos e comportamentos. Para obter uma comparação de custo entre os planos de hospedagem dinâmica (consumo e Premium), consulte a [página de preços do Azure Functions](https://azure.microsoft.com/pricing/details/functions/). Para obter os preços das várias opções de plano dedicadas, consulte a [página de preços do serviço de aplicativo](https://azure.microsoft.com/pricing/details/app-service/windows/). 

## <a name="operating-systemruntime"></a>Sistema operacional/tempo de execução

A tabela a seguir mostra o suporte ao sistema operacional e ao tempo de execução de idioma com suporte para os planos de hospedagem.

| | Linux<sup>1</sup><br/>Somente código | Windows<sup>2</sup><br/>Somente código | Linux<sup>1, 3</sup><br/>Contêiner do Docker |
| --- | --- | --- | --- |
| **[Plano de consumo](consumption-plan.md)** | .NET Core<br/>Node.js<br/>Java<br/>Python | .NET Core<br/>Node.js<br/>Java<br/>PowerShell Core | Sem suporte  |
| **[Plano Premium](functions-premium-plan.md)** | .NET Core<br/>Node.js<br/>Java<br/>Python|.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python  | 
| **[Plano dedicado](dedicated-plan.md)** | .NET Core<br/>Node.js<br/>Java<br/>Python|.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python |
| **[ASE](dedicated-plan.md)** | .NET Core<br/>Node.js<br/>Java<br/>Python |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core  |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python | 
| **[Kubernetes](functions-kubernetes-keda.md)** | N/D | N/D |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python |

<sup>1</sup> o Linux é o único sistema operacional com suporte para a pilha de tempo de execução do Python. <br/>
<sup>2</sup> o Windows é o único sistema operacional com suporte para a pilha de tempo de execução do PowerShell.<br/>
<sup>3</sup> o Linux é o único sistema operacional com suporte para contêineres do Docker.<br/>

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]

## <a name="scale"></a>Escala

A tabela a seguir compara os comportamentos de dimensionamento dos vários planos de hospedagem.

| | Escalar horizontalmente | N º máximo de instâncias |
| --- | --- | --- |
| **[Plano de consumo](consumption-plan.md)** | [Controlado por evento](event-driven-scaling.md). Escale horizontalmente de forma automática, mesmo durante períodos de carga alta. A infraestrutura de Azure Functions dimensiona os recursos de CPU e memória adicionando instâncias adicionais do host do functions, com base no número de eventos de gatilho de entrada. | 200 |
| **[Plano Premium](functions-premium-plan.md)** | [Controlado por evento](event-driven-scaling.md). Escale horizontalmente de forma automática, mesmo durante períodos de carga alta. A infraestrutura de Azure Functions dimensiona os recursos de CPU e memória adicionando instâncias adicionais do host do functions, com base no número de eventos em que suas funções são disparadas. |100|
| **[Plano dedicado](dedicated-plan.md)**<sup>1</sup> | Manual/dimensionamento automático |10-20|
| **[Ase](dedicated-plan.md)**<sup>1</sup> | Manual/dimensionamento automático |100 |
| **[Kubernetes](functions-kubernetes-keda.md)**  | Dimensionamento automático controlado por evento para clusters kubernetes usando [Keda](https://keda.sh). | Varia &nbsp; de acordo com o &nbsp; cluster&nbsp;&nbsp;|

<sup>1</sup> para limites específicos para as várias opções do plano do serviço de aplicativo, consulte os [limites do plano do serviço de aplicativo](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits).

## <a name="cold-start-behavior"></a>Comportamento de início frio

|    |    | 
| -- | -- |
| **[Plano de consumo &nbsp;](consumption-plan.md)** | Os aplicativos podem ser dimensionados para zero quando estiverem ociosos, o que significa que algumas solicitações podem ter latência adicional na inicialização.  O plano de consumo tem algumas otimizações para ajudar a diminuir a hora de início frio, incluindo a extração de funções de espaço reservado pré-configuradas que já têm o host de funções e os processos de linguagem em execução. |
| **[Plano Premium](functions-premium-plan.md)** | Instâncias passivas perpétuas para evitar qualquer inicialização a frio. |
| **[Plano dedicado](dedicated-plan.md)** | Ao executar em um plano dedicado, o host do Functions pode ser executado continuamente, o que significa que a inicialização a frio não é realmente um problema. |
| **[ASE](dedicated-plan.md)** | Ao executar em um plano dedicado, o host do Functions pode ser executado continuamente, o que significa que a inicialização a frio não é realmente um problema. |
| **[Kubernetes](functions-kubernetes-keda.md)**  | Dependendo da configuração do KEDA, os aplicativos podem ser configurados para evitar uma inicialização a frio. Se estiver configurado para ser dimensionado para zero, um início frio será experimentado em busca de novos eventos. 

## <a name="service-limits"></a>Limites de serviço

[!INCLUDE [functions-limits](../../includes/functions-limits.md)]

## <a name="networking-features"></a>Recursos de rede

[!INCLUDE [functions-networking-features](../../includes/functions-networking-features.md)]

## <a name="billing"></a>Cobrança

| | | 
| --- | --- |
| **[Plano de consumo](consumption-plan.md)** | Pague apenas pelo tempo em que suas funções são executadas. A cobrança baseia-se no número de execuções, no tempo de execução e na memória usada. |
| **[Plano Premium](functions-premium-plan.md)** | O plano Premium é baseado no número de segundos de núcleo e na memória usada nas instâncias necessárias e pré-configuradas. Pelo menos uma instância por plano deve ser mantida quase sempre. Esse plano fornece os preços mais previsíveis. |
| **[Plano dedicado](dedicated-plan.md)* | Você paga o mesmo para aplicativos de funções em um plano do serviço de aplicativo como faria para outros recursos do serviço de aplicativo, como aplicativos Web.|
| **[ASE (Ambiente do Serviço de Aplicativo)](dedicated-plan.md)** | Há uma taxa mensal simples para um ASE que paga pela infraestrutura e não muda com o tamanho do ASE. Também há um custo por vCPU do plano do serviço de aplicativo. Todos os aplicativos hospedados no ASE estão em um SKU de preços Isolado. |
| **[Kubernetes](functions-kubernetes-keda.md)**| Você paga apenas os custos de seu cluster kubernetes; nenhuma cobrança adicional para funções. Seu aplicativo de funções é executado como uma carga de trabalho de aplicativo sobre o cluster, assim como um aplicativo regular. |

## <a name="next-steps"></a>Próximas etapas

+ [Tecnologias de implantação no Azure Functions](functions-deployment-technologies.md) 
+ [Guia do desenvolvedor do Azure Functions](functions-reference.md)