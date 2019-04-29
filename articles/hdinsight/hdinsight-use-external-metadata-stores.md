---
title: Usar armazenamentos de metadados externos - Azure HDInsight
description: Use armazenamentos de metadados externos com clusters HDInsight.
services: hdinsight
documentationcenter: ''
author: jasonwhowell
manager: cgronlun
tags: azure-portal
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
origin.date: 09/14/2018
ms.author: v-yiso
ms.date: 04/01/2019
ms.openlocfilehash: 0a6bcb3fb94df56caf4d095097572dd8a704f856
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63763863"
---
# <a name="use-external-metadata-stores-in-azure-hdinsight"></a>Usar armazenamentos de metadados externos no Azure HDInsight

O metastore Apache Hive no HDInsight é uma parte essencial da arquitetura do Apache Hadoop. Um metastore é o repositório central de esquema que pode ser usado por outras ferramentas de acesso de Big Data, como Apache Spark, Interactive Query (LLAP), Presto ou Apache Pig. O HDInsight usa um Banco de Dados SQL do Azure como metastore do Hive.

![Arquitetura de armazenamento de metadados Hive do HDInsight](./media/hdinsight-use-external-metadata-stores/metadata-store-architecture.png)

Há duas maneiras de configurar um metastore para seus clusters HDInsight:

* [Metastore padrão](#default-metastore)
* [Metastore personalizado](#custom-metastore)

## <a name="default-metastore"></a>Metastore padrão

Por padrão, o HDInsight cria um metastore com cada tipo de cluster. Em vez disso, você pode especificar um metastore personalizado. O metastore padrão inclui as seguintes considerações:
- Sem custo adicional. O HDInsight cria um metastore com cada tipo de cluster sem nenhum custo adicional para você.
- Cada metastore padrão é parte do ciclo de vida do cluster. Quando você exclui um cluster, o metastore e os metadados correspondentes também são excluídos.
- Você não pode compartilhar o metastore padrão com outros clusters.
- O metastore padrão usa o SQL DB básico do Azure, que possui um limite de cinco DTUs (unidade de transação do banco de dados).
Esse metastore padrão é normalmente usado para cargas de trabalho relativamente simples que não exigem vários clusters e não precisam de metadados preservados além do ciclo de vida do cluster.


## <a name="custom-metastore"></a>Metastore personalizado

O HDInsight também dá suporte a metastores personalizados, que são recomendados para clusters de produção:
- Você pode especificar seu próprio Banco de Dados SQL do Azure com o metastore.
- O ciclo de vida do metastore não está associado a um ciclo de vida de clusters e, portanto, você pode criar e excluir clusters sem perder metadados. Os metadados, como os esquemas Hive, serão mantidos até mesmo depois de você excluir e recriar o cluster HDInsight.
- Um metastore personalizado permite que você anexe vários clusters e tipos de cluster ao metastore. Por exemplo, um único metastore pode ser compartilhado em clusters Interactive Query, Hive e Spark no HDInsight.
- Você paga o custo de um metastore (Banco de Dados SQL do Azure) de acordo com o nível de desempenho escolhido.
- Você pode expandir o metastore conforme a necessidade.

![Caso de uso do armazenamento de metadados Hive do HDInsight](./media/hdinsight-use-external-metadata-stores/metadata-store-use-case.png)


### <a name="select-a-custom-metastore-during-cluster-creation"></a>Selecione um metastore personalizado durante a criação do cluster

Você pode apontar seu cluster para um Banco de Dados SQL do Azure criado anteriormente durante a criação do cluster, ou configurar o Banco de Dados SQL depois que o cluster é criado. Essa opção é especificada nas configurações Armazenamento > Metastore na criação de um novo cluster Hadoop, Spark ou Hive interativo do portal do Azure.

![Armazenamento de metadados Hive do HDInsight Portal do Azure](./media/hdinsight-use-external-metadata-stores/metadata-store-azure-portal.png)

Você também pode adicionar outros clusters a um metastore personalizado no portal do Azure ou nas configurações do Ambari (Hive > Avançado)

![Armazenamento de metadados Hive do HDInsight Ambari](./media/hdinsight-use-external-metadata-stores/metadata-store-ambari.png)

## <a name="hive-metastore-best-practices"></a>Melhores práticas de metastore Hive

Aqui estão algumas melhores práticas gerais de metastore Hive do HDInsight:

- Use um metastore personalizado sempre que possível, para ajudar a separar recursos de computação (seu cluster em execução) e metadados (armazenados no metastore).
- Inicie com uma camada S2, que fornece 50 DTU e 250 GB de armazenamento. Se você vir um afunilamento, poderá expandir o banco de dados.
- Se você pretende que vários clusters HDInsight acessem dados separados, use um banco de dados separado para o metastore em cada cluster. Se você compartilhar um metastore entre vários clusters do HDInsight, isso significa que os clusters usam os mesmos metadados e arquivos de dados do usuário subjacentes.
- Faça backup de seu metastore personalizado periodicamente. O Banco de Dados SQL do Azure gera backups automaticamente, mas o prazo de retenção de backup varia. Para obter mais informações, consulte [Saiba mais sobre os backups automáticos do Banco de Dados SQL](../sql-database/sql-database-automated-backups.md).
- Localize seu cluster do metastore e HDInsight na mesma região, para obter o mais alto desempenho e os menores custos de saída de rede.
- Monitore seu metastore para desempenho e disponibilidade, usando ferramentas de monitoramento do banco de dados SQL do Azure, como o portal do Azure ou os logs do Azure Monitor.
- Quando uma nova versão superior do Azure HDInsight é criada em um banco de dados metastore personalizado existente, o sistema atualiza o esquema do metastore, que é irreversível sem restaurar o backup do banco de dados.
- Se você compartilhar um metastore em vários clusters, verifique se todos os clusters são da mesma versão do HDInsight. Diferentes versões do Hive usam diferentes esquemas de banco de dados metastore. Por exemplo, você não pode compartilhar um metastore entre os clusters com versão Hive 1.2 e Hive 2.1. 

##  <a name="apache-oozie-metastore"></a>Metastore do Apache Oozie

Apache Oozie é um sistema de coordenação do fluxo de trabalho que gerencia trabalhos do Hadoop.  O Oozie dá suporte a trabalhos do Hadoop para Apache MapReduce, Pig, Hive e outros.  O Oozie usa um metastore para armazenar detalhes sobre fluxos de trabalho atuais e concluídos. Para aumentar o desempenho ao usar o Oozie, você pode usar o Banco de Dados SQL do Azure como um metastore personalizado. O metastore também pode fornecer acesso a dados de trabalho do Oozie depois de você excluir o cluster.

Para obter instruções sobre como criar um metastore Oozie com o Banco de Dados SQL do Azure, confira [Usar o Apache Oozie para fluxos de trabalho](hdinsight-use-oozie-linux-mac.md).

## <a name="next-steps"></a>Próximos passos

- [Configure os clusters no HDInsight com o Apache Hadoop, o Apache Spark, o Apache Kafka e muito mais](./hdinsight-hadoop-provision-linux-clusters.md)
