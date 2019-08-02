---
title: Criar seu primeiro aplicativo do Service Fabric em C# | Microsoft Docs
description: Introdução à criação de um aplicativo do Service Fabric do Microsoft Azure com serviços com e sem estado.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: d9b44d75-e905-468e-b867-2190ce97379a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/10/2019
ms.author: vturecek
ms.openlocfilehash: f3b3d5c3dcea7d190724ae946a27c47b34a26c31
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "68225025"
---
# <a name="get-started-with-reliable-services"></a>Introdução aos Reliable Services
> [!div class="op_single_selector"]
> * [C# em Windows](service-fabric-reliable-services-quick-start.md)
> * [Java no Linux](service-fabric-reliable-services-quick-start-java.md)
> 
> 

Um aplicativo do Service Fabric do Azure contém um ou mais serviços que executam seu código. Este guia mostra como criar aplicativos Service Fabric com e sem estado usando os [Reliable Services](service-fabric-reliable-services-introduction.md).  

## <a name="basic-concepts"></a>Conceitos básicos
Para começar a usar os Reliable Services, você só precisa entender alguns conceitos básicos:

* **Tipo de serviço**: esta é sua implementação de serviço. Ele é definido pela classe que você escreve que estende `StatelessService` e qualquer outro código ou dependências usadas nele, juntamente com um nome e um número de versão.
* **Instância de serviço nomeada**: para executar seu serviço, criar instâncias nomeadas do tipo de serviço, bem como criar instâncias de objeto de um tipo de classe. Uma instância de serviço tem um nome na forma de um URI usando o esquema "fabric: /", por exemplo, "fabric:/MyApp/MyService".
* **Host de serviço**: As instâncias de serviço nomeadas que você criar têm que ser executadas dentro do processo de um host. O host de serviço é apenas um processo em que instâncias do serviço podem ser executadas.
* **Registro do serviço**: o registro reúne tudo. O tipo de serviço deve ser registrado com o tempo de execução do Service Fabric em um host de serviço para permitir que o Service Fabric crie instâncias para executar.  

## <a name="create-a-stateless-service"></a>Criar um serviço sem estado
Um serviço sem estado é um tipo de serviço que atualmente está na norma dos aplicativos em nuvem. Ele é considerado sem estado porque o serviço em si não contém dados que precisam ser armazenados de modo confiável nem altamente disponibilizados. Se uma instância de um serviço sem estado for desligada, todo seu estado interno será perdido. Nesse tipo de serviço, o estado deve ser mantido em um repositório externo, como em Tabelas do Azure ou um banco de dados SQL, para que ele se torne altamente disponível e confiável.

Inicie o Visual Studio 2017 ou o Visual Studio 2019 como administrador e crie um novo projeto de aplicativo Service Fabric chamado *HelloWorld*:

![Usar a caixa de diálogo Novo Projeto para criar um novo aplicativo do Service Fabric](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject.png)

Em seguida, crie um projeto de serviço sem estado usando o **.NET Core 2,0** chamado *HelloWorldStateless*:

![Na segunda caixa de diálogo, criar um projeto de serviço sem estado](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject2.png)

Agora, sua solução contém dois projetos:

* *HelloWorld*. Esse é o projeto de *aplicativo* que contém seus *serviços*. Ele também contém o manifesto do aplicativo que descreve o aplicativo, bem como diversos scripts do PowerShell que ajudam a implantar o aplicativo.
* *HelloWorldStateless*. Esse é o projeto de serviço. Ele contém a implementação do serviço sem estado.

## <a name="implement-the-service"></a>Implementar o serviço
Abra o arquivo **HelloWorldStateless.cs** no projeto de serviço. No Service Fabric, um serviço pode executar qualquer lógica de negócios. A API de serviço fornece dois pontos de entrada para seu código:

* Um método de ponto de entrada em aberto chamado *RunAsync*, em que você pode começar a executar qualquer carga de trabalho, incluindo cargas de trabalho de computação de longa duração.

```csharp
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    ...
}
```

* Um ponto de entrada de comunicação em que você pode conectar a pilha de comunicação de sua escolha como o ASP.NET Core. É onde você pode começar a receber solicitações de usuários e outros serviços.

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    ...
}
```

Neste tutorial, enfatizaremos o método de ponto de entrada `RunAsync()` . É aqui que você pode começar imediatamente a executar seu código.
O modelo de projeto inclui um exemplo de implementação de `RunAsync()` que incrementa uma contagem progressiva.

> [!NOTE]
> Para obter detalhes sobre como trabalhar com uma pilha de comunicação, confira [Serviços de API do Service Fabric com auto-hospedagem do OWIN](service-fabric-reliable-services-communication-webapi.md)
> 
> 

### <a name="runasync"></a>RunAsync
```csharp
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    // TODO: Replace the following sample code with your own logic
    //       or remove this RunAsync override if it's not needed in your service.

    long iterations = 0;

    while (true)
    {
        cancellationToken.ThrowIfCancellationRequested();

        ServiceEventSource.Current.ServiceMessage(this.Context, "Working-{0}", ++iterations);

        await Task.Delay(TimeSpan.FromSeconds(1), cancellationToken);
    }
}
```

A plataforma chama esse método quando uma instância de um serviço é estabelecida e está pronta para execução. Para um serviço sem estado, isso simplesmente significa quando a instância do serviço é aberta. Um token de cancelamento é fornecido para coordenar quando sua instância de serviço deve ser fechada. No Service Fabric, esse ciclo de abertura/fechamento de uma instância de serviço pode ocorrer várias vezes durante a vida útil do serviço como um todo. Isso pode ocorrer por vários motivos, incluindo:

* O sistema move as instâncias de serviço para balanceamento de recursos.
* Ocorrem falhas no código.
* O aplicativo ou sistema é atualizado.
* O hardware subjacente sofre uma interrupção.

Essa orquestração é gerenciada pelo sistema a fim de manter o serviço altamente disponível e devidamente balanceado.

`RunAsync()` não deve bloquear sincronicamente. Sua implementação de RunAsync deve retornar uma tarefa ou esperar operações de execução longa ou de bloqueio para permitir que o tempo de execução continue. Observe que no loop `while(true)` no exemplo anterior, um retorno de tarefa `await Task.Delay()` é usado. Se sua carga de trabalho deve bloquear sincronicamente, agende uma nova tarefa com `Task.Run()` na sua implementação `RunAsync`.

O cancelamento da sua carga de trabalho é um esforço cooperativo orquestrado pelo token de cancelamento fornecido. O sistema aguardará o encerramento da tarefa (por conclusão bem-sucedida, cancelamento ou falha) antes de prosseguir. É importante honrar o token de cancelamento, concluir qualquer trabalho e sair do `RunAsync()` o mais rapidamente possível quando o sistema solicita o cancelamento.

Neste exemplo de serviço sem estado, a contagem é armazenada em uma variável local. Mas como esse é um serviço sem estado, o valor que é armazenado existe apenas para o ciclo de vida atual da sua instância de serviço. Quando o serviço se move ou é reiniciado, o valor é perdido.

## <a name="create-a-stateful-service"></a>Criar um serviço com estado
O Service Fabric introduz um novo tipo de serviço que é com estado. Um serviço com estado pode manter o estado de maneira confiável dentro do próprio serviço, localizado em conjunto com o código que o está usando. O estado é altamente disponibilizado pelo Service Fabric sem a necessidade de persistir o estado em um repositório externo.

Para converter o valor de contador de sem estado para altamente disponível e persistente, mesmo quando o serviço for movido ou reiniciado, você precisa de um serviço com estado.

No mesmo aplicativo *HelloWorld*, é possível adicionar um novo serviço clicando com o botão direito do mouse nas referências Serviços no projeto de aplicativo e selecionando **Adicionar -> Novo Serviço Service Fabric**.

![Adicione um serviço ao aplicativo da Malha de Serviços](media/service-fabric-reliable-services-quick-start/hello-stateful-NewService.png)

Selecione **.NET Core 2,0-> serviço com estado** e nomeie-o *HelloWorldStateful*. Clique em **OK**.

![Usar a caixa de diálogo Novo Projeto para criar um novo serviço com estado do Service Fabric](media/service-fabric-reliable-services-quick-start/hello-stateful-NewProject.png)

Seu aplicativo agora deve ter dois serviços: o serviço sem estado *HelloWorldStateless* e o serviço com estado *HelloWorldStateful*.

Um serviço com estado tem os mesmos pontos de entrada que um serviço sem estado. A principal diferença é a disponibilidade de um *provedor de estado* que pode armazenar estados de maneira confiável. O Service Fabric é fornecido com uma implementação de provedor de estado chamada [Coleções Confiáveis](service-fabric-reliable-services-reliable-collections.md), que permite que você crie estruturas de dados replicados por meio do Gerenciador de Estado Confiável. Por padrão, um serviço confiável com estado usa esse provedor de estado.

Abra **HelloWorldStateful.cs** em *HelloWorldStateful*, que contém o método RunAsync a seguir:

```csharp
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    // TODO: Replace the following sample code with your own logic
    //       or remove this RunAsync override if it's not needed in your service.

    var myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");

    while (true)
    {
        cancellationToken.ThrowIfCancellationRequested();

        using (var tx = this.StateManager.CreateTransaction())
        {
            var result = await myDictionary.TryGetValueAsync(tx, "Counter");

            ServiceEventSource.Current.ServiceMessage(this.Context, "Current Counter Value: {0}",
                result.HasValue ? result.Value.ToString() : "Value does not exist.");

            await myDictionary.AddOrUpdateAsync(tx, "Counter", 0, (key, value) => ++value);

            // If an exception is thrown before calling CommitAsync, the transaction aborts, all changes are
            // discarded, and nothing is saved to the secondary replicas.
            await tx.CommitAsync();
        }

        await Task.Delay(TimeSpan.FromSeconds(1), cancellationToken);
    }
