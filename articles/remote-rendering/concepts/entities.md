---
title: Entidades
description: Definição de entidades no escopo da API de Azure Remote Rendering
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 29952353b8c3452d95bcced163fafa81fe158f64
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99593394"
---
# <a name="entities"></a>Entidades

Uma *Entidade* representa um objeto móvel no espaço e é o bloco de construção fundamental do conteúdo renderizado remotamente.

## <a name="entity-properties"></a>Propriedades da entidade

As entidades têm uma transformação definida por posição, rotação e escala. As entidades não têm nenhuma funcionalidade observável por si só. Em vez disso, o comportamento é adicionado por meio de componentes, que são anexados às entidades. Por exemplo, anexar um [CutPlaneComponent](../overview/features/cut-planes.md) criará um plano de corte na posição da entidade.

O aspecto mais importante da entidade em si é a hierarquia e a transformação hierárquica resultante. Por exemplo, quando várias entidades estão anexadas como filhos a uma entidade pai compartilhada, todas elas podem ser movidas, giradas e dimensionadas de forma não dinâmica alterando a transformação da entidade pai. Além disso, o estado da entidade `enabled` pode ser usado para desativar a visibilidade e as respostas a Ray casts para um subgrafo completo na hierarquia.

Uma entidade é de propriedade exclusiva de seu pai, o que significa que, quando o pai é destruído com `Entity.Destroy()`, também são seus filhos e todos os [componentes](components.md) conectados. Deste modo, a remoção de um modelo da cena é realizada chamando `Destroy` no nó raiz de um modelo, retornado por `RenderingSession.Connection.LoadModelAsync()` ou sua variante SAS `RenderingSession.Connection.LoadModelFromSasAsync()`.

As entidades são criadas quando o servidor carrega o conteúdo ou quando o usuário quer adicionar um objeto à cena. Por exemplo, se um usuário quiser adicionar um plano de corte para visualizar o interior de uma malha, o usuário poderá criar uma entidade em que o plano deve existir e, em seguida, adicionar o componente do plano de corte a ela.

## <a name="create-an-entity"></a>Criar uma entidade

Para adicionar uma nova entidade à cena, por exemplo, para passá-la como um objeto raiz para carregar modelos ou anexar componentes a ela, use o seguinte código:

```cs
Entity CreateNewEntity(RenderingSession session)
{
    Entity entity = session.Connection.CreateEntity();
    entity.Position = new LocalPosition(1, 2, 3);
    return entity;
}
```

```cpp
ApiHandle<Entity> CreateNewEntity(ApiHandle<RenderingSession> session)
{
    ApiHandle<Entity> entity(nullptr);
    if (auto entityRes = session->Connection()->CreateEntity())
    {
        entity = entityRes.value();
        entity->SetPosition(Double3{ 1, 2, 3 });
        return entity;
    }
    return entity;
}
```

## <a name="query-functions"></a>Funções de consulta

Há dois tipos de funções de consulta em entidades: chamadas síncronas e chamadas assíncronas. As consultas síncronas podem ser usadas apenas para dados que estão presentes no cliente e não envolvem muita computação. Por exemplo, consultas para componentes, transformações de objeto relativo ou relações pai/filho. As consultas assíncronas são usadas para dados que residem apenas no servidor ou envolvem computação extra que seria muito cara para ser executada no cliente. Por exemplo, consultas de limites espaciais ou consultas de metadados.

### <a name="querying-components"></a>Consultando componentes

Para encontrar um componente de um tipo específico, use `FindComponentOfType`:

```cs
CutPlaneComponent cutplane = (CutPlaneComponent)entity.FindComponentOfType(ObjectType.CutPlaneComponent);

// or alternatively:
CutPlaneComponent cutplane = entity.FindComponentOfType<CutPlaneComponent>();
```

```cpp
ApiHandle<CutPlaneComponent> cutplane = entity->FindComponentOfType(ObjectType::CutPlaneComponent)->as<CutPlaneComponent>();

// or alternatively:
ApiHandle<CutPlaneComponent> cutplane = entity->FindComponentOfType<CutPlaneComponent>();
```

### <a name="querying-transforms"></a>Como consultar transformações

As chamadas síncronas no objeto são consultas de transformação. Observe que as transformações consultadas por meio da API são transformações de espaço local em relação ao pai do objeto. As exceções são objetos raiz para os quais o espaço local e o espaço do mundo são idênticos.

> [!NOTE]
> Não há API dedicada para consultar a transformação de espaço do mundo de objetos arbitrários.

```cs
// local space transform of the entity
Double3 translation = entity.Position;
Quaternion rotation = entity.Rotation;
```

```cpp
// local space transform of the entity
Double3 translation = entity->GetPosition();
Quaternion rotation = entity->GetRotation();
```

### <a name="querying-spatial-bounds"></a>Consultando limites espaciais

Chamadas assíncronas são consultas de limites que operam em uma hierarquia de objetos completa, usando uma entidade como raiz. Consulte o capítulo dedicado sobre [limites de objetos](object-bounds.md).

### <a name="querying-metadata"></a>Consultando metadados

Os metadados são dados adicionais armazenados em objetos e ignorados pelo servidor. Os metadados de objeto são essencialmente um conjunto de pares (nome, valor), em que o _valor_ pode ser numérico, booliano ou tipo de cadeia de caracteres. É possível exportar os metadados com o modelo.

Consultas de metadados são as chamadas assíncronas em uma entidade específica. A consulta fornece apenas os metadados de uma única entidade, não as informações mescladas de um subgrafo.

```cs
Task<ObjectMetadata> metaDataQuery = entity.QueryMetadataAsync();
ObjectMetadata metaData = await metaDataQuery;
ObjectMetadataEntry entry = metaData.GetMetadataByName("MyInt64Value");
System.Int64 intValue = entry.AsInt64;
// ...
```

```cpp
entity->QueryMetadataAsync([](Status status, ApiHandle<ObjectMetadata> metaData) 
{
    if (status == Status::OK)
    {
        ApiHandle<ObjectMetadataEntry> entry = *metaData->GetMetadataByName("MyInt64Value");
        int64_t intValue = *entry->GetAsInt64();

        // ...
    }
});
```

A consulta será realizada mesmo se o objeto não tiver nenhum metadado.

## <a name="api-documentation"></a>Documentação da API

* [Classe de entidade C#](/dotnet/api/microsoft.azure.remoterendering.entity)
* [C# RenderingConnection. CreateEntity ()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.createentity)
* [Classe de entidade C++](/cpp/api/remote-rendering/entity)
* [C++ RenderingConnection:: CreateEntity ()](/cpp/api/remote-rendering/renderingconnection#createentity)

## <a name="next-steps"></a>Próximas etapas

* [Componentes](components.md)
* [Limites de objetos](object-bounds.md)