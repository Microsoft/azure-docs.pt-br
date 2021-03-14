---
title: Governança de recursos para contêineres e serviços
description: O Service Fabric do Azure permite que você especifique solicitações de recursos e limites para serviços em execução como processos ou contêineres.
ms.topic: conceptual
ms.date: 8/9/2017
ms.openlocfilehash: d760766870c8c2be0a2d2384f6d012b75bc92fbd
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101735651"
---
# <a name="resource-governance"></a>Governança de recursos

Quando você estiver executando vários serviços no mesmo nó ou cluster, é possível que um serviço consuma mais recursos, privando outros serviços no processo. Esse problema é conhecido como o problema do “vizinho barulhento”. O Azure Service Fabric permite que o desenvolvedor controle esse comportamento especificando solicitações e limites por serviço para limitar o uso de recursos.

> Antes de continuar lendo este artigo, recomendamos que você se familiarize com o [Modelo de aplicativo do Service Fabric][application-model-link] e o [Modelo de hospedagem do Service Fabric][hosting-model-link].
>

## <a name="resource-governance-metrics"></a>Métricas de governança de recursos

Há suporte para a governança de recursos no Service Fabric de acordo com o [pacote de serviço][application-model-link]. Os recursos que são atribuídos ao pacote de serviço podem ser subdivididos entre pacotes de códigos. O Service Fabric dá suporte à governança de CPU e de memória por pacote de serviço, com duas [métricas](service-fabric-cluster-resource-manager-metrics.md)internas:

* *CPU* (nome da métrica `servicefabric:/_CpuCores` ): um núcleo lógico que está disponível no computador host. Todos os núcleos em todos os nós têm o mesmo peso.

* *Memória* (nome `servicefabric:/_MemoryInMB` da métrica): a memória é expressa em megabytes e é mapeada para a memória física que está disponível no computador.

Para essas duas métricas, o [CRM (Gerenciador de recursos de cluster)][cluster-resource-manager-description-link] controla a capacidade total do cluster, a carga em cada nó no cluster e os recursos restantes no cluster. Essas duas métricas são equivalentes a qualquer outro usuário ou métrica personalizada. Todos os recursos existentes podem ser usados com elas:

* O cluster pode ser [equilibrado](service-fabric-cluster-resource-manager-balancing.md) de acordo com essas duas métricas (comportamento padrão).
* O cluster pode ser [desfragmentado](service-fabric-cluster-resource-manager-defragmentation-metrics.md) de acordo com essas duas métricas.
* Ao [descrever um cluster][cluster-resource-manager-description-link], a capacidade armazenada em buffer pode ser definida para essas duas métricas.

> [!NOTE]
> Não há suporte para o [relatório de carga dinâmica](service-fabric-cluster-resource-manager-metrics.md) para essas métricas; as cargas para essas métricas são definidas no momento da criação.

## <a name="resource-governance-mechanism"></a>Mecanismo de governança de recursos

A partir da versão 7,2, o tempo de execução do Service Fabric dá suporte à especificação de solicitações e limites para recursos de CPU e memória.

> [!NOTE]
> Service Fabric versões de tempo de execução mais antigas que 7,2 dão suporte apenas a um modelo em que um único valor serve como a **solicitação** e o **limite** para um recurso específico (CPU ou memória). Isso é descrito como a especificação **RequestsOnly** neste documento.

* *Solicitações:* Os valores de solicitação de CPU e memória representam as cargas usadas pelo [CRM (Gerenciador de recursos de cluster)][cluster-resource-manager-description-link] para as `servicefabric:/_CpuCores` `servicefabric:/_MemoryInMB` métricas e. Em outras palavras, o CRM considera o consumo de recursos de um serviço igual aos seus valores de solicitação e usa esses valores ao tomar decisões de posicionamento.

* *Limites:* Os valores de CPU e limite de memória representam os limites de recursos reais aplicados quando um processo ou um contêiner é ativado em um nó.

Service Fabric permite **RequestsOnly, LimitsOnly** e ambas as especificações de **RequestsAndLimits** para CPU e memória.
* Quando a especificação RequestsOnly é usada, o Service Fabric também usa os valores de solicitação como limites.
* Quando a especificação LimitsOnly é usada, o Service Fabric considera os valores da solicitação como 0.
* Quando a especificação RequestsAndLimits é usada, os valores de limite devem ser maiores ou iguais aos valores de solicitação.

