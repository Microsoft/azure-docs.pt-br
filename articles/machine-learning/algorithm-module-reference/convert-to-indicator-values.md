---
title: Converter em valores de indicador
titleSuffix: Azure Machine Learning
description: Use o módulo converter para valores de indicador no designer de Azure Machine Learning para converter colunas categóricas em uma série de colunas de indicador binário.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 81b3c113f46428327842c1555fdd1934e9ae8762
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93420845"
---
# <a name="convert-to-indicator-values"></a>Converter em valores de indicador
Este artigo descreve um módulo do designer de Azure Machine Learning.

Use o módulo **converter para valores de indicador** no designer de Azure Machine Learning para converter colunas que contêm valores categóricos em uma série de colunas de indicador binário.  

Esse módulo também gera uma definição da transformação usada para converter para valores de indicador. Você pode reutilizar essa transformação em outros conjuntos de valores que têm o mesmo esquema, usando o módulo [aplicar transformação](apply-transformation.md) .

## <a name="how-to-configure-convert-to-indicator-values"></a>Como configurar converter para valores de indicador

1.  Localize a **conversão para valores de indicador** e arraste-o para o rascunho do seu pipeline. Você pode encontrar esse módulo na categoria **Data Transformation** .
    > [!NOTE]
    > Você pode usar o módulo [Editar metadados](edit-metadata.md) antes do módulo **converter para valores Indiciator** para marcar as colunas de destino como categóricas.

1. Conecte o módulo **converter para valores de indicador** ao conjunto de um que contém as colunas que você deseja converter. 

1. Selecione **Editar coluna** para escolher uma ou mais colunas categóricas.

1. Selecione a opção **substituir colunas categóricas** se desejar gerar **apenas** as novas colunas booleanas. Por padrão, essa opção é desativada.
    

    > [!TIP]
    >  Se você escolher a opção de substituir, a coluna de origem não será realmente excluída nem modificada. Em vez disso, as novas colunas são geradas e apresentadas no conjunto de resultados de saída, e a coluna de origem permanece disponível no espaço de trabalho. Se você precisar ver os dados originais, poderá usar o módulo [adicionar colunas](add-columns.md) a qualquer momento para adicionar a coluna de origem novamente.

1. Enviar o pipeline.

## <a name="results"></a>Resultados

Suponha que você tenha uma coluna com pontuações que indicam se um servidor tem uma probabilidade alta, média ou baixa de falha.  

| ID de servidor | Pontuação de falha |
| --------- | ------------- |
| 10301     | Baixo           |
| 10302     | Médio        |
| 10303     | Alta          |

Quando você aplica **converter a valores de indicador**, o designer converte uma única coluna de rótulos em várias colunas contendo valores Boolianos:  

| ID de servidor | Pontuação de falha-baixa | Pontuação de falha-média | Pontuação de falha-alta |
| --------- | ------------------- | ---------------------- | -------------------- |
| 10301     | 1                   | 0                      | 0                    |
| 10302     | 0                   | 1                      | 0                    |
| 10303     | 0                   | 0                      | 1                    |

Veja como a conversão funciona:  

-   Na coluna **Pontuação de falha** que descreve o risco, há apenas três valores possíveis (alto, médio e baixo) e nenhum valor ausente. Portanto, exatamente três novas colunas são criadas.  

-   As novas colunas de indicador são nomeadas com base nos títulos de coluna e valores da coluna de origem, usando esse padrão: *\<source column>- \<data value>* .  

-   Deve haver uma coluna de indicador 1 em exatamente uma e 0 em todas as outras colunas de indicador, já que cada servidor pode ter apenas uma classificação de risco.  

Agora você pode usar as três colunas de indicador como recursos em um modelo de aprendizado de máquina.

O módulo retorna duas saídas:

- **Conjunto de resultados**: um conjunto de linhas com colunas de valores de indicador convertidos. As colunas não selecionadas para limpeza também são "passadas".
- **Transformação de valores de indicador**: uma transformação de dados usada para converter para valores de indicador, que pode ser salva em seu espaço de trabalho e aplicada a novos dados posteriormente.

## <a name="apply-a-saved-indicator-values-operation-to-new-data"></a>Aplicar uma operação de valores de indicadores salvos a novos dados

Se você precisar repetir as operações de valores de indicador com frequência, você poderá salvar as etapas de manipulação de dados como uma *transformação* para reutilizá-la com o mesmo conjunto. Isso será útil se você precisar reimportar com frequência e limpar os dados que têm o mesmo esquema.

1. Adicione o módulo [aplicar transformação](apply-transformation.md) ao seu pipeline.

1. Adicione o DataSet que você deseja limpar e conecte o conjunto de dados à porta de entrada à direita.

1. Expanda o grupo **transformação de dados** no painel esquerdo do designer. Localize a transformação salva e arraste-a para o pipeline.

1. Conecte a transformação salva à porta de entrada à esquerda de [aplicar transformação](apply-transformation.md).

   Ao aplicar uma transformação salva, você não pode selecionar quais colunas transformar. Isso ocorre porque a transformação foi definida e se aplica automaticamente aos tipos de dados especificados na operação original.

1. Envie o pipeline.
 
## <a name="technical-notes"></a>Observações técnicas  

Esta seção contém detalhes de implementação, dicas e respostas para perguntas frequentes.

### <a name="usage-tips"></a>Dicas de uso

-   Somente as colunas que são marcadas como categóricas podem ser convertidas em colunas de indicador. Se você vir o erro a seguir, é provável que uma das colunas selecionadas não seja categórica:  

     Erro 0056: a coluna com  \<column name> o nome não está em uma categoria permitida.  

     Por padrão, a maioria das colunas de cadeia de caracteres são tratadas como recursos de cadeia de caracteres, portanto, você deve marcá-las explicitamente como categóricos usando [Editar metadados](edit-metadata.md).  

-   Não há limite para o número de colunas que você pode converter em colunas de indicador. No entanto, como cada coluna de valores pode produzir várias colunas de indicador, talvez você queira converter e examinar apenas algumas colunas de cada vez.  

-   Se a coluna contiver valores ausentes, uma coluna de indicador separada será criada para a categoria ausente, com este nome: *\<source column> -ausente*  

-   Se a coluna que você converter em valores de indicador contiver números, elas deverão ser marcadas como categóricas como qualquer outra coluna de recurso. Depois de fazer isso, os números são tratados como valores discretos. Por exemplo, se você tiver uma coluna numérica com valores MPG variando de 25 a 30, uma nova coluna de indicador será criada para cada valor discreto:  

    | Faça       | Rodovia MPG-25 | Rodovia MPG-26 | Rodovia MPG-27 | Rodovia MPG-28 | Rodovia MPG-29 | Rodovia MPG-30 |
    | ---------- | --------------- | --------------- | --------------- | --------------- | --------------- | --------------- |
    | Carros da contoso | 0               | 0               | 0               | 0               | 0               | 1               |

- Para evitar a adição de muitas dimensões ao conjunto de seus conjuntos de seus. É recomendável primeiro verificar o número de valores na coluna e, em seguida, ou quantificar os dados adequadamente.  


## <a name="next-steps"></a>Próximas etapas

Confira o [conjunto de módulos disponíveis](module-reference.md) no Azure Machine Learning. 
