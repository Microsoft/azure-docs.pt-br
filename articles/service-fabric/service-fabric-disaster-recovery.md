---
title: Recuperação de desastre do Azure Service Fabric | Microsoft Docs
description: O Azure Service Fabric oferece os recursos necessários para lidar com todos os tipos de desastres. Este artigo descreve os tipos de desastres que podem ocorrer e como lidar com eles.
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 7153a6ed4a91e59eea936f1e17d827a40bb99371
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60948496"
---
# <a name="disaster-recovery-in-azure-service-fabric"></a>Recuperação de desastre no Azure Service Fabric
Uma parte crítica da entrega de alta disponibilidade é garantir que os serviços possam sobreviver a todos os tipos diferentes de falhas. Isso é especialmente importante para falhas não planejadas e fora de seu controle. Este artigo descreve alguns modos de falha comuns que poderão se transformar em desastres se não forem modelados e gerenciados corretamente. Ele também aborda mitigações e ações a serem tomadas se, ainda assim, um desastre acontecer. A meta é limitar ou eliminar o risco de que haja tempo de inatividade ou perda de dados quando ocorrerem falhas, sejam elas planejadas ou não.

## <a name="avoiding-disaster"></a>Evitando desastres
A meta principal do Service Fabric é ajudá-lo a modelar seu ambiente e seus serviços de forma que tipos de falha comuns não se transformem em desastres. 

De modo geral, há dois tipos de cenários de desastre/falha:

1. Falhas de hardware ou software
2. Falhas operacionais

### <a name="hardware-and-software-faults"></a>Falhas de hardware e software
Falhas de hardware e software são imprevisíveis. A maneira mais fácil de sobreviver a falhas é executar mais cópias do serviço distribuídas entre os limites de falha de hardware ou software. Por exemplo, se seu serviço estiver em execução somente em um computador específico, a falha desse computador será um desastre para o serviço em questão. A maneira simples de evitar esse desastre é garantir que, na verdade, o serviço esteja em execução em vários computadores. Testes também são necessários para garantir que a falha de um computador não interrompa o serviço em execução. O planejamento de capacidade garante que uma instância de substituição possa ser criada em outro lugar e que a redução da capacidade não sobrecarregue os serviços restantes. O mesmo padrão funciona independentemente da falha que você estiver tentando evitar. Por exemplo. Se estiver preocupado com a falha de uma SAN, execute entre várias SANs. Se estiver preocupado com a perda de um rack de servidores, execute entre vários racks. Se você estiver preocupado com a perda de data centers, seu serviço deverá ser executado em vários data centers ou regiões do Azure. 

Quando executa neste tipo de modo estendido, você ainda está sujeito a alguns tipos de falhas simultâneas, mas falhas únicas, e até mesmo múltiplas, de um determinado tipo (por exemplo, a falha de um link de uma única VM ou rede) são tratadas automaticamente (e, portanto, não são mais um "desastre"). O Service Fabric fornece vários mecanismos para expandir o cluster e trata de colocar nós e serviços com falha de volta em funcionamento. O Service Fabric também permite executar várias instâncias de seus serviços para evitar que esses tipos de falhas não planejadas se tornem desastres reais.

Pode haver motivos pelos quais executar uma implantação grande o suficiente para abranger essas falhas não seja viável. Por exemplo, podem ser necessários mais recursos de hardware do que você está disposto a pagar em relação à possibilidade de falha. Ao lidar com aplicativos distribuídos, é possível que os saltos de comunicação adicionais ou os custos de replicação de estado entre distâncias geográficas causem latência inaceitável. Esse limite varia com cada aplicativo. Para falhas de software especificamente, a falha pode estar no serviço que você está tentando dimensionar. Nesse caso, mais cópias não impedem um desastre, uma vez que a condição de falha está correlacionada entre todas as instâncias.

### <a name="operational-faults"></a>Falhas operacionais
Mesmo que o serviço esteja estendido por todo o mundo com muitas redundâncias, ele ainda poderá passar por eventos desastrosos. Por exemplo, se alguém reconfigurar acidentalmente o nome DNS do serviço ou simplesmente exclui-lo. Por exemplo, digamos que você tivesse um serviço do Service Fabric com estado e alguém excluísse esse serviço acidentalmente. A menos que houvesse alguma outra mitigação, esse serviço e todos o seu estado desapareceriam. Esses tipos de desastres operacionais ("ops") exigem mitigações e etapas de recuperação diferentes do que as que são necessárias para falhas não planejadas regulares. 

