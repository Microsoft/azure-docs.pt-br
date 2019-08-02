---
title: 'Início Rápido: Executar consultas do Apache Hive no Azure HDInsight – Apache Zeppelin'
description: Neste início rápido, você aprenderá a usar o Apache Zeppelin para executar consultas do Apache Hive.
keywords: hdinsight, hadoop, hive, consulta interativa, LLAP
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: quickstart
ms.date: 05/06/2019
ms.author: hrasheed
ms.openlocfilehash: 1642c64b0b14c2e290aad689399b59d896660a28
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67056688"
---
# <a name="quickstart-execute-apache-hive-queries-in-azure-hdinsight-with-apache-zeppelin"></a>Início Rápido: Executar consultas do Apache Hive no Azure HDInsight com o Apache Zeppelin

Neste início rápido, você aprenderá a usar o Apache Zeppelin para executar consultas do [Apache Hive](https://hive.apache.org/) no Azure HDInsight. Os clusters de Consulta Interativa do HDInsight incluem blocos de anotações do [Apache Zeppelin](https://zeppelin.apache.org/) que você pode usar para executar consultas interativas do Hive.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Um cluster Consulta interativa do HDInsight. Confira [Criar cluster](../hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster) para criar um cluster HDInsight.  Escolha o tipo de cluster **Consulta interativa**.

## <a name="create-an-apache-zeppelin-note"></a>Criar uma anotação do Apache Zeppelin

1. Substitua `CLUSTERNAME` pelo nome do cluster na seguinte URL `https://CLUSTERNAME.azurehdinsight.net/zeppelin`. Em seguida, insira a URL em um navegador da Web.

2. Insira o nome de usuário e a senha de logon do cluster. Na página do Zeppelin, você pode criar uma nova anotação ou abrir anotações existentes. O **HiveSample** contém alguns exemplos de consultas de Hive.  

    ![HDInsight Consulta Interativa Zeppelin](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin.png)

3. Selecione **Criar anotação**.

4. Na caixa de diálogo **Criar anotação**, digite ou selecione os seguintes valores:

    - Nome da anotação: insira um nome para a anotação.
    - Interpretador padrão: selecione **jdbc** na lista suspensa.

5. Selecione **Criar anotação**.

6. Insira a seguinte consulta de Hive na seção do código e, em seguida, pressione **Shift + Enter**:

    ```hive
    %jdbc(hive)
    show tables
    ```

    ![HDInsight Consulta Interativa Zeppelin executa a consulta](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin-query.png)

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

## <a name="clean-up-resources"></a>Limpar recursos

Após concluir o início rápido, poderá ser conveniente excluir o cluster. Com o HDInsight, seus dados são armazenados no Armazenamento do Azure, assim você poderá excluir, com segurança, um cluster quando ele não estiver em uso. Você também é cobrado por um cluster HDInsight, mesmo quando ele não está em uso. Como os encargos para o cluster são muitas vezes maiores do que os encargos para armazenamento, faz sentido, do ponto de vista econômico, excluir os clusters quando não estiverem em uso.

Para excluir um cluster, confira [Delete an HDInsight cluster using your browser, PowerShell, or the Azure CLI](../hdinsight-delete-cluster.md) (Excluir um cluster HDInsight usando o navegador, o PowerShell ou a CLI do Azure).

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu a usar o Apache Zeppelin para executar consultas de Apache Hive no Azure HDInsight. Para saber mais sobre as consultas de Hive, o próximo artigo mostrará como executar consultas com o Visual Studio.

> [!div class="nextstepaction"]
> [Conectar-se ao Azure HDInsight e executar consultas de Apache Hive usando as Ferramentas do Data Lake para Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)
