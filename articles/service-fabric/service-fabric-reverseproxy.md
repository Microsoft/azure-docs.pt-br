---
title: Proxy reverso do Azure Service Fabric
description: Usar o proxy reverso do Service Fabric para comunicação com microsserviços de dentro e fora do cluster.
author: BharatNarasimman
ms.topic: conceptual
ms.date: 11/03/2017
ms.author: bharatn
ms.custom: devx-track-csharp
ms.openlocfilehash: 012a49762596adee39988614ed0c1020cd8bc104
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98791097"
---
# <a name="reverse-proxy-in-azure-service-fabric"></a>Proxy reverso no Azure Service Fabric
Proxy reverso incorporado no Azure Service Fabric ajuda microsserviços em execução em um cluster do Service Fabric a descobrir e comunicar-se com outros serviços que têm pontos de extremidade http.

## <a name="microservices-communication-model"></a>Modelo de comunicação de microsserviços
Microsserviços no Service Fabric são executados em um subconjunto de nós no cluster e podem migrar entre os nós por vários motivos. Assim, os pontos de extremidade para microsserviços podem mudar dinamicamente. Para descobrir e se comunicar com outros serviços no cluster, o microsserviço deve percorrer as etapas a seguir:

1. Resolver a localização do serviço por meio do serviço de nomenclatura.
2. Conecte-se ao serviço.
3. Encapsular as etapas anteriores em um loop que implementa a resolução do serviço e tenta novamente as políticas a aplicar em caso de falhas de conexão

Para obter mais informações, consulte [Conectar-se a serviços e se comunicar com eles](service-fabric-connect-and-communicate-with-services.md).

### <a name="communicating-by-using-the-reverse-proxy"></a>Comunicar-se usando o proxy reverso
Proxy reverso é um serviço executado em cada nó e manipula a resolução do ponto de extremidade, a repetição automática e outras falhas de conexão em nome de serviços de cliente. Proxy reverso pode ser configurado para aplicar várias políticas, uma vez que ele lida com solicitações de serviços do cliente. Usar um proxy reverso permite que o serviço de cliente use qualquer biblioteca de comunicação HTTP no lado do cliente e não exige resolução especial e lógica de repetição no serviço. 

Proxy reverso expõe um ou mais pontos de extremidade no nó local para serviços de cliente a serem usados para enviar solicitações a outros serviços.

![Comunicação interna][1]

> [!NOTE]
> **Plataformas com suporte**
>
> Proxy reverso no Service Fabric atualmente dá suporte às seguintes plataformas
> * *Cluster do Windows*: Windows 8 e posteriores ou Windows Server 2012 e posteriores
> * *Cluster do Linux*: proxy reverso não está disponível no momento para clusters do Linux
>

## <a name="reaching-microservices-from-outside-the-cluster"></a>Alcançar microsserviços de fora do cluster
O modelo de comunicação externa padrão para microsserviços é um modelo de aceitação em que cada serviço não pode ser acessado diretamente de clientes externos. O [Azure Load Balancer](../load-balancer/load-balancer-overview.md), que é um limite de rede entre microsserviços e clientes externos, executa a conversão de endereços de rede e encaminha as solicitações externas para pontos de extremidade de IP:porta internos. Para tornar o ponto de extremidade do microsserviço diretamente acessível para clientes externos, você deve primeiro configurar o Load Balancer para encaminhar o tráfego para cada porta usada pelo serviço no cluster. Além disso, a maioria dos microsserviços, especialmente microsserviços com estado, não estão em todos os nós do cluster. Os microsserviços podem mover-se entre os nós no failover. Nesses casos, o Load Balancer não pode determinar efetivamente o local do nó de destino das réplicas para o qual encaminhar tráfego.

### <a name="reaching-microservices-via-the-reverse-proxy-from-outside-the-cluster"></a>Acessar os microsserviços por meio do proxy reverso do Service Fabric de fora do cluster
Em vez de configurar a porta de um serviço individual no Load Balancer, você pode configurar apenas a porta do proxy reverso no Load Balancer. Isso permite que clientes fora do cluster alcancem serviços dentro do cluster por meio do proxy reverso sem configurações adicionais.

![Comunicação externa][0]

> [!WARNING]
> Quando você configura a porta do proxy reverso no Load Balancer, todos os microsserviços no cluster que expõem um ponto de extremidade HTTP se tornam endereçáveis da parte externa do cluster. Isso significa que os microsserviços destinados a serem internos pode ser descobertos por um determinado usuário mal-intencionado. Isso potencialmente apresenta vulnerabilidades sérias que podem ser exploradas, por exemplo:
>
> * Um usuário mal-intencionado pode lançar um ataque de negação de serviço chamando repetidamente o serviço interno que não tem uma superfície de ataque protegida.
> * Um usuário mal-intencionado poderá entregar pacotes mal formados a um serviço interno resultando em comportamento não intencionado.
> * Um serviço destinado a ser interno pode devolver informações particulares ou sensíveis não destinadas a serem expostas a serivoços fora do cluster, assim expondo essas informações sensíveis a um usuário mal-intencionado. 
>
> Certifique-se de entender totalmente e reduza as implicações de segurança potenciais para o cluster e os aplicativos em execução, antes de tornar pública a porta do proxy reverso. 
>


