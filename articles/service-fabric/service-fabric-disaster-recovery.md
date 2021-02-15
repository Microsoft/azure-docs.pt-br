---
title: Recuperação de desastre do Azure Service Fabric
description: O Azure Service Fabric oferece recursos para lidar com desastres. Este artigo descreve os tipos de desastres que podem ocorrer e como lidar com eles.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 8d99b4d1fbf227d850de387b7ca24dcd3fd40646
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98791148"
---
# <a name="disaster-recovery-in-azure-service-fabric"></a>Recuperação de desastre no Azure Service Fabric
Uma parte crítica do fornecimento de alta disponibilidade é garantir que os serviços possam sobreviver a todos os diferentes tipos de falhas. Isso é especialmente importante para falhas não planejadas e fora do seu controle. 

Este artigo descreve alguns modos de falha comuns que podem ser desastres se não forem modelados e gerenciados corretamente. Ele também aborda atenuações e ações a serem tomadas se um desastre ocorrer de qualquer forma. O objetivo é limitar ou eliminar o risco de tempo de inatividade ou perda de dados quando ocorrerem falhas, planejadas ou de outra forma.

## <a name="avoiding-disaster"></a>Evitando desastres
A principal meta do Azure Service Fabric é ajudá-lo a modelar seu ambiente e seus serviços de forma que os tipos de falha comuns não sejam desastres. 

Em geral, há dois tipos de cenários de desastre/falha:
- Falhas de hardware e software
- Falhas operacionais

### <a name="hardware-and-software-faults"></a>Falhas de hardware e software
Falhas de hardware e software são imprevisíveis. A maneira mais fácil de sobreviver a falhas é executar mais cópias do serviço em limites de falha de hardware ou software. 

Por exemplo, se o serviço estiver sendo executado em apenas um computador, a falha desse computador será um desastre para esse serviço. A maneira simples de evitar esse desastre é garantir que o serviço esteja em execução em vários computadores. O teste também é necessário para garantir que a falha de uma máquina não interrompa o serviço em execução. O planejamento de capacidade garante que uma instância de substituição possa ser criada em outro lugar e que a redução na capacidade não sobrecarregue os serviços restantes. 

O mesmo padrão funciona independentemente da falha que você estiver tentando evitar. Por exemplo, se você estiver preocupado com a falha de uma SAN, será executado em várias SANs. Se estiver preocupado com a perda de um rack de servidores, execute entre vários racks. Se você estiver preocupado com a perda de data centers, seu serviço deverá ser executado em várias regiões do Azure, em vários Zonas de Disponibilidade do Azure ou em seus próprios datacenters. 

Quando um serviço é distribuído em várias instâncias físicas (computadores, racks, data centers, regiões), você ainda está sujeito a alguns tipos de falhas simultâneas. Mas uma falha única e até várias falhas de um determinado tipo (por exemplo, uma única máquina virtual ou um link de rede que falhou) são manipuladas automaticamente e, portanto, não são mais um "desastre". 

Service Fabric fornece mecanismos para expandir o cluster e os identificadores que trazem os nós e os serviços com falha de volta. Service Fabric também permite a execução de muitas instâncias de seus serviços para evitar que falhas não planejadas entrem em desastres reais.

Pode haver motivos pelos quais a execução de uma implantação grande o suficiente para falhas de span não é viável. Por exemplo, pode levar mais recursos de hardware do que você está disposto a pagar em relação à possibilidade de falha. Quando você está lidando com aplicativos distribuídos, os saltos de comunicação adicionais ou os custos de replicação de estado entre distâncias geográficas podem causar latência inaceitável. Esse limite varia com cada aplicativo. 

Para falhas de software especificamente, a falha pode estar no serviço que você está tentando dimensionar. Nesse caso, mais cópias não impedem o desastre, porque a condição de falha está correlacionada em todas as instâncias.

### <a name="operational-faults"></a>Falhas operacionais
Mesmo que o serviço esteja estendido por todo o mundo com muitas redundâncias, ele ainda poderá passar por eventos desastrosos. Por exemplo, alguém pode reconfigurar acidentalmente o nome DNS para o serviço ou excluí-lo imediatamente. 

