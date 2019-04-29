---
title: Notas da versão mais recente do Microsoft Azure HDInsight
description: Notas da versão mais recente do Microsoft Azure HDInsight. Obtenha detalhes e dicas de desenvolvimento para Hadoop, Spark, servidor R, Hive e muito mais.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 0beac64ceb0bbf729d2f6d0f6a0ca8d5af499f42
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63760120"
---
# <a name="release-notes-for-azure-hdinsight"></a>Notas da versão mais recente do Microsoft Azure HDInsight

Este artigo fornece informações sobre as atualizações de versão **mais recentes** do Azure HDInsight. Para obter informações sobre versões anteriores, consulte o [Arquivo morto de notas de versão do HDInsight](hdinsight-release-notes-archive.md).

> [!IMPORTANT]  
> O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para saber mais, confira o [artigo sobre controle de versão do HDInsight](hdinsight-component-versioning.md).

## <a name="summary"></a>Resumo

O Azure HDInsight é um dos serviços mais populares entre os clientes corporativos para análise de software livre Apache Hadoop e Apache Spark no Azure.

## <a name="new-features"></a>Novos recursos

Para obter mais informações sobre alterações importantes com o HDInsight 4.0., consulte [o que há de novo no HDI 4.0?](../hdinsight/hdinsight-version-release.md).

## <a name="component-versions"></a>Versões do componente

As versões do Apache oficiais de todos os componentes do HDInsight 4.0 são fornecidas abaixo. Os componentes listados são versões das versões estáveis mais recentes disponíveis.

- Apache Ambari 2.7.1
- Apache Hadoop 3.1.1
- Apache HBase 2.0.0
- Apache Hive 3.1.0
- Apache Kafka 1.1.1
- Apache Mahout 0.9.0+
- Apache Oozie 4.2.0
- Apache Phoenix 4.7.0
- Apache Pig 0.16.0
- Apache Ranger 0.7.0
- Apache Slider 0.92.0
- Apache Spark 2.3.1
- Apache Sqoop 1.4.7
- Apache TEZ 0.9.1
- Apache Zeppelin 0.8.0
- Apache ZooKeeper 3.4.6

Em versões posteriores dos componentes do Apache, às vezes, estão incluídas na distribuição de HDP além das versões listadas acima. Nesse caso, as versões posteriores estão listadas na tabela de Versões Técnicas e não devem substituir as versões do componente Apache da lista acima em um ambiente de produção.

## <a name="apache-patch-information"></a>Informações de patch do Apache

Para obter mais informações sobre os patches disponíveis no HDInsight 4.0, consulte o patch de listagem para cada produto na tabela a seguir.

| Nome do produto | Informações de patch |
|---|---|
| Ambari | [Informações de patch do Ambari](https://docs.hortonworks.com/HDPDocuments/Ambari-2.7.1.0/bk_ambari-release-notes/content/ambari_relnotes-2.7.1.0-patch-information.html) |
| O Hadoop | [Informações de patch do Hadoop](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_hadoop.html) |
| HBase | [Informações de patch do HBase](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_hbase.html) |
| Hive  | Esta versão fornece Hive 3.1.0 com nenhum patch Apache adicionais.  |
| Kafka | Esta versão fornece Kafka 1.1.1 com nenhum patch Apache adicionais. |
| Oozie | [Informações de patch do Oozie](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_oozie.html) |
| Phoenix | [Informações de patch do Phoenix](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_phoenix.html) |
| Pig | [Informações de patch do pig](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_pig.html) |
| Ranger | [Informações de patch do Ranger](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_ranger.html) |
| Spark | [Informações de patch do Spark](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_spark.html) |
| Sqoop | Esta versão fornece Sqoop 1.4.7 com nenhum patch Apache adicionais. |
| Tez | Esta versão fornece Tez 0.9.1 com nenhum patch Apache adicionais. |
| Zeppelin | Esta versão fornece Zeppelin 0.8.0 com nenhum patch Apache adicionais. |
| Zookeeper | [Informações de patch do zookeeper](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_zookeeper.html) |

## <a name="fixed-common-vulnerabilities-and-exposures"></a>Common Vulnerabilities e Exposures Corrigidos

Para obter mais informações sobre segurança problemas resolvidos nesta versão, consulte Hortonworks [fixa vulnerabilidades e exposições comuns para HDP 3.0.1](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/cve.html).

## <a name="known-issues"></a>Problemas conhecidos

### <a name="replication-is-broken-for-secure-hbase-with-default-installation"></a>A replicação é interrompida para proteger o HBase com instalação padrão

Para HDInsight 4.0, execute as seguintes etapas:

1. Habilite a comunicação entre clusters.
1. Faça logon no nó principal do Active Directory.
1. Baixe um script para habilitar a replicação com o seguinte comando:

    ```
    sudo wget https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh
    ```
1. Digite o comando `sudo kinit <domainuser>`.
1. Digite o seguinte comando para executar o script:

    ```
    sudo bash hdi_enable_replication.sh -m <hn0> -s <srclusterdns> -d <dstclusterdns> -sp <srcclusterpasswd> -dp <dstclusterpasswd> -copydata
    ```
Para o HDInsight 3.6, faça o seguinte:

1. Entrar no active HMaster ZK.
1. Baixe um script para habilitar a replicação com o seguinte comando:
    ```
    sudo wget https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh
    ```
1. Digite o comando `sudo kinit -k -t /etc/security/keytabs/hbase.service.keytab hbase/<FQDN>@<DOMAIN>`.
1. Digite o seguinte comando: 

    ```bash
    sudo bash hdi_enable_replication.sh -s <srclusterdns> -d <dstclusterdns> -sp <srcclusterpasswd> -dp <dstclusterpasswd> -copydata
    ```

### <a name="phoenix-sqlline-stops-working-after-migrating-hbase-cluster-to-hdinsight-40"></a>Sqlline Phoenix para de funcionar após a migração de cluster HBase para HDInsight 4.0

Execute as seguintes etapas:

1. Remova as seguintes tabelas de Phoenix:
    1. `SYSTEM.FUNCTION`
    1. `SYSTEM.SEQUENCE`
    1. `SYSTEM.STATS`
    1. `SYSTEM.MUTEX`
    1. `SYSTEM.CATALOG`
1. Se você não pode excluir qualquer uma das tabelas, reinicie o HBase para limpar todas as conexões para as tabelas.
1. Execute `sqlline.py` novamente. Phoenix novamente criará todas as tabelas que foram excluídas na etapa 1.
1. Regenere tabelas Phoenix e modos de exibição para seus dados do HBase.

### <a name="phoenix-sqlline-stops-working-after-replicating-hbase-phoenix-metadata-from-hdinsight-36-to-40"></a>Sqlline Phoenix para de funcionar após replicar metadados HBase Phoenix do HDInsight 3.6 para 4.0

Execute as seguintes etapas:

1. Antes de fazer a replicação, vá para o cluster de destino 4.0 e execute `sqlline.py`. Esse comando irá gerar tabelas de Phoenix, como `SYSTEM.MUTEX` e `SYSTEM.LOG` que existem somente no 4.0.
1. Descarte as tabelas a seguir:
    1. `SYSTEM.FUNCTION`
    1. `SYSTEM.SEQUENCE`
    1. `SYSTEM.STATS`
    1. `SYSTEM.CATALOG`
1. Iniciar a replicação de HBase

## <a name="deprecation"></a>Reprovação

Apache Storm e ML services não estão disponíveis no HDInsight 4.0.
