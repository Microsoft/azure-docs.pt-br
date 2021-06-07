---
title: Usar fluxos de dados para processar dados de modelos de aprendizado automático de máquina (AutoML)
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
ms.openlocfilehash: 45cd44cc0678b7f3a006a88bf66be2bca091af76
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104595372"
---
# <a name="process-data-from-automated-machine-learning-models-by-using-data-flows"></a>Processar dados de modelos de aprendizado de máquina automatizados usando fluxos de dados

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

O AutoML (Machine Learning automatizado) é adotado por projetos de Machine Learning para treinar, ajustar e obter os melhores modelos automaticamente usando as métricas de destino que você especifica para classificação, regressão e previsão de série temporal.

Um desafio para o AutoML é que os dados brutos de um data warehouse ou de um banco de dados transacional seriam um conjunto enorme, possivelmente 10 GB. Um conjunto de dados grande requer um tempo maior para treinar modelos, portanto, recomendamos que você otimize o processamento de dados antes de treinar os modelos de Azure Machine Learning. Este tutorial abordará como usar Azure Data Factory para particionar um conjunto de um DataSet em arquivos AutoML para um conjunto de Machine Learning.

O projeto AutoML inclui os três cenários de processamento de dados a seguir:

* Particione dados grandes para arquivos AutoML antes de treinar modelos.

     O [quadro de dados pandas](https://pandas.pydata.org/pandas-docs/stable/getting_started/overview.html) é comumente usado para processar dados antes de treinar modelos. O quadro de dados pandas funciona bem para tamanhos de dados menores que 1 GB, mas se os dados forem maiores que 1 GB, um quadro de dados pandas reduzirá os dados do processo. Às vezes, você pode até obter uma mensagem de erro de memória insuficiente. É recomendável usar um formato de [arquivo parquet](https://parquet.apache.org/) para o aprendizado de máquina porque ele é um formato binário de coluna.
    
     Fluxos de dados de mapeamento de Data Factory são transformações de dados projetadas visualmente que liberam engenheiros de dados de escrever código. O mapeamento de fluxos de dados é uma maneira eficiente de processar dados grandes, pois o pipeline usa clusters Spark expandidos.

* Divida o conjunto de testes de treinamento e o conjunto de teste.
    
    O conjunto de os de treinamento será usado para um modelo de treinamento. O conjunto de teste será usado para avaliar modelos em um projeto de Machine Learning. A atividade de divisão condicional para mapear fluxos de dados dividiria dados de treinamento e dados de teste.

* Remover dados não qualificados.

    Talvez você queira remover dados não qualificados, como um arquivo parquet com zero linhas. Neste tutorial, usaremos a atividade de agregação para obter uma contagem do número de linhas. A contagem de linhas será uma condição para remover dados não qualificados.

## <a name="preparation"></a>Preparação

Use a tabela de banco de dados SQL do Azure a seguir.

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

O fluxo de dados a seguir converterá uma tabela de banco de dado SQL em um formato de arquivo parquet:

- **DataSet de origem**: tabela de transação do banco de dados SQL.
- **Conjunto de coleta**: armazenamento de BLOBs com formato parquet.

## <a name="remove-unqualified-data-based-on-row-count"></a>Remover dados não qualificados com base na contagem de linhas

Vamos supor que precisamos remover uma contagem de linhas menor que duas.

1. Use a atividade de agregação para obter uma contagem do número de linhas. Use **agrupado por** com base em Col2 e **agregações** com `count(1)` para a contagem de linhas.

    ![Captura de tela que mostra a configuração da atividade de agregação para obter uma contagem do número de linhas.](./media/scenario-dataflow-process-data-aml-models/aggregate-activity-addrowcount.png)

1. Usando a atividade do coletor, selecione o **tipo de coletor** como **cache** na guia **coletor** . Em seguida, selecione a coluna desejada na lista suspensa **colunas de chave** na guia **configurações** .

    ![Captura de tela que mostra a configuração da atividade CacheSink para obter uma contagem do número de linhas em um coletor armazenado em cache.](./media/scenario-dataflow-process-data-aml-models/cachesink-activity-addrowcount.png)

1. Use a atividade coluna derivada para adicionar uma coluna de contagem de linhas no fluxo de origem. Na guia **configurações da coluna derivada** , use a `CacheSink#lookup` expressão para obter uma contagem de linhas de CacheSink.

    ![Captura de tela que mostra a configuração da atividade de coluna derivada para adicionar uma contagem do número de linhas em origem1.](./media/scenario-dataflow-process-data-aml-models/derived-column-activity-rowcount-source-1.png)

1. Use a atividade de divisão condicional para remover dados não qualificados. Neste exemplo, a contagem de linhas é baseada na coluna Col2. A condição é remover uma contagem de linhas menor que duas, portanto, duas linhas (ID = 2 e ID = 7) serão removidas. Você salvaria dados não qualificados no armazenamento de BLOBs para o gerenciamento de dados.

    ![Captura de tela que mostra a configuração da atividade Split condicional para obter dados maiores ou iguais a dois.](./media/scenario-dataflow-process-data-aml-models/conditionalsplit-greater-or-equal-than-2.png)

> [!NOTE]
>    * Crie uma nova fonte para obter uma contagem do número de linhas que serão usadas na fonte original em etapas posteriores.
>    * Use o CacheSink de um ponto de vista de desempenho.

## <a name="split-training-data-and-test-data"></a>Dividir dados de treinamento e dados de teste

Queremos dividir os dados de treinamento e testar os dados para cada partição. Neste exemplo, para o mesmo valor de Col2, obtenha as duas primeiras linhas como dados de teste e o restante das linhas como dados de treinamento.

1. Use a atividade janela para adicionar um número de linha de coluna para cada partição. Na guia **acima** , selecione uma coluna para partição. Neste tutorial, vamos particionar para Col2. Dê um pedido na guia **classificar** , que neste tutorial será baseado na ID. Dê um pedido na guia **colunas da janela** para adicionar uma coluna como um número de linha para cada partição.

    ![Captura de tela que mostra a configuração da atividade de janela para adicionar uma nova coluna com número de linha.](./media/scenario-dataflow-process-data-aml-models/window-activity-add-row-number.png)

1. Use a atividade de divisão condicional para dividir as duas primeiras linhas de cada partição no conjunto de teste e no restante das linhas no conjunto de testes de treinamento. Na guia **configurações de divisão condicional** , use a expressão `lesserOrEqual(RowNum,2)` como a condição.

    ![Captura de tela que mostra a configuração da atividade Split condicional para dividir o conjunto de conjuntos atual no conjunto de testes e no conjunto de teste.](./media/scenario-dataflow-process-data-aml-models/split-training-dataset-test-dataset.png)

## <a name="partition-the-training-and-test-datasets-with-parquet-format"></a>Particionar os conjuntos de testes de treinamento e de teste com o formato parquet

Usando a atividade Sink, na guia **otimizar** , use **valor exclusivo por partição** para definir uma coluna como chave de coluna para partição.

![Captura de tela que mostra a configuração da atividade do coletor para definir a partição do conjunto de programas de treinamento.](./media/scenario-dataflow-process-data-aml-models/partition-training-dataset-sink.png)

Vamos examinar toda a lógica do pipeline.

![Captura de tela que mostra a lógica de todo o pipeline.](./media/scenario-dataflow-process-data-aml-models/entire-pipeline.png)

## <a name="next-steps"></a>Próximas etapas

Compile o restante da lógica de fluxo de dados usando [transformações](concepts-data-flow-overview.md)de fluxo de dados de mapeamento.
