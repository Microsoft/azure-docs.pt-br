---
title: "Gerenciador de Recursos de Cluster do Service Fabric – Grupos de Aplicativos | Microsoft Docs"
description: "Visão geral da funcionalidade de Grupo de Aplicativos no Gerenciador de Recursos de Cluster do Service Fabric"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 4cae2370-77b3-49ce-bf40-030400c4260d
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 3212631ede7125bd849c2d9ba86ba2a0747d69ca
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-application-groups"></a>Introdução aos grupos de aplicativos
O Cluster Resource Manager do Service Fabric geralmente gerencia os recursos de cluster distribuindo a carga (representada por meio de [Métricas](service-fabric-cluster-resource-manager-metrics.md)) uniformemente em todo o cluster. O Service Fabric gerencia a capacidade dos nós no cluster e o cluster como um todo por meio da [capacidade](service-fabric-cluster-resource-manager-cluster-description.md). As métricas e a capacidade funcionam muito bem para muitas cargas de trabalho, mas padrões que fazem uso intenso de diferentes Instâncias de Aplicativos do Service Fabric às vezes trazem requisitos adicionais. Por exemplo, você pode querer:

- Reservar alguma capacidade em nós do cluster para os serviços em algumas instâncias nomeadas de aplicativo
- Limitar o número total de nós em que os serviços em uma instância nomeada de aplicativo são executados (em vez de propagá-los por todo o cluster)
- Definir as capacidades na instância nomeada do aplicativo em si para limitar o número de serviços ou o consumo de recursos total dos serviços dentro dele

Para atender a esses requisitos, o Service Fabric Cluster Resource Manager dá suporte a um recurso chamado Grupos de Aplicativos.

## <a name="limiting-the-maximum-number-of-nodes"></a>Limitando o número máximo de nós
O caso de uso mais simples para capacidade do Aplicativo é quando uma instancia de aplicativo precisa ser limitada a um determinado número máximo de nós. Isso consolida todos os serviços na instância de aplicativo para um determinado número de máquinas. A consolidação é útil quando você está tentando prever ou limitar o uso de recursos físicos, os serviços na instância nomeada do aplicativo. 

A imagem a seguir mostra uma instância de aplicativo com e sem um número máximo de nós definido:

<center>
![Instância do aplicativo definindo o número máximo de nós][Image1]
</center>

No exemplo à esquerda, o aplicativo não tem um número máximo de nós definidos, e ele tem três serviços. O Cluster Resource Manager distribuiu todas as réplicas em seis nós disponíveis para obter o melhor equilíbrio no cluster (o comportamento padrão). No exemplo à direita, vemos o mesmo aplicativo limitado a três nós.

O parâmetro que controla esse comportamento é chamado de MaximumNodes. Esse parâmetro pode ser definido durante a criação do aplicativo ou atualizado para uma instância do aplicativo que já estava em execução.

Powershell

``` posh
New-ServiceFabricApplication -ApplicationName fabric:/AppName -ApplicationTypeName AppType1 -ApplicationTypeVersion 1.0.0.0 -MaximumNodes 3
Update-ServiceFabricApplication –Name fabric:/AppName –MaximumNodes 5
```

C#

``` csharp
ApplicationDescription ad = new ApplicationDescription();
ad.ApplicationName = new Uri("fabric:/AppName");
ad.ApplicationTypeName = "AppType1";
ad.ApplicationTypeVersion = "1.0.0.0";
ad.MaximumNodes = 3;
await fc.ApplicationManager.CreateApplicationAsync(ad);

ApplicationUpdateDescription adUpdate = new ApplicationUpdateDescription(new Uri("fabric:/AppName"));
adUpdate.MaximumNodes = 5;
await fc.ApplicationManager.UpdateApplicationAsync(adUpdate);

```

Dentro do conjunto de nós, o Gerenciador de Recursos de Cluster não garante que objetos de serviço serão colocados juntos ou quais nós são usados.

## <a name="application-metrics-load-and-capacity"></a>Métrica, carga e capacidade do aplicativo
Os grupos de aplicativos também permitem definir métricas associadas a uma determinada instância nomeado do aplicativo e a capacidade da instância do aplicativo para essas métricas. As métricas de aplicativo permitem controlar, reservar e limitar o consumo de recursos dos serviços dentro dessa instância do aplicativo.

Para cada métrica do aplicativo, há dois valores que podem ser definidos:

- **Capacidade Total do Aplicativo** – essa configuração representa a capacidade total do aplicativo para uma determinada métrica. O Cluster Resource Manager não permite a criação de quaisquer novos serviços dentro dessa instância do aplicativo que fariam com que esse valor fosse excedido. Por exemplo, digamos que a instância do aplicativo tinha a capacidade de 10 e já tinha a carga de cinco. A criação de um serviço com uma carga padrão total de 10 não deve ser permitida.
- **Capacidade Máxima do Nó** – essa configuração especifica a carga total máxima para o aplicativo em um único nó. Se a carga ultrapassar essa capacidade, o Gerenciador de Recursos de Cluster move réplicas para outros nós de forma que a carga diminua.


