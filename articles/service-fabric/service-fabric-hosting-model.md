---
title: Modelo de hospedagem do Microsoft Azure Service Fabric
description: Descreve a relação entre réplicas (ou instâncias) de um serviço do Service Fabric implantado e o processo de host de serviço.
author: harahma
ms.topic: conceptual
ms.date: 04/15/2017
ms.author: harahma
ms.custom: devx-track-csharp
ms.openlocfilehash: 9932c11332a616928d59c213d4f4806feb81cfe2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98791638"
---
# <a name="azure-service-fabric-hosting-model"></a>Modelo de hospedagem do Microsoft Azure Service Fabric
Este artigo fornece uma visão geral dos modelos de hospedagem de aplicativos fornecidos pelo Microsoft Azure Service Fabric e descreve as diferenças entre os modelos de **Processo Compartilhado** e **Processo Exclusivo**. Ele descreve a aparência de um aplicativo implantado em um nó do Service Fabric e a relação entre réplicas (ou instâncias) do serviço e o processo de host de serviço.

Antes de continuar, certifique-se de compreender os vários conceitos e relacionamentos explicados em [Modelar um aplicativo no Service Fabric][a1]. 

> [!NOTE]
> Neste artigo, a menos que seja explicitamente mencionado como significando algo diferente:
>
> - *Réplica* refere-se tanto a uma réplica de um serviço com estado como a uma instância de um serviço sem estado.
> - *CodePackage* é tratado de forma equivalente a um processo *ServiceHost* que registra um *ServiceType* e hospeda réplicas de serviços do *ServiceType*.
>

Para reconhecer o modelo de hospedagem, vamos percorrer um exemplo. Digamos que temos um *ApplicationType* 'MyAppType', que tem um *ServiceType* 'MyServiceType'. “MyServiceType” é fornecido pelo *ServicePackage* “MyServicePackage”, que tem um *CodePackage* “MyCodePackage”. “MyCodePackage” registra *ServiceType* “MyServiceTypeB” quando ele é executado.

Vamos supor que temos um cluster de 3 nós e que criamos um *aplicativo* **fabric:/App1** do tipo “MyAppType”. Nesse aplicativo **fabric:/App1**, criamos um serviço **fabric:/App1/ServiceA** do tipo 'MyServiceType'. Esse serviço tem duas partições (por exemplo, **P1** e **P2**) e três réplicas por partição. O diagrama a seguir mostra a exibição desse aplicativo ao final de sua implantação em um nó.


![Diagrama que mostra a exibição desse aplicativo à medida que ele acaba sendo implantado em um nó.][node-view-one]


O Service Fabric ativou “MyServicePackage”, que iniciou “MyCodePackage”, que está hospedando réplicas de ambas as partições. Todos os nós do cluster têm a mesma exibição, porque escolhemos o número de réplicas por partição para ser igual ao número de nós no cluster. Vamos criar outro serviço, **fabric:/App1/ServiceB**, no aplicativo **fabric:/App1**. Esse serviço tem uma partição (por exemplo, **P3**) e três réplicas por partição. O seguinte diagrama mostra a nova exibição do nó:


![Diagrama que mostra a nova exibição no nó.][node-view-two]


O Service Fabric colocou a nova réplica da partição **P3** do serviço **fabric:/App1/ServiceB** na ativação existente de “MyServicePackage”. Agora. vamos criar outro aplicativo **fabric:/App2** do tipo 'MyAppType'. No **fabric:/App2**, crie um serviço **fabric:/App2/ServiceA**. Esse serviço tem duas partições (**P4** e **P5**) e três réplicas por partição. O diagrama a seguir mostra a nova exibição do nó:


![Diagrama que mostra a nova exibição de nó.][node-view-three]


O Service Fabric ativa uma nova cópia de “MyServicePackage”, que inicia uma nova cópia de “MyCodePackage”. As réplicas de ambas as partições do serviço **fabric:/App2/ServiceA** (**P4** e **P5**) são colocadas nesta nova cópia 'MyCodePackage'.

