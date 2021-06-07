---
title: Uma predefinição para codificação com reconhecimento de conteúdo
description: Este artigo discute a codificação com reconhecimento de conteúdo nos Serviços de Mídia do Microsoft Azure v3.
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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101095943"
---
# <a name="use-the-content-aware-encoding-preset-to-find-the-optimal-bitrate-value-for-a-given-resolution"></a>Usar a predefinição de codificação com reconhecimento de conteúdo para localizar o valor de taxa de bits ideal para determinada resolução

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

A fim de preparar o conteúdo para entrega por [streaming de taxa de bits adaptável](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming), o vídeo precisa ser codificado em várias taxas de bits (de alta a baixa). Isso garante uma degradação normal da qualidade, pois a taxa de bits é reduzida, assim como a resolução do vídeo. Essa codificação em várias taxa de bits usa a chamada escada de codificação – uma tabela de resoluções e taxas de bits – confira as [predefinições de codificação interna](/rest/api/media/transforms/createorupdate#encodernamedpreset) dos Serviços de Mídia.

Você deve estar ciente do conteúdo que está processando e personalizar/ajustar a escada de codificação à complexidade de cada vídeo individual. Em cada resolução há uma taxa de bits limite, acima da qual os aumentos de qualidade são imperceptíveis – o codificador opera com esse valor de taxa de bits ideal. O próximo nível de otimização é selecionar as resoluções com base no conteúdo – por exemplo, um vídeo de uma apresentação do PowerPoint não se beneficia de resoluções inferiores a 720p. Indo além, o codificador pode ser encarregado de otimizar as configurações de cada uma das capturas do vídeo. 

A predefinição de [Streaming Adaptável](autogen-bitrate-ladder.md) da Microsoft aborda parcialmente o problema da variabilidade na qualidade e na resolução dos vídeos de origem. Nossos clientes têm um mix variado de conteúdos, alguns em resolução 1080p, outros em 720p e alguns ainda em SD ou em resoluções mais baixas. Além disso, nem todo o conteúdo de origem corresponde aos mezaninos de alta qualidade dos filmes ou estúdios de TV. A predefinição de Streaming Adaptável aborda esses problemas verificando se a escada da taxa de bits nunca excede a resolução ou a taxa de bits média do mezanino de entrada. No entanto, essa predefinição não examina as propriedades de origem que não sejam a resolução e a taxa de bits.

## <a name="the-content-aware-encoding"></a>Codificação com reconhecimento de conteúdo

A predefinição de codificação com reconhecimento de conteúdo estende o mecanismo de "streaming de taxa de bits adaptável", incorporando uma lógica personalizada que permite ao codificador buscar o valor de taxa de bits ideal para uma determinada resolução, mas sem exigir uma análise computacional extensiva. Essa predefinição produz um conjunto de MP4s alinhados a GOP. Dado qualquer conteúdo de entrada, o serviço executa uma análise inicial leve desse conteúdo e usa os resultados para determinar o número ideal de camadas, a taxa de bits apropriada e as configurações de resolução para entrega por streaming adaptável. Essa predefinição é particularmente eficaz para vídeos de baixa e de média complexidade, em que os arquivos de saída estarão em taxas de bits mais baixas do que a predefinição de streaming adaptável, mas com uma qualidade que ainda oferece uma boa experiência aos espectadores. A saída conterá arquivos MP4 com vídeo e áudio intercalados

Os gráficos de exemplo a seguir mostram a comparação usando métricas de qualidade como [PSNR](https://en.wikipedia.org/wiki/Peak_signal-to-noise_ratio) e [VMAF](https://en.wikipedia.org/wiki/Video_Multimethod_Assessment_Fusion). A fonte foi criada por meio da concatenação de pequenos clipes de capturas de alta complexidade de filmes e programas de TV, destinados a estressar o codificador. Por definição, essa predefinição produz resultados que variam de conteúdo para conteúdo – isso também significa que, para alguns conteúdos, pode não haver uma redução significativa na taxa de bits ou um aprimoramento significativo na qualidade.

![Curva de RD (taxa de distorção) usando PSNR](media/content-aware-encoding/msrv1.png)

**Figura 1: curva de RD (taxa de distorção) usando a métrica PSNR para fonte de alta complexidade**

![Curva de RD (taxa de distorção) usando VMAF](media/content-aware-encoding/msrv2.png)

**Figura 2: curva de RD (taxa de distorção) usando a métrica VMAF para fonte de alta complexidade**

Abaixo estão os resultados de outra categoria de conteúdo de origem, em que o codificador foi capaz de determinar que a entrada era de baixa qualidade (muitos artefatos de compactação devido à baixa taxa de bits). Observe que, com a predefinição de reconhecimento de conteúdo, o codificador decidiu produzir apenas uma camada de saída – a uma taxa de bits baixa o suficiente para que a maioria dos clientes possa reproduzir o streaming sem interrupções.

![Curva de RD usando PSNR](media/content-aware-encoding/msrv3.png)

**Figura 3: curva de RD usando PSNR para entrada de baixa qualidade (em 1080p)**

![Curva de RD usando VMAF](media/content-aware-encoding/msrv4.png)

**Figura 4: curva de RD usando VMAF para entrada de baixa qualidade (em 1080p)**

## <a name="how-to-use-the-content-aware-encoding-preset"></a>Como usar a predefinição de codificação com reconhecimento de conteúdo 

Você pode criar transformações que usam essa predefinição conforme a seguir. 

Confira a seção [Próximas etapas](#next-steps) para ver tutoriais que usam saídas de transformação. O ativo de saída pode ser entregue por meio de pontos de extremidade de streaming dos Serviços de Mídia em protocolos como MPEG-DASH e HLS (conforme mostrado nos tutoriais).

> [!NOTE]
> Use a predefinição **ContentAwareEncoding** e não ContentAwareEncodingExperimental.

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
> Os trabalhos de codificação usando a predefinição `ContentAwareEncoding` estão sendo cobrados com base nos minutos de saída. 
  
## <a name="next-steps"></a>Próximas etapas

* [Tutorial: carregar, codificar e transmitir vídeos com os Serviços de Mídia v3](stream-files-tutorial-with-api.md)
* [Tutorial: Codificar um arquivo remoto baseado em URL e transmitir o vídeo – REST](stream-files-tutorial-with-rest.md)
* [Tutorial: codificar um arquivo remoto baseado em URL e transmitir o vídeo – CLI](stream-files-cli-quickstart.md)
* [Tutorial: Codificar um arquivo remoto baseado em URL e transmitir o vídeo – .NET](stream-files-dotnet-quickstart.md)
* [Tutorial: Codificar um arquivo remoto baseado em URL e transmitir o vídeo – Node.js](stream-files-nodejs-quickstart.md)
