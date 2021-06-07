---
title: Como cortar arquivos de vídeo com os serviços de mídia-.NET | Microsoft Docs
description: Corte é o processo de seleção de uma janela retangular no quadro de vídeo e codificação apenas dos pixels nessa janela. Este tópico mostra como cortar arquivos de vídeo com os serviços de mídia usando o .NET.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: how-to
ms.date: 03/24/2021
ms.author: inhenkel
ms.openlocfilehash: 43d0e1e3b8c0eaa37a3b09557b333c3abafe8b63
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105572311"
---
# <a name="how-to-crop-video-files-with-media-services---net"></a>Como cortar arquivos de vídeo com os serviços de mídia-.NET

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Você pode usar os serviços de mídia para cortar um vídeo de entrada. Corte é o processo de seleção de uma janela retangular no quadro de vídeo e codificação apenas dos pixels nessa janela. O diagrama a seguir ajuda a ilustrar o processo.

## <a name="pre-processing-stage"></a>Pré-processando estágio

O corte é um estágio de pré-processamento, portanto, os *parâmetros de corte* na predefinição de codificação se aplicam ao vídeo de *entrada* . A codificação é um estágio posterior, e as configurações de largura/altura se aplicam ao vídeo *pré-processado* , não ao vídeo original. Ao criar sua predefinição, faça o seguinte:

1. Selecione os parâmetros de corte com base no vídeo de entrada original
1. Selecione as configurações de codificação com base no vídeo cortado.

> [!WARNING]
> Se as configurações de codificação não forem correspondentes ao vídeo cortado, a saída não será como esperado.

Por exemplo, o vídeo de entrada tem uma resolução de pixels de 1920 x 1080 (taxa de proporção de 16:9), mas tem barras pretas (caixas de pilar) à esquerda e à direita, para que apenas uma janela 4:3 ou 1440 x 1080 pixels contenha vídeo ativo. Você pode cortar as barras pretas e codificar a área 1440 x 1080.

## <a name="transform-code"></a>Código de transformação

O trecho de código a seguir ilustra como escrever uma transformação no .NET para cortar vídeos.  O código pressupõe que você tenha um arquivo local com o qual trabalhar.

- Esquerda é o local mais à esquerda do corte.
- Top é o local mais alto do corte.
- Largura é a largura final do corte.
- Altura é a altura final do corte.

```dotnet
var preset = new StandardEncoderPreset

    {

        Filters = new Filters

        {                   

            Crop = new Rectangle

            {

                Left = "200",

                Top = "200",

                Width = "1280",

                Height = "720"

            }

        },

        Codecs =

        {

            new AacAudio(),

            new H264Video()

            {

                Layers =

                {                           

                    new H264Layer

                    {

                        Bitrate = 1000000,

                        Width = "1280",

                        Height = "720"

                    }

                }

            }

        },

        Formats =

        {

            new Mp4Format

            {

                FilenamePattern = "{Basename}_{Bitrate}{Extension}"

            }

        }

    }

```