Por exemplo, digamos que você tivesse um serviço do Service Fabric com estado e alguém excluísse esse serviço acidentalmente. A menos que haja alguma outra mitigação, esse serviço e todo o estado que ele tinha agora já foram. Esses tipos de desastres operacionais ("ops") exigem mitigações e etapas de recuperação diferentes do que as que são necessárias para falhas não planejadas regulares. 

As melhores maneiras de evitar esses tipos de falhas operacionais são:
- Restrinja o acesso operacional ao ambiente.
- Faça uma auditoria rigorosa de operações perigosas.
- Imponha a automação, impeça alterações manuais ou fora de banda e valide alterações específicas no ambiente antes de fazê-las.
- Verifique se as operações destrutivas estão "suaves". As operações flexíveis não entram em vigor imediatamente ou podem ser desfeitas em uma janela de tempo.

O Service Fabric fornece mecanismos para evitar falhas operacionais, como fornecer controle de acesso [baseado em função](service-fabric-cluster-security-roles.md) para operações de cluster. No entanto, a maioria dessas falhas operacionais exige esforços organizacionais e outros sistemas. O Service Fabric fornece mecanismos para sobreviver a falhas operacionais, mais notavelmente [backup e restauração para serviços com estado](service-fabric-backuprestoreservice-quickstart-azurecluster.md).

## <a name="managing-failures"></a>Gerenciando falhas
O objetivo do Service Fabric é o gerenciamento automático de falhas. Mas, para lidar com alguns tipos de falhas, os serviços devem ter código adicional. Outros tipos de falhas _não_ devem ser abordados automaticamente por motivos de segurança e continuidade de negócios. 

### <a name="handling-single-failures"></a>Lidando com falhas únicas
Um único computador pode falhar por diversos tipos de motivos. Às vezes, são as causas de hardware, como fontes de alimentação e falhas de hardware de rede. Outras falhas estão no software. Isso inclui falhas do sistema operacional e do próprio serviço. Service Fabric detecta automaticamente esses tipos de falhas, incluindo casos em que a máquina se torna isolada de outras máquinas devido a problemas de rede.

Independentemente do tipo de serviço, executar uma única instância resulta em tempo de inatividade para esse serviço se essa única cópia do código falhar por qualquer motivo. 

Para lidar com qualquer falha única, a coisa mais simples que você pode fazer é garantir que seus serviços sejam executados em mais de um nó por padrão. Para serviços sem estado, certifique-se de que `InstanceCount` seja maior que 1. Para serviços com estado, a recomendação mínima é que `TargetReplicaSetSize` e `MinReplicaSetSize` são definidos como 3. Executar mais cópias do código de seu serviço garante que o serviço seja capaz de lidar com qualquer falha única automaticamente. 

### <a name="handling-coordinated-failures"></a>Lidando com falhas coordenadas
Falhas coordenadas em um cluster podem ser devido a falhas de infraestrutura planejadas ou não planejadas, alterações ou alterações de software planejadas. O Service Fabric modela zonas de infraestrutura que experimentam falhas coordenadas como *domínios de falha*. As áreas que apresentarão alterações de software coordenadas serão modeladas como *domínios de atualização*. Para obter mais informações sobre domínios de falha, domínios de atualização e topologia de cluster, consulte [descrever um cluster de Service Fabric usando o Gerenciador de recursos de cluster](service-fabric-cluster-resource-manager-cluster-description.md).

Por padrão, Service Fabric considera os domínios de falha e de atualização ao planejar onde os serviços devem ser executados. Por padrão, o Service Fabric tenta garantir que seus serviços sejam executados em vários domínios de falha e de atualização para que, se ocorrerem alterações planejadas ou não planejadas, seus serviços permaneçam disponíveis. 

Por exemplo, digamos que a falha de uma fonte de energia faça com que todos os computadores em um rack falhem simultaneamente. Com várias cópias do serviço em execução, a perda de muitos computadores na falha de domínio de falha se transforma em apenas outro exemplo de uma única falha para um serviço. É por isso que gerenciar domínios de falha e de atualização é essencial para garantir a alta disponibilidade de seus serviços. 

