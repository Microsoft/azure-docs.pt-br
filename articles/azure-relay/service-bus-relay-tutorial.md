---
title: Expor um serviço REST do WCF local para clientes usando a Retransmissão do Azure
description: Este tutorial descreve como expor um serviço REST do WCF local para um cliente externo usando a Retransmissão do WCF do Azure.
ms.topic: tutorial
ms.custom: devx-track-dotnet
ms.date: 06/23/2020
ms.openlocfilehash: 7669bc07ad91933cd31bd2ccd10eaf830d98de7c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101710780"
---
# <a name="tutorial-expose-an-on-premises-wcf-rest-service-to-external-client-by-using-azure-wcf-relay"></a>Tutorial: Expor um serviço REST do WCF local para o cliente externo usando a Retransmissão do WCF do Azure

Este tutorial descreve como criar um aplicativo cliente e um serviço da Retransmissão do WCF usando a Retransmissão do Azure. Para obter um tutorial semelhante que usa o [Sistema de mensagens do Barramento de Serviço](../service-bus-messaging/service-bus-messaging-overview.md), consulte a [Introdução às filas do Barramento de Serviço](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md).

Ao acompanhar este tutorial, você obterá um entendimento das etapas necessárias para criar um aplicativo cliente e de serviço da Retransmissão do WCF. Assim como os respectivos equivalentes do WCF originais, um serviço é um constructo que expõe um ou mais pontos de extremidade. Cada ponto de extremidade expõe uma ou mais operações de serviço. O ponto de extremidade de serviço especifica um endereço em que o serviço pode ser encontrado, uma associação que contém as informações que um cliente deve comunicar com o serviço e um contrato que define a funcionalidade fornecida pelo serviço a seus clientes. A principal diferença entre um WCF e uma de Retransmissão de WCF é que o ponto de extremidade é exposto na nuvem, em vez de localmente em seu computador.

Depois de trabalhar na sequência de seções deste tutorial, você terá um serviço em execução. Você também terá um cliente que pode invocar as operações do serviço. 

Você executará as seguintes tarefas neste tutorial:

> [!div class="checklist"]
>
> * Instalar os pré-requisitos para este tutorial.
> * Criar um namespace de Retransmissão
> * Criar um contrato de serviço WCF.
> * Implementar o contrato WCF.
> * Hospedar e executar o serviço WCF para registrar-se no serviço de Retransmissão.
> * Criar um cliente WCF para o contrato de serviço.
> * Configurar o cliente WCF.
> * Implementar o cliente WCF.
> * Executar os aplicativos.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará dos seguintes pré-requisitos:

