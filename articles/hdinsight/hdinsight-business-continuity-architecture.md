---
title: Arquiteturas de continuidade de negócios do Azure HDInsight
description: Este artigo aborda as diferentes arquiteturas de continuidade de negócios possíveis para o HDInsight
keywords: alta disponibilidade hadoop
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: f74309370f1489714013344116e7feb9551fbfd5
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98933442"
---
# <a name="azure-hdinsight-business-continuity-architectures"></a>Arquiteturas de continuidade de negócios do Azure HDInsight

Este artigo fornece alguns exemplos de arquiteturas de continuidade de negócios que você pode considerar para o Azure HDInsight. A tolerância para reduzir a funcionalidade durante um desastre é uma decisão de negócios que varia de um aplicativo para o outro. Pode ser aceitável que alguns aplicativos estejam indisponíveis ou estejam parcialmente disponíveis com funcionalidade reduzida ou processamento atrasado por um período. Para outros aplicativos, qualquer funcionalidade reduzida poderia ser inaceitável.

> [!NOTE]
> As arquiteturas apresentadas neste artigo não são exaustivamente. Você deve projetar suas próprias arquiteturas exclusivas depois de ter feito as determinações objetivas em relação à continuidade dos negócios, à complexidade operacional e ao custo de propriedade esperados.

## <a name="apache-hive-and-interactive-query"></a>Apache Hive e consulta interativa

