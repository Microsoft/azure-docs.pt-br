---
title: "Introdução às retransmissões WCF da Retransmissão do Azure no .NET | Microsoft Docs"
description: "Saiba como usar as retransmissões WCF da Retransmissão do Azure para conectar dois aplicativos hospedados em locais diferentes."
services: service-bus-relay
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 5493281a-c2e5-49f2-87ee-9d3ffb782c75
ms.service: service-bus-relay
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/23/2017
ms.author: sethm
ms.openlocfilehash: 1af1ac78398d65e6a87f0d24d6198f3dfbc82ffd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-azure-relay-wcf-relays-with-net"></a>Como usar as retransmissões WCF da Retransmissão do Azure com .NET
Este artigo descreve como usar o serviço Retransmissão do Azure. Os exemplos são escritos em C# e usam a API da WCF (Windows Communication Foundation) com extensões contidas no assembly do Barramento de Serviço. Para obter mais informações sobre a retransmissão do Azure, consulte a [Visão geral da Retransmissão do Azure](relay-what-is-it.md).

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="what-is-wcf-relay"></a>O que é Retransmissão do WCF?

O serviço [*Retransmissão do WCF*](relay-what-is-it.md) do Azure permite criar aplicativos híbridos que executam no datacenter do Azure e em seu próprio ambiente corporativo local. O serviço de retransmissão facilita isso permitindo que você exponha com segurança os serviços do WCF (Windows Communication Foundation) que residem em uma rede corporativa à nuvem pública, sem precisar abrir uma conexão de firewall nem exigir alterações intrusivas em uma infraestrutura de rede corporativa.

![Conceitos de retransmissão do WCF](./media/service-bus-dotnet-how-to-use-relay/sb-relay-01.png)

A Retransmissão do Azure permite que você hospede serviços WCF em seu ambiente corporativo existente. Em seguida, você pode delegar a escuta de sessões e solicitações de entrada para esses serviços WCF ao serviço de retransmissão em execução no Azure. Isso permite que você exponha esses serviços para o código do aplicativo em execução no Azure ou para usuários móveis ou ambientes de parceiros na extranet. A Retransmissão permite controlar com segurança quem pode acessar esses serviços em um nível refinado. Ele fornece uma maneira poderosa e segura de exibir a funcionalidade do aplicativo e os dados de suas soluções corporativas existentes e tira proveito disso na nuvem.

