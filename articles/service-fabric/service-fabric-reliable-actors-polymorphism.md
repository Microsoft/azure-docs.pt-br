---
title: Polimorfismo na estrutura Reliable Actors | Microsoft Docs
description: Crie hierarquias de interfaces .NET e tipos na estrutura Reliable Actors para reutilizar a funcionalidade e as definições da API.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: vturecek
ms.assetid: ef0eeff6-32b7-410d-ac69-87cba8b8fd46
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: c14b3006184f7bd6dcd1eb67be11bd0214957d72
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60725476"
---
# <a name="polymorphism-in-the-reliable-actors-framework"></a>Polimorfismo na estrutura Reliable Actors
A estrutura Reliable Actors permite que você crie atores usando muitas das mesmas técnicas que usaria no design orientado a objeto. Uma dessas técnicas é o polimorfismo, que permite que tipos e interfaces herdem de pais mais generalizados. A herança na estrutura de Reliable Actors geralmente segue o modelo de .NET com algumas restrições adicionais. No caso de Java/Linux, ele segue o modelo de Java.

## <a name="interfaces"></a>Interfaces
A estrutura de Reliable Actors exige que você defina pelo menos uma interface a ser implementada pelo seu tipo de ator. Essa interface é usada para gerar uma classe proxy que pode ser usada pelos clientes para se comunicar com seus atores. Interfaces podem herdar de outras interfaces, desde que cada interface implementada por um tipo de ator e todos os seus pais derivem, por fim, de IActor(C#) ou Actor(Java). IActor(C#) e Actor(Java) são as interfaces base definidas pela plataforma para atores em estruturas .NET e Java, respectivamente. Assim, o exemplo de polimorfismo clássico usando formas pode parecer semelhante ao seguinte:

![Hierarquia de interface de atores de forma][shapes-interface-hierarchy]

## <a name="types"></a>Tipos
Você também pode criar uma hierarquia de tipos de ator, derivados da classe base de ator fornecida pela plataforma. No caso de formas, você pode ter um tipo base `Shape`(C#) ou `ShapeImpl`(Java):

```csharp
public abstract class Shape : Actor, IShape
{
    public abstract Task<int> GetVerticeCount();

    public abstract Task<double> GetAreaAsync();
}
```
```Java
public abstract class ShapeImpl extends FabricActor implements Shape
{
    public abstract CompletableFuture<int> getVerticeCount();

    public abstract CompletableFuture<double> getAreaAsync();
}
```

Os subtipos de `Shape`(C#) ou `ShapeImpl`(Java) podem substituir os métodos da base.

```csharp
[ActorService(Name = "Circle")]
[StatePersistence(StatePersistence.Persisted)]
public class Circle : Shape, ICircle
{
    public override Task<int> GetVerticeCount()
    {
        return Task.FromResult(0);
    }

    public override async Task<double> GetAreaAsync()
    {
        CircleState state = await this.StateManager.GetStateAsync<CircleState>("circle");

        return Math.PI *
            state.Radius *
            state.Radius;
    }
}
```
```Java
@ActorServiceAttribute(name = "Circle")
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
public class Circle extends ShapeImpl implements Circle
{
    @Override
    public CompletableFuture<Integer> getVerticeCount()
    {
        return CompletableFuture.completedFuture(0);
    }

    @Override
    public CompletableFuture<Double> getAreaAsync()
    {
        return (this.stateManager().getStateAsync<CircleState>("circle").thenApply(state->{
          return Math.PI * state.radius * state.radius;
        }));
    }
}
```

Observe o atributo `ActorService` no tipo de ator. Esse atributo informa à estrutura Reliable Actor que ela deverá criar automaticamente um serviço de hospedagem de atores desse tipo. Em alguns casos, você poderá criar um tipo base que destina-se somente para o compartilhamento de recursos com subtipos e nunca será usado para criar uma instância de atores concretos. Nesses casos, você deve usar a palavra-chave `abstract` para indicar que você nunca criará um ator com base nesse tipo.

## <a name="next-steps"></a>Próximas etapas
* Veja [Como a estrutura Reliable Actors aproveita a plataforma do Service Fabric](service-fabric-reliable-actors-platform.md) para oferecer estado consistente, escalabilidade e confiabilidade.
* Saiba mais sobre o [ciclo de vida do ator](service-fabric-reliable-actors-lifecycle.md).

<!-- Image references -->

[shapes-interface-hierarchy]: ./media/service-fabric-reliable-actors-polymorphism/Shapes-Interface-Hierarchy.png
