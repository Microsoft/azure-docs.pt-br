---
title: Efeito Fresnel
description: Página de explicação de recursos para o efeito de material Fresnel
author: jumeder
ms.author: jumeder
ms.date: 11/09/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: f63cd3b50642c3cf531387b4446992b6f15116f2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99594105"
---
# <a name="fresnel-effect"></a>Efeito Fresnel

O recurso material do Fresnel Effect é um efeito não fisicamente correto, ad-hoc. O recurso se baseia na observação física de objetos que se tornam mais refletidos nesses ângulos. A reflexão da Fresnel em si já está fisicamente incorporada no [modelo de material do PBR](../../overview/features/pbr-materials.md) usado na renderização remota do Azure. Por outro lado, o recurso material de efeito do Fresnel é apenas um efeito de cor aditivo sem dependência de [luzes](../../overview/features/lights.md) ou do [ambiente do céu](../../overview/features/sky.md).

O efeito Fresnel dá aos objetos afetados um brilho colorido em relação às suas bordas. Informações sobre a personalização de efeito e exemplos de resultados de renderização podem ser encontradas nas seções a seguir.

## <a name="enabling-the-fresnel-effect"></a>Habilitando o efeito de Fresnel

Para usar o recurso de efeito de Fresnel, ele precisa ser habilitado nos materiais em questão. Você pode habilitá-lo definindo o FresnelEffect bit do [PbrMaterialFeatures](/dotnet/api/microsoft.azure.remoterendering.pbrmaterialfeatures) no [material do PBR](../../overview/features/pbr-materials.md). O mesmo padrão se aplica ao [ColorMaterialFeatures](/dotnet/api/microsoft.azure.remoterendering) e ao [material de cor](../../overview/features/color-materials.md). Consulte a seção de exemplos de código para ver uma demonstração de uso.

Depois de habilitar, o efeito Fresnel estará imediatamente visível. Por padrão, o brilho será branco (1, 1, 1, 1) e terá um expoente de 1. Você pode personalizar essas configurações usando os setters de parâmetro abaixo.

## <a name="customizing-the-effect-appearance"></a>Personalizando a aparência do efeito

Atualmente, o efeito de Fresnel pode ser personalizado por material usando as seguintes propriedades:

| Propriedade material | Tipo | Explicação |
|-------------------|------|-------------|
| FresnelEffectColor | Color4 | A cor que é adicionada no máximo como o brilho do Fresnel. O canal alfa é ignorado no momento. |
| FresnelEffectExponent | FLOAT | A disseminação do Fresnel brilha. Varia de 0, 1 (espalhado por todo o objeto) a 10 (somente os ângulos mais gracings). |

Na prática, as configurações de cor e expoente diferentes terão esta aparência:

![Exemplos de efeito de Fresnel](./media/fresnel-effect-examples.png)

O expoente do efeito Fresnel é aumentado progressivamente de 1 para 10 para cada linha de cor. Fazer isso progressivamente o brilho do Fresnel para as bordas dos objetos exibidos. O efeito Fresnel também não é afetado pela transparência, como você pode ver no exemplo a seguir:

![Exemplos de transparência de efeito de Fresnel](./media/fresnel-effect-transparent-examples.png)

Como mostrado, os objetos na diagonal são totalmente transparentes, mas o brilho Fresnel permanece. O efeito imita Fresnel com base em termos físicos, que também está presente nessas capturas de tela.

## <a name="code-samples"></a>Exemplos de código

Os exemplos de código a seguir mostram como habilitar e personalizar o efeito de Fresnel para um [material de PBR](../../overview/features/pbr-materials.md) e um [material de cor](../../overview/features/color-materials.md):

```cs
    void SetFresnelEffect(RenderingSession session, Material material)
    {
        if (material.MaterialSubType == MaterialType.Pbr)
        {
            var pbrMaterial = material as PbrMaterial;
            pbrMaterial.PbrFlags |= PbrMaterialFeatures.FresnelEffect;
            pbrMaterial.FresnelEffectColor = new Color4(1.0f, 0.5f, 0.1f, 1.0f);
            pbrMaterial.FresnelEffectExponent = 3.141592f;
        }
        else if (material.MaterialSubType == MaterialType.Color)
        {
            var colorMaterial = material as ColorMaterial;
            colorMaterial.ColorFlags |= ColorMaterialFeatures.FresnelEffect;
            colorMaterial.FresnelEffectColor = new Color4(0.25f, 1.0f, 0.25f, 1.0f);
            colorMaterial.FresnelEffectExponent = 7.654321f;
        }
    }
```

```cpp
void SetFresnelEffect(ApiHandle<RenderingSession> session, ApiHandle<Material> material)
{
    if (material->GetMaterialSubType() == MaterialType::Pbr)
    {
        auto pbrMaterial = material.as<PbrMaterial>();
        auto featureFlags = PbrMaterialFeatures((int32_t)pbrMaterial->GetPbrFlags() | (int32_t)PbrMaterialFeatures::FresnelEffect);
        pbrMaterial->SetPbrFlags(featureFlags);
        pbrMaterial->SetFresnelEffectColor(Color4{ 1.f, 0.5f, 0.1f, 1.f });
        pbrMaterial->SetFresnelEffectExponent(3.141592f);

    }
    else if (material->GetMaterialSubType() == MaterialType::Color)
    {
        auto colorMaterial = material.as<ColorMaterial>();
        auto featureFlags = ColorMaterialFeatures((int32_t)colorMaterial->GetColorFlags() | (int32_t)ColorMaterialFeatures::FresnelEffect);
        colorMaterial->SetColorFlags(featureFlags);
        colorMaterial->SetFresnelEffectColor(Color4{ 0.25f, 1.f, 0.25f, 1.f });
        colorMaterial->SetFresnelEffectExponent(7.654321f);
    }
}
```

## <a name="api-documentation"></a>Documentação da API

* [PbrMaterialFeatures C#](/dotnet/api/microsoft.azure.remoterendering.pbrmaterialfeatures)
* [C++ PbrMaterialFeatures](/cpp/api/remote-rendering/pbrmaterialfeatures)
* [ColorMaterialFeatures C#](/dotnet/api/microsoft.azure.remoterendering.colormaterialfeatures)
* [C++ ColorMaterialFeatures](/cpp/api/remote-rendering/colormaterialfeatures)

## <a name="next-steps"></a>Próximas etapas

* [Materiais](../../concepts/materials.md)
* [Materiais de PBR](../../overview/features/pbr-materials.md)
* [Materiais de cores](../../overview/features/color-materials.md)