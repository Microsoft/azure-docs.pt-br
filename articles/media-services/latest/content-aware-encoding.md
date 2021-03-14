---
title: Uma predefinição para codificação com reconhecimento de conteúdo
description: Este artigo discute a codificação com reconhecimento de conteúdo no Serviços de Mídia do Microsoft Azure v3.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: ce18e71ced320c408933caeb39b469d5885bd6ba
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2021
ms.locfileid: "101095943"
---
# <a name="use-the-content-aware-encoding-preset-to-find-the-optimal-bitrate-value-for-a-given-resolution"></a>Usar a predefinição de codificação com reconhecimento de conteúdo para localizar o valor de taxa de bits ideal para uma determinada resolução

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Para preparar o conteúdo para entrega por [streaming de taxa de bits adaptável](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming), o vídeo precisa ser codificado em taxas de bits múltiplas (alta para baixa). Isso garante uma degradação normal da qualidade, pois a taxa de bits é reduzida, portanto, é a resolução do vídeo. Essa codificação de taxa de bits múltipla usa uma chamada de escada de codificação – uma tabela de resoluções e taxas de bits, consulte as [predefinições de codificação interna](/rest/api/media/transforms/createorupdate#encodernamedpreset)dos serviços de mídia.

Você deve estar ciente do conteúdo que está processando e personalizar/ajustar a escada de codificação à complexidade do vídeo individual. Em cada resolução, há uma taxa de bits além da qual qualquer aumento na qualidade não é um tanto enganoso – o codificador opera com esse valor de taxa de bits ideal. O próximo nível de otimização é selecionar as resoluções com base no conteúdo – por exemplo, um vídeo de uma apresentação do PowerPoint não se beneficia da sua saída abaixo de 720p. Indo além, o codificador pode ser transtarefado para otimizar as configurações de cada uma das capturas dentro do vídeo. 

A predefinição de [streaming adaptável](autogen-bitrate-ladder.md) da Microsoft aborda parcialmente o problema da variabilidade na qualidade e na resolução dos vídeos de origem. Nossos clientes têm uma mistura variada de conteúdo, alguns na 1080p, outros em 720p, e alguns no SD e em resoluções menores. Além disso, nem todo o conteúdo de origem são mezaninos de alta qualidade de estúdios de filmes ou de TV. A predefinição de streaming adaptável resolve esses problemas, garantindo que a escada de taxa de bits nunca exceda a resolução ou a taxa de bits média da mezanino de entrada. No entanto, essa predefinição não examina as propriedades de origem que não sejam de resolução e taxa de bits.

## <a name="the-content-aware-encoding"></a>A codificação com reconhecimento de conteúdo

A predefinição de codificação com reconhecimento de conteúdo estende o mecanismo de "streaming de taxa de bits adaptável", incorporando uma lógica personalizada que permite ao codificador buscar o valor de taxa de bits ideal para uma determinada resolução, mas sem a necessidade de uma análise computacional extensiva. Essa predefinição produz um conjunto de MP4s alinhado a GOP. Dado qualquer conteúdo de entrada, o serviço executa uma análise leve inicial do conteúdo de entrada e usa os resultados para determinar o número ideal de camadas, taxa de bits apropriada e configurações de resolução para entrega por streaming adaptável. Essa predefinição é particularmente eficaz para vídeos de complexidade baixa e média, em que os arquivos de saída estarão em taxas de bits menores do que a predefinição de streaming adaptável, mas com uma qualidade que ainda oferece uma boa experiência para os visualizadores. A saída conterá arquivos MP4 com vídeo e áudio intercalados

Os gráficos de exemplo a seguir mostram a comparação usando métricas de qualidade como [PSNR](https://en.wikipedia.org/wiki/Peak_signal-to-noise_ratio) e [VMAF](https://en.wikipedia.org/wiki/Video_Multimethod_Assessment_Fusion). A fonte foi criada por meio da concatenação de pequenos clipes de capturas de alta complexidade de filmes e programas de TV, destinados a enfatizar o codificador. Por definição, essa predefinição produz resultados que variam de conteúdo para conteúdo – isso também significa que, para algum conteúdo, pode não haver uma redução significativa na taxa de bits ou melhoria na qualidade.

![Curva de taxa de distorção (RD) usando PSNR](media/content-aware-encoding/msrv1.png)

**Figura 1: curva de taxa de distorção (RD) usando a métrica PSNR para fonte de alta complexidade**

![Curva de taxa de distorção (RD) usando VMAF](media/content-aware-encoding/msrv2.png)

**Figura 2: curva de taxa de distorção (RD) usando a métrica VMAF para fonte de alta complexidade**

Abaixo estão os resultados de outra categoria de conteúdo de origem, em que o codificador foi capaz de determinar que a entrada era de baixa qualidade (muitos artefatos de compactação devido à baixa taxa de bits). Observe que, com a predefinição de reconhecimento de conteúdo, o codificador decidiu produzir apenas uma camada de saída – em uma taxa de bits baixa o suficiente para que a maioria dos clientes possa reproduzir o fluxo sem interrupções.

![Curva de RD usando PSNR](media/content-aware-encoding/msrv3.png)

**Figura 3: curva RD usando PSNR para entrada de baixa qualidade (em 1080p)**

![Curva de RD usando VMAF](media/content-aware-encoding/msrv4.png)

**Figura 4: curva RD usando VMAF para entrada de baixa qualidade (em 1080p)**

## <a name="how-to-use-the-content-aware-encoding-preset"></a>Como usar a predefinição de codificação com reconhecimento de conteúdo 

Você pode criar transformações que usam essa predefinição da seguinte maneira. 

Consulte a seção [próximas etapas](#next-steps) para ver tutoriais que usam saídas de transformação. O ativo de saída pode ser entregue de pontos de extremidade de streaming dos serviços de mídia em protocolos como MPEG-DASH e HLS (conforme mostrado nos tutoriais).

> [!NOTE]
> Certifique-se de usar a predefinição **ContentAwareEncoding** não ContentAwareEncodingExperimental.

```csharp
TransformOutput[] output = new TransformOutput[]
{
   new TransformOutput
   {
      // The preset for the Transform is set to one of Media Services built-in sample presets.
      // You can customize the encoding settings by changing this to use "StandardEncoderPreset" class.
      Preset = new BuiltInStandardEncoderPreset()
      {
         // This sample uses the new preset for content-aware encoding
         PresetName = EncoderNamedPreset.ContentAwareEncoding
      }
   }
};
```

> [!NOTE]
> Os trabalhos de codificação usando a `ContentAwareEncoding` predefinição estão sendo cobrados com base nos minutos de saída. 
  
## <a name="next-steps"></a>Próximas etapas

* [Tutorial: carregar, codificar e transmitir vídeos com os serviços de mídia v3](stream-files-tutorial-with-api.md)
* [Tutorial: Codificar um arquivo remoto baseado em URL e transmitir o vídeo – REST](stream-files-tutorial-with-rest.md)
* [Tutorial: codificar um arquivo remoto com base na URL e transmitir a video-CLI](stream-files-cli-quickstart.md)
* [Tutorial: Codificar um arquivo remoto baseado em URL e transmitir o vídeo – .NET](stream-files-dotnet-quickstart.md)
* [Tutorial: Codificar um arquivo remoto baseado em URL e transmitir o vídeo – Node.js](stream-files-nodejs-quickstart.md)
