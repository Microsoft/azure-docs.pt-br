---
title: Obter informações sobre conversões
description: Obter informações sobre conversões
author: malcolmtyrrell
ms.author: matyrr
ms.date: 03/05/2020
ms.topic: how-to
ms.openlocfilehash: 89ec0ad40822785457e988cf9e0f9bd6d00ed81f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91576618"
---
# <a name="get-information-about-conversions"></a>Obter informações sobre conversões

## <a name="information-about-a-conversion-the-result-file"></a>Informações sobre uma conversão: o arquivo de resultado

Quando o serviço de conversão converte um ativo, ele grava um resumo de quaisquer problemas em um "arquivo de resultado". Por exemplo, se um arquivo `buggy.gltf` for convertido, o contêiner de saída conterá um arquivo chamado `buggy.result.json` .

O arquivo de resultado lista todos os erros e avisos ocorridos durante a conversão e fornece um resumo de resultado, que é um de `succeeded` , `failed` ou `succeeded with warnings` .
O arquivo de resultado é estruturado como uma matriz JSON de objetos, cada um com uma propriedade de cadeia de caracteres que é um de `warning` , `error` , `internal warning` , `internal error` e `result` . Haverá no máximo um erro ( `error` ou `internal error` ) e sempre haverá um `result` .

## <a name="example-result-file"></a>Exemplo de arquivo de *resultado*

O exemplo a seguir descreve uma conversão que gerou com êxito um arrAsset. No entanto, como havia uma textura ausente, o arrAsset resultante pode não ser o pretendido.

```JSON
[
  {"warning":"4004","title":"Missing texture","details":{"texture":"buggy_baseColor.png","material":"buggy_col"}},
  {"result":"succeeded with warnings"}
]
```

## <a name="information-about-a-converted-model-the-info-file"></a>Informações sobre um modelo convertido: o arquivo de informações

O arquivo arrAsset produzido pelo serviço de conversão destina-se exclusivamente ao consumo pelo serviço de renderização. No entanto, pode haver ocasiões em que você deseja acessar informações sobre um modelo sem iniciar uma sessão de renderização. Para dar suporte a esse fluxo de trabalho, o serviço de conversão coloca um arquivo JSON ao lado do arquivo arrAsset no contêiner de saída. Por exemplo, se um arquivo `buggy.gltf` for convertido, o contêiner de saída conterá um arquivo chamado `buggy.info.json` ao lado do ativo convertido `buggy.arrAsset` . Ele contém informações sobre o modelo de origem, o modelo convertido e sobre a conversão em si.

## <a name="example-info-file"></a>Arquivo de *informações* de exemplo

Aqui está um arquivo de *informações* de exemplo produzido pela conversão de um arquivo chamado `buggy.gltf` :

```JSON
{
    "files": {
        "input": "Buggy.gltf"
    },
    "conversionSettings": {
        "recenterToOrigin": true
    },
    "inputInfo": {
        "sourceAssetExtension": ".gltf",
        "sourceAssetFormat": "glTF2 Importer",
        "sourceAssetFormatVersion": "2.0",
        "sourceAssetGenerator": "COLLADA2GLTF"
    },
    "inputStatistics": {
        "numMeshes": 148,
        "numFaces": 308306,
        "numVertices": 245673,
        "numMaterial": 149,
        "numFacesSmallestMesh": 2,
        "numFacesBiggestMesh": 8764,
        "numNodes": 206,
        "numMeshUsagesInScene": 236,
        "maxNodeDepth": 3
    },
    "materialOverrides": {
        "numOverrides": 4,
        "numOverriddenMaterials": 4
    },
    "outputInfo": {
        "conversionToolVersion": "3b28d840de9916f9d628342f474d38c3ab949590",
        "conversionHash": "CCDB1F7A4C09F565"
    },
    "outputStatistics": {
        "numMeshPartsCreated": 236,
        "numMeshPartsInstanced": 88,
        "recenteringOffset": [
            -24.1,
            -50.9,
            -16.5974
        ],
        "boundingBox": {
            "min": [
                -43.52,
                -61.775,
                -79.6416
            ],
            "max": [
                43.52,
                61.775,
                79.6416
            ]
        }
    }
}
```

## <a name="information-in-the-info-file"></a>Informações no arquivo de informações

