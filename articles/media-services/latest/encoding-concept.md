---
title: Codificando vídeo e áudio com os serviços de mídia
description: Este artigo explica sobre a codificação de vídeo e áudio com os serviços de mídia do Azure.
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
ms.custom: seodec18
ms.openlocfilehash: 6a486057a265b02ce30059940c8c98837ec43f8e
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102617634"
---
# <a name="encoding-video-and-audio-with-media-services"></a>Codificando vídeo e áudio com os serviços de mídia

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

O termo codificação nos serviços de mídia se aplica ao processo de conversão de arquivos que contêm vídeo digital e/ou áudio de um formato padrão para outro, com a finalidade de (a) reduzir o tamanho dos arquivos e/ou (b) produzir um formato compatível com uma ampla variedade de dispositivos e aplicativos. Esse processo também é conhecido como compactação de vídeo ou transcodificação. Consulte a [compactação de dados](https://en.wikipedia.org/wiki/Data_compression) e o [que é codificação e transcodificação?](https://www.streamingmedia.com/Articles/Editorial/What-Is-/What-Is-Encoding-and-Transcoding-75025.aspx) para obter mais informações sobre os conceitos.

Os vídeos normalmente são entregues a dispositivos e aplicativos por [download progressivo](https://en.wikipedia.org/wiki/Progressive_download) ou por meio de [streaming de taxa de bits adaptável](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming).

> [!IMPORTANT]
> Os serviços de mídia não cobram por trabalhos cancelados ou com erro. Por exemplo, um trabalho que atingiu 50% de progresso e é cancelado não é cobrado em 50% dos minutos de trabalho. Você é cobrado apenas pelos trabalhos acabados.

* Para entregar por download progressivo, você pode usar os serviços de mídia do Azure para converter um arquivo de mídia digital (mezanino) em um arquivo [MP4](https://en.wikipedia.org/wiki/MPEG-4_Part_14) , que contém vídeo que foi codificado com o codec [H. 264](https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC) e áudio que foi codificado com o codec [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) . Esse arquivo MP4 é gravado em um ativo em sua conta de armazenamento. Você pode usar as APIs de armazenamento do Azure ou SDKs (por exemplo, [API REST de armazenamento](../../storage/common/storage-rest-api-auth.md) ou [SDK do .net](../../storage/blobs/storage-quickstart-blobs-dotnet.md)) para baixar o arquivo diretamente. Se você criou o ativo de saída com um nome de contêiner específico no armazenamento, use esse local. Caso contrário, você pode usar os serviços de mídia para [listar as URLs do contêiner de ativos](/rest/api/media/assets/listcontainersas). 
* Para preparar o conteúdo para entrega por streaming de taxa de bits adaptável, o arquivo de mezanino precisa ser codificado em várias taxas de bits (alta para baixa). Para garantir a transição normal de qualidade, a resolução do vídeo é reduzida à medida que a taxa de bits é reduzida. Isso resulta em uma chamada de escada de codificação – uma tabela de resoluções e taxas de bits (consulte a [escada de taxa de bits adaptável gerada automaticamente](autogen-bitrate-ladder.md)). Você pode usar os serviços de mídia para codificar seus arquivos de mezanino em várias taxas de bits. Ao fazer isso, você obterá um conjunto de arquivos MP4 e arquivos de configuração de streaming associados gravados em um ativo em sua conta de armazenamento. Em seguida, você pode usar o recurso de [empacotamento dinâmico](dynamic-packaging-overview.md) nos serviços de mídia para entregar o vídeo por meio de protocolos de streaming como [MPEG-Dash](https://en.wikipedia.org/wiki/Dynamic_Adaptive_Streaming_over_HTTP) e [HLS](https://en.wikipedia.org/wiki/HTTP_Live_Streaming). Isso exige que você crie um [localizador de streaming](streaming-locators-concept.md) e crie URLs de streaming correspondentes aos protocolos com suporte, que podem ser entregues a dispositivos/aplicativos com base em seus recursos.

O diagrama a seguir mostra o fluxo de trabalho para a codificação sob demanda com o empacotamento dinâmico.

![Fluxo de trabalho para codificação sob demanda com empacotamento dinâmico](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

Este tópico fornece orientação sobre como codificar seu conteúdo com o Serviços de Mídia do Microsoft Azure v3.

## <a name="transforms-and-jobs"></a>Transformações e trabalhos

Para codificar com os Serviços de Mídia v3 é necessário criar uma [Transformação](/rest/api/media/transforms) e um [Trabalho](/rest/api/media/jobs). A transformação define uma receita para suas configurações e saídas de codificação; o trabalho é uma instância da receita. Para obter mais informações, consulte [Transformações e Trabalhos](transforms-jobs-concept.md).

Ao codificar com o Serviços de Mídia do Microsoft Azure, você usa predefinições para informar ao codificador como os arquivos de mídia de entrada devem ser processados. No Media Services V3, você usa o codificador padrão para codificar seus arquivos. Por exemplo, é possível especificar a resolução de vídeo e/ou o número de canais de áudio que desejar no conteúdo codificado.

Você pode iniciar de forma rápida com uma das predefinições internas recomendadas com base nas melhores práticas do setor ou pode optar por criar uma predefinição personalizada para segmentar os requisitos de dispositivo ou cenário específico. Para obter mais informações, consulte [Codificar com uma transformação personalizada](customize-encoder-presets-how-to.md).

A partir de janeiro de 2019, ao codificar com o codificador padrão para produzir arquivos MP4, um novo arquivo. MPI é gerado e adicionado ao ativo de saída. Esse arquivo MPI destina-se a melhorar o desempenho dos cenários de empacotamento e [streaming dinâmicos](dynamic-packaging-overview.md).

> [!NOTE]
> Você não deve modificar ou remover o arquivo MPI ou assumir qualquer dependência em seu serviço na existência (ou não) desse arquivo.

### <a name="creating-job-input-from-an-https-url"></a>Criando entrada de trabalho de uma URL HTTPS

Ao enviar trabalhos para processar seus vídeos, você precisa informar aos serviços de mídia onde encontrar o vídeo de entrada. Uma das opções é especificar uma URL HTTPS como uma entrada de trabalho. Atualmente, os serviços de mídia v3 não dão suporte à codificação de transferência em partes sobre URLs HTTPS.

#### <a name="examples"></a>Exemplos

* [Codificar de uma URL HTTPS com .NET](stream-files-dotnet-quickstart.md)
* [Codificar de uma URL HTTPS com REST](stream-files-tutorial-with-rest.md)
* [Codificar de uma URL HTTPS com CLI](stream-files-cli-quickstart.md)
* [Codificar de uma URL HTTPS com Node.js](stream-files-nodejs-quickstart.md)

### <a name="creating-job-input-from-a-local-file"></a>Criando entrada de trabalho de um arquivo local

O vídeo de entrada pode ser armazenado como um Ativo de Serviço de Mídia. Nesse caso, você cria um ativo de entrada com base em um arquivo (armazenado localmente ou no Armazenamento de Blobs do Azure).

#### <a name="examples"></a>Exemplos

[Codificar um arquivo local usando predefinições internas](job-input-from-local-file-how-to.md)

### <a name="creating-job-input-with-subclipping"></a>Criando entrada de trabalho com subcorte

Ao codificar um vídeo, você pode especificar também aparar ou recortar o arquivo de origem e produzir uma saída que tenha apenas uma parte desejada do vídeo de entrada. Essa funcionalidade funciona com qualquer [transformação](/rest/api/media/transforms) criada usando as predefinições [BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) ou as predefinições de [StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset) .

Você pode especificar para criar um [trabalho](/rest/api/media/jobs/create) com um único clipe de um arquivo de vídeo sob demanda ou dinâmico (um evento gravado). A entrada do trabalho pode ser um ativo ou uma URL HTTPS.

> [!TIP]
> Se você quiser transmitir uma sublip de seu vídeo sem recodificar o vídeo, considere o uso de [manifestos de filtragem prévia com o Dynamic Packager](filters-dynamic-manifest-overview.md).

#### <a name="examples"></a>Exemplos

Veja exemplos:

* [Sub-recortar um vídeo com .NET](subclip-video-dotnet-howto.md)
* [Sub-recortar um vídeo com REST](subclip-video-rest-howto.md)

## <a name="built-in-presets"></a>Predefinições internas

Os serviços de mídia oferecem suporte às seguintes predefinições de codificação internas:  

### <a name="builtinstandardencoderpreset"></a>BuiltInStandardEncoderPreset

O [BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) é usado para definir uma predefinição interna para codificar o vídeo de entrada com o Codificador Standard.

Atualmente, há suporte para as seguintes predefinições internas:

- **EncoderNamedPreset. AACGoodQualityAudio**: produz um único arquivo MP4 contendo apenas áudio estéreo codificado a 192 kbps.
- **EncoderNamedPreset. AdaptiveStreaming** (recomendado): isso oferece suporte à codificação de taxa de bits adaptável H. 264. Para obter mais informações, consulte [geração automática de uma escada de taxa de bits ](autogen-bitrate-ladder.md).
- **EncoderNamerPreset. H265AdaptiveStreaming** : semelhante à predefinição AdaptiveStreaming, mas usa o codec HEVC (H. 265). Produz um conjunto de arquivos MP4 alinhados a GOP com o vídeo H. 265 e áudio AAC estéreo. Gera automaticamente uma escada de taxa de bits com base na resolução de entrada, taxa de bits e taxas de quadros. A predefinição gerada automaticamente nunca excederá a resolução de entrada. Por exemplo, se a entrada for 720p, a saída permanecerá 720p na melhor das hipóteses.
- **EncoderNamedPreset. ContentAwareEncoding**: expõe uma predefinição para a codificação H. 264 com reconhecimento de conteúdo. Dado qualquer conteúdo de entrada, o serviço tenta determinar automaticamente o número ideal de camadas e as configurações apropriadas de taxa de bits e resolução para entrega por streaming adaptável. Os algoritmos subjacentes continuarão a evoluir ao longo do tempo. A saída conterá arquivos MP4 com vídeo e áudio intercalados. Para obter mais informações, consulte [codificação com reconhecimento de conteúdo](content-aware-encoding.md).
- **EncoderNamedPreset. H265ContentAwareEncoding**: expõe uma predefinição para a codificação de reconhecimento de conteúdo HEVC (H. 265). Produz um conjunto de MP4s alinhado a GOP usando a codificação com reconhecimento de conteúdo. Dado qualquer conteúdo de entrada, o serviço executa uma análise leve inicial do conteúdo de entrada e usa os resultados para determinar o número ideal de camadas, taxa de bits apropriada e configurações de resolução para entrega por streaming adaptável. Essa predefinição é particularmente eficaz para vídeos de complexidade baixa e média, em que os arquivos de saída estarão em taxas de bits menores, mas com uma qualidade que ainda oferece uma boa experiência aos visualizadores. A saída conterá arquivos MP4 com vídeo e áudio intercalados.
  > [!NOTE]
  > Certifique-se de usar **ContentAwareEncoding** não ContentAwareEncodingExperimental que agora está preterido

- **EncoderNamedPreset. H264MultipleBitrate1080p**: produz um conjunto de oito arquivos MP4 alinhados a GOP, variando de 6000 kbps a 400 Kbps e áudio AAC estéreo. A resolução inicia em 1080p e diminui para 360p.
- **EncoderNamedPreset. H264MultipleBitrate720p**: produz um conjunto de seis arquivos MP4 alinhados a GOP, variando de 3400 kbps a 400 Kbps e áudio AAC estéreo. A resolução inicia em 720p e diminui para 360p.
- **EncoderNamedPreset. H264MultipleBitrateSD**: produz um conjunto de cinco arquivos MP4 alinhados a GOP, variando de 1600 kbps a 400 Kbps e áudio AAC estéreo. A resolução inicia em 480p e diminui para 360p.
- **EncoderNamedPreset. H264SingleBitrate1080p**: produz um arquivo MP4 em que o vídeo é codificado com o codec H. 264 a 6750 kbps e uma altura de imagem de 1080 pixels, e o áudio estéreo é codificado com o codec AAC-LC em 64 Kbps.
- **EncoderNamedPreset. H264SingleBitrate720p**: produz um arquivo MP4 em que o vídeo é codificado com o codec H. 264 a 4500 kbps e uma altura de imagem de 720 pixels, e o áudio estéreo é codificado com o codec AAC-LC em 64 Kbps.
- **EncoderNamedPreset. H264SingleBitrateSD**: produz um arquivo MP4 em que o vídeo é codificado com o codec H. 264 a 2200 kbps e uma altura de imagem de 480 pixels, e o áudio estéreo é codificado com o codec AAC-LC em 64 Kbps.
- **EncoderNamedPreset. H265SingleBitrate720P**: produz um arquivo MP4 em que o vídeo é codificado com o codec HEVC (H. 265) a 1800 kbps e uma altura de imagem de 720 pixels, e o áudio estéreo é codificado com o codec AAC-LC em 128 kbps.
- **EncoderNamedPreset. H265SingleBitrate1080p**: produz um arquivo MP4 em que o vídeo é codificado com o codec HEVC (H. 265) a 3500 kbps e uma altura de imagem de 1080 pixels, e o áudio estéreo é codificado com o codec AAC-LC em 128 kbps.
- **EncoderNamedPreset. H265SingleBitrate4K**: produz um arquivo MP4 em que o vídeo é codificado com o codec HEVC (H. 265) a 9500 kbps e uma altura de imagem de 2160 pixels, e o áudio estéreo é codificado com o codec AAC-LC em 128 kbps.

Para ver a lista de predefinições mais atualizada, consulte [predefinições internas a serem usadas para codificar vídeos](/rest/api/media/transforms/createorupdate#encodernamedpreset).

Para ver como as predefinições são usadas, consulte [carregando, codificando e transmitindo arquivos](stream-files-tutorial-with-api.md).

### <a name="standardencoderpreset"></a>StandardEncoderPreset

O [StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset) descreve as configurações a serem usadas ao codificar o vídeo de entrada com o Codificador Standard. Use essa predefinição ao personalizar as predefinições de Transformação.

#### <a name="considerations"></a>Considerações

Ao criar predefinições personalizadas, as seguintes considerações se aplicam:

- Todos os valores para altura e largura em conteúdo AVC devem ser um múltiplo de quatro.
- Nos serviços de mídia do Azure v3, todas as taxas de bits de codificação estão em bits por segundo. Isso é diferente das predefinições com nossas APIs v2, que usavam kilobits/segundo como a unidade. Por exemplo, se a taxa de bits em v2 tiver sido especificada como 128 (kilobits/segundo), em v3, ela seria definida como 128000 (bits/segundo).

### <a name="customizing-presets"></a>Personalizando predefinições

Os Serviços de Mídia oferece suporte completo para a personalização de todos os valores em predefinições, a fim de atender às suas necessidades e requisitos de codificação. Para obter exemplos que mostram como personalizar as predefinições do codificador, consulte a lista abaixo:

#### <a name="examples"></a>Exemplos

- [Personalizar as predefinições com o .NET](customize-encoder-presets-how-to.md)
- [Personalizar predefinições com a CLI](custom-preset-cli-howto.md)
- [Personalizar predefinições com REST](custom-preset-rest-howto.md)


## <a name="preset-schema"></a>Esquema predefinido

Nos serviços de mídia v3, as predefinições são entidades fortemente tipadas na própria API. Você pode encontrar a definição de "esquema" para esses objetos em [especificação de API aberta (ou Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01). Você também pode exibir as definições predefinidas (como **StandardEncoderPreset**) na [API REST](/rest/api/media/transforms/createorupdate#standardencoderpreset), no [SDK do .net](/dotnet/api/microsoft.azure.management.media.models.standardencoderpreset) (ou em outra documentação de referência do SDK dos serviços de mídia v3).

## <a name="scaling-encoding-in-v3"></a>Codificação de escala na v3

Para dimensionar o processamento de mídia, consulte [dimensionar com a CLI](media-reserved-units-cli-how-to.md).
Para contas criadas no com a versão **2020-05-01** da API ou por meio do portal do Azure, as unidades reservadas de dimensionamento e mídia não são mais necessárias. O dimensionamento será automático e manipulado pelo serviço internamente.

## <a name="billing"></a>Cobrança

Os serviços de mídia não cobram por trabalhos cancelados ou com erro. Por exemplo, um trabalho que atingiu 50% de progresso e é cancelado não é cobrado em 50% dos minutos de trabalho. Você é cobrado apenas pelos trabalhos acabados.

Para obter mais informações, consulte [preços](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="ask-questions-give-feedback-get-updates"></a>Fazer perguntas, comentar, obter atualizações

Confira o artigo [comunidade dos Serviços de Mídia do Azure](media-services-community.md) para ver diferentes maneiras de fazer perguntas, comentários e obter atualizações sobre os serviços de mídia.

## <a name="next-steps"></a>Próximas etapas

* [Carregue, codifique e transmita usando os serviços de mídia](stream-files-tutorial-with-api.md).
* [Codifique a partir de uma URL https usando predefinições internas](job-input-from-http-how-to.md).
* [Codifique um arquivo local usando predefinições internas](job-input-from-local-file-how-to.md).
* [Criar uma predefinição personalizada para segmentar um cenário específico ou requisitos de dispositivo](customize-encoder-presets-how-to.md).
