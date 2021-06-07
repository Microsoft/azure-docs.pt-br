---
title: Filtragem de profanação-Tradutor
titleSuffix: Azure Cognitive Services
description: Use a filtragem de profanação para determinar o nível de profanação traduzido em seu texto no Tradutor de serviços cognitivas do Azure.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: lajanuar
ms.openlocfilehash: 9f389d669e69dbfa6ec6d4d0b4716d2367443f17
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98896792"
---
# <a name="add-profanity-filtering-with-the-translator"></a>Adicionar filtragem de profanação com o tradutor

Normalmente o serviço de tradução retém a linguagem obscena presente no texto de origem da tradução. O grau de obscenidade e o contexto que torna as palavras impróprias diferem entre culturas. Como resultado, o grau de profanação na língua-alvo pode ser amplificado ou reduzido.

Se você quiser evitar ver palavrões na tradução, mesmo que a profanidade esteja presente no texto de origem, use a opção de filtragem de profanação disponível no método Translate (). Essa opção permite escolher se você deseja ver palavrões excluídos, marcados com as tags apropriadas ou não executar nenhuma ação.

O método translate () usa o parâmetro "Options", que contém o novo elemento "ProfanityAction". Os valores aceitos de ProfanityAction são "NoAction", "Marked" e "Deleted".

## <a name="accepted-values-of-profanityaction-and-examples"></a>Valores aceitos de ProfanityAction e exemplos
|Valor de ProfanityAction | Ação | Exemplo: Origem - Japonês | Exemplo: Destino - Inglês|
| :---|:---|:---|:---|
| NoAction | Padrão. O mesmo que não configurar a opção. A linguagem obscena passa do texto de origem para o texto de destino. | 彼は変態です。 | Ele é um idiota. |
| Marked | Palavras obscenas são circundadas por marcas XML \<profanity> ... \</profanity> | 彼は変態です。 | Ele é um \<profanity> automáticas \</profanity> . |
| Excluído | Palavras impróprias são removidas da saída sem substituição. | 彼は。 | Ele é um. |

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Aplicar a filtragem de profanação à sua chamada de Tradutor](reference/v3-0-translate.md)
