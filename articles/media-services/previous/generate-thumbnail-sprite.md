---
title: Gerar um sprite de miniatura com os Serviços de Mídia do Azure | Microsoft Docs
description: Este tópico mostra como gerar um sprite de miniatura com os Serviços de Mídia do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.openlocfilehash: e5d32d1bc3bd704b03e58c62251a323ed3f4662c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61229043"
---
# <a name="generate-a-thumbnail-sprite"></a>Gerar um sprite de miniatura  

Você pode usar o Media Encoder Standard para gerar um sprite de miniatura, que é um arquivo JPEG que contém várias miniaturas de resolução pequena reunidas em uma única imagem (grande), junto com um arquivo VTT. Esse arquivo VTT especifica o intervalo de tempo no vídeo de entrada que representa cada miniatura, junto com o tamanho e as coordenadas da miniatura dentro do arquivo JPEG grande. Players de vídeo usam o arquivo VTT e a imagem de sprite para mostrar uma barra de pesquisa “visual”, fornecendo um visualizador com feedback visual ao arrastar a linha do tempo do vídeo para frente e para trás.

Para usar o Media Encoder Standard para gerar o sprite de miniatura, a predefinição:

1. Precisa usar o formato de imagem de miniatura JPG
2. Precisa especificar valores de Início/Etapa/Intervalo como carimbos de data/hora ou valores de % (e não contagens de quadros) 
    
    1. Não há problema em mesclar carimbo de data/hora e valores de %

3. Precisa ter o valor SpriteColumn como um número não negativo maior que ou igual a 1

    1. Se SpriteColumn for definido como M >= 1, a imagem de saída será um retângulo com colunas M. Se o número de miniaturas geradas por meio do nº 2 não for um múltiplo exato de M, a última linha ficará incompleta e com pixels pretos.  

Veja um exemplo:

```json
{
    "Version": 1.0,
    "Codecs": [
    {
      "Start": "00:00:01",
      "Type": "JpgImage",
      "Step": "5%",
      "Range": "100%",
      "JpgLayers": [
        {
          "Type": "JpgLayer",
          "Width": "10%",
          "Height": "10%",
          "Quality": 90
        }
      ],
      "SpriteColumn": 10
    }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "JpgFormat"
          }
        }
   ]
}
```

## <a name="known-issues"></a>Problemas conhecidos

1.  Não é possível gerar uma imagem de sprite com uma única linha de imagens (SpriteColumn = 1 resulta em uma imagem com uma única coluna).
2.  Ainda não há compatibilidade com o agrupamento de imagens de sprite em imagens JPEG de tamanho moderado. Portanto, é preciso ter cuidado para limitar o número de miniaturas e o tamanho delas, para que o sprite de miniatura costurado resultante seja cerca de 8M pixels ou menos.
3.  O Player de Mídia do Azure é compatível com sprites nos navegadores Microsoft Edge, Chrome e Firefox. A análise de VTT não é compatível com o IE11.

## <a name="next-steps"></a>Próximas etapas

[Codificar conteúdo](media-services-encode-asset.md)
