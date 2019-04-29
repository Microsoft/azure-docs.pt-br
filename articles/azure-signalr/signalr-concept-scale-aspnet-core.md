---
title: Dimensionar o SignalR do ASP.NET Core com o Azure SignalR
description: Uma visão geral de como usar o serviço Azure SignalR para dimensionar aplicativos do SignalR do ASP.NET Core.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: 8a4012d204b6dafa1233e4ce3d878590120be47d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60640218"
---
# <a name="scale-aspnet-core-signalr-applications-with-azure-signalr-service"></a>Dimensionar aplicativos do SignalR do ASP.NET Core com o Serviço Azure SignalR

## <a name="developing-signalr-apps"></a>Desenvolvendo aplicativos SignalR

Atualmente, há [duas versões](https://docs.microsoft.com/aspnet/core/signalr/version-differences) do SignalR que você pode usar com seus aplicativos Web: SignalR para ASP.NET e SignalR do ASP.NET Core, que é a versão mais recente. O Serviço Azure SignalR é um serviço gerenciado do Azure baseado no ASP.NET Core SignalR.

O ASP.NET Core SignalR é uma reformulação da versão anterior. Como resultado, o ASP.NET Core SignalR não é compatível com a versão anterior do SignalR. As APIs e os comportamentos são diferentes. O SDK do ASP.NET Core SignalR tem como destino o .NET Standard, então você ainda pode usá-lo com o .NET Framework. No entanto, você deve usar as novas APIs em vez das antigas. Se você estiver usando o SignalR e quiser mudar para o ASP.NET Core SignalR, ou para o Serviço Azure SignalR, será necessário alterar o código para lidar com as diferenças nas APIs.

Com o Serviço Azure SignalR, o componente do servidor do ASP.NET Core SignalR é hospedado no Azure. No entanto, como a tecnologia tem base no ASP.NET Core, você tem a capacidade de executar seu aplicativo Web real em várias plataformas (Windows, Linux e MacOS) durante a hospedagem com o [Serviço de Aplicativo Azure](../app-service/overview.md), [IIS](https://docs.microsoft.com/aspnet/core/host-and-deploy/iis/index), [Nginx](https://docs.microsoft.com/aspnet/core/host-and-deploy/linux-nginx), [Apache](https://docs.microsoft.com/aspnet/core/host-and-deploy/linux-apache), [Docker](https://docs.microsoft.com/aspnet/core/host-and-deploy/docker/index). Você também pode usar a auto-hospedagem em seu próprio processo.

Se os objetivos de seu aplicativo incluírem: suporte à funcionalidade mais recente para atualização de clientes da Web com atualizações de conteúdo em tempo real, execução em várias plataformas (Azure, Windows, Linux e macOS) e hospedagem em ambientes diferentes, a melhor escolha será aproveitar o Serviço Azure SignalR.

## <a name="why-not-deploy-signalr-myself"></a>Por que eu mesmo não posso implantar o SignalR?

Ainda é uma abordagem válida implantar seu próprio aplicativo Web do Azure com suporte para o ASP.NET Core SignalR como um componente de back-end para seu aplicativo Web geral.

Um dos principais motivos para usar o Serviço Azure SignalR é a simplicidade. Com o Serviço Azure SignalR, você não precisa lidar com problemas de desempenho, escalabilidade, disponibilidade. Esses problemas são solucionados para você com um contrato de nível de serviço de 99,9%.

Além disso, os WebSockets normalmente são a técnica preferencial para dar suporte a atualizações de conteúdo em tempo real. No entanto, o balanceamento de carga de uma grande quantidade de conexões WebSocket persistentes se torna um problema complicado para resolver ao dimensionar. As soluções comuns usam: balanceamento de carga de DNS, balanceadores de carga de hardware e balanceamento de carga de software. O Serviço Azure SignalR soluciona esse problema para você.

Outro motivo pode ser a falta de necessidade de hospedar um aplicativo Web. A lógica do seu aplicativo Web pode aproveitar a [Computação sem servidor](https://azure.microsoft.com/overview/serverless-computing/). Por exemplo, talvez seu código seja hospedado e executado apenas sob demanda, com gatilhos do [Azure Functions](https://docs.microsoft.com/azure/azure-functions/). Esse cenário pode ser complicado, pois seu código só é executado sob demanda e não mantém conexões longas com clientes. O Serviço Azure SignalR pode lidar com essa situação, uma vez que o serviço já gerencia conexões para você. Veja a [visão geral sobre como usar o Serviço SignalR com o Azure Functions](signalr-concept-azure-functions.md) para obter mais detalhes.

## <a name="how-does-it-scale"></a>Como é possível dimensioná-lo?

É comum dimensionar o SignalR com SQL Server, Barramento de Serviço do Azure ou Cache do Azure para Redis. O Serviço Azure SignalR trata do dimensionamento para você. O desempenho e o custo dessas abordagens são parecidos, sem a complexidade de lidar com os outros serviços. Basta atualizar a contagem de unidade para o seu serviço. Cada unidade dá suporte a até 1.000 conexões de cliente.

## <a name="next-steps"></a>Próximas etapas

* [Início Rápido: criar uma sala de chat com o Azure SignalR](signalr-quickstart-dotnet-core.md)