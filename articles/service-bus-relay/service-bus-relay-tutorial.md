---
title: "Tutorial de Retransmissão de WCF do Barramento de Serviço do Azure | Microsoft Docs"
description: "Compilar um serviço e um aplicativo cliente do Barramento de Serviço usando a Retransmissão WCF."
services: service-bus-relay
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 53dfd236-97f1-4778-b376-be91aa14b842
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/02/2017
ms.author: sethm
ms.openlocfilehash: 0298a93da0d8cd0b1f2e15146a708c8dd6ecb8e6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-wcf-relay-tutorial"></a>Tutorial de Retransmissão de WCF do Azure

Este tutorial descreve como compilar um aplicativo cliente e de serviço simples de Retransmissão de WCF usando a Retransmissão do Azure. Para obter um tutorial semelhante que usa o [Sistema de Mensagens do Barramento de Serviço](../service-bus-messaging/service-bus-messaging-overview.md#brokered-messaging), consulte a [Introdução às filas do Barramento de Serviço](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md).

Trabalhar com este tutorial fornece uma compreensão das etapas necessárias para criar um aplicativo de serviço e cliente de Retransmissão de WCF. Assim como suas contrapartes WCF originais, um serviço é um constructo que expõe um ou mais pontos de extremidade, cada um dos quais expõe uma ou mais operações de serviço. O ponto de extremidade de serviço especifica um endereço em que o serviço pode ser encontrado, uma associação que contém as informações que um cliente deve comunicar com o serviço e um contrato que define a funcionalidade fornecida pelo serviço a seus clientes. A principal diferença entre um WCF e uma de Retransmissão de WCF é que o ponto de extremidade é exposto na nuvem, em vez de localmente em seu computador.

Depois de trabalhar passando pela sequência de tópicos neste tutorial, você terá um serviço em execução e um cliente que pode invocar as operações do serviço. O primeiro tópico descreve como configurar uma conta. As próximas etapas descrevem como definir um serviço que usa um contrato, como implementar o serviço e como configurar o serviço no código. Eles também descrevem como hospedar e executar o serviço. O serviço é criado é auto-hospedado e o cliente e o serviço são executados no mesmo computador. Você pode configurar o serviço usando código ou um arquivo de configuração.

As três etapas finais descrevem como criar um aplicativo cliente, configurá-lo e usar um cliente que pode acessar a funcionalidade do host.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir esse tutorial, você precisará do seguinte:

* [Microsoft Visual Studio 2015 ou superior](http://visualstudio.com). Este tutorial usa o Visual Studio 2017.
* Uma conta ativa do Azure. Se não tiver uma, você poderá criar uma conta gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/free/).

## <a name="create-a-service-namespace"></a>Criar um namespace de serviço

A primeira etapa é criar um namespace e obter uma chave de [SAS (Assinatura de Acesso Compartilhado)](../service-bus-messaging/service-bus-sas.md). Um namespace fornece um limite de aplicativo para cada aplicativo exposto por meio do serviço de retransmissão. A chave SAS é automaticamente gerada pelo sistema quando um namespace de serviço é criado. A combinação do namespace de serviço e a chave SAS fornece as credenciais para o Azure autenticar o acesso a um aplicativo. Siga as [instruções aqui](relay-create-namespace-portal.md) para criar um namespace de Retransmissão.

## <a name="define-a-wcf-service-contract"></a>Definir um contrato de serviço do WCF

O contrato de serviço especifica a quais operações (a terminologia do serviço Web para funções ou métodos) o serviço dá suporte. Os contratos são criados pela definição de uma interface C++, C# ou Visual Basic. Cada método na interface corresponde a uma operação de serviço específica. O atributo [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) deve ser aplicado a cada interface e o atributo [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx) deve ser aplicado a cada operação. Se um método em uma interface que tem o atributo [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) não tiver o atributo [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx), tal método não será exposto. O código para essas tarefas é fornecido no exemplo logo após o procedimento. Para uma discussão mais ampla de contratos e serviços, confira [Projetar e Implementar Serviços](https://msdn.microsoft.com/library/ms729746.aspx) na documentação do WCF.

### <a name="create-a-relay-contract-with-an-interface"></a>Criar um contrato de retransmissão com uma interface

1. Abra o Visual Studio como administrador clicando com o botão direito no programa no menu **Iniciar** e, em seguida, selecionando **Executar como administrador**.
2. Crie um novo projeto de aplicativo de console. Clique no menu **Arquivo** e selecione **Novo**, então, clique em **Projeto**. Na caixa de diálogo **Novo Projeto**, clique em **Visual C#** (se **Visual C#** não aparecer, procure em **Outras Linguagens**). Clique no modelo **Aplicativo de Console (.NET Framework)** e nomeie-o **EchoService**. Clique em **OK** para criar o projeto.

    ![][2]

3. Instalar o pacote NuGet do Barramento de Serviço. Esse pacote adiciona automaticamente referências para as bibliotecas do Barramento de Serviço, bem como o WCF **System.ServiceModel**. [System.ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx) é o namespace que permite o acesso programático aos recursos básicos do WCF. O Barramento de Serviço usa vários dos objetos e atributos do WCF para definir contratos de serviço.

    No Gerenciador de Soluções, clique com o botão direito do mouse no projeto e em **Gerenciar Pacotes NuGet...**. Clique na guia **Procurar** e procure `Microsoft Azure Service Bus`. Verifique se o nome do projeto está selecionado na caixa **Versão(ões)**. Clique em **Instalar**e aceite os termos de uso.

    ![][3]
4. No Gerenciador de Soluções, clique duas vezes no arquivo Program.cs para abri-lo no editor, se já não estiver aberto.
5. Adicione o seguinte usando as instruções na parte superior do arquivo:

    ```csharp
    using System.ServiceModel;
    using Microsoft.ServiceBus;
    ```
6. Altere o nome do namespace, de seu nome padrão **EchoService** para **Microsoft.ServiceBus.Samples**.

   > [!IMPORTANT]
   > Este tutorial usa o namespace do C# **Microsoft.ServiceBus.Samples**, que é o namespace do tipo gerenciado baseado em contrato que é usado no arquivo de configuração na etapa [Configurar cliente WCF](#configure-the-wcf-client). Você pode especificar qualquer namespace desejado ao compilar esta amostra; no entanto, o tutorial não funcionará a menos que você modifique os namespaces do contrato e do serviço de modo correspondente, no arquivo de configuração de aplicativo. O namespace especificado no arquivo App.config deve ser o mesmo que o namespace especificado em seus arquivos C#.
   >
   >
7. Imediatamente após a declaração do namespace `Microsoft.ServiceBus.Samples`, mas ainda dentro do namespace, defina uma nova interface chamada `IEchoContract` e aplique o atributo `ServiceContractAttribute` à interface com um valor de namespace de `http://samples.microsoft.com/ServiceModel/Relay/`. O valor do namespace é diferente do namespace que você usa em todo o escopo do seu código. Em vez disso, o valor do namespace é usado como um identificador exclusivo para este contrato. Especificar o namespace de forma explícita impede a adição do valor de namespace padrão ao nome do contrato. Cole o trecho de código a seguir após a declaração de namespace:

    ```csharp
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
    }
    ```

   > [!NOTE]
   > Normalmente, o namespace de contrato de serviço contém um esquema de nomenclatura que inclui informações de versão. A inclusão de informações de versão no namespace de contrato de serviço permite que os serviços isolem as alterações principais, definindo um novo contrato de serviço com um novo namespace e expondo-o em um novo ponto de extremidade. Dessa maneira, os clientes podem continuar a usar o antigo contrato de serviço sem que ele precise ser atualizado. Informações de versão podem consistir de uma data ou um número da versão. Para saber mais, veja [Controle de Versão do Serviço](http://go.microsoft.com/fwlink/?LinkID=180498). Para os fins deste tutorial, o esquema de nomenclatura do namespace do contrato de serviço não contém informações de versão.
   >
   >
8. Dentro da interface `IEchoContract`, declare um método para a operação individual exposta pelo contrato `IEchoContract` na interface e aplique o atributo `OperationContractAttribute` ao método que você deseja expor como parte do contrato de retransmissão de WCF público, conforme descrito a seguir:

    ```csharp
    [OperationContract]
    string Echo(string text);
    ```
9. Logo após a definição da interface `IEchoContract`, declare um canal que herde de `IEchoContract` e também a interface `IClientChannel`, como mostrado aqui:

    ```csharp
    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```

    Um canal é o objeto WCF por meio do qual o host e o cliente trocam informações. Posteriormente, você vai escrever código contra o canal para repercutir informações entre os dois aplicativos.
10. No menu **Compilar**, clique em **Solução de Compilação** ou pressione **Ctrl+Shift+B** para confirmar a precisão de seu trabalho até o momento.

### <a name="example"></a>Exemplo

O código a seguir mostra uma interface básica que define um contrato de Retransmissão de WCF.

```csharp
using System;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

Agora que a interface está criada, você pode implementar a interface.

## <a name="implement-the-wcf-contract"></a>Implementar o contrato WCF

Criar uma retransmissão do Azure requer que você primeiro crie o contrato, que é definido usando uma interface. Para obter mais informações sobre como criar a interface, consulte a etapa anterior. A próxima etapa é implementar a interface. Isso envolve a criação de uma classe chamada `EchoService` que implementa a interface `IEchoContract` definida pelo usuário. Depois de implementar a interface, você a configura usando um arquivo App.config. O arquivo de configuração contém as informações necessárias para o aplicativo, como o nome do serviço, o nome do contrato e o tipo de protocolo usado para se comunicar com o serviço de retransmissão. O código usado para essas tarefas é fornecido no exemplo logo após o procedimento. Para ver uma análise mais geral sobre como implementar um contrato de serviço, confira [Implementar Contratos de Serviço](https://msdn.microsoft.com/library/ms733764.aspx) na documentação WCF.

1. Crie uma nova classe chamada `EchoService` diretamente após a definição da interface `IEchoContract`. A classe `EchoService` implementa a interface `IEchoContract`.

    ```csharp
    class EchoService : IEchoContract
    {
    }
    ```

    Assim como outras implementações de interface, você pode implementar a definição em um arquivo diferente. No entanto, para este tutorial, a implementação está localizada no mesmo arquivo que a definição de interface e o método `Main`.
2. Aplique o atributo [ServiceBehaviorAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicebehaviorattribute.aspx) na interface `IEchoContract`. O atributo especifica o nome do serviço e o namespace. Depois de fazer isso, a classe `EchoService` aparecerá da seguinte maneira:

    ```csharp
    [ServiceBehavior(Name = "EchoService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class EchoService : IEchoContract
    {
    }
    ```
3. Implemente o método `Echo` definido na interface `IEchoContract` na classe `EchoService`.

    ```csharp
    public string Echo(string text)
    {
        Console.WriteLine("Echoing: {0}", text);
        return text;
    }
    ```
4. Clique em **Compilar**, depois em **Compilar Solução** para confirmar a precisão do seu trabalho.

### <a name="define-the-configuration-for-the-service-host"></a>Definir a configuração do host de serviço

1. O arquivo de configuração é muito semelhante a um arquivo de configuração do WCF. Ele inclui o nome do serviço, o ponto de extremidade (ou seja, o local que a Retransmissão do Azure expõe para os clientes e hosts se comunicarem) e a associação (o tipo de protocolo usado para comunicação). A principal diferença é que esse ponto de extremidade de serviço configurado refere-se a uma associação [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding), que não faz parte do .NET Framework. [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) é uma das associações definidas pelo serviço.
2. No **Gerenciador de Soluções**, clique duas vezes no arquivo App.config para abri-lo no editor do Visual Studio.
3. No elemento `<appSettings>`, substitua os espaços reservados pelo nome do seu namespace de serviço e a chave SAS copiada em uma etapa anterior.
4. Dentro das marcas `<system.serviceModel>`, adicione um elemento `<services>`. Assim como nas associações, você pode definir vários aplicativos de retransmissão em um único arquivo de configuração. Este tutorial, porém, define apenas um.

    ```xml
    <?xmlversion="1.0"encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <services>

        </services>
      </system.serviceModel>
    </configuration>
    ```
5. Dentro do elemento `<services>`, adicione um elemento `<service>` para definir o nome do serviço.

    ```xml
    <service name="Microsoft.ServiceBus.Samples.EchoService">
    </service>
    ```
6. Dentro do elemento `<service>`, defina o local do contrato do ponto de extremidade e também o tipo de associação para o ponto de extremidade.

    ```xml
    <endpoint contract="Microsoft.ServiceBus.Samples.IEchoContract" binding="netTcpRelayBinding"/>
    ```

    O ponto de extremidade define no qual o cliente procurará o aplicativo host. Posteriormente, o tutorial usa esta etapa para criar um URI que expõe totalmente o host através da Retransmissão do Azure. A associação declara que estamos usando TCP como protocolo para comunicação com o serviço de retransmissão.
7. No menu **Compilar**, clique em **Compilar Solução** para confirmar a precisão do seu trabalho.

### <a name="example"></a>Exemplo

O código a seguir mostra a implementação do contrato de serviço.

```csharp
[ServiceBehavior(Name = "EchoService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]

    class EchoService : IEchoContract
    {
        public string Echo(string text)
        {
            Console.WriteLine("Echoing: {0}", text);
            return text;
        }
    }
```

O código a seguir mostra o formato básico do arquivo App.config associado ao host de serviço.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.serviceModel>
    <services>
      <service name="Microsoft.ServiceBus.Samples.EchoService">
        <endpoint contract="Microsoft.ServiceBus.Samples.IEchoContract" binding="netTcpRelayBinding" />
      </service>
    </services>
    <extensions>
      <bindingExtensions>
        <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
      </bindingExtensions>
    </extensions>
  </system.serviceModel>
</configuration>
```

## <a name="host-and-run-a-basic-web-service-to-register-with-the-relay-service"></a>Hospedar e executar um serviço Web básico para efetuar o registro com o serviço de retransmissão

Esta etapa descreve como executar um serviço de Retransmissão do Azure.

### <a name="create-the-relay-credentials"></a>Criar as credenciais de retransmissão

1. Em `Main()`, crie duas variáveis nas quais armazenar o namespace e a chave de SAS que são lidos da janela do console.

    ```csharp
    Console.Write("Your Service Namespace: ");
    string serviceNamespace = Console.ReadLine();
    Console.Write("Your SAS key: ");
    string sasKey = Console.ReadLine();
    ```

    A chave de SAS será usada posteriormente para acessar seu projeto. O namespace é passado como um parâmetro para `CreateServiceUri` para criar um URI de serviço.
2. Usando um objeto [TransportClientEndpointBehavior](/dotnet/api/microsoft.servicebus.transportclientendpointbehavior), declare que você usará uma chave de SAS como o tipo de credencial. Adicione o código a seguir diretamente após o código adicionado na última etapa.

    ```csharp
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```

### <a name="create-a-base-address-for-the-service"></a>Criar um endereço básico para o serviço

Após o código adicionado na última etapa, crie uma instância de `Uri` para o endereço básico do serviço. Esse URI especifica o esquema de Barramento de Serviço, o namespace e o caminho da interface do serviço.

```csharp
Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
```

"sb" é uma abreviação para o esquema de Barramento de Serviço e indica que estamos usando TCP como o protocolo. Isso também foi indicado anteriormente no arquivo de configuração, quando [NetTcpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.nettcprelaybinding.aspx) foi especificado como a associação.

Para este tutorial, o URI é `sb://putServiceNamespaceHere.windows.net/EchoService`.

### <a name="create-and-configure-the-service-host"></a>Criar e configurar o host de serviço

1. Defina o modo de conectividade para `AutoDetect`.

    ```csharp
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```

    O modo de conectividade descreve o protocolo que o serviço usa para comunicar-se com o serviço de retransmissão, HTTP ou TCP. Usando a configuração padrão `AutoDetect`, o serviço tentará conectar-se à Retransmissão do Azure por TCP se estiver disponível e por HTTP se o TCP não estiver disponível. Observe que isso difere do protocolo que o serviço especifica para comunicação de cliente. Esse protocolo é determinado pela associação usada. Por exemplo, um serviço pode usar a associação [BasicHttpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.basichttprelaybinding.aspx), que especifica que o ponto de extremidade comunica-se com clientes por HTTP. Esse mesmo serviço pôde especificar **ConnectivityMode.AutoDetect** para que o serviço se comunicasse com a Retransmissão do Azure por TCP.
2. Crie o host de serviço, usando o endereço URI criado anteriormente nesta seção.

    ```csharp
    ServiceHost host = new ServiceHost(typeof(EchoService), address);
    ```

    O host de serviço é o objeto WCF que instancia o serviço. Aqui, você informa a ele o tipo de serviço que você deseja criar (um tipo `EchoService`) e também o endereço no qual você deseja expor o serviço.
3. Na parte superior do arquivo Program.cs, adicione referências a [System.ServiceModel.Description](https://msdn.microsoft.com/library/system.servicemodel.description.aspx) e [Microsoft.ServiceBus.Description](/dotnet/api/microsoft.servicebus.description).

    ```csharp
    using System.ServiceModel.Description;
    using Microsoft.ServiceBus.Description;
    ```
4. De volta em `Main()`, configure o ponto de extremidade para habilitar o acesso público.

    ```csharp
    IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);
    ```

    Essa etapa informa ao feed do serviço de retransmissão que seu aplicativo pode ser encontrado publicamente ao examinar o feed ATOM para o seu projeto. Se você definir **DiscoveryType** como **private**, um cliente ainda poderá acessar o serviço. No entanto, o serviço não seria exibido ao procurar o namespace de Retransmissão. Em vez disso, o cliente precisaria saber o caminho do ponto de extremidade com antecedência.
5. Aplique as credenciais de serviço aos pontos de extremidade de serviço definidos no arquivo App.config:

    ```csharp
    foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
    {
        endpoint.Behaviors.Add(serviceRegistrySettings);
        endpoint.Behaviors.Add(sasCredential);
    }
    ```

    Conforme mencionado na etapa anterior, você poderia ter declarado vários serviços e pontos de extremidade no arquivo de configuração. Se você tivesse feito isso, esse código percorreria o arquivo de configuração e procuraria por cada ponto de extremidade ao qual ele devesse aplicar suas credenciais. No entanto, para este tutorial, o arquivo de configuração tem apenas um ponto de extremidade.

### <a name="open-the-service-host"></a>Abrir o host de serviço

1. Abra o arquivo serviço.

    ```csharp
    host.Open();
    ```
2. Informe ao usuário que o serviço está em execução e explique como desligá-lo.

    ```csharp
    Console.WriteLine("Service address: " + address);
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```
3. Quando terminar, feche o host do serviço.

    ```csharp
    host.Close();
    ```
4. Pressione **Ctrl+Shift+B** para compilar o projeto.

### <a name="example"></a>Exemplo

O código de serviço concluído deve aparecer conforme demonstrado a seguir. O código inclui o contrato e a implementação de serviço das etapas anteriores no tutorial e hospeda o serviço em um aplicativo de console.

```csharp
using System;
using System.ServiceModel;
using System.ServiceModel.Description;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Description;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { };

    [ServiceBehavior(Name = "EchoService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class EchoService : IEchoContract
    {
        public string Echo(string text)
        {
            Console.WriteLine("Echoing: {0}", text);
            return text;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {

            ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;         

            Console.Write("Your Service Namespace: ");
            string serviceNamespace = Console.ReadLine();
            Console.Write("Your SAS key: ");
            string sasKey = Console.ReadLine();

           // Create the credentials object for the endpoint.
            TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
            sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);

            // Create the service URI based on the service namespace.
            Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");

            // Create the service host reading the configuration.
            ServiceHost host = new ServiceHost(typeof(EchoService), address);

            // Create the ServiceRegistrySettings behavior for the endpoint.
            IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);

            // Add the Relay credentials to all endpoints specified in configuration.
            foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
            {
                endpoint.Behaviors.Add(serviceRegistrySettings);
                endpoint.Behaviors.Add(sasCredential);
            }

            // Open the service.
            host.Open();

            Console.WriteLine("Service address: " + address);
            Console.WriteLine("Press [Enter] to exit");
            Console.ReadLine();

            // Close the service.
            host.Close();
        }
    }
}
```

## <a name="create-a-wcf-client-for-the-service-contract"></a>Criar um cliente WCF para o contrato de serviço

A próxima etapa é criar um aplicativo cliente e definir o contrato de serviço que você implementará em etapas posteriores. Observe que muitas dessas etapas se assemelham àquelas usadas para criar um serviço: definir um contrato, editar um arquivo App.config, usar as credenciais para se conectar ao serviço de retransmissão e assim por diante. O código usado para essas tarefas é fornecido no exemplo logo após o procedimento.

1. Crie um novo projeto na solução atual do Visual Studio para o cliente fazendo o seguinte:

   1. No Gerenciador de Soluções, na mesma solução que contém o serviço, clique com o botão direito do mouse na solução atual (não no projeto) e clique em **Adicionar**. Em seguida, clique em **Novo Projeto**.
   2. Na caixa de diálogo **Adicionar Novo Projeto**, clique em **Visual C#** (se **Visual C#** não aparecer, procure em **Outras Linguagens**), selecione o modelo **Aplicativo de Console (.NET Framework)** e nomeie-o como **EchoClient**.
   3. Clique em **OK**.
      <br />
2. No Gerenciador de Soluções, clique duas vezes no arquivo Program.cs no projeto **EchoClient** para abri-lo no editor, se já não estiver aberto.
3. Altere o nome do namespace de seu padrão `EchoClient` para `Microsoft.ServiceBus.Samples`.
4. Instale o [pacote NuGet do Barramento de Serviço](https://www.nuget.org/packages/WindowsAzure.ServiceBus): no Gerenciador de Soluções, clique com o botão direito do mouse no projeto **EchoClient** e, em seguida, clique em **Gerenciar Pacotes NuGet**. Clique na guia **Procurar** e procure `Microsoft Azure Service Bus`. Clique em **Instalar**e aceite os termos de uso.

    ![][3]
5. Adicione uma declaração `using` ao namespace [System.ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx) no arquivo Program.cs.

    ```csharp
    using System.ServiceModel;
    ```
6. Adicione a definição de contrato de serviço ao namespace, conforme mostrado no exemplo a seguir. Observe que essa definição é idêntica à definição usada no projeto **Service**. Você deve adicionar esse código à parte superior do namespace `Microsoft.ServiceBus.Samples`.

    ```csharp
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        string Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```
7. Pressione **Ctrl+Shift+B** para compilar o cliente.

### <a name="example"></a>Exemplo

O código a seguir mostra o status atual do arquivo Program.cs no projeto **EchoClient**.

```csharp
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        string Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }


    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

## <a name="configure-the-wcf-client"></a>Configurar o cliente WCF

Nesta etapa, você cria um arquivo App.config para um aplicativo cliente básico que acessa o serviço criado anteriormente neste tutorial. Esse arquivo App.config define o contrato, a associação e o nome do ponto de extremidade. O código usado para essas tarefas é fornecido no exemplo logo após o procedimento.

1. No Gerenciador de Soluções, no projeto **EchoClient**, clique duas vezes em **App.config** para abrir o arquivo no editor do Visual Studio.
2. No elemento `<appSettings>`, substitua os espaços reservados pelo nome do seu namespace de serviço e a chave SAS copiada em uma etapa anterior.
3. Dentro do elemento system.serviceModel, adicione um elemento `<client>`.

    ```xml
    <?xmlversion="1.0"encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <client>
        </client>
      </system.serviceModel>
    </configuration>
    ```

    Essa etapa declara que você está definindo um aplicativo de cliente no estilo WCF.
4. Dentro do elemento `client`, defina o nome, o contrato e o tipo de associação para o ponto de extremidade.

    ```xml
    <endpoint name="RelayEndpoint"
                    contract="Microsoft.ServiceBus.Samples.IEchoContract"
                    binding="netTcpRelayBinding"/>
    ```

    Essa etapa define o nome do ponto de extremidade, o contrato definido no serviço e o fato de que o aplicativo cliente usa TCP para se comunicar com a Retransmissão do Azure. O nome do ponto de extremidade é usado na próxima etapa para vincular esta configuração de ponto de extremidade com o URI do serviço.
5. Clique em **Arquivo**, depois em **Salvar Tudo**.

## <a name="example"></a>Exemplo

O código a seguir mostra o arquivo App.config para o cliente Echo.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.serviceModel>
    <client>
      <endpoint name="RelayEndpoint"
                      contract="Microsoft.ServiceBus.Samples.IEchoContract"
                      binding="netTcpRelayBinding"/>
    </client>
    <extensions>
      <bindingExtensions>
        <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
      </bindingExtensions>
    </extensions>
  </system.serviceModel>
</configuration>
```

## <a name="implement-the-wcf-client"></a>Implementar o cliente WCF
Nesta etapa, você implementa um aplicativo cliente básico que acessa o serviço que você criou anteriormente neste tutorial. Semelhante ao serviço, o cliente executa muitas das mesmas operações para acessar a Retransmissão do Azure:

1. Define o modo de conectividade.
2. Cria o URI que localiza o serviço de host.
3. Define as credenciais de segurança.
4. Aplica as credenciais à conexão.
5. Abre a conexão.
6. Executa as tarefas específicas do aplicativo.
7. Encerra a conexão.

No entanto, uma das principais diferenças é que o aplicativo cliente usa um canal para conectar-se ao serviço de retransmissão, enquanto o serviço usa uma chamada para **ServiceHost**. O código usado para essas tarefas é fornecido no exemplo logo após o procedimento.

### <a name="implement-a-client-application"></a>Implementar um aplicativo cliente
1. Defina o modo de conectividade como **AutoDetect**. Adicione o código a seguir dentro do método `Main()` do aplicativo **EchoClient**.

    ```csharp
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```
2. Defina variáveis para conter os valores para o namespace de serviço e a chave SAS que são lidos do console.

    ```csharp
    Console.Write("Your Service Namespace: ");
    string serviceNamespace = Console.ReadLine();
    Console.Write("Your SAS Key: ");
    string sasKey = Console.ReadLine();
    ```
3. Crie o URI que define o local do host em seu projeto de Retransmissão.

    ```csharp
    Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
    ```
4. Crie o objeto de credencial para o ponto de extremidade do namespace de serviço.

    ```csharp
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```
5. Crie a fábrica de canais que carrega a configuração descrita no arquivo App.config.

    ```csharp
    ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));
    ```

    Uma fábrica de canais é um objeto WCF que cria um canal por meio do qual o os aplicativos cliente e de serviço se comunicam.
6. Aplique as credenciais.

    ```csharp
    channelFactory.Endpoint.Behaviors.Add(sasCredential);
    ```
7. Crie e abra o canal para o serviço.

    ```csharp
    IEchoChannel channel = channelFactory.CreateChannel();
    channel.Open();
    ```
8. Escreva a interface do usuário básico e a funcionalidade para o eco.

    ```csharp
    Console.WriteLine("Enter text to echo (or [Enter] to exit):");
    string input = Console.ReadLine();
    while (input != String.Empty)
    {
        try
        {
            Console.WriteLine("Server echoed: {0}", channel.Echo(input));
        }
        catch (Exception e)
        {
            Console.WriteLine("Error: " + e.Message);
        }
        input = Console.ReadLine();
    }
    ```

    Observe que o código usa a instância do objeto do canal como um proxy para o serviço.
9. Feche o canal e a fábrica.

    ```csharp
    channel.Close();
    channelFactory.Close();
    ```

## <a name="example"></a>Exemplo

Seu código completo deve aparecer conforme demonstrado a seguir, mostrando como criar um aplicativo cliente, como chamar as operações do serviço e como fechar o cliente após a conclusão da chamada da operação.

```csharp
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
            ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;


            Console.Write("Your Service Namespace: ");
            string serviceNamespace = Console.ReadLine();
            Console.Write("Your SAS Key: ");
            string sasKey = Console.ReadLine();



            Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");

            TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
            sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);

            ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));

            channelFactory.Endpoint.Behaviors.Add(sasCredential);

            IEchoChannel channel = channelFactory.CreateChannel();
            channel.Open();

            Console.WriteLine("Enter text to echo (or [Enter] to exit):");
            string input = Console.ReadLine();
            while (input != String.Empty)
            {
                try
                {
                    Console.WriteLine("Server echoed: {0}", channel.Echo(input));
                }
                catch (Exception e)
                {
                    Console.WriteLine("Error: " + e.Message);
                }
                input = Console.ReadLine();
            }

            channel.Close();
            channelFactory.Close();

        }
    }
}
```

## <a name="run-the-applications"></a>Executar os aplicativos

1. Pressione **CTRL+SHIFT+B** para criar a solução. Isso compila o projeto cliente e o projeto de serviço que você criou nas etapas anteriores.
2. Antes de executar o aplicativo cliente, verifique se o aplicativo de serviço está em execução. No Gerenciador de Soluções no Visual Studio, clique com o botão direito do mouse na solução **EchoService** e, em seguida, clique em **Propriedades**.
3. Na caixa de diálogo Propriedades da solução, clique em **Projeto de Inicialização**, então, clique no botão **Vários projetos de inicialização**. Verifique se **EchoService** aparece primeiro na lista.
4. Defina a caixa **Ação** dos projetos **EchoService** e **EchoClient** para **Iniciar**.

    ![][5]
5. Clique em **Dependências do Projeto**. Na caixa **Projetos**, selecione **EchoClient**. Na caixa **Depende**, verifique se **EchoService** está marcado.

    ![][6]
6. Clique em **OK** para descartar o diálogo **Propriedades**.
7. Pressione **F5** para executar os dois projetos.
8. Ambas as janelas do console serão abertas e o nome do namespace será solicitado. O serviço deve ser executado primeiro e, então, na janela do console **EchoService**, digite o namespace e pressione **Enter**.
9. Em seguida, será solicitado que você informe a sua chave de SAS. Insira a chave de SAS e pressione ENTER.

    Eis aqui um exemplo de saída da janela do console. Observe que os valores fornecidos aqui são apenas para fins de exemplificação.

    `Your Service Namespace: myNamespace` `Your SAS Key: <SAS key value>`

    O aplicativo de serviço imprime na janela do console o endereço no qual ele está escutando, como visto no exemplo a seguir.

    `Service address: sb://mynamespace.servicebus.windows.net/EchoService/` `Press [Enter] to exit`
10. Na janela do console **EchoClient**, digite as mesmas informações inseridas anteriormente para o aplicativo de serviço. Siga as etapas anteriores para inserir os mesmos valores para o namespace de serviço e a chave SAS do aplicativo do cliente.
11. Depois de inserir esses valores, o cliente abre um canal para o serviço e solicita que você digite algum texto, como visto no exemplo de saída do console a seguir.

    `Enter text to echo (or [Enter] to exit):`

    Digite algum texto para enviar ao aplicativo de serviço e pressione Enter. Esse texto é enviado para o serviço por meio da operação de serviço Echo e aparece na janela do console de serviço como a saída de exemplo a seguir.

    `Echoing: My sample text`

    O aplicativo cliente recebe o valor retornado da operação `Echo`, que é o texto original, e o exibe na janela do console. A seguir, um exemplo de saída da janela do console do cliente.

    `Server echoed: My sample text`
12. Você pode continuar a enviar mensagens de texto do cliente para o serviço dessa maneira. Quando terminar, pressione Enter nas janelas do console de cliente e serviço para encerrar ambos os aplicativos.

## <a name="next-steps"></a>Próximas etapas

Este tutorial mostrou como compilar um serviço e um aplicativo cliente simples da a Retransmissão do Azure usando os recursos de Retransmissão de WCF do Barramento de Serviço. Para obter um tutorial semelhante que usa o [Sistema de Mensagens do Barramento de Serviço](../service-bus-messaging/service-bus-messaging-overview.md#brokered-messaging), consulte a [Introdução às filas do Barramento de Serviço](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md).

Para saber mais sobre a Retransmissão do Azure, consulte os tópicos a seguir.

* [Visão geral da arquitetura de Barramento de Serviço do Azure](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md#relays)
* [Visão geral da Retransmissão do Azure](relay-what-is-it.md)
* [Como usar o serviço de Retransmissão de WCF com o .NET](relay-wcf-dotnet-get-started.md)

[2]: ./media/service-bus-relay-tutorial/create-console-app.png
[3]: ./media/service-bus-relay-tutorial/install-nuget.png
[5]: ./media/service-bus-relay-tutorial/set-projects.png
[6]: ./media/service-bus-relay-tutorial/set-depend.png
