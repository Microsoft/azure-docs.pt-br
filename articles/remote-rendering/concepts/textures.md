---
title: Texturas
description: Fluxo de trabalho de recurso de textura
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: e01ddf0690f11d41021e0a5ae5958c7c80646743
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99594410"
---
# <a name="textures"></a>Texturas

As texturas são um [recurso compartilhado](../concepts/lifetime.md) imutável. As texturas podem ser carregadas do [armazenamento de blobs](../how-tos/conversion/blob-storage.md) e aplicadas aos modelos diretamente, como demonstrado no [Tutorial: Alteração do ambiente e dos materiais](../tutorials/unity/materials-lighting-effects/materials-lighting-effects.md). No entanto, geralmente as texturas serão parte de um [modelo convertido](../how-tos/conversion/model-conversion.md), em que eles são referenciados por seus [materiais](materials.md).

## <a name="texture-types"></a>Tipos de textura

Tipos de textura diferentes têm diferentes casos de uso:

* As **texturas 2D** são usadas principalmente em [materiais](materials.md).
* **Cubemaps** podem ser usados para [céu](../overview/features/sky.md).

## <a name="supported-texture-formats"></a>Formatos de textura compatíveis

Todas as texturas para o Application Request Routing devem estar no formato [DDS](https://en.wikipedia.org/wiki/DirectDraw_Surface). Preferencialmente com mipmaps e compactação de textura.

## <a name="loading-textures"></a>Carregar texturas

Ao carregar uma textura, é preciso especificar seu tipo esperado. Se o tipo não corresponder, o carregamento da textura falhará.
Carregar uma textura com o mesmo URI duas vezes retornará o mesmo objeto de textura, pois é um [recurso compartilhado](../concepts/lifetime.md).

De forma semelhante ao carregamento de modelos, há duas variantes de endereçamento de um ativo de textura no armazenamento do blob de origem:

* Lide com a textura diretamente pelos parâmetros de Armazenamento de Blobs, caso o [armazenamento de blobs esteja vinculado à conta](../how-tos/create-an-account.md#link-storage-accounts). A função de carregamento relevante nesse caso é `LoadTextureAsync` com o parâmetro `LoadTextureOptions`.
* O ativo de textura pode ser endereçado por seu URI de SAS. A função de carregamento relevante é `LoadTextureFromSasAsync` com o parâmetro `LoadTextureFromSasOptions`. Use essa variante também ao carregar [texturas integradas](../overview/features/sky.md#built-in-environment-maps).

O código de exemplo a seguir mostra como carregar uma textura:

```cs
async void LoadMyTexture(RenderingSession session, string storageContainer, string blobName, string assetPath)
{
    try
    {
        LoadTextureOptions options = new LoadTextureOptions(storageContainer, blobName, assetPath, TextureType.Texture2D);
        Texture texture = await session.Connection.LoadTextureAsync(options);
    
        // use texture...
    }
    catch (RRException ex)
    {
    }
}
```

```cpp
void LoadMyTexture(ApiHandle<RenderingSession> session, std::string storageContainer, std::string blobName, std::string assetPath)
{
    LoadTextureOptions params;
    params.TextureType = TextureType::Texture2D;
    params.Blob.StorageAccountName = std::move(storageContainer);
    params.Blob.BlobContainerName = std::move(blobName);
    params.Blob.AssetPath = std::move(assetPath);
    session->Connection()->LoadTextureAsync(params, [](Status status, ApiHandle<Texture> texture)
    {
        // use texture...
    });
}
```

Observe que, no caso de usar sua variante SAS, apenas a função/parâmetro de carregamento difere.

Dependendo do uso da textura, pode haver restrições para o tipo de textura e o conteúdo. Por exemplo, o mapa de irregularidades de um [material PBR](../overview/features/pbr-materials.md) deve ser em escala de cinza.

## <a name="api-documentation"></a>Documentação da API

* [Classe de textura C#](/dotnet/api/microsoft.azure.remoterendering.texture)
* [C# RenderingConnection. LoadTextureAsync ()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.loadtextureasync)
* [C# RenderingConnection. LoadTextureFromSasAsync ()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.loadtexturefromsasasync)
* [Classe de textura C++](/cpp/api/remote-rendering/texture)
* [C++ RenderingConnection:: LoadTextureAsync ()](/cpp/api/remote-rendering/renderingconnection#loadtextureasync)
* [C++ RenderingConnection:: LoadTextureFromSasAsync ()](/cpp/api/remote-rendering/renderingconnection#loadtexturefromsasasync)

## <a name="next-steps"></a>Próximas etapas

* [Materiais](materials.md)
* [Céu](../overview/features/sky.md)