## <a name="shared-process-model"></a>Modelo de processo compartilhado
A seção anterior descreve o modelo de hospedagem padrão fornecido pelo Service Fabric, conhecido como modelo de processo compartilhado. Nesse modelo, para um determinado aplicativo, apenas uma cópia de um determinado *ServicePackage* é ativada em um nó (que inicia todos os *CodePackages* contidos nele). Todas as réplicas de todos os serviços de um determinado *ServiceType* são colocadas no *CodePackage* que registra esse *ServiceType*. Em outras palavras, todas as réplicas de todos os serviços em um nó de determinado *ServiceType* compartilham o mesmo processo.

## <a name="exclusive-process-model"></a>Modelo de processo exclusivo
O outro modelo de hospedagem fornecido pelo Service Fabric é o modelo de processo exclusivo. Nesse modelo, em um determinado nó, cada réplica reside em seu próprio processo dedicado. O Service Fabric ativa uma nova cópia do *ServicePackage* (que inicia todos os *CodePackages* contidos nele). As réplicas são colocadas no *CodePackage* que registrou o *ServiceType* do serviço ao qual a réplica pertence. 

Se você estiver usando o Service Fabric versão 5.6 ou posterior, poderá escolher o modelo de Processo Exclusivo no momento em que criar um serviço (usando o [PowerShell][p1], [REST][r1] ou o [FabricClient][c1]). Especifique **ServicePackageActivationMode** como 'ExclusiveProcess'.

```powershell
PS C:\>New-ServiceFabricService -ApplicationName "fabric:/App1" -ServiceName "fabric:/App1/ServiceA" -ServiceTypeName "MyServiceType" -Stateless -PartitionSchemeSingleton -InstanceCount -1 -ServicePackageActivationMode "ExclusiveProcess"
```

```csharp
var serviceDescription = new StatelessServiceDescription
{
    ApplicationName = new Uri("fabric:/App1"),
    ServiceName = new Uri("fabric:/App1/ServiceA"),
    ServiceTypeName = "MyServiceType",
    PartitionSchemeDescription = new SingletonPartitionSchemeDescription(),
    InstanceCount = -1,
    ServicePackageActivationMode = ServicePackageActivationMode.ExclusiveProcess
};

var fabricClient = new FabricClient(clusterEndpoints);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Se você tiver um serviço padrão no manifesto do aplicativo, poderá escolher o modelo de Processo Exclusivo especificando o atributo **ServicePackageActivationMode**:

```xml
<DefaultServices>
  <Service Name="MyService" ServicePackageActivationMode="ExclusiveProcess">
    <StatelessService ServiceTypeName="MyServiceType" InstanceCount="1">
      <SingletonPartition/>
    </StatelessService>
  </Service>
