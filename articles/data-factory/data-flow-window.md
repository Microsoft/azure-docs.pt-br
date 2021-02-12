---
title: Transformação de janela no fluxo de dados de mapeamento
description: Transformação da janela fluxo de dados de mapeamento de Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/16/2020
ms.openlocfilehash: 8f0d10b6ed69cd31249447b59114c590bdbeb078
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94832357"
---
# <a name="window-transformation-in-mapping-data-flow"></a>Transformação de janela no fluxo de dados de mapeamento

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A Transformação de janela é o local em que você definirá agregações baseadas em janela de colunas em seus fluxos de dados. No Construtor de Expressões, é possível definir diferentes tipos de agregações baseadas em janelas de dados ou de tempo (cláusula SQL OVER) como LEAD, LAG, NTILE, CUMEDIST, RANK, etc.). Um novo campo será gerado na saída que inclui essas agregações. Também é possível incluir campos opcionais agrupar por.

![Captura de tela mostra janelas selecionadas no menu.](media/data-flow/windows1.png "Windows 1")

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4IAVu]

## <a name="over"></a>Acima
Defina o particionamento dos dados da coluna para sua transformação de janela. O SQL equivalente é o ```Partition By``` na cláusula Over no SQL. Se desejar criar um cálculo ou uma expressão a ser usada para o particionamento, será possível fazer isso passando o mouse sobre o nome da coluna e selecionando “coluna computada”.

![Captura de tela mostra as configurações de janela com a guia acima selecionada.](media/data-flow/windows4.png "Windows 4")

## <a name="sort"></a>Classificar
Outra parte da cláusula Over é a configuração de ```Order By```. Isso definirá a ordem de classificação dos dados. Também é possível criar uma expressão para um valor de cálculo nesse campo de coluna para classificação.

![A captura de tela mostra as configurações de janelas com a guia Classificar selecionada.](media/data-flow/windows5.png "Windows 5")

## <a name="range-by"></a>Ordenar por
Em seguida, defina o quadro de janela como Não associado ou Associado. Para definir um quadro de janela não associado, defina o controle deslizante como Não associado em ambas as extremidades. Se você escolher uma configuração entre a Linha não associada e a atual, deverá definir os valores de início e término do deslocamento. Os dois valores serão inteiros positivos. É possível usar números ou valores relativos dos seus dados.

O controle deslizante da janela tem dois valores a serem definidos: os valores antes da linha atual e os valores depois da linha atual. O deslocamento inicial e final corresponde aos dois seletores no controle deslizante.

![Captura de tela mostra as configurações de janelas com a guia intervalo por selecionada.](media/data-flow/windows6.png "Windows 6")

## <a name="window-columns"></a>Colunas da janela
Por fim, use o Construtor de Expressões para definir as agregações que você deseja usar com as janelas de dados como RANK, COUNT, MIN, MAX, DENSE RANK, LEAD, LAG, etc.

![Captura de tela mostra o resultado da ação de janela.](media/data-flow/windows7.png "Windows 7")

A lista completa de funções analíticas e de agregação disponível para você usar na Linguagem de Expressão do Fluxo de Dados do ADF por meio do Construtor de Expressões está listada aqui: https://aka.ms/dataflowexpressions.

## <a name="next-steps"></a>Próximas etapas

Se você estiver procurando uma agregação de grupo por simples, use a [transformação Agregação](data-flow-aggregate.md)
