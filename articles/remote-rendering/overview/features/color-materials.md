---
title: Materiais de cores
description: Descreve o tipo de material de cor.
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: fb26a669229ac140aba262032f8ce84ef9f37727
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99593370"
---
# <a name="color-materials"></a>Materiais de cores

*Materiais de cores* são um dos [tipos de material](../../concepts/materials.md) com suporte na renderização remota do Azure. Eles são usados para [malhas](../../concepts/meshes.md) que não devem receber nenhum tipo de iluminação, mas estão sempre com brilho completo. Esse pode ser o caso para materiais "brilhantes", como painéis de carro, lâmpadas ou dados que já incorpora iluminação estática, como modelos obtidos por meio de [Photogrammetry](https://en.wikipedia.org/wiki/Photogrammetry).

Materiais de cor são mais eficientes para renderizar do que os [materiais de PBR](pbr-materials.md) devido ao seu modelo de sombreamento mais simples. Eles também dão suporte a diferentes modos de transparência.

## <a name="common-material-properties"></a>Propriedades do material comum

Essas propriedades são comuns a todos os materiais:

* **albedoColor:** Essa cor é multiplicada por outras cores, como *albedoMap* ou *:::no-loc text="vertex"::: cores*. Se a *transparência* estiver habilitada em um material, o canal alfa será usado para ajustar a opacidade, com `1` significado totalmente opaco e `0` significado totalmente transparente. O padrão é branco.

  > [!NOTE]
  > Como os materiais de cor não refletem o ambiente, um material de cor totalmente transparente torna-se invisível. Isso é diferente para [materiais de PBR](pbr-materials.md).

* **albedoMap:** Uma [textura 2D](../../concepts/textures.md) para valores de albedo por pixel.

* **alphaClipEnabled** e **AlphaClipThreshold:** se *alphaClipEnabled* for true, todos os pixels em que o valor de alfa albedo for menor que *alphaClipThreshold* não serão desenhados. O recorte alfa pode ser usado mesmo sem habilitar a transparência e é muito mais rápido de renderizar. No entanto, os materiais recortados alfa ainda são mais lentos do que os materiais totalmente opacos. Por padrão, o recorte alfa é desabilitado.

* **textureCoordinateScale** e **textureCoordinateOffset:** a escala é multiplicada nas coordenadas de textura UV, o deslocamento é adicionado a ela. Pode ser usado para alongar e deslocar as texturas. A escala padrão é (1, 1) e o deslocamento é (0, 0).

* **useVertexColor:** Se a malha contiver :::no-loc text="vertex"::: cores e essa opção estiver habilitada, a cor das malhas :::no-loc text="vertex"::: será multiplicada em *albedoColor* e *albedoMap*. Por padrão, o *useVertexColor* está desabilitado.

* **isDoubleSided:** Se Double-sidedness for definido como true, os triângulos com esse material serão renderizados mesmo se a câmera estiver observando suas faces de apoio. Por padrão, essa opção está desabilitada. Consulte também [ :::no-loc text="Single-sided"::: renderização](single-sided-rendering.md).

* **TransparencyWritesDepth:** Se o sinalizador TransparencyWritesDepth estiver definido no material e o material for transparente, os objetos que usam esse material também contribuirão para o buffer de profundidade final. Consulte a propriedade material de cor *transparênciamode* na próxima seção. Habilitar esse recurso é recomendado se o caso de uso precisar de uma [Reprojeção de estágio tardia](late-stage-reprojection.md) mais plausível de cenas totalmente transparentes. Para cenas mistas/transparentes misturadas, essa configuração pode introduzir comportamento de Reprojeção inplausível ou artefatos de Reprojeção. Por esse motivo, a configuração padrão e recomendada para o caso de uso geral é desabilitar esse sinalizador. Os valores de profundidade gravados são obtidos da camada de profundidade por pixel do objeto mais próximo da câmera.

* **FresnelEffect:** Esse sinalizador de material habilita o [efeito Fresnel](../../overview/features/fresnel-effect.md) aditivo no respectivo material. A aparência do efeito é regida pelos outros parâmetros de Fresnel explicados a seguir. 

* **FresnelEffectColor:** A cor Fresnel usada para este material. Somente importante quando o bit de efeito Fresnel tiver sido definido neste material (veja acima). Essa propriedade controla a cor base do brilho Fresnel (consulte [Fresnel Effect](../../overview/features/fresnel-effect.md) para obter uma explicação completa). No momento, apenas os valores de canal RGB são importantes e o valor alfa será ignorado.

* **FresnelEffectExponent:** O expoente Fresnel usado para este material. Somente importante quando o bit de efeito Fresnel tiver sido definido neste material (veja acima). Essa propriedade controla a disseminação do Fresnel brilhar. O valor mínimo 0, 1 causa uma propagação em todo o objeto. O valor máximo 10,0 restringe o brilho para apenas as bordas mais gracing visíveis.

## <a name="color-material-properties"></a>Propriedades do material de cor

As propriedades a seguir são específicas para materiais de cores:

* **vertexMix:** Esse valor entre `0` e `1` especifica quão forte a :::no-loc text="vertex"::: cor em uma [malha](../../concepts/meshes.md) contribui para a cor final. No valor padrão de 1, a :::no-loc text="vertex"::: cor é multiplicada na cor albedo totalmente. Com um valor de 0, as :::no-loc text="vertex"::: cores são ignoradas inteiramente.

* **transparênciamode:** Ao contrário dos [materiais de PBR](pbr-materials.md), materiais de cor distinguem entre diferentes modos de transparência:

  1. **Opaco:** O modo padrão desabilita a transparência. O recorte alfa ainda é possível, porém, e deve ser preferencial, se for suficiente.
  
  1. **AlphaBlended:** Esse modo é semelhante ao modo de transparência para materiais PBR. Ele deve ser usado para materiais de visualização como vidro.

  1. **Aditivo:** Esse modo é o modo de transparência mais simples e eficiente. A contribuição do material é adicionada à imagem renderizada. Esse modo pode ser usado para simular objetos brilhantes (mas ainda transparentes), como marcadores usados para realçar objetos importantes.

## <a name="api-documentation"></a>Documentação da API

* [Classe C# ColorMaterial](/dotnet/api/microsoft.azure.remoterendering.colormaterial)
* [C# RenderingConnection. creatematerial ()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.creatematerial)
* [Classe C++ ColorMaterial](/cpp/api/remote-rendering/colormaterial)
* [C++ RenderingConnection:: creatematerial ()](/cpp/api/remote-rendering/renderingconnection#creatematerial)

## <a name="next-steps"></a>Próximas etapas

* [Materiais de PBR](pbr-materials.md)
* [Texturas](../../concepts/textures.md)
* [Malhas](../../concepts/meshes.md)