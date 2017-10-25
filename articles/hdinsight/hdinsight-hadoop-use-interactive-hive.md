---
title: Usar a Consulta Interativa com o Azure HDInsight | Microsoft Docs
description: Saiba como usar a Consulta Interativa (Hive LLAP) com o HDInsight.
keywords: 
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 0957643c-4936-48a3-84a3-5dc83db4ab1a
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2017
ms.author: jgao
ms.openlocfilehash: 822c5f2302fb32d0d5a05ba69132282c050b7b8b
ms.sourcegitcommit: 54fd091c82a71fbc663b2220b27bc0b691a39b5b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/12/2017
---
# <a name="use-interactive-query-with-hdinsight"></a>Usar a Consulta Interativa com o HDInsight
A Consulta Interativa (também chamada de Hive LLAP ou [Live Long and Process](https://cwiki.apache.org/confluence/display/Hive/LLAP)) é um [tipo de cluster](hdinsight-hadoop-provision-linux-clusters.md#cluster-types) Azure HDInsight. A Consulta Interativa dá suporte ao cache na memória, o que torna as consultas do Hive mais rápidas e muito mais interativas. 

Um cluster de Consulta Interativa é diferente de um cluster Hadoop. Ele contém apenas o serviço do Hive. 

> [!NOTE]
> É possível acessar o serviço do Hive no cluster da Consulta Interativa somente por meio da exibição do Hive do Ambari, do Beeline e do driver Microsoft Hive Open Database Connectivity (Hive ODBC). Não é possível acessá-lo por meio do console do Hive, Templeton, a CLI do Azure (ferramenta de linha de comando do Azure) ou Azure PowerShell. 
> 
> 

## <a name="create-an-interactive-query-cluster"></a>Criar um cluster de Consulta Interativa
Para obter mais informações sobre a criação de um cluster HDInsight, veja [Criar clusters Hadoop no HDInsight](hdinsight-hadoop-provision-linux-clusters.md). Escolha o tipo de cluster Consulta Interativa.

## <a name="execute-hive-queries-from-interactive-query"></a>Executar consultas de Hive da Consulta Interativa
Para executar consultas de Hive, você tem as seguintes opções:

* Usar o Power BI

    Veja [Visualizar Big Data com o Power BI no Azure HDInsight](./hdinsight-connect-hive-power-bi.md).

* Usar Zeppelin

    Veja [Usar Zeppelin para executar consultas do Hive no Azure HDInsight ](./hdinsight-connect-hive-zeppelin.md).

* Usar o Virtual Studio

    Veja [Conectar-se ao Azure HDInsight e executar consultas Hive usando Ferramentas do Data Lake para Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md#run-a-hive-query).

* Usar o Visual Studio Code

    Veja [Usar o Visual Studio Code para o Hive, o LLAP ou o pySpark](hdinsight-for-vscode.md).
* Execute o Hive usando a exibição do Hive do Ambari.
  
    Veja [Usar a Exibição do Hive com o Hadoop no Azure HDInsight](hdinsight-hadoop-use-hive-ambari-view.md).
* Execute o Hive usando o Beeline.
  
    Veja [Usar o Hive com o Hadoop no HDInsight com Beeline](hdinsight-hadoop-use-hive-beeline.md).
  
    É possível usar o Beeline no nó de cabeçalho ou em um nó de borda vazio. Recomendamos usar o Beeline em um nó de borda vazio. Para obter informações sobre como criar um cluster HDInsight usando um nó de borda vazio, consulte [Use empty edge nodes in HDInsight](hdinsight-apps-use-edge-node.md) (Usar nós de borda vazios no HDInsight).
* Execute o Hive usando o ODBC do Hive.
  
    Veja [Conectar o Excel ao Hadoop com o driver ODBC do Microsoft Hive](hdinsight-connect-excel-hive-odbc-driver.md).

Para localizar a cadeia de conexão do JDBC (Java Database Connectivity):

1. Entre no Ambari usando a seguinte URL: https://\<nome do cluster\>.AzureHDInsight.net.
2. No menu à esquerda, selecione **Hive**.
3. Para copiar a URL, selecione o ícone da área de transferência:
   
   ![JDBC LLAP da Consulta Interativa no Hadoop HDInsight](./media/hdinsight-hadoop-use-interactive-hive/hdinsight-hadoop-use-interactive-hive-jdbc.png)

## <a name="next-steps"></a>Próximas etapas

* Saiba como [criar clusters da Consulta Interativa no HDInsight](hdinsight-hadoop-provision-linux-clusters.md).
* Saiba como [visualizar Big Data com o Power BI no Azure HDInsight](./hdinsight-connect-hive-power-bi.md).
* Saiba como [usar Zeppelin para executar consultas do Hive no Azure HDInsight ](./hdinsight-connect-hive-zeppelin.md).
* Saiba como [executar consultas Hive usando as Ferramentas do Data Lake para o Visual Studio](./hdinsight-hadoop-visual-studio-tools-get-started.md#run-a-hive-query).
* Aprenda como [usar as Ferramentas do Azure HDInsight para Visual Studio Code](hdinsight-for-vscode.md).
* Saiba como [usar a Exibição do Hive com o Hadoop no HDInsight](hdinsight-hadoop-use-hive-ambari-view.md)
* Saiba como [usar o Beeline para enviar consultas de Hive no HDInsight](hdinsight-hadoop-use-hive-beeline.md).
* Saiba como [conectar o Excel ao Hadoop com o driver ODBC do Microsoft Hive](hdinsight-connect-excel-hive-odbc-driver.md).

