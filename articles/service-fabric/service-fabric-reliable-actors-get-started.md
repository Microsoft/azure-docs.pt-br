---
title: Criar um serviço baseado em ator no Azure Service Fabric | Microsoft Docs
description: Saiba como criar, depurar e implantar seu primeiro serviço baseado em ator em C# usando Reliable Actors do Service Fabric.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: d4aebe72-1551-4062-b1eb-54d83297f139
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/10/2019
ms.author: vturecek
ms.openlocfilehash: d870690416f96a2e1c24e6de16bdc8faa060f6bd
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "68225138"
---
# <a name="getting-started-with-reliable-actors"></a>Introdução aos Reliable Actors
> [!div class="op_single_selector"]
> * [C# em Windows](service-fabric-reliable-actors-get-started.md)
> * [Java no Linux](service-fabric-reliable-actors-get-started-java.md)

Este artigo explica a criação e depuração de um aplicativo de Reliable Actor simples no Visual Studio. Para mais informações sobre Reliable Actors, consulte [Introdução aos Reliable Actors do Service Fabric](service-fabric-reliable-actors-introduction.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar, assegure-se de que você tenha o ambiente de desenvolvimento do Service Fabric, incluindo o Visual Studio, configurado em seu computador. Para obter mais detalhes, consulte [como configurar o ambiente de desenvolvimento](service-fabric-get-started.md).

## <a name="create-a-new-project-in-visual-studio"></a>Criar um novo projeto no Visual Studio

Inicie o Visual Studio 2019 ou posterior como administrador e, em seguida, crie um novo projeto de **aplicativo Service Fabric** :

![Ferramentas do Service Fabric para Visual Studio – novo projeto][1]

Na próxima caixa de diálogo, escolha **serviço de ator** em **.NET Core 2,0** e insira um nome para o serviço.

![Modelos de projeto do Service Fabric][5]

O projeto criado mostra a seguinte estrutura:

![Estrutura de projeto do Service Fabric][2]

## <a name="examine-the-solution"></a>Examine a solução

A solução contém dois projetos:

* **Projeto de aplicativo (MyApplication)** . Este projeto agrupa todos os serviços para implantação. Ele contém os scripts do PowerShell e o *ApplicationManifest.xml* para gerenciamento do aplicativo.

* **Projeto de interface (HelloWorld.Interfaces)** . Este projeto contém a definição de interface para o ator. As interfaces de ator podem ser definidas em qualquer projeto com qualquer nome.  A interface define o contrato de ator que é compartilhado pela implementação do ator e os clientes que chamam o ator.  Como os projetos de clientes podem depender disso, geralmente faz sentido defini-los em um assembly separado da implementação do ator.

* **Projeto de serviço de ator (HelloWorld)** . Esse projeto define o serviço do Service Fabric que vai hospedar o ator. Ele contém a implementação do ator, *HelloWorld.cs*. Uma implementação de ator é uma classe que deriva do tipo de base `Actor` e implementa as interfaces definidas no projeto *MyActor.Interfaces*. Uma classe de ator também deve implementar um construtor que aceita uma instância `ActorService` e um `ActorId` e as passem para a classe de base `Actor`.
    
    Este projeto também contém *Program.cs*, que registra classes de ator com o tempo de execução do Service Fabric usando `ActorRuntime.RegisterActorAsync<T>()`. A classe `HelloWorld` já está registrada. Todas as implementações de ator adicionais, adicionadas ao projeto, também devem ser registradas no método `Main()`.

## <a name="customize-the-helloworld-actor"></a>Personalizar o ator HelloWorld

O modelo de projeto define alguns métodos na `IHelloWorld` interface e implementa-os na implementação do `HelloWorld`.  Substitua esses métodos para que o serviço de ator retorne uma cadeia de caracteres "Hello World" simples.

No projeto *HelloWorld.Interfaces*, no arquivo *IHelloWorld.cs*, substitua a definição da interface da seguinte maneira:

```csharp
public interface IHelloWorld : IActor
{
    Task<string> GetHelloWorldAsync();
}
```

No projeto **HelloWorld**, em **HelloWorld.cs**, substitua a definição de classe inteira da seguinte maneira:

```csharp
[StatePersistence(StatePersistence.Persisted)]
internal class HelloWorld : Actor, IHelloWorld
{
    public HelloWorld(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<string> GetHelloWorldAsync()
    {
        return Task.FromResult("Hello from my reliable actor!");
    }
}
```

Pressione **Ctrl-Shift-B** para compilar o projeto e certificar-se de que tudo é compilado.

## <a name="add-a-client"></a>Adicionar um cliente

Crie um aplicativo de console simples para chamar o serviço de ator.

1. Clique com o botão direito do mouse na solução no Gerenciador de Soluções > **Adicionar** > **Novo Projeto...** .

2. Nos tipos de projeto **.NET Core**, selecione **Console App (.NET Core)** .  Nomeie o projeto *ActorClient*.
    
    ![Caixa de diálogo Adicionar novo projeto][6]    
    
    > [!NOTE]
    > Um aplicativo de console não é o tipo de aplicativo que você normalmente usaria como cliente no Service Fabric, mas é um exemplo conveniente para depuração e teste com o cluster do Service Fabric local.

3. O aplicativo de console deve ser um aplicativo de 64 bits para manter a compatibilidade com o projeto de interface e outras dependências.  No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **ActorClient**e, em seguida, em **Propriedades**.  Na guia **Compilar**, defina o **destino da plataforma** para **x64**.
    
    ![Compilar propriedades][8]

4. O projeto de cliente requer o pacote NuGet do Reliable Actors .  Clique em **Ferramentas** > **Gerenciador de Pacotes do NuGet** > **Console do Gerenciador de Pacotes**.  No Console do Gerenciador de Pacotes, digite o seguinte comando:
    
    ```powershell
    Install-Package Microsoft.ServiceFabric.Actors -IncludePrerelease -ProjectName ActorClient
    ```

    O pacote NuGet e todas as suas dependências estão instaladas no projeto ActorClient.

5. O projeto de cliente também requer uma referência ao projeto de interfaces.  No projeto ActorClient, clique com o botão direito do mouse em **Dependências** e, em seguida, em **Adicionar referência...** .  Selecione **Projetos > Solução** (se ainda não estiver selecionado) e, em seguida, tique a caixa de seleção ao lado de **HelloWorld.Interfaces**.  Clique em **OK**.
    
    ![Caixa de diálogo Adicionar referência][7]

6. No projeto ActorClient, substitua todo o conteúdo de *Program.cs* com o seguinte código:
    
    ```csharp
    using System;
    using System.Threading.Tasks;
    using Microsoft.ServiceFabric.Actors;
    using Microsoft.ServiceFabric.Actors.Client;
    using HelloWorld.Interfaces;
    
    namespace ActorClient
    {
        class Program
        {
            static void Main(string[] args)
            {
                IHelloWorld actor = ActorProxy.Create<IHelloWorld>(ActorId.CreateRandom(), new Uri("fabric:/MyApplication/HelloWorldActorService"));
                Task<string> retval = actor.GetHelloWorldAsync();
                Console.Write(retval.Result);
                Console.ReadLine();
            }
        }
    }
    ```

## <a name="running-and-debugging"></a>Executar e depurar

Pressione **F5** para criar, implantar e executar o aplicativo localmente no cluster de desenvolvimento do Service Fabric.  Durante o processo de implantação, você poderá ver o andamento na janela **Saída** .

![Janela de saída de depuração do Service Fabric][3]

Quando a saída contiver o texto, *O aplicativo está pronto*, será possível testar o serviço usando o aplicativo ActorClient.  No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **ActorClient**e, em seguida, clique em **Depurar** > **Iniciar nova instância**.  O aplicativo de linha de comando deverá exibir a saída do serviço de ator.

![Saída do aplicativo][9]

> [!TIP]
> O tempo de execução dos Atores do Service Fabric emitem alguns [eventos e contadores de desempenho relacionados aos métodos de ator](service-fabric-reliable-actors-diagnostics.md#actor-method-events-and-performance-counters). Eles são úteis para diagnóstico e monitoramento de desempenho.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre [como os Reliable Actors usam a plataforma do Service Fabric](service-fabric-reliable-actors-platform.md).


[1]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject.PNG
[2]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-projectstructure.PNG
[3]: ./media/service-fabric-reliable-actors-get-started/debugging-output.PNG
[4]: ./media/service-fabric-reliable-actors-get-started/vs-context-menu.png
[5]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject1.PNG
[6]: ./media/service-fabric-reliable-actors-get-started/new-console-app.png
[7]: ./media/service-fabric-reliable-actors-get-started/add-reference.png
[8]: ./media/service-fabric-reliable-actors-get-started/build-props.png
[9]: ./media/service-fabric-reliable-actors-get-started/app-output.png