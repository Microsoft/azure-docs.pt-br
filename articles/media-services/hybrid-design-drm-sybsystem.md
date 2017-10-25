---
title: "Design híbrido dos subsistemas DRM usando os Serviços de Mídia do Azure | Microsoft Docs"
description: "Este tópico discute o design híbrido dos subsistemas DRM usando os Serviços de Mídia do Azure."
services: media-services
documentationcenter: 
author: willzhan
manager: cfowler
editor: 
ms.assetid: 18213fc1-74f5-4074-a32b-02846fe90601
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: willzhan;juliako
ms.openlocfilehash: 841b1164db6fd1a2c029b98392509c15f23158e2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="hybrid-design-of-drm-subsystems"></a>Design híbrido de subsistemas DRM

Este tópico discute o design híbrido dos subsistemas DRM usando os Serviços de Mídia do Azure.

## <a name="overview"></a>Visão geral

Os Serviços de Mídia do Azure dão suporte aos três sistemas DRM a seguir:

* PlayReady
* Widevine (Modular)
* FairPlay

O suporte DRM inclui criptografia DRM (criptografia dinâmica) e a entrega de licença, sendo que o Player de Mídia do Azure dá suporte a todos os três DRMs como um SDK do player de navegador.

Para um tratamento detalhado do design e da implementação do subsistema DRM/CENC, consulte o documento intitulado [CENC com vários DRM e controle de acesso](media-services-cenc-with-multidrm-access-control.md).

Embora ofereçamos suporte completo aos três sistemas DRM, às vezes os clientes precisam usar várias partes de suas próprias infraestruturas/subsistemas além dos Serviços de Mídia do Azure para criar um subsistema DRM híbrido.

Abaixo estão algumas perguntas comuns feitas pelos clientes:

* "Posso usar meus próprios servidores de licença DRM?" (Nesse caso, os clientes investiram no farm de servidores de licença do DRM com lógica de negócios inserida).
* "Posso usar somente sua entrega de licença do DRM nos Serviços de Mídia do Azure sem hospedar conteúdo no AMS?"

## <a name="modularity-of-the-ams-drm-platform"></a>Modularidade da plataforma de DRM do AMS

Como parte de uma plataforma de vídeo em nuvem abrangente, o DRM dos Serviços de Mídia do Azure tem um design elaborado pensando em flexibilidade e modularidade. Você pode usar os Serviços de Mídia do Azure com qualquer uma das seguintes combinações diferentes descritas na tabela a seguir (uma explicação da notação usada na tabela a seguir). 

|**Hospedagem e origem de conteúdo**|**Criptografia de conteúdo**|**Entrega de licença do DRM**|
|---|---|---|
|AMS|AMS|AMS|
|AMS|AMS|Terceiros|
|AMS|Terceiros|AMS|
|AMS|Terceiros|Terceiros|
|Terceiros|Terceiros|AMS|

### <a name="content-hosting--origin"></a>Hospedagem e origem de conteúdo

* AMS: ativo de vídeo é hospedado no AMS e streaming é por meio de pontos de extremidade de streaming do AMS (mas não necessariamente empacotamento dinâmico).
* Terceiros: o vídeo é hospedada e distribuído em uma plataforma de streaming de terceiros fora do AMS.

### <a name="content-encryption"></a>Criptografia de conteúdo

* AMS: criptografia de conteúdo é realizada dinamicamente/sob demanda por criptografia dinâmica do AMS.
* Terceiros: criptografia de conteúdo é executada fora do AMS usando um fluxo de trabalho pré-processamento.

### <a name="drm-license-delivery"></a>Entrega de licença do DRM

* AMS: a licença DRM é fornecida pelo serviço de entrega de licença do AMS.
* Terceiros: a licença DRM é fornecida por um servidor de licença DRM de terceiros fora do AMS.

## <a name="configure-based-on-your-hybrid-scenario"></a>Configurar com base em seu cenário híbrido

### <a name="content-key"></a>Chave de conteúdo

Por meio da configuração de uma chave de conteúdo, você pode controlar os seguintes atributos de criptografia dinâmica do AMS e serviço de entrega de licença do AMS:

* A chave de conteúdo usada para criptografia dinâmica do DRM.
* Conteúdo de licença do DRM a ser entregue por serviços de entrega de licença: direitos, chave de conteúdo e restrições.
* Tipo de **restrição de política de autorização da chave de conteúdo**: restrição aberta, de IP ou de token.
* Se o tipo **token** de **restrição de política de autorização de chave de conteúdo for usado**, a **restrição de política de autorização de chave de conteúdo** deverá ser cumprida antes da emissão de uma licença.

