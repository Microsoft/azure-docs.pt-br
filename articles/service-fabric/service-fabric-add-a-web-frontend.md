---
title: Criar um front-end da Web para seu aplicativo do Azure Service Fabric usando ASP.NET Core | Microsoft Docs
description: "Exponha seu aplicativo do Service Fabric na Web usando um projeto de ASP.NET Core a e comunicação entre serviços via Service Remoting."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: 96176149-69bb-4b06-a72e-ebbfea84454b
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/01/2017
ms.author: vturecek
ms.openlocfilehash: b19aaa652f2c15573ded632ca1348e1a6752f080
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="build-a-web-service-front-end-for-your-application-using-aspnet-core"></a>Compilar um serviço Web front-end para seu aplicativo usando ASP.NET Core
Por padrão, os serviços do Azure Service Fabric não fornecem uma interface pública para a Web. Para expor a funcionalidade do aplicativo para clientes HTTP, você precisa criar um projeto Web para atuar como um ponto de entrada e, depois, se comunicar nele com os serviços individuais.

Neste tutorial, continuamos de onde paramos no tutorial [Criando seu primeiro aplicativo no Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md) e adicionamos um serviço Web do ASP.NET Core antes do serviço de contador com estado. Se ainda não tiver feito isso, volte e percorra esse tutorial primeiro.

## <a name="set-up-your-environment-for-aspnet-core"></a>Configurar o ambiente para o ASP.NET Core

