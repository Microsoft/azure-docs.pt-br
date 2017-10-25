---
title: "Criar seu primeiro microsserviço do Azure baseado em ator em C# | Microsoft Docs"
description: "Este tutorial orienta você pelas etapas de criação, depuração e implantação de um serviço simples baseado em ator usando os Reliable Actors do Service Fabric."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: d4aebe72-1551-4062-b1eb-54d83297f139
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2017
ms.author: vturecek
ms.openlocfilehash: 3f447e049ccd33c77f422e8aa703ad6646f9ffa2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="getting-started-with-reliable-actors"></a>Introdução aos Reliable Actors
> [!div class="op_single_selector"]
> * [C# em Windows](service-fabric-reliable-actors-get-started.md)
> * [Java no Linux](service-fabric-reliable-actors-get-started-java.md)
> 
> 

Este artigo explica os conceitos básicos dos Reliable Actors do Service Fabric e orienta você durante a criação, a depuração e a implantação de um aplicativo Reliable Actor simples no Visual Studio.

## <a name="installation-and-setup"></a>Instalação e configuração
Antes de começar, verifique se há um ambiente de desenvolvimento do Service Fabric configurado no seu computador.
Se você precisa configurá-lo, confira as instruções detalhadas em [Como configurar o ambiente de desenvolvimento](service-fabric-get-started.md).

## <a name="basic-concepts"></a>Conceitos básicos
Para começar a usar os Reliable Actors, você só precisa entender alguns conceitos básicos:

* **Serviço do ator**. Os Reliable Actors são empacotados em Reliable Services que podem ser implantados na infraestrutura do Service Fabric. As instâncias de ator são ativadas em uma instância de serviço nomeado.
* **Registro do ator**. Como ocorre com Reliable Services, um serviço de Reliable Actor precisa ser registrado com o tempo de execução do Service Fabric. Além disso, o tipo de ator precisa ser registrado com o tempo de execução do ator.
* **Interface do Ator**. A interface do ator é usada para definir uma interface pública fortemente tipada de um ator. Na terminologia do modelo de Reliable Actor, a interface do ator define os tipos de mensagem que o ator pode entender e processar. A interface do ator é usada por outros atores ou aplicativos cliente para “enviar” (de modo assíncrono) mensagens ao ator. Os Reliable Actors pode implantar várias interfaces.
* **Classe ActorProxy**. A classe ActorProxy é usada por aplicativos cliente para invocar os métodos expostos por meio de suas interfaces de ator. A classe ActorProxy apresenta duas funcionalidades importantes:
  
  * Resolução do nome: ela consegue localizar o ator no cluster (localizar o nó do cluster no qual ele está hospedado).
  * Tratamento de falhas: ela pode repetir as invocações do método e resolver novamente o local do ator, por exemplo, depois que uma falha exige que ele seja relocado para outro nó no cluster.

Vale a pena mencionar as seguintes regras que pertencem às interfaces de ator:

* Métodos da interface de ator não podem ser sobrecarregados.
* Métodos da interface de ator não podem ter parâmetros de saída, de referência e opcionais.
* Não há suporte para interfaces genéricas.

## <a name="create-a-new-project-in-visual-studio"></a>Criar um novo projeto no Visual Studio
Inicie o Visual Studio 2015 ou 2017 como administrador e crie um novo projeto de aplicativo do Service Fabric:

![Ferramentas do Service Fabric para Visual Studio – novo projeto][1]

Na próxima caixa de diálogo, você pode escolher o tipo de projeto que deseja criar.

![Modelos de projeto do Service Fabric][5]

Para o projeto HelloWorld, vamos usar o serviço Reliable Actors do Service Fabric.

Depois de criar a solução, você verá a seguinte estrutura:

![Estrutura de projeto do Service Fabric][2]

## <a name="reliable-actors-basic-building-blocks"></a>Blocos de construção básicos de Reliable Actors
Uma solução comum do Reliable Actors é composta por três projetos:

* **O projeto do aplicativo (MyActorApplication)**. Esse é o projeto que empacota todos os serviços juntos para implantação. Ele contém os scripts do PowerShell e o *ApplicationManifest.xml* para gerenciamento do aplicativo.
* **O projeto da interface (MyActor.Interfaces)**. Esse é o projeto que contém a definição de interface para o ator. No projeto MyActor.Interfaces, você pode definir as interfaces que serão usadas pelos atores na solução. As interfaces de ator podem ser definidas em qualquer projeto com qualquer nome, mas a interface define o contrato de ator compartilhado pela implementação do ator e os clientes que chamam o ator e, portanto, geralmente faz sentido defini-lo em um assembly separado da implementação do ator e que possa ser compartilhado por vários outros projetos.

```csharp
public interface IMyActor : IActor
{
    Task<string> HelloWorld();
}
```

* **O projeto do serviço de ator (MyActor)**. Esse é o projeto usado para definir o serviço da Malha do Serviço que hospedará o ator. Ele contém a implementação do ator. Uma implementação do ator é uma classe derivada do tipo base `Actor` e implementa as interfaces definidas no projeto MyActor.Interfaces. Uma classe de ator também deve implementar um construtor que aceita uma instância `ActorService` e um `ActorId` e as passem para a classe de base `Actor`. Isso possibilita a injeção de dependência do construtor de dependências de plataforma.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<string> HelloWorld()
    {
        return Task.FromResult("Hello world!");
    }
}
```

O serviço de ator deve ser registrado com um tipo de serviço no tempo de execução do Service Fabric. Para que o Serviço de Ator seja executado em suas instâncias de ator, o tipo de ator também deve ser registrado no Serviço de Ator. O método de registro `ActorRuntime` realiza esse trabalho para os atores.

```csharp
internal static class Program
{
    private static void Main()
    {
        try
        {
            ActorRuntime.RegisterActorAsync<MyActor>(
                (context, actorType) => new ActorService(context, actorType, () => new MyActor())).GetAwaiter().GetResult();

            Thread.Sleep(Timeout.Infinite);
        }
        catch (Exception e)
        {
            ActorEventSource.Current.ActorHostInitializationFailed(e.ToString());
            throw;
        }
    }
}

```

Se você iniciar um novo projeto no Visual Studio e tiver apenas uma definição de ator, o registro será incluído por padrão no código gerado pelo Visual Studio. Se você definir outros atores no serviço, será preciso adicionar o registro do ator usando:

```csharp
 ActorRuntime.RegisterActorAsync<MyOtherActor>();

```

> [!TIP]
> O tempo de execução dos Atores do Service Fabric emitem alguns [eventos e contadores de desempenho relacionados aos métodos de ator](service-fabric-reliable-actors-diagnostics.md#actor-method-events-and-performance-counters). Eles são úteis para diagnóstico e monitoramento de desempenho.
> 
> 

## <a name="debugging"></a>Depurando
As ferramentas para Visual Studio do Service Fabric dão suporte à depuração no computador local. Você pode iniciar uma sessão de depuração pressionando a tecla F5. O Visual Studio cria (se necessário) pacotes. Ele também implanta o aplicativo no cluster do Service Fabric local e anexa o depurador.

Durante o processo de implantação, você poderá ver o andamento na janela **Saída** .

![Janela de saída de depuração do Service Fabric][3]

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre [como os Reliable Actors usam a plataforma do Service Fabric](service-fabric-reliable-actors-platform.md).

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject.PNG
[2]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-projectstructure.PNG
[3]: ./media/service-fabric-reliable-actors-get-started/debugging-output.PNG
[4]: ./media/service-fabric-reliable-actors-get-started/vs-context-menu.png
[5]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject1.PNG
