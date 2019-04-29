---
title: Transformação de Divisão Condicional do Fluxo de Dados de Mapeamento do Azure Data Factory
description: Transformação de Divisão Condicional do Fluxo de Dados do Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: a4ea79e05165dfae4f79aa6473a07151ba7c00fc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60627620"
---
# <a name="azure-data-factory-mapping-data-flow-conditional-split-transformation"></a>Transformação de Divisão Condicional do Fluxo de Dados de Mapeamento do Azure Data Factory

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

A transformação de divisão condicional pode rotear linhas de dados para diferentes fluxos dependendo do conteúdo dos dados. A implementação da transformação de divisão condicional é semelhante a uma estrutura de decisão CASE em uma linguagem de programação. A transformação avalia expressões, e com base nos resultados, direciona a linha de dados para o fluxo especificado. Essa transformação também fornece uma saída padrão, para que se uma linha não corresponder a nenhuma expressão ela seja direcionada para a saída padrão.

![divisão condicional](media/data-flow/cd1.png "divisão condicional")

Para adicionar outras condições, selecione "Adicionar Stream" no painel de configuração inferior e clique na caixa de texto do construtor de expressões para criar sua expressão.
