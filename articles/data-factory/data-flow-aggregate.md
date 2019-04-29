---
title: Transformação de agregação do Fluxo de Dados de Mapeamento do Azure Data Factory
description: Transformação de agregação do Fluxo de Dados do Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 7b488b243c0520befb6b5470598f460b5a759fed
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61467338"
---
# <a name="azure-data-factory-mapping-data-flow-aggregate-transformation"></a>Transformação de agregação do Fluxo de Dados de Mapeamento do Azure Data Factory

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

A transformação de agregação é o lugar para definir as agregações de colunas em seus fluxos de dados. No Construtor de Expressões, você pode definir tipos diferentes de agregações (ou seja, SUM, MIN, MAX, COUNT, etc.) e criar um novo campo na saída que inclua essas agregações com campos opcionais group-by.

![Opções de transformação de agregação](media/data-flow/agg.png "Agregação 1")

## <a name="group-by"></a>Agrupar Por
(Opcional) Escolha uma cláusula Group-by para a agregação e use o nome de uma coluna existente ou um novo nome. Use "Adicionar coluna" para adicionar mais cláusulas group-by e clique na caixa de texto ao lado do nome da coluna para iniciar o Construtor de Expressões. Em seguida, escolha apenas uma coluna existente, uma combinação de colunas ou expressões para o agrupamento.

## <a name="the-aggregate-column-tab"></a>Guia Coluna de Agregação 
(Obrigatório) Escolha a guia Coluna de Agregação para criar expressões de agregação. Você pode escolher uma coluna existente para substituir seu valor pela agregação ou criar um novo campo com o novo nome para a agregação. A expressão que você deseja usar para a agregação será inserida na caixa à direita ao lado do seletor de nome da coluna. Ao clicar na caixa de texto, o Construtor de Expressões será aberto.

![Opções de transformação de agregação](media/data-flow/agg2.png "Agregador")

## <a name="data-preview-in-expression-builder"></a>Visualização de dados no Construtor de Expressões

No modo Depuração, o Construtor de Expressões não pode produzir visualizações de dados com funções de agregação. Para exibir as visualizações de dados de transformações de agregação, feche o Construtor de Expressões e exiba o perfil de dados no designer do fluxo de dados.
