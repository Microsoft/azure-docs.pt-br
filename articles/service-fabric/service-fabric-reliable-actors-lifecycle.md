---
title: Visão geral do ciclo de vida do ator do Azure Service Fabric
description: Explica o ciclo de vida, a coleta de lixo e a exclusão manual de atores e seu estado de Reliable Actor do Service Fabric
ms.topic: conceptual
ms.date: 10/06/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 88db4bb2376cbc418d6954e274a18a6c18a280d1
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96576036"
---
# <a name="actor-lifecycle-automatic-garbage-collection-and-manual-delete"></a>Ciclo de vida, coleta automática de lixo e exclusão manual do ator
Um ator é ativado na primeira vez que uma chamada é feita para qualquer um de seus métodos. Um ator será desativado (lixo coletado pelo runtime dos Atores) se ele não for usado pelo período configurável. Um ator e seu estado também podem ser excluídos manualmente a qualquer momento.

## <a name="actor-activation"></a>Ativação do ator
Quando um ator é ativado, ocorre o seguinte:

* Quando uma chamada chega para um ator e ele ainda não está ativo, é criado um novo ator.
* O estado do ator será carregado se ele estiver mantendo o estado.
* O método `OnActivateAsync` (C#) ou `onActivateAsync` (Java) – que pode ser substituído na implementação do ator – é chamado.
* Agora, o ator é considerado ativo.

## <a name="actor-deactivation"></a>Desativação do ator
Quando um ator é desativado, ocorre o seguinte:

* Quando um ator não é usado por algum tempo, ele é removido da tabela de Atores Ativos.
* O método `OnDeactivateAsync` (C#) ou `onDeactivateAsync` (Java) – que pode ser substituído na implementação do ator – é chamado. Isso limpa todos os medidores de tempo do ator. Operações de ator, como alterações de estado, não devem ser chamadas por meio desse método.

> [!TIP]
> O runtime da malha atores emite alguns eventos de [relacionados à desativação e ativação de ator](service-fabric-reliable-actors-diagnostics.md#list-of-events-and-performance-counters). Eles são úteis para diagnóstico e monitoramento de desempenho.
>
>

### <a name="actor-garbage-collection"></a>Coleta de Lixo de Ator
Quando um ator é desativado, as referências ao objeto do ator são liberadas e ele pode ter seu lixo coletado normalmente pelo coletor de lixo CLR (Common Language Runtime) ou JVM (máquina virtual Java). A coleta de lixo elimina apenas o objeto do ator; ela **não** remove o estado armazenado no Gerenciador de Estado do ator. Na próxima vez que o ator for ativado, um novo objeto de ator será criado e seu estado será restaurado.

O que conta como "está sendo usado" para fins de desativação e coleta de lixo?

* Recebimento de chamadas
* `IRemindable.ReceiveReminderAsync` que está sendo invocado (aplicável somente se o ator usar lembretes).

> [!NOTE]
> Se o ator usar temporizadores e o retorno de chamada de seu temporizador for invocado, ele **não** será contado como "está sendo usado".
>
>

Antes de entrar nos detalhes da desativação, é importante definir os seguintes termos:

* *Intervalo de verificação*. É o intervalo no qual o runtime dos Atores verifica a tabela de Atores Ativos para saber se há atores que podem ser desativados e ter o lixo coletado. O valor padrão é 1 minuto.
* *Tempo limite de ociosidade*. Este é o período que um ator deve permanecer sem utilização (ocioso) para que possa ser desativado e ter o lixo coletado. O valor padrão é 60 minuto.

Normalmente não é necessário alterar esses padrões. No entanto, se necessário, esses intervalos podem ser alterados por meio de `ActorServiceSettings` ao registrar seu [Serviço de Ator](service-fabric-reliable-actors-platform.md):

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        ActorRuntime.RegisterActorAsync<MyActor>((context, actorType) =>
                new ActorService(context, actorType,
                    settings:
                        new ActorServiceSettings()
                        {
                            ActorGarbageCollectionSettings =
                                new ActorGarbageCollectionSettings(10, 2)
                        }))
            .GetAwaiter()
            .GetResult();
    }
}
```

```Java
public class Program
{
    public static void main(String[] args)
    {
        ActorRuntime.registerActorAsync(
                MyActor.class,
                (context, actorTypeInfo) -> new FabricActorService(context, actorTypeInfo),
                timeout);
    }
}
```
Para cada ator ativo, o runtime do ator controla por quanto tempo ele permanece ocioso (ou seja, não usado). O tempo de execução do ator verifica cada um dos atores `ScanIntervalInSeconds` a cada para ver se ele pode ser coletado pelo lixo e o marca se ele esteve ocioso para `IdleTimeoutInSeconds` .

Sempre que um ator é usado, seu tempo ocioso é redefinido como 0. Depois disso, o ator só pode ter seu lixo coletado se permanecer ocioso novamente por `IdleTimeoutInSeconds`. Lembre-se de que um ator é considerado como usado se o método de interface de ator ou um retorno de chamada de lembrete de ator for executado. Um ator **não** é considerado usado se seu retorno de chamada do temporizador for executado.

O diagrama a seguir mostra o ciclo de vida de um único ator para ilustrar esses conceitos.

![Exemplo de tempo ocioso][1]

O exemplo mostra o impacto das chamadas de método de ator, lembretes e medidores de tempo no tempo de vida desse ator. Vale a pena mencionar os pontos a seguir sobre o exemplo:

* ScanInterval e IdleTimeout são definidos como 5 e 10, respectivamente. (As unidades não importam aqui, pois nosso objetivo é apenas para ilustrar o conceito.)
* A verificação de atores para terem o lixo coletado ocorre em T = 0, 5, 10, 15, 20, 25, conforme definido pelo ScanInterval de 5.
* Um medidor de tempo periódico é acionado em T = 4, 8, 12, 16, 20, 24 e executa seu retorno de chamada. Ela não afeta o tempo ocioso do ator.
* Uma chamada de método de ator em T = 7, redefine o tempo ocioso para 0 e atrasa a coleta de lixo do ator.
* Um retorno de chamada de lembrete de ator é executado a cada T = 14 e atrasa ainda mais a coleta de lixo do ator.
* Durante a verificação de coleta de lixo em T = 25, o tempo ocioso do ator finalmente excede o IdleTimeout de 10 e o ator tem seu lixo coletado.

Um ator nunca terá o lixo coletado durante a execução de um de seus métodos, não importa quanto tempo seja gasto na execução do método. Como mencionado anteriormente, a execução de métodos de interface de ator e retornos de chamada de lembrete impede a coleta de lixo, redefinindo o tempo ocioso do ator como 0. A execução de retornos de chamada do temporizador não redefine o tempo ocioso para 0. No entanto, a coleta de lixo do ator é adiada até que o retorno de chamada do temporizador tenha concluído a execução.

## <a name="manually-deleting-actors-and-their-state"></a>Excluindo manualmente atores e seu estado
A coleta de lixo dos atores desativados elimina apenas o objeto do ator, mas não remove os dados que são armazenados no Gerenciador de Estado de um ator. Quando um ator é reativado, seus dados são disponibilizados novamente para ele por meio do Gerenciador de Estado. Nos casos em que atores armazenam dados no Gerenciador de Estado e são desativados, mas nunca reativados, pode ser necessário eliminar seus dados.  Para obter exemplos de como excluir atores, leia [Excluir atores e seu estado](service-fabric-reliable-actors-delete-actors.md).

## <a name="next-steps"></a>Próximas etapas
* [Lembretes e temporizadores de ator](service-fabric-reliable-actors-timers-reminders.md)
* [Eventos de ator](service-fabric-reliable-actors-events.md)
* [Reentrância do ator](service-fabric-reliable-actors-reentrancy.md)
* [Diagnóstico e monitoramento de desempenho do ator](service-fabric-reliable-actors-diagnostics.md)
* [Documentação de referência da API do Ator](/previous-versions/azure/dn971626(v=azure.100))
* [Código de exemplo em C#](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Código de exemplo Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-lifecycle/garbage-collection.png
