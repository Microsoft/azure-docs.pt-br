---
title: Modelos de amostra
description: Lista fontes para modelos de amostra.
author: florianborn71
ms.author: flborn
ms.date: 01/29/2020
ms.topic: sample
ms.openlocfilehash: 8e5dcb6c9dfa08efc0889fcab779d6cb333d2330
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88507528"
---
# <a name="sample-models"></a>Modelos de amostra

Este artigo lista alguns recursos para dados de amostra que podem ser usados para testar o serviço Azure Remote Rendering.

## <a name="built-in-sample-model"></a>Modelo de amostra interno

Fornecemos um modelo de amostra interno que sempre pode ser carregado usando a URL **builtin://Engine**

![Modelo de exemplo](./media/sample-model.png "Modelo de exemplo")

Estatísticas do modelo:

| Nome | Valor |
|-----------|:-----------|
| [Tamanho de servidor necessário](../how-tos/session-rest-api.md#create-a-session) | padrão |
| Número de triângulos | 18,7 milhões |
| Número de blocos móveis | 2073 |
| Número de materiais | 94 |

## <a name="third-party-data"></a>Dados de terceiros

O Grupo Khronos mantém um conjunto de modelos de amostra glTF para teste. O ARR dá suporte ao formato glTF em texto ( *.gltf*) e na forma binária ( *.glb*). Sugerimos usar os modelos de PBR para obter os melhores resultados visuais:

* [Modelos de amostra glTF](https://github.com/KhronosGroup/glTF-Sample-Models)

## <a name="next-steps"></a>Próximas etapas

* [Início Rápido: Renderizar um modelo com o Unity](../quickstarts/render-model.md)
* [Início Rápido: Converter um modelo para renderização](../quickstarts/convert-model.md)
