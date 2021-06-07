---
title: Entenda Apache Spark para Azure Data Lake Analytics desenvolvedores U-SQL.
description: Este artigo descreve Apache Spark conceitos para ajudá-lo a diferenças entre os desenvolvedores de U-SQL.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.custom: understand-apache-spark-for-usql-developers
ms.date: 10/15/2019
ms.openlocfilehash: a66a82a6d2a5bb1f534ed211091793b2ab4d2a28
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92221087"
---
# <a name="understand-apache-spark-for-u-sql-developers"></a>Noções básicas do Apache Spark para desenvolvedores do U-SQL

A Microsoft dá suporte a vários serviços de análise, como o [Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks) e o [Azure HDInsight](../hdinsight/hdinsight-overview.md) , bem como Azure data Lake Analytics. Ouvimos os desenvolvedores de que eles têm uma clara preferência para soluções de software livre à medida que criam pipelines de análise. Para ajudar os desenvolvedores do U-SQL a entender Apache Spark e como você pode transformar seus scripts U-SQL em Apache Spark, criamos essas diretrizes.

Ele inclui uma série de etapas que você pode executar e várias alternativas.

## <a name="steps-to-transform-u-sql-to-apache-spark"></a>Etapas para transformar o U-SQL em Apache Spark

1. Transforme seus pipelines de orquestração de trabalho.

   Se você usar [Azure data Factory](../data-factory/introduction.md) para orquestrar seus scripts de Azure data Lake Analytics, você precisará ajustá-los para orquestrar os novos programas do Spark.
2. Entenda as diferenças entre o modo como o U-SQL e o Spark gerenciam dados

   Se desejar mover seus dados de [Azure data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) para [Azure data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md), você precisará copiar os dados do arquivo e o catálogo os dados mantidos. Observe que Azure Data Lake Analytics só oferece suporte a Azure Data Lake Storage Gen1. Veja [entender os formatos de dados do Spark](understand-spark-data-formats.md)
3. Transformar seus scripts U-SQL no Spark

   Antes de transformar seus scripts U-SQL, você precisará escolher um serviço de análise. Alguns dos serviços de computação disponíveis estão disponíveis:
      - [Fluxo de Azure data Factory](../data-factory/concepts-data-flow-overview.md) Mapear fluxos de dados são transformações de dados visualmente projetadas que permitem que os engenheiros de dados desenvolvam uma lógica de transformação de dados gráficas sem escrever código. Embora não seja adequado para executar um código de usuário complexo, eles podem representar facilmente transformações de Dataflow tradicionais como o SQL
      - [Hive do Azure HDInsight](../hdinsight/hadoop/apache-hadoop-using-apache-hive-as-an-etl-tool.md) Apache Hive no HDInsight é adequado para operações de extração, transformação e carregamento (ETL). Isso significa que você vai converter seus scripts U-SQL para Apache Hive.
      - Apache Spark mecanismos como [Azure HDInsight Spark](../hdinsight/spark/apache-spark-overview.md) ou [Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks) isso significa que você converterá seus scripts U-SQL no Spark. Para obter mais informações, consulte [entender os formatos de dados do Spark](understand-spark-data-formats.md)

> [!CAUTION]
> Tanto [Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks) quanto [Azure HDInsight Spark](../hdinsight/spark/apache-spark-overview.md) são serviços de cluster e não são trabalhos sem servidor como Azure data Lake Analytics. Você precisará considerar como provisionar os clusters para obter a taxa de custo/desempenho apropriada e como gerenciar seu tempo de vida para minimizar os custos. Esses serviços têm características de desempenho diferentes com o código do usuário escrito em .NET, portanto, você terá que gravar wrappers ou reescrever seu código em um idioma com suporte. Para obter mais informações, consulte [entender os formatos de dados do Spark](understand-spark-data-formats.md), [entender os conceitos de Apache Spark de código para desenvolvedores do U-SQL](understand-spark-code-concepts.md), [.net para Apache Spark](https://dotnet.microsoft.com/apps/data/spark)

## <a name="next-steps"></a>Próximas etapas

- [Entender os formatos de dados do Spark para desenvolvedores do U-SQL](understand-spark-data-formats.md)
- [Entender os conceitos de código do Spark para desenvolvedores do U-SQL](understand-spark-code-concepts.md)
- [Atualize suas soluções de análise de Big Data do Azure Data Lake Storage Gen1 para o Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-migrate-gen1-to-gen2.md)
- [.NET para Apache Spark](/dotnet/spark/what-is-apache-spark-dotnet)
- [Transformar dados usando a atividade do Hive do Hadoop no Azure Data Factory](../data-factory/transform-data-using-hadoop-hive.md)
- [Transformar dados usando a atividade do Spark no Azure Data Factory](../data-factory/transform-data-using-spark.md)
- [O que é o Apache Spark no Azure HDInsight](../hdinsight/spark/apache-spark-overview.md)