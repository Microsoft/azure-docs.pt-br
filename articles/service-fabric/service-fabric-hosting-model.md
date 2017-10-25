---
title: Modelo de hospedagem do Azure Service Fabric | Microsoft Docs
description: "Descreve a relação entre réplicas (ou instâncias) de um serviço do Service Fabric implantado e o processo de host do serviço."
services: service-fabric
documentationcenter: .net
author: harahma
manager: timlt
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/15/2017
ms.author: harahma
ms.openlocfilehash: ca7092a06a9ffce8383ca8bc9f70ce312cdf9de4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="service-fabric-hosting-model"></a>Modelo de hospedagem do Service Fabric
Este artigo fornece uma visão geral dos modelos de hospedagem de aplicativos fornecidos pelo Service Fabric e descreve as diferenças entre os modelos de **Processo Compartilhado** e **Processo Exclusivo**. Ele descreve a aparência de um aplicativo implantado em um nó do Service Fabric e a relação entre réplicas (ou instâncias) do serviço e o processo de host do serviço.

Antes de continuar, familiarize-se com o [Modelo de Aplicativo do Service Fabric][a1] e compreenda vários conceitos e a relação entre eles. 

> [!NOTE]
> Neste artigo, para fins de simplicidade, a menos que mencionado explicitamente:
>
> - Todos os usos da palavra *réplica* referem-se a uma réplica de um serviço com estado ou a uma instância de um serviço sem estado.
> - *CodePackage* é tratado de forma equivalente ao processo *ServiceHost* que registra um *ServiceType* e hospeda réplicas de serviços do *ServiceType*.
>

Para entender o modelo de hospedagem, vamos ver um exemplo. Digamos que temos um *ApplicationType* “MyAppType”, que tem um *ServiceType* “MyServiceType”, que é fornecido por *ServicePackage* “MyServicePackage”, que tem um *CodePackage* “MyCodePackage”, que registra *ServiceType* “MyServiceType” quando é executado.

Vamos supor que temos um cluster de 3 nós e que criamos um *aplicativo* **fabric:/App1** do tipo “MyAppType”. Nesse *aplicativo* **fabric:/App1**, criamos um serviço **fabric:/App1/ServiceA** do tipo “MyServiceType”, que tem 2 partições (digamos **P1** & **P2**) e 3 réplicas por partição. O diagrama a seguir mostra a exibição desse aplicativo ao final de sua implantação em um nó.

<center>
![Exibição do nó do aplicativo implantado][node-view-one]
</center>

O Service Fabric ativou “MyServicePackage”, que iniciou “MyCodePackage”, que está hospedando réplicas de ambas as partições, ou seja, **P1** & **P2**. Observe que todos os nós do cluster terão a mesma exibição, já que escolhemos um número de réplicas por partição igual ao número de nós do cluster. Vamos criar outro serviço **fabric:/App1/ServiceB** no aplicativo **fabric:/App1** que tem 1 partição (digamos **P3**) e 3 réplicas por partição. O seguinte diagrama mostra a nova exibição do nó:

<center>
![Exibição do nó do aplicativo implantado][node-view-two]
</center>

Como você pode ver, o Service Fabric colocou a nova réplica da partição **P3** do serviço **fabric:/App1/ServiceB** na ativação existente de “MyServicePackage”. Agora, vamos criar outro *aplicativo* **fabric:/App2** do tipo “MyAppType” e dentro de **fabric:/App2** criar o serviço **fabric:/App2/ServiceA**, que tem 2 partições (digamos **P4** & **P5**) e 3 réplicas por partição. Os seguintes diagramas mostram a nova exibição do nó:

<center>
![Exibição do nó do aplicativo implantado][node-view-three]
</center>

Desta vez, o Service Fabric ativou uma nova cópia de “MyServicePackage”, que inicia uma nova cópia de “MyCodePackage” e as réplicas de ambas as partições do serviço **fabric:/App2/ServiceA** (ou seja, **P4** & **P5**) são colocadas nessa nova cópia de “MyCodePackage”.

