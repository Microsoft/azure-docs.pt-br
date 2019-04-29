---
title: Transformação da janela Fluxo de Dados de Mapeamento do Azure Data Factory
description: Transformação da janela Fluxo de Dados de Mapeamento do Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 6f3f06ff54fc76416ba63f4f09835897d546f8dc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61349604"
---
# <a name="azure-data-factory-window-transformation"></a>Transformação de janela do Azure Data Factory

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

A Transformação de janela é o local em que você definirá agregações baseadas em janela de colunas em seus fluxos de dados. No Construtor de Expressões, é possível definir diferentes tipos de agregações baseadas em janelas de dados ou de tempo (cláusula SQL OVER) como LEAD, LAG, NTILE, CUMEDIST, RANK, etc.). Um novo campo será gerado na saída que inclui essas agregações. Também é possível incluir campos opcionais agrupar por.

![Opções da janela](media/data-flow/windows1.png "janelas 1")

## <a name="over"></a>Acima
Defina o particionamento dos dados da coluna para sua transformação de janela. O SQL equivalente é o ```Partition By``` na cláusula Over no SQL. Se desejar criar um cálculo ou uma expressão a ser usada para o particionamento, será possível fazer isso passando o mouse sobre o nome da coluna e selecionando “coluna computada”.

![Opções da janela](media/data-flow/windows4.png "janelas 4")

## <a name="sort"></a>Classificar
Outra parte da cláusula Over é a configuração de ```Order By```. Isso definirá a ordem de classificação dos dados. Também é possível criar uma expressão para um valor de cálculo nesse campo de coluna para classificação.

![Opções da janela](media/data-flow/windows5.png "janelas 5")

## <a name="range-by"></a>Ordenar por
Em seguida, defina o quadro de janela como Não associado ou Associado. Para definir um quadro de janela não associado, defina o controle deslizante como Não associado em ambas as extremidades. Se você escolher uma configuração entre a Linha não associada e a atual, deverá definir os valores de início e término do deslocamento. Os dois valores serão inteiros positivos. É possível usar números ou valores relativos dos seus dados.

O controle deslizante da janela tem dois valores a serem definidos: os valores antes da linha atual e os valores depois da linha atual. O deslocamento inicial e final corresponde aos dois seletores no controle deslizante.

![Opções da janela](media/data-flow/windows6.png "janelas 6")

## <a name="window-columns"></a>Colunas da janela
Por fim, use o Construtor de Expressões para definir as agregações que você deseja usar com as janelas de dados como RANK, COUNT, MIN, MAX, DENSE RANK, LEAD, LAG, etc.

![Opções da janela](media/data-flow/windows7.png "janelas 7")

A lista completa de funções analíticas e de agregação disponível para você usar na Linguagem de Expressão do Fluxo de Dados do ADF por meio do Construtor de Expressões está listada aqui: https://aka.ms/dataflowexpressions.

## <a name="next-steps"></a>Próximas etapas

Se você estiver procurando por uma agregação group-by simples, use o [transformação agregação](data-flow-aggregate.md)