Para entender melhor o mecanismo de governança de recursos, vejamos um cenário de posicionamento de exemplo com uma especificação de **RequestsOnly** para o recurso de CPU (o mecanismo de governança de memória é equivalente). Considere um nó com dois núcleos de CPU e dois pacotes de serviço que serão colocados nele. O primeiro pacote de serviço a ser colocado, é composto de apenas um pacote de código de contêiner e especifica apenas uma solicitação de um núcleo de CPU. O segundo pacote de serviço a ser colocado, é composto de apenas um pacote de código baseado em processo e também especifica apenas uma solicitação de um núcleo de CPU. Como os dois pacotes de serviço têm uma especificação RequestsOnly, seus valores de limite são definidos para seus valores de solicitação.

1. Primeiro, o pacote de serviço baseado em contêiner solicitando um núcleo de CPU é colocado no nó. O tempo de execução ativa o contêiner e define o limite de CPU para um núcleo. O contêiner não poderá usar mais de um núcleo.

2. Em seguida, o pacote de serviço baseado em processo solicitando um núcleo de CPU é colocado no nó. O tempo de execução ativa o processo de serviço e define seu limite de CPU para um núcleo.

Neste ponto, a soma das solicitações é igual à capacidade do nó. O CRM não coloca mais nenhum contêiner ou processo de serviço com solicitações de CPU neste nó. No nó, um processo e um contêiner estão sendo executados com um núcleo cada e não serão disputados entre si para a CPU.

Agora, Vamos revisitar nosso exemplo com uma especificação **RequestsAndLimits** . Desta vez, o pacote de serviço baseado em contêiner especifica uma solicitação de um núcleo de CPU e um limite de dois núcleos de CPU. O pacote de serviço baseado em processo especifica uma solicitação e um limite de um núcleo de CPU.
  1. Primeiro, o pacote de serviço baseado em contêiner é colocado no nó. O tempo de execução ativa o contêiner e define o limite de CPU para dois núcleos. O contêiner não poderá usar mais de dois núcleos.
  2. Em seguida, o pacote de serviço baseado em processo é colocado no nó. O tempo de execução ativa o processo de serviço e define seu limite de CPU para um núcleo.

  Neste ponto, a soma de solicitações de CPU de pacotes de serviço que são colocados no nó é igual à capacidade de CPU do nó. O CRM não coloca mais nenhum contêiner ou processo de serviço com solicitações de CPU neste nó. No entanto, no nó, a soma dos limites (dois núcleos para o contêiner + um núcleo para o processo) excede a capacidade de dois núcleos. Se o contêiner e o processo forem intermitentes ao mesmo tempo, haverá a possibilidade de contenção para o recurso de CPU. Essa contenção será gerenciada pelo sistema operacional subjacente para a plataforma. Para este exemplo, o contêiner poderia disparar até dois núcleos de CPU, resultando na solicitação do processo de um núcleo de CPU não ser garantido.

> [!NOTE]
> Conforme ilustrado no exemplo anterior, os valores de solicitação para CPU e memória **não levam à reserva de recursos em um nó**. Esses valores representam o consumo de recursos que o Gerenciador de recursos de cluster considera ao tomar decisões de posicionamento. Os valores de limite representam os limites de recursos reais aplicados quando um processo ou um contêiner é ativado em um nó.


Há algumas situações em que pode haver contenção para a CPU. Nessas situações, o processo e o contêiner do nosso exemplo podem experimentar o problema do vizinho com ruído:

* *Combinação de serviços e contêineres controlados e não controlados*: se um usuário criar um serviço sem nenhuma governança de recursos especificada, o runtime considerará que ele não está consumindo nenhum recurso e poderá colocá-lo no nó de nosso exemplo. Nesse caso, esse novo processo efetivamente consume alguns recursos da CPU à custa dos serviços que já estão sendo executados no nó. Há duas soluções para esse problema. Não misturar serviços controlados e não controlado no mesmo cluster ou usar [restrições de posicionamento](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) para que esses dois tipos de serviços não terminem no mesmo conjunto de nós.

* *Quando outro processo for iniciado no nó, fora do Service Fabric (por exemplo, um serviço do SO)*: nessa situação, o processo fora do Service Fabric também competirá pela CPU com os serviços existentes. A solução para esse problema é configurar as capacidades de nó corretamente para a conta para a sobrecarga do sistema operacional, conforme mostrado na próxima seção.