</DefaultServices>
```
Agora vamos criar outro serviço, **fabric:/App1/ServiceC**, in application **fabric:/App1**. Esse serviço tem duas partições (por exemplo, **P6** e **P7**) e três réplicas por partição. Defina **ServicePackageActivationMode** para 'ExclusiveProcess'. O diagrama a seguir mostra uma nova exibição no nó:


![Diagrama da exibição do nó do aplicativo implantado][node-view-four]


Como é possível observar, o Service Fabric ativou duas novas cópias do 'MyServicePackage' (uma para cada réplica da partição **P6** e **P7**). O Service Fabric colocou cada réplica em sua cópia dedicada do *CodePackage*. Ao utilizar o modelo de Processo Exclusivo para um determinado aplicativo, várias cópias de um determinado *ServicePackage* podem estar ativas em um nó. No exemplo anterior, três cópias do 'MyServicePackage' estão ativas para **fabric:/App1**. Cada uma dessas cópias ativas de 'MyServicePackage' tem um **ServicePackageActivationId** associado a ela. Essa ID identifica essa cópia no aplicativo **fabric:/App1**.

Ao utilizar apenas o modelo de Processo Compartilhado para um aplicativo, haverá apenas uma cópia ativa do *ServicePackage* em um nó. O **ServicePackageActivationId** para essa ativação do *ServicePackage* é uma cadeia de caracteres vazia. Esse é o caso, por exemplo, com **fabric:/App2**.

> [!NOTE]
>- O modelo de hospedagem de Processo Compartilhado corresponde a **ServicePackageActivationMode** igual a **SharedProcess**. Esse é o modelo de hospedagem padrão e **ServicePackageActivationMode** não precisa ser especificado no momento da criação do serviço.
>
>- O modelo de hospedagem de Processo Exclusivo corresponde a **ServicePackageActivationMode** igual a **ExclusiveProcess**. Para usar essa configuração, é necessário especificá-la explicitamente no momento da criação do serviço. 
>
>- Para exibir o modelo de hospedagem de um serviço, consulte a [descrição do serviço][p2], e observe o valor de **ServicePackageActivationMode**.
>
>

## <a name="work-with-a-deployed-service-package"></a>Trabalhar com um pacote de serviço implantado
Uma cópia ativa de um *ServicePackage* em um nó é referido como um [pacote de serviço implantado][p3]. Ao utilizar o modelo de Processo Exclusivo para criar serviços, para um determinado aplicativo, pode haver vários pacotes de serviço implantados para o mesmo *ServicePackage*. Se estiver executando operações específicas para um pacote de serviço implantado, deverá fornecer **ServicePackageActivationId** para identificar um pacote de serviço implantado específico. Por exemplo, forneça a ID se você [relatar a integridade de um pacote de serviço implantado][p4] ou [reiniciar o pacote de códigos de um pacote de serviço implantado][p5].

É possível localizar o **ServicePackageActivationId** de um pacote de serviço implantado, consultando a lista de [pacotes de serviço implantados][p3] em um nó. Quando estiver consultando os [tipos de serviço implantado][p6], [réplicas implantadas][p7] e [pacotes de códigos implantados][p8] em um nó, o resultado da consulta também conterá o **ServicePackageActivationId** do pacote de serviço primário implantado.

> [!NOTE]
>- No modelo de hospedagem do Processo Compartilhado, em um determinado nó, para um determinado aplicativo, apenas uma cópia de um *ServicePackage* é ativada. Ele tem um **ServicePackageActivationId** igual a *cadeia de caracteres vazia* e não precisa ser especificado durante a execução de operações relacionadas ao pacote de serviço implantado. 
>
> - No modelo de hospedagem de Processo Exclusivo, em um determinado nó, para um determinado aplicativo, uma ou mais cópias de um *ServicePackage* podem estar ativas. Cada ativação tem um *ServicePackageActivationId* **não vazio**, especificado durante a execução de operações relacionadas ao pacote de serviço implantado. 
>
> - Se **ServicePackageActivationId** for omitido, o padrão será *cadeia de caracteres vazia*. Se um pacote de serviço implantado que foi ativado no modelo de Processo Compartilhado estiver presente, a operação será executada nele. Caso contrário, a operação falhará.
>
> - Não consulte nem por uma vez e armazene em cache o **ServicePackageActivationId**. A ID é gerada dinamicamente e poderá ser alterada por vários motivos. Antes de executar uma operação que precisa de **ServicePackageActivationId**, será necessário primeiro consultar a lista de [pacotes de serviço implantados][p3] em um nó. Em seguida, use o **ServicePackageActivationId** do resultado da consulta para executar a operação original.
>
>

## <a name="guest-executable-and-container-applications"></a>Aplicativos executáveis e de contêiner convidados
O Service Fabric trata o [executável do convidado][a2] e [contêiner][a3] como serviços sem estado, que são independentes. Não há runtime do Service Fabric em *ServiceHost* (um processo ou contêiner). Como esses serviços são independentes, o número de réplicas por *ServiceHost* não é aplicável a esses serviços. A configuração mais comum usada com esses serviços é a partição única, com [InstanceCount][c2] igual a -1 (uma cópia do código de serviço em execução em cada nó do cluster). 

O **ServicePackageActivationMode** padrão para esses serviços é **SharedProcess**, nesse caso Service Fabric ativa apenas uma cópia do *pacote* de serviço em um nó para um determinado aplicativo.  Isso significa que apenas uma cópia do código de serviço executará um nó. Se você quiser que várias cópias de seu código de serviço sejam executadas em um nó, especifique **ServicePackageActivationMode** como **ExclusiveProcess** no momento da criação do serviço. Por exemplo, é possível fazer isso quando criar vários serviços (*Service1* para *ServiceN*) do *ServiceType* (especificado em *ServiceManifest*) ou quando o serviço é multiparticionado. 

## <a name="change-the-hosting-model-of-an-existing-service"></a>Alterar o modelo de hospedagem de um serviço existente
No momento, não é possível alterar o modelo de hospedagem de um serviço existente de Processo Compartilhado para Processo Exclusivo (ou vice-versa).

## <a name="choose-between-the-hosting-models"></a>Escolher entre os modelos de hospedagem
Você deve avaliar qual modelo de hospedagem atende melhor às suas necessidades. O modelo de Processo Compartilhado usa melhor os recursos do sistema operacional porque menos processos são gerados e várias réplicas no mesmo processo podem compartilhar portas. No entanto, se uma das réplicas tiver um erro no qual precisa desativar o host de serviço, ela impactará todas as outras réplicas no mesmo processo.

 O modelo de Processo Exclusivo fornece melhor isolamento, com cada réplica em seu próprio processo. Se uma das réplicas tiver um erro, isso não afetará outras réplicas. Esse modelo é útil para casos em que o compartilhamento de porta não tem suporte pelo protocolo de comunicação. Ela facilita a capacidade de aplicar a governança de recursos no nível da réplica. No entanto, o Processo Exclusivo consome mais recursos do sistema operacional, pois ele gera um processo para cada réplica no nó.

## <a name="exclusive-process-model-and-application-model-considerations"></a>Modelo de Processo Exclusivo e considerações de modelo de aplicativo
Para a maioria dos aplicativos, é possível modelar o aplicativo no Service Fabric mantendo um *ServiceType* por *ServicePackage*. 

Para determinados casos, o Service Fabric também permite mais de um *ServiceType* por *ServicePackage* (e um *CodePackage* pode registrar mais de um *ServiceType*). Veja a seguir alguns dos cenários em que essas configurações podem ser úteis:

- Você deseja otimizar a utilização de recursos, gerando menos processos e com maior densidade de réplica por processo.
- Réplicas de diferentes *tipos de tipo* precisam compartilhar alguns dados comuns que têm um alto custo de inicialização ou de memória.
- Você tem uma oferta de serviço gratuita e deseja limitar a utilização de recursos colocando todas as réplicas do serviço no mesmo processo.

O modelo de hospedagem de Processo Exclusivo não é coerente com um modelo de aplicativo que possui vários *ServiceTypes* por *ServicePackage*. Isso ocorre porque vários *tipos* de recurso por *pacote de pacotes* são projetados para alcançar um compartilhamento de recursos maior entre réplicas e permite maior densidade de réplica por processo. O modelo de Processo Exclusivo é projetado para alcançar resultados diferentes.

Considere o caso de vários *ServiceTypes* por *ServicePackage*, com um *CodePackage* diferente registrando cada *ServiceType*. Vamos supor que tenhamos um *ServicePackage* 'MultiTypeServicePackage', que tem dois *CodePackages*:

- “MyCodePackageA”, que registra *ServiceType* “MyServiceTypeA”.
- ' MyCodePackageB ', que registra o *ServiceType* ' MyServiceTypeB '.

Agora, vamos supor que criamos um aplicativo **fabric:/SpecialApp**. No **fabric:/SpecialApp**, criamos dois serviços a seguir com o modelo de Processo Exclusivo:

- Serviço **fabric:/SpecialApp/ServiceA** do tipo 'MyServiceTypeA', com duas partições (por exemplo, **P1** e **P2**) e três réplicas por partição.
- Serviço **fabric:/SpecialApp/ServiceB** do tipo 'MyServiceTypeB', com duas partições (**P3** e **P4**) e três réplicas por partição.

Em um determinado nó, os dois serviços têm duas réplicas cada. Como usamos o modelo de Processo Exclusivo para criar os serviços, o Service Fabric ativa uma nova cópia de 'MyServicePackage' para cada réplica. Cada ativação de 'MultiTypeServicePackage' inicia uma cópia de 'MyCodePackageA' e 'MyCodePackageB'. No entanto, apenas 'MyCodePackageA' ou 'MyCodePackageB' hospeda a réplica para a qual 'MultiTypeServicePackage' foi ativado. O diagrama a seguir mostra a exibição do nó:


![Diagrama que mostra a exibição de nó.][node-view-five]


Na ativação de 'MultiTypeServicePackage' para a réplica da partição **P1** do serviço **fabric:/SpecialApp/ServiceA**, 'MyCodePackageA' está hospedando a réplica. 'MyCodePackageB' está em execução. Da mesma forma, na ativação de 'MultiTypeServicePackage' para a réplica da partição **P3** do serviço **fabric:/SpecialApp/ServiceB**, 'MyCodePackageB' está hospedando a réplica. 'MyCodePackageA' está em execução. Portanto, quanto maior o número de *CodePackages* (registrando diferentes *ServiceTypes*) por *ServicePackage*, maior será o uso de recursos redundantes. 
 
 No entanto, se criarmos os serviços **fabric:/SpecialApp/ServiceA** e **fabric:/SpecialApp/ServiceB** com o modelo de Processo Compartilhado, o Service Fabric ativará apenas uma cópia de 'MultiTypeServicePackage' para o aplicativo **fabric:/SpecialApp**. 'MyCodePackageA' hospeda todas as réplicas para o serviço **fabric:/SpecialApp/ServiceA**. 'MyCodePackageB' hospeda todas as réplicas para o serviço **fabric:/SpecialApp/ServiceB**. O seguinte diagrama mostra a exibição do nó nesta configuração: 


![Diagrama da exibição do nó do aplicativo implantado][node-view-six]


No exemplo anterior, é possível pensar que, se o 'MyCodePackageA' registrar ambos o 'MyServiceTypeA' e o 'MyServiceTypeB' e não houver 'MyCodePackageB', então, não haverá *CodePackage* redundante em execução. Embora isso esteja correto, esse modelo de aplicativo não está alinhado ao modelo de hospedagem de Processo Exclusivo. Se a meta é colocar cada réplica em seu próprio processo dedicado, não é necessário registrar os *ServiceTypes* do mesmo *CodePackage*. Em vez disso, você simplesmente coloca cada *ServiceType* em seu próprio *ServicePackage*.

### <a name="reliable-services-and-actor-forking-subprocesses"></a>Reliable Services e subprocessos de bifurcação de ator

O Service Fabric não dá suporte a serviços confiáveis e, subsequentemente, a subprocessos confiáveis de bifurcação de atores. Um exemplo do motivo pelo qual não há suporte para ele é o fato de que [CodePackageActivationContext](/dotnet/api/system.fabric.codepackageactivationcontext) não pode ser usado para registrar um subprocesso sem suporte e que tokens de cancelamento são enviados somente para processos registrados, resultando em diversos tipos de problemas, como falhas de atualização, quando os subprocessos não são fechados depois que o processo pai recebe um token de cancelamento.

## <a name="next-steps"></a>Próximas etapas
[Empacotar um aplicativo][a4] e prepará-lo para a implantação.

[Implantar e remover aplicativos][a5]. Esse artigo descreve como usar o PowerShell para gerenciar instâncias do aplicativo.

<!--Image references-->
[node-view-one]: ./media/service-fabric-hosting-model/node-view-one.png
[node-view-two]: ./media/service-fabric-hosting-model/node-view-two.png
[node-view-three]: ./media/service-fabric-hosting-model/node-view-three.png
[node-view-four]: ./media/service-fabric-hosting-model/node-view-four.png
[node-view-five]: ./media/service-fabric-hosting-model/node-view-five.png
[node-view-six]: ./media/service-fabric-hosting-model/node-view-six.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[a1]: service-fabric-application-model.md
[a2]: service-fabric-guest-executables-introduction.md
[a3]: service-fabric-containers-overview.md
[a4]: service-fabric-package-apps.md
[a5]: service-fabric-deploy-remove-applications.md

[r1]: /rest/api/servicefabric/sfclient-api-createservice

[c1]: /dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync
[c2]: /dotnet/api/system.fabric.description.statelessservicedescription.instancecount

[p1]: /powershell/module/servicefabric/new-servicefabricservice
[p2]: /powershell/module/servicefabric/get-servicefabricservicedescription
[p3]: /powershell/module/servicefabric/get-servicefabricdeployedservicepackage
[p4]: /powershell/module/servicefabric/send-servicefabricdeployedservicepackagehealthreport
[p5]: /powershell/module/servicefabric/restart-servicefabricdeployedcodepackage
[p6]: /powershell/module/servicefabric/get-servicefabricdeployedservicetype
[p7]: /powershell/module/servicefabric/get-servicefabricdeployedreplica
[p8]: /powershell/module/servicefabric/get-servicefabricdeployedcodepackage
