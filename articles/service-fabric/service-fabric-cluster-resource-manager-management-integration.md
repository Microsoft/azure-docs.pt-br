---
title: Resource Manager de Cluster do Service Fabric – integração de gerenciamento | Microsoft Docs
description: Uma visão geral dos pontos de integração entre o Gerenciador de Recursos de Cluster e o Gerenciamento do Service Fabric.
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: 956cd0b8-b6e3-4436-a224-8766320e8cd7
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: c201945e94474d54b8a19918f3b55a0b40995a97
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60743506"
---
# <a name="cluster-resource-manager-integration-with-service-fabric-cluster-management"></a>Integração do Gerenciador de Recursos de Cluster com o gerenciamento de cluster do Service Fabric
O Gerenciador de recursos de Cluster do Service Fabric não realiza as atualizações no Service Fabric, mas está envolvido. A primeira maneira que o Cluster Resource Manager pode ajudar no gerenciamento é rastreando o estado desejado do cluster e dos serviços dentro dele. O Cluster Resource Manager envia relatórios de integridade quando não consegue deixar o cluster na configuração desejada. Por exemplo, se não houver capacidade suficiente, o Gerenciador de Recursos de Cluster enviará avisos de integridade e erros indicando o problema. Outra parte de integração tem a ver com a forma como as atualizações funcionam. O Gerenciador de Recursos de Cluster altera ligeiramente seu comportamento durante as atualizações.  

## <a name="health-integration"></a>Integração da integridade
O Gerenciador de Recursos de Cluster controla constantemente as regras que você definiu para posicionamento de seus serviços. Ele também controla a capacidade restante de cada métrica nos nós no cluster, e no cluster como um todo. Se ele não puder atender a essas regras ou se não houver capacidade insuficiente, erros e avisos de integridade serão emitidos. Por exemplo, se um nó estiver acima da capacidade, o Cluster Resource Manager tentará corrigir a situação movendo serviços. Se não puder corrigir a situação, ele emitirá um aviso de integridade indicando qual nó está acima da capacidade e para quais métricas.

Outro exemplo de avisos de integridade do Resource Manager são violações de restrições de posicionamento. Por exemplo, se você tiver definido uma restrição de posicionamento (como `“NodeColor == Blue”`) e o Resource Manager detectar uma violação desta restrição, ele emitirá um aviso de integridade. Isso é válido tanto para restrições personalizadas e padrão (como restrições de Domínio de Falha e Domínio de Atualização).

Eis um exemplo de tal relatório de integridade. Nesse caso, o relatório de integridade é voltado para uma das partições do serviço do sistema. A mensagem de integridade indica quais réplicas dessa partição estão temporariamente empacotadas em Domínios de Atualização insuficientes.

```posh
PS C:\Users\User > Get-ServiceFabricPartitionHealth -PartitionId '00000000-0000-0000-0000-000000000001'


PartitionId           : 00000000-0000-0000-0000-000000000001
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.PLB', Property='ReplicaConstraintViolation_UpgradeDomain', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   :
                        ReplicaId             : 130766528804733380
                        AggregatedHealthState : Ok

                        ReplicaId             : 130766528804577821
                        AggregatedHealthState : Ok

                        ReplicaId             : 130766528854889931
                        AggregatedHealthState : Ok

                        ReplicaId             : 130766528804577822
                        AggregatedHealthState : Ok

                        ReplicaId             : 130837073190680024
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.PLB
                        Property              : ReplicaConstraintViolation_UpgradeDomain
                        HealthState           : Warning
                        SequenceNumber        : 130837100116930204
                        SentAt                : 8/10/2015 7:53:31 PM
                        ReceivedAt            : 8/10/2015 7:53:33 PM
                        TTL                   : 00:01:05
                        Description           : The Load Balancer has detected a Constraint Violation for this Replica: fabric:/System/FailoverManagerService Secondary Partition 00000000-0000-0000-0000-000000000001 is
                        violating the Constraint: UpgradeDomain Details: UpgradeDomain ID -- 4, Replica on NodeName -- Node.8 Currently Upgrading -- false Distribution Policy -- Packing
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Ok->Warning = 8/10/2015 7:13:02 PM, LastError = 1/1/0001 12:00:00 AM
```

Veja o que a mensagem de integridade está nos dizendo:

1. Todas as réplicas estão íntegras: Cada uma tem AggregatedHealthState: OK
2. No momento, a restrição de distribuição Domínio de Atualização está sendo violada. Isso significa que um determinado Domínio de Atualização tem mais réplicas desta partição do que deveria.
3. Qual nó contém a réplica que está causando a violação. Nesse caso, é o nó com o nome "Node.8"
4. Se uma atualização estiver ocorrendo no momento para essa partição ("Atualmente em Atualização - falso")
5. A política de distribuição para este serviço: "Política de distribuição - empacotamento". Isso é controlado pela [política de posicionamento](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md#requiring-replica-distribution-and-disallowing-packing) do `RequireDomainDistribution`. "Empacotamento" indica que, nesse caso, DomainDistribution _não_ era necessária, portanto sabemos que a política de posicionamento não era especificada para esse serviço. 
6. Quando o relatório ocorreu (10/08/2015 19:13:02)

Informações sobre como isso gera alertas que são disparados na produção, para que você saiba que algo deu errado. Também são usadas para detectar e impedir atualizações inválidas. Nesse caso, queremos ver se conseguimos descobrir por que o Resource Manager precisou empacotar as réplicas no Domínio de Atualização. Normalmente, o empacotamento é temporário, pois os nós em outros Domínios de Atualização estavam inativos, por exemplo.

Digamos que o Gerenciador de Recursos de Cluster esteja tentando posicionar alguns serviços, mas nenhuma solução parece funcionar. Quando os serviços não podem ser posicionados, normalmente é por um dos seguintes motivos:

1. Alguma condição temporária impossibilitou o posicionamento correto dessa instância de serviço ou dessa réplica
2. Os requisitos de posicionamento do serviço não podem ser atendidos.

Nesses casos, os relatórios de integridade do Gerenciador de Recursos de Cluster ajudarão você a determinar por que o serviço não pode ser posicionado. Chamamos esse processo de sequência de eliminação de restrição. Durante o processo, o sistema percorre as restrições configuradas que afetam o serviço e os registros que eliminam. Dessa forma, quando os serviços não podem ser posicionados, você pode ver quais nós foram eliminados e por quê.

## <a name="constraint-types"></a>Tipos de restrição
Vamos falar sobre cada uma das diferentes restrições nesses relatórios de integridade. Você verá mensagens de integridade relacionadas a essas restrições quando não for possível posicionar as réplicas.

* **ReplicaExclusionStatic** e **ReplicaExclusionDynamic**: Essas restrições indicam que uma solução foi rejeitada porque dois objetos de serviço da mesma partição teriam que ser colocada no mesmo nó. Isso não é permitido, pois a falha desse nó afetaria demais nessa partição. ReplicaExclusionStatic e ReplicaExclusionDynamic são regras quase idênticas, e as diferenças não importam. Se você estiver vendo uma sequência de eliminação de restrição contendo a restrição ReplicaExclusionStatic ou ReplicaExclusionDynamic, o Cluster Resource Manager considerará que não existem nós válidos suficientes. Isso exige que as soluções restantes usem esses posicionamentos inválidos que não têm permissão. As outras restrições na sequência normalmente nos contarão o motivo de os nós estarem sendo eliminados em primeiro lugar.
* **PlacementConstraint**: Se você vir essa mensagem, isso significa que eliminamos alguns nós porque eles não correspondiam a restrições de posicionamento do serviço. Rastreamos as restrições de posicionamento configuradas atualmente como parte dessa mensagem. Isso é normal se houver restrições de posicionamento em vigor. No entanto, se a restrição de posicionamento estiver causando incorretamente a eliminação de muitos nós, isso será notado aqui.
* **NodeCapacity**: Essa restrição significa que o Cluster Resource Manager não conseguiu posicionar as réplicas em nós indicados porque isso colocaria acima da capacidade.
* **Afinidade**: Essa restrição indica que não Posicionamos a réplica em nós afetados, pois isso causaria uma violação da restrição de afinidade. Veja mais informações sobre afinidade [neste artigo](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
* **FaultDomain** e **UpgradeDomain**: Essa restrição elimina nós se o posicionamento da réplica em nós indicados causar empacotamento em um domínio de atualização ou de falha específico. Há vários exemplos que discutem essa restrição no tópico sobre [restrições de domínio de falha e de atualização e o comportamento resultante](service-fabric-cluster-resource-manager-cluster-description.md)
* **PreferredLocation**: Normalmente, você não verá essa restrição removendo nós da solução, pois ele é executado como uma otimização por padrão. Além disso, a restrição de local preferencial também está presente durante as atualizações. Durante a atualização, ela é usada para mover os serviços para onde estavam quando a atualização foi iniciada.

## <a name="blocklisting-nodes"></a>Incluir os nós em lista de bloqueio
Outra mensagem de integridade reportada pelo Gerenciador de Recursos de Cluster é quando os nós são incluídos em uma lista de bloqueio. Você pode pensar na inclusão em uma lista de bloqueio como uma restrição temporária aplicada automaticamente a você. Os nós são incluídos na lista de bloqueio quando sofrem falhas repetidas ao iniciar instâncias desse tipo de serviço. Os nós são incluídos em uma lista de bloqueio de acordo com o tipo de serviço. Um nó pode ser incluído em uma lista de bloqueio para um tipo de serviço, mas não outro. 

Você verá a inclusão em uma lista de bloqueio ocorrer com frequência durante o desenvolvimento: alguns bugs fazem com que o host de serviço falhe na inicialização. O Service Fabric tenta criar o host de serviço algumas vezes, e a falha continua ocorrendo. Após algumas tentativas, o nó é incluído em uma lista de bloqueio, e o Gerenciador de Recursos de Cluster tentará criar o serviço em outro lugar. Se essa falha continuar acontecendo em vários nós, talvez todos os nós válidos no cluster acabem bloqueados. A inclusão na lista de bloqueio também pode remover vários nós não conseguem iniciar o serviço a fim de atender à escala desejada. Normalmente, você verá erros ou avisos adicionais do Gerenciador de Recursos de Cluster, indicando que o serviço está abaixo da contagem desejada de réplicas ou de instâncias, bem como mensagens de integridade indicando o motivo da falha que está gerando a inclusão na lista de bloqueio.

A inclusão na lista de bloqueio não é uma condição permanente. Após alguns minutos, o nó é removido da lista de bloqueios, e o Service Fabric pode ativar novamente os serviços nesse nó. Se os serviços continuarem falhando, o nó será incluído na lista de bloqueio para esse tipo de serviço novamente. 

### <a name="constraint-priorities"></a>Prioridades de restrição

> [!WARNING]
> A alteração das prioridades de restrição não é recomendada e pode ter efeitos adversos consideráveis em seu cluster. As informações abaixo são fornecidas para referência das prioridades de restrição padrão e seu comportamento. 
>

Em todas essas restrições, você pode ter pensado “Ei, acho que restrições de domínio de falha são a coisa mais importante em meu sistema. Para garantir a não violação da restrição de domínio de falha, estou disposto(a) a violar outras restrições."

As restrições podem ser configuradas com níveis de prioridade diferentes. Estes são:

   - "inflexível" (0)
   - "flexível" (1)
   - "otimização” (2)
   - "desativado" (-1). 
   
A maioria das restrições é configurada como inflexível por padrão.

A alteração da prioridade das restrições é incomum. Às vezes, era necessário alterar as prioridades de restrição, normalmente para solucionar alguns bugs ou comportamento que estava afetando o ambiente. Em geral, a flexibilidade da infraestrutura de prioridade da restrição funcionou muito bem, mas ela não é necessário com tanta frequência. Na maioria das vezes, tudo fica em suas prioridades padrão. 

A existência dos níveis de prioridade não significa que uma certa restrição _será_ violada, nem que sempre será atendida. As prioridades de restrição definem uma ordem de imposição das restrições. As prioridades definem as compensações quando não é possível atender a todas as restrições. Geralmente, todas as restrições podem ser atendidas, exceto se algo a mais estiver ocorrendo no ambiente. Entre os exemplos de cenários que causarão violações de restrição estão as restrições conflitantes ou muitas falhas simultâneas.

Em situações avançadas, é possível alterar as prioridades de restrição. Por exemplo, digamos que você queira garantir que a afinidade seja sempre violada quando for necessário solucionar problemas de capacidade do nó. Para fazer isso, você poderia definir a prioridade da restrição de afinidade como “flexível” (1) e deixar a restrição de capacidade definida como “inflexível” (0).

Os valores de prioridade padrão para as diferentes restrições são especificados na configuração a seguir:

ClusterManifest.xml

```xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PlacementConstraintPriority" Value="0" />
            <Parameter Name="CapacityConstraintPriority" Value="0" />
            <Parameter Name="AffinityConstraintPriority" Value="0" />
            <Parameter Name="FaultDomainConstraintPriority" Value="0" />
            <Parameter Name="UpgradeDomainConstraintPriority" Value="1" />
            <Parameter Name="PreferredLocationConstraintPriority" Value="2" />
        </Section>
```

via ClusterConfig.json para implantações Autônomas ou Template.json para clusters hospedados pelo Azure:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "PlacementConstraintPriority",
          "value": "0"
      },
      {
          "name": "CapacityConstraintPriority",
          "value": "0"
      },
      {
          "name": "AffinityConstraintPriority",
          "value": "0"
      },
      {
          "name": "FaultDomainConstraintPriority",
          "value": "0"
      },
      {
          "name": "UpgradeDomainConstraintPriority",
          "value": "1"
      },
      {
          "name": "PreferredLocationConstraintPriority",
          "value": "2"
      }
    ]
  }
]
```

## <a name="fault-domain-and-upgrade-domain-constraints"></a>Restrições de domínio de falha e de atualização
O Gerenciador de Recursos de Cluster deseja manter os serviços distribuídos entre domínios de falha e de atualização. Ele simula isso como uma restrição dentro do mecanismo do Gerenciador de Recursos de Cluster. Para saber mais sobre como eles são usados e o comportamento específico, confira o artigo na [configuração de cluster](service-fabric-cluster-resource-manager-cluster-description.md#fault-and-upgrade-domain-constraints-and-resulting-behavior).

O Gerenciador de Recursos de Cluster pode precisar empacotar algumas réplicas em um domínio de atualização para lidar com atualizações, falhas ou outras violações de restrição. O empacotamento dentro de domínios de falha ou de atualização normalmente ocorre somente quando há várias falhas ou outra variação no sistema que impede o posicionamento correto. Se você quiser evitar o empacotamento mesmo durante essas situações, você pode utilizar a [política de posicionamento](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md#requiring-replica-distribution-and-disallowing-packing) do `RequireDomainDistribution`. Observe que isso pode afetar a disponibilidade e a confiabilidade do serviço como um efeito colateral, portanto, considere com cuidado.

Se o ambiente estiver configurado corretamente, todas as restrições serão totalmente respeitadas, mesmo durante as atualizações. O mais importante é que o Gerenciador de Recursos de Cluster esteja observando suas restrições. Ao detectar uma violação, ele imediatamente informa e tenta corrigir o problema.

## <a name="the-preferred-location-constraint"></a>A restrição do local preferencial
A restrição PreferredLocation é um pouco diferente, pois tem dois usos diferentes. Uma utilização dessa restrição ocorre durante as atualizações de aplicativo. O Gerenciador de Recursos de Cluster gerencia automaticamente essa restrição durante as atualizações. Ele é usado para garantir que, após a conclusão das atualizações, as réplicas retornem aos seus locais inicias. Outro uso da restrição PreferredLocation destina-se à [`PreferredPrimaryDomain` política de posicionamento](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) do. Ambos são otimizações e, então, a restrição PreferredLocation é a única restrição definida como "Otimização" por padrão.

## <a name="upgrades"></a>Atualizações
O Cluster Resource Manager também ajuda durante atualizações de aplicativos e clusters, quando então ele tem dois trabalhos:

* verificar se as regras do cluster não foram comprometidos
* tentar ajudar a atualização a ocorrer sem problemas

### <a name="keep-enforcing-the-rules"></a>Manter a imposição das regras
O principal a ser observado são se as regras, restrições rígidas sobre coisas como restrições de posicionamento e capacidades, ainda são impostas durante as atualizações. Restrições de posicionamento garantem que suas cargas de trabalho serão executadas apenas onde elas são permitidas, mesmo durante atualizações. Quando os serviços forem altamente restritos, as atualizações podem demorar mais. Quando o serviço, ou o nó onde ele está em execução, ficar inativo devido a uma atualização, você terá algumas opções.

### <a name="smart-replacements"></a>Substituições inteligentes
Quando uma atualização é iniciada, o Resource Manager tira um instantâneo da disposição atual do cluster. À medida que cada Domínio de Atualização for concluído, ele tentará retornar os serviços que estavam nesse Domínio de Atualização para a organização original. Dessa forma, há no máximo duas transições para um serviço durante a atualização. Há uma movimentação para fora do nó afetado e uma movimentação para dentro. Retornar o cluster ou serviço ao estado anterior à atualização também garante que a atualização não afete o layout do cluster. 

### <a name="reduced-churn"></a>Variação reduzida
Outra coisa que acontece durante as atualizações é que o Gerenciador de Recursos de Cluster desativa o balanceamento. Impedir o balanceamento impede reações desnecessárias à atualização em si, como a movimentação dos serviços para nós que foram esvaziados para a atualização. Se a atualização em questão for uma atualização de Cluster, então o cluster inteiro não poderá ser balanceado durante a atualização. As verificações de restrição permanecem ativas, somente a movimentação com base no balanceamento proativo de métricas é desabilitada.

### <a name="buffered-capacity--upgrade"></a>Capacidade de buffer e atualização
Em geral, convém concluir a atualização mesmo se o cluster estiver sob restrição ou quase cheio. Ser capaz de gerenciar a capacidade do cluster é ainda mais importante do que o normal. Dependendo do número de domínios de atualização, entre cinco e 20% da capacidade devem ser migradas à medida que a atualização percorre o cluster. Esse trabalho precisa ir para algum lugar. É aqui que a noção de [capacidades de buffer](service-fabric-cluster-resource-manager-cluster-description.md#buffered-capacity) é útil. A capacidade de buffer é respeitada durante a operação normal. O Gerenciador de Recursos de Cluster pode preencher os nós até sua capacidade total (consumindo o buffer) durante as atualizações, se for necessário.

## <a name="next-steps"></a>Próximas etapas
* Comece do princípio e [veja uma introdução ao Resource Manager de Cluster do Service Fabric](service-fabric-cluster-resource-manager-introduction.md)