As melhores maneiras de evitar esses tipos de falhas operacionais são
1. restringir o acesso operacional ao ambiente
2. realizar auditorias rígidas de operações perigosas
3. impor a automação, prevenir-se contra alterações manuais ou fora de banda e validar alterações específicas no ambiente real antes de aplicá-las
4. garantir que operações destrutivas sejam "suaves". Operações suaves não entram em efeito imediatamente ou podem ser desfeitas dentro de uma janela de tempo

O Service Fabric fornece alguns mecanismos para evitar falhas operacionais, como fornecer controle de acesso [baseado em função](service-fabric-cluster-security-roles.md) para operações de cluster. No entanto, a maioria dessas falhas operacionais exige esforços organizacionais e outros sistemas. O Service Fabric fornece alguns mecanismos para sobreviver a falhas operacionais, mais notoriamente o backup e restauração para serviços com estado.

## <a name="managing-failures"></a>Gerenciando falhas
A meta do Service Fabric é quase sempre o gerenciamento automático de falhas. No entanto, para lidar com alguns tipos de falhas, os serviços precisam ter código adicional. Outros tipos de falhas _não_ devem ser resolvidas automaticamente por motivos de segurança e de continuidade de negócios. 

### <a name="handling-single-failures"></a>Lidando com falhas únicas
Um único computador pode falhar por diversos tipos de motivos. Alguns deles são causados pelo hardware, como falhas de fontes de alimentação e de hardware de rede. Outras falhas estão no software. Elas incluem falhas no sistema operacional em si e no próprio serviço. O Service Fabric detecta automaticamente esses tipos de falha, inclusive os casos em que o computador fica isolado de outros computadores devido a problemas de rede.

Independentemente do tipo de serviço, executar uma única instância resulta em tempo de inatividade para esse serviço se essa única cópia do código falhar por qualquer motivo. 

Para lidar com qualquer falha única, a coisa mais simples que você pode fazer é garantir que seus serviços sejam executados em mais de um nó por padrão. Para serviços sem estado, isso pode ser feito tendo um `InstanceCount` maior que 1. Para serviços com estado, a recomendação mínima é sempre um `TargetReplicaSetSize` e `MinReplicaSetSize` de pelo menos 3. Executar mais cópias do código de seu serviço garante que o serviço seja capaz de lidar com qualquer falha única automaticamente. 

### <a name="handling-coordinated-failures"></a>Lidando com falhas coordenadas
Falhas coordenadas podem ocorrer em um cluster devido a alterações e falhas de infraestrutura planejadas ou não planejadas, ou a alterações de software planejadas. O Service Fabric cria zonas de infraestrutura que passam por falhas coordenadas e são chamadas de Domínios de Falha. Áreas que passarão por alterações de software coordenadas são chamadas de Domínios de Atualização. Há mais informações sobre os domínios de falha e de atualização [neste documento](service-fabric-cluster-resource-manager-cluster-description.md), que descreve a topologia e a definição dos clusters.

Por padrão, o Service Fabric considera os domínios de falha e de atualização ao planejar onde os serviços devem ser executados. Por padrão, o Service Fabric tenta garantir que seus serviços sejam executados em vários domínios de falha e de atualização, de modo que se ocorrerem alterações planejadas ou não planejadas, os serviços permanecerão disponíveis. 

Por exemplo, digamos que a falha de uma fonte de energia faça com que um rack de computadores falhe simultaneamente. Com várias cópias do serviço em execução, a perda de vários computadores em uma falha de domínio de falha se transforma em apenas mais um exemplo de falha única para um determinado serviço. Por isso, gerenciar domínios de falha é essencial para garantir a alta disponibilidade de seus serviços. Ao executar o Service Fabric no Azure, os domínios de falha são gerenciados automaticamente. Em outros ambientes, eles podem não ser. Se você estiver criando seus próprios clusters locais, não deixe de mapear e planejar o layout de seu domínio de falha corretamente.

