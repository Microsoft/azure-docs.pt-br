---
title: Proteger comunicações remotas do serviço com Java no Azure Service Fabric | Microsoft Docs
description: Saiba como proteger a comunicação remota do serviço com base na comunicação para serviços confiáveis de Java executados em um cluster do Azure Service Fabric.
services: service-fabric
documentationcenter: java
author: PavanKunapareddyMSFT
manager: chackdan
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 06/30/2017
ms.author: pakunapa
ms.openlocfilehash: b465ab602a14285f8cf40b24ce1dfa9c763fecb8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60773342"
---
# <a name="secure-service-remoting-communications-in-a-java-service"></a>Proteger as comunicações remotas do serviço em um serviço Java
> [!div class="op_single_selector"]
> * [C# em Windows](service-fabric-reliable-services-secure-communication.md)
> * [Java no Linux](service-fabric-reliable-services-secure-communication-java.md)
>
>

A segurança é um dos aspectos mais importantes da comunicação. A estrutura de aplicativo dos Reliable Services fornece algumas pilhas e ferramentas de comunicação predefinidas que você pode usar para aprimorar a segurança. Este artigo discute sobre como melhorar a segurança quando você estiver usando a comunicação remota em um serviço Java. Ele compila um [exemplo](service-fabric-reliable-services-communication-remoting-java.md) existente que explica como configurar a comunicação remota para os Reliable Services escritos em Java. 

Para ajudar a proteger um serviço quando você estiver usando a comunicação remota de serviço com serviços Java, siga estas etapas:

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

    Certifique-se de que o certificado que você deseja usar para ajudar proteger a comunicação do serviço esteja instalado em todos os nós do cluster. Para serviços executados em Linux, o certificado deve estar disponível como um arquivo em formato PEM; tanto um arquivo `.pem` que contenha o certificado e chave privada ou um arquivo `.crt` que contenha o certificado e um arquivo `.key` que contenha a chave privada. Para saber mais, consulte [Local e formato de certificados X.509 em nós do Linux](./service-fabric-configure-certificates-linux.md#location-and-format-of-x509-certificates-on-linux-nodes).
    
    Há duas maneiras de fornecer configurações de ouvinte e credenciais de segurança:

   1. Fornecê-las usando um [pacote de configuração](service-fabric-application-and-service-manifests.md):

       Adicione uma seção nomeada `TransportSettings` ao arquivo settings.xml.

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
