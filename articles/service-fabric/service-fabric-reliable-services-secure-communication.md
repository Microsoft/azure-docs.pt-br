---
title: Proteger comunicações remotas do serviço com C# no Azure Service Fabric | Microsoft Docs
description: Saiba como proteger a comunicação remota do serviço com base na comunicação para serviços confiáveis C# executados em um cluster do Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: suchiagicha
manager: chackdan
editor: vturecek
ms.assetid: fc129c1a-fbe4-4339-83ae-0e69a41654e0
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 04/20/2017
ms.author: chackdan
ms.openlocfilehash: 193df34a092d9feea3e0cf370fe38543395dad92
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67871724"
---
# <a name="secure-service-remoting-communications-in-a-c-service"></a>Proteger as comunicações remotas do serviço em um serviço C#
> [!div class="op_single_selector"]
> * [C# em Windows](service-fabric-reliable-services-secure-communication.md)
> * [Java no Linux](service-fabric-reliable-services-secure-communication-java.md)
>
>

A segurança é um dos aspectos mais importantes da comunicação. A estrutura de aplicativo dos Reliable Services fornece algumas pilhas e ferramentas de comunicação predefinidas que você pode usar para aprimorar a segurança. Este artigo discute sobre como melhorar a segurança quando você estiver usando a comunicação remota em um serviço C#. Ele compila um [exemplo](service-fabric-reliable-services-communication-remoting.md) existente que explica como configurar a comunicação remota para os Reliable Services escritos em C#. 

Para ajudar a proteger um serviço quando você estiver usando a comunicação remota de serviço com serviços C#, siga estas etapas:

1. Crie uma interface, `IHelloWorldStateful`, que define os métodos que estarão disponíveis para chamada de procedimento remoto no seu serviço. Seu serviço usará `FabricTransportServiceRemotingListener`, que é declarado no namespace `Microsoft.ServiceFabric.Services.Remoting.FabricTransport.Runtime`. Esta é uma implementação de `ICommunicationListener` que fornece recursos de comunicação remota.

    ```csharp
    public interface IHelloWorldStateful : IService
    {
        Task<string> GetHelloWorld();
    }

    internal class HelloWorldStateful : StatefulService, IHelloWorldStateful
    {
        protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
        {
            return new[]{
                    new ServiceReplicaListener(
                        (context) => new FabricTransportServiceRemotingListener(context,this))};
        }

        public Task<string> GetHelloWorld()
        {
            return Task.FromResult("Hello World!");
        }
    }
    ```
2. Adicionar credenciais de segurança e configurações de ouvinte.

    Certifique-se de que o certificado que você deseja usar para ajudar proteger a comunicação do serviço esteja instalado em todos os nós do cluster. 
    
    > [!NOTE]
    > Em nós do Linux, o certificado deve estar presente como arquivos no formato PEM no diretório */var/lib/sfcerts*. Para saber mais, consulte [Local e formato de certificados X.509 em nós do Linux](./service-fabric-configure-certificates-linux.md#location-and-format-of-x509-certificates-on-linux-nodes). 

    Há duas maneiras de fornecer configurações de ouvinte e credenciais de segurança:

   1. Fornecê-las diretamente no código do serviço:

       ```csharp
       protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
       {
           FabricTransportRemotingListenerSettings  listenerSettings = new FabricTransportRemotingListenerSettings
           {
               MaxMessageSize = 10000000,
               SecurityCredentials = GetSecurityCredentials()
           };
           return new[]
           {
               new ServiceReplicaListener(
                   (context) => new FabricTransportServiceRemotingListener(context,this,listenerSettings))
           };
       }

       private static SecurityCredentials GetSecurityCredentials()
       {
           // Provide certificate details.
           var x509Credentials = new X509Credentials
           {
               FindType = X509FindType.FindByThumbprint,
               FindValue = "4FEF3950642138446CC364A396E1E881DB76B48C",
               StoreLocation = StoreLocation.LocalMachine,
               StoreName = "My",
               ProtectionLevel = ProtectionLevel.EncryptAndSign
           };
           x509Credentials.RemoteCommonNames.Add("ServiceFabric-Test-Cert");
           x509Credentials.RemoteCertThumbprints.Add("9FEF3950642138446CC364A396E1E881DB76B483");
           return x509Credentials;
       }
       ```
   2. Fornecê-las usando um [pacote de configuração](service-fabric-application-and-service-manifests.md):

       Adicione uma seção nomeada `TransportSettings` ao arquivo settings.xml.

       ```xml
       <Section Name="HelloWorldStatefulTransportSettings">
           <Parameter Name="MaxMessageSize" Value="10000000" />
           <Parameter Name="SecurityCredentialsType" Value="X509" />
           <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
           <Parameter Name="CertificateFindValue" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
           <Parameter Name="CertificateRemoteThumbprints" Value="9FEF3950642138446CC364A396E1E881DB76B483" />
           <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
           <Parameter Name="CertificateStoreName" Value="My" />
           <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
           <Parameter Name="CertificateRemoteCommonNames" Value="ServiceFabric-Test-Cert" />
       </Section>
       ```

       Nesse caso, o método `CreateServiceReplicaListeners` terá esta aparência:

       ```csharp
       protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
       {
           return new[]
           {
               new ServiceReplicaListener(
                   (context) => new FabricTransportServiceRemotingListener(
                       context,this,FabricTransportRemotingListenerSettings .LoadFrom("HelloWorldStatefulTransportSettings")))
           };
       }
       ```

        Se você adicionar uma seção `TransportSettings` ao arquivo settings.xml, `FabricTransportRemotingListenerSettings` carregará todas as configurações dessa seção por padrão.

        ```xml
        <!--"TransportSettings" section .-->
        <Section Name="TransportSettings">
            ...
        </Section>
        ```
        Nesse caso, o método `CreateServiceReplicaListeners` terá esta aparência:

        ```csharp
        protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
        {
            return new[]
            {
                return new[]{
                        new ServiceReplicaListener(
                            (context) => new FabricTransportServiceRemotingListener(context,this))};
            };
        }
        ```
3. Ao chamar métodos em um serviço protegido usando a pilha de comunicação remota, em vez de usar a classe `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` para criar um proxy de serviço, use `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxyFactory`. Passe `FabricTransportRemotingSettings`, que contém `SecurityCredentials`.

    ```csharp

    var x509Credentials = new X509Credentials
    {
        FindType = X509FindType.FindByThumbprint,
        FindValue = "9FEF3950642138446CC364A396E1E881DB76B483",
        StoreLocation = StoreLocation.LocalMachine,
        StoreName = "My",
        ProtectionLevel = ProtectionLevel.EncryptAndSign
    };
    x509Credentials.RemoteCommonNames.Add("ServiceFabric-Test-Cert");
    x509Credentials.RemoteCertThumbprints.Add("4FEF3950642138446CC364A396E1E881DB76B48C");

    FabricTransportRemotingSettings transportSettings = new FabricTransportRemotingSettings
    {
        SecurityCredentials = x509Credentials,
    };

    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(transportSettings));

    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

    Se o código cliente estiver sendo executado como parte de um serviço, você poderá carregar `FabricTransportRemotingSettings` do arquivo settings.xml. Crie uma seção HelloWorldClientTransportSettings semelhante ao código de serviço, conforme mostrado acima. Faça as alterações a seguir no código cliente:

    ```csharp
    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(FabricTransportRemotingSettings.LoadFrom("HelloWorldClientTransportSettings")));

    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

    Se o cliente não estiver sendo executado como parte de um serviço, você poderá criar um arquivo client_name.settings.xml no mesmo local em que client_name.exe estiver. Em seguida, crie uma seção TransportSettings nesse arquivo.

    De forma semelhante ao serviço, se você adicionar uma seção `TransportSettings` em client settings.xml/client_name.settings.xml, o `FabricTransportRemotingSettings` carregará por padrão todas as configurações dessa seção.

    Nesse caso, o código anterior é ainda mais simplificado:  

    ```csharp

    IHelloWorldStateful client = ServiceProxy.Create<IHelloWorldStateful>(
                 new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```


Como próxima etapa, leia [API Web com OWIN no Reliable Services](service-fabric-reliable-services-communication-webapi.md).