Domínios de Atualização são úteis para modelar áreas em que o software será atualizado ao mesmo tempo. Por isso, frequentemente os Domínios de Atualização também definem os limites em que o software é desativado durante atualizações planejadas. Atualizações do Service Fabric e de seus serviços seguem o mesmo modelo. Para saber mais sobre atualizações sem interrupção, domínios de atualização e o modelo de integridade do Service Fabric que ajuda a impedir que alterações não intencionais afetem o cluster e seu serviço, consulte estes documentos:

 - [Atualização de aplicativo](service-fabric-application-upgrade.md)
 - [Tutorial de atualização de aplicativo](service-fabric-application-upgrade-tutorial.md)
 - [Modelo de integridade do Service Fabric](service-fabric-health-introduction.md)

É possível visualizar o layout de seu cluster usando o mapa de cluster fornecido no [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md):

<center>

![Nós espalhados pelos domínios de falha no Service Fabric Explorer][sfx-cluster-map]
</center>

> [!NOTE]
> A modelagem de áreas de falha, as atualizações sem interrupção, a execução de várias instâncias de seu código e estado de serviço, as regras de posicionamento para garantir que os serviços sejam executados entre os domínios de falha e de atualização e o monitoramento de integridade interno são apenas **alguns** dos recursos que o Service Fabric fornece para impedir que falhas e problemas operacionais normais se tornem desastres. 
>

### <a name="handling-simultaneous-hardware-or-software-failures"></a>Lidando com falhas simultâneas de hardware ou software
Acima, falamos sobre falhas únicas. Como você pode ver, é fácil lidar com elas com serviços com e sem estado apenas mantendo mais cópias do código (e do estado) em execução entre domínios de falha e de atualização. Várias falhas aleatórias simultâneas também podem ocorrer. Elas têm mais probabilidade de causar um desastre real.


### <a name="random-failures-leading-to-service-failures"></a>Falhas aleatórias que causam falhas de serviço
Digamos que o serviço tivesse um `InstanceCount` igual 5 e que vários nós em execução nessas instâncias falhassem todos ao mesmo tempo. O Service Fabric responde criando automaticamente instâncias de substituição em outros nós. Ele continuará criando substituições até que o serviço volte à contagem de instâncias desejada. Em outro exemplo, digamos que houvesse um serviço sem estado com um `InstanceCount` igual a -1, o que significa que ele é executado em todos os nós válidos do cluster. Digamos que algumas dessas instâncias falhassem. Nesse caso, o Service Fabric observa que o serviço não está no estado desejado e tenta criar as instâncias nos nós em que elas estão ausentes. 

Para serviços com estado, a situação depende de o serviço estar em um estado persistente ou não. Também depende de quantas réplicas o serviço tinha e de quantas falharam. Determinar se um desastre ocorreu para um serviço com estado e gerenciá-lo é um processo de três estágios:

1. Determinar se houve perda de quorum ou não
   - Uma perda de quorum ocorre sempre que a maioria das réplicas de um serviço com estado ficam inativas ao mesmo tempo, inclusive a primária.
2. Determinar se a perda de quorum é permanente ou não
   - Na maioria das vezes, as falhas são transitórias. Os processos são reiniciados, os nós são reiniciados, as VMs são relançadas e as partições de rede se recuperam. No entanto, algumas vezes as falhas são permanentes. 
     - Para serviços sem estado persistente, a falha de um quorum ou mais de réplicas causa _imediatamente_ uma perda de quorum permanente. Quando o Service Fabric detecta a perda de quorum em um serviço não persistente com estado, ele prossegue imediatamente para a etapa 3, declarando uma (potencial) perda de dados. Prosseguir com a perda de dados faz sentido porque o Service Fabric sabe que não há motivos para esperar para as réplicas retornem, uma vez que, mesmo que fossem recuperadas, elas estariam vazias.
     - Para serviços persistentes com estado, uma falha de um quorum ou mais de réplicas faz com que o Service Fabric comece a aguardar que as réplicas retornem e restaure o quorum. Isso resulta em uma interrupção de serviço para qualquer _gravação_ na partição afetada (ou "conjunto de réplicas") do serviço. No entanto, leituras ainda podem ser possíveis, com garantias de consistência reduzidas. A quantidade de tempo padrão que o Service Fabric aguarda o quorum ser restaurado é infinita, uma vez que prosseguir é um evento de (potencial) perda de dados e traz consigo outros riscos. Substituir o valor de `QuorumLossWaitDuration` padrão é possível, mas não é recomendado. Em vez disso, nesse momento, devem ser feitos todos os esforços para restaurar as réplicas inoperantes. Isso requer colocar os nós inoperantes de volta em operação e garantir que eles possam remontar as unidades em que armazenaram o estado persistente local. Se a perda de quorum for causada por uma falha de processo, o Service Fabric tentará automaticamente recriar os processos e reiniciar as réplicas dentro deles. Se isso falhar, o Service Fabric relatará erros de integridade. Esses erros podem ser resolvidos e, em seguida, as réplicas normalmente voltam. Às vezes, no entanto, não é possível trazer as réplicas de volta. Por exemplo, as unidades podem todas ter falhado ou as máquinas podem ter sido destruídas fisicamente de alguma forma. Nesses casos, agora temos um evento de perda de quorum permanente. Para instruir o Service Fabric a parar de aguardar o retorno das réplicas inoperantes, um administrador de cluster deve determinar quais partições de quais serviços foram afetadas e chamar a API `Repair-ServiceFabricPartition -PartitionId` ou `System.Fabric.FabricClient.ClusterManagementClient.RecoverPartitionAsync(Guid partitionId)`.  Essa API permite especificar a ID da partição a ser movida de QuorumLoss para a potencial perda de dados.

   > [!NOTE]
   > _Nunca_ é seguro usar essa API a não ser que seja de maneira direcionada a partições específicas. 
   >

3. Determinando se houve perda de dados real e restaurando backups
   - Quando o Service Fabric chama o método `OnDataLossAsync`, isso sempre se deve a uma perda de dados _suspeita_. O Service Fabric garante que essa chamada seja entregue para a _melhor_ réplica restante. Essa seria qualquer réplica tenha tido o maior progresso. O motivo pelo qual sempre falamos em perdas de dados _suspeitas_ é que é possível que a réplica restante tenha de fato o mesmo estado que a réplica primária tinha quando foi desativada. No entanto, sem o estado para compará-la, não há nenhum modo adequado do Service Fabric ou dos operadores terem certeza. Nesse ponto, o Service Fabric também sabe que a outras réplicas não retornarão. Essa foi a decisão tomada quando paramos de esperar que a perda de quorum se resolvesse. Normalmente, a melhor alternativa para o serviço é congelar e aguardar a intervenção administrativa específica. Então, o que uma implementação típica do método `OnDataLossAsync` faz?
   - Primeiro, registrar em log que `OnDataLossAsync` foi disparado, bem como disparar os alertas administrativos necessários.
   - Geralmente, nesse ponto, pausar e aguardar até que outras decisões e ações manuais sejam tomadas. Isso ocorre porque, mesmo que os backups estejam disponíveis, talvez seja necessário prepará-los. Por exemplo, se dois serviços diferentes coordenarem informações, talvez seja necessário modificar esses backups para garantir que, depois que a restauração acontecer, as informações com que esses dois serviços se preocupam sejam consistentes. 
   - Geralmente, também há mais telemetria ou exaustão do serviço. Esses metadados podem estar contidos em outros serviços ou em logs. Essas informações podem ser necessárias para determinar se foi recebida e processada alguma chamada na primária que não estava presente no backup ou replicada nessa réplica específica. Pode ser necessário reproduzi-las ou adicioná-las ao backup antes que a restauração seja viável.  
   - Comparações do estado da réplica restante com a que está contida nos backups disponíveis. Se você estiver usando coleções confiáveis do Service Fabric, ferramentas e processos estarão disponíveis para fazer isso, descritos [neste artigo](service-fabric-reliable-services-backup-restore.md). A meta é verificar se o estado dentro da réplica é suficiente e também o que pode estar ausente no backup.
   - Após a comparação ser feita e, se necessário, a restauração ser concluída, o código de serviço deverá retornar true se alterações de estado tiverem sido feitas. Se a réplica tiver determinado que essa era a melhor cópia do estado e não tiver feito alterações, false será retornado. True indica que qualquer _outra_ réplica restante agora pode estar inconsistente com esta. Elas serão descartadas e recriadas desta réplica. False indica que nenhuma alteração de estado foi feita, de modo que as outras réplicas podem manter o que têm. 

É extremamente importante que os autores do serviço pratiquem cenários potenciais de falha e perda de dados antes que os serviços sejam implantados em produção. Para proteger-se contra a possibilidade de perda de dados, é importante periodicamente [fazer backup do estado](service-fabric-reliable-services-backup-restore.md) de qualquer um de seus serviços com estado para um repositório com redundância geográfica. Você também deve garantir que tenha capacidade de restaurá-lo. Como são feitos backups de vários serviços diferentes em momentos diferentes, você precisa garantir que, após uma restauração, seus serviços tenham uma exibição consistente uns dos outros. Por exemplo, considere uma situação em que um serviço gera e armazena um número e, então, o envia para outro serviço que também o armazena. Após uma restauração, talvez você descubra que o segundo serviço tem o número, mas o primeiro não tem, porque seu backup não incluiu essa operação.

Se você descobrir que as réplicas restantes são insuficientes para continuar delas em um cenário de perda de dados e se você não puder reconstruir o estado do serviço da telemetria ou exaustão, a frequência dos backups determinará o melhor RPO (objetivo de ponto de recuperação) possível. O Service Fabric fornece várias ferramentas para testar vários cenários de falha, incluindo a perda de quorum e de dados permanente que exige restauração de um backup. Esses cenários são incluídos como parte das ferramentas de teste do Service Fabric, gerenciadas pelo Serviço de Análise de Falha. Mais informações sobre essas ferramentas e padrões estão disponíveis [aqui](service-fabric-testability-overview.md). 

> [!NOTE]
> Os serviços do sistema também podem sofrer perda de quorum, e o impacto será específico para o serviço em questão. Por exemplo, a perda de quorum no serviço de nomenclatura afeta a resolução de nomes, ao passo que a perda de quorum no serviço gerenciador de failover bloqueia os failovers e a criação de novos serviços. Embora os serviços de sistema do Service Fabric sigam o mesmo padrão que seus serviços para o gerenciamento de estado, não é recomendável tentar movê-los da Perda de quorum para uma potencial perda de dados. Em vez disso, a recomendação é [buscar suporte](service-fabric-support.md) para determinar uma solução que seja destinada à sua situação específica.  Normalmente, é preferível simplesmente aguardar até que as réplicas desativadas voltem.
>

## <a name="availability-of-the-service-fabric-cluster"></a>Disponibilidade do cluster do Service Fabric
De modo geral, o cluster do Service Fabric em si é um ambiente altamente distribuído, sem pontos únicos de falha. Uma falha de qualquer um dos nós não causará problemas de disponibilidade ou confiabilidade para o cluster, principalmente porque os serviços de sistema do Service Fabric seguem as mesmas diretrizes fornecidas anteriormente: eles sempre são executados com três ou mais réplicas por padrão e os serviços de sistema sem estado são executados em todos os nós. As camadas subjacentes de detecção de falha e rede do Service Fabric são totalmente distribuídas. A maioria dos serviços de sistema podem ser recriados dos metadados no cluster ou sabem como ressincronizar seu estado de outros locais. A disponibilidade do cluster poderá ser comprometida se os serviços de sistema entrarem em situações de perda de quorum como as descritas acima. Nesses casos, talvez você não consiga executar determinadas operações no cluster, como iniciar uma atualização ou implantar novos serviços, mas o cluster em si ainda está ativo. Serviços que já estão em execução continuarão em execução nessas condições, a menos que eles exijam gravações nos serviços de sistema para continuar funcionando. Por exemplo, se o Gerenciador de Failover estiver em perda de quorum, todos os serviços continuarão sendo executados, mas os serviços que falharem não poderão ser reiniciados automaticamente, pois isso requer o envolvimento do Gerenciador de Failover. 

### <a name="failures-of-a-datacenter-or-azure-region"></a>Falhas de um data center ou região do Azure
Raramente, um data center físico pode ficar indisponível temporariamente devido à perda de energia ou de conectividade de rede. Nesses casos, seus clusters e serviços do Service Fabric nesse data center ou região do Azure ficarão indisponíveis. No entanto, _os dados ficam preservados_. Para os clusters em execução no Azure, você pode exibir as atualizações sobre interrupções na [Página de status do Azure][azure-status-dashboard]. Na hipótese altamente improvável de um data center físico ser destruído parcial ou completamente, os clusters do Service Fabric hospedados nele ou os serviços dentro dele poderão ser perdidos. Isso inclui qualquer estado que não esteja salvo em backup fora desse data center ou região.

Há duas estratégias diferentes para sobreviver a uma falha prolongada ou permanente de um único data center ou região. 

1. Executar clusters separados do Service Fabric em várias dessas regiões e utilizar um mecanismo para failover e failback entre esses ambientes. Esse tipo de modelo ativo-ativo ou ativo-passivo multi-cluster requer código adicional de gerenciamento e operação. Isso também requer a coordenação de backups dos serviços em um data center ou região para que eles fiquem disponíveis em outros data centers ou regiões quando um deles falhar. 
2. Executar um único cluster do Service Fabric que abrange vários data centers ou regiões. A configuração mínima com suporte para isso é de três data centers ou regiões. O número recomendado de regiões ou data centers é cinco. Isso requer uma topologia de clusters mais complexa. No entanto, o benefício desse modelo é que a falha de um data center ou região é convertida de um desastre em uma falha normal. Essas falhas podem ser tratadas pelos mecanismos que funcionam para clusters dentro de uma única região. Domínios de falha, domínios de atualização e regras de posicionamento do Service Fabric garantem que as cargas de trabalho sejam distribuídas para que tolerem falhas normais. Para obter mais informações sobre políticas que podem ajudar a operar serviços nesse tipo de cluster, leia sobre as [políticas de posicionamento](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)

### <a name="random-failures-leading-to-cluster-failures"></a>Falhas aleatórias que causam falhas de cluster
O Service Fabric tem o conceito de Nós de Semente. Trata-se de nós que mantêm a disponibilidade do cluster subjacente. Esses nós ajudam a garantir que o cluster permaneça ativo estabelecendo concessões com outros nós e servindo como agentes de desempate durante determinados tipos de falhas de rede. Se falhas aleatórias removerem a maioria de nós de semente no cluster e eles não ficam novamente, o anel de federação de cluster recolhe conforme você perdeu o quorum de nós de semente e o cluster falhar. No Azure, o provedor de recursos de malha do serviço gerencia configurações de cluster do Service Fabric e por padrão distribui nós de propagação em domínios de falha e atualização do tipo de nó primário. Se o nodetype primário está marcado como durabilidade ouro ou Prata, quando você remove um nó de semente, colocação em escala no seu nodetype primário ou removendo manualmente um nó de propagação, o cluster tentará promover outro nó de semeação não o nodetype primário disponível capacidade e falharão se você tem capacidade menor disponível que requer do seu nível de confiabilidade do cluster para o tipo de nó primário.

Em clusters independentes do Service Fabric e no Azure, o "Tipo de nó primário" é aquela que executa as propagações. Ao definir um tipo de nó primário, o Service Fabric aproveitará automaticamente o número de nós fornecidos, criando até nove nós iniciais e 7 réplicas de cada um dos serviços do sistema. Se um conjunto de falhas aleatórias desativar a maior parte dessas réplicas do serviço de sistema ao mesmo tempo, os serviços de sistema entrarão em perda de quorum, conforme descrito acima. Se a maioria desses nós de semente for perdida, o cluster será desligado logo em seguida.

## <a name="next-steps"></a>Próximas etapas
- Saiba como simular várias falhas usando a [estrutura de capacidade de teste](service-fabric-testability-overview.md)
- Leia outros recursos de recuperação de desastres e alta disponibilidade. A Microsoft publicou várias orientações sobre estes tópicos. Embora alguns desses documentos mencionem técnicas específicas para uso em outros produtos, eles contêm várias práticas recomendadas gerais que também se aplicam ao contexto do Service Fabric:
  - [Lista de verificação de disponibilidade](../best-practices-availability-checklist.md)
  - [Executando a análise de recuperação de desastre](../sql-database/sql-database-disaster-recovery-drills.md)
  - [Recuperação de desastre e alta disponibilidade para aplicativos do Azure][dr-ha-guide]
- Saiba mais sobre as [opções de suporte do Service Fabric](service-fabric-support.md)


<!-- External links -->

[repair-partition-ps]: https://msdn.microsoft.com/library/mt163522.aspx
[azure-status-dashboard]:https://azure.microsoft.com/status/
[azure-regions]: https://azure.microsoft.com/regions/
[dr-ha-guide]: https://msdn.microsoft.com/library/azure/dn251004.aspx


<!-- Images -->

[sfx-cluster-map]: ./media/service-fabric-disaster-recovery/sfx-clustermap.png
