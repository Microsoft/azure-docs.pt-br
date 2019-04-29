---
title: Dimensionamento automático de serviços e contêineres no Azure Service Fabric | Microsoft Docs
description: O Azure Service Fabric permite que você defina políticas de dimensionamento automático para serviços e contêineres.
services: service-fabric
documentationcenter: .net
author: radicmilos
manager: ''
editor: nipuzovi
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/17/2018
ms.author: miradic
ms.openlocfilehash: 8e57c071c9fd93a8581d574aeec2b23b38b3ab95
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60844016"
---
# <a name="introduction-to-auto-scaling"></a>Introdução ao dimensionamento automático
O dimensionamento automático é uma funcionalidade adicional do Service Fabric para dimensionar dinamicamente seus serviços com base na carga que os serviços estão relatando ou com base no uso dos recursos. O dimensionamento automático fornece excelente elasticidade e permite o provisionamento de instâncias ou partições adicionais do seu serviço sob demanda. Todo o processo de dimensionamento automático é automatizado e transparente e, depois que você configurar as políticas em um serviço, não é necessário dimensionar as operações de dimensionamento manuais no nível do serviço. O dimensionamento automático pode ser ativado no momento da criação de serviço, ou a qualquer momento, atualizando o serviço.

Um cenário comum em que o dimensionamento automático é útil ocorre quando a carga de um serviço específico varia ao longo do tempo. Por exemplo, um serviço, como um gateway, pode ser dimensionado com base na quantidade de recursos necessários para lidar com solicitações de entrada. Vejamos um exemplo de como essas regras de dimensionamento poderiam ser:
* Se todas as instâncias do meu gateway estiverem usando mais de dois núcleos em média, escale o serviço de gateway horizontalmente adicionando mais uma instância. Faça isso a cada hora, mas nunca tenha mais de sete instâncias no total.
* Se todas as instâncias do meu gateway usarem menos de 0,5 núcleos em média, escale o serviço removendo uma instância. Faça isso a cada hora, mas nunca tenha menos que três instâncias no total.

Compatibilidade com dimensionamento automático para contêineres e serviços regulares do Service Fabric. Para usar o dimensionamento automático, você precisa estar executando a versão 6.2 ou superior do tempo de execução do Service Fabric. 

O restante deste artigo descreve as políticas de dimensionamento, maneiras de habilitar ou desabilitar o dimensionamento automático e oferece exemplos de como usar esse recurso.

## <a name="describing-auto-scaling"></a>Descrevendo o dimensionamento automático
As políticas de dimensionamento automático podem ser definidas para cada serviço em um cluster do Service Fabric. Cada política de dimensionamento consiste em duas partes:
* O **gatilho de dimensionamento** descreve quando o dimensionamento do serviço será executado. As condições definidas no gatilho são verificadas periodicamente para determinar se um serviço deve ser escalado ou não.

* O **mecanismo de dimensionamento** descreve como o dimensionamento será executado quando ele for disparado. O mecanismo só é aplicado quando as condições do gatilho são atendidas.

Todos os gatilhos compatíveis no momento funcionam com as [métricas de carga lógica](service-fabric-cluster-resource-manager-metrics.md) ou com métricas físicas como o uso de CPU ou de memória. De qualquer forma, o Service Fabric monitorará a carga relatada para a métrica e avaliará o gatilho periodicamente para determinar se o dimensionamento for necessário.

Atualmente, há dois mecanismos compatíveis com o dimensionamento automático. O primeiro destina-se aos serviços sem estado ou para contêineres quando o dimensionamento automático é executado adicionando ou removendo [instâncias](service-fabric-concepts-replica-lifecycle.md). Para serviços com e sem estado, o dimensionamento automático também pode ser executado adicionando ou removendo as [partições](service-fabric-concepts-partitioning.md) nomeadas do serviço.

> [!NOTE]
> Atualmente, não há suporte para apenas uma política de dimensionamento por serviço e apenas um gatilho de dimensionamento por dimensionamento de política.

## <a name="average-partition-load-trigger-with-instance-based-scaling"></a>Gatilho de carga média de partição com o dimensionamento baseado em instância
O primeiro tipo de gatilho é baseado na carga de instâncias em uma partição de serviço sem estado. Cargas de métricas são primeiro suavizadas para obter a carga de cada instância de uma partição e, em seguida, é calculada a média desses valores em todas as instâncias da partição. Há três fatores que determinam quando o serviço será dimensionado:

* _Limite inferior de carga_ é um valor que determina quando o serviço será **reduzido horizontalmente**. Se a carga média de todas as instâncias das partições for menor do que esse valor, o serviço será reduzido horizontalmente.
* _Limite superior de carga_ é um valor que determina quando o serviço será **escalado horizontalmente**. Se a carga média de todas as instâncias da partição for maior que esse valor, o serviço será escalado horizontalmente.
* _Intervalo de escala_ determina a frequência na qual o gatilho será verificado. Depois que o gatilho for verificado, se for necessário escalar, o mecanismo será aplicado. Se não for necessário escalar, nenhuma ação será tomada. Em ambos os casos, o gatilho não será verificado novamente antes do intervalo de escala expirar novamente.

Esse gatilho pode ser usado somente com os serviços sem estado (contêineres sem estado ou serviços do Service Fabric). Nos casos que um serviço tiver várias partições, o gatilho será avaliado separadamente para cada partição, e cada partição terá o mecanismo especificado aplicado independentemente. Portanto, nesse caso, é possível que algumas das partições do serviço sejam escaladas horizontalmente, reduzidas horizontalmente e outras não serão dimensionadas de forma nenhuma, tudo ao mesmo tempo e dependendo da carga.

O único mecanismo que pode ser usado com esse gatilho é PartitionInstanceCountScaleMechanism. Há três fatores que determinam como esse mecanismo é aplicado:
* O _Incremento de Escala_ determina quantas instâncias serão adicionadas ou removidas quando o mecanismo for disparado.
* _Número máximo de instâncias_ define o limite superior para o dimensionamento. Se o número de instâncias da partição atingir esse limite, o serviço não será escalado horizontalmente, independentemente da carga. É possível omitir esse limite especificando o valor de -1 e, nesse caso, o serviço será escalado horizontalmente tanto quanto possível (o limite é o número de nós que estão disponíveis no cluster).
* _Número mínimo de instâncias_ define o limite inferior para escala. Se o número de instâncias da partição atingir esse limite, o serviço não será reduzido horizontalmente, independentemente da carga.

## <a name="setting-auto-scaling-policy"></a>Configuração de política de escala automática

### <a name="using-application-manifest"></a>Usando o manifesto do aplicativo
``` xml
<LoadMetrics>
<LoadMetric Name="MetricB" Weight="High"/>
</LoadMetrics>
<ServiceScalingPolicies>
<ScalingPolicy>
    <AveragePartitionLoadScalingTrigger MetricName="MetricB" LowerLoadThreshold="1" UpperLoadThreshold="2" ScaleIntervalInSeconds="100"/>
    <InstanceCountScalingMechanism MinInstanceCount="3" MaxInstanceCount="4" ScaleIncrement="1"/>
</ScalingPolicy>
</ServiceScalingPolicies>
```
### <a name="using-c-apis"></a>Usando APIs C#
```csharp
FabricClient fabricClient = new FabricClient();
StatelessServiceDescription serviceDescription = new StatelessServiceDescription();
//set up the rest of the ServiceDescription
AveragePartitionLoadScalingTrigger trigger = new AveragePartitionLoadScalingTrigger();
PartitionInstanceCountScaleMechanism mechanism = new PartitionInstanceCountScaleMechanism();
mechanism.MaxInstanceCount = 3;
mechanism.MinInstanceCount = 1;
mechanism.ScaleIncrement = 1;
trigger.MetricName = "servicefabric:/_CpuCores";
trigger.ScaleInterval = TimeSpan.FromMinutes(20);
trigger.LowerLoadThreshold = 1.0;
trigger.UpperLoadThreshold = 2.0;
ScalingPolicyDescription policy = new ScalingPolicyDescription(mechanism, trigger);
serviceDescription.ScalingPolicies.Add(policy);
//as we are using scaling on a resource this must be exclusive service
//also resource monitor service needs to be enabled
serviceDescription.ServicePackageActivationMode = ServicePackageActivationMode.ExclusiveProcess
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```
### <a name="using-powershell"></a>Usando o Powershell
```posh
$mechanism = New-Object -TypeName System.Fabric.Description.PartitionInstanceCountScaleMechanism
$mechanism.MinInstanceCount = 1
$mechanism.MaxInstanceCount = 6
$mechanism.ScaleIncrement = 2
$trigger = New-Object -TypeName System.Fabric.Description.AveragePartitionLoadScalingTrigger
$trigger.MetricName = "servicefabric:/_CpuCores"
$trigger.LowerLoadThreshold = 0.3
$trigger.UpperLoadThreshold = 0.8
$trigger.ScaleInterval = New-TimeSpan -Minutes 10
$scalingpolicy = New-Object -TypeName System.Fabric.Description.ScalingPolicyDescription
$scalingpolicy.ScalingMechanism = $mechanism
$scalingpolicy.ScalingTrigger = $trigger
$scalingpolicies = New-Object 'System.Collections.Generic.List[System.Fabric.Description.ScalingPolicyDescription]'
$scalingpolicies.Add($scalingpolicy)
#as we are using scaling on a resource this must be exclusive service
#also resource monitor service needs to be enabled
Update-ServiceFabricService -Stateless -ServiceName "fabric:/AppName/ServiceName" -ScalingPolicies $scalingpolicies
```

## <a name="average-service-load-trigger-with-partition-based-scaling"></a>Gatilho de carga média de serviço com o dimensionamento baseado em partição
O segundo gatilho se baseia na carga de todas as partições de um serviço. Cargas de métricas são primeiro suavizadas para obter a carga para cada réplica ou uma instância de uma partição. Para os serviços com estado, a carga da partição é considerada a carga da réplica primária, enquanto para serviços sem estado, a carga da partição é a carga média de todas as instâncias da partição. A média desses valores é calculada em todas as partições do serviço, e esse valor é usado para disparar o dimensionamento automático. O mesmo ocorre no mecanismo anterior, há três fatores que determinam quando o serviço será escalado:

* _Limite inferior de carga_ é um valor que determina quando o serviço será **reduzido horizontalmente**. Se a carga média de todas as partições do serviço for menor do que esse valor, o serviço será reduzido horizontalmente.
* _Limite superior de carga_ é um valor que determina quando o serviço será **escalado horizontalmente**. Se a carga média de todas as partições do serviço for maior do que esse valor, o serviço será escalado horizontalmente.
* _Intervalo de escala_ determina a frequência na qual o gatilho será verificado. Depois que o gatilho for verificado, se for necessário escalar, o mecanismo será aplicado. Se não for necessário escalar, nenhuma ação será tomada. Em ambos os casos, o gatilho não será verificado novamente antes do intervalo de escala expirar novamente.

Esse gatilho pode ser usado com serviços com e sem estado. O único mecanismo que pode ser usado com esse gatilho é AddRemoveIncrementalNamedPartitionScalingMechanism. Quando o serviço é escalado horizontalmente, uma nova partição é adicionada, e quando o serviço é reduzido horizontalmente, uma das partições existentes é removida. Há restrições que serão verificadas quando o serviço for criado ou atualizado e a criação/atualização de serviço falhará se essas condições não forem atendidas:
* O esquema de partição nomeada precisa ser usado para o serviço.
* Os nomes de partição precisam ser números inteiros consecutivos, como “0”, “1”...
* O primeiro nome de partição precisa ser “0”.

Por exemplo, se um serviço for criado inicialmente com três partições, a única possibilidade válida para os nomes de partição será “0”, “1” e “2”.

A operação de dimensionamento automático real realizada também respeitará esse esquema de nomenclatura:
* Se as partições atuais do serviço forem nomeadas como “0”, “1” e “2”, a partição que será adicionada para escala horizontal será denominada “3”.
* Se as partições atuais do serviço forem nomeadas como “0”, “1” e “2”, a partição que será removida para redução horizontal na partição será denominada “2”.

Assim como acontece com o mecanismo que usa o dimensionamento adicionando ou removendo instâncias, há três parâmetros que determinam como esse mecanismo é aplicado:
* O _Incremento de Escala_ determina quantas partições serão adicionadas ou removidas quando o mecanismo for disparado.
* _Número máximo de partições_ define o limite superior para o dimensionamento. Se o número de partições do serviço atingir esse limite, o serviço não será escalado horizontalmente, independentemente da carga. É possível omitir esse limite especificando o valor de -1 e, nesse caso, o serviço será escalado horizontalmente tanto quanto possível (o limite é a capacidade real do cluster).
* _Número mínimo de instâncias_ define o limite inferior para escala. Se o número de partições do serviço atingir esse limite, o serviço não será reduzido horizontalmente, independentemente da carga.

> [!WARNING] 
> Quando AddRemoveIncrementalNamedPartitionScalingMechanism for usado com serviços com estado, o Service Fabric adicionará ou removerá partições **sem notificação ou aviso**. O reparticionamento de dados não será executado quando o mecanismo de colocação em escala for disparado. No caso da operação de aumento, novas partições ficarão vazias e, no caso de operação de redução, **a partição será excluída junto com todos os dados contidos nela**.

