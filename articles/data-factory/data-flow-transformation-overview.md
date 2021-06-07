---
title: Visão geral da transformação fluxo de dados de mapeamento
description: Uma visão geral das diferentes transformações disponíveis no fluxo de dados de mapeamento
author: dcstwh
ms.author: weetok
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/27/2020
ms.openlocfilehash: f595766d49d6c00c313990717d8fd241ddfc8f81
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104593849"
---
# <a name="mapping-data-flow-transformation-overview"></a>Visão geral da transformação fluxo de dados de mapeamento

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)] 

Veja abaixo uma lista das transformações com suporte no fluxo de dados de mapeamento. Clique em cada transformação para aprender seus detalhes de configuração.

| Nome | Categoria | Descrição |
| ---- | -------- | ----------- |
| [Aggregate](data-flow-aggregate.md) | Modificador de esquema | Defina diferentes tipos de agregações como SUM, MIN, MAX e COUNT agrupados por colunas computadas ou existentes. | 
| [Alterar linha](data-flow-alter-row.md) | Modificador de linha | Defina as políticas de inserção, exclusão, atualização e Upsert em linhas. |
| [Divisão condicional](data-flow-conditional-split.md) | Várias entradas/saídas | Encaminhe linhas de dados para fluxos diferentes com base em condições de correspondência. |
| [Coluna derivada](data-flow-derived-column.md) | Modificador de esquema | gere novas colunas ou modifique os campos existentes usando a linguagem de expressão de fluxo de dados. | 
| [Exists](data-flow-exists.md) | Várias entradas/saídas | Verifique se os dados existem em outra fonte ou fluxo. | 
| [Filter](data-flow-filter.md) | Modificador de linha | Filtrar uma linha com base em uma condição. |
| [Mesclar](data-flow-flatten.md) | Modificador de esquema |  Pegue valores de matriz dentro de estruturas hierárquicas, como JSON, e os registre em linhas individuais. |
| [Join](data-flow-join.md) | Várias entradas/saídas |  Combine dados de duas origens ou fluxos. |
| [Pesquisar](data-flow-lookup.md) | Várias entradas/saídas | Dados de referência de outra fonte. |
| [Novo branch](data-flow-new-branch.md) | Várias entradas/saídas | Aplique vários conjuntos de operações e transformações no mesmo fluxo de dados. |
| [Analisar](data-flow-new-branch.md) | Formatador | Analise as colunas de texto no fluxo de dados que são cadeias de caracteres JSON, texto delimitado ou texto formatado XML. |
| [Pivô](data-flow-pivot.md) | Modificador de esquema | Uma agregação em que uma ou mais colunas de Agrupamento têm seus valores de linha distintos transformados em colunas individuais. |
| [Fique](data-flow-rank.md) | Modificador de esquema | Gerar uma classificação ordenada com base nas condições de classificação |
| [Selecionar](data-flow-select.md) | Modificador de esquema | Colunas de alias e nomes de fluxo, e soltar ou reordenar colunas |
| [Coletor](data-flow-sink.md) | - | Um destino final para seus dados |
| [Sort](data-flow-sort.md) | Modificador de linha | Classificar linhas de entrada no fluxo de dados atual |
| [Origem](data-flow-source.md) | - | Uma fonte de dados para o fluxo de dados |
| [Chave alternativa](data-flow-surrogate-key.md) | Modificador de esquema | Adicionar um valor de chave arbitrária de não comercial em incremento |
| [Union](data-flow-union.md) | Várias entradas/saídas | Combinar vários fluxos de dados verticalmente |
| [Não Dinâmico](data-flow-unpivot.md) | Modificador de esquema | Dinamizar colunas em valores de linha |
| [Window](data-flow-window.md) | Modificador de esquema |  Defina as agregações baseadas em janela de colunas em seus fluxos de dados. |
