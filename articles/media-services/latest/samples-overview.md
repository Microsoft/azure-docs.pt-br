---
title: Exemplos de Serviços de Mídia v3
description: Este artigo contém uma lista de todos os exemplos disponíveis para os Serviços de Mídia v3 organizados por método e SDK.  Os exemplos incluem .NET, Node.JS, Python e Java, bem como REST com o Postman.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: overview
ms.date: 03/24/2021
ms.author: inhenkel
ms.openlocfilehash: 1d827d734c434204ff6b7ec60d27e507ae626abd
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107227680"
---
# <a name="media-services-v3-samples"></a>Exemplos de Serviços de Mídia v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Este artigo contém uma lista de todos os exemplos disponíveis para os Serviços de Mídia organizados por método e SDK.  Os exemplos incluem .NET, Node.JS, Python e Java, bem como REST com o Postman.

## <a name="rest-postman-collection"></a>Coleção do Postman para REST

Os exemplos do [Postman para REST](https://github.com/Azure-Samples/media-services-v3-rest-postman) incluem um ambiente e uma coleção do Postman para que você importe para o cliente do Postman.

## <a name="samples-by-sdk"></a>Exemplos por SDK

Você encontrará as descrições e os links para os exemplos que pode estar procurando em cada uma das guias.

## <a name="net"></a>[.NET](#tab/net/)

| Pasta | Descrição |
|-------------|-------------|
| [VideoEncoding/EncodingWithMESPredefinedPreset](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/EncodingWithMESPredefinedPreset)|Como enviar um trabalho usando uma predefinição interna e uma entrada de URL HTTP, publicar um ativo de saída para streaming e baixar os resultados para verificação.|
| [VideoEncoding/EncodingWithMESCustomPreset_H264](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/EncodingWithMESCustomPreset_H264)|Como enviar um trabalho usando uma predefinição de codificação H.264 e uma entrada de URL HTTP, publicar um ativo de saída para streaming e baixar os resultados para verificação.|
| [VideoEncoding/EncodingWithMESCustomPreset_HEVC](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/EncodingWithMESCustomPreset_HEVC)|Como enviar um trabalho usando uma predefinição de codificação HEVC e uma entrada de URL HTTP, publicar um ativo de saída para streaming e baixar os resultados para verificação.|
| [VideoEncoding/EncodingWithMESCustomStitchTwoAssets](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/EncodingWithMESCustomStitchTwoAssets)|Como enviar um trabalho usando a JobInputSequence para costurar dois ou mais ativos que podem ser recortados pela hora de início ou de término. O arquivo codificado resultante é um vídeo com todos os ativos costurados.  O exemplo também publicará o ativo de saída para streaming e baixará os resultados para verificação.|
| [VideoEncoding/EncodingWithMESCustomPresetAndSprite](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/EncodingWithMESCustomPresetAndSprite)|Como enviar um trabalho usando uma predefinição personalizada com um sprite de miniatura e uma entrada de URL HTTP, publicar um ativo de saída para streaming e baixar os resultados para verificação.|
| [Live/LiveEventWithDVR](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Live/LiveEventWithDVR)|Como criar um LiveEvent com um arquivo completo de até 25 horas e um filtro no ativo com uma janela de DVR de 5 minutos. Como usar um filtro para criar um localizador para streaming.|
| [VideoAnalytics/VideoAnalyzer](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoAnalytics/VideoAnalyzer)|Como criar uma transformação do analisador de vídeo, carregar um arquivo de vídeo em um ativo de entrada, enviar um trabalho com a transformação e baixar os resultados para verificação.|
| [AudioAnalytics/AudioAnalyzer](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/AudioAnalytics/AudioAnalyzer)|Como criar uma transformação do analisador de áudio, carregar um arquivo de mídia em um ativo de entrada, enviar um trabalho com a transformação e baixar os resultados para verificação.|
| [ContentProtection/BasicAESClearKey](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/BasicAESClearKey)|Como criar uma transformação com a predefinição AdaptiveStreaming interna, enviar um trabalho, criar uma ContentKeyPolicy usando uma chave secreta, associar a ContentKeyPolicy ao StreamingLocator, obter um token e imprimir uma URL para reprodução no Player de Mídia do Azure. Quando um fluxo é solicitado por um player, os Serviços de Mídia usam a chave especificada para criptografar dinamicamente o conteúdo com o AES-128 e o Player de Mídia do Azure usa o token para descriptografar.|
| [ContentProtection/BasicWidevine](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/BasicWidevine)|Como criar uma transformação com a predefinição AdaptiveStreaming interna, enviar um trabalho, criar uma ContentKeyPolicy com a configuração do Widevine usando uma chave secreta, associar a ContentKeyPolicy ao StreamingLocator, obter um token e imprimir uma URL para reprodução em um Player Widevine. Quando um usuário solicita o conteúdo protegido pelo Widevine, o player de mídia solicita uma licença do serviço de licença dos Serviços de Mídia. Se o player de mídia estiver autorizado, o serviço de licença dos Serviços de Mídia emitirá uma licença para o player. Uma licença do Widevine contém a chave de descriptografia que pode ser usada pelo player cliente para descriptografar e transmitir o conteúdo.|
| [ContentProtection/BasicPlayReady](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/BasicPlayReady)|Como criar uma transformação com a predefinição AdaptiveStreaming interna, enviar um trabalho, criar uma ContentKeyPolicy com a configuração do PlayReady usando uma chave secreta, associar a ContentKeyPolicy ao StreamingLocator, obter um token e imprimir uma URL para reprodução em um Player de Mídia do Azure. Quando um usuário solicita o conteúdo protegido pelo PlayReady, o player de mídia solicita uma licença do serviço de licença dos Serviços de Mídia. Se o player de mídia estiver autorizado, o serviço de licença dos Serviços de Mídia emitirá uma licença para o player. Uma licença do PlayReady contém a chave de descriptografia que pode ser usada pelo player do cliente para descriptografar e transmitir o conteúdo.|
| [ContentProtection/OfflinePlayReadyAndWidevine](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/OfflinePlayReadyAndWidevine)|Como criptografar dinamicamente seu conteúdo com o DRM do PlayReady e do Widevine e reproduzir o conteúdo sem solicitar uma licença do serviço de licença. Ela mostra como criar uma transformação com a predefinição AdaptiveStreaming interna, enviar um trabalho, criar uma ContentKeyPolicy com a restrição aberta e a configuração persistente do PlayReady/Widevine, associar a ContentKeyPolicy ao StreamingLocator e imprimir uma URL para reprodução.|
| [Streaming/AssetFilters](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Streaming/AssetFilters)|Como criar uma transformação com a predefinição AdaptiveStreaming interna, enviar um trabalho, criar um filtro de ativos e um filtro de contas, associar os filtros aos localizadores de streaming e imprimir URLs para reprodução.|
| [Streaming/StreamHLSAndDASH](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Streaming/StreamHLSAndDASH)|Como criar uma transformação com a predefinição AdaptiveStreaming interna, enviar um trabalho, publicar o ativo de saída para streaming de HLS e DASH.|
| [HighAvailabilityEncodingStreaming](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/HighAvailabilityEncodingStreaming/) | Diretrizes e melhores práticas para um sistema de produção usando codificação ou análise sob demanda. Os leitores devem começar com o artigo complementar [Alta disponibilidade com os Serviços de Mídia e o VOD](https://docs.microsoft.com/azure/media-services/latest/architecture-high-availability-encoding-concept). Um arquivo de solução separado é fornecido para o exemplo [HighAvailabilityEncodingStreaming](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/HighAvailabilityEncodingStreaming/README.md). |

## <a name="nodejs"></a>[Node.JS](#tab/node/)

|Pasta|Descrição|
|---|---|
|[HelloWorld-ListAssets](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/HelloWorld-ListAssets) |Como conectar e listar ativos |
|[Ao vivo](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/Live)| Como fazer uma transmissão ao vivo básica. **AVISO**, não deixe de verificar se todos os recursos foram limpos e não estão sendo cobrados no portal ao usar o recurso ao vivo.|
|[StreamFilesSample](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/StreamFilesSample)| Como carregar um arquivo local ou uma codificação de uma URL de origem, como usar o SDK de armazenamento para baixar conteúdo e como transmitir para um player. |
|[StreamFilesWithDRMSample](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/StreamFilesWithDRMSample)| Como codificar e transmitir usando o DRM do Widevine e do PlayReady |
|[VideoIndexerSample](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/VideoIndexerSample)| Como usar as predefinições de Vídeo e do Analisador de Áudio para gerar metadados e insights de um arquivo de vídeo ou áudio. |

## <a name="python"></a>[Python](#tab/python)

Atualmente, há um exemplo de Python, [Codificação básica com Python](https://github.com/Azure-Samples/media-services-v3-python).

## <a name="java"></a>[Java](#tab/java)

|Pasta|Descrição|
|---|---|
|[AudioAnalytics/AudioAnalyzer/](https://github.com/Azure-Samples/media-services-v3-java/tree/master/AudioAnalytics/AudioAnalyzer)|Como analisar o áudio em um arquivo de mídia. |
|Proteção de Conteúdo|
|ContentProtection||
|[BasicAESClearKey](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/BasicAESClearKey)|Como criptografar dinamicamente seu conteúdo com o AES-128.|
|[BasicPlayReady](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/BasicPlayReady)|Como criptografar dinamicamente seu conteúdo com o DRM do PlayReady.|
|[BasicWidevine](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/BasicWidevine)|Como criptografar dinamicamente seu conteúdo com o DRM do Widevine.|
|[OfflineFairPlay](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/OfflineFairPlay)|Como criptografar dinamicamente seu conteúdo com o DRM do FairPlay e reproduzir o conteúdo sem solicitar uma licença do serviço de licença.|
|[OfflinePlayReadyAndWidevine](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/OfflinePlayReadyAndWidevine)|Como criptografar dinamicamente seu conteúdo com o DRM do PlayReady e do Widevine e reproduzir o conteúdo sem solicitar uma licença do serviço de licença.|
|DynamicPackagingVODContent||
|[AssetFilters](https://github.com/Azure-Samples/media-services-v3-java/tree/master/DynamicPackagingVODContent/AssetFilters)|Como filtrar conteúdo usando filtros de conta e de ativo.|
|[StreamHLSAndDASH](https://github.com/Azure-Samples/media-services-v3-java/tree/master/DynamicPackagingVODContent/StreamHLSAndDASH)|Como empacotar dinamicamente conteúdo de VOD em HLS/DASH para streaming.|
|[LiveIngest/LiveEventWithDVR](https://github.com/Azure-Samples/media-services-v3-java/tree/master/LiveIngest/LiveEventWithDVR)|Como criar um Evento ao Vivo com um arquivo completo de até 25 horas e um filtro no ativo com uma janela de DVR de 5 minutos e como criar um localizador para streaming e usar o filtro.|
|[VideoAnalytics/VideoAnalyzer](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoAnalytics/VideoAnalyzer)|Como criar um Evento ao Vivo com um arquivo completo de até 25 horas e um filtro no ativo com uma janela de DVR de 5 minutos e como criar um localizador para streaming e usar o filtro.|
|VideoEncoding||
|[EncodingWithMESCustomPreset](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoEncoding/EncodingWithMESCustomPreset)|Como criar uma Transformação de codificação personalizada usando as configurações de StandardEncoderPreset.|
|[EncodingWithMESPredefinedPreset](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoEncoding/EncodingWithMESPredefinedPreset)|Como enviar um trabalho usando uma predefinição interna e uma entrada de URL HTTP, publicar um ativo de saída para streaming e baixar os resultados para verificação.|

---
