---
title: Excluir os atores do Microsoft Azure Service Fabric | Microsoft Docs
description: Saiba como excluir manualmente o Reliable Actors do Service Fabric e o respectivo estado.
services: service-fabric
documentationcenter: .net
author: amanbha
manager: chackdan
editor: vturecek
ms.assetid: b91384cc-804c-49d6-a6cb-f3f3d7d65a8e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/19/2018
ms.author: amanbha
ms.openlocfilehash: e297a6f42774f29e2eca4a410b695d5bbb636300
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60726597"
---
# <a name="delete-reliable-actors-and-their-state"></a>Excluir Reliable Actors e o respectivo estado
A coleta de lixo dos atores desativados elimina apenas o objeto do ator, mas não remove os dados que são armazenados no Gerenciador de Estado de um ator. Quando um ator é reativado, seus dados são novamente disponibilizados a ele através do Gerenciador de Estado. Nos casos em que atores armazenam dados no Gerenciador de Estado e são desativados, mas nunca reativados, pode ser necessário eliminar seus dados.

O [Serviço de Ator](service-fabric-reliable-actors-platform.md) fornece uma função para excluir atores de um chamador remoto:

```csharp
ActorId actorToDelete = new ActorId(id);

IActorService myActorServiceProxy = ActorServiceProxy.Create(
    new Uri("fabric:/MyApp/MyService"), actorToDelete);

await myActorServiceProxy.DeleteActorAsync(actorToDelete, cancellationToken)
```
```Java
ActorId actorToDelete = new ActorId(id);

ActorService myActorServiceProxy = ActorServiceProxy.create(
    new Uri("fabric:/MyApp/MyService"), actorToDelete);

myActorServiceProxy.deleteActorAsync(actorToDelete);
```

Os efeitos de excluir um ator são descritos abaixo de acordo com o estado, ativo ou inativo, do ator no momento.

* **Ator ativo**
  * O ator é removido da lista de atores ativos e é desativado.
  * Seu estado é excluído permanentemente.
* **Ator inativo**
  * Seu estado é excluído permanentemente.

Um ator não pode chamar a exclusão em si mesmo a partir de um de seus métodos de ator porque o ator não pode ser excluído durante a execução em um contexto de chamada de ator, no qual o tempo de execução obteve um bloqueio em torno da chamada de ator para impor o acesso single-threaded.

Para obter mais informações sobre Reliable Actors, leia o seguinte:
* [Lembretes e temporizadores de ator](service-fabric-reliable-actors-timers-reminders.md)
* [Eventos de ator](service-fabric-reliable-actors-events.md)
* [Reentrância de ator](service-fabric-reliable-actors-reentrancy.md)
* [Diagnóstico e monitoramento de desempenho do ator](service-fabric-reliable-actors-diagnostics.md)
* [Documentação de referência da API do Ator](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Código de exemplo C#](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Código de exemplo Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-lifecycle/garbage-collection.png
