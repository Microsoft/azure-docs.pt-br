---
title: "Ferramentas do Hive com Data Lake (Hadoop) para Visual Studio – Azure HDInsight | Microsoft Docs"
description: Saiba como usar as ferramentas de Data Lake para Visual Studio para executar consultas do Apache Hive com Apache Hadoop no Azure HDInsight.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 2b3e672a-1195-4fa5-afb7-b7b73937bfbe
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/28/2017
ms.author: larryfr
ms.openlocfilehash: b9a8f857f42f16ec7735a79ec29e77e3c99c8ba7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="run-hive-queries-using-the-data-lake-tools-for-visual-studio"></a>Executar consultas Hive usando as ferramentas do Data Lake para o Visual Studio

Saiba como usar as ferramentas do Data Lake para Visual Studio para consultar o Apache Hive. As ferramentas de Data Lake permitem que você facilmente crie, envie e monitore consultas de Hive para Hadoop no Azure HDInsight.

## <a id="prereq"></a>Pré-requisitos

* Um cluster Azure HDInsight (Hadoop no HDInsight)

  > [!IMPORTANT]
  > O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para obter mais informações, confira [baixa do HDInsight no Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Visual Studio (uma das versões a seguir):

    * Visual Studio 2013 Community/Professional/Premium/Ultimate com Atualização 4

    * Visual Studio 2015 (qualquer edição)

    * Visual Studio 2017 (qualquer edição)

* Ferramentas do HDInsight para Visual Studio ou ferramentas do Azure Data Lake para Visual Studio. Confira [Começar a usar as ferramentas Hadoop para HDInsight do Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md) para obter informações sobre como instalar e configurar as ferramentas.

## <a id="run"></a> Executar consultas Hive usando o Visual Studio

1. Abra o **Visual Studio** e selecione **Novo** > **Projeto** > **Azure Data Lake** > **HIVE** > **Aplicativo Hive**. Forneça um nome para esse projeto.

2. Abra o arquivo **Script.hql** criado com esse projeto e cole as seguintes instruções HiveQL:

   ```hiveql
   set hive.execution.engine=tez;
   DROP TABLE log4jLogs;
   CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
   ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
   STORED AS TEXTFILE LOCATION '/example/data/';
   SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND  INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
   ```

    As instruções executam as seguintes ações:

   * `DROP TABLE`: se a tabela existir, esta instrução a excluirá.

   * `CREATE EXTERNAL TABLE`: cria uma nova tabela 'externa' no Hive. As tabelas externas armazenam apenas a definição da tabela no Hive (os dados são mantidos no local original).

     > [!NOTE]
     > As tabelas externas devem ser usadas quando você espera que os dados subjacentes sejam atualizados por uma fonte externa. Por exemplo, um trabalho de MapReduce ou serviço do Azure.
     >
     > Remover uma tabela externa **não** exclui os dados, somente a definição de tabela.

   * `ROW FORMAT`: informa ao Hive como os dados são formatados. Nesse caso, os campos em cada log são separados por um espaço.

   * `STORED AS TEXTFILE LOCATION`: informa ao Hive o local em que os dados são armazenados no diretório de exemplo/de dados e que eles estão armazenados como texto.

   * `SELECT`: seleciona uma contagem de todas as linhas, nas quais a coluna `t4` contém o valor `[ERROR]`. Essa instrução retorna um valor de `3`, já que há três linhas que contêm esse valor.

   * `INPUT__FILE__NAME LIKE '%.log'`: informa ao Hive que só devemos retornar dados de arquivos que terminam em .log. Essa cláusula restringe a pesquisa para o arquivo sample.log que contém os dados.

3. Na barra de ferramentas, selecione o **Cluster HDInsight** que você deseja usar nessa consulta. Selecione **Enviar** para executar as instruções como um trabalho do Hive.

   ![Barra de envio](./media/hdinsight-hadoop-use-hive-visual-studio/toolbar.png)

4. O **Resumo do Trabalho do Hive** aparecerá e exibirá informações sobre o trabalho em execução. Use o link **Atualizar** para atualizar as informações do trabalho, até o **Status do Trabalho** ser alterado para **Concluído**.

   ![resumo do trabalho exibindo um trabalho concluído](./media/hdinsight-hadoop-use-hive-visual-studio/jobsummary.png)

5. Use o link **Saída de Trabalho** para exibir a saída desse trabalho. Ele exibe `[ERROR] 3`, que é o valor retornado por essa consulta.

6. Você também pode executar consultas Hive sem criar um projeto. Usando o **Gerenciador de Servidores**, expanda **Azure** > **HDInsight**, clique com o botão direito do mouse no seu servidor do HDInsight e selecione **Escrever uma Consulta de Hive**.

7. No documento **temp.hql** que aparece, adicione as seguintes instruções HiveQL:

   ```hiveql
   set hive.execution.engine=tez;
   CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
   INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
   ```

    As instruções executam as seguintes ações:

   * `CREATE TABLE IF NOT EXISTS`: cria uma tabela, se ela ainda não existir. Uma vez que a palavra-chave `EXTERNAL` não é usada, essa instrução cria uma tabela interna. As tabelas internas são armazenadas no data warehouse do Hive e gerenciadas por ele.

     > [!NOTE]
     > Ao contrário das tabelas `EXTERNAL`, o descarte de uma tabela interna excluirá também os dados subjacentes.

   * `STORED AS ORC`: armazena os dados no formato ORC (Optimized Row Columnar). Esse é um formato altamente otimizado e eficiente para o armazenamento de dados do Hive.

   * `INSERT OVERWRITE ... SELECT`: seleciona linhas da tabela `log4jLogs` que contêm `[ERROR]` e, então, insere os dados na tabela `errorLogs`.

8. Na barra de ferramentas, selecione no menu suspenso de **Enviar** para executar o trabalho. Use o **Status do Trabalho** para determinar se o trabalho foi concluído com êxito.

9. Para verificar se o trabalho criou a tabela, use o **Gerenciador de Servidores** e expanda **Azure** > **HDInsight** > seu cluster HDInsight > **Bancos de Dados do Hive** > **padrão**. As tabelas **errorLogs** e **log4jLogs** são listadas.

## <a id="nextsteps"></a>Próximas etapas

Como você pode ver, as ferramentas do HDInsight para o Visual Studio fornecem uma maneira fácil de trabalhar com as consultas do Hive no HDInsight.

Para obter informações gerais sobre o Hive no HDInsight:

* [Usar o Hive com Hadoop no HDInsight](hdinsight-use-hive.md)

Para obter informações sobre outros modos possíveis de trabalhar com Hadoop no HDInsight:

* [Usar o Pig com Hadoop no HDInsight](hdinsight-use-pig.md)

* [Usar o MapReduce com Hadoop no HDInsight](hdinsight-use-mapreduce.md)

Para obter mais informações sobre as ferramentas do HDInsight para o Visual Studio:

* [Introdução às ferramentas do HDInsight para Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md)

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md



[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md

[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

[image-hdi-hive-powershell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
[image-hdi-hive-architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png