Quando você estiver executando Service Fabric no Azure, os domínios de falha e os domínios de atualização serão gerenciados automaticamente. Em outros ambientes, eles podem não ser. Se você estiver criando seus próprios clusters localmente, certifique-se de mapear e planejar o layout do domínio de falha corretamente.

Os domínios de atualização são úteis para modelar áreas em que o software será atualizado ao mesmo tempo. Por causa disso, os domínios de atualização geralmente definem os limites em que o software é desativado durante as atualizações planejadas. Atualizações do Service Fabric e de seus serviços seguem o mesmo modelo. Para obter mais informações sobre atualizações sem interrupção, domínios de atualização e o modelo de integridade de Service Fabric que ajuda a impedir que alterações não intencionais afetem o cluster e seu serviço, consulte:

 - [Atualização de aplicativo](service-fabric-application-upgrade.md)
 - [Tutorial de atualização de aplicativo](service-fabric-application-upgrade-tutorial.md)
 - [Modelo de integridade Service Fabric](service-fabric-health-introduction.md)

Você pode visualizar o layout do cluster usando o mapa de cluster fornecido no [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md):

<center>

![Os nós se espalham entre domínios de falha no Service Fabric Explorer][sfx-cluster-map]
</center>

> [!NOTE]
> Modelando áreas de falha, atualizações sem interrupção, executando muitas instâncias de seu código de serviço e estado, regras de posicionamento para garantir que seus serviços sejam executados em domínios de falha e de atualização, e monitoramento de integridade interno são apenas *alguns* dos recursos que Service Fabric fornece para manter problemas operacionais normais e falhas de entrada de desastres. 
>

### <a name="handling-simultaneous-hardware-or-software-failures"></a>Lidando com falhas simultâneas de hardware ou software
Falamos sobre falhas únicas. Como você pode ver, é fácil lidar com serviços com e sem estado, apenas mantendo mais cópias do código (e do estado) em execução nos domínios de falha e de atualização. 

Várias falhas aleatórias simultâneas também podem ocorrer. É mais provável que eles levem ao tempo de inatividade ou a um desastre real.


#### <a name="stateless-services"></a>Serviços sem estado
A contagem de instâncias para um serviço sem estado indica o número desejado de instâncias que precisam estar em execução. Quando qualquer (ou todas) das instâncias falha, Service Fabric responde criando automaticamente as instâncias de substituição em outros nós. Service Fabric continua a criar substituições até que o serviço volte à contagem de instâncias desejada.

Por exemplo, suponha que o serviço sem estado tenha um `InstanceCount` valor de-1. Esse valor significa que uma instância deve ser executada em cada nó no cluster. Se algumas dessas instâncias falharem, Service Fabric detectará que o serviço não está em seu estado desejado e tentará criar as instâncias nos nós em que estão ausentes.

#### <a name="stateful-services"></a>Serviços com estado
Há dois tipos de serviços com estado:
- Com estado persistente.
- Com estado sem persistência. (O estado é armazenado na memória.)

A recuperação de uma falha de um serviço com estado depende do tipo de serviço com estado, quantas réplicas o serviço tinha e quantas réplicas falharam.

Em um serviço com estado, os dados de entrada são replicados entre as réplicas (a primária e as secundárias ativas). Se a maioria das réplicas receber os dados, os dados serão considerados *Quorum* confirmados. (Para cinco réplicas, três serão um quorum.) Isso significa que, a qualquer momento, haverá pelo menos um quorum de réplicas com os dados mais recentes. Se as réplicas falharem (digamos duas de cinco), podemos usar o valor de quorum para calcular se pudermos recuperar. (Como as três restantes de cinco réplicas ainda estão ativas, é garantido que pelo menos uma réplica terá dados completos.)

Quando um quorum de réplicas falha, a partição é declarada para estar em um estado de *perda de quorum* . Digamos que uma partição tenha cinco réplicas, o que significa que pelo menos três têm a garantia de ter dados completos. Se um quorum (três a cinco) de réplicas falhar, Service Fabric não poderá determinar se as réplicas restantes (duas de cinco) têm dados suficientes para restaurar a partição. Nos casos em que o Service Fabric detecta perda de quorum, seu comportamento padrão é impedir gravações adicionais na partição, declarar a perda de quorum e aguardar a restauração de um quorum de réplicas.