## <a name="shared-process-model"></a>Modelo de processo compartilhado
O que vimos anteriormente é o modelo de hospedagem padrão fornecido pelo Service Fabric, que é conhecido como o modelo de **Processo Compartilhado**. Nesse modelo, em determinado *aplicativo*, apenas uma cópia de determinado *ServicePackage* é ativada em um *Nó* (que inicia todos os *CodePackages* contidos nele) e todas as réplicas de todos os serviços de determinado *ServiceType* são colocadas no *CodePackage* que registra o *ServiceType*. Em outras palavras, todas as réplicas de todos os serviços de determinado *ServiceType* compartilham o mesmo processo.

## <a name="exclusive-process-model"></a>Modelo de processo exclusivo
O outro modelo de hospedagem fornecido pelo Service Fabric é o modelo de **Processo Exclusivo**. Nesse modelo, em determinado *Nó*, para colocar cada réplica, o Service Fabric ativa uma nova cópia do *ServicePackage* (que inicia todos os *CodePackages* contidos nele) e a réplica é colocada no *CodePackage* que registrou o *ServiceType* do serviço ao qual a réplica pertence. Em outras palavras, cada réplica reside em seu próprio processo dedicado. 

Há suporte para esse modelo a partir da versão 5.6 do Service Fabric. O modelo de **Processo Exclusivo** pode ser escolhido no momento da criação do serviço (usando o [PowerShell][p1], a [REST][ r1] ou o [FabricClient][c1]), especificando **ServicePackageActivationMode** como “ExclusiveProcess”.

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

Se você tiver um serviço padrão no manifesto do aplicativo, poderá escolher o modelo de **Processo Exclusivo** especificando o atributo **ServicePackageActivationMode** conforme mostrado abaixo:

```xml
<DefaultServices>
  <Service Name="MyService" ServicePackageActivationMode="ExclusiveProcess">
    <StatelessService ServiceTypeName="MyServiceType" InstanceCount="1">
      <SingletonPartition/>
    </StatelessService>
  </Service>
</DefaultServices>
```
Continuando com nosso exemplo acima, vamos criar outro serviço **fabric:/App1/ServiceC** no aplicativo **fabric:/App1** que tem 2 partições (digamos **P6** & **P7**) e 3 réplicas por partição com **ServicePackageActivationMode** definido como “ExclusiveProcess”. O seguinte diagrama mostra a nova exibição do nó:

<center>
![Exibição do nó do aplicativo implantado][node-view-four]
</center>

Como você pode ver, o Service Fabric ativou duas novas cópias de “MyServicePackage” (uma para cada réplica da partição **P6** & **P7**) e colocou cada réplica em sua cópia dedicada de *CodePackage*. Outro ponto importante a observar aqui é que, quando o modelo de **Processo Exclusivo** é usado, em determinado *aplicativo*, várias cópias de um *ServicePackage* específico podem estar ativas em um *Nó*. No exemplo acima, vemos que três cópias de “MyServicePackage” estão ativas em **fabric:/App1**. Cada uma dessas cópias ativas de “MyServicePackage” tem uma **ServicePackageAtivationId** associada a ela que identifica essa cópia no *aplicativo* **fabric:/App1**.

Quando apenas o modelo de **Processo Compartilhado** é usado para um *aplicativo*, como **fabric:/App2** no exemplo acima, há apenas uma cópia ativa do *ServicePackage* em um *Nó* e uma **ServicePackageAtivationId** dessa ativação do *ServicePackage* é uma “cadeia de caracteres vazia”.

> [!NOTE]
>- O modelo de hospedagem do **Processo Compartilhado** corresponde ao **ServicePackageAtivationMode** igual a **SharedProcess**. Esse é o modelo de hospedagem padrão e **ServicePackageAtivationMode** não precisa ser especificado no momento da criação do serviço.
>
>- O modelo de hospedagem do **Processo Exclusivo** corresponde ao **ServicePackageAtivationMode** igual a **ExclusiveProcess** e precisa ser especificado explicitamente no momento da criação do serviço. 
>
>- O modelo de hospedagem de um serviço pode ser conhecido consultando a [descrição do serviço][p2] e examinando o valor de **ServicePackageAtivationMode**.
>
>

