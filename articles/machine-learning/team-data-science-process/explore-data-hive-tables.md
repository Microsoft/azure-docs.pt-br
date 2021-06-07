---
title: Explorar dados em tabelas do Hive com consultas do Hive - Processo de Ciência de Dados da Equipe
description: Use o exemplo dos scripts do Hive que são usados para explorar dados nas tabelas do Hive em um cluster HDInsight Hadoop.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: a7234a8c45c20c64dddb43a52a099aa92f2d297d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "93321124"
---
# <a name="explore-data-in-hive-tables-with-hive-queries"></a>Explorar dados em tabelas do Hive com consultas do Hive.

Este artigo fornece exemplos de scripts do Hive que são usados para explorar dados nas tabelas do Hive em um cluster HDInsight Hadoop.

Esta tarefa é uma etapa no [Processo de Ciência de Dados da Equipe](overview.md).

## <a name="prerequisites"></a>Pré-requisitos
Este artigo supõe que você:

* Criou uma conta de armazenamento do Azure. Se precisar de instruções, confira [Criar uma conta de Armazenamento do Azure](../../storage/common/storage-account-create.md)
* Provisionou um cluster do Hadoop personalizado com o serviço HDInsight. Se precisar de instruções, consulte [Personalizar os clusters do Hadoop do Azure HDInsight para análise avançada](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md).
* Os dados foram carregados para tabelas Hive em clusters do Hadoop do Azure HDInsight. Se não tiverem sido, siga as instruções em [Criar e carregar dados para tabelas Hive](move-hive-tables.md) para carregar dados para tabelas Hive primeiro.
* Habilitou o acesso remoto ao cluster. Se precisar de instruções, consulte [Acessar o nó principal do Cluster do Hadoop](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md).
* Se você precisar de instruções sobre como enviar consultas do Hive, consulte [Como enviar consultas do Hive](move-hive-tables.md#submit)

## <a name="example-hive-query-scripts-for-data-exploration"></a>Exemplo de scripts de consulta do Hive para exploração de dados
1. Obter a contagem de observações por partição `SELECT <partitionfieldname>, count(*) from <databasename>.<tablename> group by <partitionfieldname>;`
2. Obter a contagem de observações por dia `SELECT to_date(<date_columnname>), count(*) from <databasename>.<tablename> group by to_date(<date_columnname>);`
3. Obter os níveis em uma coluna categórica   
    `SELECT  distinct <column_name> from <databasename>.<tablename>`
4. Obter o número de níveis na combinação de duas colunas categóricas `SELECT <column_a>, <column_b>, count(*) from <databasename>.<tablename> group by <column_a>, <column_b>`
5. Obter a distribuição de colunas numéricas  
    `SELECT <column_name>, count(*) from <databasename>.<tablename> group by <column_name>`
6. Extrair registros de associação de duas tabelas
   
    ```hiveql
    SELECT
        a.<common_columnname1> as <new_name1>,
        a.<common_columnname2> as <new_name2>,
        a.<a_column_name1> as <new_name3>,
        a.<a_column_name2> as <new_name4>,
        b.<b_column_name1> as <new_name5>,
        b.<b_column_name2> as <new_name6>
    FROM
        (
        SELECT <common_columnname1>,
            <common_columnname2>,
            <a_column_name1>,
            <a_column_name2>,
        FROM <databasename>.<tablename1>
        ) a
        join
        (
        SELECT <common_columnname1>,
            <common_columnname2>,
            <b_column_name1>,
            <b_column_name2>,
        FROM <databasename>.<tablename2>
        ) b
        ON a.<common_columnname1>=b.<common_columnname1> and a.<common_columnname2>=b.<common_columnname2>
    ```

## <a name="additional-query-scripts-for-taxi-trip-data-scenarios"></a>Scripts de consulta adicionais para cenários de dados de viagem de táxi
Exemplos de consultas que são específicas para cenários de [Dados de Viagens de Táxi em NYC](https://chriswhong.com/open-data/foil_nyc_taxi/) também são fornecidos no [repositório GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). Essas consultas já tem o esquema de dados especificado e estão prontas para ser enviadas para execução.