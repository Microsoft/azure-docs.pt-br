---
title: Usar Dataflow para processar dados de modelos de aprendizado de máquina automatizado (AutoML)
description: Saiba como usar Azure Data Factory fluxos de dados para processar dados de modelos de aprendizado automático de máquina (AutoML).
services: data-factory
author: amberz
co-author: ATLArcht
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 1/31/2021
ms.author: amberz
ms.co-author: Donnana
ms.openlocfilehash: 494fc2c227b6fe855e96a780d43cc6702722fa94
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100520857"
---
# <a name="process-data-from-automated-machine-learningautoml-models-using-data-flow"></a>Processar dados de modelos AutoML (Machine Learning automatizados) usando o fluxo de dados

O AutoML (Machine Learning automatizado) é adotado por projetos de Machine Learning para treinar, ajustar e obter o melhor modelo automaticamente usando a métrica de destino que você especifica para classificação, regressão e previsão de série temporal. 

Um desafio é que os dados brutos de data warehouse ou de um banco de dado transacional seriam um conjunto enorme de conjuntos, como: 10 GB, o conjunto de dados grande requer tempo maior para treinar modelos, portanto, é recomendável otimizar o processamento de dado antes de treinar modelos de Azure Machine Learning. Este tutorial abordará como usar o ADF para particionar o conjunto de espaço de parquet para os arquivos do conjunto de Azure Machine Learning. 

No projeto AutoML (Machine Learning automatizado), ele aplicaria os três cenários de processamento de dados a seguir:

* Particione dados grandes para arquivos parquet antes de modelos de treinamento. 

     O [quadro de dados do pandas](https://pandas.pydata.org/pandas-docs/stable/getting_started/overview.html) geralmente é usado para processar dados antes dos modelos de treinamento. O quadro de dados do pandas funciona bem para tamanhos de dados menores que 1 GB, mas se os dados forem grandes do que 1GB, o quadro de dados pandas ficará lento para processar dados, um pouco até mesmo receberá a mensagem de erro de memória. Os formatos de [arquivo parquet](https://parquet.apache.org/) são recomendados para o aprendizado de máquina, pois ele é um formato binário de coluna.
    
    Os fluxos de dados de mapeamento das fábricas de dados do Azure são transformações de dados visualmente projetadas com código livre para engenheiros de dados. É poderoso processar dados grandes, pois o pipeline usa clusters Spark expandidos.

* Conjunto de DataSet de treinamento e conjunto de teste.
    
    O conjunto de informações de treinamento será usado para o modelo de treinamento, o conjunto de testes será usado para avaliar modelos no projeto do Machine Learning. O mapeamento de atividade de divisão condicional de fluxos de dados dividiria dados de treinamento e dados de teste. 

* Remover dados não qualificados.

    Talvez você queira remover dados não qualificados, como: arquivo parquet com zero linha. Neste tutorial, usaremos a atividade de agregação para obter números de contagem de linhas, a contagem de linhas será uma condição para remover dados não qualificados. 


## <a name="preparation"></a>Preparação
Use a tabela a seguir do banco de dados SQL do Azure. 
```
CREATE TABLE [dbo].[MyProducts](
    [ID] [int] NULL,
    [Col1] [char](124) NULL,
    [Col2] [char](124) NULL,
    [Col3] datetime NULL,
    [Col4] int NULL

) 

```

## <a name="convert-data-format-to-parquet"></a>Converter o formato de dados em parquet

O fluxo de dados converterá uma tabela do banco de dado SQL do Azure no formato de arquivo parquet. 

**Conjunto** de dados de origem: tabela de transações do banco de dados SQL do Azure

**Conjunto de coleta**: armazenamento de BLOBs com formato parquet


## <a name="remove-unqualified-data-based-on-row-count"></a>Remover dados não qualificados com base na contagem de linhas

Vamos supor que você remova a contagem de linhas menor que 2. 

1. Use a atividade de agregação para obter o número de contagem de linhas: **Agrupar por** com base em Col2 e **agregações** com Count (1) para contagem de linhas. 

    ![configurar a atividade de agregação para obter o número da contagem de linhas](./media/scenario-dataflow-process-data-aml-models/aggregate-activity-addrowcount.png)

1. Use a atividade do coletor, escolha **tipo de coletor** como cache na guia **coletor** e escolha a coluna desejada na lista suspensa **colunas de chave** na guia **configurações** . 

    ![configurar a atividade CacheSink para obter o número da contagem de linhas no coletor em cache](./media/scenario-dataflow-process-data-aml-models/cachesink-activity-addrowcount.png)

1. Use a atividade de coluna derivada para adicionar a coluna de contagem de linhas no fluxo de origem. Na guia **configurações da coluna derivada** , use a expressão de pesquisa CacheSink # obtendo a contagem de linhas de SinkCache.
    ![configurar a atividade de coluna derivada para adicionar o número de contagem de linhas na origem 1](./media/scenario-dataflow-process-data-aml-models/derived-column-activity-rowcount-source-1.png)

1. Use a atividade de divisão condicional para remover dados não qualificados. Neste exemplo, contagem de linhas com base na coluna Col2 e a condição é remover a contagem de linhas menor que 2, portanto, duas linhas (ID = 2 e ID = 7) serão removidas. Você salvaria dados não qualificados em um armazenamento de BLOBs para gerenciamento de dados. 

    ![configurar a atividade de divisão condicional para obter dados que sejam maiores ou iguais a 2](./media/scenario-dataflow-process-data-aml-models/conditionalsplit-greater-or-equal-than-2.png)

> [!NOTE]
>    *    Crie uma nova fonte para obter o número da contagem de linhas que serão usadas na fonte original em etapas posteriores. 
>    *    Use CacheSink do ponto de vista do desempenho. 

## <a name="split-training-data-and-test-data"></a>Dividir dados de treinamento e dados de teste 

1. Queremos dividir dados de treinamento e testar dados para cada partição. Neste exemplo, para o mesmo valor de Col2, obtenha as duas primeiras linhas como dados de teste e as linhas REST como dados de treinamento. 

    Use a atividade de janela para adicionar um número de linha de coluna para cada partição. Na **guia** , escolha coluna para partição (neste tutorial, será particionado para Col2), faça ordem na guia **classificar** (neste tutorial, será baseado na ID para ordem) e na guia **colunas da janela** para adicionar uma coluna como número de linha para cada partição. 
    ![configurar a atividade de janela para adicionar uma nova coluna com número de linha](./media/scenario-dataflow-process-data-aml-models/window-activity-add-row-number.png)

1. Use a atividade Split condicional para dividir as duas linhas principais da partição para o conjunto de teste e as linhas REST para treinar o conjunto de registros. Na guia **configurações de divisão condicional** , use a expressão LesserOrEqual (rownum, 2) como condição. 

    ![configurar a atividade de divisão condicional para dividir o conjunto de conjuntos atual em conjunto de teste e conjunto de testes](./media/scenario-dataflow-process-data-aml-models/split-training-dataset-test-dataset.png)

## <a name="partition-training-dataset-and-test-dataset-with-parquet-format"></a>DataSet de treinamento de partição e conjunto de teste com o formato parquet

1. Use a atividade Sink, na guia **otimizar** , usando **valor exclusivo por partição** para definir uma coluna como chave de coluna para partição. 
    ![configurar atividade de coletor para definir a partição de conjunto de](./media/scenario-dataflow-process-data-aml-models/partition-training-dataset-sink.png)

    Vamos examinar toda a lógica do pipeline.
    ![A lógica do pipeline inteiro](./media/scenario-dataflow-process-data-aml-models/entire-pipeline.png)


## <a name="next-steps"></a>Próximas etapas

* Compile o restante da lógica de fluxo de dados usando as [transformações](concepts-data-flow-overview.md)de fluxos de dados de mapeamento.