* *Quando as solicitações não são iguais aos limites*: conforme descrito no exemplo de RequestsAndLimits anterior, as solicitações não levam à reserva de recursos em um nó. Quando um serviço com limites maiores que as solicitações é colocado em um nó, ele pode consumir recursos (se disponíveis) até os limites de ti. Nesses casos, outros serviços no nó podem não ser capazes de consumir recursos até seus valores de solicitação.

## <a name="cluster-setup-for-enabling-resource-governance"></a>Configuração de cluster para habilitar a governança de recursos

Quando o nó for iniciado e ingressado no cluster, o Service Fabric detectará a quantidade de memória e o número de núcleos disponíveis e então definirá as capacidades de nó para esses dois recursos.

Para deixar o espaço do buffer para o sistema operacional e para outros processos que possam estar em execução no nó, Service Fabric usa apenas 80% dos recursos disponíveis no nó. Esse percentual é configurável e pode ser alterado no manifesto do cluster.

Este é um exemplo de como instruir o Service Fabric a usar 50% da CPU disponível e 70% da memória disponível:

```xml
<Section Name="PlacementAndLoadBalancing">
    <!-- 0.0 means 0%, and 1.0 means 100%-->
    <Parameter Name="CpuPercentageNodeCapacity" Value="0.5" />
    <Parameter Name="MemoryPercentageNodeCapacity" Value="0.7" />
</Section>
```

Para a maioria dos clientes e cenários, a detecção automática de capacidades de nó para CPU e memória é a configuração recomendada (a detecção automática é ativada por padrão). No entanto, se precisar de configuração manual completa de capacidades do nó, você poderá configurá-las por tipo de nó usando o mecanismo para descrever os nós no cluster. Aqui está um exemplo de como configurar o tipo de nó com quatro núcleos e 2 GB de memória:

```xml
    <NodeType Name="MyNodeType">
      <Capacities>
        <Capacity Name="servicefabric:/_CpuCores" Value="4"/>
        <Capacity Name="servicefabric:/_MemoryInMB" Value="2048"/>
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
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="PreventTransientOvercommit" Value="true" />
    <Parameter Name="AllowConstraintCheckFixesDuringApplicationUpgrade" Value="true" />
</Section>
```

> [!IMPORTANT]
> A partir do Service Fabric versão 7,0, atualizamos a regra para como as capacidades de recursos de nó são calculadas nos casos em que o usuário fornece manualmente os valores para as capacidades de recurso do nó. Vamos considerar o seguinte cenário:
>
> * Há um total de 10 núcleos de CPU no nó
> * O it está configurado para usar 80% do total de recursos para os serviços de usuário (configuração padrão), que deixa um buffer de 20% para os outros serviços em execução no nó (incluindo Service Fabric serviços do sistema)
> * O usuário decide substituir manualmente a capacidade do recurso de nó para a métrica de núcleos de CPU e a define como 5 núcleos
>
> Alteramos a regra sobre como a capacidade disponível para serviços de usuário Service Fabric é calculada da seguinte maneira:
>
> * Antes de Service Fabric 7,0, a capacidade disponível para serviços de usuário seria calculada para **5 núcleos** (o buffer de capacidade de 20% é ignorado)
> * A partir do Service Fabric 7,0, a capacidade disponível para serviços de usuário seria calculada para **4 núcleos** (o buffer de capacidade de 20% não é ignorado)

## <a name="specify-resource-governance"></a>Especificar a governança de recurso

As solicitações e os limites de governança de recursos são especificados no manifesto do aplicativo (seção ServiceManifestImport). Veja alguns exemplos:

**Exemplo 1: especificação de RequestsOnly**
```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='https://www.w3.org/2001/XMLSchema-instance'>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCores="1"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="512" MemoryInMB="1024" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="256" MemoryInMB="1024" />
    </Policies>
  </ServiceManifestImport>
```

Neste exemplo, o `CpuCores` atributo é usado para especificar uma solicitação de 1 núcleo de CPU para o **pacote de pacotes**. Como o limite de CPU ( `CpuCoresLimit` atributo) não é especificado, Service Fabric também usa o valor de solicitação especificado de 1 núcleo como o limite de CPU para o pacote de serviço.

