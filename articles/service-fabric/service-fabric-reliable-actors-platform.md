---
title: Reliable Actors no Service Fabric | Microsoft Docs
description: Descreve como os Reliable Actors são dispostos em camadas nos Reliable Services e como eles usam os recursos da plataforma do Service Fabric.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: amanbha
ms.assetid: 45839a7f-0536-46f1-ae2b-8ba3556407fb
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 3/9/2018
ms.author: vturecek
ms.openlocfilehash: bc7569c9f230abb7677a8df9fc0cc0268e57296f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60725885"
---
# <a name="how-reliable-actors-use-the-service-fabric-platform"></a>Como Reliable Actors usam a plataforma do Service Fabric
Este artigo explica sobre o funcionamento dos Reliable Actors na plataforma do Azure Service Fabric. Os Reliable Actors são executados em uma estrutura hospedada em uma implementação de um serviço confiável com estado chamado *serviço de ator*. O serviço de ator contém todos os componentes necessários para gerenciar o ciclo de vida e a expedição de mensagens para seus atores:

* O Tempo de Execução de Ator gerencia o ciclo de vida, a coleta de lixo e impõe o acesso single-threaded.
* Um ouvinte de comunicação remota do serviço de ator aceita chamadas de acesso remoto aos atores e as envia a um dispatcher para encaminhamento à instância de ator apropriada.
* O Provedor de Estado de Ator encapsula provedores de estado (como o provedor de estado das Coleções Confiáveis) e fornece um adaptador para o gerenciamento de estado de ator.

Juntos, esses componentes formam a estrutura do Reliable Actor.

## <a name="service-layering"></a>Disposição em camadas de serviço
Como o próprio serviço de ator é um serviço confiável, todos os conceitos de [modelo de aplicativo](service-fabric-application-model.md), ciclo de vida, [empacotamento](service-fabric-package-apps.md), [implantação](service-fabric-deploy-remove-applications.md), atualização e dimensionamento dos Reliable Services aplicam-se da mesma maneira aos serviços de ator.

![Disposição em camadas do serviço de ator][1]

O diagrama anterior mostra a relação entre as estruturas do aplicativo do Service Fabric e o código do usuário. Os elementos em azul representam a estrutura do aplicativo dos Reliable Services, os elementos em laranja representam a estrutura do Reliable Actor e os elementos em verde representam o código do usuário.

Nos Reliable Services, o serviço herda a classe `StatefulService`. Essa classe é derivada de `StatefulServiceBase` (ou `StatelessService` para serviços sem estado). Nos Reliable Actors, você usa o serviço de ator. O serviço de ator é uma implementação diferente da classe `StatefulServiceBase` que implementa o padrão de ator no qual seus atores são executados. Como o próprio serviço de ator é apenas uma implementação de `StatefulServiceBase`, é possível escrever seu próprio serviço que deriva de `ActorService` e implementar recursos no nível de serviço da mesma forma que você faria ao herdar `StatefulService`, tais como:

* Backup e restauração do serviço.
* Funcionalidade compartilhada para todos os atores, por exemplo, um disjuntor.
* Chamadas de procedimento remotas no próprio serviço de ator, bem como em cada ator individual.

Para obter mais informações, consulte [Implementação de recursos de nível de serviço em seu serviço de ator](service-fabric-reliable-actors-using.md).

## <a name="application-model"></a>Modelo de aplicativo
Os serviços de ator são Reliable Services; portanto, o modelo do aplicativo é o mesmo. No entanto, as ferramentas de build da estrutura de ator geram alguns dos arquivos de modelo do aplicativo para você.

### <a name="service-manifest"></a>Manifesto do serviço
As ferramentas de build da estrutura de ator geram automaticamente o conteúdo do arquivo ServiceManifest.xml do serviço de ator. Este arquivo inclui:

* O tipo de serviço de ator. O nome do tipo é gerado de acordo com o nome de projeto do ator. Com base no atributo de persistência do ator, o sinalizador HasPersistedState também é definido de acordo.
* Pacote de códigos.
* Pacote de configuração.
* Recursos e pontos de extremidade.

### <a name="application-manifest"></a>Manifesto do aplicativo
As ferramentas de build da estrutura de ator criam automaticamente uma definição de serviço padrão para o serviço de ator. As ferramentas de build preenchem as propriedades padrão do serviço:

* A contagem de conjuntos de réplicas é determinada pelo atributo de persistência no ator. Sempre que o atributo de persistência no ator é alterado, a contagem de conjuntos de réplicas na definição de serviço padrão é redefinida de acordo.
* O esquema de partição e o intervalo são definidos como Int64 Uniforme com o intervalo de chaves Int64 completo.

## <a name="service-fabric-partition-concepts-for-actors"></a>Conceitos de partição de Malha do Serviço para atores
Serviços de ator são serviços com estado particionados. Cada partição de um serviço de ator contém um conjunto de atores. As partições de serviço são distribuídas automaticamente em vários nós no Service Fabric. As instâncias de ator são distribuídas como resultado disso.

![Particionamento e distribuição de ator][5]

Os Reliable Services podem ser criados com esquemas de partição diferentes e intervalos de chaves de partição. O serviço de ator usa o esquema de particionamento Int64 com o intervalo de chaves Int64 completo para mapear os atores para as partições.

### <a name="actor-id"></a>ID de ator
Cada ator criado no serviço tem uma ID exclusiva associada, representada pela classe `ActorId` . A `ActorId` é um valor de ID opaco que pode ser usado para a distribuição uniforme de atores nas partições de serviço por meio da geração de IDs aleatórias:

```csharp
ActorProxy.Create<IMyActor>(ActorId.CreateRandom());
```
```Java
ActorProxyBase.create<MyActor>(MyActor.class, ActorId.newId());
```


Cada `ActorId` é codificada em hash para um Int64. É por esse motivo que o serviço de ator deve usar um esquema de particionamento Int64 com o intervalo de chaves Int64 completo. No entanto, valores de ID personalizados podem ser usados para uma `ActorID`, incluindo GUIDs/UUIDs, cadeias de caracteres e Int64s.

```csharp
ActorProxy.Create<IMyActor>(new ActorId(Guid.NewGuid()));
ActorProxy.Create<IMyActor>(new ActorId("myActorId"));
ActorProxy.Create<IMyActor>(new ActorId(1234));
```
```Java
ActorProxyBase.create(MyActor.class, new ActorId(UUID.randomUUID()));
ActorProxyBase.create(MyActor.class, new ActorId("myActorId"));
ActorProxyBase.create(MyActor.class, new ActorId(1234));
```

Ao usar GUIDs/UUIDs e cadeias de caracteres, os valores são codificados em hash para um Int64. No entanto, ao fornecer explicitamente um Int64 para uma `ActorId`, o Int64 será mapeado diretamente para uma partição sem hash adicional. Você pode usar essa técnica para controlar em qual partição os atores serão colocados.


## <a name="next-steps"></a>Próximas etapas
* [Gerenciamento de estado do ator](service-fabric-reliable-actors-state-management.md)
* [Ciclo de vida do ator e coleta de lixo](service-fabric-reliable-actors-lifecycle.md)
* [Documentação de referência da API dos Atores](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.actors?redirectedfrom=MSDN&view=azure-dotnet)
* [Código de exemplo do .NET](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Código de exemplo de Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png
