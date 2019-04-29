---
title: Padrões de Coluna do Fluxo de Dados do Mapeamento do Azure Data Factory
description: Os Padrões de Coluna do Fluxo de Dados do Mapeamento do Azure Data Factory são usados para criar padrões de modelo generalizados para transformar os campos em um fluxo de dados sem levar em consideração os metadados de esquema subjacente
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 53d3300ea11a86c34909ba6ce0fd6c8c0c38b4b5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61269534"
---
# <a name="azure-data-factory-mapping-data-flow-concepts"></a>Conceitos do Fluxo de Dados do Mapeamento do Azure Data Factory

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Várias transformações de Fluxo de Dados do Azure Data Factory dão suporte à ideia de "Padrões de colunas" para que você possa criar colunas de modelo com base nos padrões em vez de nomes de coluna embutidos em código. Você pode usar esse recurso no Construtor de Expressões para definir padrões para correspondência das colunas para transformação em vez de exigir nomes de campo exatos específicos. Padrões são úteis quando campos de origem de entrada são alterados com frequência, especialmente no caso de alteração das colunas nos arquivos de texto ou bancos de dados NoSQL. Isso é, às vezes, chamado de "Descompasso do esquema".

![padrões de coluna](media/data-flow/columnpattern2.png "Padrões de coluna")

Padrões de coluna são úteis para lidar com cenários de Descompasso de esquema, bem como cenários gerais. É uma boa opção para condições em que não é possível saber totalmente cada nome de coluna. Você pode padronizar a correspondência no nome de coluna e no tipo de dados de coluna e criar uma expressão para a transformação que executará essa operação em relação a qualquer campo no fluxo de dados que corresponde aos seus padrões `name` & `type`.

Ao adicionar uma expressão a uma transformação que aceita padrões, escolha "Adicionar coluna padrão". Padrões de coluna permitem que a coluna de descompasso do esquema corresponda aos padrões.

Ao criar padrões de coluna de modelo, use `$$` na expressão para declarar uma referência para cada campo correspondente do fluxo de dados de entrada.

Se optar por usar uma das funções regex do Construtor de Expressões, você poderá, posteriormente, usar $1, $2, $3 ... para fazer referência a subpadrões com correspondência de sua expressão regex.

Um exemplo de cenário de Padrão de Coluna está usando SUM com uma série de campos de entrada. Os cálculos SUM agregados ficam na transformação de agregação. Você pode usar SUM em cada correspondência de tipos de campo que corresponda a "integer" e, em seguida, usar $$ para fazer referência a cada correspondência na sua expressão.
