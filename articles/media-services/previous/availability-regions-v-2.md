---
title: Disponibilidade regional dos serviços de mídia do Azure | Microsoft Docs
description: Este artigo é uma visão geral dos recursos do Serviços de Mídia do Microsoft Azure e da disponibilidade regional do serviço.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: references_regions
ms.openlocfilehash: 125f2be205760618ab6ffa3175f719f5e91e8c05
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103012251"
---
# <a name="media-services-regional-availability"></a>Disponibilidade regional dos serviços de mídia

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Não estão sendo adicionados novos recursos ou funcionalidades aos Serviços de Mídia v2. Confira a versão mais recente, [Serviços de Mídia v3](../latest/media-services-overview.md). Além disso, consulte s [diretrizes de migração da v2 para a v3](../latest/migrate-v-2-v-3-migration-introduction.md)

Os Serviços de Mídia do Microsoft Azure (AMS) permitem que você carregue com segurança, armazene, codifique e empacote o conteúdo de áudio ou vídeo para a entrega de streaming sob demanda e ao vivo para vários clientes (por exemplo, TV, PCs e dispositivos móveis).

O AMS opera em várias regiões em todo o mundo, dando a você flexibilidade para escolher onde criar seus aplicativos. Este artigo é uma visão geral dos recursos do Serviços de Mídia do Microsoft Azure e da disponibilidade regional do serviço.

Para obter mais informações sobre toda a infraestrutura global do Azure, consulte [geografias do Azure](https://azure.microsoft.com/global-infrastructure/geographies/).

## <a name="ams-accounts"></a>Contas AMS

Use os [produtos do Azure por região](https://azure.microsoft.com/global-infrastructure/services/?products=media-services&regions=all) para determinar se os serviços de mídia estão disponíveis em uma região específica.

## <a name="streaming-endpoints"></a>Pontos de extremidade de streaming

Os clientes dos Serviços de Mídia podem escolher um ponto de extremidade de streaming **Standard** ou do streaming **Premium**.

|Nome|Status|Região
|---|---|---|
|Standard|GA|Tudo|
|Premium|GA|Tudo|

## <a name="live-encoding"></a>Codificação ativa

Disponível em todas as regiões, exceto: Alemanha, sul do Brasil, Índia ocidental, sul da Índia e Índia central.

## <a name="encoding-media-processors"></a>Codificando processadores de mídia

A AMS oferece dois codificadores de sob demanda **Media Encoder Standard** e **Fluxo de Trabalho do Media Encoder Premium**. Para obter mais informações, consulte [Visão geral e comparação dos codificadores de mídia sob demanda do Azure](media-services-encode-asset.md).

|Nome do processador de mídia|Status|Regiões
|---|---|---|
|Media Encoder Standard|GA|Tudo|
|Fluxo de trabalho do Media Encoder Premium|GA|Todos, exceto China|

## <a name="analytics-media-processors"></a>Processadores de mídia da Análise

A Análise de Mídia é uma coleção de componentes de fala e pesquisa visual que facilitam a obtenção de análises acionáveis dos arquivos de vídeo de organizações e de empresas. Para saber mais, confira [Visão geral a Análise dos Serviços de Mídia do Azure](./legacy-components.md).

> [!NOTE]
> Alguns processadores de mídia de análise serão desativados. Para ver as datas de desativação, consulte o tópico [Componentes herdados](legacy-components.md).

|Nome do processador de mídia|Status|Região
|---|---|---|
|Detector de Rostos em Mídias do Azure|Visualização|Todos|
|Indexador de Mídia do Azure|GA|Tudo|
|Detector de Movimento em Mídias do Azure|Visualização|Todos|
|OCR de Mídia do Azure|Visualização|Todos|
|Azure Media Redactor|GA|Tudo|
|Miniaturas de Vídeo de Mídia do Azure|Visualização|Todos|

## <a name="protection"></a>Proteção

Os Serviços de Mídia do Microsoft Azure permitem proteger a mídia desde o momento em que ela deixa computador e durante o armazenamento, processamento e entrega. Para obter mais informações, consulte [Protegendo o conteúdo AMS](media-services-content-protection-overview.md).

|Criptografia|Status|Regiões|
|---|---|---| 
|Armazenamento|GA|Tudo|
|Chaves AES-128|GA|Tudo|
|FairPlay|GA|Tudo|
|PlayReady|GA|Tudo|
|Widevine|GA|Todos, exceto Alemanha, governo federal e China.

> [!NOTE]
> O Widevine é um serviço fornecido pela Google Inc. e está sujeito aos termos de serviço e à política de privacidade da Google, Inc.

## <a name="reserved-units-rus"></a>Unidades Reservadas (URs)

O número de unidades reservadas provisionadas determina o número de tarefas de mídia que podem ser processadas simultaneamente em uma determinada conta.

Disponível em todas as regiões.

## <a name="reserved-unit-ru-type"></a>Tipo de unidade reservada (UR)

Uma conta dos serviços de mídia está associada a um tipo de unidade reservada que determina a velocidade com que suas tarefas de processamento de mídia são concluídas. Você pode escolher entre os seguintes tipos de unidade reservada: S1, S2 ou S3.

|Nome do tipo de UR|Status|Regiões
|---|---|---|
|S1|GA|Tudo|
|S2|GA|Todos, exceto sul do Brasil e Oeste da Índia|
|S3|GA|Todos, exceto Oeste da Índia|

## <a name="next-steps"></a>Próximas etapas

[Migrar para os Serviços de Mídia v3](../latest/media-services-overview.md)

## <a name="provide-feedback"></a>Fornecer comentários

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]