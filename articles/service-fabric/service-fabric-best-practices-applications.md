---
title: Práticas recomendadas de design de aplicativo Service Fabric do Azure | Microsoft Docs
description: Práticas recomendadas para o desenvolvimento de aplicativos Service Fabric.
services: service-fabric
documentationcenter: .net
author: markfussell
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/18/2019
ms.author: mfussell
ms.openlocfilehash: 06af1f4326e3f6a6dcb53c8710a126f43e2d2f6a
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67875110"
---
# <a name="azure-service-fabric-application-design-best-practices"></a>Práticas recomendadas de design do aplicativo Service Fabric do Azure

Este artigo fornece orientações de práticas recomendadas para a criação de aplicativos e serviços no Azure Service Fabric.
 
## <a name="get-familiar-with-service-fabric"></a>Familiarize-se com o Service Fabric
* Leia o artigo [para saber mais sobre Service Fabric?](service-fabric-content-roadmap.md) .
* Leia sobre [cenários de aplicativo Service Fabric](service-fabric-application-scenarios.md).
* Entenda as opções do modelo de programação lendo [Service Fabric visão geral do modelo de programação](service-fabric-choose-framework.md).



## <a name="application-design-guidance"></a>Diretrizes de design de aplicativo
Familiarize-se com a [arquitetura geral](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric) de aplicativos Service Fabric e suas [considerações de design](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric#design-considerations).

### <a name="choose-an-api-gateway"></a>Escolher um gateway de API
Use um serviço de gateway de API que se comunica com serviços de back-end que podem ser escalados horizontalmente. Os serviços de gateway de API mais comuns usados são:

- [Gerenciamento de API do Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-api-management-overview), que é [integrado com Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-deploy-api-management).
- [Hub IOT do Azure](https://docs.microsoft.com/azure/iot-hub/) ou [hubs de eventos do Azure](https://docs.microsoft.com/azure/event-hubs/), usando o [ServiceFabricProcessor](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/ServiceFabricProcessor) para ler as partições do hub de eventos.
- [Træfik proxy reverso](https://blogs.msdn.microsoft.com/azureservicefabric/2018/04/05/intelligent-routing-on-service-fabric-with-traefik/), usando o [provedor de Service Fabric do Azure](https://docs.traefik.io/configuration/backends/servicefabric/).
- [Gateway de Aplicativo do Azure](https://docs.microsoft.com/azure/application-gateway/).

   > [!NOTE] 
   > Aplicativo Azure gateway não está diretamente integrado ao Service Fabric. O gerenciamento de API do Azure é normalmente a escolha preferida.
- Seu próprio gateway de aplicativo Web [ASP.NET Core](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-aspnetcore) criado de forma personalizada.

### <a name="stateless-services"></a>Serviços sem estado
Recomendamos que você sempre comece criando serviços sem estado usando [Reliable Services](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction) e armazenando o estado em um banco de dados do azure, Azure Cosmos DB ou armazenamento do Azure. O estado externaled é a abordagem mais familiar para a maioria dos desenvolvedores. Essa abordagem também permite que você aproveite os recursos de consulta no repositório.  

### <a name="when-to-use-stateful-services"></a>Quando usar serviços com estado
Considere serviços com estado quando você tiver um cenário de baixa latência e precisar manter os dados próximos à computação. Alguns cenários de exemplo incluem dispositivos de alta do IoT digital, estado do jogo, estado da sessão, cache de dados de um banco e fluxos de trabalho de longa duração para rastrear chamadas para outros serviços.

Decida sobre o período de retenção de dados:

- **Dados armazenados em cache**. Use o Caching quando a latência para armazenamentos externos for um problema. Use um serviço com estado como seu próprio cache de dados ou considere o uso do SoCreate de software livre [Service Fabric cache distribuído](https://github.com/SoCreate/service-fabric-distributed-cache). Nesse cenário, você não precisa se preocupar se perder todos os dados no cache.
- **Dados associados ao tempo**. Nesse cenário, você precisa manter os dados próximos para serem computados por um período de tempo para latência, mas pode perder os dados em um *desastre*. Por exemplo, em muitas soluções de IoT, os dados precisam ser próximos de computação, como quando a temperatura média nos últimos dias está sendo calculada, mas se esses dados forem perdidos, os pontos de dados específicos registrados não serão importantes. Além disso, nesse cenário, normalmente, você não se preocupa em fazer backup dos pontos de dados individuais. Você faz backup somente dos valores médios computados que são gravados periodicamente no armazenamento externo.  
- **Dados de longo prazo**. As coleções confiáveis podem armazenar seus dados permanentemente. Mas, nesse caso, você precisa [se preparar para a recuperação de desastre](https://docs.microsoft.com/azure/service-fabric/service-fabric-disaster-recovery), incluindo a [configuração de políticas de backup periódicos](https://docs.microsoft.com/azure/service-fabric/service-fabric-backuprestoreservice-configure-periodic-backup) para seus clusters. Na verdade, você configura o que acontece se o cluster for destruído em um desastre, onde você precisaria criar um novo cluster e como implantar novas instâncias de aplicativo e recuperar-se do backup mais recente.

Economize custos e melhore a disponibilidade:
- Você pode reduzir os custos usando serviços com estado porque não incorre em custos de acesso a dados e transações do repositório remoto e porque não precisa usar outro serviço, como o cache do Azure para Redis.
- Usar serviços com estado principalmente para armazenamento e não para computação é caro e não é recomendável. Considere os serviços com estado como computação com armazenamento local barato.
- Ao remover dependências de outros serviços, você pode melhorar a disponibilidade do serviço. O gerenciamento de estado com HA no cluster isola você de outros problemas de tempo de inatividade de serviço ou de latência.

## <a name="how-to-work-with-reliable-services"></a>Como trabalhar com Reliable Services
Service Fabric Reliable Services permite que você crie facilmente serviços com e sem estado. Para obter mais informações, consulte a [introdução ao Reliable Services](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction).
- Sempre honra o [token](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-lifecycle#stateful-service-primary-swaps) de cancelamento no `RunAsync()` método para serviços com e sem estado e `ChangeRole()` o método para serviços com estado. Caso contrário, Service Fabric não saberá se o serviço pode ser fechado. Por exemplo, se você não honrar o token de cancelamento, poderão ocorrer tempos de atualização de aplicativos muito mais longos.
-   Abra e feche os ouvintes de [comunicação](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication) em tempo hábil e aceite os tokens de cancelamento.
-   Nunca misture código de sincronização com código assíncrono. Por exemplo, não use `.GetAwaiter().GetResult()` em suas chamadas assíncronas. Use Async *completamente* por meio da pilha de chamadas.

## <a name="how-to-work-with-reliable-actors"></a>Como trabalhar com Reliable Actors
Service Fabric Reliable Actors permite que você crie facilmente atores com estado e virtual. Para obter mais informações, consulte a [introdução ao Reliable Actors](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction).

- Considere seriamente o uso de mensagens pub/sub entre seus atores para dimensionar seu aplicativo. As ferramentas que fornecem esse serviço incluem o [SoCreate de código-fonte aberto Service Fabric pub/sub](https://service-fabric-pub-sub.socreate.it/) e o [barramento de serviço do Azure](https://docs.microsoft.com/azure/service-bus/).
- Torne o estado do ator o mais [granular possível](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices).
- Gerencie o [ciclo de vida do ator](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices). Exclua os atores se você não for usá-los novamente. Excluir atores desnecessários é especialmente importante quando você está usando o [provedor de estado volátil](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#state-persistence-and-replication), pois todo o estado é armazenado na memória.
- Devido à sua [simultaneidade baseada em](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction#concurrency)desligamento, os atores são mais bem usados como objetos independentes. Não crie grafos de chamadas de método síncronas multisservidor (cada uma delas mais provavelmente se tornará uma chamada de rede separada) ou crie solicitações de ator circulares. Isso irá afetar significativamente o desempenho e a escala.
- Não misture o código de sincronização com código assíncrono. Use o Async de forma consistente para evitar problemas de desempenho.
- Não faça chamadas de longa execução em atores. Chamadas de longa execução bloquearão outras chamadas para o mesmo ator, devido à simultaneidade baseada em desligamento.
- Se você estiver se comunicando com outros serviços usando [Service Fabric comunicação remota](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-remoting) e estiver criando `ServiceProxyFactory`um, crie a fábrica no nível de [serviço de ator](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-using) e *não* no nível de ator.


## <a name="application-diagnostics"></a>Diagnóstico de aplicativo
Seja completo sobre a adição de [log de aplicativo](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-app) em chamadas de serviço. Ele o ajudará a diagnosticar cenários nos quais os serviços se chamam uns aos outros. Por exemplo, quando uma chamada B chama C calls D, ela pode falhar em qualquer lugar. Se você não tiver registro em log suficiente, as falhas serão difíceis de diagnosticar. Se os serviços estiverem registrando muito em log devido a volumes de chamada, certifique-se de pelo menos erros e avisos de log.

## <a name="iot-and-messaging-applications"></a>Aplicativos de mensagens e de IoT
Quando você estiver lendo mensagens do [Hub IOT do Azure](https://docs.microsoft.com/azure/iot-hub/) ou [hubs de eventos do Azure](https://docs.microsoft.com/azure/event-hubs/), use [ServiceFabricProcessor](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/ServiceFabricProcessor). O ServiceFabricProcessor integra-se com Service Fabric Reliable Services para manter o estado de leitura das partições do hub de eventos e envia por push novas mensagens para `IEventProcessor::ProcessEventsAsync()` seus serviços por meio do método.


## <a name="design-guidance-on-azure"></a>Diretrizes de design no Azure
* Visite o [centro de arquitetura do Azure](https://docs.microsoft.com/azure/architecture/microservices/) para obter diretrizes de design sobre como [criar microserviços no Azure](https://docs.microsoft.com/azure/architecture/microservices/).

* Visite [a introdução ao Azure para jogos](https://docs.microsoft.com/gaming/azure/) para obter diretrizes de design sobre como [usar Service Fabric nos serviços de jogos](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf).
