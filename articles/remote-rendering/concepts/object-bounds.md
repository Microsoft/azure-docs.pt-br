---
title: Limites do objeto
description: Explica como os limites do objeto espacial podem ser consultados
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: df04b767035dffb62fde89d1e74b808d62fcc943
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99594477"
---
# <a name="object-bounds"></a>Limites do objeto

Os limites do objeto representam o volume que uma [entidade](entities.md) e seus filhos ocupam. No Azure Remote Rendering, os limites do objeto são sempre fornecidos como AABB *(caixas delimitadoras alinhadas do eixo)* . Os limites do objeto podem estar no *espaço local* ou no *espaço de mundo*. De qualquer forma, eles são sempre alinhados por eixo, o que significa que as extensões e o volume podem ser diferentes entre a representação de espaço local e de mundo.

## <a name="querying-object-bounds"></a>Como consultar limites do objeto

A caixa delimitadora alinhada ao eixo local de uma [malha](meshes.md) pode ser consultada diretamente do recurso de malha. Esses limites podem ser transformados no espaço local ou no espaço de mundo de uma entidade usando a transformação da entidade.

É possível computar os limites de uma hierarquia de objetos inteira dessa forma, mas isso requer percorrer a hierarquia, consultar os limites de cada malha e combiná-los manualmente. Essa operação é entediante e ineficiente.

Um modo melhor é chamar `QueryLocalBoundsAsync` ou `QueryWorldBoundsAsync` em uma entidade. O cálculo é então descarregado para o servidor e retornado com atraso mínimo.

```cs
public async void GetBounds(Entity entity)
{
    try
    {
        Task<Bounds> boundsQuery = entity.QueryWorldBoundsAsync();
        Bounds result = await boundsQuery;
    
        Double3 aabbMin = result.Min;
        Double3 aabbMax = result.Max;
        // ...
    }
    catch (RRException ex)
    {
    }
}
```

```cpp
void GetBounds(ApiHandle<Entity> entity)
{
    entity->QueryWorldBoundsAsync(
        // completion callback:
        [](Status status, Bounds bounds)
        {
           if (status == Status::OK)
            {
                Double3 aabbMin = bounds.Min;
                Double3 aabbMax = bounds.Max;
                // ...
            }
        }
    );
}
```

## <a name="api-documentation"></a>Documentação da API

* [Entidade C#. QueryLocalBoundsAsync](/dotnet/api/microsoft.azure.remoterendering.entity.querylocalboundsasync)
* [Entidade C++:: QueryLocalBoundsAsync](/cpp/api/remote-rendering/entity#querylocalboundsasync)

## <a name="next-steps"></a>Próximas etapas

* [Consultas espaciais](../overview/features/spatial-queries.md)