---
title: Consultas espaciais
description: Como fazer consultas espaciais em uma cena
author: jakrams
ms.author: jakras
ms.date: 02/07/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: c664df586c260b3e16f64c071190055dbaeccd24
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594037"
---
# <a name="spatial-queries"></a>Consultas espaciais

As consultas espaciais são operações com as quais você pode perguntar ao serviço de renderização remoto quais objetos estão localizados em uma área. Consultas espaciais são usadas frequentemente para implementar interações, como descobrir para qual objeto um usuário está apontando.

Todas as consultas espaciais são avaliadas no servidor. Consequentemente, elas são operações assíncronas, e os resultados chegarão com um atraso que depende da sua latência de rede. Como cada consulta espacial gera tráfego de rede, tenha cuidado para não fazer muitas de uma vez.

## <a name="collision-meshes"></a>Malhas de colisão

As consultas espaciais são alimentadas pelo mecanismo [Havok](https://www.havok.com/products/havok-physics) e exigem a presença de uma malha de colisão dedicada. Por padrão, a [conversão de modelo](../../how-tos/conversion/model-conversion.md) gera malhas de colisão. Se você não precisar de consultas espaciais em um modelo complexo, considere desabilitar a geração de malha de colisão nas [opções de conversão](../../how-tos/conversion/configure-model-conversion.md), pois ela impactam de várias maneiras:

* A [conversão de modelo](../../how-tos/conversion/model-conversion.md) levará consideravelmente mais tempo.
* Os tamanhos de arquivos de modelo convertidos são visivelmente maiores, impactando a velocidade de download.
* Os tempos de carregamento em runtime são mais longos.
* O consumo de memória de CPU em runtime é maior.
* Há uma pequena sobrecarga de desempenho de runtime para cada instância de modelo.

## <a name="ray-casts"></a>Incidências de raio

Uma *incidência de raio* é uma consulta espacial em que o runtime verifica quais objetos são interseccionados por um raio, começando em uma determinada posição e apontando para uma determinada direção. Como uma otimização, uma distância máxima de raios também é mostrada, para não procurar objetos que estejam muito distantes.

```cs
async void CastRay(RenderingSession session)
{
    // trace a line from the origin into the +z direction, over 10 units of distance.
    RayCast rayCast = new RayCast(new Double3(0, 0, 0), new Double3(0, 0, 1), 10);

    // only return the closest hit
    rayCast.HitCollection = HitCollectionPolicy.ClosestHit;

    RayCastQueryResult result = await session.Connection.RayCastQueryAsync(rayCast);
    RayCastHit[] hits = result.Hits;
    if (hits.Length > 0)
    {
        var hitObject = hits[0].HitObject;
        var hitPosition = hits[0].HitPosition;
        var hitNormal = hits[0].HitNormal;

        // do something with the hit information
    }
}
```

```cpp
void CastRay(ApiHandle<RenderingSession> session)
{
    // trace a line from the origin into the +z direction, over 10 units of distance.
    RayCast rayCast;
    rayCast.StartPos = {0, 0, 0};
    rayCast.EndPos = {0, 0, 1};
    rayCast.MaxHits = 10;

    // only return the closest hit
    rayCast.HitCollection = HitCollectionPolicy::ClosestHit;

    session->Connection()->RayCastQueryAsync(rayCast, [](Status status, ApiHandle<RayCastQueryResult> result)
    {
        if (status == Status::OK)
        {
            std::vector<RayCastHit> hits;
            result->GetHits(hits);

            if (hits.size() > 0)
            {
                auto hitObject = hits[0].HitObject;
                auto hitPosition = hits[0].HitPosition;
                auto hitNormal = hits[0].HitNormal;

                // do something with the hit information
            }
        }
    });
}
```


Há três modos de coleta de impactos:

* **`Closest`:** nesse modo, somente o impacto mais próximo a será relatado.
* **`Any`:** prefira esse modo quando tudo o que você deseja saber é *se* um raio atingiria algum ponto, mas sem se importar com o que exatamente seria atingido. Essa consulta pode ser consideravelmente mais barata para avaliar, mas também tem poucas aplicações.
* **`All`:** nesse modo, todos os impactos ao longo do raio são relatados, classificados por distância. Não use esse modo, a menos que você realmente precise saber mais do que o primeiro impacto. Limite o número de impactos relatados com a opção `MaxHits`.

Para excluir objetos de forma seletiva a serem considerados para incidências de raio, o componente [HierarchicalStateOverrideComponent](override-hierarchical-state.md) pode ser usado.

<!--
The CollisionMask allows the query to consider or ignore some objects based on their collision layer. If an object has layer L, it will be hit only if the mask has bit L set.
It is useful in case you want to ignore objects, for instance when setting an object transparent, and trying to select another object behind it.
TODO : Add an API to make that possible.
-->

### <a name="hit-result"></a>Resultado do impacto

O resultado de uma consulta de incidências de raio é uma matriz de impactos. A matriz estará vazia se nenhum objeto tiver sido atingido.

Um impacto tem as seguintes propriedades:

* **`HitEntity`:** qual [entidade](../../concepts/entities.md) foi atingida.
* **`SubPartId`:** qual *submalha* foi atingida em um [MeshComponent](../../concepts/meshes.md). Pode ser usado para indexar `MeshComponent.UsedMaterials` e procurar o [material](../../concepts/materials.md) nesse ponto.
* **`HitPosition`:** a posição do espaço global em que o raio interseccionou o objeto.
* **`HitNormal`:** a normal da superfície do espaço mundial da malha na posição da interseção.
* **`DistanceToHit`:** a distância da posição inicial do raio até o impacto.

## <a name="api-documentation"></a>Documentação da API

* [C# RenderingConnection. RayCastQueryAsync ()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.raycastqueryasync)
* [C++ RenderingConnection:: RayCastQueryAsync ()](/cpp/api/remote-rendering/renderingconnection#raycastqueryasync)

## <a name="next-steps"></a>Próximas etapas

* [Limites do objeto](../../concepts/object-bounds.md)
* [Substituir estados hierárquicos](override-hierarchical-state.md)