---
title: Tutorial do REST do usando Retransmissão do Azure | Microsoft Docs
description: Compile um aplicativo host simples de Retransmissão do Barramento de Serviço do Azure que expõe uma interface baseada em REST.
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: 1312b2db-94c4-4a48-b815-c5deb5b77a6a
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/06/2018
ms.author: spelluru
ms.openlocfilehash: 4e988724f842ff12cd599eba95c31006fe208fad
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68422899"
---
# <a name="azure-wcf-relay-rest-tutorial"></a>Tutorial do REST de Retransmissão de WCF do Azure
Este tutorial descreve como compilar um aplicativo host simples da Retransmissão do Azure que expõe uma interface baseada em REST. O REST permite que um cliente da Web, como, por exemplo, um navegador da Web, acesse as APIs de Barramento de Serviço por meio de solicitações HTTP.

Este tutorial usa o modelo de programação REST do WCF (Windows Communication Foundation) para construir um serviço REST na Retransmissão do Azure. Para saber mais, confira [Modelo de programação REST WCF](/dotnet/framework/wcf/feature-details/wcf-web-http-programming-model) e [Criando e implementando serviços](/dotnet/framework/wcf/designing-and-implementing-services) na documentação do WCF.

Neste tutorial, execute as seguintes etapas:

> [!div class="checklist"]
> * Criar um namespace de Retransmissão
> * Definir um contrato de serviço WCF baseado em REST
> * Implementar o contrato do WCF baseado em REST
> * Hospedar e executar o serviço WCF baseado em REST
> * Executar e testar o serviço

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará dos seguintes pré-requisitos:

- Uma assinatura do Azure. Se você não tiver [uma conta gratuita](https://azure.microsoft.com/free/), crie uma antes de começar.
- [Visual Studio 2015 ou posterior](https://www.visualstudio.com). Os exemplos neste tutorial usam o Visual Studio 2017.
- SDK do Azure para .NET. Instale-o a partir da [página de downloads do SDK](https://azure.microsoft.com/downloads/).

## <a name="create-a-relay-namespace"></a>Criar um namespace de Retransmissão

Para começar a usar os recursos de retransmissão no Azure, você deve primeiro criar um namespace de serviço. Um namespace fornece um contêiner de escopo para endereçar recursos do Azure dentro de seu aplicativo. Siga as [instruções aqui](relay-create-namespace-portal.md) para criar um namespace de Retransmissão.

## <a name="define-a-rest-based-wcf-service-contract-to-use-with-azure-relay"></a>Definir um contrato de serviço WCF baseado em REST para usar com a Retransmissão do Azure

Quando você cria um serviço no estilo REST do WCF, é preciso definir o contrato. O contrato especifica para quais operações o host oferece suporte. Uma operação de serviço pode ser considerada um método de serviço Web. Os contratos são criados pela definição de uma interface C++, C# ou Visual Basic. Cada método na interface corresponde a uma operação de serviço específica. O atributo [ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute) deve ser aplicado a cada interface, e o atributo [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute) deve ser aplicado a cada operação. Se um método em uma interface que tem o [ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute) não tiver o [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute), tal método não será exposto. O código usado para essas tarefas é mostrado no exemplo logo após o procedimento.

A principal diferença entre um contrato do WCF e um contrato no estilo REST é a adição de uma propriedade no [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute): [WebGetAttribute](/dotnet/api/system.servicemodel.web.webgetattribute). Esta propriedade permite mapear um método em sua interface para um método no outro lado da interface. Este exemplo usa o atributo [WebGetAttribute](/dotnet/api/system.servicemodel.web.webgetattribute) para vincular um método a HTTP GET. Isso permite que o Barramento de Serviço recupere e interprete de maneira precisa os comandos enviados à interface.

### <a name="to-create-a-contract-with-an-interface"></a>Para criar um contrato com uma interface

1. Abra o Visual Studio como administrador: clique com o botão direito no programa no menu **Iniciar** e, em seguida, clique em **Executar como administrador**.
2. Crie um novo projeto de aplicativo de console. Clique no menu **Arquivo** e selecione **Novo** e, em seguida, **Projeto**. Na caixa de diálogo **Novo Projeto**, clique em **Visual C#** , selecione o modelo **Aplicativo de Console** e chame-o de **ImageListener**. Use o **Local** padrão. Clique em **OK** para criar o projeto.
3. Para um projeto C#, o Visual Studio cria um arquivo `Program.cs`. Essa classe contém um método `Main()` vazio, necessário para um projeto de aplicativo de console ser criado corretamente.
4. Adicione referências ao Barramento de Serviço e ao **System.ServiceModel.dll** ao projeto instalando o pacote do NuGet do Barramento de Serviço. Esse pacote adiciona automaticamente referências para as bibliotecas do Barramento de Serviço, bem como o WCF **System.ServiceModel**. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **ImageListener** e clique em **Gerenciar Pacotes NuGet**. Clique na guia **Procurar** e procure `Microsoft Azure Service Bus`. Clique em **Instalar**e aceite os termos de uso.
5. Você precisa adicionar explicitamente uma referência a **System.ServiceModel.Web.dll** ao projeto:
   
    a. No Gerenciador de Soluções, clique com o botão direito do mouse na pasta **Referências**, na pasta do projeto e clique em **Adicionar Referência**.
   
    b. Na caixa de diálogo **Adicionar Referência**, clique na guia **Estrutura** no lado esquerdo e, na caixa **Pesquisar**, digite **System.ServiceModel.Web**. Marque a caixa de seleção **System.ServiceModel.Web** e clique em **OK**.
6. Adicione as instruções `using` abaixo na parte superior do arquivo Program.cs.
   
    ```csharp
    using System.ServiceModel;
    using System.ServiceModel.Channels;
    using System.ServiceModel.Web;
    using System.IO;
    ```
   
    [System.ServiceModel](/dotnet/api/system.servicemodel) é o namespace que permite o acesso programático aos recursos básicos do WCF. A Retransmissão de WCF usa vários dos objetos e atributos do WCF para definir contratos de serviço. Você usa este namespace na maioria dos seus aplicativos de retransmissão. Da mesma forma, [System.ServiceModel.Channels](/dotnet/api/system.servicemodel.channels) ajuda a definir o canal, que é o objeto por meio do qual você se comunica com a Retransmissão do Azure e o navegador da Web do cliente. Por fim, [System.ServiceModel.Web](/dotnet/api/system.servicemodel.web) contém os tipos que permitem a criação de aplicativos baseados na Web.
7. Renomeie o namespace `ImageListener` para **Microsoft.ServiceBus.Samples**.
   
    ```csharp
    namespace Microsoft.ServiceBus.Samples
    {
        ...
    ```
8. Imediatamente após a chave de abertura da declaração do namespace, defina uma nova interface chamada **IImageContract** e aplique o atributo **ServiceContractAttribute** à interface com um valor de `https://samples.microsoft.com/ServiceModel/Relay/`. O valor do namespace é diferente do namespace que você usa em todo o escopo do seu código. O valor do namespace é usado como um identificador exclusivo para este contrato e deve ter informações sobre a versão. Para saber mais, veja [Controle de Versão do Serviço](https://go.microsoft.com/fwlink/?LinkID=180498). Especificar o namespace de forma explícita impede a adição do valor de namespace padrão ao nome do contrato.
   
    ```csharp
    [ServiceContract(Name = "ImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/RESTTutorial1")]
    public interface IImageContract
    {
    }
    ```
9. Dentro da interface `IImageContract`, declare um método para a operação individual exposta pelo contrato `IImageContract` na interface e aplique o atributo `OperationContractAttribute` ao método que você deseja expor como parte do contrato de Barramento de Serviço público.
   
    ```csharp
    public interface IImageContract
    {
        [OperationContract]
        Stream GetImage();
    }
    ```
10. No atributo **OperationContract**, adicione o valor **WebGet**.
    
    ```csharp
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }
    ```
    
    Isso permite que o serviço de retransmissão encaminhe as solicitações HTTP GET para `GetImage` e converta os valores retornados de `GetImage` em uma resposta HTTP GETRESPONSE. Posteriormente no tutorial, você usará um navegador da Web para acessar esse método e exibir a imagem no navegador.
11. Logo após a definição `IImageContract`, declare um canal que herde das interfaces `IImageContract` e `IClientChannel`.
    
    ```csharp
    public interface IImageChannel : IImageContract, IClientChannel { }
    ```
    
    Um canal é o objeto WCF por meio do qual o serviço e o cliente trocam informações. Posteriormente, você criará o canal em seu aplicativo host. A Retransmissão do Azure usa este canal para passar as solicitações HTTP GET do navegador para a implementação **GetImage**. A retransmissão também usa o canal para obter o valor retornado de **GetImage** e convertê-lo em uma GETRESPONSE HTTP para o navegador do cliente.
12. No menu **Compilar**, clique em **Compilar Solução** para confirmar a precisão de seu trabalho até o momento.

### <a name="example"></a>Exemplo
O código a seguir mostra uma interface básica que define um contrato de Retransmissão de WCF.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "IImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

## <a name="implement-the-rest-based-wcf-service-contract"></a>Implementar o contrato de serviço WCF baseado em REST
A criação de um serviço de Retransmissão de WCF no estilo REST exige primeiro a criação do contrato, que é definido por meio de uma interface. A próxima etapa é implementar a interface. Isso envolve a criação de uma classe chamada **ImageService** que implementa a interface **IImageContract** definida pelo usuário. Depois de implementar o contrato, configure a interface usando um arquivo App.config. O arquivo de configuração contém as informações necessárias para o aplicativo, como o nome do serviço, o nome do contrato e o tipo de protocolo usado para se comunicar com o serviço de retransmissão. O código usado para essas tarefas é fornecido no exemplo logo após o procedimento.

Assim como nas etapas anteriores, há pouca diferença entre a implementação de um contrato no estilo REST e um contrato de Retransmissão de WCF.

### <a name="to-implement-a-rest-style-service-bus-contract"></a>Para implementar um contrato de Barramento de Serviço no estilo REST
1. Crie uma nova classe chamada **ImageService** diretamente após a definição da interface **IImageContract**. A classe **ImageService** implementa a interface **IImageContract**.
   
    ```csharp
    class ImageService : IImageContract
    {
    }
    ```
    Assim como outras implementações de interface, você pode implementar a definição em um arquivo diferente. No entanto, para este tutorial, a implementação é exibida no mesmo arquivo que a definição de interface e o método `Main()`.
2. Aplique o atributo [ServiceBehaviorAttribute](/dotnet/api/system.servicemodel.servicebehaviorattribute) à classe **IImageService** para indicar que a classe é uma implementação de um contrato do WCF.
   
    ```csharp
    [ServiceBehavior(Name = "ImageService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
    }
    ```
   
    Conforme mencionado anteriormente, esse namespace não é um namespace tradicional. Em vez disso, ele faz parte da arquitetura do WCF que identifica o contrato. Para saber mais, confira o artigo [Nomes de contrato de dados](https://msdn.microsoft.com/library/ms731045.aspx) na documentação do WCF.
3. Adicione uma imagem .jpg ao seu projeto.  
   
    Esta é uma imagem exibida pelo serviço no navegador receptor. Clique com o botão direito do mouse em seu projeto e clique em **Adicionar**. Em seguida, clique em **Item Existente**. Use a caixa de diálogo **Adicionar Item Existente** para navegar até um .jpg adequado e,em seguida, clique em **Adicionar**.
   
    Ao adicionar o arquivo, certifique-se de que a opção **Todos os Arquivos** esteja selecionada na lista suspensa ao lado do campo **Nome do arquivo:** . O restante deste tutorial supõe que o nome da imagem seja "image.jpg". Se você tiver um arquivo diferente, será necessário renomear a imagem ou alterar seu código para compensar.
4. Para ter certeza de que o serviço em execução consegue encontrar o arquivo de imagem, no **Gerenciador de Soluções**, clique com o botão direito do mouse no arquivo de imagem e clique em **Propriedades**. No painel **Propriedades**, defina **Copiar para o Diretório de Saída** como **Copiar se for mais recente**.
5. Adicione uma referência ao assembly **System.Drawing.dll** ao projeto, e adicione também as seguintes instruções `using` associadas.  
   
    ```csharp
    using System.Drawing;
    using System.Drawing.Imaging;
    using Microsoft.ServiceBus;
    using Microsoft.ServiceBus.Web;
    ```
6. Na classe **ImageService**, adicione o seguinte construtor que carrega o bitmap e o prepara para envio ao navegador do cliente.
   
    ```csharp
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";
   
        Image bitmap;
   
        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }
    }
    ```
7. Logo após o código anterior, adicione o seguinte método **GetImage** à classe **ImageService** para retornar uma mensagem HTTP que contém a imagem.
   
    ```csharp
    public Stream GetImage()
    {
        MemoryStream stream = new MemoryStream();
        this.bitmap.Save(stream, ImageFormat.Jpeg);
   
        stream.Position = 0;
        WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";
   
        return stream;
    }
    ```
   
    Essa implementação usa **MemoryStream** para recuperar a imagem e prepará-la para transmissão por streaming para o navegador. Ela começa a posição da transmissão em zero, declara o conteúdo da transmissão como jpeg e transmite as informações.
8. No menu **Compilar**, clique em **Solução de Compilação**.

### <a name="to-define-the-configuration-for-running-the-web-service-on-service-bus"></a>Para definir a configuração a fim de executar o serviço da Web no Barramento de Serviço
1. No **Gerenciador de Soluções**, clique duas vezes em **App.config** para abri-lo no editor do Visual Studio.
   
    O arquivo **App.config** inclui o nome do serviço, o ponto de extremidade (ou seja, o local que a Retransmissão do Azure expõe para os clientes e hosts se comunicarem) e a associação (o tipo de protocolo usado para comunicação). A principal diferença é que o ponto de extremidade de serviço configurado refere-se a uma associação [WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding).
2. O elemento XML `<system.serviceModel>` é um elemento WCF que define um ou mais serviços. Aqui, ele é usado para definir o nome do serviço e o ponto de extremidade. Na parte inferior do elemento `<system.serviceModel>` (mas ainda dentro de `<system.serviceModel>`), adicione um elemento `<bindings>` que tenha o seguinte conteúdo. Isso define as associações usadas no aplicativo. É possível definir várias associações, mas para este tutorial você definirá apenas uma.
   
    ```xml
    <bindings>
        <!-- Application Binding -->
        <webHttpRelayBinding>
            <binding name="default">
                <security relayClientAuthenticationType="None" />
            </binding>
        </webHttpRelayBinding>
    </bindings>
    ```
   
    O código anterior define uma associação [WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) da Retransmissão de WCF com **relayClientAuthenticationType** definido como **None**. Essa configuração indica que um ponto de extremidade usando essa associação não exige uma credencial de cliente.
3. Após o elemento `<bindings>`, adicione um elemento `<services>`. Assim como nas associações, você pode definir vários serviços em um único arquivo de configuração. No entanto, para este tutorial, você definirá apenas um.
   
    ```xml
    <services>
        <!-- Application Service -->
        <service name="Microsoft.ServiceBus.Samples.ImageService"
             behaviorConfiguration="default">
            <endpoint name="RelayEndpoint"
                    contract="Microsoft.ServiceBus.Samples.IImageContract"
                    binding="webHttpRelayBinding"
                    bindingConfiguration="default"
                    behaviorConfiguration="sbTokenProvider"
                    address="" />
        </service>
    </services>
    ```
   
    Esta etapa configura um serviço que usa o padrão previamente definido **webHttpRelayBinding**. Ele também usa o padrão **sbTokenProvider**, definido na próxima etapa.
4. Após o elemento `<services>`, crie um elemento `<behaviors>` com o conteúdo a seguir, substituindo "SAS_KEY" pela chave *Assinatura de Acesso Compartilhado* (SAS) obtida anteriormente no [portal do Azure][Azure portal].
   
    ```xml
    <behaviors>
        <endpointBehaviors>
            <behavior name="sbTokenProvider">
                <transportClientEndpointBehavior>
                    <tokenProvider>
                        <sharedAccessSignature keyName="RootManageSharedAccessKey" key="YOUR_SAS_KEY" />
                    </tokenProvider>
                </transportClientEndpointBehavior>
            </behavior>
            </endpointBehaviors>
            <serviceBehaviors>
                <behavior name="default">
                    <serviceDebug httpHelpPageEnabled="false" httpsHelpPageEnabled="false" />
                </behavior>
            </serviceBehaviors>
    </behaviors>
    ```
5. Ainda em App.config, no elemento `<appSettings>`, substitua todo o valor da cadeia de conexão pela cadeia de conexão obtida anteriormente no portal. 
   
    ```xml
    <appSettings>
       <!-- Service Bus specific app settings for messaging connections -->
       <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SAS_KEY"/>
    </appSettings>
    ```
6. No menu **Compilar**, clique em **Compilar Solução** para compilar a solução inteira.

### <a name="example"></a>Exemplo
O código a seguir mostra o contrato e a implementação do serviço para um serviço baseado em REST que está em execução no Barramento de Serviço usando a associação **WebHttpRelayBinding**.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;
using System.Drawing;
using System.Drawing.Imaging;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Web;

namespace Microsoft.ServiceBus.Samples
{


    [ServiceContract(Name = "ImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }

        public Stream GetImage()
        {
            MemoryStream stream = new MemoryStream();
            this.bitmap.Save(stream, ImageFormat.Jpeg);

            stream.Position = 0;
            WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

            return stream;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

O exemplo a seguir mostra o arquivo App.config associado ao serviço.

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2"/>
    </startup>
    <system.serviceModel>
        <extensions>
            <!-- In this extension section we are introducing all known service bus extensions. User can remove the ones they don't need. -->
            <behaviorExtensions>
                <add name="connectionStatusBehavior"
                    type="Microsoft.ServiceBus.Configuration.ConnectionStatusElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="transportClientEndpointBehavior"
                    type="Microsoft.ServiceBus.Configuration.TransportClientEndpointBehaviorElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="serviceRegistrySettings"
                    type="Microsoft.ServiceBus.Configuration.ServiceRegistrySettingsElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </behaviorExtensions>
            <bindingElementExtensions>
                <add name="netMessagingTransport"
                    type="Microsoft.ServiceBus.Messaging.Configuration.NetMessagingTransportExtensionElement, Microsoft.ServiceBus,  Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="tcpRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.TcpRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="httpRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.HttpRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="httpsRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.HttpsRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="onewayRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.RelayedOnewayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </bindingElementExtensions>
            <bindingExtensions>
                <add name="basicHttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.BasicHttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="webHttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.WebHttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="ws2007HttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.WS2007HttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netOnewayRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetOnewayRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netEventRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetEventRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netMessagingBinding"
                    type="Microsoft.ServiceBus.Messaging.Configuration.NetMessagingBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </bindingExtensions>
        </extensions>
      <bindings>
        <!-- Application Binding -->
        <webHttpRelayBinding>
          <binding name="default">
            <security relayClientAuthenticationType="None" />
          </binding>
        </webHttpRelayBinding>
      </bindings>
      <services>
        <!-- Application Service -->
        <service name="Microsoft.ServiceBus.Samples.ImageService"
             behaviorConfiguration="default">
          <endpoint name="RelayEndpoint"
                  contract="Microsoft.ServiceBus.Samples.IImageContract"
                  binding="webHttpRelayBinding"
                  bindingConfiguration="default"
                  behaviorConfiguration="sbTokenProvider"
                  address="" />
        </service>
      </services>
      <behaviors>
        <endpointBehaviors>
          <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
              <tokenProvider>
                <sharedAccessSignature keyName="RootManageSharedAccessKey" key="YOUR_SAS_KEY" />
              </tokenProvider>
            </transportClientEndpointBehavior>
          </behavior>
        </endpointBehaviors>
        <serviceBehaviors>
          <behavior name="default">
            <serviceDebug httpHelpPageEnabled="false" httpsHelpPageEnabled="false" />
          </behavior>
        </serviceBehaviors>
      </behaviors>
    </system.serviceModel>
    <appSettings>
        <!-- Service Bus specific app settings for messaging connections -->
        <add key="Microsoft.ServiceBus.ConnectionString"
            value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SAS_KEY>"/>
    </appSettings>
</configuration>
```

## <a name="host-the-rest-based-wcf-service-to-use-azure-relay"></a>Hospedar o serviço WCF baseado em REST para usar a Retransmissão do Azure
Esta etapa descreve como executar um serviço Web usando um aplicativo de console com Retransmissão de WCF. Confira uma lista completa com os códigos escritos nesta etapa logo após o procedimento.

### <a name="to-create-a-base-address-for-the-service"></a>Para criar um endereço base para o serviço
1. Na declaração de função `Main()`, crie uma variável para armazenar o namespace de seu projeto. Substitua `yourNamespace` pelo nome do namespace de Retransmissão que você criou anteriormente.
   
    ```csharp
    string serviceNamespace = "yourNamespace";
    ```
    O Barramento de Serviço usa o nome do namespace para criar um URI exclusivo.
2. Crie uma instância de `Uri` para o endereço básico do serviço que está baseado no namespace.
   
    ```csharp
    Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");
    ```

### <a name="to-create-and-configure-the-web-service-host"></a>Para criar e configurar o host do serviço Web
* Crie o host do serviço Web usando o endereço URI criado anteriormente nesta seção.
  
    ```csharp
    WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
    ```
    O host do serviço é o objeto do WCF que instancia o aplicativo host. Este exemplo passa o tipo de host que você deseja criar (um **ImageService**) e também o endereço no qual você deseja expor o aplicativo host.

### <a name="to-run-the-web-service-host"></a>Para executar o host do serviço Web
1. Abra o arquivo serviço.
   
    ```csharp
    host.Open();
    ```
    O serviço está em execução.
2. É exibida uma mensagem indicando que o serviço está em execução e dizendo como parar o serviço.
   
    ```csharp
    Console.WriteLine("Copy the following address into a browser to see the image: ");
    Console.WriteLine(address + "GetImage");
    Console.WriteLine();
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```
3. Quando terminar, feche o host do serviço.
   
    ```csharp
    host.Close();
    ```

### <a name="example"></a>Exemplo
O exemplo a seguir inclui o contrato e a implementação do serviço das etapas anteriores no tutorial e hospeda o serviço em um aplicativo de console. Compile o código a seguir em um arquivo executável chamado ImageListener.exe.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;
using System.Drawing;
using System.Drawing.Imaging;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Web;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "ImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }

        public Stream GetImage()
        {
            MemoryStream stream = new MemoryStream();
            this.bitmap.Save(stream, ImageFormat.Jpeg);

            stream.Position = 0;
            WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

            return stream;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            string serviceNamespace = "InsertServiceNamespaceHere";
            Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");

            WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
            host.Open();

            Console.WriteLine("Copy the following address into a browser to see the image: ");
            Console.WriteLine(address + "GetImage");
            Console.WriteLine();
            Console.WriteLine("Press [Enter] to exit");
            Console.ReadLine();

            host.Close();
        }
    }
}
```

## <a name="run-and-test-the-service"></a>Executar e testar o serviço
Após compilar a solução, faça o seguinte para executar o aplicativo:

1. Pressione **F5** ou navegue até a localização do arquivo executável (ImageListener\bin\Debug\ImageListener.exe) para executar o serviço. Mantenha o aplicativo em execução, pois ele é necessário para executar a próxima etapa.
2. Copie e cole o endereço do prompt de comando em um navegador para ver a imagem.
3. Quando tiver terminado, pressione **Enter** na janela do prompt de comando para fechar o aplicativo.

## <a name="next-steps"></a>Próximas etapas
Agora que você criou um aplicativo que usa o serviço de Retransmissão do Azure, leia os seguintes artigos para saber mais:

* [Visão geral da Retransmissão do Azure](relay-what-is-it.md)
* [Como usar o serviço de Retransmissão de WCF com o .NET](service-bus-relay-tutorial.md)

[Azure portal]: https://portal.azure.com
