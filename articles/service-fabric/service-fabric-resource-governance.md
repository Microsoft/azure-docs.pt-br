---
title: Governança de recursos do Azure Service Fabric para contêineres e serviços | Microsoft Docs
description: O Azure Service Fabric permite especificar os limites de recurso para os serviços executados dentro ou fora de contêineres.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: aljo, subramar
ms.openlocfilehash: e011554e61411fddca034f024c30c2270593e07b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60772528"
---
# <a name="resource-governance"></a>Governança de recursos

Ao executar vários serviços no mesmo nó ou cluster, é possível que um serviço consuma mais recursos, privando outros serviços no processo. Esse problema é conhecido como o problema do “vizinho barulhento”. O Service Fabric do Azure permite ao desenvolvedor especificar reservas e limites por serviço para assegurar recursos e limitar o uso de recursos.

> Antes de continuar lendo este artigo, recomendamos que você se familiarize com o [Modelo de aplicativo do Service Fabric](service-fabric-application-model.md) e o [Modelo de hospedagem do Service Fabric](service-fabric-hosting-model.md).
>

## <a name="resource-governance-metrics"></a>Métricas de governança de recursos

Há suporte para a governança de recursos no Service Fabric de acordo com o [pacote de serviço](service-fabric-application-model.md). Os recursos que são atribuídos ao pacote de serviço podem ser subdivididos entre pacotes de códigos. Os limites de recurso especificados também indicam a reserva dos recursos. O Service Fabric dá suporte à especificação de CPU e memória por pacote de serviço, com duas [métricas](service-fabric-cluster-resource-manager-metrics.md) internas:

* *CPU* (nome da métrica `servicefabric:/_CpuCores`): Um núcleo lógico que está disponível no computador host. Todos os núcleos em todos os nós têm o mesmo peso.

* *Memória* (nome da métrica `servicefabric:/_MemoryInMB`): A memória é expressa em megabytes e é mapeada para a memória física disponível no computador.

Para essas duas métricas, o [Gerenciador de Recursos de Cluster](service-fabric-cluster-resource-manager-cluster-description.md) controla a capacidade total do cluster, a carga em cada nó do cluster e os recursos restantes no cluster. Essas duas métricas são equivalentes a qualquer outro usuário ou métrica personalizada. Todos os recursos existentes podem ser usados com elas:

* O cluster pode ser [equilibrado](service-fabric-cluster-resource-manager-balancing.md) de acordo com essas duas métricas (comportamento padrão).
* O cluster pode ser [desfragmentado](service-fabric-cluster-resource-manager-defragmentation-metrics.md) de acordo com essas duas métricas.
* Ao [descrever um cluster](service-fabric-cluster-resource-manager-cluster-description.md), a capacidade armazenada em buffer pode ser definida para essas duas métricas.

Não há suporte para [relatórios de cargas dinâmicas](service-fabric-cluster-resource-manager-metrics.md) nessas métricas e as cargas dessas métricas são definidas no momento da criação.

## <a name="resource-governance-mechanism"></a>Mecanismo de governança de recursos

Atualmente, o tempo de execução do Service Fabric não fornece uma reserva de recursos. Quando um processo ou um contêiner é aberto, o tempo de execução define os limites de recursos para as cargas que foram definidas no momento da criação. Além disso, o tempo de execução rejeita a abertura de novos pacotes de serviço disponíveis quando os recursos são excedidos. Para entender melhor como o processo funciona, vamos usar um exemplo de um nó com dois núcleos de CPU (o mecanismo de governança de memória é equivalente):

1. Primeiro, um contêiner é colocado no nó, solicitando um núcleo de CPU. O tempo de execução abre o contêiner e define o limite de CPU para um núcleo. O contêiner não poderá usar mais de um núcleo.

2. Em seguida, uma réplica do serviço é colocada no nó e o pacote de serviço correspondente especifica um limite de um núcleo de CPU. O tempo de execução abre o pacote de códigos e define o limite de CPU para um núcleo.

Neste ponto, a soma dos limites é igual à capacidade do nó. Um processo e um contêiner estão em execução com um núcleo cada e não interferem um no outro. O Service Fabric não coloca mais nenhum contêiner ou réplica quando eles estão especificando o limite de CPU.

No entanto, há duas situações em que outros processos podem brigar pela CPU. Nessas situações, um processo e um contêiner do nosso exemplo poderá ter o problema do vizinho barulhento:

* *Misturar serviços controlados e não controlados e contêineres*: Se um usuário criar um serviço sem nenhuma governança de recursos especificada, o tempo de execução considerará que ele não está consumindo nenhum recurso e poderá colocá-lo no nó de nosso exemplo. Nesse caso, esse novo processo efetivamente consume alguns recursos da CPU à custa dos serviços que já estão sendo executados no nó. Há duas soluções para esse problema. Não misturar serviços controlados e não controlado no mesmo cluster ou usar [restrições de posicionamento](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) para que esses dois tipos de serviços não terminem no mesmo conjunto de nós.

* *Quando outro processo for iniciado no nó, fora do Service Fabric (por exemplo, um serviço de sistema operacional)*: Nessa situação, o processo fora do Service Fabric também competirá pela CPU com os serviços existentes. A solução para esse problema é configurar as capacidades de nó corretamente para a conta para a sobrecarga do sistema operacional, conforme mostrado na próxima seção.

## <a name="cluster-setup-for-enabling-resource-governance"></a>Configuração de cluster para habilitar a governança de recursos

Quando o nó for iniciado e ingressado no cluster, o Service Fabric detectará a quantidade de memória e o número de núcleos disponíveis e então definirá as capacidades de nó para esses dois recursos.

Para deixar algum espaço do buffer para o sistema operacional e para outros processos que possam estar em execução no nó, o Service Fabric usará somente 80% dos recursos disponíveis no nó. Esse percentual é configurável e pode ser alterado no manifesto do cluster.

Este é um exemplo de como instruir o Service Fabric a usar 50% da CPU disponível e 70% da memória disponível:

```xml
<Section Name="PlacementAndLoadBalancing">
    <!-- 0.0 means 0%, and 1.0 means 100%-->
    <Parameter Name="CpuPercentageNodeCapacity" Value="0.5" />
    <Parameter Name="MemoryPercentageNodeCapacity" Value="0.7" />
</Section>
```

Se você precisar de uma configuração manual completa das capacidades de nó, poderá usar o mecanismo regular para descrever os nós no cluster. Este é um exemplo de como configurar o nó com dois núcleos e 2 GB de memória:

```xml
    <NodeType Name="MyNodeType">
      <Capacities>
        <Capacity Name="servicefabric:/_CpuCores" Value="4"/>
        <Capacity Name="servicefabric:/_MemoryInMB" Value="2048"/>
      </Capacities>
    </NodeType>
```

Quando a detecção automática de recursos disponíveis está habilitada e as capacidades de nó são definidas manualmente no manifesto do cluster, o Service Fabric verifica se o nó tem recursos suficientes para dar suporte à capacidade definida pelo usuário:

* Se as capacidades de nó definidas no manifesto forem menores ou iguais aos recursos disponíveis no nó, o Service Fabric usará as capacidades especificadas no manifesto.

* Se as capacidades do nó definidas no manifesto forem maiores que os recursos disponíveis, o Service Fabric usará os recursos disponíveis como as capacidades do nó.

A detecção automática de recursos disponíveis poderá ser desativada se não for necessária. Para desativá-la, altere a seguinte configuração:

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="AutoDetectAvailableResources" Value="false" />
</Section>
```

Para obter o desempenho ideal, a seguinte configuração também deve ser ativada no manifesto do cluster:

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="PreventTransientOvercommit" Value="true" />
    <Parameter Name="AllowConstraintCheckFixesDuringApplicationUpgrade" Value="true" />
</Section>
```

## <a name="specify-resource-governance"></a>Especificar a governança de recurso

Os limites da governança de recursos são especificados no manifesto do aplicativo (seção ServiceManifestImport), conforme mostrado no seguinte exemplo:

```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='https://www.w3.org/2001/XMLSchema-instance'>

  <!--
  ServicePackageA has the number of CPU cores defined, but doesn't have the MemoryInMB defined.
  In this case, Service Fabric sums the limits on code packages and uses the sum as 
  the overall ServicePackage limit.
  -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCores="1"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="512" MemoryInMB="1000" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="256" MemoryInMB="1000" />
    </Policies>
  </ServiceManifestImport>
```

Neste exemplo, o pacote de serviço chamado **ServicePackageA** obtém um núcleo nos nós em que ele é colocado. Esse pacote de serviço contém dois pacotes de códigos (**CodeA1** e **CodeA2**) e ambos especificam o parâmetro `CpuShares`. A proporção de CpuShares 512:256 divide o núcleo entre os dois pacotes de códigos.

Portanto, neste exemplo, CodeA1 obtém dois terços de um núcleo e CodeA2 obtém um terço de um núcleo (e uma reserva de garantia flexível dele). Se CpuShares não forem especificados para pacotes de códigos, o Service Fabric dividirá os núcleos igualmente entre eles.

