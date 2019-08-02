---
title: Transformação Agregação no fluxo de dados de mapeamento-Azure Data Factory | Microsoft Docs
description: Saiba como agregar dados em escala em Azure Data Factory com a transformação agregar fluxo de dados de mapeamento.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 778aefc05a9b12648e60d752a3c281cb18323125
ms.sourcegitcommit: da0a8676b3c5283fddcd94cdd9044c3b99815046
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68314222"
---
# <a name="aggregate-transformation-in-mapping-data-flow"></a>Transformação Agregação no fluxo de dados de mapeamento 

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

A transformação de agregação é o lugar para definir as agregações de colunas em seus fluxos de dados. Usando o construtor de expressões, você pode definir diferentes tipos de agregações como SUM, MIN, MAX e COUNT que podem ser agrupados por colunas computadas ou existentes.

## <a name="group-by"></a>Agrupar por
Selecione uma coluna existente ou crie uma nova coluna computada para usar como uma cláusula Group by para sua agregação. Para usar uma coluna existente, selecione a coluna desejada na lista suspensa. Para criar uma nova coluna computada, passe o mouse sobre a cláusula e clique em ' coluna computada '. Isso abrirá o [Construtor de expressões de fluxo de dados](concepts-data-flow-expression-builder.md). Depois de criar a coluna computada, insira o nome da coluna de saída no campo ' nome como '. Se você quiser adicionar uma cláusula Group by adicional, passe o mouse sobre uma cláusula existente e clique em ' + '.

![Grupos de transformação agregados por configurações](media/data-flow/agg.png "Grupos de transformação agregados por configurações")

> [!NOTE]
> Uma cláusula Group by é opcional em uma transformação Agregação.

## <a name="aggregate-column"></a>Coluna de agregação 
Escolha a guia ' agregações ' para criar expressões de agregação. Você pode escolher uma coluna existente e substituir o valor pela agregação ou criar um novo campo com um novo nome. A expressão de agregação é inserida na caixa à direita ao lado do seletor de nome de coluna. Para editar a expressão, clique na caixa de texto para abrir o construtor de expressões. Para adicionar uma agregação adicional, passe o mouse sobre uma expressão existente e clique em ' + ' para criar um novo [padrão](concepts-data-flow-column-pattern.md)de coluna ou coluna de agregação.

![Configurações] de agregação de transformação Agregação (media/data-flow/agg2.png "Configurações") de agregação de transformação Agregação

> [!NOTE]
> Cada expressão de agregação deve conter pelo menos uma função de agregação.

> [!NOTE]
> No modo de depuração, o construtor de expressões não pode produzir visualizações de dados com funções de agregação. Para exibir visualizações de dados para transformações agregadas, feche o construtor de expressões e exiba os dados por meio da guia ' Data preview '.

## <a name="reconnect-rows-and-columns"></a>Reconectar linhas e colunas
As transformações agregadas são bastante equivalentes às consultas SELECT de agregação SQL. As colunas que não estão incluídas em sua cláusula Group by ou funções de agregação não fluirão para a saída da transformação Agregação. Se houver outras colunas que você deseja incluir com a saída de linhas agregadas, você deverá:

* Use uma função de agregação para incluir essa coluna adicional, como Last () ou First ()
* Ingresse novamente as colunas antes da agregação usando o [padrão](https://mssqldude.wordpress.com/2018/12/20/adf-data-flows-self-join/)de autojunção.

## <a name="next-steps"></a>Próximas etapas

* Definir a agregação baseada em janela usando a [transformação janela](data-flow-window.md)
