---
title: Malhas
description: Definição de malhas no escopo da renderização remota do Azure
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: 33894e0d0b6f3ea50ffeac4f0c90c60f28e09f5e
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594511"
---
# <a name="meshes"></a>Malhas

## <a name="mesh-resource"></a>Recurso de malha

As malhas são um [recurso compartilhado](../concepts/lifetime.md)imutável, que só pode ser criado por meio da [conversão de modelo](../how-tos/conversion/model-conversion.md). As malhas contêm uma ou várias *submalhas* juntamente com uma representação física para [consultas Raycast](../overview/features/spatial-queries.md). Cada submalha faz referência a um [material](materials.md) com o qual ele deve ser renderizado por padrão. Para posicionar uma malha no espaço 3D, adicione um [MeshComponent](#meshcomponent) a uma [entidade](entities.md).

### <a name="mesh-resource-properties"></a>Propriedades do recurso de malha

As `Mesh` Propriedades de classe são:

* **Materiais:** Uma matriz de materiais. Cada material é usado por uma submalha diferente. Várias entradas na matriz podem referenciar o mesmo [material](materials.md). Esses dados não podem ser modificados em tempo de execução.

* **Limites:** Uma AABB (caixa delimitadora alinhada ao eixo de espaço local) dos vértices de malha.

## <a name="meshcomponent"></a>MeshComponent

A `MeshComponent` classe é usada para posicionar uma instância de um recurso de malha. Cada MeshComponent faz referência a uma única malha. Ele pode substituir quais materiais são usados para renderizar cada submalha.

### <a name="meshcomponent-properties"></a>Propriedades de MeshComponent

* **Malha:** O recurso de malha usado por esse componente.

* **Materiais:** A matriz de materiais especificada no próprio componente de malha. A matriz terá sempre o mesmo comprimento que a matriz de *materiais* no recurso de malha. Os materiais que não devem ser substituídos do padrão de malha são definidos como *NULL* nessa matriz.

* **UsedMaterials:** A matriz de materiais realmente usados para cada submalha. Será idêntico aos dados na matriz de *materiais* , para valores não nulos. Caso contrário, ele contém o valor da matriz de *materiais* na instância de malha.

### <a name="sharing-of-meshes"></a>Compartilhamento de malhas

Um `Mesh` recurso pode ser compartilhado entre várias instâncias de componentes de malha. Além disso, o `Mesh` recurso atribuído a um componente de malha pode ser alterado programaticamente a qualquer momento. O código a seguir demonstra como clonar uma malha:

```cs
Entity CloneEntityWithModel(RenderingConnection api, Entity sourceEntity)
{
    MeshComponent meshComp = sourceEntity.FindComponentOfType<MeshComponent>();
    if (meshComp != null)
    {
        Entity newEntity = api.CreateEntity();
        MeshComponent newMeshComp = api.CreateComponent(ObjectType.MeshComponent, newEntity) as MeshComponent;
        newMeshComp.Mesh = meshComp.Mesh; // share the mesh
        return newEntity;
    }
    return null;
}
```

```cpp
ApiHandle<Entity> CloneEntityWithModel(ApiHandle<RenderingConnection> api, ApiHandle<Entity> sourceEntity)
{
    if (ApiHandle<MeshComponent> meshComp = sourceEntity->FindComponentOfType<MeshComponent>())
    {
        ApiHandle<Entity> newEntity = *api->CreateEntity();
        ApiHandle<MeshComponent> newMeshComp = api->CreateComponent(ObjectType::MeshComponent, newEntity)->as<RemoteRendering::MeshComponent>();
        newMeshComp->SetMesh(meshComp->GetMesh()); // share the mesh
        return newEntity;
    }
    return nullptr;
}
```

## <a name="api-documentation"></a>Documentação da API

* [Classe de malha C#](/dotnet/api/microsoft.azure.remoterendering.mesh)
* [Classe C# MeshComponent](/dotnet/api/microsoft.azure.remoterendering.meshcomponent)
* [Classe de malha C++](/cpp/api/remote-rendering/mesh)
* [Classe C++ MeshComponent](/cpp/api/remote-rendering/meshcomponent)


## <a name="next-steps"></a>Próximas etapas

* [Materiais](materials.md)