Os limites de memória são absolutos e, portanto, os dois pacotes de códigos são limitados a 1.024 MB de memória (e a uma reserva de garantia reversível da mesma). Os pacotes de códigos (contêineres ou processos) não podem alocar mais memória do que esse limite e tentar fazer isso resultará em uma exceção de memória insuficiente. Para que a imposição do limite de recursos funcione, todos os pacotes de códigos em um pacote de serviço devem ter limites de memória especificados.

### <a name="using-application-parameters"></a>Usando parâmetros de aplicativo

Ao especificar a governança dos recursos, é possível usar os [parâmetros do aplicativo](service-fabric-manage-multiple-environment-app-configuration.md) para gerenciar várias configurações de aplicativo. O exemplo a seguir mostra o uso de parâmetros do aplicativo:

```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='https://www.w3.org/2001/XMLSchema-instance'>

  <Parameters>
    <Parameter Name="CpuCores" DefaultValue="4" />
    <Parameter Name="CpuSharesA" DefaultValue="512" />
    <Parameter Name="CpuSharesB" DefaultValue="512" />
    <Parameter Name="MemoryA" DefaultValue="2048" />
    <Parameter Name="MemoryB" DefaultValue="2048" />
  </Parameters>

  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCores="[CpuCores]"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="[CpuSharesA]" MemoryInMB="[MemoryA]" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="[CpuSharesB]" MemoryInMB="[MemoryB]" />
    </Policies>
  </ServiceManifestImport>
```

Neste exemplo, os valores de parâmetro padrão são definidos para o ambiente de produção, onde cada pacote de serviço obteria 4 núcleos e 2 GB de memória. É possível alterar os valores padrão com arquivos de parâmetro do aplicativo. Neste exemplo, um arquivo de parâmetro pode ser usado para testar o aplicativo localmente, onde receberia menos recursos do que em produção:

```xml
<!-- ApplicationParameters\Local.xml -->

<Application Name="fabric:/TestApplication1" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="CpuCores" DefaultValue="2" />
    <Parameter Name="CpuSharesA" DefaultValue="512" />
    <Parameter Name="CpuSharesB" DefaultValue="512" />
    <Parameter Name="MemoryA" DefaultValue="1024" />
    <Parameter Name="MemoryB" DefaultValue="1024" />
  </Parameters>
</Application>
```

> [!IMPORTANT]
> Especificar a governança de recursos com os parâmetros do aplicativo está disponível a partir do Service Fabric versão 6.1.<br>
>
> Quando são usados parâmetros de aplicativo para especificar a governança dos recursos, o Service Fabric não pode ser rebaixado para uma versão anterior à versão 6.1.

## <a name="other-resources-for-containers"></a>Outros recursos para contêineres

Além da CPU e da memória, é possível especificar outros limites de recursos para contêineres. Esses limites são especificados no nível do pacote de códigos e aplicados quando o contêiner é iniciado. Ao contrário da CPU e da memória, o Gerenciador de Recursos de Cluster não reconhece esses recursos e não faz verificações de capacidade nem balanceamento de carga para eles.

* *MemorySwapInMB*: A quantidade de memória de troca que pode ser usada por um contêiner.
* *MemoryReservationInMB*: O limite flexível para governança de memória que é imposto apenas quando a contenção de memória é detectada no nó.
* *CpuPercent*: Percentual de CPU que pode ser usado pelo contêiner. Se os limites de CPU forem especificados para o pacote de serviço, esse parâmetro será efetivamente ignorado.
* *MaximumIOps*: IOPS máximo que pode ser usado por um contêiner (leitura e gravação).
* *MaximumIOBytesps*: E/S máximo (bytes por segundo) que pode ser usado por um contêiner (leitura e gravação).
* *BlockIOWeight*: Peso de E/S do bloqueio em relação a outros contêineres.

Esses recursos podem ser combinados com a CPU e a memória. Este é um exemplo de como especificar recursos adicionais para contêineres:

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ResourceGovernancePolicy CodePackageRef="FrontendService.Code" CpuPercent="5"
            MemorySwapInMB="4084" MemoryReservationInMB="1024" MaximumIOPS="20" />
        </Policies>
    </ServiceManifestImport>
```

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre o Gerenciador de Recursos de Cluster, leia [Introdução ao Gerenciador de Recursos de Cluster do Service Fabric](service-fabric-cluster-resource-manager-introduction.md).
* Para saber mais sobre o modelo de aplicativo, pacotes de serviço e pacotes de códigos e como as réplicas são mapeadas para eles, leia [Modelar um aplicativo no Service Fabric](service-fabric-application-model.md).