Determinar se um desastre ocorreu para um serviço com estado e, em seguida, gerenciá-lo segue três estágios:

1. Determinando se houve perda de quorum ou não.
   
   A perda de quorum é declarada quando a maioria das réplicas de um serviço com estado está inativa ao mesmo tempo.
2. Determinando se a perda de quorum é permanente ou não.
   
   Na maioria das vezes, as falhas são transitórias. Os processos são reiniciados, os nós são reiniciados, as máquinas virtuais são reiniciadas e as partições de rede são reparadas. Às vezes, no entanto, as falhas são permanentes. Se as falhas são permanentes ou não depende se o serviço com estado persiste seu estado ou se ele o mantém apenas na memória: 
   
   - Para serviços sem estado persistente, a falha de um quorum ou mais de réplicas causa _imediatamente_ uma perda de quorum permanente. Quando o Service Fabric detecta a perda de quorum em um serviço não persistente com estado, ele prossegue imediatamente para a etapa 3, declarando uma (potencial) perda de dados. Prosseguir com a perda de dados faz sentido porque Service Fabric sabe que não há nenhum ponto em esperar que as réplicas voltem. Mesmo que eles se recuperem, os dados serão perdidos devido à natureza não persistente do serviço.
   - Para serviços persistentes com estado, uma falha de um quorum ou mais de réplicas faz com que Service Fabric aguarde até que as réplicas voltem e restaurem o quorum. Isso resulta em uma interrupção de serviço para qualquer _gravação_ na partição afetada (ou "conjunto de réplicas") do serviço. No entanto, as leituras ainda podem ser possíveis com garantias de consistência reduzidas. A quantidade de tempo padrão que Service Fabric aguarda até que o quorum seja restaurado é *infinito*, porque continuar é um evento de perda de dados (potencial) e transporta outros riscos. Isso significa que Service Fabric não prosseguirá para a próxima etapa, a menos que um administrador siga uma ação para declarar a perda de dados.
3. Determinar se os dados são perdidos e restaurá-los de backups.

   Se a perda de quorum tiver sido declarada (automaticamente ou por meio de ação administrativa), Service Fabric e os serviços passarão para determinar se os dados foram realmente perdidos. Neste ponto, Service Fabric também sabe que as outras réplicas não estão retornando. Essa foi a decisão tomada quando paramos de esperar que a perda de quorum se resolvesse. Normalmente, a melhor alternativa para o serviço é congelar e aguardar a intervenção administrativa específica.
   
   Quando Service Fabric chama o `OnDataLossAsync` método, é sempre devido à _suspeita_ de perda de dados. O Service Fabric garante que essa chamada seja entregue para a _melhor_ réplica restante. Essa seria qualquer réplica tenha tido o maior progresso. 
   
   O motivo pelo qual sempre dizem que a perda de dados _suspeita_ é que é possível que a réplica restante tenha o mesmo estado que o primário quando o quorum fosse perdido. No entanto, sem o estado para compará-la, não há nenhum modo adequado do Service Fabric ou dos operadores terem certeza.     
   
   Então, o que uma implementação típica do método `OnDataLossAsync` faz?
   1. Os logs de implementação que `OnDataLossAsync` foram disparados e que são acionados por todos os alertas administrativos necessários.
   1. Normalmente, a implementação pausa e aguarda mais decisões e ações manuais a serem tomadas. Isso ocorre porque, mesmo que os backups estejam disponíveis, talvez eles precisem ser preparados. 
   
      Por exemplo, se dois serviços diferentes coordenarem informações, talvez esses backups precisem ser modificados para garantir que, após a restauração, as informações que esses dois serviços se preocupam sejam consistentes. 
   1. Geralmente, há alguma outra telemetria ou esgotamento do serviço. Esses metadados podem estar contidos em outros serviços ou em logs. Essas informações podem ser usadas conforme necessário para determinar se houveram chamadas recebidas e processadas no primário que não estavam presentes no backup ou replicadas para essa réplica específica. Essas chamadas podem precisar ser reproduzidas ou adicionadas ao backup antes que a restauração seja viável.  
   1. A implementação compara o estado da réplica restante com o que está contido em quaisquer backups disponíveis. Se você estiver usando Service Fabric coleções confiáveis, há [ferramentas e processos](service-fabric-reliable-services-backup-restore.md) disponíveis para fazer isso. O objetivo é ver se o estado dentro da réplica é suficiente e ver o que o backup pode estar ausente.
   1. Depois que a comparação for feita e depois que a restauração for concluída (se necessário), o código de serviço deverá retornar **true** se alguma alteração de estado tiver sido feita. Se a réplica determinou que era a melhor cópia disponível do estado e não fez alterações, o código retorna **false**. 
   
      Um valor **true** indica que todas as _outras_ réplicas restantes agora podem ser inconsistentes com esta. Elas serão descartadas e recriadas desta réplica. Um valor **false** indica que nenhuma alteração de estado foi feita, para que as outras réplicas possam manter o que têm. 

