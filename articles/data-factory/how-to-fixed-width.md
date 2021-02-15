---
title: Processar arquivos de texto de comprimento fixo com fluxos de dados de mapeamento em Azure Data Factory
description: Saiba como processar arquivos de texto de comprimento fixo em Azure Data Factory usando fluxos de dados de mapeamento.
author: balakreshnan
ms.service: data-factory
ms.topic: conceptual
ms.date: 8/18/2019
ms.author: makromer
ms.openlocfilehash: 2297364e8f1a531b4512f2497bc4d99963d61179
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100386196"
---
# <a name="process-fixed-length-text-files-by-using-data-factory-mapping-data-flows"></a>Processar arquivos de texto de comprimento fixo usando fluxos de dados de mapeamento Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ao usar o mapeamento de fluxos de dados em Microsoft Azure Data Factory, você pode transformar dados de arquivos de texto de largura fixa. Na tarefa a seguir, definiremos um conjunto de um DataSet para um arquivo de texto sem um delimitador e, em seguida, configuraremos divisões de subcadeias com base na posição ordinal.

## <a name="create-a-pipeline"></a>Criar um pipeline

1. Selecione **+ novo pipeline** para criar um novo pipeline.

2. Adicione uma atividade de fluxo de dados, que será usada para processar arquivos de largura fixa:

    ![Pipeline de largura fixa](media/data-flow/fwpipe.png)

3. Na atividade fluxo de dados, selecione **novo mapeamento fluxo de dados**.

4. Adicione uma transformação de origem, coluna derivada, seleção e coletor:

    ![Fluxo de dados de largura fixa](media/data-flow/fw2.png)

5. Configure a transformação origem para usar um novo conjunto de um, que será do tipo de texto delimitado.

6. Não defina nenhum delimitador de coluna ou cabeçalhos.

   Agora, vamos definir pontos de início de campo e comprimentos para o conteúdo deste arquivo:

    ```
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    ```

7. Na guia **projeção** da transformação origem, você deverá ver uma coluna de cadeia de caracteres denominada *column_1*.

8. Na coluna derivada, crie uma nova coluna.

9. Forneceremos nomes simples de colunas como *Col1*.

10. No construtor de expressões, digite o seguinte:

    ```substring(Column_1,1,4)```

    ![coluna derivada](media/data-flow/fwderivedcol1.png)

11. Repita a etapa 10 para todas as colunas que você precisa analisar.

12. Selecione a guia **inspecionar** para ver as novas colunas que serão geradas:

    ![Quanto](media/data-flow/fwinspect.png)

13. Use a transformação selecionar transformar para remover qualquer uma das colunas que você não precisa para a transformação:

    ![selecionar transformação](media/data-flow/fwselect.png)

14. Use o coletor para gerar os dados para uma pasta:

    ![coletor de largura fixa](media/data-flow/fwsink.png)

    Veja abaixo a aparência da saída:

    ![saída de largura fixa](media/data-flow/fxdoutput.png)

  Os dados de largura fixa agora são divididos, com quatro caracteres cada e atribuídos a Col1, Col2, Col3, COL4 e assim por diante. Com base no exemplo anterior, os dados são divididos em quatro colunas.

## <a name="next-steps"></a>Próximas etapas

* Compile o restante da lógica de fluxo de dados usando as [transformações](concepts-data-flow-overview.md)de fluxos de dados de mapeamento.