Powershell:

``` posh
New-ServiceFabricApplication -ApplicationName fabric:/AppName -ApplicationTypeName AppType1 -ApplicationTypeVersion 1.0.0.0 -Metrics @("MetricName:Metric1,MaximumNodeCapacity:100,MaximumApplicationCapacity:1000")
```

C#:

``` csharp
ApplicationDescription ad = new ApplicationDescription();
ad.ApplicationName = new Uri("fabric:/AppName");
ad.ApplicationTypeName = "AppType1";
ad.ApplicationTypeVersion = "1.0.0.0";

var appMetric = new ApplicationMetricDescription();
appMetric.Name = "Metric1";
appMetric.TotalApplicationCapacity = 1000;
appMetric.MaximumNodeCapacity = 100;
ad.Metrics.Add(appMetric);
await fc.ApplicationManager.CreateApplicationAsync(ad);
```

## <a name="reserving-capacity"></a>Reserva de capacidade
Outro uso comum de grupos de aplicativos é para garantir que os recursos dentro do cluster sejam reservados para uma determinada instância do aplicativo. O espaço é sempre reservado quando a instância do aplicativo é criada.

A reserva do espaço no cluster para o aplicativo acontece imediatamente, mesmo quando:
- a instância do aplicativo é criada, mas ainda não tem todos os serviços dentro dela
- o número de serviços na instância do aplicativo é alterado sempre 
- os serviços existem, mas não estão consumindo os recursos 

Reservar recursos para uma instância de aplicativo requer especificar dois parâmetros adicionais: *MinimumNodes* e *NodeReservationCapacity*

- **MinimumNodes** - define o número mínimo de nós que deve ser executados na instância do aplicativo.  
- **NodeReservationCapacity** - essa configuração é por métrica para o aplicativo. O valor é a quantidade de métrica reservada para o aplicativo em qualquer nó onde os serviços do aplicativo são executados.

A combinação de **MinimumNodes** e **NodeReservationCapacity** garante uma reserva de carga mínima para o aplicativo no cluster. Se houver menos capacidade restante no cluster do que a reserva total necessária, a criação do aplicativo falhará. 

Vejamos um exemplo de reserva de capacidade:

<center>
Instâncias do aplicativo definindo a capacidade reservada![][Image2]
</center>

No exemplo à esquerda, os aplicativos não têm nenhuma Capacidade de Aplicativo definida. O Cluster Resource Manager equilibra tudo de acordo com as regras normais.

No exemplo à direita, digamos que o Application1 foi criado com as seguintes configurações:

- MinimumNodes definido como dois
- Uma métrica de aplicativo definida com
  - NodeReservationCapacity de 20

Powershell

 ``` posh
 New-ServiceFabricApplication -ApplicationName fabric:/AppName -ApplicationTypeName AppType1 -ApplicationTypeVersion 1.0.0.0 -MinimumNodes 2 -Metrics @("MetricName:Metric1,NodeReservationCapacity:20")
 ```

C#

 ``` csharp
ApplicationDescription ad = new ApplicationDescription();
ad.ApplicationName = new Uri("fabric:/AppName");
ad.ApplicationTypeName = "AppType1";
ad.ApplicationTypeVersion = "1.0.0.0";
ad.MinimumNodes = 2;

var appMetric = new ApplicationMetricDescription();
appMetric.Name = "Metric1";
appMetric.NodeReservationCapacity = 20;

ad.Metrics.Add(appMetric);

await fc.ApplicationManager.CreateApplicationAsync(ad);
```

O Service Fabric reserva a capacidade em dois nós para Application1 e não permite que os serviços de Application2 consumam essa capacidade, mesmo se não houver nenhuma carga sendo consumida pelos serviços em Application1 no momento. Essa capacidade reservada do aplicativo é considerada consumida e conta em relação à capacidade restante naquele nó e dentro do cluster.  A reserva é deduzida da capacidade restante do cluster imediatamente, mas o consumo reservado é deduzido da capacidade de um nó específico somente quando pelo menos um objeto de serviço é colocado nele. Essa reserva posterior permite flexibilidade e melhor utilização de recursos já que os recursos são reservados nos nós apenas quando necessário.

## <a name="obtaining-the-application-load-information"></a>Obtendo as informações de carga do aplicativo
Para cada aplicativo que tem capacidade de aplicativos definida por uma ou mais métricas que você pode obter as informações sobre a carga de agregada relatada por réplicas de seus serviços.

Powershell:

``` posh
Get-ServiceFabricApplicationLoad –ApplicationName fabric:/MyApplication1
```

C#

