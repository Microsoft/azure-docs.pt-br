---
title: Materiais
description: Renderizar propriedades e descrição do material
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 4406e3875bd2d56a33bcc9a94a24ad874fecf8da
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99592170"
---
# <a name="materials"></a>Materiais

Os materiais são [recursos compartilhados](../concepts/lifetime.md) que definem como [malhas](meshes.md) são renderizadas. Os materiais são usados para especificar quais [texturas](textures.md) aplicar, se devem tornar os objetos transparentes e como a iluminação será calculada.

Os materiais são criados automaticamente durante a [conversão de modelo](../how-tos/conversion/model-conversion.md) e podem ser acessados no runtime. Também é possível criar materiais personalizados a partir do código e substituir os existentes. Esse cenário faz sentido principalmente se você quiser compartilhar o mesmo material em várias malhas. Como as mudanças de um material são visíveis em todas as malhas que fazem referência a ela, esse método pode ser usado para aplicar alterações com facilidade.

> [!NOTE]
> Alguns casos de uso, como realçar um objeto selecionado, podem ser aplicados por meio da mudança de materiais, mas é muito mais fácil fazer isso usando [HierarchicalStateOverrideComponent](../overview/features/override-hierarchical-state.md).

## <a name="material-types"></a>Tipos de material

O Azure Remote Rendering tem dois tipos de material distintos:

* [Materiais de PBR](../overview/features/pbr-materials.md) são usados para superfícies que devem ser renderizadas como fisicamente corretas, o quanto possível. A iluminação realista é calculada para esses materiais usando *renderização baseada em física* (PBR). Para aproveitar ao máximo esse tipo de material, é importante fornecer dados de entrada de alta qualidade, como esboços e mapas normais.

* [Materiais de cor](../overview/features/color-materials.md) são usados para casos em que nenhuma iluminação adicional é desejada. Esses materiais são sempre brilhantes e mais fáceis de configurar. Materiais de cor são usados para dados que não devem ter nenhuma iluminação ou que já incorporam iluminação estática, como modelos obtidos por meio de [fotogrametria](https://en.wikipedia.org/wiki/Photogrammetry).

## <a name="mesh-vs-meshcomponent-material-assignment"></a>Malha em comparação com atribuição de material MeshComponent

[Malhas](meshes.md) têm uma ou mais submalhas. Cada submalha faz referência a um material. É possível mudar o material a ser usado diretamente na malha ou substituir o material a ser usado em uma submalha em um [MeshComponent](meshes.md#meshcomponent).

Quando você muda um material diretamente no recurso de malha, essa alteração afeta todas as instâncias dessa malha. Alterá-lo no MeshComponent, no entanto, afeta apenas uma instância de malha. O método mais apropriado depende do comportamento desejado, mas a mudança de um MeshComponent é a abordagem mais comum.

## <a name="material-de-duplication"></a>Eliminação de duplicação do material

Durante a conversão, vários materiais com as mesmas propriedades e texturas são automaticamente desduplicadas em um único material. É possível desabilitar esse recurso nas [configurações de conversão](../how-tos/conversion/configure-model-conversion.md), mas é recomendável deixar habilitado para obter o melhor desempenho.

## <a name="material-classes"></a>Classes de material

Todos os materiais fornecidos pela API derivam da classe base `Material`. Seu tipo pode ser consultado por meio de `Material.MaterialSubType` ou convertendo-os diretamente:

```cs
void SetMaterialColorToGreen(Material material)
{
    if (material.MaterialSubType == MaterialType.Color)
    {
        ColorMaterial colorMaterial = material as ColorMaterial;
        colorMaterial.AlbedoColor = new Color4(0, 1, 0, 1);
        return;
    }

    PbrMaterial pbrMat = material as PbrMaterial;
    if (pbrMat != null)
    {
        pbrMat.AlbedoColor = new Color4(0, 1, 0, 1);
        return;
    }
}
```

```cpp
void SetMaterialColorToGreen(ApiHandle<Material> material)
{
    if (material->GetMaterialSubType() == MaterialType::Color)
    {
        ApiHandle<ColorMaterial> colorMaterial = material.as<ColorMaterial>();
        colorMaterial->SetAlbedoColor({ 0, 1, 0, 1 });
        return;
    }

    if (material->GetMaterialSubType() == MaterialType::Pbr)
    {
        ApiHandle<PbrMaterial> pbrMat = material.as<PbrMaterial>();
        pbrMat->SetAlbedoColor({ 0, 1, 0, 1 });
        return;
    }
}
```

## <a name="api-documentation"></a>Documentação da API

* [Classe de material C#](/dotnet/api/microsoft.azure.remoterendering.material)
* [Classe C# ColorMaterial](/dotnet/api/microsoft.azure.remoterendering.colormaterial)
* [Classe C# PbrMaterial](/dotnet/api/microsoft.azure.remoterendering.pbrmaterial)
* [C# RenderingConnection. creatematerial ()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.creatematerial)
* [Classe de material do C++](/cpp/api/remote-rendering/material)
* [Classe C++ ColorMaterial](/cpp/api/remote-rendering/colormaterial)
* [Classe C++ PbrMaterial](/cpp/api/remote-rendering/pbrmaterial)
* [C++ RenderingConnection:: creatematerial ()](/cpp/api/remote-rendering/renderingconnection#creatematerial)

## <a name="next-steps"></a>Próximas etapas

* [Materiais de PBR](../overview/features/pbr-materials.md)
* [Materiais de cores](../overview/features/color-materials.md)