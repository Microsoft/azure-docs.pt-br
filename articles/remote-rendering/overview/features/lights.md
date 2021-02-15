---
title: Iluminação de cena
description: Descrição e propriedades da fonte de luz
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.openlocfilehash: 49027899d66a2192cc311fb4dba66e441155b527
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92206842"
---
# <a name="scene-lighting"></a>Iluminação de cena

Por padrão, os objetos renderizados remotamente são acesos usando uma [luz céu](sky.md). Para a maioria dos aplicativos, isso já é suficiente, mas você pode adicionar mais fontes de luz à cena.

> [!IMPORTANT]
> Somente [materiais PBR](pbr-materials.md) são afetados por fontes de luz. Os [materiais coloridos](color-materials.md) sempre aparecem totalmente brilhantes.

> [!NOTE]
> Atualmente, não há suporte para as sombras de conversão. A renderização remota do Azure é otimizada para renderizar enormes quantidades de geometria, utilizando várias GPUs, se necessário. As abordagens tradicionais para a conversão de sombra não funcionam bem nesses cenários.

## <a name="common-light-component-properties"></a>Propriedades comuns do componente de luz

Todos os tipos de luz derivam da classe base abstrata `LightComponent` e compartilham essas propriedades:

* **Cor:** A cor da luz no [espaço gama](https://en.wikipedia.org/wiki/SRGB). Alfa é ignorado.

* **Intensidade:** O brilho da luz. Para luzes de ponto e Spot, a intensidade também define o quanto a luz se destaca.

## <a name="point-light"></a>Luz pontual

Na renderização remota do Azure `PointLightComponent` , o pode não apenas emitir luz de um único ponto, mas também de uma esfera pequena ou de um pequeno tubo, para simular fontes de luz mais suaves.

### <a name="pointlightcomponent-properties"></a>Propriedades de PointLightComponent

* **Raio:** O raio padrão é zero; nesse caso, a luz age como uma luz pontual. Se o raio for maior que zero, ele atuará como fonte de luz esférica, que altera a aparência dos realces especulares.

* **Comprimento:** Se ambos `Length` e `Radius` forem diferentes de zero, a luz agirá como uma luz de tubo. Isso pode ser usado para simular tubos de neon.

* **AttenuationCutoff:** Se esquerda para (0, 0) a atenuação da luz depende apenas de seu `Intensity` . No entanto, você pode fornecer distâncias mínimas/máximas personalizadas em que a intensidade da luz é dimensionada linearmente para 0. Esse recurso pode ser usado para impor um intervalo menor de influência de uma luz específica.

* **ProjectedCubemap:** Se for definido como um [cubemap](../../concepts/textures.md)válido, a textura será projetada na geometria ao redor da luz. A cor da cubemap é modulada com a cor da luz.

## <a name="spot-light"></a>Luz Spot

O `SpotLightComponent` é semelhante a `PointLightComponent` , mas a luz é restrita à forma de um cone. A orientação do cone é definida pelo *eixo z negativo da entidade do proprietário*.

### <a name="spotlightcomponent-properties"></a>Propriedades de SpotLightComponent

* **Raio:** O mesmo que para o `PointLightComponent` .

* **SpotAngleDeg:** Esse intervalo define o ângulo interno e externo do cone, medido em graus. Tudo dentro do ângulo interno é iluminado com brilho completo. Uma queda é aplicada em direção ao ângulo externo que gera um efeito semelhante a penumbra.

* **FalloffExponent:** Define a nitidez da transição entre o ângulo do cone interno e o exterior. Um valor mais alto resulta em uma transição mais nítida. O padrão de 1,0 resulta em uma transição linear.

* **AttenuationCutoff:** O mesmo que para o `PointLightComponent` .

* **Projected2dTexture:** Se for definido como uma [textura 2D](../../concepts/textures.md)válida, a imagem será projetada em geometry em que a luz se destaca. A cor da textura é modulada com a cor da luz.

## <a name="directional-light"></a>Luz direcional

O `DirectionalLightComponent` simula uma fonte de luz que está infinitamente distante. A luz se destaca na direção do *eixo z negativo da entidade do proprietário*. A posição da entidade é ignorada.

Não há nenhuma propriedade adicional.

## <a name="performance-considerations"></a>Considerações sobre o desempenho

Fontes de luz têm um impacto significativo no desempenho de renderização. Use-os com cuidado e apenas se exigido pelo aplicativo. Qualquer condição de iluminação global estática, incluindo um componente direcional estático, pode ser obtida com uma [textura céu personalizada](sky.md), sem custo de renderização adicional.

## <a name="api-documentation"></a>Documentação da API

* [Classe C# LightComponentBase](/dotnet/api/microsoft.azure.remoterendering.lightcomponentbase)
* [Classe C# PointLightComponent](/dotnet/api/microsoft.azure.remoterendering.pointlightcomponent)
* [Classe C# SpotLightComponent](/dotnet/api/microsoft.azure.remoterendering.spotlightcomponent)
* [Classe C# DirectionalLightComponent](/dotnet/api/microsoft.azure.remoterendering.directionallightcomponent)
* [Classe C++ LightComponentBase](/cpp/api/remote-rendering/lightcomponentbase)
* [Classe C++ PointLightComponent](/cpp/api/remote-rendering/pointlightcomponent)
* [Classe C++ SpotLightComponent](/cpp/api/remote-rendering/spotlightcomponent)
* [Classe C++ DirectionalLightComponent](/cpp/api/remote-rendering/directionallightcomponent)

## <a name="next-steps"></a>Próximas etapas

* [Materiais](../../concepts/materials.md)
* [Céu](sky.md)