## <a name="setting-auto-scaling-policy"></a>Configuração de política de escala automática

### <a name="using-application-manifest"></a>Usando o manifesto do aplicativo
``` xml
<ServiceScalingPolicies>
    <ScalingPolicy>
        <AverageServiceLoadScalingTrigger MetricName="servicefabric:/_MemoryInMB" LowerLoadThreshold="300" UpperLoadThreshold="500" ScaleIntervalInSeconds="600"/>
        <AddRemoveIncrementalNamedPartitionScalingMechanism MinPartitionCount="1" MaxPartitionCount="3" ScaleIncrement="1"/>
    </ScalingPolicy>
</ServiceScalingPolicies>
```
### <a name="using-c-apis"></a>Usando APIs C#
```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceUpdateDescription serviceUpdate = new StatefulServiceUpdateDescription();
AveragePartitionLoadScalingTrigger trigger = new AverageServiceLoadScalingTrigger();
PartitionInstanceCountScaleMechanism mechanism = new AddRemoveIncrementalNamedPartitionScalingMechanism();
mechanism.MaxPartitionCount = 4;
mechanism.MinPartitionCount = 1;
mechanism.ScaleIncrement = 1;
//expecting that the service already has metric NumberOfConnections
trigger.MetricName = "NumberOfConnections";
trigger.ScaleInterval = TimeSpan.FromMinutes(15);
trigger.LowerLoadThreshold = 10000;
trigger.UpperLoadThreshold = 20000;
ScalingPolicyDescription policy = new ScalingPolicyDescription(mechanism, trigger);
serviceUpdate.ScalingPolicies = new List<ScalingPolicyDescription>;
serviceUpdate.ScalingPolicies.Add(policy);
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/AppName/ServiceName"), serviceUpdate);
```
### <a name="using-powershell"></a>Usando o Powershell
```posh
$mechanism = New-Object -TypeName System.Fabric.Description.AddRemoveIncrementalNamedPartitionScalingMechanism
$mechanism.MinPartitionCount = 1
$mechanism.MaxPartitionCount = 3
$mechanism.ScaleIncrement = 2
$trigger = New-Object -TypeName System.Fabric.Description.AverageServiceLoadScalingTrigger
$trigger.MetricName = "servicefabric:/_MemoryInMB"
$trigger.LowerLoadThreshold = 5000
$trigger.UpperLoadThreshold = 10000
$trigger.ScaleInterval = New-TimeSpan -Minutes 25
$scalingpolicy = New-Object -TypeName System.Fabric.Description.ScalingPolicyDescription
$scalingpolicy.ScalingMechanism = $mechanism
$scalingpolicy.ScalingTrigger = $trigger
$scalingpolicies = New-Object 'System.Collections.Generic.List[System.Fabric.Description.ScalingPolicyDescription]'
$scalingpolicies.Add($scalingpolicy)
#as we are using scaling on a resource this must be exclusive service
#also resource monitor service needs to be enabled
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -TargetReplicaSetSize 3 -MinReplicaSetSize 2 -HasPersistedState true -PartitionNames @("0","1") -ServicePackageActivationMode ExclusiveProcess -ScalingPolicies $scalingpolicies
```

## <a name="auto-scaling-based-on-resources"></a>Dimensionamento automático baseado em recursos

Para habilitar o serviço do Monitor de Recursos, o dimensionamento ocorre com base em recursos reais

``` json
"fabricSettings": [
...      
],
"addonFeatures": [
    "ResourceMonitorService"
],
```
Existem duas métricas que representam os recursos físicos reais. Um deles é servicefabric:/_CpuCores, que representa o uso real da CPU (portanto, 0,5 representa meio núcleo) e o outro é servicefabric:/_MemoryInMB que representa o uso de memória em MBs.
O ResourceMonitorService é responsável por controlar o uso da CPU e da memória dos serviços do usuário. Esse serviço aplicará a média móvel ponderada para contabilizar possíveis picos de curta duração. O monitoramento de recursos é compatível com aplicativos dentro e fora de contêineres no Windows e para os que estão dentro de contêineres no Linux. O dimensionamento automático de recursos é habilitado somente para serviços ativados no [modelo de processo exclusivo](service-fabric-hosting-model.md#exclusive-process-model).

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre [escalabilidade de aplicativo](service-fabric-concepts-scalability.md).