## <a name="working-with-deployed-service-package"></a>Trabalhando com o pacote de serviço implantado
Uma cópia ativa de um *ServicePackage* em um nó é conhecido como um [pacote de serviço implantado][p3]. Conforme mencionado acima, quando o modelo de **Processo Exclusivo** é usado para criar serviços, para determinado *aplicativo*, é possível que vários pacotes de serviço sejam implantados no mesmo *ServicePackage*. Ao executar operações específicas ao pacote de serviço implantado como [relatório de integridade de um pacote de serviço implantado][p4] ou [reinicialização do pacote de códigos de um pacote de serviço implantado][p5], etc., a **ServicePackageActivationId** deve ser fornecida para identificar um pacote de serviço implantado específico.

 A **ServicePackageActivationId** de um pacote de serviço implantado pode ser obtida consultando a lista de [pacotes de serviço implantados][p3] de um nó. Ao consultar os [tipos de serviço implantados][p6], as [réplicas implantadas][p7] e os [pacotes de códigos implantados][p8] em um nó, o resultado da consulta também contém a **ServicePackageActivationId** do pacote de serviço pai implantado.

> [!NOTE]
>- No modelo de hospedagem do **Processo Compartilhado**, em determinado *nó* de determinado *aplicativo*, apenas uma cópia de um *ServicePackage* é ativada. Ele tem **ServicePackageActivationId** igual a *cadeia de caracteres vazia* e não precisa ser especificada durante a execução das operações relacionadas ao pacote de serviço implantado. 
>
> - No modelo de hospedagem do **Processo Exclusivo**, em um determinado *nó* de determinado *aplicativo*, uma ou mais cópias de um *ServicePackage* podem estar ativas. Cada ativação tem uma *ServicePackageActivationId* **não vazia** e precisa ser especificada durante a execução de operações relacionadas ao pacote de serviço implantado. 
>
> - Se **ServicePackageActivationId** for omitida, ele usará “cadeia de caracteres vazia” como padrão. Se um pacote de serviço implantado que foi ativado no modelo de **Processo Compartilhado** estiver presente, a operação será executada nele; caso contrário, a operação falhará.
>
> - Não é recomendável consultar uma vez e armazenar **ServicePackageActivationId** em cache, pois ela é gerada dinamicamente e pode ser alterada por vários motivos. Antes de executar uma operação que precisa da **ServicePackageActivationId**, primeiro você deve consultar a lista de [pacotes de serviço implantados][p3] em um nó e, depois, usar *ServicePackageActivationId** do resultado da consulta para executar a operação original.
>
>

## <a name="guest-executable-and-container-applications"></a>Aplicativos executáveis e de contêiner convidados
O Service Fabric trata os aplicativos [executáveis][a2] e de [contêiner convidados][a3] como serviços sem estado que são independentes; ou seja, não há nenhum tempo de execução do Service Fabric em *ServiceHost* (um processo ou contêiner). Como esses serviços são independentes, o número de réplicas por *ServiceHost* não é aplicável a esses serviços. A configuração mais comum usada com esses serviços é a única partição com [InstanceCount][c2] igual a -1 (ou seja, uma cópia do código do serviço em execução em cada nó do cluster). 

O **ServicePackageActivationMode** padrão para esses serviços é **SharedProcess**, em que o Service Fabric ativa apenas uma cópia de *ServicePackage* em um *Nó* de determinado *aplicativo*, o que significa que apenas uma cópia do código de serviço executará um *Nó*. Se você desejar que várias cópias do código do serviço sejam executadas em um *Nó* ao criar vários serviços (*Service1* para *ServiceN*) de *ServiceType* (especificado em *ServiceManifest*) ou quando o serviço tiver várias partições, deverá especificar **ServicePackageActivationMode** como **ExclusiveProcess** no momento da criação do serviço.

## <a name="changing-hosting-model-of-an-existing-service"></a>Alterando o modelo de hospedagem de um serviço existente
Atualmente, não há suporte para a alteração do modelo de hospedagem de um serviço existente de **Processo Compartilhado** para **Processo Exclusivo** e vice-versa por meio do mecanismo de atualização (ou na especificação de serviço padrão no manifesto do aplicativo). O suporte a esse recurso será fornecido em versões futuras.