```

### <a name="runasync"></a>RunAsync
`RunAsync()` opera da mesma forma em serviços com e sem estado. No entanto, em um serviço com estado, a plataforma executa trabalho adicional em seu nome antes de executar `RunAsync()`. Esse trabalho pode incluir garantir que o Gerenciador de Estado Confiável e as Coleções Confiáveis estejam prontos para uso.

### <a name="reliable-collections-and-the-reliable-state-manager"></a>Coleções Confiáveis e Gerenciador de Estado Confiável
```csharp
var myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");
```

[IReliableDictionary](https://msdn.microsoft.com/library/dn971511.aspx) é uma implementação de dicionário que você pode usar para armazenar o estado no serviço de forma confiável. Com o Service Fabric e as Reliable Collections, você agora pode armazenar dados diretamente em seu serviço sem a necessidade de um repositório persistente externo. As Coleções Confiáveis tornam os dados altamente disponíveis. O Service Fabric consegue isso criando e gerenciando várias *réplicas* do seu serviço para você. Ele também fornece uma API que abstrai as complexidades de gerenciar essas réplicas e as respectivas transições de estado.

As Reliable Collections podem armazenar qualquer tipo .NET, incluindo tipos personalizados, com algumas limitações:

* O Service Fabric torna seu estado altamente disponível *replicando* o estado entre nós, e as Coleções Confiáveis armazenam seus dados no disco local em cada réplica. Isso significa que tudo o que é armazenado nas Coleções Confiáveis deve ser *serializável*. Por padrão, as Coleções Confiáveis usam [DataContract](https://msdn.microsoft.com/library/system.runtime.serialization.datacontractattribute%28v=vs.110%29.aspx) para serialização, de modo que é importante verificar se seus tipos têm [suporte do Serializador de Contrato de Dados](https://msdn.microsoft.com/library/ms731923%28v=vs.110%29.aspx) quando você usa o serializador padrão.
* Os objetos são replicados para alta disponibilidade quando você confirma uma transação nas Reliable Collections. Objetos armazenados nas Reliable Collections são mantidos na memória local do serviço. Isso significa que você tem uma referência local para o objeto.
  
   É importante que você não modifique instâncias locais desses objetos sem executar uma operação de atualização na coleção confiável em uma transação. Isso ocorre porque as mudanças para instâncias de objetos locais não serão replicadas automaticamente. Você deve inserir novamente o objeto de volta no dicionário ou usar um dos métodos *atualizar* do dicionário.

O Gerenciador De Estado Confiável gerencia as Coleções Confiáveis para você. Basta solicitar ao Gerenciador de Estado Confiável uma coleção confiável por nome a qualquer momento e em qualquer lugar no seu serviço. O Gerenciador de Estado Confiável assegura que você obtenha uma referência de volta. Não é recomendável salvar referências nas instâncias de coleção confiável em propriedades ou variáveis de membro de classe. É preciso tomar muito cuidado para garantir que a referência seja definida para uma instância o tempo todo no ciclo de vida do serviço. O Gerenciador de Estado Confiável faz esse trabalho para você e jé otimizado para repetir visitas.

### <a name="transactional-and-asynchronous-operations"></a>Operações transacionais e assíncronas
```csharp
using (ITransaction tx = this.StateManager.CreateTransaction())
{
    var result = await myDictionary.TryGetValueAsync(tx, "Counter-1");

    await myDictionary.AddOrUpdateAsync(tx, "Counter-1", 0, (k, v) => ++v);

    await tx.CommitAsync();
}
```

As Coleções Confiáveis têm muitas das mesmas operações que suas correspondentes `System.Collections.Generic` e `System.Collections.Concurrent`, incluindo o LINQ. As operações nas Coleções Confiáveis são assíncronas. Isso ocorre porque as operações de gravação nas Coleções Confiáveis executam operações de E/S para replicar e manter os dados no disco.

As operações de Coleção Confiável são *transacionais*, de modo que você pode manter o estado consistente entre várias Coleções Confiáveis e operações. Por exemplo, você pode remover um item de trabalho de uma Fila Confiável, executar uma operação nele e salvar o resultado em um Dicionário Confiável, tudo em uma única transação. Trata-se de uma operação atômica e ela garante que toda a operação seja bem-sucedida ou revertida. Se ocorrer um erro depois de remover o item da fila, mas antes de salvar o resultado, toda a transação será revertida e o item permanecerá na fila para processamento.

## <a name="run-the-application"></a>Executar o aplicativo
Agora retornamos ao aplicativo *HelloWorld* . Agora você pode criar e implantar seus serviços. Quando você pressionar **F5**, seu aplicativo será criado e implantado no cluster local.

Depois que os serviços começaram a ser executados, você poderá exibir os eventos ETW (Rastreamento de Eventos para Windows) gerados em uma janela **Eventos de Diagnóstico** . Observe que os eventos exibidos são dos serviços sem e com estado do aplicativo. Você pode pausar o fluxo clicando no botão **Pausar** . Assim, você pode examinar os detalhes de uma mensagem expandindo-a.

> [!NOTE]
> Antes de executar o aplicativo, verifique a existência de um cluster de desenvolvimento local em execução. Confira o [guia de introdução](service-fabric-get-started.md) para obter informações sobre como configurar o ambiente local.
> 
> 

![Exibir Eventos de Diagnóstico no Visual Studio](media/service-fabric-reliable-services-quick-start/hello-stateful-Output.png)

## <a name="next-steps"></a>Próximas etapas
[Depurar seu aplicativo do Service Fabric usando o Visual Studio](service-fabric-debugging-your-application.md)

[Introdução: Serviços de API Web do Service Fabric com auto-hospedagem OWIN](service-fabric-reliable-services-communication-webapi.md)

[Saiba mais sobre as Reliable Collections](service-fabric-reliable-services-reliable-collections.md)

[Implantar um aplicativo](service-fabric-deploy-remove-applications.md)

[Atualização de aplicativo](service-fabric-application-upgrade.md)

[Referência do desenvolvedor para Reliable Services](https://msdn.microsoft.com/library/azure/dn706529.aspx)