### <a name="the-files-section"></a>A seção *arquivos*

Esta seção contém os nomes de filefornecidos.

* `input`: O nome do arquivo de origem.
* `output`: O nome do arquivo de saída, quando o usuário especificou um nome não padrão.

### <a name="the-conversionsettings-section"></a>A seção *conversionSettings*

Esta seção contém uma cópia do [ConversionSettings](configure-model-conversion.md#settings-file) especificado quando o modelo foi convertido.

### <a name="the-inputinfo-section"></a>A seção *inputInfo*

Esta seção registra informações sobre o formato do arquivo de origem.

* `sourceAssetExtension`: A extensão de arquivo do arquivo de origem.
* `sourceAssetFormat`: Uma descrição do formato do arquivo de origem.
* `sourceAssetFormatVersion`: A versão do formato do arquivo de origem.
* `sourceAssetGenerator`: O nome da ferramenta que gerou o arquivo de origem, se disponível.

### <a name="the-materialoverrides-section"></a>A seção *materialOverrides*

Esta seção fornece informações sobre a [substituição de material](override-materials.md) quando um arquivo de substituição de material foi fornecido ao serviço de conversão.
Ele contém as seguintes informações:
* `numOverrides`: O número de entradas de substituição lidas do arquivo de substituição de material.
* `numOverriddenMaterials`: O número de materiais que foram substituídos.

### <a name="the-inputstatistics-section"></a>A seção *inputStatistics*

Esta seção fornece informações sobre a cena de origem. Em geral, haverá discrepâncias entre os valores nesta seção e os valores equivalentes na ferramenta que criou o modelo de origem. Essas diferenças são esperadas, pois o modelo é modificado durante as etapas de exportação e conversão.

* `numMeshes`: O número de partes de malha, onde cada parte pode fazer referência a um único material.
* `numFaces`: O número total de _triângulos_ em todo o modelo. Observe que a malha está triangulada durante a conversão. Esse número contribui para o limite do polígono no [tamanho do servidor de renderização padrão](../../reference/vm-sizes.md#how-the-renderer-evaluates-the-number-of-polygons).
* `numVertices`: O número total de vértices em todo o modelo.
* `numMaterial`: O número total de materiais em todo o modelo.
* `numFacesSmallestMesh`: O número de triângulos na menor malha do modelo.
* `numFacesBiggestMesh`: O número de triângulos na maior malha do modelo.
* `numNodes`: O número de nós no grafo de cena do modelo.
* `numMeshUsagesInScene`: O número de vezes que os nós referenciam malhas. Mais de um nó pode referenciar a mesma malha.
* `maxNodeDepth`: A profundidade máxima dos nós dentro do grafo de cena.

### <a name="the-outputinfo-section"></a>A seção *outputInfo*

Esta seção registra informações gerais sobre a saída gerada.

* `conversionToolVersion`: Versão do conversor de modelo.
* `conversionHash`: Um hash dos dados dentro do arrAsset que podem contribuir para renderização. Pode ser usado para entender se o serviço de conversão produziu um resultado diferente ao executar novamente no mesmo arquivo.

### <a name="the-outputstatistics-section"></a>A seção *outputStatistics*

Esta seção registra as informações calculadas do ativo convertido.

* `numMeshPartsCreated`: O número de malhas no arrAsset. Ele pode ser diferente da `numMeshes` `inputStatistics` seção, pois a instanciação é afetada pelo processo de conversão.
* `numMeshPartsInstanced`: O número de malhas reutilizadas no arrAsset.
* `recenteringOffset`: Quando a `recenterToOrigin` opção no [ConversionSettings](configure-model-conversion.md) está habilitada, esse valor é a conversão que moveria o modelo convertido de volta para sua posição original.
* `boundingBox`: Os limites do modelo.

## <a name="deprecated-features"></a>Recursos preteridos

O serviço de conversão grava os arquivos `stdout.txt` e no `stderr.txt` contêiner de saída, e eles tinham sido a única fonte de avisos e erros.
Esses arquivos agora estão preteridos. Em vez disso, use [os arquivos de resultado](#information-about-a-conversion-the-result-file) para essa finalidade.

## <a name="next-steps"></a>Próximas etapas

* [Conversão de modelo](model-conversion.md)
* [Configurar a conversão de modelo](configure-model-conversion.md)
