---
title: Codificar .NET de transformação personalizada
description: Este tópico mostra como usar os serviços de mídia do Azure V3 para codificar uma transformação personalizada usando o .NET.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: c8c9f1deb1b776a818269909b3feb19f00a2c356
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954269"
---
# <a name="how-to-encode-with-a-custom-transform---net"></a>Como codificar com uma transformação personalizada-.NET

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Ao codificar com os serviços de mídia do Azure, você pode começar rapidamente com uma das predefinições internas recomendadas com base nas práticas recomendadas do setor, conforme demonstrado no tutorial de [arquivos de streaming](stream-files-tutorial-with-api.md) . Você também pode criar uma predefinição personalizada para direcionar seus requisitos específicos de cenário ou dispositivo.

## <a name="considerations"></a>Considerações

Ao criar predefinições personalizadas, as seguintes considerações se aplicam:

* Todos os valores para altura e largura em conteúdo AVC devem ser um múltiplo de 4.
* Nos serviços de mídia do Azure v3, todas as taxas de bits de codificação estão em bits por segundo. Isso é diferente das predefinições com nossas APIs v2, que usavam kilobits/segundo como a unidade. Por exemplo, se a taxa de bits em v2 tiver sido especificada como 128 (kilobits/segundo), em v3, ela seria definida como 128000 (bits/segundo).

## <a name="prerequisites"></a>Pré-requisitos 

[Criar uma conta dos Serviços de Mídia](./create-account-howto.md)

## <a name="download-the-sample"></a>Baixe o exemplo

Clone um repositório do GitHub que contém o exemplo de .NET Core completo em sua máquina usando o comando a seguir:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```
 
O exemplo de predefinição personalizado está localizado na pasta [EncodeCustomTransform](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/EncodeCustomTransform/).

## <a name="create-a-transform-with-a-custom-preset"></a>Criar uma transformação com uma predefinição personalizada 

Ao criar uma nova [Transformação](/rest/api/media/transforms), você precisará especificar o que deseja produzir como uma saída. O parâmetro necessário é um objeto [TransformOutput](/rest/api/media/transforms/createorupdate#transformoutput), como mostrado no código a seguir. Cada **TransformOutput** contém um **Predefinição**. A **predefinição** descreve as instruções detalhadas de operações de processamento de vídeo e/ou áudio que devem ser usadas para gerar o **TransformOutput** desejado. O seguinte **TransformOutput** cria configurações personalizadas de saída de codec e camada.

Ao criar uma [Transformação](/rest/api/media/transforms), você deverá verificar primeiro se já existe uma usando o método **Get**, conforme mostrado no código a seguir. Nos serviços de mídia v3, os métodos **Get** em entidades retornarão **NULL** se a entidade não existir (uma verificação que não diferencia maiúsculas de minúsculas no nome).

### <a name="example"></a>Exemplo

O exemplo a seguir define um conjunto de saídas que desejamos gerar quando essa transformação é usada. Primeiro, adicionamos uma camada AacAudio para a codificação de áudio e duas camadas H264Video para a codificação de vídeo. Nas camadas de vídeo, atribuímos rótulos para que possam ser usados nos nomes de arquivo de saída. Em seguida, queremos que a saída também inclua miniaturas. No exemplo abaixo, especificamos as imagens no formato PNG, geradas a 50% da resolução do vídeo de entrada e em três carimbos de data/hora-{25%, 50%, 75%} do comprimento do vídeo de entrada. Por fim, especificamos o formato dos arquivos de saída-um para vídeo + áudio e outro para as miniaturas. Como temos vários H264Layers, temos que usar macros que produzem nomes exclusivos por camada. Podemos usar uma `{Label}` `{Bitrate}` macro ou, o exemplo mostra o primeiro.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/EncodeCustomTransform/MediaV3ConsoleApp/Program.cs#EnsureTransformExists)]

## <a name="next-steps"></a>Próximas etapas

[Arquivos de streaming](stream-files-tutorial-with-api.md) 
