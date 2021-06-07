---
title: Excluir atores de Service Fabric do Azure
description: Saiba como excluir manualmente e completamente Reliable Actors e seu estado em um aplicativo de Service Fabric do Azure.
ms.topic: conceptual
ms.date: 03/19/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: 16d4ab6a3c155f897cf9212fb1cd6c34d977b9ec
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96574013"
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

Um ator não pode chamar a exclusão em si mesmo a partir de um de seus métodos de ator porque o ator não pode ser excluído durante a execução em um contexto de chamada de ator, no qual o runtime obteve um bloqueio em torno da chamada de ator para impor o acesso single-threaded.

Para obter mais informações sobre Reliable Actors, leia o seguinte:
* [Lembretes e temporizadores de ator](service-fabric-reliable-actors-timers-reminders.md)
* [Eventos de ator](service-fabric-reliable-actors-events.md)
* [Reentrância do ator](service-fabric-reliable-actors-reentrancy.md)
* [Diagnóstico e monitoramento de desempenho do ator](service-fabric-reliable-actors-diagnostics.md)
* [Documentação de referência da API do Ator](/previous-versions/azure/dn971626(v=azure.100))
* [Código de exemplo em C#](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Código de exemplo Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-lifecycle/garbage-collection.png
