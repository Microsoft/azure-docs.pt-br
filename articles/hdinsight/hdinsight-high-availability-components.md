---
title: Componentes de alta disponibilidade no Azure HDInsight
description: Visão geral dos vários componentes de alta disponibilidade usados pelos clusters HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: 93d2317c85f93ce8a22f2d434fbc081a88265a74
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104863710"
---
# <a name="high-availability-services-supported-by-azure-hdinsight"></a>Serviços de alta disponibilidade com suporte do Azure HDInsight

Para fornecer a você os níveis ideais de disponibilidade para seus componentes de análise, o HDInsight foi desenvolvido com uma arquitetura exclusiva para garantir alta disponibilidade (HA) de serviços críticos. Alguns componentes dessa arquitetura foram desenvolvidos pela Microsoft para fornecer failover automático. Outros componentes são componentes padrão do Apache que são implantados para dar suporte a serviços específicos. Este artigo explica a arquitetura do modelo de serviço de alta disponibilidade no HDInsight, como o HDInsight dá suporte a failover para serviços de HA e práticas recomendadas para recuperação de outras interrupções de serviço.

> [!NOTE]
> Este artigo contém referências ao termo *subordinado*, um termo que a Microsoft não usa mais. Quando o termo for removido do software, também o removeremos deste artigo.

## <a name="high-availability-infrastructure"></a>Infraestrutura de alta disponibilidade

O HDInsight fornece uma infraestrutura personalizada para garantir que quatro serviços principais sejam de alta disponibilidade com recursos de failover automático:

- Servidor Apache Ambari
- Linha do Tempo do Aplicativo Server para Apache YARN
- Servidor de histórico de trabalho para o MapReduce do Hadoop
- Apache Livy

Essa infra-estrutura consiste em vários serviços e componentes de software, alguns dos quais são projetados pela Microsoft. Os seguintes componentes são exclusivos para a plataforma HDInsight:

- Controlador de failover escravo
- Controlador de failover mestre
- Serviço de alta disponibilidade subordinado
- Serviço mestre de alta disponibilidade

:::image type="content" source="./media/hdinsight-high-availability-components/high-availability-architecture.png" alt-text="infraestrutura de alta disponibilidade" border="false":::

Também há outros serviços de alta disponibilidade, que têm suporte de componentes de confiabilidade do Apache de código aberto. Esses componentes também estão presentes em clusters HDInsight:

- Sistema de arquivos do Hadoop (HDFS) NameNode
- ResourceManager YARN
- HBase Master

As seções a seguir fornecerão mais detalhes sobre como esses serviços funcionam juntos.

## <a name="hdinsight-high-availability-services"></a>Serviços de alta disponibilidade do HDInsight

A Microsoft fornece suporte para os quatro serviços Apache na tabela a seguir em clusters HDInsight. Para diferenciá-los dos serviços de alta disponibilidade com suporte dos componentes do Apache, eles são chamados de *serviços de ha do HDInsight*.

| Serviço | Nós de cluster | Tipos de cluster | Finalidade |
|---|---|---|---|
| Servidor Apache Ambari| Cabeçalho ativo | Tudo | Monitora e gerencia o cluster.|
| Linha do Tempo do Aplicativo Server para Apache YARN | Cabeçalho ativo | Todos, exceto Kafka | Mantém informações de depuração sobre trabalhos do YARN em execução no cluster.|
| Servidor de histórico de trabalho para o MapReduce do Hadoop | Cabeçalho ativo | Todos, exceto Kafka | Mantém dados de depuração para trabalhos MapReduce.|
| Apache Livy | Cabeçalho ativo | Spark | Permite uma interação fácil com um cluster Spark em uma interface REST |

>[!Note]
> Os clusters do Enterprise Security Package do HDInsight (ESP) fornecem atualmente a alta disponibilidade do servidor Ambari. Linha do Tempo do Aplicativo Server, servidor de histórico de trabalho e Livy estão todos em execução apenas em headnode0 e não fazem failover para headnode1 quando Ambari failsover. O banco de dados de linha do tempo do aplicativo também está em headnode0 e não em Ambari SQL Server.

### <a name="architecture"></a>Arquitetura

Cada cluster HDInsight tem dois cabeçalho nos modos ativo e em espera, respectivamente. Os serviços de alta disponibilidade do HDInsight são executados somente no cabeçalho. Esses serviços devem estar sempre em execução no cabeçalho ativo e interrompidos e colocados no modo de manutenção no cabeçalho em espera.

Para manter os Estados corretos dos serviços de HA e fornecer um failover rápido, o HDInsight utiliza Apache ZooKeeper, que é um serviço de coordenação para aplicativos distribuídos, para realizar eleição cabeçalho ativa. O HDInsight também provisiona alguns processos Java em segundo plano, que coordenam o procedimento de failover para os serviços de HA do HDInsight. Esses serviços são os seguintes: o controlador de failover mestre, o controlador de failover escravo, o *Master-ha-Service* e o *slave-ha-Service*.

### <a name="apache-zookeeper"></a>Apache ZooKeeper

Apache ZooKeeper é um serviço de coordenação de alto desempenho para aplicativos distribuídos. Em produção, o ZooKeeper geralmente é executado no modo replicado, em que um grupo replicado de servidores ZooKeeper formam um quorum. Cada cluster HDInsight tem três nós ZooKeeper que permitem que três servidores ZooKeeper formem um quorum. O HDInsight tem dois quorums ZooKeeper executados em paralelo uns com os outros. Um quorum decide a cabeçalho ativa em um cluster no qual os serviços de HA do HDInsight devem ser executados. Outro quorum é usado para coordenar os serviços de alta disponibilidade fornecidos pelo Apache, conforme detalhado nas seções posteriores.

### <a name="slave-failover-controller"></a>Controlador de failover escravo

O controlador de failover escravo é executado em cada nó em um cluster HDInsight. Esse controlador é responsável por iniciar o agente Ambari e o *slave-ha-Service* em cada nó. Ele consulta periodicamente o primeiro quorum ZooKeeper sobre o cabeçalho ativo. Quando as cabeçalho ativa e em espera são alteradas, o controlador de failover escravo executa o seguinte:

1. Atualiza o arquivo de configuração do host.
1. Reinicia o agente do Ambari.

O *serviço escravo-ha-Service* é responsável por parar os serviços de ha do HDInsight (exceto o servidor Ambari) no cabeçalho em espera.

### <a name="master-failover-controller"></a>Controlador de failover mestre

Um controlador de failover mestre é executado em ambos os cabeçalho. Ambos os controladores de failover mestre se comunicam com o primeiro quorum ZooKeeper para indicar o cabeçalho em que estão sendo executados como o cabeçalho ativo.

Por exemplo, se o controlador de failover mestre no cabeçalho 0 vence a eleição, as seguintes alterações ocorrem:

1. Cabeçalho 0 se torna ativo.
1. O controlador de failover mestre inicia o Ambari Server e o *Master-ha-Service* no cabeçalho 0.
1. O outro controlador de failover mestre interrompe o servidor Ambari e o *Master-ha-Service* no cabeçalho 1.

O Master-ha-Service é executado somente no cabeçalho ativo, ele interrompe os serviços de HA do HDInsight (exceto Ambari Server) no cabeçalho em espera e os inicia no cabeçalho ativo.

### <a name="the-failover-process"></a>O processo de failover

:::image type="content" source="./media/hdinsight-high-availability-components/failover-steps.png" alt-text="processo de failover" border="false":::

Um monitor de integridade é executado em cada cabeçalho junto com o controlador de failover mestre para enviar notificações de pulsação para o quorum Zookeeper. O cabeçalho é considerado um serviço de HA nesse cenário. O Health Monitor verifica se cada serviço de alta disponibilidade está íntegro e se está pronto para ingressar na eleição de liderança. Em caso afirmativo, esse cabeçalho competirá na eleição. Caso contrário, ele encerrará a eleição até que ela se torne pronta novamente.

Se o cabeçalho em espera atingir a liderança e se tornar ativo (como no caso de uma falha com o nó ativo anterior), seu controlador de failover mestre iniciará todos os serviços do HDInsight HA nele. O controlador de failover mestre também irá parar esses serviços no outro cabeçalho.

Para falhas do serviço de HA do HDInsight, como um serviço inoperante ou não íntegro, o controlador de failover mestre deve reiniciar automaticamente ou parar os serviços de acordo com o status de cabeçalho. Os usuários não devem iniciar manualmente os serviços do HDInsight HA em ambos os nós de cabeça. Em vez disso, permita o failover automático ou manual para ajudar a recuperar o serviço.

### <a name="inadvertent-manual-intervention"></a>Intervenção manual inadvertida

Os serviços do HDInsight HA só devem ser executados no cabeçalho ativo e serão reiniciados automaticamente quando necessário. Como os serviços de HA individuais não têm seu próprio monitor de integridade, o failover não pode ser disparado no nível do serviço individual. O failover é garantido no nível do nó e não no nível de serviço.

### <a name="some-known-issues"></a>Alguns problemas conhecidos

- Ao iniciar manualmente um serviço de HA no cabeçalho em espera, ele não será interrompido até que o próximo failover ocorra. Quando os serviços de alta disponibilidade são executados em ambos os cabeçalho, alguns problemas potenciais incluem: a interface do usuário do Ambari é inacessível, o Ambari gera erros, YARN, Spark e trabalhos de Oozie podem ficar presos.

- Quando um serviço de HA no Active cabeçalho para, ele não é reiniciado até que o próximo failover ocorra ou o controlador de failover mestre/Master-ha-Service seja reiniciado. Quando um ou mais serviços de HA param no cabeçalho ativo, especialmente quando o servidor Ambari é interrompido, a interface do usuário do Ambari é inacessível, outros problemas potenciais incluem falhas de trabalhos YARN, Spark e Oozie.

## <a name="apache-high-availability-services"></a>Serviços de alta disponibilidade do Apache

O Apache fornece alta disponibilidade para HDFS NameNode, YARN ResourceManager e HBase Master, que também estão disponíveis em clusters HDInsight. Ao contrário dos serviços de HA do HDInsight, eles têm suporte em clusters ESP. Os serviços Apache HA se comunicam com o segundo quorum ZooKeeper (descrito na seção acima) para eleger os Estados ativo/em espera e conduzir o failover automático. As seções a seguir detalham como esses serviços funcionam.

### <a name="hadoop-distributed-file-system-hdfs-namenode"></a>Sistema de Arquivos Distribuído do Hadoop (HDFS) NameNode

Os clusters HDInsight baseados em Apache Hadoop 2,0 ou superior fornecem alta disponibilidade NameNode. Há dois NameNodes em execução no cabeçalho, que são configurados para failover automático. O NameNodes usa o *ZKFailoverController* para se comunicar com Zookeeper para eleger o status ativo/em espera. O *ZKFailoverController* é executado em ambos os cabeçalho e funciona da mesma maneira que o controlador de failover mestre acima.

O segundo quorum Zookeeper é independente do primeiro quorum, portanto, o Active NameNode pode não ser executado no cabeçalho ativo. Quando o NameNode ativo está inativo ou não íntegro, o NameNode em espera ganha a eleição e se torna ativo.

### <a name="yarn-resourcemanager"></a>ResourceManager YARN

Os clusters HDInsight com base no Apache Hadoop 2,4 ou superior oferecem suporte à alta disponibilidade do ResourceManager YARN. Há dois ResourceManagers, RM1 e RM2, em execução no cabeçalho 0 e no cabeçalho 1, respectivamente. Como NameNode, o ResourceManager YARN também é configurado para failover automático. Outro ResourceManager é automaticamente optado por estar ativo quando o ResourceManager ativo atual fica inativo ou sem resposta.

O ResourceManager YARN usa seu *ActiveStandbyElector* incorporado como um detector de falha e elector de líder. Diferentemente do HDFS NameNode, o ResourceManager YARN não precisa de um daemon ZKFC separado. O ResourceManager ativo grava seus Estados no Apache Zookeeper.

A alta disponibilidade do ResourceManager YARN é independente do NameNode e de outros serviços de HA do HDInsight. O ResourceManager ativo pode não ser executado no cabeçalho ativo ou no cabeçalho em que o NameNode ativo está em execução. Para obter mais informações sobre a alta disponibilidade de ResourceManager do YARN, consulte [alta disponibilidade de ResourceManager](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/ResourceManagerHA.html).

### <a name="hbase-master"></a>HBase Master

Os clusters do HBase do HDInsight dão suporte a HBase Master alta disponibilidade. Ao contrário de outros serviços de alta disponibilidade, que são executados em cabeçalho, os mestres do HBase são executados nos três nós do Zookeeper, em que um deles é o mestre ativo e os outros dois são em espera. Como NameNode, a HBase Master coordena com o Apache Zookeeper para eleição de líder e realiza o failover automático quando o mestre ativo atual apresenta problemas. Há apenas um HBase Master ativo a qualquer momento.

## <a name="next-steps"></a>Próximas etapas

- [Disponibilidade e confiabilidade dos clusters Apache Hadoop em HDInsight](./hdinsight-business-continuity.md)
- [Arquitetura de rede virtual do Azure HDInsight](hdinsight-virtual-network-architecture.md)