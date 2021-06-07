---
title: Modelos de amostra
description: Lista fontes para modelos de amostra.
author: florianborn71
ms.author: flborn
ms.date: 01/29/2020
ms.topic: sample
ms.openlocfilehash: 6817601659c841ca98031f4e3e1590743bbed171
ms.sourcegitcommit: 7ec45b7325e36debadb960bae4cf33164176bc24
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100530528"
---
# <a name="sample-models"></a>Modelos de amostra

Este artigo lista alguns recursos para dados de amostra que podem ser usados para testar o serviço Azure Remote Rendering.

## <a name="built-in-sample-model"></a>Modelo de amostra interno

Fornecemos um modelo de amostra interno que sempre pode ser carregado usando a URL **builtin://Engine**

![Modelo de exemplo](./media/sample-model.png "Modelo de exemplo")

Estatísticas do modelo:

| Nome | Valor |
|-----------|:-----------|
| [Tamanho de servidor necessário](../reference/vm-sizes.md) | padrão |
| Número de triângulos | 18,7 milhões |
| Número de blocos móveis | 2073 |
| Número de materiais | 94 |

## <a name="third-party-data"></a>Dados de terceiros

O Grupo Khronos mantém um conjunto de modelos de amostra glTF para teste. O ARR dá suporte ao formato glTF em texto ( *.gltf*) e na forma binária ( *.glb*). Sugerimos usar os modelos de PBR para obter os melhores resultados visuais:

* [Modelos de amostra glTF](https://github.com/KhronosGroup/glTF-Sample-Models)

## <a name="next-steps"></a>Próximas etapas

* [Início Rápido: Renderizar um modelo com o Unity](../quickstarts/render-model.md)
* [Início Rápido: Converter um modelo para renderização](../quickstarts/convert-model.md)
