---
title: Temporizadores e lembretes de Reliable Actors | Microsoft Docs
description: Introdução a temporizadores e lembretes para Reliable Actors do Service Fabric.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: amanbha
ms.assetid: 00c48716-569e-4a64-bd6c-25234c85ff4f
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 323de842645cced3c6f490e98112fcbcd184aa64
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60726801"
---
# <a name="actor-timers-and-reminders"></a>Lembretes e temporizadores de ator
Os atores podem agendar o trabalho periódico neles mesmos ao registrarem temporizadores ou lembretes. Este artigo mostra como usar temporizadores e lembretes e explica as diferenças entre eles.

## <a name="actor-timers"></a>Temporizadores de ator
Os temporizadores de ator oferecem um wrapper simples em torno de um temporizador Java ou .NET para garantir que os métodos de retorno de chamada respeitem as garantias de simultaneidade baseada em turnos fornecidas pelo tempo de execução dos Atores.

Os atores podem usar os métodos `RegisterTimer`(C#) ou `registerTimer`(Java) e `UnregisterTimer`(C#) ou `unregisterTimer`(Java) em sua classe base para registrar e cancelar o registro de seus temporizadores. O exemplo a seguir mostra o uso de APIs de temorizador. As APIs são muito semelhantes ao temporizador do .NET ou do Java. Neste exemplo, quando o temporizador chega ao fim, o tempo de execução dos Atores chamará o método `MoveObject`(C#) ou `moveObject`(Java). É garantido que o método respeitará a simultaneidade baseada em turnos. Isso significa que nenhum outro método de ator ou retornos de chamada de temporizador/lembrete estará em andamento até que a execução desse retorno de chamada seja concluída.

```csharp
class VisualObjectActor : Actor, IVisualObject
{
    private IActorTimer _updateTimer;

    public VisualObjectActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    protected override Task OnActivateAsync()
    {
        ...

        _updateTimer = RegisterTimer(
            MoveObject,                     // Callback method
            null,                           // Parameter to pass to the callback method
            TimeSpan.FromMilliseconds(15),  // Amount of time to delay before the callback is invoked
            TimeSpan.FromMilliseconds(15)); // Time interval between invocations of the callback method

        return base.OnActivateAsync();
    }

    protected override Task OnDeactivateAsync()
    {
        if (_updateTimer != null)
        {
            UnregisterTimer(_updateTimer);
        }

        return base.OnDeactivateAsync();
    }

    private Task MoveObject(object state)
    {
        ...
        return Task.FromResult(true);
    }
}
```
```Java
public class VisualObjectActorImpl extends FabricActor implements VisualObjectActor
{
    private ActorTimer updateTimer;

    public VisualObjectActorImpl(FabricActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    @Override
    protected CompletableFuture onActivateAsync()
    {
        ...

        return this.stateManager()
                .getOrAddStateAsync(
                        stateName,
                        VisualObject.createRandom(
                                this.getId().toString(),
                                new Random(this.getId().toString().hashCode())))
                .thenApply((r) -> {
                    this.registerTimer(
                            (o) -> this.moveObject(o),                        // Callback method
                            "moveObject",
                            null,                                             // Parameter to pass to the callback method
                            Duration.ofMillis(10),                            // Amount of time to delay before the callback is invoked
                            Duration.ofMillis(timerIntervalInMilliSeconds));  // Time interval between invocations of the callback method
                    return null;
                });
    }

    @Override
    protected CompletableFuture onDeactivateAsync()
    {
        if (updateTimer != null)
        {
            unregisterTimer(updateTimer);
        }

        return super.onDeactivateAsync();
    }

    private CompletableFuture moveObject(Object state)
    {
        ...
        return this.stateManager().getStateAsync(this.stateName).thenCompose(v -> {
            VisualObject v1 = (VisualObject)v;
            v1.move();
            return (CompletableFuture<?>)this.stateManager().setStateAsync(stateName, v1).
                    thenApply(r -> {
                      ...
                      return null;});
        });
    }
}
```

O período seguinte do temporizador é iniciado depois que o retorno de chamada concluir a execução. Isso significa que o temporizador será interrompido enquanto o retorno de chamada estiver em execução será iniciado quando o retorno de chamada for concluído.

O tempo de execução dos Atores salva as alterações feitas ao Gerenciador de Estado do ator quando o retorno de chamada é concluído. Se ocorrer um erro ao salvar o estado, esse objeto de ator será desativado e uma nova instância será ativada.

Todos os temporizadores são interrompidos quando o ator é desativado como parte da coleta de lixo. Nenhum retorno de chamada de temporizador é chamado depois disso. Além disso, o tempo de execução de atores não retém todas as informações sobre os temporizadores que estavam em execução antes de desativação. Cabe ao ator registrar todos os temporizadores necessários quando ele for reativado no futuro. Para obter mais informações, consulte a seção sobre [coleta de lixo de ator](service-fabric-reliable-actors-lifecycle.md).

## <a name="actor-reminders"></a>Lembretes de ator
Os lembretes são um mecanismo para disparar chamadas de retorno persistentes em um ator em horários específicos. Sua funcionalidade é semelhante à dos temporizadores. Mas, ao contrário dos temporizadores, os lembretes são acionados em todas as circunstâncias até que o ator cancele o registro deles explicitamente ou até que o ator seja explicitamente excluído. Especificamente, os lembretes são acionados em todas as desativações e failovers ator porque o tempo de execução de atores retém as informações sobre os lembretes do ator usando o provedor de estado do ator. Observe que a confiabilidade de lembretes está associada às garantias de confiabilidade de estado fornecidas pelo provedor de estado de ator. Isso significa que para os atores cuja persistência de estado é definida como Nenhuma, os lembretes não serão acionado após um failover. 

Para registrar um lembrete, um ator chama o método `RegisterReminderAsync` fornecido na classe base, como mostrado no exemplo a seguir:

```csharp
protected override async Task OnActivateAsync()
{
    string reminderName = "Pay cell phone bill";
    int amountInDollars = 100;

    IActorReminder reminderRegistration = await this.RegisterReminderAsync(
        reminderName,
        BitConverter.GetBytes(amountInDollars),
        TimeSpan.FromDays(3),    //The amount of time to delay before firing the reminder
        TimeSpan.FromDays(1));    //The time interval between firing of reminders
}
```

```Java
@Override
protected CompletableFuture onActivateAsync()
{
    String reminderName = "Pay cell phone bill";
    int amountInDollars = 100;

    ActorReminder reminderRegistration = this.registerReminderAsync(
            reminderName,
            state,
            dueTime,    //The amount of time to delay before firing the reminder
            period);    //The time interval between firing of reminders
}
```

Neste exemplo, `"Pay cell phone bill"` é o nome do lembrete. Essa é uma cadeia de caracteres usada pelo ator para identificar exclusivamente um lembrete. `BitConverter.GetBytes(amountInDollars)`(C#) é o contexto que está associado ao lembrete. Ele será passado para o ator como um argumento para o retorno de chamada do lembrete, ou seja `IRemindable.ReceiveReminderAsync`(C#) ou `Remindable.receiveReminderAsync`(Java).

Os atores que usam lembretes devem implementar a interface `IRemindable` como mostrado no exemplo a seguir.

```csharp
public class ToDoListActor : Actor, IToDoListActor, IRemindable
{
    public ToDoListActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task ReceiveReminderAsync(string reminderName, byte[] context, TimeSpan dueTime, TimeSpan period)
    {
        if (reminderName.Equals("Pay cell phone bill"))
        {
            int amountToPay = BitConverter.ToInt32(context, 0);
            System.Console.WriteLine("Please pay your cell phone bill of ${0}!", amountToPay);
        }
        return Task.FromResult(true);
    }
}
```
```Java
public class ToDoListActorImpl extends FabricActor implements ToDoListActor, Remindable
{
    public ToDoListActor(FabricActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture receiveReminderAsync(String reminderName, byte[] context, Duration dueTime, Duration period)
    {
        if (reminderName.equals("Pay cell phone bill"))
        {
            int amountToPay = ByteBuffer.wrap(context).getInt();
            System.out.println("Please pay your cell phone bill of " + amountToPay);
        }
        return CompletableFuture.completedFuture(true);
    }

```

Quando um lembrete é disparado, o tempo de execução dos Reliable Actors invoca o método `ReceiveReminderAsync`(C#) ou `receiveReminderAsync`(Java) no Ator. Um ator pode registrar vários lembretes, que o método `ReceiveReminderAsync`(C#) ou `receiveReminderAsync`(Java) será chamado quando qualquer um desses lembretes for disparado. O ator pode usar o nome de lembrete que é passado para o método `ReceiveReminderAsync`(C#) ou `receiveReminderAsync`(Java) para descobrir qual lembrete foi acionado.

O tempo de execução dos Atores salva o estado do ator quando a chamada `ReceiveReminderAsync`(C#) ou `receiveReminderAsync`(Java) é concluída. Se ocorrer um erro ao salvar o estado, esse objeto de ator será desativado e uma nova instância será ativada.

Para cancelar o registro de um lembrete, um ator chama o método `UnregisterReminderAsync`(C#) ou `unregisterReminderAsync`(Java) como mostrado no exemplo abaixo.

```csharp
IActorReminder reminder = GetReminder("Pay cell phone bill");
Task reminderUnregistration = UnregisterReminderAsync(reminder);
```
```Java
ActorReminder reminder = getReminder("Pay cell phone bill");
CompletableFuture reminderUnregistration = unregisterReminderAsync(reminder);
```

Como mostrado acima, o método `UnregisterReminderAsync`(C#) ou `unregisterReminderAsync`(Java) aceita uma interface `IActorReminder`(C#) ou `ActorReminder`(Java). A classe base de ator dá suporte a um método `GetReminder`(C#) ou `getReminder`(Java), que pode ser usado para recuperar a interface `IActorReminder`(C#) ou `ActorReminder`(Java) passando-se o nome do lembrete. Isso é conveniente porque o ator não precisa manter a interface `IActorReminder`(C#) ou `ActorReminder`(Java) que foi retornada com a chamada do método `RegisterReminder`(C#) ou `registerReminder`(Java).

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre reentrada e eventos de Ator Confiável:
* [Eventos de ator](service-fabric-reliable-actors-events.md)
* [Reentrância de ator](service-fabric-reliable-actors-reentrancy.md)
