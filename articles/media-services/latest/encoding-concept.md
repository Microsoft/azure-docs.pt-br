---
title: Codificação na nuvem com Serviços de Mídia - Azure | Microsoft Docs
description: Este tópico descreve o processo de codificação ao usar os Serviços de Mídia do Azure
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 06/08/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: b0a71e8b3ffff822521a23aafd6764bcce9bd4d4
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67303926"
---
# <a name="encoding-with-media-services"></a>Codificação com os Serviços de Mídia do Microsoft Azure

A codificação de termo nos serviços de mídia aplica-se ao processo de conversão de arquivos que contém o vídeo digital e/ou áudio de um formato padrão para outro, com a finalidade de (a) reduzindo o tamanho dos arquivos de e/ou (b) produzir um formato que seja compatível com um ampla gama de dispositivos e aplicativos. Esse processo também é conhecido como compactação de vídeo ou transcodificação. Consulte a [compactação de dados](https://en.wikipedia.org/wiki/Data_compression) e o [o que é a codificação e transcodificação?](https://www.streamingmedia.com/Articles/Editorial/What-Is-/What-Is-Encoding-and-Transcoding-75025.aspx) para obter mais informações sobre os conceitos.

Vídeos normalmente são entregues aos dispositivos e aplicativos por [download progressivo](https://en.wikipedia.org/wiki/Progressive_download) ou por meio das [streaming de taxa de bits adaptável](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming). 

* Para entregar por download progressivo, você pode usar os serviços de mídia do Azure para converter um arquivo de mídia digital (mezanino) em um [MP4](https://en.wikipedia.org/wiki/MPEG-4_Part_14) arquivo, que contém o vídeo que foi codificado com o [H.264](https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC) codec, e áudio foi codificado com o [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) codec. Esse arquivo MP4 é gravado em um ativo em sua conta de armazenamento. Você pode usar os SDKs ou as APIs de armazenamento do Azure (por exemplo, [API REST de armazenamento](../../storage/common/storage-rest-api-auth.md), [JAVA SDK](../../storage/blobs/storage-quickstart-blobs-java-v10.md), ou [SDK do .NET](../../storage/blobs/storage-quickstart-blobs-dotnet.md)) para baixar o arquivo diretamente. Se você criou a saída do ativo com um nome de contêiner específico no armazenamento, use esse local. Caso contrário, você pode usar os serviços de mídia [listará as URLs de contêiner do ativo](https://docs.microsoft.com/rest/api/media/assets/listcontainersas). 
* Para preparar o conteúdo para entrega por streaming de taxa de bits adaptável, o arquivo de mezanino precisa ser codificado em várias taxas de bits (altas para baixo). Para garantir uma transição gradual de qualidade, como a taxa de bits é reduzida, portanto, é a resolução do vídeo. Isso resulta em uma chamada Escada codifica – uma tabela de resoluções e taxas de bits (consulte [Escada de taxa de bits adaptável autogerado](autogen-bitrate-ladder.md)). Você pode usar os serviços de mídia para codificar seus arquivos de mezanino em várias taxas de bits – ao fazer isso, você obterá um conjunto de arquivos MP4 e streaming configuração arquivos associados, gravados em um ativo em sua conta de armazenamento. Você pode usar o [empacotamento dinâmico](dynamic-packaging-overview.md) capacidade nos serviços de mídia para fornecer o vídeo por meio de protocolos, como de streaming [MPEG-DASH](https://en.wikipedia.org/wiki/Dynamic_Adaptive_Streaming_over_HTTP) e [HLS](https://en.wikipedia.org/wiki/HTTP_Live_Streaming). Isso exige que você crie uma [localizador de Streaming](streaming-locators-concept.md) e criar URLs correspondente para os protocolos com suporte, o que poderá ser entregue aos dispositivos/aplicativos com base em seus recursos de streaming.

O diagrama a seguir mostra o fluxo de trabalho para codificação sob demanda com o empacotamento dinâmico.

![Empacotamento dinâmico](./media/dynamic-packaging-overview/media-services-dynamic-packaging.png)

Este tópico fornece orientação sobre como codificar seu conteúdo com o Serviços de Mídia do Microsoft Azure v3.

## <a name="transforms-and-jobs"></a>Transformações e trabalhos

Para codificar com os Serviços de Mídia v3 é necessário criar uma [Transformação](https://docs.microsoft.com/rest/api/media/transforms) e um [Trabalho](https://docs.microsoft.com/rest/api/media/jobs). A transformação define uma receita para suas configurações de codificação e saídas; o trabalho é uma instância da receita. Para obter mais informações, consulte [Transformações e Trabalhos](transforms-jobs-concept.md)

Ao codificar com o Serviços de Mídia do Microsoft Azure, você usa predefinições para informar ao codificador como os arquivos de mídia de entrada devem ser processados. Por exemplo, é possível especificar a resolução de vídeo e/ou o número de canais de áudio que desejar no conteúdo codificado. 

Você pode iniciar de forma rápida com uma das predefinições internas recomendadas com base nas melhores práticas do setor ou pode optar por criar uma predefinição personalizada para segmentar os requisitos de dispositivo ou cenário específico. Para obter mais informações, consulte [Codificar com uma transformação personalizada](customize-encoder-presets-how-to.md). 

A partir de janeiro de 2019, ao codificar com o Media Encoder Standard para produzir arquivos MP4, um novo arquivo .mpi será gerado e adicionado à saída Ativo. Esse arquivo MPI destina-se a melhorar o desempenho dos cenários de empacotamento e [streaming dinâmicos](dynamic-packaging-overview.md).

> [!NOTE]
> Você não deve modificar nem remover o arquivo MPI, nem usar qualquer dependência em seu serviço na existência (ou não) desse arquivo.

### <a name="creating-job-input-from-an-https-url"></a>Criando a entrada de trabalho de uma URL HTTPS

Quando você enviar trabalhos para processar seus vídeos, você precisa informar ao Media Services onde encontrar o vídeo de entrada. Uma das opções é especificar uma URL HTTPS como um trabalho de entrada. Atualmente, os serviços de mídia v3 não dá suporte a codificação nos URLs HTTPS de transferência em partes. 

#### <a name="examples"></a>Exemplos

* [Codificar a partir de uma URL HTTPS com o .NET](stream-files-dotnet-quickstart.md)
* [Codificar a partir de uma URL HTTPS com REST](stream-files-tutorial-with-rest.md)
* [Codificar a partir de uma URL HTTPS com a CLI](stream-files-cli-quickstart.md)
* [Codificar a partir de uma URL HTTPS com Node. js](stream-files-nodejs-quickstart.md)

### <a name="creating-job-input-from-a-local-file"></a>Criar entrada de trabalho de um arquivo local

O vídeo de entrada pode ser armazenado como um Ativo de Serviço de Mídia. Nesse caso, você cria um ativo de entrada com base em um arquivo (armazenado localmente ou no Armazenamento de Blobs do Azure). 

#### <a name="examples"></a>Exemplos

[Codificar um arquivo local usando os valores predefinidos internos](job-input-from-local-file-how-to.md)

### <a name="creating-job-input-with-subclipping"></a>Criar entrada de trabalho com subclipping

Ao codificar um vídeo, você pode especificar também cortar ou recortar o arquivo de origem e produzem uma saída que tem apenas uma parte desejada do vídeo de entrada. Essa funcionalidade funciona com qualquer [transformar](https://docs.microsoft.com/rest/api/media/transforms) que é criado usando o [BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) predefinições, ou o [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) predefinições. 

Você pode especificar para criar uma [trabalho](https://docs.microsoft.com/rest/api/media/jobs/create) com um único clipe de um vídeo ao vivo ou sob demanda arquivamento (um evento gravado). A entrada do trabalho pode ser um ativo ou uma URL HTTPS.

> [!TIP]
> Se você deseja transmitir um sublip do vídeo sem reencoding o vídeo, considere o uso [pré-filtragem manifestos com o Dynamic Packager](filters-dynamic-manifest-overview.md).

#### <a name="examples"></a>Exemplos

Consulte exemplos:

* [Subclipe um vídeo com .NET](subclip-video-dotnet-howto.md)
* [Um vídeo com o restante do subclipe](subclip-video-rest-howto.md)

## <a name="built-in-presets"></a>Predefinições internas

Os Serviços de Mídia atualmente dão suporte às predefinições de codificação internas a seguir:  

### <a name="builtinstandardencoderpreset"></a>BuiltInStandardEncoderPreset

O [BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) é usado para definir uma predefinição interna para codificar o vídeo de entrada com o Codificador Standard. 

As predefinições a seguir são atualmente têm suporte:

- **EncoderNamedPreset.AACGoodQualityAudio** - produz um único arquivo MP4 contendo apenas áudio estéreo codificado a 192 kbps.
- **EncoderNamedPreset.AdaptiveStreaming** (recomendado). Para obter mais informações, consulte [geração automática de uma escada de taxa de bits ](autogen-bitrate-ladder.md).
- **EncoderNamedPreset.ContentAwareEncodingExperimental** -expõe uma predefinição experimental para codificação com suporte a conteúdo. Dado qualquer conteúdo de entrada, o serviço tenta determinar automaticamente o número ideal de camadas, taxa de bits apropriada e as configurações de resolução para entrega por streaming adaptável. Os algoritmos subjacentes continuará a evoluir ao longo do tempo. A saída conterá arquivos MP4 com vídeo e áudio intercalados. Para obter mais informações, consulte [Experimental predefinição de codificação com suporte a conteúdo](cae-experimental.md).
- **EncoderNamedPreset.H264MultipleBitrate1080p** - produz um conjunto de 8 arquivos MP4 alinhados a GOP, variando de 6000 kbps a 400 kbps e áudio AAC estéreo. A resolução inicia em 1080p e diminui para 360p.
- **EncoderNamedPreset.H264MultipleBitrate720p** - produz um conjunto de 6 arquivos MP4 alinhados a GOP, variando de 3400 kbps a 400 kbps e áudio AAC estéreo. A resolução inicia em 720p e diminui para 360p.
- **EncoderNamedPreset.H264MultipleBitrateSD** - produz um conjunto de 5 arquivos MP4 alinhados a GOP, variando de 1600kbps a 400 kbps e áudio AAC estéreo. A resolução inicia em 480p e diminui para 360p.
- **EncoderNamedPreset.H264SingleBitrate1080p** -produz um arquivo MP4 em que o vídeo é codificado com o codec de h. 264 6750 kbps e uma altura de 1080 pixels da imagem e áudio estéreo codificado com o codec AAC-LC a 64 kbps.
- **EncoderNamedPreset.H264SingleBitrate720p** -produz um arquivo MP4 em que o vídeo é codificado com o codec de h. 264 4500 kbps e uma altura de imagem de 720 pixels e o áudio estéreo codificado com o codec AAC-LC a 64 kbps.
- **EncoderNamedPreset.H264SingleBitrateSD** -produz um arquivo MP4 em que o vídeo é codificado com o codec de h. 264 2200 kbps e uma altura de 480 pixels da imagem e áudio estéreo codificado com o codec AAC-LC a 64 kbps.

Para ver a lista mais atualizada de predefinições, consulte [predefinições para ser usado para codificar vídeos](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset).

Para ver como os valores predefinidos são usados, fazer check-out [carregando, codificação e streaming de arquivos](stream-files-tutorial-with-api.md).

### <a name="standardencoderpreset"></a>StandardEncoderPreset

O [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) descreve as configurações a serem usadas ao codificar o vídeo de entrada com o Codificador Standard. Use essa predefinição ao personalizar as predefinições de Transformação. 

#### <a name="considerations"></a>Considerações

Ao criar predefinições personalizadas, as seguintes considerações se aplicam:

- Todos os valores de altura e largura no conteúdo de AVC devem ser um múltiplo de 4.
- Serviços de mídia do Azure v3, todas as taxas de bits de codifica são em bits por segundo. Isso é diferente das predefinições com nossas APIs v2, que usado quilobits por segundo, como a unidade. Por exemplo, se a taxa de bits na versão 2 foi especificada como 128 (quilobits/segundo), na v3 ele deve ser definido como 128000 (bits/segundo).

### <a name="customizing-presets"></a>Personalizando as predefinições

Os Serviços de Mídia oferece suporte completo para a personalização de todos os valores em predefinições, a fim de atender às suas necessidades e requisitos de codificação. Para obter exemplos que mostram como personalizar as predefinições do codificador, consulte:

#### <a name="examples"></a>Exemplos

- [Personalizar as predefinições com .NET](customize-encoder-presets-how-to.md)
- [Personalizar as predefinições com a CLI](custom-preset-cli-howto.md)
- [Personalizar as predefinições com REST](custom-preset-rest-howto.md)

## <a name="preset-schema"></a>Predefinição do esquema

Em serviços de mídia v3 predefinições são entidades com rigidez de tipos na própria API. Você pode encontrar a definição de "esquema" para esses objetos no [especificação de API aberta (ou Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01). Você também pode exibir as definições predefinidas (como **StandardEncoderPreset**) na [API REST](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset), [SDK do .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.standardencoderpreset?view=azure-dotnet) (ou outra documentação de referência SDK do Media Services v3).

## <a name="scaling-encoding-in-v3"></a>Codificação de escala na v3

Para dimensionar o processamento de mídia, consulte [escala com CLI](media-reserved-units-cli-how-to.md).

## <a name="ask-questions-give-feedback-get-updates"></a>Fazer perguntas, comentar, obter atualizações

Confira o artigo [comunidade dos Serviços de Mídia do Azure](media-services-community.md) para ver diferentes maneiras de fazer perguntas, comentários e obter atualizações sobre os serviços de mídia.

## <a name="next-steps"></a>Próximas etapas

* [Carregar, codificar e transmitir usando os Serviços de Mídia do Microsoft Azure](stream-files-tutorial-with-api.md)
* [Codificar a partir de uma URL HTTPS usando predefinições](job-input-from-http-how-to.md)
* [Codificar um arquivo local usando os valores predefinidos internos](job-input-from-local-file-how-to.md)
* [Criar uma predefinição para seus requisitos específicos de cenário ou dispositivo de destino personalizada](customize-encoder-presets-how-to.md)