---
title: Modelos
description: Descreve o que é um modelo no Azure Remote Rendering
author: jakrams
ms.author: jakras
ms.date: 02/05/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 0d1e66d09db3e3934871ed15493feb685d1cbe6a
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99593867"
---
# <a name="models"></a>Modelos

Um *modelo* no Azure Remote Rendering refere-se a uma representação de objeto completa, composta de [entidades](entities.md) e [componentes](components.md). Os modelos são a principal maneira de obter dados personalizados para o serviço de renderização remoto.

## <a name="model-structure"></a>Estrutura do modelo

Um modelo tem exatamente um [entidade](entities.md) como seu nó raiz. Abaixo disso, ele pode ter uma hierarquia arbitrária de entidades filho. Ao carregar um modelo, uma referência a essa entidade raiz é retornada.

Cada entidade pode ter [componentes](components.md) anexados. No caso mais comum, as entidades têm *MeshComponents*, que fazem referência a [recursos de malha](meshes.md).

## <a name="creating-models"></a>Criar modelos

A criação de modelos para runtime é obtida ao [converter modelos de entrada](../how-tos/conversion/model-conversion.md) a partir de formatos de arquivo, como FBX e GLTF. O processo de conversão extrai todos os recursos, como texturas, materiais e malhas, e os converte em formatos de runtime otimizados. Ele também extrairá as informações estruturais e as converterá na estrutura do grafo de entidade/componente do Application Request Routing.

> [!IMPORTANT]
> A [conversão de modelo](../how-tos/conversion/model-conversion.md) é a única maneira de criar [malhas](meshes.md). Embora as malhas possam ser compartilhadas entre entidades no runtime, não há outra maneira de obter uma malha no runtime, a não ser ao carregar um modelo.

## <a name="loading-models"></a>Carregar modelos

Depois que um modelo é convertido, ele pode ser carregado a partir do armazenamento de blobs do Azure para o runtime.

Há duas funções de carregamento distintas que diferem na maneira como o ativo é endereçado no armazenamento de blobs:

* O modelo pode ser tratado pelos parâmetros de armazenamento de blobs, caso o [Armazenamento de Blobs esteja vinculado à conta](../how-tos/create-an-account.md#link-storage-accounts). A função de carregamento relevante nesse caso é `LoadModelAsync` com o parâmetro `LoadModelOptions`.
* O modelo pode ser endereçado por seu URI de SAS. A função de carregamento relevante é `LoadModelFromSasAsync` com o parâmetro `LoadModelFromSasOptions`. Use essa variante também ao carregar [modelos integrados](../samples/sample-model.md).

Os trechos de código a seguir mostram como carregar modelos com uma das funções. Para carregar um modelo usando seus parâmetros de armazenamento de BLOBs, use um código como o mostrado abaixo:


```cs
async void LoadModel(RenderingSession session, Entity modelParent, string storageAccount, string containerName, string assetFilePath)
{
    // load a model that will be parented to modelParent
    var modelOptions = new LoadModelOptions(
        storageAccount, // storage account name + '.blob.core.windows.net', e.g., 'mystorageaccount.blob.core.windows.net'
        containerName,  // name of the container in your storage account, e.g., 'mytestcontainer'
        assetFilePath,  // the file path to the asset within the container, e.g., 'path/to/file/myAsset.arrAsset'
        modelParent
    );

    var loadOp = session.Connection.LoadModelAsync(modelOptions, (float progress) =>
    {
        Debug.WriteLine($"Loading: {progress * 100.0f}%");
    });

    await loadOp;
}
```

```cpp
void LoadModel(ApiHandle<RenderingSession> session, ApiHandle<Entity> modelParent, std::string storageAccount, std::string containerName, std::string assetFilePath)
{
    LoadModelOptions modelOptions;
    modelOptions.Parent = modelParent;
    modelOptions.Blob.StorageAccountName = std::move(storageAccount);
    modelOptions.Blob.BlobContainerName = std::move(containerName);
    modelOptions.Blob.AssetPath = std::move(assetFilePath);

    ApiHandle<LoadModelResult> result;
    session->Connection()->LoadModelAsync(modelOptions,
        // completion callback
        [](Status status, ApiHandle<LoadModelResult> result)
        {
            printf("Loading: finished.");
        },
        // progress callback
        [](float progress)
        {
            printf("Loading: %.1f%%", progress * 100.f);
        }
    );
}
```

Se você quiser carregar um modelo usando um token SAS, use um código semelhante ao trecho a seguir:

```cs
async void LoadModel(RenderingSession session, Entity modelParent, string modelUri)
{
    // load a model that will be parented to modelParent
    var modelOptions = new LoadModelFromSasOptions(modelUri, modelParent);

    var loadOp = session.Connection.LoadModelFromSasAsync(modelOptions, (float progress) =>
    {
        Debug.WriteLine($"Loading: {progress * 100.0f}%");
    });

    await loadOp;
}
```

```cpp
void LoadModel(ApiHandle<RenderingSession> session, ApiHandle<Entity> modelParent, std::string modelUri)
{
    LoadModelFromSasOptions modelOptions;
    modelOptions.ModelUri = modelUri;
    modelOptions.Parent = modelParent;

    ApiHandle<LoadModelResult> result;
    session->Connection()->LoadModelFromSasAsync(modelOptions,
        // completion callback
        [](Status status, ApiHandle<LoadModelResult> result)
        {
            printf("Loading: finished.");
        },
        // progress callback
        [](float progress)
        {
            printf("Loading: %.1f%%", progress * 100.f);
        }
    );
}
```

Posteriormente, é possível percorrer a hierarquia de entidade e mudar as entidades e os componentes. Carregar o mesmo modelo várias vezes cria várias instâncias, cada uma com sua própria cópia da estrutura de entidade/componente. Como as malhas, os materiais e as texturas são [recursos compartilhados](../concepts/lifetime.md), os dados não serão carregados novamente. Portanto, instanciar um modelo mais de uma vez gera uma sobrecarga de memória relativamente pequena.

## <a name="api-documentation"></a>Documentação da API

* [C# RenderingConnection. LoadModelAsync ()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.loadmodelasync)
* [C# RenderingConnection. LoadModelFromSasAsync ()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.loadmodelfromsasasync)
* [C++ RenderingConnection:: LoadModelAsync ()](/cpp/api/remote-rendering/renderingconnection#loadmodelasync)
* [C++ RenderingConnection:: LoadModelFromSasAsync ()](/cpp/api/remote-rendering/renderingconnection#loadmodelfromsasasync)

## <a name="next-steps"></a>Próximas etapas

* [Entidades](entities.md)
* [Malhas](meshes.md)