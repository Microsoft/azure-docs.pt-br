---
title: "Ajudar a proteger a comunicação para serviços no Azure Service Fabric | Microsoft Docs"
description: "Visão geral de como ajudar a proteger a comunicação para os Reliable Services executados em um cluster do Azure Service Fabric."
services: service-fabric
documentationcenter: java
author: PavanKunapareddyMSFT
manager: timlt
ms.assetid: 
ms.service: service-fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 06/30/2017
ms.author: pakunapa
ms.openlocfilehash: c4634e3d8efb1745fffcfe3e647e43d867038716
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="help-secure-communication-for-services-in-azure-service-fabric"></a>Ajudar a proteger a comunicação para serviços no Azure Service Fabric
> [!div class="op_single_selector"]
> * [C# em Windows](service-fabric-reliable-services-secure-communication.md)
> * [Java no Linux](service-fabric-reliable-services-secure-communication-java.md)
>
>

## <a name="help-secure-a-service-when-youre-using-service-remoting"></a>Ajudar a proteger um serviço quando você estiver usando a comunicação remota de serviço
Usaremos um [exemplo](service-fabric-reliable-services-communication-remoting-java.md) existente que explica como configurar a comunicação remota para os Reliable Services. Para ajudar a proteger um serviço quando você estiver usando a comunicação remota de serviço, siga estas etapas:

1. Crie uma interface, `HelloWorldStateless`, que define os métodos que estarão disponíveis para chamada de procedimento remoto no seu serviço. Seu serviço usará `FabricTransportServiceRemotingListener`, declarado no pacote do `microsoft.serviceFabric.services.remoting.fabricTransport.runtime`. Esta é uma implementação de `CommunicationListener` que fornece recursos de comunicação remota.

    ```java
    public interface HelloWorldStateless extends Service {
        CompletableFuture<String> getHelloWorld();
    }

    class HelloWorldStatelessImpl extends StatelessService implements HelloWorldStateless {
        @Override
        protected List<ServiceInstanceListener> createServiceInstanceListeners() {
            ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
            listeners.add(new ServiceInstanceListener((context) -> {
                return new FabricTransportServiceRemotingListener(context,this);
            }));
        return listeners;
        }

        public CompletableFuture<String> getHelloWorld() {
            return CompletableFuture.completedFuture("Hello World!");
        }
    }
    ```
2. Adicionar credenciais de segurança e configurações de ouvinte.

    Certifique-se de que o certificado que você deseja usar para ajudar proteger a comunicação do serviço esteja instalado em todos os nós do cluster. Há duas maneiras de fornecer configurações de ouvinte e credenciais de segurança:

   1. Fornecê-las usando um [pacote de configuração](service-fabric-application-model.md):

       Adicione uma seção `TransportSettings` ao arquivo settings.xml.

       ```xml
       <!--Section name should always end with "TransportSettings".-->
       <!--Here we are using a prefix "HelloWorldStateless".-->
        <Section Name="HelloWorldStatelessTransportSettings">
            <Parameter Name="MaxMessageSize" Value="10000000" />
            <Parameter Name="SecurityCredentialsType" Value="X509_2" />
            <Parameter Name="CertificatePath" Value="/path/to/cert/BD1C71E248B8C6834C151174DECDBDC02DE1D954.crt" />
            <Parameter Name="CertificateProtectionLevel" Value="EncryptandSign" />
            <Parameter Name="CertificateRemoteThumbprints" Value="BD1C71E248B8C6834C151174DECDBDC02DE1D954" />
        </Section>

       ```

       Nesse caso, o método `createServiceInstanceListeners` terá esta aparência:

       ```java
        protected List<ServiceInstanceListener> createServiceInstanceListeners() {
            ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
            listeners.add(new ServiceInstanceListener((context) -> {
                return new FabricTransportServiceRemotingListener(context,this, FabricTransportRemotingListenerSettings.loadFrom(HelloWorldStatelessTransportSettings));
            }));
            return listeners;
        }
       ```

        Se você adicionar uma seção `TransportSettings` ao arquivo settings.xml sem nenhum prefixo, `FabricTransportListenerSettings` carregará todas as configurações dessa seção por padrão.

        ```xml
        <!--"TransportSettings" section without any prefix.-->
        <Section Name="TransportSettings">
            ...
        </Section>
        ```
        Nesse caso, o método `CreateServiceInstanceListeners` terá esta aparência:

        ```java
        protected List<ServiceInstanceListener> createServiceInstanceListeners() {
            ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
            listeners.add(new ServiceInstanceListener((context) -> {
                return new FabricTransportServiceRemotingListener(context,this);
            }));
            return listeners;
        }
       ```
3. Ao chamar métodos em um serviço protegido usando a pilha de comunicação remota, em vez de usar a classe `microsoft.serviceFabric.services.remoting.client.ServiceProxyBase` para criar um proxy de serviço, use `microsoft.serviceFabric.services.remoting.client.FabricServiceProxyFactory`.

    Se o código cliente estiver sendo executado como parte de um serviço, você poderá carregar `FabricTransportSettings` do arquivo settings.xml. Crie uma seção TransportSettings semelhante ao código de serviço, conforme mostrado acima. Faça as alterações a seguir no código cliente:

    ```java

    FabricServiceProxyFactory serviceProxyFactory = new FabricServiceProxyFactory(c -> {
            return new FabricTransportServiceRemotingClientFactory(FabricTransportRemotingSettings.loadFrom("TransportPrefixTransportSettings"), null, null, null, null);
        }, null)

    HelloWorldStateless client = serviceProxyFactory.createServiceProxy(HelloWorldStateless.class,
        new URI("fabric:/MyApplication/MyHelloWorldService"));

    CompletableFuture<String> message = client.getHelloWorld();

    ```
