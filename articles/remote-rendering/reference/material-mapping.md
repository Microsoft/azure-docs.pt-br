---
title: Mapeamento de material para formatos de modelo
description: Descreve a conversão padrão dos formatos de origem do modelo para o material do PBR
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: 8313243bf680ea1a1d63f2719b647149a04935a9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96024037"
---
# <a name="material-mapping-for-model-formats"></a>Mapeamento de material para formatos de modelo

Quando um ativo de origem é [convertido como um modelo](../how-tos/conversion/model-conversion.md), o conversor cria [materiais](../concepts/materials.md) para cada [malha](../concepts/meshes.md). O modo como os materiais são criados pode ser [substituído](../how-tos/conversion/override-materials.md). No entanto, por padrão, a conversão criará [materiais de PBR](../overview/features/pbr-materials.md). Como cada formato de arquivo de origem, como FBX, usa suas próprias convenções para definir materiais, essas convenções devem ser mapeadas para os parâmetros de material do PBR da renderização remota do Azure. 

Este artigo lista os mapeamentos exatos usados para converter materiais de ativos de origem em materiais de tempo de execução.

## <a name="gltf"></a>glTF

Quase tudo, da especificação glTF 2,0, tem suporte na renderização remota do Azure, exceto *EmissiveFactor* e *EmissiveTexture*.

A tabela abaixo mostra o mapeamento:

| glTF | Azure Remote Rendering |
|:-------------------|:--------------------------|
|   baseColorFactor   |   albedoColor              |
|   baseColorTexture  |   albedoMap                |
|   metallicFactor    |   metal                |
|   metallicTexture   |   metalnessMap             |
|   roughnessFactor   |   áspero                |
|   roughnessTexture  |   roughnessMap             |
|   occlusionFactor   |   oclusão                |
|   occlusionTexture  |   occlusionMap             |
|   normalTexture     |   normalMap                |
|   alphaCutoff       |   alphaClipThreshold       |
|   alphamode. opaco  |   alphaClipEnabled = false, Transparent = false |
|   MÁSCARA de alfamode    |   alphaClipEnabled = true, Transparent = false  |
|   alphamode. BLEND   |   istransparente = verdadeiro     |
|   doubleSided       |   isDoubleSided            |
|   emissiveFactor    |   -                        |
|   emissiveTexture   |   -                        |

Cada textura em glTF pode ter um `texCoord` valor, que também tem suporte nos materiais de renderização remota do Azure.

### <a name="embedded-textures"></a>Texturas inseridas

Há suporte para texturas inseridas em arquivos *\* . bin* ou *\* . glb* .

### <a name="supported-gltf-extension"></a>Extensão glTF com suporte

Além do conjunto de recursos base, a renderização remota do Azure dá suporte às seguintes extensões glTF:

* [MSFT_packing_occlusionRoughnessMetallic](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Vendor/MSFT_packing_occlusionRoughnessMetallic/README.md)
* [KHR_materials_unlit](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Khronos/KHR_materials_unlit/README.md): corresponde aos [materiais de cor](../overview/features/color-materials.md). Para materiais *emissiva* , é recomendável usar essa extensão.
* [KHR_materials_pbrSpecularGlossiness](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Khronos/KHR_materials_pbrSpecularGlossiness/README.md): em vez de texturas de irregularidades metálicas, você pode fornecer texturas difusas de especulação de textura. A implementação de renderização remota do Azure segue diretamente as fórmulas de conversão da extensão.

## <a name="fbx"></a>FBX

O formato FBX é fechado-os materiais de código-fonte e FBX não são compatíveis com os materiais de PBR em geral. O FBX usa uma descrição complexa de superfícies com muitos parâmetros e propriedades exclusivos, e **nem todos eles são usados pelo pipeline de renderização remoto do Azure**.

> [!IMPORTANT]
> O pipeline de conversão do modelo de renderização remota do Azure só dá suporte ao **FBX 2011 e superior**.

O formato FBX define uma abordagem conservadora para materiais, há apenas dois tipos na especificação FBX oficial:

* *Lambert* – normalmente, não é usado por muito tempo, mas ainda é suportado convertendo em Phong no momento da conversão.
* *Phong* -quase todos os materiais e a maioria das ferramentas de conteúdo usam esse tipo.

O modelo Phong é mais preciso e é usado como o *único* modelo para materiais de FBX. A seguir, ele será chamado de *material FBX*.

