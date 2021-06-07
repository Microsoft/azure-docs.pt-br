---
title: Substituir materiais durante a conversão de modelo
description: Explica o fluxo de trabalho de substituição de material no momento da conversão
author: florianborn71
ms.author: flborn
ms.date: 02/13/2020
ms.topic: how-to
ms.openlocfilehash: 11bd79a1bc88d2605a20744f5a6b6536d754c100
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91576635"
---
# <a name="override-materials-during-model-conversion"></a>Substituir materiais durante a conversão de modelo

As configurações de material no modelo de origem são usadas para definir os [materiais de PBR](../../overview/features/pbr-materials.md) usados pelo renderizador.
Às vezes, a [conversão padrão](../../reference/material-mapping.md) não fornece os resultados desejados e você precisa fazer alterações.
Quando um modelo é convertido para uso na renderização remota do Azure, você pode fornecer um arquivo de substituição de material para personalizar como a conversão de material é feita em uma base por material.
Se um arquivo chamado `<modelName>.MaterialOverrides.json` for encontrado no contêiner de entrada ao lado do modelo de entrada `<modelName>.<ext>` , ele será usado como o arquivo de substituição de material.

## <a name="the-override-file-used-during-conversion"></a>O arquivo de substituição usado durante a conversão

Como um exemplo simples, digamos que um modelo de caixa tenha um único material, chamado "default".
Além disso, digamos que sua cor de albedo precise ser ajustada para uso no ARR.
Nesse caso, um `box.MaterialOverrides.json` arquivo pode ser criado da seguinte maneira:

```json
[
    {
        "name": "Default",
        "albedoColor": {
            "r": 0.33,
            "g": 0.33,
            "b": 0.33,
            "a": 1.0
        }
    }
]
```

O `box.MaterialOverrides.json` arquivo é colocado no contêiner de entrada ao lado `box.fbx` , que diz ao serviço de conversão para aplicar as novas configurações.

### <a name="color-materials"></a>Materiais de cores

O modelo de [material de cor](../../overview/features/color-materials.md) descreve uma superfície constantemente sombreada que é independente da iluminação.
Materiais de cores são úteis para ativos feitos por algoritmos Photogrammetry, por exemplo.
Em arquivos de substituição de material, um material pode ser declarado como um material de cor, definindo `unlit` como `true` .

```json
[
    {
        "name": "Photogrametry_mat1",
        "unlit" : true
    },
    {
        "name": "Photogrametry_mat2",
        "unlit" : true
    }
]
```

### <a name="ignore-specific-texture-maps"></a>Ignorar mapas de textura específicos

Às vezes, talvez você queira que o processo de conversão ignore mapas de textura específicos. Esse pode ser o caso quando o modelo foi gerado por uma ferramenta que gera mapas especiais não compreendidos corretamente pelo processador. Por exemplo, um "OpacityMap" que é usado para definir algo diferente de Opacity ou um modelo em que "NormalMap" é armazenado como "BumpMap". (No último caso, você deseja ignorar "NormalMap", o que fará com que o conversor use "BumpMap" como "NormalMap".)

O princípio é simples. Basta adicionar uma propriedade chamada `ignoreTextureMaps` e adicionar qualquer mapa de textura que você deseja ignorar:

```json
[
    {
        "name": "Default",
        "ignoreTextureMaps": ["OpacityMap", "NormalMap"]
    }
]
```

Para obter a lista completa de mapas de textura que você pode ignorar, consulte o esquema JSON abaixo.

### <a name="applying-the-same-overrides-to-multiple-materials"></a>Aplicando as mesmas substituições a vários materiais

Por padrão, uma entrada no arquivo de substituições de material se aplica quando seu nome corresponde exatamente ao nome do material.
Como é muito comum que a mesma substituição deva ser aplicada a vários materiais, você pode, opcionalmente, fornecer uma expressão regular como o nome da entrada.
O campo `nameMatching` tem um valor padrão `exact` , mas pode ser definido como `regex` para declarar que a entrada deve ser aplicada a cada material correspondente.
A sintaxe usada é a mesma usada para JavaScript. O exemplo a seguir mostra uma substituição que se aplica a materiais com nomes como "Material2", "Material01" e "Material999".

