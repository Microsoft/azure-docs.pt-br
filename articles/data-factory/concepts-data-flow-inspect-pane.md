---
title: Visualização de Dados e Inspeção do Fluxo de Dados de Mapeamento do Azure Data Factory
description: O Fluxo de Dados de Mapeamento do Azure Data Factory tem um painel que mostra os metadados de fluxo de dados (inspeção) e a visualização de dados no modo de depuração (visualização de dados)
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 47cde50e0874f0f73523925b6d1b2f8ee4abaea9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61283992"
---
# <a name="azure-data-factory-mapping-data-flow-transformation-inspect-tab"></a>Guia Inspeção da Transformação do Fluxo de Dados de Mapeamento do Azure Data Factory

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Painel Inspeção](media/data-flow/agg3.png "Painel Inspeção")

O painel Inspeção fornece uma exibição dos metadados do fluxo de dados que você está transformando. Você será capaz de ver as contagens de coluna, as colunas alteradas, as colunas adicionadas, os tipos de dados, a ordem das colunas e as referências de coluna. "inspeção" é uma exibição somente leitura de seus metadados. Você não precisa ter o modo de depuração habilitado para ver metadados no painel Inspeção.

À medida que você alterar a forma de seus dados por meio de transformações, verá os metadados alterarem o fluxo por meio do painel Inspeção. Se não houver um esquema definido na sua transformação de fonte, os metadados não serão visíveis no painel Inspeção. A falta de metadados é comum em cenários de Descompasso do esquema.

![Visualização de dados](media/data-flow/datapreview.png "Visualização de dados")

Visualização de dados é um painel que fornece uma exibição somente leitura de seus dados enquanto eles são transformados. Você pode exibir a saída de sua transformação e as expressões para validar seu fluxo de dados. Você deve ter o modo de depuração ativado para ver as visualizações de dados. Ao clicar nas colunas na grade de visualização de dados, você verá um painel subsequente à direita. O painel pop-out mostrará as estatísticas de perfil sobre cada uma das colunas que você selecionar.

![Gráfico de visualização de dados](media/data-flow/chart.png "Gráfico de visualização de dados")

