---
title: O que é a Consulta Interativa no Azure HDInsight?
description: Uma introdução à Consulta Interativa no Azure HDInsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: overview
ms.date: 06/14/2019
ms.openlocfilehash: ea17ddeab21c371f41cc57115df4dd91277c3c42
ms.sourcegitcommit: 6e6813f8e5fa1f6f4661a640a49dc4c864f8a6cb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/17/2019
ms.locfileid: "67151194"
---
# <a name="what-is-interactive-query-in-azure-hdinsight"></a>O que é a Consulta Interativa no HDInsight

Consulta Interativa (também chamado Apache Hive LLAP ou [Processamento Analítico de Baixa Latência](https://cwiki.apache.org/confluence/display/Hive/LLAP)) é um [tipo de cluster do Azure HDInsight](../hdinsight-hadoop-provision-linux-clusters.md#cluster-types). A Consulta Interativa dá suporte ao cache na memória, o que torna as consultas do Apache Hive mais rápidas e muito mais interativas. Os clientes usam a consulta interativa para consultar dados armazenados no armazenamento do Azure e no Azure Data Lake Storage de modo extremamente rápido. A consulta interativa torna facilita o trabalho de cientista de dados e desenvolvedores com big data usando as ferramentas de BI que eles mais gostam. A Consulta interativa do HDInsight oferece suporte a várias ferramentas para acessar big data de maneira fácil.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)]

Um cluster de Consulta Interativa é diferente de um cluster Apache Hadoop. Ele contém apenas o serviço do Hive.

É possível acessar o serviço do Hive no cluster da Consulta Interativa somente por meio da exibição do Hive do Apache Ambari, do Beeline e do driver Microsoft Hive Open Database Connectivity (Hive ODBC). Não é possível acessá-lo por meio do console do Hive, do Templeton, da CLI Clássica do Azure nem do Azure PowerShell.

## <a name="create-an-interactive-query-cluster"></a>Criar um cluster de Consulta Interativa

Para obter mais informações sobre a criação de um cluster HDInsight, veja [Criar clusters Apache Hadoop no HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Escolha o tipo de cluster Consulta Interativa.

## <a name="execute-apache-hive-queries-from-interactive-query"></a>Executar consultas de Apache Hive da Consulta Interativa

Para executar consultas de Hive, você tem as seguintes opções:

* Usar o Microsoft Power BI

    Consulte [Visualizar dados de Hive de consulta interativa Apache Hive com o Power BI no Azure HDInsight](./apache-hadoop-connect-hive-power-bi-directquery.md) Consulte [Visualizar Big Data com o Power BI no Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md).

* Usar o Virtual Studio

    Consulte [Conectar-se ao Azure HDInsight e executar consultas Apache Hive usando Ferramentas do Data Lake para Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#run-interactive-apache-hive-queries).

* Usar o Visual Studio Code

    Veja [Usar o Visual Studio Code para o Apache Hive, o LLAP ou o pySpark](../hdinsight-for-vscode.md).
* Execute o Apache Hive usando a exibição do Hive do Apache Ambari.
  
    Use o [Apache Ambari Hive View com o Apache Hadoop no HDInsight](../hadoop/apache-hadoop-use-hive-ambari-view.md).

* Execute o Apache Hive usando o Beeline.
  
    Consulte [Usar Apache Hive com Apache Hadoop no HDInsight com Beeline](../hadoop/apache-hadoop-use-hive-beeline.md).
  
    É possível usar o Beeline no nó de cabeçalho ou em um nó de borda vazio. Recomendamos usar o Beeline em um nó de borda vazio. Para obter informações sobre como criar um cluster HDInsight usando um nó de borda vazio, consulte [Use empty edge nodes in HDInsight](../hdinsight-apps-use-edge-node.md) (Usar nós de borda vazios no HDInsight).
* Execute o Apache Hive usando o Hive ODBC.
  
    Veja [Conectar o Excel ao Apache Hadoop com o driver ODBC do Microsoft Hive](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).

Para localizar a cadeia de conexão do JDBC (Java Database Connectivity):

1. Entre no Apache Ambari usando a seguinte URL: `https://<cluster name>.AzureHDInsight.net`.
2. No menu à esquerda, selecione **Hive**.
3. Para copiar a URL, selecione o ícone da área de transferência:

   ![JDBC LLAP da Consulta Interativa no Hadoop HDInsight](./media/apache-interactive-query-get-started/hdinsight-hadoop-use-interactive-hive-jdbc.png)

## <a name="next-steps"></a>Próximas etapas

* Saiba como [criar clusters da Consulta Interativa no HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).
* Saiba como [visualizar Big Data com o Power BI no Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md).
* Saiba como [usar o Apache Zeppelin para executar consultas do Apache Hive no HDInsight do Azure](../interactive-query/hdinsight-connect-hive-zeppelin.md).
