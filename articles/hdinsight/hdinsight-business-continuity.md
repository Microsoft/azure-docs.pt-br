---
title: Continuidade de negócios do Azure HDInsight
description: Este artigo fornece uma visão geral das práticas recomendadas, disponibilidade de região única e opções de otimização para o planejamento da continuidade de negócios do Azure HDInsight.
keywords: alta disponibilidade hadoop
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/08/2020
ms.openlocfilehash: 74f8bdd26e000b89bfae84102077c241f85abf7e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98933310"
---
# <a name="azure-hdinsight-business-continuity"></a>Continuidade de negócios do Azure HDInsight

Os clusters do Azure HDInsight dependem de muitos serviços do Azure, como armazenamento, bancos de dados, Active Directory, Active Directory Domain Services, rede e Key Vault. Um aplicativo de análise bem projetado, altamente disponível e tolerante a falhas deve ser projetado com redundância suficiente para resistir a interrupções regionais ou locais em um ou mais desses serviços. Este artigo fornece uma visão geral das práticas recomendadas, disponibilidade de região única e opções de otimização para o planejamento de continuidade de negócios.

## <a name="general-best-practices"></a>Práticas recomendadas gerais

Esta seção aborda algumas das práticas recomendadas a serem consideradas durante o planejamento da continuidade dos negócios.

* Determine a funcionalidade de negócios mínima que será necessária se houver um desastre e por quê. Por exemplo, avalie se você precisa de recursos de failover para a camada de transformação de dados (mostrada em amarelo) *e* a camada de serviço de dados (mostrada em azul) ou se você só precisa de failover para a camada de serviços de dados.

   :::image type="content" source="media/hdinsight-business-continuity/data-layers.png" alt-text="transformação de dados e camadas de serviço de dados":::

* Segmente seus clusters com base na carga de trabalho, no ciclo de vida de desenvolvimento e nos departamentos. Ter mais clusters reduz as chances de uma única falha grande afetando vários processos de negócios diferentes.

* Torne suas regiões secundárias somente leitura. As regiões de failover com recursos de leitura e gravação podem levar a arquiteturas complexas.

* Os clusters transitórios são mais fáceis de gerenciar quando há um desastre. Projete suas cargas de trabalho de forma que os clusters possam ser alternados e nenhum Estado seja mantido em clusters.

* Muitas vezes, as cargas de trabalho permanecem inacabadas se houver um desastre e precisarem ser reiniciadas na nova região. Projete suas cargas de trabalho para serem idempotentes por natureza.

* Use a automação durante implantações de cluster e certifique-se de que as definições de configuração de cluster tenham o script o máximo possível para garantir uma implantação rápida e totalmente automatizada se houver um desastre.

* Use as ferramentas de monitoramento do Azure no HDInsight para detectar o comportamento anormal no cluster e definir as notificações de alerta correspondentes. Você pode implantar as soluções de gerenciamento específicas do cluster HDInsight pré-configuradas que coletam métricas de desempenho importantes do tipo de cluster específico. Para obter mais informações, consulte [monitoramento do Azure para HDInsight](./hdinsight-hadoop-oms-log-analytics-tutorial.md).  

* Assine alertas de integridade do Azure para ser notificado sobre problemas de serviço, manutenção planejada, recomendações de integridade e segurança para uma assinatura, um serviço ou uma região. As notificações de integridade que incluem a causa do problema e o Resolute ETA ajudam a executar melhor failover e failbacks. Para obter mais informações, consulte a [documentação de integridade do serviço do Azure](../service-health/index.yml).

## <a name="single-region-availability"></a>Disponibilidade de região única

Um sistema HDInsight básico tem os seguintes componentes. Todos os componentes têm seus próprios mecanismos de tolerância a falhas de região única.

* Computação (máquinas virtuais): cluster HDInsight do Azure
* Metastore (s): banco de dados SQL do Azure
* Armazenamento: Azure Data Lake Gen2 ou armazenamento de BLOBs
* Autenticação: Azure Active Directory, Azure Active Directory Domain Services, Enterprise Security Package
* Resolução de nomes de domínio: DNS do Azure

Há outros serviços opcionais que podem ser usados, como Azure Key Vault e Azure Data Factory.

:::image type="content" source="media/hdinsight-business-continuity/hdinsight-components.png" alt-text="Componentes do HDInsight":::

### <a name="azure-hdinsight-cluster-compute"></a>Cluster HDInsight do Azure (computação)

O HDInsight oferece um SLA de disponibilidade de 99,9%. Para fornecer alta disponibilidade em uma única implantação, o HDInsight é acompanhado por muitos serviços que estão no modo de alta disponibilidade por padrão. Mecanismos de tolerância a falhas no HDInsight são fornecidos pelos serviços de alta disponibilidade do ecossistema do Apache OSS e da Microsoft.

Os seguintes serviços foram projetados para serem altamente disponíveis:

#### <a name="infrastructure"></a>Infraestrutura

* Cabeçalho ativo e em espera
* Vários nós de gateway
* Três nós de quorum Zookeeper
* Nós de trabalho distribuídos por domínios de falha e de atualização

#### <a name="service"></a>Serviço

* Servidor Apache Ambari
* Servidores de linha do tempo de aplicativo para YARN
* Servidor de histórico de trabalho para o MapReduce do Hadoop
* Apache Livy
* HDFS
* Gerenciador de recursos YARN
* HBase Master

Consulte a documentação sobre [serviços de alta disponibilidade com suporte do Azure HDInsight](./hdinsight-high-availability-components.md) para saber mais.

Nem sempre leva um evento catastrófico para afetar a funcionalidade dos negócios. Os incidentes de serviço em um ou mais dos seguintes serviços em uma única região também podem levar à perda da funcionalidade de negócios esperada.