Este artigo aborda como usar a Retransmissão do Azure para criar um serviço Web do WCF, exposto usando uma associação de canal TCP, que implementa uma conversa segura entre duas entidades.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="get-the-service-bus-nuget-package"></a>Obtenha o pacote do NuGet do Barramento de Serviço
O [pacote NuGet de Barramento de Serviço](https://www.nuget.org/packages/WindowsAzure.ServiceBus) é a maneira mais fácil de obter a API do Barramento de Serviço e configurar seu aplicativo com todas as dependências do Barramento de Serviço. Para instalar o pacote do NuGet em seu projeto, proceda da seguinte maneira:

1. No Gerenciador de Soluções, clique com o botão direito do mouse em **Referências** e clique em **Gerenciar Pacotes NuGet**.
2. Procure "Barramento de Serviço" e selecione o item **Barramento de Serviço do Microsoft Azure** . Clique em **Instalar** para concluir a instalação e feche a seguinte caixa de diálogo:
   
   ![](./media/service-bus-dotnet-how-to-use-relay/getting-started-multi-tier-13.png)

## <a name="expose-and-consume-a-soap-web-service-with-tcp"></a>Exponha e consuma um serviço Web SOAP com TCP
Para expor um serviço Web SOAP WCF existente para consumo externo, você deve alterar as associações e endereços do serviço. Isso pode exigir alterações em seu arquivo de configuração ou exigir alterações de código, dependendo de como você instalou e configurou seus serviços WCF. Observe que o WCF permite ter vários pontos de extremidade de rede no mesmo serviço e, portanto, você pode reter os pontos de extremidade internos existentes, ao mesmo tempo que adiciona pontos de extremidade de retransmissão para o acesso externo simultaneamente.

Nesta tarefa, você cria um serviço WCF simples e adiciona um ouvinte de retransmissão a ele. Este exercício pressupõe alguma familiaridade com o Visual Studio e, portanto, não percorre todos os detalhes da criação de um projeto. Em vez disso, ele se concentra no código.

Antes de iniciar as etapas a seguir, conclua o seguinte procedimento para configurar seu ambiente:

1. No Visual Studio, crie um aplicativo de console que contenha dois projetos: “Cliente” e “Serviço” na solução.
2. Adicione o pacote NuGet do Barramento de Serviço aos dois projetos. Esse pacote adiciona todas as referências ao assembly necessárias para seus projetos.

### <a name="how-to-create-the-service"></a>Como criar o serviço
Primeiro, crie o serviço. Qualquer serviço WCF consiste em pelo menos três partes distintas:

* Definição de um contrato que descreve quais mensagens são trocadas e quais operações devem ser chamadas.
* Implementação do contrato.
* Host que hospeda o serviço WCF e exibe vários pontos de extremidade.

Os exemplos de código desta seção abordam cada um desses componentes.

O contrato define uma única operação, `AddNumbers`, que adiciona dois números e retorna o resultado. A interface `IProblemSolverChannel` permite que o cliente gerencie mais facilmente o tempo de vida do proxy. A criação dessa interface é considerada uma prática recomendada. É uma boa ideia colocar essa definição de contrato em um arquivo separado para que você possa fazer referência a ele nos dois projetos, "Cliente" e "Serviço", mas você também pode copiar o código nos dois projetos:

```csharp
using System.ServiceModel;

[ServiceContract(Namespace = "urn:ps")]
interface IProblemSolver
{
    [OperationContract]
    int AddNumbers(int a, int b);
}

interface IProblemSolverChannel : IProblemSolver, IClientChannel {}
```

Com o contrato em vigor, a implementação ocorre da seguinte maneira:

```csharp
class ProblemSolver : IProblemSolver
{
    public int AddNumbers(int a, int b)
    {
        return a + b;
    }
}
```

### <a name="configure-a-service-host-programmatically"></a>Configure um host de serviço de forma programática
Com o contrato e a implementação estabelecidos, você agora pode hospedar o serviço. A hospedagem ocorre dentro de um objeto [System.ServiceModel.ServiceHost](https://msdn.microsoft.com/library/system.servicemodel.servicehost.aspx), que cuida do gerenciamento de instâncias do serviço e hospeda os pontos de extremidade que detectam as mensagens. O código a seguir configura o serviço com um ponto de extremidade local normal e um ponto de extremidade de retransmissão para ilustrar a aparência, lado a lado, dos pontos de extremidade internos e externos. Substitua a cadeia de caracteres *namespace* pelo nome do namespace e *yourKey* pela chave SAS obtida na etapa de configuração anterior.

```csharp
ServiceHost sh = new ServiceHost(typeof(ProblemSolver));

sh.AddServiceEndpoint(
   typeof (IProblemSolver), new NetTcpBinding(),
   "net.tcp://localhost:9358/solver");

sh.AddServiceEndpoint(
   typeof(IProblemSolver), new NetTcpRelayBinding(),
   ServiceBusEnvironment.CreateServiceUri("sb", "namespace", "solver"))
    .Behaviors.Add(new TransportClientEndpointBehavior {
          TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", "<yourKey>")});

sh.Open();

Console.WriteLine("Press ENTER to close");
Console.ReadLine();

sh.Close();
```

No exemplo, você cria dois pontos de extremidade que estão na mesma implementação de contrato. Um é local e o outro é projetado por meio da Retransmissão do Azure. As principais diferenças entre eles são as associações; [NetTcpBinding](https://msdn.microsoft.com/library/system.servicemodel.nettcpbinding.aspx) para o local e [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding#microsoft_servicebus_nettcprelaybinding) para o ponto de extremidade de retransmissão e os endereços. O ponto de extremidade local tem um endereço de rede local com uma porta distinta. O ponto de extremidade de retransmissão tem um endereço do ponto de extremidade composto pela cadeia de caracteres `sb`, o nome do namespace e o caminho “solver”. Isso resulta na identificação pelo URI `sb://[serviceNamespace].servicebus.windows.net/solver` do ponto de extremidade de serviço como um ponto de extremidade TCP (retransmissão) do Barramento de Serviço com um nome DNS externo totalmente qualificado. Se você colocar o código substituindo os espaços reservados, na função `Main` do aplicativo **Serviço**, você terá um serviço funcional. Se desejar que o serviço ouça exclusivamente na retransmissão, remova a declaração de ponto de extremidade local.

### <a name="configure-a-service-host-in-the-appconfig-file"></a>Configure um host de serviço no arquivo App.config
Você também pode configurar o host usando o arquivo App.config. O serviço de hospedagem de código, nesse caso, é exibido no exemplo a seguir.

```csharp
ServiceHost sh = new ServiceHost(typeof(ProblemSolver));
sh.Open();
Console.WriteLine("Press ENTER to close");
Console.ReadLine();
sh.Close();
```

As definições de ponto de extremidade são movidas para o arquivo App.config. O pacote NuGet já adicionou uma série de definições ao arquivo App.config, que são as extensões de configuração necessárias para a Retransmissão do Azure. O seguinte exemplo de código, que é o equivalente exato do exemplo de código anterior, deve aparecer diretamente sob o elemento **system.serviceModel**. Esse exemplo pressupõe que o namespace do projeto C# é chamado de **Serviço**.
Substitua os espaços reservados pelo nome de namespace da retransmissão e pela chave de SAS.

```xml
<services>
    <service name="Service.ProblemSolver">
        <endpoint contract="Service.IProblemSolver"
                  binding="netTcpBinding"
                  address="net.tcp://localhost:9358/solver"/>
        <endpoint contract="Service.IProblemSolver"
                  binding="netTcpRelayBinding"
                  address="sb://<namespaceName>.servicebus.windows.net/solver"
                  behaviorConfiguration="sbTokenProvider"/>
    </service>
</services>
<behaviors>
    <endpointBehaviors>
        <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
                <tokenProvider>
                    <sharedAccessSignature keyName="RootManageSharedAccessKey" key="<yourKey>" />
                </tokenProvider>
            </transportClientEndpointBehavior>
        </behavior>
    </endpointBehaviors>
</behaviors>
```

Depois de fazer essas alterações, o serviço é iniciado como antes, mas com dois pontos de extremidade ativos: um local e um escutando na nuvem.

### <a name="create-the-client"></a>Crie o cliente
#### <a name="configure-a-client-programmatically"></a>Configure um cliente de forma programática
Para consumir o serviço, você pode construir um cliente WCF usando um objeto [ChannelFactory](https://msdn.microsoft.com/library/system.servicemodel.channelfactory.aspx). O Barramento de Serviço usa um modelo de segurança baseado em token implementado com a SAS. A classe [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) representa um provedor de token de segurança com métodos de fábrica internos que retornam alguns provedores de token conhecidos. O exemplo a seguir usa o método [CreateSharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider#Microsoft_ServiceBus_TokenProvider_CreateSharedAccessSignatureTokenProvider_System_String_) para lidar com a aquisição do token SAS apropriado. O nome e a chave são os obtidos no portal, conforme descrito na seção anterior.

Primeiro, faça referência ou copie o código do contrato `IProblemSolver` do serviço para o projeto cliente.

Em seguida, substitua o código do método `Main` do cliente, substituindo novamente o texto do espaço reservado pelo namespace de retransmissão e pela chave de SAS.

```csharp
var cf = new ChannelFactory<IProblemSolverChannel>(
    new NetTcpRelayBinding(),
    new EndpointAddress(ServiceBusEnvironment.CreateServiceUri("sb", "<namespaceName>", "solver")));

cf.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior
            { TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey","<yourKey>") });

using (var ch = cf.CreateChannel())
{
    Console.WriteLine(ch.AddNumbers(4, 5));
}
```

Agora, você pode compilar o cliente e o serviço, executá-los (execute o serviço primeiro) e o cliente chama o serviço e imprime **9**. Você pode executar o cliente e o servidor em máquinas diferentes, até entre redes, e a comunicação ainda funcionará. O código do cliente também pode ser executado na nuvem ou localmente.

#### <a name="configure-a-client-in-the-appconfig-file"></a>Configure um cliente no arquivo App.config
O código a seguir mostra como configurar o cliente usando o arquivo App.config.

```csharp
var cf = new ChannelFactory<IProblemSolverChannel>("solver");
using (var ch = cf.CreateChannel())
{
    Console.WriteLine(ch.AddNumbers(4, 5));
}
```

As definições de ponto de extremidade são movidas para o arquivo App.config. O exemplo a seguir, que é o mesmo código listado anteriormente, deve aparecer diretamente sob o elemento `<system.serviceModel>`. Aqui, como antes, você deve substituir os espaços reservados pelo namespace de retransmissão e pela chave de SAS.

```xml
<client>
    <endpoint name="solver" contract="Service.IProblemSolver"
              binding="netTcpRelayBinding"
              address="sb://<namespaceName>.servicebus.windows.net/solver"
              behaviorConfiguration="sbTokenProvider"/>
</client>
<behaviors>
    <endpointBehaviors>
        <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
                <tokenProvider>
                    <sharedAccessSignature keyName="RootManageSharedAccessKey" key="<yourKey>" />
                </tokenProvider>
            </transportClientEndpointBehavior>
        </behavior>
    </endpointBehaviors>
</behaviors>
```

## <a name="next-steps"></a>Próximas etapas
Agora que você já sabe os conceitos básicos sobre a Retransmissão do Azure, siga estes links para saber mais.

* [O que é Retransmissão do Azure?](relay-what-is-it.md)
* [Visão geral da arquitetura de Barramento de Serviço do Azure](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)
* Baixe exemplos de Barramento de Serviço das [amostras do Azure][Azure samples] ou confira a [visão geral de amostras do Barramento de Serviço][overview of Service Bus samples].

[Shared Access Signature Authentication with Service Bus]: ../service-bus-messaging/service-bus-shared-access-signature-authentication.md
[Azure samples]: https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=2
[overview of Service Bus samples]: ../service-bus-messaging/service-bus-samples.md