É extremamente importante que os autores de serviço pratiquem cenários potenciais de perda de dados e falha antes que os serviços sejam implantados na produção. Para proteger contra a possibilidade de perda de dados, é importante [fazer backup periódico do estado](service-fabric-reliable-services-backup-restore.md) de qualquer um dos seus serviços com estado para um armazenamento com redundância geográfica. 

Você também deve garantir que você tenha a capacidade de restaurar o estado. Como os backups de vários serviços diferentes são feitos em momentos diferentes, você precisa garantir que, após uma restauração, seus serviços tenham uma exibição consistente entre si. 

Por exemplo, considere uma situação em que um serviço gera um número e o armazena e, em seguida, o envia para outro serviço que também o armazena. Após uma restauração, você pode descobrir que o segundo serviço tem o número, mas o primeiro não, porque seu backup não incluiu essa operação.

Se você descobrir que as réplicas restantes são insuficientes para continuar em um cenário de perda de dados e não puder reconstruir o estado do serviço da telemetria ou do esgotamento, a frequência dos backups determinará o melhor RPO (objetivo de ponto de recuperação) possível. O Service Fabric fornece muitas ferramentas para testar vários cenários de falha, incluindo quorum permanente e perda de dados que requer a restauração de um backup. Esses cenários são incluídos como parte das ferramentas de capacidade de teste no Service Fabric, gerenciados pelo serviço de análise de falha. Para obter mais informações sobre essas ferramentas e padrões, consulte [introdução ao serviço de análise de falha](service-fabric-testability-overview.md). 

> [!NOTE]
> Os serviços do sistema também podem sofrer perda de quorum. O impacto é específico para o serviço em questão. Por exemplo, a perda de quorum no serviço de nomenclatura afeta a resolução de nomes, enquanto que a perda de quorum no serviço de Gerenciador de Failover bloqueia novos failovers e criação de serviço. 
> 
> Os serviços do sistema Service Fabric seguem o mesmo padrão que seus serviços de gerenciamento de estado, mas não recomendamos que você tente movê-los para fora da perda de quorum e para uma possível perda de dados. Em vez disso, recomendamos que você  [procure o suporte](service-fabric-support.md) para encontrar uma solução que seja destinada à sua situação. Geralmente é preferível simplesmente esperar até que as réplicas inativas sejam retornadas.
>

#### <a name="troubleshooting-quorum-loss"></a>Solucionando problemas de perda de quorum

As réplicas podem estar inativas intermitentemente devido a uma falha transitória. Aguarde algum tempo enquanto Service Fabric tenta trazê-las. Se as réplicas estiverem inativas por mais do que uma duração esperada, siga estas ações de solução de problemas:
- As réplicas podem estar falhando. Verifique os relatórios de integridade no nível da réplica e os logs do aplicativo. Colete despejos de memória e execute as ações necessárias para recuperar.
- O processo de réplica pode ter se tornado sem resposta. Inspecione os logs de aplicativo para verificar isso. Coletar despejos de processo e parar o processo sem resposta. Service Fabric criará um processo de substituição e tentará retornar a réplica.
- Os nós que hospedam as réplicas podem estar inativos. Reinicie a máquina virtual subjacente para trazer os nós para cima.