* Uma assinatura do Azure. Se você não tiver [uma conta gratuita](https://azure.microsoft.com/free/), crie uma antes de começar.
* [Visual Studio 2015 ou posterior](https://www.visualstudio.com). Os exemplos deste tutorial usam o Visual Studio 2019.
* SDK do Azure para .NET. Instale-o a partir da [página de downloads do SDK](https://azure.microsoft.com/downloads/).

## <a name="create-a-relay-namespace"></a>Criar um namespace de Retransmissão

A primeira etapa é criar um namespace e obter uma chave de [SAS (Assinatura de Acesso Compartilhado)](../service-bus-messaging/service-bus-sas.md). Um namespace fornece um limite de aplicativo para cada aplicativo exposto por meio do serviço de retransmissão. Uma chave SAS é automaticamente gerada pelo sistema quando um namespace do serviço é criado. A combinação do namespace de serviço e a chave SAS fornece as credenciais para o Azure autenticar o acesso a um aplicativo.

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="define-a-wcf-service-contract"></a>Definir um contrato de serviço do WCF

O contrato de serviço especifica a quais operações o serviço dá suporte. As operações são métodos ou funções de serviço Web. Os contratos são criados pela definição de uma interface C++, C# ou Visual Basic. Cada método na interface corresponde a uma operação de serviço específica. O atributo [ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute) deve ser aplicado a cada interface e o atributo [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute) deve ser aplicado a cada operação. Se um método em uma interface que tem o atributo [ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute) não tiver o atributo [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute), esse método não será exposto. O código para essas tarefas é fornecido no exemplo logo após o procedimento. Para obter uma discussão mais ampla de contratos e serviços, confira [Como projetar e implementar serviços](/dotnet/framework/wcf/designing-and-implementing-services).

### <a name="create-a-relay-contract-with-an-interface"></a>Criar um contrato de retransmissão com uma interface

1. Inicie o Microsoft Visual Studio como um administrador. Para fazer isso, clique com o botão direito do mouse no ícone de programa do Visual Studio e selecione **Executar como administrador**.
1. No Visual Studio, selecione **Criar um projeto**.
1. Em **Criar um novo projeto**, escolha **Aplicativo de Console (.NET Framework)** para C# e, em seguida, selecione **Avançar**.
1. Nomeie o projeto *EchoService* e selecione **Criar**.

   ![Criar um aplicativo de console][2]

1. No **Gerenciador de Soluções**, clique com o botão direito no projeto e escolha **Gerenciar Pacotes NuGet**. No **Gerenciador de Pacotes NuGet**, selecione **Procurar** e, em seguida, pesquise e escolha **WindowsAzure.ServiceBus**. Selecione **Instalar** e aceite os termos de uso.

    ![Pacote de Barramento de Serviço][3]

   Esse pacote adiciona referências automaticamente às bibliotecas do Barramento de Serviço e ao `System.ServiceModel` do WCF. [System.ServiceModel](/dotnet/api/system.servicemodel) é o namespace que permite o acesso programático aos recursos básicos do WCF. O Barramento de Serviço usa vários dos objetos e atributos do WCF para definir contratos de serviço.

1. Adicione as seguintes instruções `using` na parte superior de *Program.cs*:

    ```csharp
    using System.ServiceModel;
    using Microsoft.ServiceBus;
    ```

1. Altere o nome do namespace de seu padrão `EchoService` para `Microsoft.ServiceBus.Samples`.

   > [!IMPORTANT]
   > Este tutorial usa o namespace `Microsoft.ServiceBus.Samples` do C#, que é o namespace do tipo gerenciado baseado em contrato usado no arquivo de configuração na seção [Configurar o cliente WCF](#configure-the-wcf-client). Você pode especificar qualquer namespace desejado ao criar essa amostra. No entanto, o tutorial não funcionará, a menos que você modifique os namespaces do contrato e do serviço de acordo, no arquivo de configuração de aplicativo. O namespace especificado no arquivo *App.config* precisa ser o mesmo namespace especificado nos arquivos C#.
   >

1. Imediatamente após a declaração do namespace `Microsoft.ServiceBus.Samples`, mas ainda dentro do namespace, defina uma nova interface chamada `IEchoContract` e aplique o atributo `ServiceContractAttribute` à interface com um valor de namespace de `https://samples.microsoft.com/ServiceModel/Relay/`. Cole o trecho de código a seguir após a declaração de namespace:

    ```csharp
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
    }
    ```

    O valor do namespace é diferente do namespace que você usa em todo o escopo do seu código. Em vez disso, o valor do namespace é usado como um identificador exclusivo para este contrato. Especificar o namespace de forma explícita impede a adição do valor de namespace padrão ao nome do contrato.

   > [!NOTE]
   > Normalmente, o namespace de contrato de serviço contém um esquema de nomenclatura que inclui informações de versão. A inclusão de informações de versão no namespace de contrato de serviço permite que os serviços isolem as alterações principais, definindo um novo contrato de serviço com um novo namespace e expondo-o em um novo ponto de extremidade. Dessa maneira, os clientes podem continuar a usar o antigo contrato de serviço sem que ele precise ser atualizado. Informações de versão podem consistir de uma data ou um número da versão. Para saber mais, veja [Controle de Versão do Serviço](/dotnet/framework/wcf/service-versioning). Para este tutorial, o esquema de nomenclatura do namespace do contrato de serviço não contém informações de versão.
   >

1. Dentro da interface `IEchoContract`, declare um método para a operação individual exposta pelo contrato `IEchoContract` na interface e aplique o atributo `OperationContractAttribute` ao método que você deseja expor como parte do contrato de retransmissão de WCF público, conforme descrito a seguir:

    ```csharp
    [OperationContract]
    string Echo(string text);
    ```

1. Logo após a definição da interface `IEchoContract`, declare um canal que herde de `IEchoContract` e também a interface `IClientChannel`, como mostrado aqui:

    ```csharp
    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```

    Um canal é o objeto WCF por meio do qual o host e o cliente trocam informações. Posteriormente, você escreverá o código no canal para ecoar as informações entre os dois aplicativos.

1. Selecione **Compilar** > **Compilar Solução** ou selecione Ctrl+Shift+B para confirmar a precisão do trabalho até o momento.

### <a name="example-of-a-wcf-contract"></a>Exemplo de um contrato WCF

O código a seguir mostra uma interface básica que define um contrato de Retransmissão de WCF.

```csharp
using System;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
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

A criação de uma retransmissão do Azure exige que você primeiro crie o contrato usando uma interface. Para obter mais informações sobre como criar a interface, confira a seção anterior. O próximo procedimento implementa a interface. Essa tarefa envolve a criação de uma classe chamada `EchoService` que implementa a interface `IEchoContract` definida pelo usuário. Depois de implementar a interface, você a configurará usando um arquivo de configuração *App.config*. O arquivo de configuração contém as informações necessárias para o aplicativo. Essas informações incluem o nome do serviço, o nome do contrato e o tipo de protocolo usado para se comunicar com o serviço de retransmissão. O código usado para essas tarefas é fornecido no exemplo logo após o procedimento. Para obter uma discussão mais geral sobre como implementar um contrato de serviço, confira [Como implementar contratos de serviço](/dotnet/framework/wcf/implementing-service-contracts).

1. Crie uma nova classe chamada `EchoService` diretamente após a definição da interface `IEchoContract`. A classe `EchoService` implementa a interface `IEchoContract`.

    ```csharp
    class EchoService : IEchoContract
    {
    }
    ```

    Assim como outras implementações de interface, você pode implementar a definição em um arquivo diferente. No entanto, para este tutorial, a implementação está localizada no mesmo arquivo que a definição de interface e o método `Main()`.

1. Aplique o atributo [ServiceBehaviorAttribute](/dotnet/api/system.servicemodel.servicebehaviorattribute) na interface `IEchoContract`. O atributo especifica o nome do serviço e o namespace. Depois de fazer isso, a classe `EchoService` aparecerá da seguinte maneira:

    ```csharp
    [ServiceBehavior(Name = "EchoService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class EchoService : IEchoContract
    {
    }
    ```

1. Implemente o método `Echo` definido na interface `IEchoContract` na classe `EchoService`.

    ```csharp
    public string Echo(string text)
    {
        Console.WriteLine("Echoing: {0}", text);
        return text;
    }
    ```

1. Selecione **Compilar** > **Compilar Solução** ou selecione Ctrl+Shift+B.

### <a name="define-the-configuration-for-the-service-host"></a>Definir a configuração do host de serviço

O arquivo de configuração é semelhante a um arquivo de configuração do WCF. Ele inclui o nome do serviço, o ponto de extremidade e a associação. O ponto de extremidade é a localização que a Retransmissão do Azure expõe para clientes e hosts se comunicarem entre si. A associação é o tipo de protocolo usado para a comunicação. A principal diferença é que esse ponto de extremidade de serviço configurado refere-se a uma associação [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding), que não faz parte do .NET Framework. [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) é uma das associações definidas pelo serviço.

1. No **Gerenciador de Soluções**, clique duas vezes em **App.config** para abrir o arquivo no editor do Visual Studio.
1. No elemento `<appSettings>`, substitua os espaços reservados pelo nome do seu namespace de serviço e a chave SAS copiada em uma etapa anterior.
1. Dentro das marcas `<system.serviceModel>`, adicione um elemento `<services>`. Assim como nas associações, você pode definir vários aplicativos de retransmissão em um único arquivo de configuração. Este tutorial, porém, define apenas um.

    ```xml
    <?xmlversion="1.0" encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <services>

        </services>
      </system.serviceModel>
    </configuration>
    ```

1. Dentro do elemento `<services>`, adicione um elemento `<service>` para definir o nome do serviço.

    ```xml
    <service name="Microsoft.ServiceBus.Samples.EchoService">
    </service>
    ```

1. Dentro do elemento `<service>`, defina o local do contrato do ponto de extremidade e também o tipo de associação para o ponto de extremidade.

    ```xml
    <endpoint contract="Microsoft.ServiceBus.Samples.IEchoContract" binding="netTcpRelayBinding"/>
    ```

    O ponto de extremidade define no qual o cliente procurará o aplicativo host. Posteriormente, o tutorial usa esta etapa para criar um URI que expõe totalmente o host através da Retransmissão do Azure. A associação declara que estamos usando o TCP como o protocolo para se comunicar com o serviço de retransmissão.

1. Selecione **Compilar** > **Compilar Solução** ou selecione Ctrl+Shift+B para confirmar a precisão do trabalho até o momento.

### <a name="example-of-implementation-of-a-service-contract"></a>Exemplo de implementação de um contrato de serviço

O código a seguir mostra a implementação do contrato de serviço.

```csharp
[ServiceBehavior(Name = "EchoService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]

    class EchoService : IEchoContract
    {
        public string Echo(string text)
        {
            Console.WriteLine("Echoing: {0}", text);
            return text;
        }
    }
```

O código a seguir mostra o formato básico do arquivo *App.config* associado ao host de serviço.

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

## <a name="host-and-run-the-wcf-service-to-register-with-the-relay-service"></a>Hospedar e executar o serviço WCF para registrar-se no serviço de retransmissão

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

1. Usando um objeto [TransportClientEndpointBehavior](/dotnet/api/microsoft.servicebus.transportclientendpointbehavior), declare que você usará uma chave SAS como o tipo de credencial. Adicione o código a seguir diretamente após o código adicionado na última etapa.

    ```csharp
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```

### <a name="create-a-base-address-for-the-service"></a>Criar um endereço básico para o serviço

Após o código adicionado na etapa anterior, crie uma instância de `Uri` para o endereço básico do serviço. Esse URI especifica o esquema de Barramento de Serviço, o namespace e o caminho da interface do serviço.

```csharp
Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
```

O valor "sb" é uma abreviação do esquema do Barramento de Serviço. Isso indica que estamos usando o TCP como o protocolo. Esse esquema também foi indicado anteriormente no arquivo de configuração, quando [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) foi especificado como a associação.

Para este tutorial, o URI é `sb://putServiceNamespaceHere.windows.net/EchoService`.

### <a name="create-and-configure-the-service-host"></a>Criar e configurar o host de serviço

1. Ainda trabalhando em `Main()`, defina o modo de conectividade como `AutoDetect`.

    ```csharp
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```

    O modo de conectividade descreve o protocolo que o serviço usa para comunicar-se com o serviço de retransmissão, HTTP ou TCP. Usando a configuração padrão `AutoDetect`, o serviço tentará se conectar à Retransmissão do Azure via TCP se ele estiver disponível e via HTTP se o TCP não estiver disponível. Esse resultado difere do protocolo que o serviço especifica para a comunicação do cliente. Esse protocolo é determinado pela associação usada. Por exemplo, um serviço pode usar a associação [BasicHttpRelayBinding](/dotnet/api/microsoft.servicebus.basichttprelaybinding), que especifica que o ponto de extremidade comunica-se com clientes por HTTP. Esse mesmo serviço pode especificar `ConnectivityMode.AutoDetect`, de modo que o serviço se comunique com a Retransmissão do Azure via TCP.

1. Crie o host de serviço, usando o endereço URI criado anteriormente nesta seção.

    ```csharp
    ServiceHost host = new ServiceHost(typeof(EchoService), address);
    ```

    O host de serviço é o objeto WCF que instancia o serviço. Aqui, você passará a ele o tipo de serviço que deseja criar, um tipo `EchoService` e também o endereço no qual deseja expor o serviço.

1. Na parte superior do arquivo *Program.cs*, adicione referências a [System.ServiceModel.Description](/dotnet/api/system.servicemodel.description) e [Microsoft.ServiceBus.Description](/dotnet/api/microsoft.servicebus.description).

    ```csharp
    using System.ServiceModel.Description;
    using Microsoft.ServiceBus.Description;
    ```

1. De volta em `Main()`, configure o ponto de extremidade para habilitar o acesso público.

    ```csharp
    IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);
    ```

    Essa etapa informa o serviço de retransmissão que o aplicativo pode ser encontrado publicamente examinando o feed Atom do projeto. Se você definir `DiscoveryType` como `private`, um cliente ainda poderá acessar o serviço. No entanto, o serviço não será exibido quando ele pesquisar o namespace `Relay`. Em vez disso, o cliente precisaria saber o caminho do ponto de extremidade com antecedência.

1. Aplique as credenciais de serviço aos pontos de extremidade de serviço definidos no arquivo *App.config*:

    ```csharp
    foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
    {
        endpoint.Behaviors.Add(serviceRegistrySettings);
        endpoint.Behaviors.Add(sasCredential);
    }
    ```

    Conforme mencionado anteriormente, você poderia ter declarado vários serviços e pontos de extremidade no arquivo de configuração. Se você tivesse feito isso, esse código percorreria o arquivo de configuração e procuraria por cada ponto de extremidade ao qual ele devesse aplicar suas credenciais. Para este tutorial, o arquivo de configuração só tem um ponto de extremidade.

### <a name="open-the-service-host"></a>Abrir o host de serviço

1. Ainda em `Main()`, adicione a linha a seguir para abrir o serviço.

    ```csharp
    host.Open();
    ```

1. Informe ao usuário que o serviço está em execução e explique como desligá-lo.

    ```csharp
    Console.WriteLine("Service address: " + address);
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```

1. Quando terminar, feche o host do serviço.

    ```csharp
    host.Close();
    ```

1. Selecione Ctrl+Shift+B para compilar o projeto.

### <a name="example-that-hosts-a-service-in-a-console-application"></a>Exemplo que hospeda um serviço em um aplicativo de console

O código de serviço concluído deve aparecer conforme demonstrado a seguir. O código inclui o contrato e a implementação de serviço das etapas anteriores no tutorial e hospeda o serviço em um aplicativo de console.

```csharp
using System;
using System.ServiceModel;
using System.ServiceModel.Description;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Description;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { };

    [ServiceBehavior(Name = "EchoService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
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

A próxima tarefa é criar um aplicativo cliente e definir o contrato de serviço que você implementará mais adiante. Essas etapas se assemelham àquelas usadas para criar um serviço: definir um contrato, editar um arquivo *App.config*, usar as credenciais para se conectar ao serviço de retransmissão etc. O código usado para essas tarefas é fornecido no exemplo logo após o procedimento.

1. Crie um projeto na solução atual do Visual Studio para o cliente:

   1. No **Gerenciador de Soluções**, clique com o botão direito do mouse na solução atual (não no projeto) e selecione **Adicionar** > **Novo Projeto**.
   1. Em **Adicionar um novo projeto**, selecione **Aplicativo de Console (.NET Framework)** para C# e **Avançar**.
   1. Nomeie o projeto *EchoClient* e selecione **Criar**.

1. No **Gerenciador de Soluções**, no projeto **EchoClient**, clique duas vezes em **Program.cs** para abrir o arquivo no editor, caso ele já não esteja aberto.
1. Altere o nome do namespace de seu padrão `EchoClient` para `Microsoft.ServiceBus.Samples`.
1. Instale o [pacote NuGet do Barramento de Serviço](https://www.nuget.org/packages/WindowsAzure.ServiceBus):

   1. No **Gerenciador de Soluções**, clique com o botão direito do mouse em **EchoClient** e, em seguida, selecione **Gerenciar Pacotes NuGet**.
   1. Selecione **Procurar** e, em seguida, pesquise e selecione **WindowsAzure.ServiceBus**. Selecione **Instalar** e aceite os termos de uso.

      ![Instalar o pacote do Barramento de Serviço][4]

1. Adicione uma instrução `using` ao namespace [System.ServiceModel](/dotnet/api/system.servicemodel) no arquivo *Program.cs*.

    ```csharp
    using System.ServiceModel;
    ```

1. Adicione a definição de contrato de serviço ao namespace, conforme mostrado no exemplo a seguir. Essa definição é idêntica à definição usada no projeto **Service**. Adicione esse código à parte superior do namespace `Microsoft.ServiceBus.Samples`.

    ```csharp
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        string Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```

1. Selecione Ctrl+Shift+B para compilar o cliente.

### <a name="example-of-the-echoclient-project"></a>Exemplo do projeto EchoClient

O código a seguir mostra o status atual do arquivo *Program.cs* no projeto **EchoClient**.

```csharp
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
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

Nesta etapa, você criará um arquivo *App.config* para um aplicativo cliente básico que acessa o serviço criado anteriormente neste tutorial. Esse arquivo *App.config* define o contrato, a associação e o nome do ponto de extremidade. O código usado para essas tarefas é fornecido no exemplo logo após o procedimento.

1. No **Gerenciador de Soluções**, no projeto **EchoClient**, clique duas vezes em **App.config** para abrir o arquivo no editor do Visual Studio.
1. No elemento `<appSettings>`, substitua os espaços reservados pelo nome do seu namespace de serviço e a chave SAS copiada em uma etapa anterior.
1. Dentro do elemento `system.serviceModel`, adicione um elemento `<client>`.

    ```xml
    <?xmlversion="1.0" encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <client>
        </client>
      </system.serviceModel>
    </configuration>
    ```

    Esse código declara que você está definindo um aplicativo cliente no estilo WCF.

1. Dentro do elemento `client`, defina o nome, o contrato e o tipo de associação para o ponto de extremidade.

    ```xml
    <endpoint name="RelayEndpoint"
                    contract="Microsoft.ServiceBus.Samples.IEchoContract"
                    binding="netTcpRelayBinding"/>
    ```

    Esse código define o nome do ponto de extremidade. Ele também define o contrato definido no serviço e o fato de que o aplicativo cliente usa o TCP para se comunicar com a Retransmissão do Azure. O nome do ponto de extremidade é usado na próxima etapa para vincular esta configuração de ponto de extremidade com o URI do serviço.

1. Selecione **Arquivo** > **Salvar Todos**.

### <a name="example-of-the-appconfig-file"></a>Exemplo do arquivo App.config

O código a seguir mostra o arquivo *App.config* para o cliente Echo.

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

Nesta seção, você implementará um aplicativo cliente básico que acessa o serviço criado anteriormente neste tutorial. Semelhante ao serviço, o cliente executa muitas das mesmas operações para acessar a Retransmissão do Azure:

* Define o modo de conectividade.
* Cria o URI que localiza o serviço de host.
* Define as credenciais de segurança.
* Aplica as credenciais à conexão.
* Abre a conexão.
* Executa as tarefas específicas do aplicativo.
* Encerra a conexão.

No entanto, uma das principais diferenças é que o aplicativo cliente usa um canal para se conectar ao serviço de retransmissão. O serviço usa uma chamada a **ServiceHost**. O código usado para essas tarefas é fornecido no exemplo logo após o procedimento.

### <a name="implement-a-client-application"></a>Implementar um aplicativo cliente

1. Defina o modo de conectividade para `AutoDetect`. Adicione o código a seguir dentro do método `Main()` do aplicativo **EchoClient**.

    ```csharp
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```

1. Defina variáveis para conter os valores para o namespace de serviço e a chave SAS que são lidos do console.

    ```csharp
    Console.Write("Your Service Namespace: ");
    string serviceNamespace = Console.ReadLine();
    Console.Write("Your SAS Key: ");
    string sasKey = Console.ReadLine();
    ```

1. Crie o URI que define o local do host em seu projeto de Retransmissão.

    ```csharp
    Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
    ```

1. Crie o objeto de credencial para o ponto de extremidade do namespace de serviço.

    ```csharp
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```

1. Crie a fábrica de canais que carrega a configuração descrita no arquivo *App.config*.

    ```csharp
    ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));
    ```

    Uma fábrica de canais é um objeto WCF que cria um canal por meio do qual o os aplicativos cliente e de serviço se comunicam.

1. Aplique as credenciais.

    ```csharp
    channelFactory.Endpoint.Behaviors.Add(sasCredential);
    ```

1. Crie e abra o canal para o serviço.

    ```csharp
    IEchoChannel channel = channelFactory.CreateChannel();
    channel.Open();
    ```

1. Escreva a interface do usuário básico e a funcionalidade para o eco.

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

    O código usa a instância do objeto do canal como um proxy para o serviço.

1. Feche o canal e a fábrica.

    ```csharp
    channel.Close();
    channelFactory.Close();
    ```

### <a name="example-code-for-this-tutorial"></a>Exemplo de código para este tutorial

O código concluído deve ser exibido conforme mostrado a seguir. Esse código mostra como criar um aplicativo cliente, como chamar as operações do serviço e como fechar o cliente após a conclusão da chamada da operação.

```csharp
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
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

1. Selecione Ctrl+Shift+B para compilar a solução. Essa ação compila o projeto de cliente e o projeto de serviço criados nas etapas anteriores.
1. Antes de executar o aplicativo cliente, verifique se o aplicativo de serviço está em execução. Em **Gerenciador de Soluções**, clique com o botão direito do mouse na solução **EchoService** e, em seguida, selecione **Propriedades**.
1. Em **Páginas de Propriedades**, **Propriedades Comuns** > **Projeto de Inicialização**, escolha **Vários projetos de inicialização**. Verifique se **EchoService** aparece primeiro na lista.
1. Defina a caixa **Ação** dos projetos **EchoService** e **EchoClient** para **Iniciar**.

    ![Página de propriedades do projeto][5]

1. Selecione **Dependências do Projeto**. Em **Projetos**, selecione **EchoClient**. Em **Depende de**, verifique se **EchoService** está selecionado.

    ![Dependências do projeto][6]

1. Selecione **OK** para fechar as **Páginas de Propriedades**.
1. Selecione F5 para executar os dois projetos.
1. Ambas as janelas do console serão abertas e o nome do namespace será solicitado. O serviço precisa ser executado primeiro, portanto, na janela do console de **EchoService**, insira o namespace e, em seguida, selecione Enter.
1. Em seguida, o console solicitará sua chave SAS. Insira a chave SAS e selecione Enter.

    Eis aqui um exemplo de saída da janela do console. Os valores aqui são apenas exemplos.

    `Your Service Namespace: myNamespace`

    `Your SAS Key: <SAS key value>`

    O aplicativo de serviço imprime na janela do console o endereço no qual ele está escutando, como visto no exemplo a seguir.

    `Service address: sb://mynamespace.servicebus.windows.net/EchoService/`

    `Press [Enter] to exit`

1. Na janela do console **EchoClient**, digite as mesmas informações inseridas anteriormente para o aplicativo de serviço. Insira os mesmos valores do namespace de serviço e da chave SAS para o aplicativo cliente.
1. Depois de inserir esses valores, o cliente abre um canal para o serviço e solicita que você digite algum texto, como visto no exemplo de saída do console a seguir.

    `Enter text to echo (or [Enter] to exit):`

    Insira um texto para ser enviado ao aplicativo de serviço e selecione Enter. Esse texto é enviado para o serviço por meio da operação de serviço Echo e aparece na janela do console de serviço como a saída de exemplo a seguir.

    `Echoing: My sample text`

    O aplicativo cliente recebe o valor retornado da operação `Echo`, que é o texto original, e o exibe na janela do console. O texto a seguir é uma saída de exemplo da janela do console do cliente.

    `Server echoed: My sample text`

1. Você pode continuar a enviar mensagens de texto do cliente para o serviço dessa maneira. Quando terminar, selecione Enter nas janelas do console de cliente e de serviço para encerrar os dois aplicativos.

## <a name="next-steps"></a>Próximas etapas

Vá para o tutorial a seguir:

> [!div class="nextstepaction"]
>[Expor um serviço REST WCF local a um cliente fora da rede](service-bus-relay-rest-tutorial.md)

[2]: ./media/service-bus-relay-tutorial/configure-echoservice-console-app.png
[3]: ./media/service-bus-relay-tutorial/install-nuget-service-bus.png
[4]: ./media/service-bus-relay-tutorial/install-nuget-service-bus-client.png
[5]: ./media/service-bus-relay-tutorial/set-projects.png
[6]: ./media/service-bus-relay-tutorial/set-depend.png