O Service **Packager** só será colocado em um nó em que a capacidade restante da CPU depois de subtrair a **soma das solicitações de CPU de todos os pacotes de serviço colocados nesse nó** seja maior ou igual a 1 núcleo. No nó, o pacote de serviço será limitado a um núcleo. O pacote de serviço contém dois pacotes de código (**CodeA1** e **CodeA2**) e ambos especificam o `CpuShares` atributo. A proporção de CpuShares 512:256 é usada para calcular os limites de CPU para os pacotes de código individuais. Portanto, o CodeA1 será limitado a dois terços de um núcleo e o CodeA2 será limitado a um terço de um núcleo. Se CpuShares não for especificado para todos os pacotes de código, Service Fabric dividirá o limite de CPU igualmente entre eles.

Embora CpuShares especificado para pacotes de códigos representem sua proporção relativa do limite de CPU geral do pacote de serviço, os valores de memória para pacotes de código são especificados em termos absolutos. Neste exemplo, o `MemoryInMB` atributo é usado para especificar solicitações de memória de 1024 MB para CodeA1 e CodeA2. Como o limite de memória ( `MemoryInMBLimit` atributo) não é especificado, Service Fabric também usa os valores de solicitação especificados como os limites para os pacotes de código. A solicitação de memória (e o limite) para o pacote de serviço é calculada como a soma dos valores de solicitação de memória (e limite) de seus pacotes de códigos de constituintes. Portanto, para o **Packager**, a solicitação de memória e o limite são calculados como 2048 MB.

O Service **Packager** só será colocado em um nó em que a capacidade de memória restante depois de subtrair a **soma das solicitações de memória de todos os pacotes de serviço colocados nesse nó** seja maior ou igual a 2048 MB. No nó, ambos os pacotes de código serão limitados a 1024 MB de memória. Pacotes de código (contêineres ou processos) não poderão alocar mais memória do que esse limite e a tentativa de fazer isso resultará em exceções de memória insuficiente.

**Exemplo 2: especificação de LimitsOnly**
```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='https://www.w3.org/2001/XMLSchema-instance'>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCoresLimit="1"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="512" MemoryInMBLimit="1024" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="256" MemoryInMBLimit="1024" />
    </Policies>
  </ServiceManifestImport>
```
Este exemplo usa `CpuCoresLimit` `MemoryInMBLimit` atributos e, que estão disponíveis apenas nas versões 7,2 e posteriores do it. O atributo CpuCoresLimit é usado para especificar um limite de CPU de 1 núcleo para o **pacote de pacotes**. Como a solicitação de CPU ( `CpuCores` atributo) não foi especificada, ela é considerada como 0. `MemoryInMBLimit` o atributo é usado para especificar os limites de memória de 1024 MB para CodeA1 e CodeA2 e, como as solicitações ( `MemoryInMB` Attribute) não são especificadas, elas são consideradas 0. A solicitação de memória e o limite do **pacote de pacotes** são, portanto, calculados como 0 e 2048, respectivamente. Como as solicitações de CPU e memória para o **pacote de pacotes** são 0, ela não apresenta nenhuma carga para o CRM considerar para posicionamento, para as `servicefabric:/_CpuCores` `servicefabric:/_MemoryInMB` métricas e. Portanto, de uma perspectiva de governança de recursos, o **pacotea** pode ser colocado em qualquer nó, **independentemente da capacidade restante**. Semelhante ao exemplo 1, no nó, CodeA1 será limitado a dois terços de um núcleo e 1024 MB de memória, e CodeA2 será limitado a um terço de um núcleo e 1024 MB de memória.

**Exemplo 3: especificação de RequestsAndLimits**
```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='https://www.w3.org/2001/XMLSchema-instance'>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCores="1" CpuCoresLimit="2"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="512" MemoryInMB="1024" MemoryInMBLimit="3072" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="256" MemoryInMB="2048" MemoryInMBLimit="4096" />
    </Policies>
  </ServiceManifestImport>
```
Com base nos dois primeiros exemplos, este exemplo demonstra a especificação de solicitações e limites para CPU e memória. O **pacotea** tem solicitações de CPU e memória de 1 núcleo e 3072 (1024 + 2048) MB, respectivamente. Ele só pode ser colocado em um nó que tenha pelo menos 1 núcleo (e 3072 MB) de capacidade restante após subtrair a soma de todas as solicitações de CPU (e memória) de todos os pacotes de serviço que são colocados no nó da capacidade total de CPU (e memória) do nó. No nó, o CodeA1 será limitado a dois terços de 2 núcleos e 3072 MB de memória, enquanto o CodeA2 será limitado a um terço de 2 núcleos e 4096 MB de memória.

