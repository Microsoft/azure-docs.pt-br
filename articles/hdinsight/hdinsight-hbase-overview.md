---
title: "O que é o HBase no Azure HDInsight? | Microsoft Docs"
description: "Uma introdução ao Apache HBase no HDInsight, uma compilação de banco de dados NoSQL no Hadoop. Aprenda sobre casos de uso e compare HBase com outros clusters Hadoop."
keywords: "bigtable, nosql, o que é o hbase, apache hbase, hbase, visão geral do habase,"
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: d2a76d53-133a-4849-a30c-88d9c794391c
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/17/2017
ms.author: jgao
ms.openlocfilehash: 62b2700c368c4b0c66df140e8961408840beb626
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-hbase-in-hdinsight-a-nosql-database-that-provides-bigtable-like-capabilities-for-hadoop"></a>O que é HBase em HDInsight: um banco de dados NoSQL que fornece capacidades semelhantes a BigTable para Hadoop
O Apache HBase é um banco de dados NoSQL de código-fonte aberto, que é compilado no Hadoop e modelado com base em Google BigTable. O HBase fornece acesso aleatório e uma coerência forte para grandes quantidades de dados não estruturados e semiestruturados em um banco de dados sem esquema, organizado por famílias de colunas.

Os dados são armazenados nas linhas de uma tabela e os dados contidos nas linhas são agrupados por famílias de colunas. O HBase é um banco de dados sem esquema, no aspecto de que nem as colunas nem os tipos de dados armazenados nelas precisam ser definidos antes de serem utilizados. O código-fonte aberto é dimensionado linearmente para lidar com petabytes de dados em milhares de nós. Ele pode fazer uso da redundância de dados, do processamento em lote e de outros recursos que são fornecidos por aplicativos distribuídos no ecossistema do Hadoop.

## <a name="how-is-hbase-implemented-in-azure-hdinsight"></a>Como o HBase é implementado no Azure HDInsight?
O HBase do HDInsight é oferecido como um cluster gerenciado integrado ao ambiente do Azure. Os clusters são configurados para armazenar dados diretamente no [Armazenamento do Azure](./hdinsight-hadoop-use-blob-storage.md) ou [Azure Data Lake Store](./hdinsight-hadoop-use-data-lake-store.md), que proporciona baixa latência e maior flexibilidade para escolhas relacionadas a desempenho e custos. Isso permite que os clientes criem sites interativos que trabalham com grandes conjuntos de dados, de modo a criar serviços que armazenam dados de sensor e telemetria de milhões de pontos de extremidade e analisar esses dados por meio de trabalhos do Hadoop. O HBase e o Hadoop são bons pontos iniciais para projetos de big data no Azure e, especificamente, podem possibilitar que aplicativos em tempo real trabalhem com grandes conjuntos de dados.

A implementação do HDInsight utiliza a arquitetura de expansão do HBase para fornecer o compartilhamento automático de tabelas, uma sólida consistência para leituras e gravações e failover automático. O desempenho é aprimorado pelo cache na memória para leituras e streaming de alta produtividade para gravações. O cluster do HBase pode ser criado dentro da rede virtual. Para obter detalhes, confira [Criar clusters HDInsight na Rede Virtual do Azure][hbase-provision-vnet].

## <a name="how-is-data-managed-in-hdinsight-hbase"></a>Como os dados no HBase do HDInsight são gerenciados?
Os dados podem ser gerenciados no HBase usando os comandos `create`, `get`, `put` e `scan` do shell do HBase. Os dados são gravados no banco de dados usando `put` e lidos usando `get`. O comando `scan` é utilizado para obter dados de múltiplas linhas em uma tabela. Os dados também podem ser gerenciados utilizando a API C# do HBase, que oferece uma biblioteca de cliente sobre a API REST do HBase. Um banco de dados HBase também pode ser consultado usando o Hive. Para ver uma introdução a esses modelos de programação, consulte [Get started using HBase with Hadoop in HDInsight][hbase-get-started] (Introdução ao uso do HBase com Hadoop no HDInsight). Também estão disponíveis coprocessadores, que permitem o processamento de dados em nós que hospedam o banco de dados.

> [!NOTE]
> Não há suporte para thrift pelo HBase no HDInsight.
>

## <a name="scenarios-use-cases-for-hbase"></a>Cenários: casos de uso para o HBase
O caso de uso canônico para o qual o BigTable (e, por extensão, o HBase) foi criado era a pesquisa na Web. Os mecanismos de pesquisa criam índices que mapeiam termos nas páginas da web que os contêm. Mas há muitos outros casos de uso aos quais o HBase se ajusta, muitos dos quais são descritos nesta seção.

* Repositório de valor-chave
  
    O HBase pode ser usado como um repositório de valor-chave e é adequado para gerenciar sistemas de mensagens. O Facebook utiliza o HBase para seu sistema de mensagens, e ele é ideal para armazenar e gerenciar comunicações pela Internet. O WebTable utiliza o HBase para pesquisar e gerenciar tabelas extraídas de páginas da Web.
* Dados de sensor
  
    O HBase é útil para capturar dados que são coletados gradativamente de várias fontes. Isso inclui análise de dados sociais, séries temporais, manter painéis interativos atualizados com tendências e contadores e gerenciar sistemas de logs de auditorias. Os exemplos incluem o terminal de comerciantes Bloomberg e o OpenTSDB (Open Time Series Database), que armazena e oferece acesso a métricas coletadas sobre a integridade dos sistemas de servidores.
* Consulta em tempo real
  
    [Phoenix](http://phoenix.apache.org/) é um mecanismo de consulta SQL para o HBase no Apache. Ele é acessado como um driver JDBC e permite consultar e gerenciar tabelas do HBase utilizando o SQL.
* HBase como uma plataforma
  
    Os aplicativos podem ser executados sobre o HBase utilizando-o como um armazenamento de dados. Exemplos incluem Phoenix, OpenTSDB, Kiji e Titan. Os aplicativos também podem ser integrados ao HBase. Exemplos incluem Hive, Pig, Solr, Storm, Flume, Impala, Spark, Ganglia e Drill.

## <a name="next-steps"></a>Próximas etapas
* [Introdução ao uso do HBase com Hadoop no HDInsight][hbase-get-started]
* [Criar clusters do HDInsight na Rede Virtual do Azure][hbase-provision-vnet]
* [Configurar a replicação do HBase no HDInsight](hdinsight-hbase-replication.md)
* [Utilizar o Maven para compilar aplicativos Java que usam o HBase com o HDInsight (Hadoop)][hbase-build-java-maven]

## <a name="see-also"></a>Consulte também
* [HBase no Apache](https://hbase.apache.org/)
* [Bigtable: um sistema de armazenamento distribuído para dados estruturados](http://research.google.com/archive/bigtable.html)

[hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md

[hbase-build-java-maven]: hdinsight-hbase-build-java-maven.md

[hdinsight-use-hive]: hdinsight-use-hive.md

[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md

[hbase-get-started]: http://azure.microsoft.com/documentation/articles/hdinsight-hbase-get-started/

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://portal.azure.com/
[azure-create-storageaccount]:../storage/common/storage-create-storage-account.md

[apache-hadoop]: http://hadoop.apache.org/