## <a name="choosing-between-shared-process-and-exclusive-process-model"></a>Escolhendo entre o modelo de processo compartilhado e processo exclusivo
Esses dois modelos de hospedagem têm seus prós e contras e o usuário precisa avaliar qual deles atende melhor às suas necessidades. O modelo de **Processo Compartilhado** permite uma melhor utilização de recursos do sistema operacional, porque menos processos são gerados, várias réplicas no mesmo processo podem compartilhar portas, etc. No entanto, se ocorrer um erro em uma das réplicas em que ela precisa desativar o host de serviço, isso afetará todas as outras réplicas no mesmo processo.

 O modelo de **Processo Exclusivo** fornece um melhor isolamento com cada réplica em seu próprio processo e uma réplica com comportamento inadequado não afetará outras réplicas. Essa opção é útil para casos em que não há suporte para o compartilhamento de porta no protocolo de comunicação. Ela facilita a capacidade de aplicar a governança de recursos no nível da réplica. Por outro lado, o **Processo Exclusivo** consumirá mais recursos do sistema operacional, pois gera um processo para cada réplica no nó.

## <a name="exclusive-process-model-and-application-model-considerations"></a>Considerações sobre o modelo de processo exclusivo e o modelo de aplicativo
A maneira recomendada de modelar o aplicativo no Service Fabric é manter um *ServiceType* por *ServicePackage* e esse modelo funciona bem na maioria dos aplicativos. 

No entanto, para possibilitar cenários especiais em que um *ServiceType* por *ServicePackage* talvez não seja desejável, funcionalmente, o Service Fabric permite ter mais de um *ServiceType* por *ServicePackage* (e um *CodePackage* pode registrar mais de um *ServiceType*). Estes são alguns dos cenários em que essas configurações podem ser úteis:

- Você deseja otimizar a utilização de recursos do sistema operacional, gerando menos processos e tendo uma densidade de réplica mais alta por processo.
- Réplicas de ServiceTypes diferentes precisam compartilhar alguns dados comuns que têm um alto custo de memória ou de inicialização.
- Você tem uma oferta de serviço gratuito e deseja colocar um limite na utilização de recursos colocando todas as réplicas do serviço no mesmo processo.

O modelo de hospedagem de **Processo Exclusivo** não é coerente com o modelo de aplicativo que tem vários *ServiceTypes* por *ServicePackage*. Isso ocorre porque a opção de vários *ServiceTypes* por *ServicePackage* foi projetada para obter um maior compartilhamento de recursos entre réplicas e permite uma densidade de réplica mais alta por processo. Isso é diferente da finalidade para a qual o modelo de **Processo Exclusivo** foi projetado.

Considere o caso de vários *ServiceTypes* por *ServicePackage* com diferentes *CodePackage* registrando cada *ServiceType*. Digamos que temos um *ServicePackage* “MultiTypeServicePackge”, que tem dois *CodePackages*:

- “MyCodePackageA”, que registra *ServiceType* “MyServiceTypeA”.
- “MyCodePackageB”, que registra *ServiceType* “MyServiceTypeB”.

Agora, digamos que criamos um *aplicativo* **fabric:/SpecialApp** e dentro de **fabric:/SpecialApp** criamos os dois seguintes serviços com o modelo de **Processo Exclusivo**:

- Serviço **fabric:/SpecialApp/ServiceA** do tipo “MyServiceTypeA” com duas partições (digamos **P1** e **P2**) e 3 réplicas por partição.
- Serviço **fabric:/SpecialApp/ServiceB** do tipo “MyServiceTypeB” com duas partições (digamos **P3** e **P4**) e 3 réplicas por partição.

Em um nó específico, os dois serviços terão duas réplicas cada um. Como usamos o modelo de **Processo Exclusivo** para criar os serviços, o Service Fabric ativará uma nova cópia de “MyServicePackge” para cada réplica. Cada ativação de “MultiTypeServicePackge” iniciará uma cópia de “MyCodePackageA” e “MyCodePackageB”. No entanto, somente um “MyCodePackageA” ou “MyCodePackageB” hospedará a réplica para a qual “MultiTypeServicePackge” foi ativado. O seguinte diagrama mostra a exibição do nó:

