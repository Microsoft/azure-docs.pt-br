---
title: 'Guia de início rápido: executar o Apache Hive no Azure HDInsight usando o Apache Zeppelin'
description: Neste início rápido, você aprenderá a usar o Apache Zeppelin para executar consultas do Apache Hive.
ms.service: hdinsight
ms.topic: quickstart
ms.custom: hdinsightactive
ms.date: 12/03/2019
ms.openlocfilehash: 75f758054f867c58dd4180fe5e728e66785e3b11
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104869762"
---
# <a name="quickstart-execute-apache-hive-queries-in-azure-hdinsight-with-apache-zeppelin"></a>Guia de início rápido: executar consultas do Apache Hive no Azure HDInsight usando o Apache Zeppelin

Neste início rápido, você aprenderá a usar o Apache Zeppelin para executar consultas do [Apache Hive](https://hive.apache.org/) no Azure HDInsight. Os clusters de Consulta Interativa do HDInsight incluem blocos de anotações do [Apache Zeppelin](https://zeppelin.apache.org/) que você pode usar para executar consultas interativas do Hive.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Um cluster Consulta interativa do HDInsight. Confira [Criar cluster](../hadoop/apache-hadoop-linux-tutorial-get-started.md) para criar um cluster HDInsight.  Escolha o tipo de cluster **Consulta interativa**.

## <a name="create-an-apache-zeppelin-note"></a>Criar uma anotação do Apache Zeppelin

1. Substitua `CLUSTERNAME` pelo nome do cluster na seguinte URL `https://CLUSTERNAME.azurehdinsight.net/zeppelin`. Em seguida, insira a URL em um navegador da Web.

2. Insira o nome de usuário e a senha de logon do cluster. Na página do Zeppelin, você pode criar uma nova anotação ou abrir anotações existentes. O **HiveSample** contém alguns exemplos de consultas de Hive.  

    :::image type="content" source="./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin.png" alt-text="HDInsight Consulta Interativa Zeppelin" border="true":::

3. Selecione **Criar anotação**.

4. Na caixa de diálogo **Criar anotação**, digite ou selecione os seguintes valores:

    - Nome da anotação: digite o nome da anotação.
    - Interpretador padrão: selecione **jdbc** na lista suspensa.

5. Selecione **Criar anotação**.

6. Insira a seguinte consulta de Hive na seção do código e, em seguida, pressione **Shift + Enter**:

    ```hive
    %jdbc(hive)
    show tables
    ```

    :::image type="content" source="./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin-query.png" alt-text="HDInsight Consulta Interativa Zeppelin executa a consulta" border="true":::

    A instrução **%jdbc(hive)** na primeira linha informa o bloco de anotações para usar o interpretador JDBC do Hive.

    A consulta deverá retornar uma tabela de Hive denominada **hivesampletable**.

    A seguir há mais duas consultas de Hive adicionais que podem ser executadas em relação a **hivesampletable**:

    ```hive
    %jdbc(hive)
    select * from hivesampletable limit 10

    %jdbc(hive)
    select ${group_name}, count(*) as total_count
    from hivesampletable
    group by ${group_name=market,market|deviceplatform|devicemake}
    limit ${total_count=10}
    ```

    Em comparação com o Hive tradicional, os resultados da consulta retornam muito mais rapidamente.

### <a name="additional-examples"></a>Mais exemplos

1. Criar uma tabela. Execute o código abaixo no Notebook do Zeppelin:

    ```hql
    %jdbc(hive)
    CREATE EXTERNAL TABLE log4jLogs (
        t1 string,
        t2 string,
        t3 string,
        t4 string,
        t5 string,
        t6 string,
        t7 string)
    ROW FORMAT DELIMITED
    FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE;
    ```

1. Carregar dados na nova tabela. Execute o código abaixo no Notebook do Zeppelin:

    ```hql
    %jdbc(hive)
    LOAD DATA
    INPATH 'wasbs:///example/data/sample.log'
    INTO TABLE log4jLogs;
    ```

1. Inserir um único registro. Execute o código abaixo no Notebook do Zeppelin:

    ```hql
    %jdbc(hive)
    INSERT INTO TABLE log4jLogs2
    VALUES ('A', 'B', 'C', 'D', 'E', 'F', 'G');
    ```

Examine o [Manual da linguagem Hive](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) para obter mais sintaxe.

## <a name="clean-up-resources"></a>Limpar os recursos

Após concluir o início rápido, poderá ser conveniente excluir o cluster. Com o HDInsight, seus dados são armazenados no Armazenamento do Azure, assim você poderá excluir, com segurança, um cluster quando ele não estiver em uso. Você também é cobrado por um cluster HDInsight, mesmo quando ele não está em uso. Como os encargos para o cluster são muitas vezes maiores do que os encargos para armazenamento, faz sentido, do ponto de vista econômico, excluir os clusters quando não estiverem em uso.

Para excluir um cluster, confira [Delete an HDInsight cluster using your browser, PowerShell, or the Azure CLI](../hdinsight-delete-cluster.md) (Excluir um cluster HDInsight usando o navegador, o PowerShell ou a CLI do Azure).

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu a usar o Apache Zeppelin para executar consultas de Apache Hive no Azure HDInsight. Para saber mais sobre as consultas de Hive, o próximo artigo mostrará como executar consultas com o Visual Studio.

> [!div class="nextstepaction"]
> [Conectar-se ao Azure HDInsight e executar consultas de Apache Hive usando as Ferramentas do Data Lake para Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)
