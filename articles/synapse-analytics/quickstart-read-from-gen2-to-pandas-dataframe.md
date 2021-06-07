---
title: 'Início Rápido: Ler os dados do ADLS Gen2 para o dataframe do Pandas'
description: Leia dados de uma conta do Azure Data Lake Storage Gen2 em um dataframe do Pandas usando Python no Synapse Studio no Azure Synapse Analytics.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: quickstart
ms.reviewer: jrasnick, garye, negust
ms.date: 03/23/2021
author: garyericson
ms.author: garye
ms.openlocfilehash: b7358c522cf12e7856496ad71fda393394e7ceab
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105969564"
---
# <a name="quickstart-read-data-from-adls-gen2-to-pandas-dataframe-in-azure-synapse-analytics"></a>Início Rápido: Ler os dados do ADLS Gen2 para o dataframe do Pandas no Azure Synapse Analytics

Neste guia de início rápido, você aprenderá a usar facilmente o Python para ler dados de um ADLS (Azure Data Lake Storage) Gen2 em um dataframe do Pandas no Azure Synapse Analytics.

Em um notebook do Synapse Studio, você vai:

- conectar-se a um contêiner no Data Lake Storage Gen2 que está vinculado ao seu workspace do Azure Synapse Analytics
- ler os dados de um notebook PySpark usando `spark.read.load`
- converter os dados em um dataframe do Pandas usando `.toPandas()`

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura do Azure – [crie uma gratuitamente](https://azure.microsoft.com/free/).
- Workspace do Synapse Analytics com Data Lake Storage Gen2 configurado como o armazenamento padrão – você precisa ser o **colaborador de dados do blob de armazenamento** do sistema de arquivos do Data Lake Storage Gen2 com o qual você trabalha. Para obter detalhes sobre como criar um workspace, confira [Como criar um workspace do Azure Synapse](get-started-create-workspace.md).
- Pool do Apache Spark em seu workspace – confira [Criar um pool do Apache Spark sem servidor](get-started-analyze-spark.md#create-a-serverless-apache-spark-pool).

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [portal do Azure](https://portal.azure.com/).

## <a name="upload-sample-data-to-adls-gen2"></a>Carregar dados de exemplo no ADLS Gen2

1. No portal do Azure, crie um contêiner no mesmo Data Lake Storage Gen2 usado pelo Synapse Studio. Você pode ignorar esta etapa se quiser usar a conta de armazenamento vinculada padrão em seu workspace do Azure Synapse Analytics.

1. No Synapse Studio, clique em **Dados**, selecione a guia **Vinculado** e selecione o contêiner em **Azure Data Lake Storage Gen2**.

1. Baixe o arquivo de exemplo [RetailSales.csv](https://github.com/Azure-Samples/Synapse/blob/main/Notebooks/PySpark/Synapse%20Link%20for%20Cosmos%20DB%20samples/Retail/RetailData/RetailSales.csv) e carregue-o no contêiner.

1. Selecione o arquivo carregado, clique em **Propriedades** e copie o valor do **caminho ABFSS**.

## <a name="read-data-from-adls-gen2-into-a-pandas-dataframe"></a>Ler os dados do ADLS Gen2 em um dataframe do Pandas

1. No painel esquerdo, clique em **Desenvolver**.

1. Clique em **+** e selecione "Notebook" para criar um notebook.

1. Em **Anexar a**, selecione o Pool do Apache Spark. Se você não tiver um, clique em **Criar pool do Apache Spark**.

1. Na célula de código do notebook, cole o seguinte código Python, inserindo o caminho ABFSS que você copiou anteriormente:

   ```python
   %%pyspark
   data_path = spark.read.load('<ABFSS Path to RetailSales.csv>', format='csv', header=True)
   data_path.show(10)
   
   print('Converting to Pandas.')
   
   pdf = data_path.toPandas()
   print(pdf)
   ```

1. Execute a célula.

Depois de alguns minutos, o texto exibido deve ter a aparência a seguir.

```text
Command executed in 25s 324ms by gary on 03-23-2021 17:40:23.481 -07:00

Job execution Succeeded Spark 2 executors 8 cores

+-------+-----------+--------+-----------+-----------+-----+------------+--------------------+
|storeId|productCode|quantity|logQuantity|advertising|price|weekStarting|                  id|
+-------+-----------+--------+-----------+-----------+-----+------------+--------------------+
|      2| surface.go|     105|9.264828557|          1|  159|   6/15/2017|d6bd47a7-2ad6-4f0...|
|      2| surface.go|      80|8.987196821|          0|  269|   7/27/2017|64cc74c2-c7da-4e1...|
|      2| surface.go|      68|8.831711918|          1|  209|    8/3/2017|9a2d164b-5e44-44d...|
|      2| surface.go|      28|7.965545573|          0|  209|   8/10/2017|b8cd9987-1d5a-4f4...|
|      2| surface.go|      16|7.377758908|          0|  209|   8/24/2017|ac0ec099-e102-4bf...|
|      2| surface.go|     253| 10.1402973|          1|  189|   8/31/2017|3d22c002-b04c-409...|
|      2| surface.go|     107|9.282847063|          0|  189|    9/7/2017|b6e19699-d684-449...|
|      2| surface.go|      66|8.803273983|          0|  189|   9/14/2017|e89a5838-fb8f-413...|
|      2| surface.go|      65|8.793612072|          0|  179|   9/21/2017|c3278682-16c0-483...|
|      2| surface.go|      17|7.454719949|          0|  269|  10/12/2017|f40190c1-b2ed-46f...|
+-------+-----------+--------+-----------+-----------+-----+------------+--------------------+
only showing top 10 rows

Converting to Pandas.
      storeId  ...                                    id
0           2  ...  d6bd47a7-2ad6-4f0a-b8de-ed1386cae5ea
1           2  ...  64cc74c2-c7da-4e12-af64-c95bdf429934
2           2  ...  9a2d164b-5e44-44d7-9837-cf9ae6566c99
3           2  ...  b8cd9987-1d5a-4f4f-9346-719d73b1f7f0
4           2  ...  ac0ec099-e102-4bfc-9775-983b151dcd03
...       ...  ...                                   ...
28942     137  ...  6af00133-7015-415d-831b-ddf05bb5828c
28943     137  ...  1e0d3a21-ab43-49c4-89e2-49d202821807
28944     137  ...  5cc7e50a-6aa4-419b-a933-905a667aa2df
28945     137  ...  650ca506-7a4f-46f8-b2e1-e52ceffadf16
28946     137  ...  9bb216f6-04ec-4b61-9e68-34772b814c44

[28947 rows x 8 columns]
```

## <a name="next-steps"></a>Próximas etapas

- [O que é o Azure Synapse Analytics?](overview-what-is.md)
- [Introdução ao Azure Synapse Analytics](get-started.md)
- [Criar um Pool do Apache Spark sem servidor](get-started-analyze-spark.md#create-a-serverless-apache-spark-pool)