```json
[
    {
        "name": "Material[0-9]+",
        "nameMatching": "regex",
        "albedoColor": {
            "r": 0.0,
            "g": 0.0,
            "b": 1.0,
            "a": 1.0
        }
    }
]
```

No máximo uma entrada em um arquivo de substituição de material se aplica a um único material.
Se houver uma correspondência exata (ou seja, `nameMatching` estiver ausente ou igual `exact` a) para o nome do material, essa entrada será escolhida.
Caso contrário, a primeira entrada Regex no arquivo que corresponde ao nome do material será escolhida.

### <a name="getting-information-about-which-entries-applied"></a>Obtendo informações sobre quais entradas são aplicadas

O [arquivo de informações](get-information.md#information-about-a-converted-model-the-info-file) gravado no contêiner de saída contém informações sobre o número de substituições fornecidas e o número de materiais que foram substituídos.

## <a name="json-schema"></a>JSON schema

O esquema JSON completo para arquivos de materiais é fornecido aqui. Com exceção de `unlit` e `ignoreTextureMaps` , as propriedades disponíveis são um subconjunto das propriedades descritas nas seções do material de [cores](../../overview/features/color-materials.md) e dos modelos de [material do PBR](../../overview/features/pbr-materials.md) .

```json
{
    "definitions" :
    {
        "color":
        {
            "type" : "object",
            "description" : "Color as 4 components vector",
            "properties":
            {
                "r": {"type":"number"},
                "g": {"type":"number"},
                "b": {"type":"number"},
                "a": {"type":"number"}
            },
            "required": ["r", "g", "b"]
        },
        "alpha":
        {
            "type" : "object",
            "description" : "Alpha channel for color",
            "properties":
            {
                "a": {"type":"number"}
            },
            "required": ["a"]
        },
        "colorOrAlpha":
        {
            "anyOf": [
                {"$ref": "#/definitions/color"},
                {"$ref": "#/definitions/alpha"}
            ]
        },
        "listOfMaps":
        {
            "type": "array",
            "items": {
                "type": "string",
                "enum": ["AlbedoMap",
                            "EmissiveMap",
                            "NormalMap",
                            "OcclusionMap",
                            "RoughnessMap",
                            "MetalnessMap",
                            "ReflectivityMap",
                            "BumpMap",
                            "OpacityMap",
                            "DiffuseMap",
                            "SpecularMap",
                            "ShininessMap",
                            "MetallicRoughnessMap",
                            "SpecularGlossinessMap"]
            }
        }
    },
    "type" : "array",
    "description" : "List of materials to override",
    "items":
    {
        "type" : "object",
        "description" : "List of parameters to override",
        "properties":
        {
            "name": { "type" : "string"},
            "nameMatching" : { "type" : "string", "enum" : ["exact", "regex"] },
            "unlit": { "type" : "boolean" },
            "albedoColor": { "$ref": "#/definitions/colorOrAlpha" },
            "roughness": { "type": "number" },
            "metalness": { "type": "number" },
            "transparent": { "type" : "boolean" },
            "alphaClipEnabled": { "type" : "boolean" },
            "alphaClipThreshold": { "type": "number" },
            "useVertexColor": { "type" : "boolean" },
            "isDoubleSided": { "type" : "boolean" },
            "ignoreTextureMaps": { "$ref" : "#/definitions/listOfMaps" }
        },
        "required": ["name"],
        "additionalProperties" : false
    }
}
```

## <a name="next-steps"></a>Próximas etapas

* [Materiais de cores](../../overview/features/color-materials.md)
* [Materiais de PBR](../../overview/features/pbr-materials.md)