É necessário o Visual Studio 2017 para acompanhar este tutorial. Qualquer edição será suficiente. 

 - [Instalar o Visual Studio 2017](https://www.visualstudio.com/)

Para desenvolver aplicativos ASP.NET Core do Service Fabric, você deve ter as seguintes cargas de trabalho instaladas:
 - **Desenvolvimento do Azure** (em *Web e Nuvem*)
 - **Desenvolvimento do ASP.NET e para a Web** (em *Web e Nuvem*)
 - **Desenvolvimento de plataforma cruzada do .NET Core** (em *Outros Conjuntos de Ferramentas*)

>[!Note] 
>As ferramentas do .NET Core para Visual Studio 2015 não estão sendo mais atualizadas. No entanto, se você ainda estiver usando o Visual Studio 2015, precisará ter as [Ferramentas do .NET Core VS 2015 Versão Prévia 2](https://www.microsoft.com/net/download/core) instaladas.

## <a name="add-an-aspnet-core-service-to-your-application"></a>Adicionar um serviço ASP.NET Core ao seu aplicativo
O ASP.NET Core é uma estrutura de desenvolvimento Web leve entre plataformas que permite a criação de uma interface do usuário Web e APIs Web modernas. 

Vamos adicionar um projeto de API Web do ASP.NET a nosso aplicativo existente.

1. No Gerenciador de Soluções, clique com o botão direito do mouse em **Serviços** no projeto de aplicativo e escolha **Adicionar > Novo Serviço do Fabric Service**.
   
    ![Adicionando um novo serviço a um aplicativo existente][vs-add-new-service]
2. Na página **Criar um Serviço**, escolha **ASP.NET Core** e dê um nome a ele.
   
    ![Escolhendo um serviço Web ASP.NET no diálogo Novo serviço][vs-new-service-dialog]

3. A próxima página fornece um conjunto de modelos de projeto do ASP.NET Core. Observe que essas são as mesmas opções que você veria se você tivesse criado um projeto do ASP.NET Core fora de um aplicativo do Service Fabric, com uma pequena quantidade de código adicional para registrar o serviço com o tempo de execução do Service Fabric. Neste tutorial, escolhemos **API Web**. No entanto, você pode aplicar os mesmos conceitos para compilar um aplicativo Web completo.
   
    ![Escolhendo o tipo de projeto do ASP.NET][vs-new-aspnet-project-dialog]
   
    Depois de criar o projeto de API Web, você deverá ter dois serviços no aplicativo. Durante a criação do aplicativo, você pode adicionar mais serviços exatamente da mesma forma. Cada um pode seu próprio controle de versão e ser atualizado de forma independente.

## <a name="run-the-application"></a>Executar o aplicativo
Para ter uma ideia do que fizemos, vamos implantar o novo aplicativo e examinar o comportamento padrão apresentado pelo modelo de API Web do ASP.NET Core.

1. Pressione F5 no Visual Studio para depurar o aplicativo.
2. Quando a implantação for concluída, o Visual Studio iniciará um navegador na raiz do serviço de API Web ASP.NET. O modelo da API Web ASP.NET Core não fornece um comportamento padrão para a raiz; portanto, você deverá receber um erro 404 no navegador.
3. Adicione `/api/values` ao local no navegador. Isso invocará o método `Get` no ValuesController no modelo de API Web. Ele retorna a resposta padrão fornecida pelo modelo – uma matriz JSON que contém duas cadeias de caracteres:
   
    ![Valores padrão retornados do modelo de API Web do ASP.NET Core][browser-aspnet-template-values]
   
    Ao final do tutorial, esta página mostrará o valor mais recente do contador de nosso serviço com estado, em vez das cadeias de caracteres padrão.

## <a name="connect-the-services"></a>Conectar os serviços
O Service Fabric fornece total flexibilidade na comunicação com Reliable Services. Em um único aplicativo, você pode ter serviços acessíveis por meio de TCP, outros serviços acessíveis por meio de uma API REST HTTP e ainda outros serviços que são acessíveis por meio de soquetes da Web. Para saber mais sobre as opções disponíveis e as compensações envolvidas, confira [Comunicação com os serviços](service-fabric-connect-and-communicate-with-services.md). Neste tutorial, usamos a [Comunicação Remota do Serviço do Service Fabric](service-fabric-reliable-services-communication-remoting.md), fornecida no SDK.

Na abordagem da Comunicação Remota do Serviço (modelada em chamadas de procedimento remoto ou RPCs), você define uma interface para atuar como o contrato público do serviço. Em seguida, você usa essa interface para gerar uma classe proxy para interação com o serviço.

### <a name="create-the-remoting-interface"></a>Criar a interface de comunicação remota
Vamos começar criando a interface para atuar como o contrato entre o serviço com estado e outros serviços, neste caso, o projeto Web ASP.NET Core. Essa interface deve ser compartilhada por todos os serviços que a usam para fazer chamadas RPC. Portanto, nós a criaremos em seu próprio projeto de Biblioteca de Classes.

1. No Gerenciador de Soluções, clique com o botão direito do mouse na solução e escolha **Adicione** > **Novo Projeto**.

2. Escolha a entrada do **Visual C#** no painel de navegação esquerdo e selecione o modelo **Biblioteca de Classes**. Verifique se a versão do .NET Framework está definida como **4.5.2**.
   
    ![Criando um projeto de interface para o serviço com estado][vs-add-class-library-project]

3. Instale o pacote NuGet **Microsoft.ServiceFabric.Services.Remoting**. Pesquise **Microsoft.ServiceFabric.Services.Remoting** no gerenciador de pacotes NuGet e instale-o para todos os projetos na solução que usam a Comunicação Remota do Serviço, incluindo:
   - O projeto de Biblioteca de Classes que contém a interface do serviço
   - O projeto de Serviço com Estado
   - O projeto de serviço Web ASP.NET Core
   
    ![Adicionar o pacote NuGet de serviços][vs-services-nuget-package]

4. Na biblioteca de classes, crie uma interface com um único método, `GetCountAsync` e estenda a interface por meio do `Microsoft.ServiceFabric.Services.Remoting.IService`. A interface de comunicação remota deve derivar dessa interface para indicar que ela é uma interface da Comunicação Remota do Serviço.
   
    ```c#
    using Microsoft.ServiceFabric.Services.Remoting;
    using System.Threading.Tasks;
        
    ...

    namespace MyStatefulService.Interface
    {
        public interface ICounter: IService
        {
            Task<long> GetCountAsync();
        }
    }
    ```

### <a name="implement-the-interface-in-your-stateful-service"></a>Implemente a interface em seu serviço com estado
Agora que definimos a interface, precisamos implementá-la no serviço com estado.

1. Em seu serviço com estado, adicione uma referência ao projeto de biblioteca de classes que contém a interface.
   
    ![Adicionando uma referência ao projeto de biblioteca de classes ao serviço com estado][vs-add-class-library-reference]
2. Localize a classe que herda de `StatefulService`, como `MyStatefulService`, e a estenda para implementar a interface `ICounter`.
   
    ```c#
    using MyStatefulService.Interface;
   
    ...
   
    public class MyStatefulService : StatefulService, ICounter
    {        
         ...
    }
    ```
3. Agora, implemente o método único definido na interface `ICounter`, `GetCountAsync`.
   
    ```c#
    public async Task<long> GetCountAsync()
    {
        var myDictionary = 
            await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");
   
        using (var tx = this.StateManager.CreateTransaction())
        {          
            var result = await myDictionary.TryGetValueAsync(tx, "Counter");
            return result.HasValue ? result.Value : 0;
        }
    }
    ```

### <a name="expose-the-stateful-service-using-a-service-remoting-listener"></a>Expor o serviço com estado usando um ouvinte de comunicação remota do serviço
Com a interface `ICounter` implementada, a etapa final é abrir o canal de comunicação da Comunicação Remota do Serviço. Para serviços com estado, o Service Fabric fornece um método substituível chamado `CreateServiceReplicaListeners`. Com esse método, você pode especificar um ou mais ouvintes de comunicação, com base no tipo de comunicação que você deseja habilitar para o serviço.

> [!NOTE]
> O método equivalente para abrir um canal de comunicação para serviços sem estado é chamado `CreateServiceInstanceListeners`.

Nesse caso, substituímos o método `CreateServiceReplicaListeners` existente e fornecemos uma instância do `ServiceRemotingListener`, que cria um ponto de extremidade RPC que pode ser chamado por clientes por meio do `ServiceProxy`.  

O método de extensão `CreateServiceRemotingListener` na interface `IService` permite que você crie facilmente um `ServiceRemotingListener` com todas as configurações padrão. Para usar esse método de extensão, verifique se você importou o namespace `Microsoft.ServiceFabric.Services.Remoting.Runtime`. 

```c#
using Microsoft.ServiceFabric.Services.Remoting.Runtime;

...

protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new List<ServiceReplicaListener>()
    {
        new ServiceReplicaListener(
            (context) =>
                this.CreateServiceRemotingListener(context))
    };
}
```


### <a name="use-the-serviceproxy-class-to-interact-with-the-service"></a>Usar a classe ServiceProxy para interagir com o serviço
Agora, nosso serviço com estado está pronto para receber o tráfego de outros serviços na RPC. Assim, tudo o que resta é adicionar o código para se comunicar com ele a partir do serviço Web ASP.NET.

1. Em seu projeto ASP.NET, adicione uma referência à biblioteca de classes que contém a interface `ICounter` .

2. Anteriormente, você adicionou o pacote NuGet **Microsoft.ServiceFabric.Services.Remoting** ao projeto ASP.NET. Esse pacote fornece a classe `ServiceProxy`, que você usa para fazer chamadas RPC ao serviço com estado. Verifique se o pacote NuGet está instalado no projeto de serviço Web ASP.NET Core.

4. Na pasta **Controladores**, abra a classe `ValuesController`. Observe que o método `Get` atualmente retorna somente uma matriz de cadeia de caracteres codificados "value1" e "value2", que coincide com o que vimos anteriormente no navegador. Substitua essa implementação pelo seguinte código:
   
    ```c#
    using MyStatefulService.Interface;
    using Microsoft.ServiceFabric.Services.Client;
    using Microsoft.ServiceFabric.Services.Remoting.Client;
   
    ...

    [HttpGet]
    public async Task<IEnumerable<string>> Get()
    {
        ICounter counter =
            ServiceProxy.Create<ICounter>(new Uri("fabric:/MyApplication/MyStatefulService"), new ServicePartitionKey(0));
   
        long count = await counter.GetCountAsync();
   
        return new string[] { count.ToString() };
    }
    ```
   
    A primeira linha de código é a principal. Para criar o proxy ICounter para o serviço com estado, você precisa fornecer duas informações: uma ID de partição e o nome do serviço.
   
    Você pode usar o particionamento para dimensionar serviços com estado dividindo seu estado em diferentes recipientes com base em uma chave definida por você, como ID de cliente ou CEP. Em nosso aplicativo comum, o serviço com estado tem somente uma partição, portanto, a chave não importa. Qualquer chave que você fornecer levará até a mesma partição. Confira [Como particionar Reliable Services do Service Fabric](service-fabric-concepts-partitioning.md)para saber mais sobre como particionar seu serviço.
   
    O nome do serviço é um URI de malha:/&lt;application_name&gt;/&lt;service_name&gt;.
   
    Com esses dois tipos de informação, o Service Fabric pode identificar exclusivamente o computador para o qual as solicitações devem ser enviadas. A classe `ServiceProxy` também cuidará da situação no caso de a máquina que hospeda a partição do serviço com estado falhar e outra máquina tiver que ser promovida em seu lugar. Essa abstração torna a escrita de código do cliente para lidar com outros serviço bem mais simples.
   
    Assim que tivermos o proxy, podemos simplesmente chamar o método `GetCountAsync` e retornar seu resultado.

5. Pressione F5 novamente para executar o aplicativo modificado. Como antes, o Visual Studio inicia o navegador automaticamente na raiz do projeto Web. Adicione o caminho "api/values" e você deve ver o valor atual do contador retornado.
   
    ![O valor do contador com estado exibido no navegador][browser-aspnet-counter-value]
   
    Atualize o navegador periodicamente para ver o valor do contador de atualização.

## <a name="kestrel-and-weblistener"></a>Kestrel e WebListener

O servidor Web do ASP.NET Core padrão, conhecido como Kestrel, [não tem suporte no momento para lidar com o tráfego direto da Internet](https://docs.microsoft.com/aspnet/core/fundamentals/servers/kestrel). Como resultado, o modelo de serviço sem estado do ASP.NET Core do Service Fabric usa o [WebListener](https://docs.microsoft.com/aspnet/core/fundamentals/servers/weblistener) por padrão. 

Para saber mais sobre Kestrel e WebListener nos serviços do Service Fabric, consulte [ASP.NET Core em Serviços Confiáveis do Service Fabric](service-fabric-reliable-services-communication-aspnetcore.md).

## <a name="connecting-to-a-reliable-actor-service"></a>Conectando-se a um serviço do Reliable Actor
Este tutorial se concentra em adicionar um front-end da Web que se comunique com um serviço com estado. No entanto, você pode seguir um modelo muito semelhante ao conversar com atores. Quando você cria um projeto do Reliable Actor, o Visual Studio gera automaticamente um projeto de interface para você. Você pode usar essa interface para gerar um proxy de ator no projeto Web para se comunicar com o ator. O canal de comunicação é fornecido automaticamente. Assim, não é necessário fazer nada equivalente a estabelecer um `ServiceRemotingListener` , como foi feito para o serviço com estado neste tutorial.

## <a name="how-web-services-work-on-your-local-cluster"></a>Como os serviços Web funcionam no cluster local
Em geral, você pode implantar exatamente o mesmo aplicativo do Service Fabric em um cluster com vários computadores implantados no cluster local e ter a certeza de que ele funciona como esperado. Isso ocorre porque o cluster local é simplesmente uma configuração de cinco nós recolhidos em um único computador.

No entanto, quando se trata de serviços Web, há uma nuance essencial. Quando o cluster fica atrás de um balanceador de carga, como é o caso no Azure, você deve garantir que os serviços Web são implantados em todos os computadores, já que o balanceador de carga simplesmente faz round-robin com o tráfego entre os computadores. Isso pode ser feito configurando `InstanceCount` do serviço para o valor especial “-1”.

Por outro lado, quando você executa um serviço Web localmente, você precisa garantir que apenas uma instância de serviço esteja em execução. Caso contrário, você terá conflitos de vários processos que estão escutando o mesmo caminho e a mesma porta. Como resultado, a contagem de instâncias de serviço Web deve ser definida como "1" para implantações locais.

Para saber como configurar valores diferentes para um ambiente diferente, confira [Gerenciar parâmetros de aplicativo para vários ambientes](service-fabric-manage-multiple-environment-app-configuration.md).

## <a name="next-steps"></a>Próximas etapas
Agora que você tem um front-end da Web configurado para o aplicativo com o ASP.NET Core, saiba mais sobre o [ASP.NET Core nos Reliable Services do Service Fabric](service-fabric-reliable-services-communication-aspnetcore.md) para obter uma compreensão detalhada de como o ASP.NET Core funciona com o Service Fabric.

Em seguida, [saiba mais sobre a comunicação com os serviços](service-fabric-connect-and-communicate-with-services.md) em geral para ter uma ideia completo de como a comunicação do serviço funciona no Service Fabric.

Assim que você tiver uma boa compreensão de como a comunicação do serviço funciona, [cria um cluster no Azure e implanta o aplicativo na nuvem](service-fabric-cluster-creation-via-portal.md).

<!-- Image References -->

[vs-add-new-service]: ./media/service-fabric-add-a-web-frontend/vs-add-new-service.png
[vs-new-service-dialog]: ./media/service-fabric-add-a-web-frontend/vs-new-service-dialog.png
[vs-new-aspnet-project-dialog]: ./media/service-fabric-add-a-web-frontend/vs-new-aspnet-project-dialog.png
[browser-aspnet-template-values]: ./media/service-fabric-add-a-web-frontend/browser-aspnet-template-values.png
[vs-add-class-library-project]: ./media/service-fabric-add-a-web-frontend/vs-add-class-library-project.png
[vs-add-class-library-reference]: ./media/service-fabric-add-a-web-frontend/vs-add-class-library-reference.png
[vs-services-nuget-package]: ./media/service-fabric-add-a-web-frontend/vs-services-nuget-package.png
[browser-aspnet-counter-value]: ./media/service-fabric-add-a-web-frontend/browser-aspnet-counter-value.png
[vs-create-platform]: ./media/service-fabric-add-a-web-frontend/vs-create-platform.png


<!-- external links -->
[dotnetcore-install]: https://www.microsoft.com/net/core#windows
