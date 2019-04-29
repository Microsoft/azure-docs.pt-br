---
title: Conectar-se e comunicar-se com serviços no Azure Service Fabric | Microsoft Docs
description: Saiba como resolver, conectar-se e comunicar-se com serviços no Service Fabric.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: msfussell
ms.assetid: 7d1052ec-2c9f-443d-8b99-b75c97266e6c
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/01/2017
ms.author: vturecek
ms.openlocfilehash: 55a0a1a8097ea46c7a3407b5f42824973edcf1a2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60882220"
---
# <a name="connect-and-communicate-with-services-in-service-fabric"></a>Conectar e se comunicar com serviços no Service Fabric
No Service Fabric, um serviço é executado em algum lugar em um cluster do Service Fabric, normalmente distribuído entre várias VMs. Ele pode ser movido de um local para outro, pelo proprietário do serviço ou automaticamente pelo Service Fabric. Os serviços não estão estaticamente vinculados a um determinado computador ou endereço.

Um aplicativo do Service Fabric geralmente é composto por vários serviços diferentes, em que cada serviço executa uma tarefa específica. Esses serviços podem se comunicar entre si para formar uma função completa, tal como partes diferentes de renderização de um aplicativo Web. Também existem aplicativos cliente que se conectam e se comunicam com os serviços. Este documento aborda como configurar a comunicação com os serviços do Service Fabric e entre eles.

## <a name="bring-your-own-protocol"></a>Trazer seu próprio protocolo
O Service Fabric ajuda a gerenciar o ciclo de vida dos seus serviços, mas ele não tome decisões sobre o que seus serviços fazem. Isso inclui a comunicação. Quando seu serviço é aberto pelo Service Fabric, que é a oportunidade do serviço de configurar um ponto de extremidade para solicitações de entrada, usando qualquer protocolo ou pilha de comunicação desejados. O serviço escutará um endereço **IP:port** normal usando qualquer esquema de endereçamento, como um URI. Várias instâncias de serviço ou réplicas podem compartilhar um processo de host, quando então eles precisarão usar portas diferentes ou um mecanismo de compartilhamento de porta, como o driver do kernel http.sys no Windows. Em ambos os casos, cada instância de serviço ou réplica em um processo de host deve ser endereçável exclusivamente.

![pontos de extremidade de serviço][1]

## <a name="service-discovery-and-resolution"></a>Resolução e descoberta de serviço
Em um sistema distribuído, os serviços podem passar de um computador para outro ao com o passar do tempo. Isso pode ocorrer por vários motivos, incluindo balanceamento de recursos, atualizações, failovers ou escala horizontal. Isso significa que os endereços de ponto de extremidade de serviço mudam conforme o serviço passa para nós com endereços IP diferentes, podendo abrir portas diferentes se o serviço usar uma porta dinâmica selecionada.

![Distribuição de serviços][7]

O Service Fabric fornece um serviço de descoberta e resolução chamado Serviço de Nomenclatura. O Serviço de Nomenclatura mantém uma tabela que mapeia instâncias do serviço nomeadas para os endereços de ponto de extremidade que elas escutam. Todas as instâncias de serviço nomeadas do Service Fabric têm nomes exclusivos representados como URIs, como por exemplo, `"fabric:/MyApplication/MyService"`. O nome do serviço não é alterado com o tempo de vida do serviço, apenas os endereços de ponto de extremidade podem ser alterados quando ao mover os serviços. Isso é semelhante aos sites que têm URLs constantes, mas em que o endereço IP pode mudar. E assim como DNS na web, que resolve as URLs do site para endereços IP, o Service Fabric tem um registrador que mapeia nomes de serviço para seu endereço do ponto de extremidade.

![pontos de extremidade de serviço][2]

Resolver e conectar-se aos serviços envolve as seguintes etapas executadas em um loop:

* **Resolver**: obter o ponto de extremidade que um serviço publicou do Serviço de Nomenclatura.
* **Conectar**: conectar-se ao serviço por meio de qualquer protocolo usado no ponto de extremidade em questão.
* **Tentar novamente**: uma tentativa de conexão pode falhar por vários motivos, por exemplo, se o serviço foi movido desde a última vez que o endereço do ponto de extremidade foi resolvido. Nesse caso, as etapas resolver e conectar anteriores precisam ser repetidas e esse ciclo é repetido até que a conexão seja bem-sucedida.

## <a name="connecting-to-other-services"></a>Conectando a outros serviços
Geralmente, os serviços que se conectam entre si em um cluster podem acessar diretamente os pontos de extremidade de outros serviços, pois os nós de um cluster estão na mesma rede local. Para facilitar a conexão entre serviços, o Service Fabric oferece serviços adicionais que usam o Serviço de Nomeação. Um serviço DNS e um serviço de proxy reverso.


### <a name="dns-service"></a>Serviço DNS
Como muitos serviços, especialmente serviços em contêineres, podem ter um nome de URL existente, ter a capacidade de resolvê-los usando o protocolo DNS padrão (em vez do protocolo do Serviço de Nomeação) é muito conveniente, especialmente em cenários que usam o método lift-and-shift para aplicativos. Isso é exatamente o que o serviço DNS faz. Ele permite mapear nomes DNS para um nome de serviço e, portanto, resolver endereços IP do ponto de extremidade. 

Conforme mostrado no diagrama a seguir, o serviço DNS, em execução no cluster do Service Fabric, mapeia nomes DNS para nomes de serviço que são então resolvidos pelo Serviço de Nomeação para retornar os endereços de ponto de extremidade aos quais se conectar. O nome DNS do serviço é fornecido no momento da criação. 

![pontos de extremidade de serviço][9]

Para obter mais detalhes sobre como usar o serviço DNS, consulte o artigo [Serviço DNS no Azure Service Fabric](service-fabric-dnsservice.md).

### <a name="reverse-proxy-service"></a>Serviço de proxy reverso
O proxy reverso lida com os serviços no cluster que expõem pontos de extremidade HTTP, incluindo HTTPS. O proxy reverso simplifica grande parte da chamada a outros serviços e seus métodos tendo um formato de URI específico e manipula as etapas de resolução, conexão e repetição necessárias para que um serviço se comunique com outro usando o Serviço de Nomeação. Em outras palavras, ele oculta o Serviço de Nomeação de você ao chamar outros serviços, fazendo com que isso fique tão simples quanto chamar uma URL.

![pontos de extremidade de serviço][10]

Para obter mais detalhes sobre como usar o serviço de proxy reverso, consulte o artigo [Proxy reverso do Azure Service Fabric](service-fabric-reverseproxy.md).

## <a name="connections-from-external-clients"></a>Conexões de clientes externos
Geralmente, os serviços que se conectam entre si em um cluster podem acessar diretamente os pontos de extremidade de outros serviços, pois os nós de um cluster estão na mesma rede local. Em alguns ambientes, entretanto, um cluster pode estar por trás de um balanceador de carga que encaminha o tráfego de entrada externo por meio de um conjunto limitado de portas. Nesses casos, os serviços ainda poderão se comunicar e resolver endereços usando o Serviço de Nomenclatura, porém, etapas adicionais deverão ser executadas para permitir que clientes externos se conectem aos serviços.

## <a name="service-fabric-in-azure"></a>Service Fabric no Azure
Um cluster do Service Fabric no Azure é colocado atrás de um Balanceador de Carga do Azure. Todo o tráfego externo para o cluster deve passar pelo balanceador de carga. O balanceador de carga encaminhará automaticamente o tráfego de entrada em uma determinada porta para um *nó* aleatório que abriu a mesma porta. O Azure Load Balancer só conhece as portas abertas nos *nós*, ele não conhece portas abertas por *serviços* individuais.

![Topologia do Balanceador de Carga do Azure e do Service Fabric][3]

Por exemplo, para aceitar o tráfego externo na porta **80**, configure os seguintes itens:

1. Escreva um serviço que escute na porta 80. Configure a porta 80 no ServiceManifest.xml do serviço e abra um ouvinte no serviço, por exemplo, um servidor Web auto-hospedado.

    ```xml
    <Resources>
        <Endpoints>
            <Endpoint Name="WebEndpoint" Protocol="http" Port="80" />
        </Endpoints>
    </Resources>
    ```
    ```csharp
        class HttpCommunicationListener : ICommunicationListener
        {
            ...

            public Task<string> OpenAsync(CancellationToken cancellationToken)
            {
                EndpointResourceDescription endpoint =
                    serviceContext.CodePackageActivationContext.GetEndpoint("WebEndpoint");

                string uriPrefix = $"{endpoint.Protocol}://+:{endpoint.Port}/myapp/";

                this.httpListener = new HttpListener();
                this.httpListener.Prefixes.Add(uriPrefix);
                this.httpListener.Start();

                string publishUri = uriPrefix.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);
                return Task.FromResult(publishUri);
            }

            ...
        }

        class WebService : StatelessService
        {
            ...

            protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
            {
                return new[] { new ServiceInstanceListener(context => new HttpCommunicationListener(context))};
            }

            ...
        }
    ```
    ```java
        class HttpCommunicationlistener implements CommunicationListener {
            ...

            @Override
            public CompletableFuture<String> openAsync(CancellationToken arg0) {
                EndpointResourceDescription endpoint =
                    this.serviceContext.getCodePackageActivationContext().getEndpoint("WebEndpoint");
                try {
                    HttpServer server = com.sun.net.httpserver.HttpServer.create(new InetSocketAddress(endpoint.getPort()), 0);
                    server.start();

                    String publishUri = String.format("http://%s:%d/",
                        this.serviceContext.getNodeContext().getIpAddressOrFQDN(), endpoint.getPort());
                    return CompletableFuture.completedFuture(publishUri);
                } catch (IOException e) {
                    throw new RuntimeException(e);
                }
            }

            ...
        }

        class WebService extends StatelessService {
            ...

            @Override
            protected List<ServiceInstanceListener> createServiceInstanceListeners() {
                <ServiceInstanceListener> listeners = new ArrayList<ServiceInstanceListener>();
                listeners.add(new ServiceInstanceListener((context) -> new HttpCommunicationlistener(context)));
                return listeners;       
            }

            ...
        }
    ```
2. Crie um Cluster do Service Fabric no Azure e especifique a porta **80** como uma porta de ponto de extremidade personalizado para o tipo de nó que vai hospedar o serviço. Se você tiver mais de um tipo de nó, configure uma *restrição de posicionamento* no serviço para assegurar que ele só seja executado no tipo de nó que tem a porta de ponto de extremidade personalizado aberta.

    ![Abrir uma porta em um tipo de nó][4]
3. Após o cluster ter sido criado, configure o Balanceador de Carga do Azure no Grupo de Recursos do cluster para encaminhar o tráfego na porta 80. Ao criar um cluster por meio do Portal do Azure, isso é configurado automaticamente para cada porta de ponto de extremidade personalizado que foi configurada.

    ![Encaminhar o tráfego no Balanceador de Carga do Azure][5]
4. O Balanceador de Carga do Azure usa uma investigação para determinar deve enviar ou não o tráfego para um nó específico. A investigação verifica periodicamente um ponto de extremidade em cada nó para determinar se o nó está respondendo ou não. Se a investigação não receber uma resposta após um número de vezes configurado, o balanceador de carga interromperá o envio de tráfego para esse nó. Ao criar um cluster por meio do Portal do Azure, uma investigação é definida automaticamente para cada porta de ponto de extremidade personalizado que foi configurada.

    ![Encaminhar o tráfego no Balanceador de Carga do Azure][8]

É importante lembrar que o Azure Load Balancer e a investigação só conhecem os *nós*, não os *serviços* em execução nos nós. O Balanceador de Carga do Azure sempre enviará tráfego para os nós que responderem à investigação, portanto tome cuidado para garantir que os serviços estão disponíveis em nós que podem responder à investigação.