<center>
![Exibição do nó do aplicativo implantado][node-view-five]
</center>

 Como você pode ver, na ativação de “MultiTypeServicePackge” na réplica da partição **P1** do serviço **fabric:/SpecialApp/ServiceA**, “MyCodePackageA” está hospedando a réplica e “MyCodePackageB” está apenas em execução. Da mesma forma, na ativação de “MultiTypeServicePackge” na réplica da partição **P3** do serviço **fabric:/SpecialApp/ServiceB**, “MyCodePackageB” está hospedando a réplica e “MyCodePackageA” está apenas em execução e assim por diante. Portanto, quanto maior o número de *CodePackages* (registrando diferentes *ServiceTypes*) por *ServicePackage*, maior será o uso de recursos redundantes. 
 
 Por outro lado, se criarmos os serviços **fabric:/SpecialApp/ServiceA** e **fabric:/SpecialApp/ServiceB** com o modelo de **Processo Compartilhado**, o Service Fabric ativará somente uma cópia de “MultiTypeServicePackge” para o *aplicativo* **fabric:/SpecialApp** (como vimos anteriormente). “MyCodePackageA” hospedará todas as réplicas do serviço **fabric:/SpecialApp/ServiceA** (ou de qualquer serviço do tipo “MyServiceTypeA”, para ser mais exato) e “MyCodePackageB” hospedará todas as réplicas do serviço **fabric:/SpecialApp/ServiceB** (ou de qualquer serviço do tipo “MyServiceTypeB”, para ser mais exato). O seguinte diagrama mostra a exibição do nó nesta configuração: 

<center>
![Exibição do nó do aplicativo implantado][node-view-six]
</center>

No exemplo acima, talvez você pense que se “MyCodePackageA” registrar “MyServiceTypeA” e “MyServiceTypeB” e não houver nenhum “MyCodePackageB”, não haverá nenhum *CodePackage* redundante em execução. Isso está correto, mas, como mencionado anteriormente, esse modelo de aplicativo não se alinha com o modelo de hospedagem de **Processo Exclusivo**. Se a meta é colocar cada réplica em seu próprio processo dedicado, registrar os dois *ServiceTypes* do mesmo *CodePackage* não é necessário e colocar cada *ServiceType* em seu próprio *ServicePackage* é a escolha mais natural.

## <a name="next-steps"></a>Próximas etapas
[Empacotar um aplicativo][a4] e prepará-lo para implantação.

[Implantar e remover aplicativos][a5] descreve como usar o PowerShell para gerenciar instâncias do aplicativo.

<!--Image references-->
[node-view-one]: ./media/service-fabric-hosting-model/node-view-one.png
[node-view-two]: ./media/service-fabric-hosting-model/node-view-two.png
[node-view-three]: ./media/service-fabric-hosting-model/node-view-three.png
[node-view-four]: ./media/service-fabric-hosting-model/node-view-four.png
[node-view-five]: ./media/service-fabric-hosting-model/node-view-five.png
[node-view-six]: ./media/service-fabric-hosting-model/node-view-six.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[a1]: service-fabric-application-model.md
[a2]: service-fabric-deploy-existing-app.md
[a3]: service-fabric-containers-overview.md
[a4]: service-fabric-package-apps.md
[a5]: service-fabric-deploy-remove-applications.md

[r1]: https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-createservice

[c1]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync
[c2]: https://docs.microsoft.com/dotnet/api/system.fabric.description.statelessservicedescription.instancecount

[p1]: https://docs.microsoft.com/powershell/servicefabric/vlatest/new-servicefabricservice
[p2]: https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricservicedescription
[p3]: https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricdeployedservicePackage
[p4]: https://docs.microsoft.com/powershell/servicefabric/vlatest/send-servicefabricdeployedservicepackagehealthreport
[p5]: https://docs.microsoft.com/powershell/servicefabric/vlatest/restart-servicefabricdeployedcodepackage
[p6]: https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricdeployedservicetype
[p7]: https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricdeployedreplica
[p8]: https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricdeployedcodepackage