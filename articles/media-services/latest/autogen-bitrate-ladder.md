---
title: Codificar vídeos com o Codificador Padrão nos Serviços de Mídia - Azure | Microsoft Docs
description: Este tópico mostra como usar o codificador padrão nos Serviços de Mídia para codificar uma entrada de vídeo com uma escada de taxa de bits gerada automaticamente, com base na resolução de entrada e na taxa de bits.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: c25c32f35adc1c017f0f4c012c82bd7e0af8d452
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60733310"
---
#  <a name="encode-with-an-auto-generated-bitrate-ladder"></a>Codificar com uma escada de taxa de bits gerada automaticamente

## <a name="overview"></a>Visão geral

Este artigo explica como usar o codificador padrão nos Serviços de Mídia para codificar uma entrada de vídeo em uma escada de taxa de bits gerada automaticamente (pares de taxa de bits e resolução), com base na resolução de entrada e na taxa de bits. Essa configuração de codificador integrada nunca excederá a resolução de entrada e a taxa de bits. Por exemplo, se a entrada for 720p em 720p, a saída continuará 720p na melhor das hipóteses e iniciará com taxas menores que 3 Mbps.

### <a name="encoding-for-streaming"></a>Codificar para streaming

Quando você usa o **AdaptiveStreaming** predefinido em **Transformar**, você obtém uma saída que é adequada para distribuição via protocolos de streaming como HLS e DASH. Ao usar esta predefinição, o serviço determina de forma inteligente quantas camadas de vídeos devem ser geradas e, em quais taxas de bits e a resolução. O conteúdo de saída contém arquivos MP4 onde áudio codificado AAC e vídeo codificado H.264 não são intercalados.

Para ver um exemplo de como essa predefinição é usada, consulte [Transmitir um arquivo por streaming](stream-files-dotnet-quickstart.md).

## <a name="output"></a>Saída

Esta seção exibe três exemplos de camadas de vídeo de saída produzidas pelo codificador dos Serviços de Mídia como resultado da codificação com a predefinição do **AdaptiveStreaming**. Em todos os casos, a saída contém um arquivo MP4 somente de áudio com áudio estéreo codificado a 128 kbps.

### <a name="example-1"></a>Exemplo 1
Fonte com altura "1080" e taxa de quadros "29.970" produz seis camadas de vídeo:

|Camada|Altura|Largura|Taxa de bits (kbps)|
|---|---|---|---|
|1|1080|1920|6780|
|2|720|1280|3520|
|3|540|960|2210|
|4|360|640|1150|
|5|270|480|720|
|6|180|320|380|

### <a name="example-2"></a>Exemplo 2
Fonte com altura "720" e taxa de quadros "23.970" produz cinco camadas de vídeo:

|Camada|Altura|Largura|Taxa de bits (kbps)|
|---|---|---|---|
|1|720|1280|2940|
|2|540|960|1850|
|3|360|640|960|
|4|270|480|600|
|5|180|320|320|

### <a name="example-3"></a>Exemplo 3
Fonte com altura "360" e taxa de quadros "29.970" produz três camadas de vídeo:

|Camada|Altura|Largura|Taxa de bits (kbps)|
|---|---|---|---|
|1|360|640|700|
|2|270|480|440|
|3|180|320|230|

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Transmitir um arquivo por streaming](stream-files-dotnet-quickstart.md)