> O Maya usa duas extensões personalizadas para FBX definindo propriedades personalizadas para os tipos PBR e Stingray de um material. Esses detalhes não estão incluídos na especificação FBX, portanto, ele não é suportado pelo processamento remoto do Azure atualmente.

Os materiais de FBX usam o conceito difuso-Especulate-SpecularLevel, portanto, para converter de uma textura Difusa em um mapa do albedo, precisamos calcular os outros parâmetros para subtrai-los da difusa.

> Todas as cores e texturas em FBX estão no espaço sRGB (também conhecido como espaço gama), mas a renderização remota do Azure funciona com espaço linear durante a visualização e, no final do quadro, converte tudo de volta para o espaço sRGB. O pipeline de ativos de renderização remota do Azure converte tudo em espaço linear para enviá-lo como dados preparados para o renderizador.

Esta tabela mostra como as texturas são mapeadas de materiais FBX para os materiais de renderização remota do Azure. Alguns deles não são usados diretamente, mas em combinação com outras texturas que participam das fórmulas (por exemplo, a textura difusa):

| FBX | Azure Remote Rendering |
|:-----|:----|
| AmbientColor | Mapa oclusão   |
| DiffuseColor | *usado para albedo, metal* |
| TransparentColor | *usado para o canal alfa de albedo* |
| TransparencyFactor | *usado para o canal alfa de albedo* |
| Opacidade | *usado para o canal alfa de albedo* |
| SpecularColor | *usado para albedo, metal, irregularidade* |
| SpecularFactor| *usado para albedo, metal, irregularidade* |
| ShininessExponent | *usado para albedo, metal, irregularidade* |
| NormalMap | NormalMap |
| Aumentar | *convertido em NormalMap* |
| EmissiveColor | - |
| EmissiveFactor | - |
| ReflectionColor | - |
| DisplacementColor | - |

O mapeamento acima é a parte mais complexa da conversão de material, devido a muitas suposições que precisam ser feitas. Discutimos essas suposições abaixo.

Algumas definições são usadas abaixo:

* `Specular` =  `SpecularColor` * `SpecularFactor`
* `SpecularIntensity` = `Specular`. Vermelho ∗ 0,2125 +  `Specular` . Verde ∗ 0,7154 + `Specular` . Blue ∗ 0, 721
* `DiffuseBrightness` = 0,299 * `Diffuse` . Vermelho<sup>2</sup> + 0,587 * `Diffuse` . Verde<sup>2</sup> + 0,114 * `Diffuse` . Azul<sup>2</sup>
* `SpecularBrightness` = 0,299 * `Specular` . Vermelho<sup>2</sup> + 0,587 * `Specular` . Verde<sup>2</sup> + 0,114 * `Specular` . Azul<sup>2</sup>
* `SpecularStrength` = Max ( `Specular` . Vermelho, `Specular` . Verde, `Specular` . Azul

A fórmula SpecularIntensity é obtida [aqui](https://en.wikipedia.org/wiki/Luma_(video)).
A fórmula de brilho é descrita nesta [especificação](http://www.itu.int/dms_pubrec/itu-r/rec/bt/R-REC-BT.601-7-201103-I!!PDF-E.pdf).

### <a name="roughness"></a>Irregularidade

`Roughness` é calculado a partir de `Specular` e `ShininessExponent` usando [esta fórmula](https://www.cs.cornell.edu/~srm/publications/EGSR07-btdf.pdf). A fórmula é uma aproximação de áspero do expoente especular Phong:

```cpp
Roughness = sqrt(2 / (ShininessExponent * SpecularIntensity + 2))
```

### <a name="metalness"></a>Metal

`Metalness` é calculado de `Diffuse` e `Specular` usando essa [fórmula da especificação glTF](https://github.com/bghgary/glTF/blob/gh-pages/convert-between-workflows-bjs/js/babylon.pbrUtilities.js).

A ideia aqui é que resolvemos a equação: AX<sup>2</sup> + BX + C = 0.
Basicamente, as superfícies interelétricas refletem cerca de 4% da luz de uma forma especular e o restante é difuso. As superfícies metálicas não refletem nenhuma luz de forma difusa, mas todas de uma maneira especular.
Essa fórmula tem algumas desvantagens, porque não há como distinguir entre superfícies brilhantes e de superfície metálica brilhante. Assumimos a maior parte do tempo em que a superfície tem propriedades metálicas e, consequentemente, as superfícies de plástico/borracha brilhantes podem não parecer conforme o esperado.

```cpp
dielectricSpecularReflectance = 0.04
oneMinusSpecularStrength = 1 - SpecularStrength

A = dielectricSpecularReflectance
B = (DiffuseBrightness * (oneMinusSpecularStrength / (1 - A)) + SpecularBrightness) - 2 * A
C = A - SpecularBrightness
squareRoot = sqrt(max(0.0, B * B - 4 * A * C))
value = (-B + squareRoot) / (2 * A)
Metalness = clamp(value, 0.0, 1.0);
```

### <a name="albedo"></a>Albedo

`Albedo` é computado de `Diffuse` , `Specular` e `Metalness` .

Conforme descrito na seção de metal, as superfícies interelétricas refletem cerca de 4% da luz.  
A ideia aqui é interpolar linearmente entre `Dielectric` e `Metal` cores usando `Metalness` valor como um fator. Se a metal for `0.0` , dependendo da especulação, ela será uma cor escura (se especular for alta) ou difusa não será alterada (se nenhum especular estiver presente). Se a metal for um valor grande, a cor difusa desaparecerá em favor da cor especular.

```cpp
dielectricSpecularReflectance = 0.04
oneMinusSpecularStrength = 1 - SpecularStrength

dielectricColor = diffuseColor * (oneMinusSpecularStrength / (1.0f - dielectricSpecularReflectance) / max(1e-4, 1.0 - metalness))
metalColor = (Specular - dielectricSpecularReflectance * (1.0 - metalness)) * (1.0 / max(1e-4, metalness))
albedoRawColor = lerpColors(dielectricColor, metalColor, metalness * metalness)
AlbedoRGB = clamp(albedoRawColor, 0.0, 1.0);
```

`AlbedoRGB` foi calculado pela fórmula acima, mas o canal alfa requer cálculos adicionais. O formato FBX é vago sobre transparência e tem várias maneiras de defini-lo. Ferramentas de conteúdo diferentes usam métodos diferentes. A ideia aqui é unificar em uma fórmula. Isso faz com que alguns ativos sejam mostrados incorretamente como transparentes, porém, se eles não forem criados de forma comum.

Isso é computado do `TransparentColor` , `TransparencyFactor` , `Opacity` :

Se `Opacity` for definido, use-o diretamente: `AlbedoAlpha`  =  `Opacity` senão  
Se `TransparencyColor` for definido, então `AlbedoAlpha` = 1,0-(( `TransparentColor` . Vermelho + `TransparentColor` . Verde + `TransparentColor` . Azul)/3,0) else  
Se `TransparencyFactor` , em seguida, `AlbedoAlpha` = 1,0- `TransparencyFactor`

A `Albedo` cor final tem quatro canais, combinando `AlbedoRGB` com o `AlbedoAlpha` .

### <a name="summary"></a>Resumo

Para resumir aqui, `Albedo` será muito próximo do original `Diffuse` , se `Specular` estiver próximo de zero. Caso contrário, a superfície se parecerá com uma superfície metálica e perderá a cor difusa. A superfície terá uma aparência mais elegante e reflexiva se `ShininessExponent` for grande o suficiente e `Specular` for brilhante. Caso contrário, a superfície terá uma aparência aproximada e quase não refletirá o ambiente.

### <a name="known-issues"></a>Problemas conhecidos

* A fórmula atual não funciona bem para geometria colorida simples. Se o `Specular` for suficientemente brilhante, todas as geometrias se tornarão superfícies metálicas refletidas sem qualquer cor. A solução alternativa aqui é reduzir `Specular` para 30% do original ou para usar a configuração de conversão [fbxAssumeMetallic](../how-tos/conversion/configure-model-conversion.md#converting-from-older-fbx-formats-with-a-phong-material-model).
* Os materiais de PBR foram adicionados recentemente às `Maya` `3DS Max` ferramentas de criação de conteúdo e. Eles usam Propriedades de caixa preta definidas pelo usuário personalizadas para passá-la para FBX. A renderização remota do Azure não lê essas propriedades adicionais porque elas não estão documentadas e o formato é fechado-código-fonte.

## <a name="next-steps"></a>Próximas etapas

* [Conversão de modelo](../how-tos/conversion/model-conversion.md)
* [Substituição de materiais durante a conversão de modelo](../how-tos/conversion/override-materials.md)