### <a name="using-application-parameters"></a>Usando parâmetros de aplicativo

Ao especificar as configurações de governança de recursos, é possível usar [parâmetros de aplicativo](service-fabric-manage-multiple-environment-app-configuration.md) para gerenciar várias configurações de aplicativo. O exemplo a seguir mostra o uso de parâmetros do aplicativo:

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

## <a name="enforcing-the-resource-limits-for-user-services"></a>Impondo os limites de recursos para serviços de usuário

Ao aplicar a governança de recursos aos seus serviços de Service Fabric garante que esses serviços controlados por recursos não possam exceder sua cota de recursos, muitos usuários ainda precisam executar alguns dos serviços Service Fabric no modo não controlado. Ao usar serviços de Service Fabric não governados, é possível executar situações em que serviços não governados "descontrolados" consomem todos os recursos disponíveis nos nós de Service Fabric, o que pode levar a problemas sérios, como:

* Consumo de recursos de outros serviços em execução nos nós (incluindo Service Fabric serviços do sistema)
* Nós terminando em um estado não íntegro
* APIs de gerenciamento de Cluster Service Fabric sem resposta

Para evitar que essas situações ocorram, Service Fabric permite que você *aplique os limites de recursos para todos os Service Fabric serviços de usuário em execução no nó* (governados e não controlados) para garantir que os serviços de usuário nunca usarão mais do que a quantidade especificada de recursos. Isso é feito definindo o valor para a configuração EnforceUserServiceMetricCapacities na seção PlacementAndLoadBalancing de ClusterManifest como true. Essa configuração é desativada por padrão.

```xml
<SectionName="PlacementAndLoadBalancing">
    <ParameterName="EnforceUserServiceMetricCapacities" Value="false"/>
</Section>
```

Comentários adicionais:

* A imposição de limite de recursos só se aplica às `servicefabric:/_CpuCores` `servicefabric:/_MemoryInMB` métricas de recurso e
* A imposição de limite de recursos só funciona se as capacidades de nó para as métricas de recurso estiverem disponíveis para Service Fabric, seja por meio do mecanismo de detecção automática ou por usuários especificando manualmente as capacidades do nó (conforme explicado na seção [configuração do cluster para habilitar a governança de recursos](service-fabric-resource-governance.md#cluster-setup-for-enabling-resource-governance) ). Se as capacidades do nó não estiverem configuradas, o recurso de imposição de limite de recursos não poderá ser usado, pois Service Fabric não poderá saber a quantidade de recursos reservados para os serviços do usuário. Service Fabric emitirá um aviso de integridade se "EnforceUserServiceMetricCapacities" for true, mas as capacidades do nó não estiverem configuradas.

## <a name="other-resources-for-containers"></a>Outros recursos para contêineres

Além da CPU e da memória, é possível especificar outros limites de recursos para contêineres. Esses limites são especificados no nível do pacote de códigos e aplicados quando o contêiner é iniciado. Ao contrário da CPU e da memória, o Gerenciador de Recursos de Cluster não reconhece esses recursos e não faz verificações de capacidade nem balanceamento de carga para eles.

* *MemorySwapInMB*: a quantidade de memória de troca que pode ser usada por um contêiner.
* *MemoryReservationInMB*: o limite dinâmico para governança de memória que é imposto apenas quando a contenção de memória é detectada no nó.
* *CpuPercent*: o percentual de CPU que pode ser usado pelo contêiner. Se as solicitações de CPU ou os limites forem especificados para o pacote de serviço, esse parâmetro será efetivamente ignorado.
* *MaximumIOps*: máximo de IOPS que um contêiner pode usar (leitura e gravação).
* *MaximumIOBytesps*: máximo de E/S (bytes por segundo) que um contêiner pode usar (leitura e gravação).
* *BlockIOWeight*: o peso de E/S do bloco em relação a outros contêineres.

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
* Para saber mais sobre o modelo de aplicativo, pacotes de serviço e pacotes de códigos e como as réplicas são mapeadas para eles, leia [Modelar um aplicativo no Service Fabric][application-model-link].

<!-- Links -->
[application-model-link]: service-fabric-application-model.md
[hosting-model-link]: service-fabric-hosting-model.md
[cluster-resource-manager-description-link]: service-fabric-cluster-resource-manager-cluster-description.md