### <a name="asset-delivery-policy"></a>Política de fornecimento de ativos

Por meio da configuração de uma política de entrega de ativos, você pode controlar os seguintes atributos usados pelo empacotador dinâmico AMS e criptografia dinâmica de um ponto de extremidade de streaming do AMS:

* Combinação de protocolo de streaming e criptografia do DRM, como DASH em CENC (PlayReady e Widevine), streaming suave em PlayReady, HLS em Widevine ou PlayReady.
* As URLs de entrega de licença padrão/inseridas para cada um dos DRMs envolvidos.
* Se as URLs de aquisição de licença (LA_URLs) na lista de reprodução do DASH MPD ou do HLS contêm uma cadeia de caracteres de consulta de KID (ID de chave) para Widevine e FairPlay, respectivamente.

## <a name="scenarios-and-samples"></a>Cenários e exemplos

Com base em explicações na seção anterior, os cinco cenários híbridos a seguir usam as respectivas combinações de configurações de **Chave de conteúdo**-**Política de entrega de ativos** (as amostras mencionadas na última coluna seguem a tabela):

|**Hospedagem e origem de conteúdo**|**Criptografia do DRM**|**Entrega de licença do DRM**|**Configurar chave de conteúdo**|**Configurar política de entrega de ativos**|**Amostra**|
|---|---|---|---|---|---|
|AMS|AMS|AMS|Sim|Sim|Exemplo 1|
|AMS|AMS|Terceiros|Sim|Sim|Exemplo 2|
|AMS|Terceiros|AMS|Sim|Não|Exemplo 3|
|AMS|Terceiros|Externa|Não|Não|Exemplo 4|
|Terceiros|Terceiros|AMS|Sim|Não|    

Nos exemplos, a proteção PlayReady funciona tanto para DASH quanto para streaming suave. As URLs de vídeos abaixo são URLs de streaming suave. Para obter as URLs do DASH correspondentes, basta acrescentar "(format=mpd-time-csf)". Você pode usar o [player de teste de mídia do azure](http://aka.ms/amtest) para testar em um navegador. Ele permite que você configure qual protocolo de streaming usar sob qual técnica. IE11 e MS Edge no Windows 10 dão suporte a PlayReady por meio de EME. Para obter mais informações, consulte os [detalhes sobre a ferramenta de teste](https://blogs.msdn.microsoft.com/playready4/2016/02/28/azure-media-test-tool/).

### <a name="sample-1"></a>Exemplo 1

* URL da fonte (base): https://willzhanmswest.streaming.mediaservices.windows.net/1efbd6bb-1e66-4e53-88c3-f7e5657a9bbd/RussianWaltz.ism/manifest 
* PlayReady LA_URL (DASH e suave): https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/ 
* LA_URL do Widevine (DASH): https://willzhanmswest.keydelivery.mediaservices.windows.net/Widevine/?kid=78de73ae-6d0f-470a-8f13-5c91f7c4 
* LA_URL do FairPlay (HLS): https://willzhanmswest.keydelivery.mediaservices.windows.net/FairPlay/?kid=ba7e8fb0-ee22-4291-9654-6222ac611bd8 

### <a name="sample-2"></a>Exemplo 2

* URL da fonte (base): http://willzhanmswest.streaming.mediaservices.windows.net/1a670626-4515-49ee-9e7f-cd50853e41d8/Microsoft_HoloLens_TransformYourWorld_816p23.ism/Manifest 
* LA_URL do PlayReady (DASH e suave): http://willzhan12.cloudapp.net/PlayReady/RightsManager.asmx 

### <a name="sample-3"></a>Exemplo 3

* URL da fonte: https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500.ism/manifest 
* PlayReady LA_URL (DASH e suave): https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/ 

### <a name="sample-4"></a>Exemplo 4

* URL da fonte: https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500.ism/manifest 
* LA_URL do PlayReady (DASH e suave): https://willzhan12.cloudapp.net/playready/rightsmanager.asmx 

## <a name="summary"></a>Resumo

Em resumo, componentes de DRM dos Serviços de Mídia do Azure são flexíveis, você pode usá-los em um cenário híbrido configurando corretamente a chave de conteúdo e a política de entrega de ativos conforme descrito neste tópico.

## <a name="next-steps"></a>Próximas etapas
Exibir os roteiros de aprendizagem dos Serviços de Mídia.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