Às vezes, talvez não seja possível recuperar réplicas. Por exemplo, as unidades falharam ou os computadores não estão respondendo fisicamente. Nesses casos, Service Fabric precisa ser instruído a não esperar pela recuperação de réplica.

Não *Use esses* métodos se a possível perda de dados for inaceitável para colocar o serviço online. Nesse caso, todos os esforços devem ser feitos na recuperação de máquinas físicas.

As ações a seguir podem resultar em perda de dados. Verifique antes de segui-las.
   
> [!NOTE]
> _Nunca_ é seguro usar esses métodos além de uma maneira direcionada contra partições específicas. 
>

- Use a `Repair-ServiceFabricPartition -PartitionId` `System.Fabric.FabricClient.ClusterManagementClient.RecoverPartitionAsync(Guid partitionId)` API ou. Essa API permite especificar a ID da partição a ser movida da perda de quorum e em potencial perda de dados.
- Se o cluster encontrar falhas frequentes que fazem com que os serviços entrem em um estado de perda de quorum e a possível _perda de dados for aceitável_, a especificação de um valor [QuorumLossWaitDuration](/powershell/module/servicefabric/update-servicefabricservice) apropriado poderá ajudar seu serviço a se recuperar automaticamente. Service Fabric aguardará o valor fornecido `QuorumLossWaitDuration` (o padrão é infinito) antes de executar a recuperação. *Não* recomendamos esse método porque isso pode causar perdas de dados inesperadas.

## <a name="availability-of-the-service-fabric-cluster"></a>Disponibilidade do cluster do Service Fabric
Em geral, o Cluster Service Fabric é um ambiente altamente distribuído sem pontos únicos de falha. Uma falha de um nó não causará problemas de disponibilidade ou confiabilidade para o cluster, principalmente porque os serviços do sistema Service Fabric seguem as mesmas diretrizes fornecidas anteriormente. Ou seja, eles sempre executam com três ou mais réplicas por padrão, e os serviços do sistema que são executados sem monitoração de estado em todos os nós. 

As camadas subjacentes de detecção de falha e rede do Service Fabric são totalmente distribuídas. A maioria dos serviços de sistema podem ser recriados dos metadados no cluster ou sabem como ressincronizar seu estado de outros locais. A disponibilidade do cluster poderá ficar comprometida se os serviços do sistema entrarem em situações de perda de quorum como as descritas anteriormente. Nesses casos, talvez você não consiga executar determinadas operações no cluster (como iniciar uma atualização ou implantar novos serviços), mas o próprio cluster ainda estará ativo. 

Os serviços em um cluster em execução continuarão em execução nessas condições, a menos que exijam gravações nos serviços do sistema para continuar funcionando. Por exemplo, se Gerenciador de Failover estiver em perda de quorum, todos os serviços continuarão a ser executados. Mas todos os serviços que falham não poderão ser reiniciados automaticamente, pois isso requer o envolvimento do Gerenciador de Failover. 

### <a name="failures-of-a-datacenter-or-an-azure-region"></a>Falhas de um datacenter ou de uma região do Azure
Em casos raros, um datacenter físico pode ficar temporariamente indisponível contra perda de energia ou conectividade de rede. Nesses casos, seus clusters e serviços do Service Fabric nesse data center ou região do Azure ficarão indisponíveis. No entanto, _os dados ficam preservados_. 

Para os clusters em execução no Azure, você pode exibir as atualizações sobre interrupções na [Página de status do Azure][azure-status-dashboard]. No evento altamente improvável de que um datacenter físico seja parcialmente ou totalmente destruído, todos os clusters Service Fabric hospedados ali ou os serviços dentro deles podem ser perdidos. Essa perda inclui qualquer Estado cujo backup não seja feito fora do Datacenter ou da região.

Há duas estratégias diferentes para sobreviver à falha permanente ou contínua de um único datacenter ou região: 

- Execute clusters Service Fabric separados em várias dessas regiões e use algum mecanismo para failover e failback entre esses ambientes. Esse tipo de modelo de vários clusters ativo/ativo ou ativo/passivo requer gerenciamento adicional e código de operações. Esse modelo também requer a coordenação de backups dos serviços em um datacenter ou região para que eles estejam disponíveis em outros datacenters ou regiões quando houver falha. 
- Executar um único cluster do Service Fabric que abrange vários data centers ou regiões. A configuração mínima com suporte para essa estratégia são três datacenters ou regiões. O número recomendado de regiões ou data centers é cinco. 
  
  Esse modelo requer uma topologia de cluster mais complexa. No entanto, o benefício é que a falha de um datacenter ou região é convertida de um desastre em uma falha normal. Essas falhas podem ser tratadas pelos mecanismos que funcionam para clusters dentro de uma única região. Domínios de falha, domínios de atualização e regras de posicionamento de Service Fabric garantem que as cargas de trabalho sejam distribuídas para tolerar falhas normais. 
  
  Para obter mais informações sobre políticas que podem ajudar a operar serviços nesse tipo de cluster, consulte [políticas de posicionamento para serviços de Service Fabric](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md).

### <a name="random-failures-that-lead-to-cluster-failures"></a>Falhas aleatórias que levam a falhas de cluster
Service Fabric tem o conceito de *nós de semente*. Trata-se de nós que mantêm a disponibilidade do cluster subjacente. 

Os nós de semente ajudam a garantir que o cluster fique ativo estabelecendo concessões com outros nós e servindo como desempates durante determinados tipos de falhas. Se as falhas aleatórias removerem a maioria dos nós de semente no cluster e não forem voltadas rapidamente, o cluster será desligado automaticamente. O cluster falhará. 

No Azure, o provedor de recursos Service Fabric gerencia Service Fabric configurações de cluster. Por padrão, o provedor de recursos distribui nós de semente entre domínios de falha e de atualização para o *tipo de nó primário*. Se o tipo de nó primário estiver marcado como durabilidade prata ou ouro, quando você remover um nó semente (seja dimensionando em seu tipo de nó primário ou removendo-o manualmente), o cluster tentará promover outro nó não semente da capacidade disponível do tipo de nó primário. Essa tentativa falhará se você tiver menos capacidade disponível do que o nível de confiabilidade do cluster exigir para o tipo de nó primário.

Em clusters de Service Fabric autônomos e no Azure, o tipo de nó primário é aquele que executa as sementes. Quando você estiver definindo um tipo de nó primário, o Service Fabric se beneficiará automaticamente do número de nós fornecidos pela criação de até nove nós de semente e sete réplicas de cada serviço do sistema. Se um conjunto de falhas aleatórias usar a maioria dessas réplicas simultaneamente, os serviços do sistema entrarão em perda de quorum. Se a maioria desses nós de semente for perdida, o cluster será desligado logo em seguida.

## <a name="next-steps"></a>Próximas etapas
- Saiba como simular várias falhas usando a [estrutura de capacidade de teste](service-fabric-testability-overview.md).
- Leia outros recursos de recuperação de desastres e alta disponibilidade. A Microsoft publicou várias orientações sobre estes tópicos. Embora alguns desses recursos se confiram em técnicas específicas para uso em outros produtos, eles contêm muitas práticas recomendadas gerais que você pode aplicar no contexto de Service Fabric:
  - [Lista de verificação de disponibilidade](/azure/architecture/checklist/resiliency-per-service)
  - [Executando a análise de recuperação de desastre](../azure-sql/database/disaster-recovery-drills.md)
  - [Recuperação de desastre e alta disponibilidade para aplicativos do Azure][dr-ha-guide]
- Saiba mais sobre as [opções de suporte do Service Fabric](service-fabric-support.md).


<!-- External links -->

[repair-partition-ps]: /windows/win32/perfctrs/specifying-a-counter-path
[azure-status-dashboard]:https://azure.microsoft.com/status/
[azure-regions]: https://azure.microsoft.com/regions/
[dr-ha-guide]: /previous-versions/azure/dn251004(v=azure.100)


<!-- Images -->

[sfx-cluster-map]: ./media/service-fabric-disaster-recovery/sfx-clustermap.png
