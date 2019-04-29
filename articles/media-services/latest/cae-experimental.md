---
title: Um experimental predefinição de codificação com suporte a conteúdo - Azure | Microsoft Docs
description: Este artigo aborda a codificação com suporte a conteúdo nos serviços de mídia do Azure
services: media-services
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/05/2019
ms.author: sethm
ms.custom: ''
ms.openlocfilehash: 3c50502a8b873503ee937914fac5f2d92cb23a2b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60733416"
---
# <a name="experimental-preset-for-content-aware-encoding"></a>Experimental predefinição de codificação com suporte a conteúdo

Para preparar o conteúdo para entrega por [streaming de taxa de bits adaptável](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming), vídeo precisa ser codificado com vários-taxas de bits (alto para baixo). Para garantir que a redução gradual de qualidade, como a taxa de bits é reduzida é então a resolução do vídeo. Isso resulta em uma escada de codifica chamada – uma tabela de resoluções e taxas de bits, como você pode ver, como em algumas de nossas predefinições de codificação fixas [H264MultipleBitrate1080p](../previous/media-services-mes-preset-h264-multiple-bitrate-1080p.md).

## <a name="overview"></a>Visão geral

Interesse em ir além de uma abordagem de uma predefinição-é adequada-all-vídeos aumentado após a publicação do Netflix suas [blog](https://medium.com/netflix-techblog/per-title-encode-optimization-7e99442b62a2) em dezembro de 2015. Desde então, foram lançadas várias soluções para a codificação de conteúdo com suporte no marketplace; ver [deste artigo](https://www.streamingmedia.com/Articles/Editorial/Featured-Articles/Buyers-Guide-to-Per-Title-Encoding-130676.aspx) para uma visão geral. A ideia é estar ciente do conteúdo – personalizar ou ajustar a escada de codifica à complexidade do vídeo individual. Cada resolução, há uma taxa de bits, além do qual um aumento na qualidade não é visão – o codificador opera com esse valor de taxa de bits ideal. O próximo nível de otimização é selecionar as resoluções com base no conteúdo – por exemplo, um vídeo de uma apresentação do PowerPoint não se beneficiarão vai abaixo 720p. Indo mais além, o codificador pode ser designado para otimizar as configurações para cada captura no vídeo. Netflix descrito [essa abordagem](https://medium.com/netflix-techblog/optimized-shot-based-encodes-now-streaming-4b9464204830) em 2018.

No início de 2017, a Microsoft lançou o [Streaming adaptável](autogen-bitrate-ladder.md) predefinido para resolver o problema a variabilidade na qualidade e resolução dos vídeos para o código-fonte. Nossos clientes tinham uma mistura de diferentes de conteúdo, algumas em 1080p, outras pessoas em 720p e alguns de cada SD e resoluções mais baixas. Além disso, não todo o conteúdo de origem foi mezzanines de alta qualidade de filme ou estúdios de TV. A Adaptive Streaming endereços predefinidos esses problemas, garantindo que a escada de taxa de bits nunca excede a resolução ou a taxa de bits média de mezanino de entrada.

A predefinição de codificação com suporte a conteúdo experimental estende esse mecanismo, incorporando a lógica personalizada que permite que o codificador buscar o valor de taxa de bits ideal para uma determinada resolução, mas sem a necessidade de uma análise abrangente computacional. O resultado líquido é que essa nova predefinição produz uma saída que tem a taxa de bits inferior que a predefinição transmissão adaptável, mas com uma qualidade mais alta. Ver os seguintes gráficos de exemplo que mostram a comparação usando métricas de qualidade como [PSNR](https://en.wikipedia.org/wiki/Peak_signal-to-noise_ratio) e [VMAF](https://en.wikipedia.org/wiki/Video_Multimethod_Assessment_Fusion). O código-fonte foi criada concatenando clipes curtos de capturas de complexidade alta de filmes e programas de TV, deve enfatizar o codificador. Por definição, resultados essa predefinição produz que variam de acordo de conteúdo para conteúdo – isso também significa que para algum conteúdo, pode não haver uma redução significativa na taxa de bits ou melhoria na qualidade.

![Curva de taxa-distorção (RD) usando PSNR](media/cae-experimental/msrv1.png)

**Figura 1: Curva de taxa-distorção (RD) usando a métrica PSNR para fonte de complexidade alta**

![Curva de taxa-distorção (RD) usando VMAF](media/cae-experimental/msrv2.png)

**Figura 2: Curva de taxa-distorção (RD) usando a métrica VMAF para fonte de complexidade alta**

A predefinição atualmente está ajustada para complexidade alta, vídeos de origem de alta qualidade (filmes, programas de TV). Trabalho está em andamento, adaptar ao conteúdo de complexidade baixa (por exemplo, apresentações do PowerPoint), bem como vídeos de qualidade inferior. Essa predefinição também usa o mesmo conjunto de resoluções da predefinição de Streaming adaptável. Microsoft está trabalhando em métodos para selecionar o conjunto mínimo de resoluções com base no conteúdo. Como a seguir são resultados para outra categoria de conteúdo de origem, em que o codificador foi capaz de determinar que a entrada foi de baixa qualidade (muitos artefatos de compactação devido à baixa taxa de bits). Observe que, com o experimental predefinição, o codificador decidiu produzir uma única camada de saída – uma taxa de bits baixas o suficiente para que a maioria dos clientes seria capaz de executar o fluxo sem atraso das.

![Curva de área de trabalho remota usando PSNR](media/cae-experimental/msrv3.png)

**Figura 3: Curva de área de trabalho remota usando PSNR para entrada de baixa qualidade (em 1080p)**

![Curva de área de trabalho remota usando VMAF](media/cae-experimental/msrv4.png)

**Figura 4: Curva de área de trabalho remota usando VMAF para entrada de baixa qualidade (em 1080p)**

## <a name="use-the-experimental-preset"></a>Usar a predefinição experimental

Você pode criar transformações que usam esta predefinição da seguinte maneira. Se usar um tutorial [como esse](stream-files-tutorial-with-api.md), você pode atualizar o código da seguinte maneira:

```csharp
TransformOutput[] output = new TransformOutput[]
{
   new TransformOutput
   {
      // The preset for the Transform is set to one of Media Services built-in sample presets.
      // You can customize the encoding settings by changing this to use "StandardEncoderPreset" class.
      Preset = new BuiltInStandardEncoderPreset()
      {
         // This sample uses the new experimental preset for content-aware encoding
         PresetName = EncoderNamedPreset.ContentAwareEncodingExperimental
      }
   }
};
```

> [!NOTE]
> O prefixo "experimental" é usado aqui para sinalizar os algoritmos subjacentes ainda estão em evolução. Pode haver e serão as alterações ao longo do tempo à lógica usada para gerar ladders de taxa de bits, com o objetivo de convergir em um algoritmo que é robusto e se adapta a uma ampla variedade de condições de entrada. Codificação trabalhos usando essa predefinição será ainda ser cobrado o valor de saída com base em minutos, e o ativo de saída pode ser fornecido de nossos pontos de extremidade de streaming em protocolos como DASH e HLS.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu sobre essa nova opção de otimizar seus vídeos, convidamos você a experimentá-lo. Você pode enviar seus comentários usando os links no final deste artigo ou entre em contato conosco mais diretamente em <amsved@microsoft.com>.