A [replicação do hive v2](https://cwiki.apache.org/confluence/display/Hive/HiveReplicationv2Development#HiveReplicationv2Development-REPLSTATUS) é recomendada para continuidade de negócios no HDInsight Hive e em clusters de consulta interativa. As seções persistentes de um cluster do hive autônomo que precisam ser replicadas são a camada de armazenamento e a metastore do Hive. Clusters do hive em um cenário de vários usuários com Enterprise Security Package precisam de Azure Active Directory Domain Services e metastore do Ranger.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/hive-interactive-query.png" alt-text="Arquitetura de consulta interativa e Hive":::

A replicação baseada em evento do hive é configurada entre os clusters primário e secundário. Isso consiste em duas fases distintas, inicialização e execuções incrementais:

* A inicialização Replica todo o depósito do hive, incluindo as informações de metastore do Hive do primário para o secundário.

* As execuções incrementais são automatizadas no cluster primário e os eventos gerados durante as execuções incrementais são reproduzidos no cluster secundário. O cluster secundário é atualizado com os eventos gerados a partir do cluster primário, garantindo que o cluster secundário seja consistente com os eventos do cluster primário após a execução da replicação.

O cluster secundário é necessário apenas no momento da replicação para executar a cópia distribuída, `DistCp` mas o armazenamento e os metastores precisam ser persistentes. Você pode optar por criar um cluster secundário com script sob demanda antes da replicação, executar o script de replicação nele e, em seguida, subdividi-lo após a replicação bem-sucedida.

O cluster secundário geralmente é somente leitura. Você pode fazer a leitura/gravação do cluster secundário, mas isso adiciona complexidade adicional que envolve a replicação das alterações do cluster secundário para o cluster primário.

### <a name="hive-event-based-replication-rpo--rto"></a>RPO de replicação baseada em evento do hive & RTO

* RPO: a perda de dados é limitada ao último evento de replicação incremental com êxito do primário para o secundário.

* RTO: o tempo entre a falha e a retomada das transações upstream e downstream com o secundário.

### <a name="apache-hive-and-interactive-query-architectures"></a>Arquiteturas de consulta Apache Hive e interativas

#### <a name="hive-active-primary-with-on-demand-secondary"></a>Primário ativo de Hive com secundário sob demanda

Em um *primário ativo com a arquitetura secundária sob demanda* , os aplicativos gravam na região primária ativa, enquanto nenhum cluster é provisionado na região secundária durante as operações normais. O metastore do SQL e o armazenamento na região secundária são persistentes, enquanto o cluster HDInsight é armazenado em script e implantado sob demanda somente antes da execução da replicação do hive agendado.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/active-primary-on-demand-secondary.png" alt-text="primário ativo com secundário sob demanda":::

#### <a name="hive-active-primary-with-standby-secondary"></a>Hive ativo primário com standby secundário

Em um *primário ativo com o secundário em espera*, os aplicativos gravam na região primária ativa enquanto um cluster secundário em espera reduzido no modo somente leitura é executado durante as operações normais. Durante as operações normais, você pode optar por descarregar operações de leitura específicas de região para o secundário.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/active-primary-standby-secondary.png" alt-text="primário ativo com o secundário em espera":::

Para obter mais informações sobre a replicação do hive e exemplos de código, consulte [Apache Hive replicação em clusters do Azure HDInsight](./interactive-query/apache-hive-replication.md)

## <a name="apache-spark"></a>Apache Spark

As cargas de trabalho do Spark podem ou não envolver um componente Hive. Para habilitar as cargas de trabalho do Spark SQL para ler e gravar dados do hive, os clusters HDInsight Spark compartilham metarepositórios personalizados do hive de clusters de consulta de Hive/Interactive na mesma região. Nesses cenários, a replicação entre regiões de cargas de trabalho do Spark também deve acompanhar a replicação de metaarmazenamentos do hive e do armazenamento. Os cenários de failover nesta seção se aplicam a ambos:

* [Spark SQL em tabelas Acid usando a instalação do conector de depósito do hive (HWC)](./interactive-query/apache-hive-warehouse-connector.md) usando um cluster de consulta interativa do HDInsight.
* Carga de trabalho do Spark SQL em tabelas não ACID usando um cluster Hadoop do HDInsight.

Para cenários em que o Spark funciona no modo autônomo, os dados estruturados e os jars do Spark armazenados (para trabalhos Livy) precisam ser replicados da região primária para a região secundária regularmente usando o Azure Data Factory `DistCP` .

Recomendamos que você use sistemas de controle de versão para armazenar bibliotecas e notebooks Spark onde eles podem ser facilmente implantados em clusters primários ou secundários. Verifique se as soluções baseadas em notebooks e não baseadas em blocos de anotações estão preparadas para carregar as montagens de dados corretas no espaço de trabalho primário ou secundário.

Se houver bibliotecas específicas do cliente que estão além do que o HDInsight fornece nativamente, elas deverão ser rastreadas e carregadas periodicamente no cluster secundário em espera.  

### <a name="apache-spark-replication-rpo--rto"></a>RPO de Apache Spark & RTO de replicação

* RPO: a perda de dados é limitada à última replicação incremental com êxito (Spark e Hive) do primário para o secundário.

* RTO: o tempo entre a falha e a retomada das transações upstream e downstream com o secundário.

### <a name="apache-spark-architectures"></a>Arquiteturas de Apache Spark

#### <a name="spark-active-primary-with-on-demand-secondary"></a>Primário do Spark ativo com secundário sob demanda

Os aplicativos lêem e gravam em clusters Spark e Hive na região primária, enquanto nenhum cluster é provisionado na região secundária durante operações normais. O metastore do SQL, o armazenamento do hive e o armazenamento do Spark são persistentes na região secundária. Os clusters Spark e Hive são incluídos no script e implantados sob demanda. A replicação do hive é usada para replicar o armazenamento do hive e metastores do hive, enquanto Azure Data Factory `DistCP` pode ser usado para copiar o armazenamento do Spark autônomo. Os clusters do hive precisam ser implantados antes de cada execução de replicação do hive por causa da computação de dependência `DistCp` .

:::image type="content" source="./media/hdinsight-business-continuity-architecture/active-primary-on-demand-secondary-spark.png" alt-text="primário ativo com arquitetura de Apache Spark secundária sob demanda":::

#### <a name="spark-active-primary-with-standby-secondary"></a>Primário do Spark ativo com standby secundário

Os aplicativos lêem e gravam em clusters Spark e Hive na região primária enquanto os clusters do hive e do Spark em espera reduzidos no modo somente leitura são executados na região secundária durante as operações normais. Durante as operações normais, você pode optar por descarregar as operações de leitura do hive e do Spark específicas da região para o secundário.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/active-primary-standby-secondary-spark.png" alt-text="Apache Spark secundárias de espera primária ativas ":::

## <a name="apache-hbase"></a>HBase no Apache

A exportação do HBase e a replicação do HBase são maneiras comuns de habilitar a continuidade dos negócios entre clusters HBase do HDInsight.

A exportação do HBase é um processo de replicação em lote que usa o utilitário de exportação do HBase para exportar tabelas do cluster do HBase primário para seu armazenamento subjacente Azure Data Lake Storage Gen 2. Os dados exportados podem ser acessados por meio do cluster HBase secundário e importados para tabelas que devem estar no secundário. Enquanto a exportação do HBase oferece granularidade de nível de tabela, em situações de atualização incremental, o mecanismo de automação de exportação controla o intervalo de linhas incrementais a serem incluídas em cada execução. Para obter mais informações, consulte [backup e replicação do HBase do HDInsight](./hbase/apache-hbase-backup-replication.md#export-then-import).

A replicação do HBase usa replicação quase em tempo real entre clusters HBase de uma maneira totalmente automatizada. A replicação é feita no nível da tabela. Qualquer tabela ou tabela específica pode ser direcionada para replicação. A replicação do HBase é eventualmente consistente, o que significa que edições recentes em uma tabela na região primária podem não estar disponíveis para todos os secundários imediatamente. Os secundários são garantidos para que eventualmente se tornem consistentes com o primário. A replicação do HBase pode ser configurada entre dois ou mais clusters do HBase do HDInsight se:

* Primário e secundário estão na mesma rede virtual.
* Primário e secundário estão em diferentes VNets emparelhados na mesma região.
* Primário e secundário estão em diferentes VNets emparelhadas em regiões diferentes.

Para obter mais informações, consulte [Configurar a replicação de cluster do Apache HBase em redes virtuais do Azure](./hbase/apache-hbase-replication.md).

Há algumas outras maneiras de executar backups de clusters HBase, como [copiar a pasta HBase](./hbase/apache-hbase-backup-replication.md#copy-the-hbase-folder), [copiar tabelas](./hbase/apache-hbase-backup-replication.md#copy-tables) e [instantâneos](./hbase/apache-hbase-backup-replication.md#snapshots).

### <a name="hbase-rpo--rto"></a>RPO & RTO de HBase

#### <a name="hbase-export"></a>Exportação do HBase

* RPO: a perda de dados é limitada à última importação incremental com êxito do lote pelo secundário a partir do primário.
* RTO: o tempo entre a falha do primário e a continuação de operações de e/s no secundário.

#### <a name="hbase-replication"></a>Replicação do HBase

* RPO: a perda de dados é limitada à última remessa de WalEdit recebida no secundário.
* RTO: o tempo entre a falha do primário e a continuação de operações de e/s no secundário.

### <a name="hbase-architectures"></a>Arquiteturas HBase

A replicação do HBase pode ser configurada em três modos: líder-de-acompanhamento, Leader-Leader e cíclica.

#### <a name="hbase-replication--leader--follower-model"></a>Replicação do HBase: líder – modelo de acompanhamento

Nessa configuração entre regiões, a replicação é unidirecional da região primária para a região secundária. Todas as tabelas ou tabelas específicas no primário podem ser identificadas para replicação unidirecional. Durante as operações normais, o cluster secundário pode ser usado para atender a solicitações de leitura em sua própria região.

O cluster secundário funciona como um cluster HBase normal que pode hospedar suas próprias tabelas e pode servir leituras e gravações de aplicativos regionais. No entanto, as gravações nas tabelas ou tabelas replicadas nativas para secundárias não são replicadas de volta para o primário.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/hbase-leader-follower.png" alt-text="Modelo de acompanhamento de líder do HBase":::

#### <a name="hbase-replication--leader--leader-model"></a>Replicação do HBase: líder – modelo de líder

Essa configuração entre regiões é muito semelhante à configuração unidirecional, exceto que a replicação ocorre bidirecionalmente entre a região primária e a região secundária. Os aplicativos podem usar ambos os clusters em modo de leitura – os modos de gravação e as atualizações são trocas de forma assíncrona entre eles.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/hbase-leader-leader.png" alt-text="Modelo líder de líder do HBase":::

#### <a name="hbase-replication-multi-region-or-cyclic"></a>Replicação do HBase: várias regiões ou cíclicas

O modelo de replicação de várias regiões/cíclicas é uma extensão da replicação do HBase e pode ser usado para criar uma arquitetura do HBase globalmente redundante com vários aplicativos que lêem e gravam em clusters HBase específicos da região. Os clusters podem ser configurados em várias combinações de líder/líder ou líder/acompanhamento, dependendo dos requisitos de negócios.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/hbase-cyclic.png" alt-text="Modelo cíclico do HBase":::

## <a name="apache-kafka"></a>Apache Kafka

Para habilitar a disponibilidade entre regiões, o HDInsight 4,0 dá suporte a Kafka MirrorMaker, que pode ser usado para manter uma réplica secundária do cluster Kafka primário em uma região diferente. MirrorMaker atua como um par consumidor-produtor de alto nível, consome de um tópico específico no cluster primário e produz a um tópico com o mesmo nome no secundário. A replicação entre clusters para recuperação de desastre de alta disponibilidade usando MirrorMaker vem com a suposição de que produtores e consumidores precisam fazer failover para o cluster de réplica. Para obter mais informações, consulte [usar MirrorMaker para replicar tópicos de Apache Kafka com o Kafka no HDInsight](./kafka/apache-kafka-mirroring.md)

Dependendo do tempo de vida do tópico quando a replicação foi iniciada, a replicação do tópico do MirrorMaker pode levar a deslocamentos diferentes entre os tópicos de origem e de réplica. Os clusters HDInsight Kafka também dão suporte à replicação de partição de tópico, que é um recurso de alta disponibilidade no nível de cluster individual.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/kafka-replication.png" alt-text="Replicação de Apache Kafka":::

### <a name="apache-kafka-architectures"></a>Arquiteturas de Apache Kafka

#### <a name="kafka-replication-active--passive"></a>Replicação do Kafka: ativo – passivo

Active-Passive configuração habilita o espelhamento unidirecional assíncrono de ativo para passivo. Produtores e consumidores precisam estar cientes da existência de um cluster ativo e passivo e devem estar prontos para failover para o passivo, caso o ativo falhe. Abaixo estão algumas vantagens e desvantagens da instalação de Active-Passive.

Vantagens:

* A latência de rede entre clusters não afeta o desempenho do cluster ativo.
* Simplicidade da Replicação unidirecional.

As desvantagens:

* O cluster passivo pode permanecer subutilizado.
* Complexidade de design na incorporação do reconhecimento de failover em produtores de aplicativos e consumidores.
* Possível perda de dados durante a falha do cluster ativo.
* Consistência eventual entre os tópicos entre os clusters ativos e passivos.
* Failbacks para primário podem levar à inconsistência de mensagem nos tópicos.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/kafka-active-passive.png" alt-text="Apache Kafka modelo passivo ativo":::

#### <a name="kafka-replication-active--active"></a>Replicação do Kafka: ativo – ativo

Active-Active configurada envolve dois clusters de Kafka do HDInsight emparelhados com rede virtual com replicação assíncrona bidirecional com MirrorMaker. Nesse design, as mensagens consumidas pelos consumidores no primário também são disponibilizadas para os consumidores no secundário e vice-versa. Abaixo estão algumas vantagens e desvantagens da instalação de Active-Active.

Vantagens:

* Devido a seu estado duplicado, failovers e failbacks são mais fáceis de executar.

As desvantagens:

* A configuração, o gerenciamento e o monitoramento são mais complexos do que os ativos-passivos.
* O problema de replicação circular precisa ser resolvido.  
* A replicação bidirecional leva a custos de egresso de dados regionais mais altos.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/kafka-active-active.png" alt-text="Modelo ativo ativo Apache Kafka":::

## <a name="hdinsight-enterprise-security-package"></a>Enterprise Security Package HDInsight

Essa configuração é usada para habilitar a funcionalidade de vários usuários no primário e no secundário, bem como [conjuntos de réplicas do Azure AD DS](../active-directory-domain-services/tutorial-create-replica-set.md) para garantir que os usuários possam se autenticar em ambos os clusters. Durante as operações normais, as políticas de Ranger precisam ser configuradas no secundário para garantir que os usuários sejam restritos a operações de leitura. A arquitetura abaixo explica como um Hive habilitado para ESP ativo primário – configuração secundária em espera pode parecer.

Replicação de metastore do Ranger:

O metastore do Ranger é usado para armazenar de forma persistente e fornecer políticas de Ranger para controlar a autorização de dados. Recomendamos que você mantenha as políticas independentes do Ranger no primário e no secundário e mantenha o secundário como uma réplica de leitura.
  
Se o requisito for manter as políticas de Ranger sincronizadas entre o primário e o secundário, use a [importação/exportação do Ranger](https://cwiki.apache.org/confluence/display/RANGER/User+Guide+For+Import-Export) para fazer backup periódico e importar políticas do Ranger do primário para o secundário.

A replicação de políticas de Ranger entre primária e secundária pode fazer com que o secundário se torne habilitado para gravação, o que pode levar a gravações involuntárias no secundário, levando a inconsistências de dados.  

:::image type="content" source="./media/hdinsight-business-continuity-architecture/hdinsight-enterprise-security-package.png" alt-text="Arquitetura de Enterprise Security Package do HDInsight":::

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre os itens discutidos neste artigo, consulte:

* [Continuidade de negócios do Azure HDInsight](./hdinsight-business-continuity.md)
* [Estudo de caso da arquitetura de solução de alta disponibilidade do Azure HDInsight](./hdinsight-high-availability-case-study.md)
* [Saiba mais sobre o Apache Hive e o HiveQL no Azure HDInsight?](./hadoop/hdinsight-use-hive.md)