## <a name="uri-format-for-addressing-services-by-using-the-reverse-proxy"></a>Formato de URI para endereçamento de serviços por meio do proxy reverso
O proxy reverso usa um formato de URI (Uniform Resource Identifier) específico para identificar a partição de serviço para a qual a solicitação de entrada deve ser encaminhada:

```
http(s)://<Cluster FQDN | internal IP>:Port/<ServiceInstanceName>/<Suffix path>?PartitionKey=<key>&PartitionKind=<partitionkind>&ListenerName=<listenerName>&TargetReplicaSelector=<targetReplicaSelector>&Timeout=<timeout_in_seconds>
```

* **http (s):** o proxy inverso pode ser configurado para aceitar o tráfego HTTP ou HTTPS. Para o encaminhamento HTTPS, consulte [Conectar-se a um serviço seguro com o proxy reverso](service-fabric-reverseproxy-configure-secure-communication.md) após a configuração do proxy reverso para escutar o HTTPS.
* **FQDN (nome de domínio totalmente qualificado) do cluster | IP interno:** para clientes externos, o proxy reverso pode ser configurado para que seja acessível por meio do domínio do cluster, por exemplo, mycluster.eastus.cloudapp.azure.com. Por padrão, o proxy reverso é executado em todos os nós. Para o tráfego interno, o proxy reverso pode ser alcançado no localhost ou em qualquer IP de nó interno (por exemplo, 10.0.0.1).
* **Porta:** essa é a porta, como 19081, que foi especificada para o proxy reverso.
* **ServiceInstanceName:** esse é o nome totalmente qualificado da instância de serviço implantado que você está tentando alcançar sem o esquema "fabric:/". Por exemplo, para alcançar o serviço *fabric:/myapp/myservice/*, você usaria *myapp/myservice*.

    O nome da instância de serviço diferencia maiúsculas de minúsculas. O uso de maiúsculas e minúsculas diferentes no nome da instância de serviço da URL causa uma falha das solicitações com 404 (Não Encontrado).
* **Caminho de sufixo:** esse é o caminho de URL real, por exemplo, *myapi/values/add/3*, para o serviço ao qual você deseja se conectar.
* **PartitionKey:** para um serviço particionado, esta é a chave de partição computada da partição que você deseja alcançar. Observe que isso *não* é o GUID da ID da partição. Esse parâmetro não é necessário para serviços usando o esquema de partição de singleton.
* **PartitionKind:** o esquema de partição de serviço. Isso pode ser 'Int64Range' ou 'Named'. Esse parâmetro não é necessário para serviços usando o esquema de partição de singleton.
* **ListenerName** Os pontos de extremidade do serviço estão no formato {"Endpoints":{"Listener1":"Endpoint1","Listener2":"Endpoint2" ...}}. Quando o serviço expõe vários pontos de extremidade, isso identifica o ponto de extremidade ao qual solicitação do cliente deve ser encaminhada. Isso poderá ser omitido se o serviço tiver somente um ouvinte.
* **TargetReplicaSelector** Especifica como a réplica de destino ou a instância deve ser selecionada.
  * Quando o serviço de destino tem monitoração de estado, TargetReplicaSelector pode ser um dos seguintes: 'PrimaryReplica', 'RandomSecondaryReplica' ou 'RandomReplica'. Quando esse parâmetro não é especificado, o padrão é 'PrimaryReplica'.
  * Quando o serviço de destino não tem monitoração de estado, o proxy reverso escolhe uma instância aleatória da partição de serviço para encaminhar a solicitação a ela.
* **Tempo limite:**  Isso especifica o tempo limite para a solicitação HTTP criada pelo proxy reverso para o serviço em nome da solicitação do cliente. O valor padrão é 120 segundos. Esse é um parâmetro opcional.

### <a name="example-usage"></a>Exemplo de uso
Por exemplo, vamos pegar o serviço *fabric:/MyApp/MyService* que abre um ouvinte HTTP na URL a seguir:

```
http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/
```

A seguir estão os recursos para o serviço:

* `/index.html`
* `/api/users/<userId>`

Se o serviço usar o esquema de particionamento de singleton, os parâmetros de cadeia de consulta *PartitionKey* e *PartitionKind* não serão necessários e o serviço poderá ser acessado pelo uso do gateway da seguinte maneira:

* Externamente: `http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService`
* Internamente: `http://localhost:19081/MyApp/MyService`

Se o serviço usar o esquema de particionamento Int64 Uniforme, os parâmetros de cadeia de caracteres de consulta *PartitionKey* e *PartitionKind* deverão ser usados para acessar uma partição do serviço:

* Externamente: `http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`
* Internamente: `http://localhost:19081/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`

Para acessar os recursos expostos pelo serviço, basta coloca o caminho do recurso após o nome do serviço na URL:

* Externamente: `http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService/index.html?PartitionKey=3&PartitionKind=Int64Range`
* Internamente: `http://localhost:19081/MyApp/MyService/api/users/6?PartitionKey=3&PartitionKind=Int64Range`

O gateway, em seguida, encaminhará essas solicitações para a URL do serviço:

* `http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/index.html`
* `http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/api/users/6`

## <a name="special-handling-for-port-sharing-services"></a>Tratamento especial para os serviços de compartilhamento de porta
O proxy reverso do Service Fabric tenta resolver um endereço de serviço novamente e repetir a solicitação quando um serviço não pode ser alcançado. Geralmente, quando um serviço não pode ser acessado, a instância de serviço ou a réplica foi movida para um nó diferente como parte de seu ciclo de vida normal. Quando isso acontece, o proxy reverso pode receber um erro de conexão de rede que indica que um ponto de extremidade não está aberto no endereço resolvido originalmente.

No entanto, réplicas ou instâncias de serviço podem compartilhar um processo de host e uma porta quando hospedadas por um servidor Web baseado em http.sys, incluindo:

* [System.Net.HttpListener](/dotnet/api/system.net.httplistener)
* [WebListener de Núcleo ASP.NET](https://docs.asp.net/latest/fundamentals/servers.html#weblistener)
* [Katana](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.OwinSelfHost/)

Nessa situação, é provável que o servidor Web esteja disponível no processo de host e respondendo às solicitações, mas a instância de serviço resolvido ou a réplica não está mais disponível no host. Nesse caso, o gateway receberá uma resposta HTTP 404 do servidor Web. Como resultado, uma resposta de HTTP 404 pode ter dois significados distintos:

- Caso n. 1: o endereço do serviço está correto, mas o recurso solicitado pelo usuário não existe.
- Caso n. 2: o endereço do serviço está incorreto e o recurso solicitado pelo usuário talvez exista em um nó diferente.

No primeiro caso, ele é um HTTP 404 normal, que é considerado um erro de usuário. No entanto, no segundo caso, o usuário solicitou um recurso que existe. O proxy reverso não pôde localizá-lo porque o serviço em si foi movido. O proxy reverso precisa resolver o endereço novamente e repetir a solicitação.

Portanto, o proxy reverso precisa de uma maneira de distinguir entre esses dois casos. Para fazer essa distinção, uma dica do servidor é necessária.

* Por padrão, o proxy reverso pressupõe caso nº 2 e tenta resolver mais uma vez e emitir novamente a solicitação.
* Para indicar o caso nº 1 para o proxy reverso, o serviço deve retornar o seguinte cabeçalho de resposta HTTP:

  `X-ServiceFabric : ResourceNotFound`

Esse cabeçalho de resposta HTTP indica uma situação de HTTP 404 normal em que o recurso solicitado não existe e o proxy reverso não tentará resolver novamente o endereço do serviço.

## <a name="special-handling-for-services-running-in-containers"></a>Tratamento especial para serviços em execução em contêineres

Para serviços em execução dentro de contêineres, você pode usar a variável de ambiente `Fabric_NodeIPOrFQDN` para construir a [URL de proxy reverso](#uri-format-for-addressing-services-by-using-the-reverse-proxy) como no código a seguir:

```csharp
    var fqdn = Environment.GetEnvironmentVariable("Fabric_NodeIPOrFQDN");
    var serviceUrl = $"http://{fqdn}:19081/DockerSFApp/UserApiContainer";
```
Para o cluster local, `Fabric_NodeIPOrFQDN` é definido como "localhost" por padrão. Inicie o cluster local com o `-UseMachineName` parâmetro para certificar-se que os contêineres podem acessar o proxy reverso em execução no nó. Para obter mais informações, consulte [Configurar seu ambiente de desenvolvedor para depurar contêineres](service-fabric-how-to-debug-windows-containers.md#configure-your-developer-environment-to-debug-containers).

Os serviços do Service Fabric executados nos contêineres do Docker Compose exigem uma configuração especial http: ou https: docker-compose.yml *Portas seção*. Para obter mais informações, consulte [Docker Compose suporte à implantação no Azure Service Fabric](service-fabric-docker-compose.md).

## <a name="next-steps"></a>Próximas etapas
* [Definir e configurar proxy reverso em um cluster](service-fabric-reverseproxy-setup.md).
* [Definir encaminhamento para o serviço HTTP seguro com um proxy reverso](service-fabric-reverseproxy-configure-secure-communication.md)
* [Diagnosticar eventos do proxy reverso](service-fabric-reverse-proxy-diagnostics.md)
* Confira um exemplo de comunicação HTTP entre serviços em um [projeto de exemplo no GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started).
* [Comunicação remota de serviço com os Reliable Services](service-fabric-reliable-services-communication-remoting.md)
* [API Web que usa o OWIN nos Reliable Services](./service-fabric-reliable-services-communication-aspnetcore.md)
* [Comunicação WCF usando os Reliable Services](service-fabric-reliable-services-communication-wcf.md)

[0]: ./media/service-fabric-reverseproxy/external-communication.png
[1]: ./media/service-fabric-reverseproxy/internal-communication.png