## <a name="reliable-services-built-in-communication-api-options"></a>Reliable Services: Opções de API de comunicação interna
A estrutura do Reliable Services vem com várias opções de comunicação predefinidas. A decisão sobre qual uma funcionará melhor para você depende da escolha do modelo de programação, da estrutura de comunicação e da linguagem de programação em que os serviços são escritos.

* **Nenhum protocolo específico:**  se você não tiver uma preferência específica de estrutura de comunicação, mas desejar colocar algo em funcionamento rapidamente, a opção ideal para você será a [comunicação remota do serviço](service-fabric-reliable-services-communication-remoting.md), que possibilita chamadas de procedimento remoto fortemente tipadas para os Reliable Services e Reliable Actors. Essa é a maneira mais fácil e rápida de começar a comunicação de serviço. A comunicação remota do serviço lida com a resolução de endereços de serviço, conexão, repetição e tratamento de erro. Isso está disponível para aplicativos Java e C#.
* **HTTP**: para comunicação independente de idioma, o HTTP fornece uma opção padrão do setor com ferramentas e servidores HTTP disponíveis em várias linguagens diferentes, tudo isso com suporte no Service Fabric. Os serviços podem usar qualquer pilha HTTP disponível, incluindo [ASP.NET Web API](service-fabric-reliable-services-communication-webapi.md) para aplicativos C#. Clientes escritos em C# podem aproveitar as classes `ICommunicationClient` e `ServicePartitionClient`, enquanto para Java, use as classes `CommunicationClient` e `FabricServicePartitionClient` [para resolução de serviço, conexões HTTP e loops de repetição](service-fabric-reliable-services-communication.md).
* **WCF**: se você tiver um código existente que use WCF como estrutura de comunicação, poderá usar o `WcfCommunicationListener` para o lado do servidor e as classes `WcfCommunicationClient` e `ServicePartitionClient` para o cliente. No entanto, isso só está disponível para aplicativos C# em clusters baseados no Windows. Para obter mais detalhes, consulte este artigo sobre [implementação baseada no WCF da pilha de comunicação](service-fabric-reliable-services-communication-wcf.md).

## <a name="using-custom-protocols-and-other-communication-frameworks"></a>Usando protocolos personalizados e outras estruturas de comunicação
Os serviços poderão usar qualquer protocolo ou estrutura para comunicação, independentemente de esse ser um protocolo binário personalizado via soquetes TCP ou eventos de streaming por meio de [Hubs de Eventos do Azure](https://azure.microsoft.com/services/event-hubs/) ou [Hub IoT do Azure](https://azure.microsoft.com/services/iot-hub/). O Service Fabric fornece APIs de comunicação aos quais você pode conectar sua pilha de comunicação, enquanto todo o trabalho de descoberta e conexão é omitido. Consulte este artigo sobre o [modelo de comunicação de serviço confiável](service-fabric-reliable-services-communication.md) para obter mais detalhes.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre os conceitos e as APIs disponíveis no [modelo de comunicação dos Reliable Services](service-fabric-reliable-services-communication.md) e começar rapidamente com a [comunicação remota do serviço](service-fabric-reliable-services-communication-remoting.md) ou aprofunde-se para aprender a escrever um ouvinte de comunicação usando [API da Web com auto-hospedagem de OWIN](service-fabric-reliable-services-communication-webapi.md).

[1]: ./media/service-fabric-connect-and-communicate-with-services/serviceendpoints.png
[2]: ./media/service-fabric-connect-and-communicate-with-services/namingservice.png
[3]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancertopology.png
[4]: ./media/service-fabric-connect-and-communicate-with-services/nodeport.png
[5]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancerport.png
[7]: ./media/service-fabric-connect-and-communicate-with-services/distributedservices.png
[8]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancerprobe.png
[9]: ./media/service-fabric-connect-and-communicate-with-services/dns.png
[10]: ./media/service-fabric-reverseproxy/internal-communication.png