``` csharp
var v = await fc.QueryManager.GetApplicationLoadInformationAsync("fabric:/MyApplication1");
var metrics = v.ApplicationLoadMetricInformation;
foreach (ApplicationLoadMetricInformation metric in metrics)
{
    Console.WriteLine(metric.ApplicationCapacity);  //total capacity for this metric in this application instance
    Console.WriteLine(metric.ReservationCapacity);  //reserved capacity for this metric in this application instance
    Console.WriteLine(metric.ApplicationLoad);  //current load for this metric in this application instance
}
```

A consulta ApplicationLoad retorna as informações básicas sobre a Capacidade do Aplicativo que foi especificada para o aplicativo. Essas informações incluem as informações de Nós Mínimos e Nós Máximos e o número que o aplicativo está ocupando atualmente. Elas também incluem informações sobre cada métrica de carga do aplicativo, incluindo:

* Nome da Métrica: o nome da métrica.
* Capacidade de Reserva: a Capacidade do Cluster reservada no cluster para esse Aplicativo.
* Carga do Aplicativo: a Carga Total de réplicas filho deste Aplicativo.
* Capacidade do Aplicativo: valor máximo permitido de Carga do Aplicativo.

## <a name="removing-application-capacity"></a>Removendo a capacidade do aplicativo
Depois que os parâmetros de Capacidade do Aplicativo estão definidos para um aplicativo, eles podem ser removidos usando cmdlets de Atualizar APIs de Aplicativo ou PowerShell. Por exemplo:

``` posh
Update-ServiceFabricApplication –Name fabric:/MyApplication1 –RemoveApplicationCapacity

```

Esse comando remove todos os parâmetros de gerenciamento de capacidade do Aplicativo do aplicativo. Isso inclui MinimumNodes, MaximumNodes e métricas do aplicativo, se houver. O efeito do comando é imediato. Após a conclusão do comando, o Cluster Resource Manager usa o comportamento padrão para o gerenciamento de aplicativos. Os parâmetros de capacidade do aplicativo podem ser especificados novamente por meio de `Update-ServiceFabricApplication`/`System.Fabric.FabricClient.ApplicationManagementClient.UpdateApplicationAsync()`.

### <a name="restrictions-on-application-capacity"></a>Restrições à capacidade do aplicativo
Há várias restrições aos parâmetros de Capacidade de Aplicativo que devem ser respeitadas. Se houver erros de validação, nenhuma alteração será feita.

- Todos os parâmetros de inteiro devem ser números não negativos.
- MinimumNodes nunca deve ser maior que MaximumNodes.
- Se as capacidades de uma métrica de carga forem definidas, elas deverão seguir estas regras:
  - A Capacidade de Reserva do Nó não deve ser maior que a Capacidade Máxima do Nó. Por exemplo, você não pode limitar a capacidade para a "CPU" métrica no nó a duas unidades e tentar reservar três unidades em cada nó.
  - Se MaximumNodes for especificado, o produto de MaximumNodes e a Capacidade Máxima do Nó não deverão ser maiores que a Capacidade Total do Aplicativo. Por exemplo, digamos que a Capacidade Máxima do Nó da métrica de carga "CPU" seja definida como oito. Digamos também que você defina os Nós Máximos como 10. Nesse caso, a Capacidade Total do Aplicativo deve ser maior do que 80 para esta métrica de carga.

As restrições são aplicadas durante a criação e a atualização de aplicativos.

## <a name="how-not-to-use-application-capacity"></a>Como não usar a Capacidade do Aplicativo
- Não tente usar os recursos de Grupo de Aplicativos para restringir o aplicativo a um subconjunto de nós _específico_. Em outras palavras, você pode especificar que o aplicativo seja executado em no máximo cinco nós, mas não quais cinco nós específicos no cluster. A restrição de um aplicativo a nós específicos pode ser obtida usando restrições de posicionamento para os serviços.
- Não tente usar a Capacidade do Aplicativo para garantir que dois serviços do mesmo aplicativo sejam colocados nos mesmos nós. Em vez disso, use [afinidade](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md) ou [restrições de posicionamento](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints).

## <a name="next-steps"></a>Próximas etapas
- Para saber mais sobre como configurar os serviços, [Saiba mais sobre como configurar serviços](service-fabric-cluster-resource-manager-configure-services.md)
- Para descobrir como o Gerenciador de Recursos de Cluster gerencia e balanceia carga no cluster, confira o artigo sobre [como balancear carga](service-fabric-cluster-resource-manager-balancing.md)
- Comece do princípio e [veja uma introdução ao Gerenciador de Recursos de Cluster do Service Fabric](service-fabric-cluster-resource-manager-introduction.md)
- Para obter mais informações sobre como as métricas funcionam em geral, leia sobre [Métricas de Carga do Service Fabric](service-fabric-cluster-resource-manager-metrics.md)
- O Cluster Resource Manager tem muitas opções para descrever o cluster. Para saber mais sobre elas, confira este artigo sobre a [descrição de um cluster do Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)

[Image1]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-max-nodes.png
[Image2]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-reserved-capacity.png