### <a name="hdinsight-metastore"></a>Metastore do HDInsight

O HDInsight usa o [banco de dados SQL do Azure](https://azure.microsoft.com/support/legal/sla/sql-database/v1_4/) como um metastore, que fornece um SLA de 99,99%. Três réplicas de dados persistem em um data center com replicação síncrona. Se houver uma perda de réplica, uma réplica alternativa será servida diretamente. A [replicação geográfica ativa](../azure-sql/database/active-geo-replication-overview.md) tem suporte pronto para uso com um máximo de quatro data centers. Quando houver um failover, seja manual ou data center, a primeira réplica na hierarquia se tornará compatível com leitura e gravação automaticamente. Para obter mais informações, consulte [continuidade de negócios do banco de dados SQL do Azure](../azure-sql/database/business-continuity-high-availability-disaster-recover-hadr-overview.md).

### <a name="hdinsight-storage"></a>Armazenamento do HDInsight

O HDInsight recomenda Azure Data Lake Storage Gen2 como a camada de armazenamento subjacente. O [armazenamento do Azure](https://azure.microsoft.com/support/legal/sla/storage/v1_5/), incluindo o Azure data Lake Storage Gen2, fornece um SLA de 99,9%. O HDInsight usa o serviço LRS no qual três réplicas de dados persistem dentro de um data center e a replicação é síncrona. Quando há uma perda de réplica, uma réplica é servida diretamente.

### <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory](https://azure.microsoft.com/support/legal/sla/active-directory/v1_0/) fornece um SLA de 99,9%. Active Directory é um serviço global com vários níveis de redundância interna e capacidade de recuperação automática. Para obter mais informações, consulte como [a Microsoft está continuamente aprimorando a confiabilidade do Azure Active Directory](https://azure.microsoft.com/blog/advancing-azure-active-directory-availability/).

### <a name="azure-active-directory-domain-services-ad-ds"></a>Azure Active Directory Domain Services (AD DS)

[Azure Active Directory Domain Services](https://azure.microsoft.com/support/legal/sla/active-directory-ds/v1_0/) fornece um SLA de 99,9%. O Azure AD DS é um serviço altamente disponível hospedado em data centers distribuídos globalmente. Os conjuntos de réplicas são um recurso de visualização no Azure AD DS que permite a recuperação de desastre geográfico se uma região do Azure ficar offline. Para obter mais informações, consulte [conjuntos de réplicas e recursos para Azure Active Directory Domain Services](../active-directory-domain-services/concepts-replica-sets.md) para saber mais.  

### <a name="azure-dns"></a>DNS do Azure

O [DNS do Azure](https://azure.microsoft.com/support/legal/sla/dns/v1_1/) fornece um SLA de 100%. O HDInsight usa o DNS do Azure em vários locais para resolução de nomes de domínio.

## <a name="multi-region-cost-and-complexity-optimizations"></a>Otimizações de custo e complexidade de várias regiões

Melhorar a continuidade dos negócios usando a recuperação de desastres de alta disponibilidade entre regiões requer designs arquitetônicos de maior complexidade e custo mais alto. As tabelas a seguir detalham algumas áreas técnicas que podem aumentar o custo total de propriedade.

### <a name="cost-optimizations"></a>Otimizações de custo

|Área|Causa do escalonamento de custos|Estratégias de otimização|
|----|------------------------|-----------------------|
|Armazenamento de dados|Duplicando dados/tabelas primárias em uma região secundária|Replicar somente os dados organizados|
|Saída de dados|Transferências de dados de região cruzada de saída chegam a um preço. Analisar diretrizes de preços de largura de banda|Replicar somente os dados organizados para reduzir a superfície de saída da região|
|Computação de cluster|Clusters HDInsight adicionais na região secundária|Use scripts automatizados para implantar a computação secundária após a falha principal. Use o dimensionamento automático para manter o tamanho do cluster secundário no mínimo. Use SKUs de VM mais baratas. Crie secundários em regiões nas quais os SKUs de VM podem ser descontados.|
|Autenticação |Cenários de multiusuário na região secundária incorrerão em configurações de AD DS do Azure adicionais|Evite configurações multiusuários na região secundária.|

### <a name="complexity-optimizations"></a>Otimizações de complexidade

|Área|Causa do escalonamento de complexidade|Estratégias de otimização|
|----|------------------------|-----------------------|
|Padrões de gravação de leitura |Exigindo que tanto o primário quanto o secundário sejam lidos e habilitados para gravação |Criar o secundário para ser somente leitura|
|RPO zero & RTO |Exigindo perda de dados zero (RPO = 0) e zero tempo de inatividade (RTO = 0) |Projete o RPO e o RTO de maneiras para reduzir o número de componentes que precisam de failover.|
|Funcionalidade de negócios |Exigindo funcionalidade comercial completa do primário no secundário |Avalie se você pode executar com um subconjunto crítico mínimo da funcionalidade de negócios no secundário.|
|Conectividade |Exigindo todos os sistemas upstream e downstream do primário para se conectar ao secundário também|Limite a conectividade secundária a um subconjunto crítico mínimo simples.|

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre os itens discutidos neste artigo, consulte:

* [Arquiteturas de continuidade de negócios do Azure HDInsight](./hdinsight-business-continuity-architecture.md)
* [Estudo de caso da arquitetura de solução de alta disponibilidade do Azure HDInsight](./hdinsight-high-availability-case-study.md)
* [Saiba mais sobre o Apache Hive e o HiveQL no Azure HDInsight?](./hadoop/hdinsight-use-hive.md)