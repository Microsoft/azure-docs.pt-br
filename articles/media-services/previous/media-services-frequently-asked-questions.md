---
title: Perguntas frequentes sobre os Serviços de Mídia do Azure | Microsoft Docs
description: Perguntas frequentes (FAQs)
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
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 3164445dc28a2436d8492902d865414da8d75702
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61463887"
---
# <a name="frequently-asked-questions"></a>Perguntas frequentes

Este artigo aborda perguntas frequentes geradas pela comunidade de usuários dos Serviços de Mídia do Azure (AMS).

## <a name="general-ams-faqs"></a>Perguntas frequentes sobre o AMS geral

P: como fazer streaming para dispositivos iOS da Apple?

R: adicione o caminho "(format = m3u8-aapl)" à parte "/ Manifest" do URL para informar o servidor de origem de streaming a devolver o conteúdo HLS para consumo em dispositivos nativos Apple iOS (para detalhes, consulte [ conteúdo ](media-services-deliver-content-overview.md)),

P: como dimensionar a indexação?

R: as unidades reservadas são as mesmas para tarefas de codificação e indexação. Siga as instruções em [Como dimensionar unidades reservadas de codificação](media-services-scale-media-processing-overview.md). **Observação** o desempenho do indexador não é afetado pelo tipo de unidade reservada.

P: carreguei, codifiquei e publiquei um vídeo. Qual seria o motivo pelo qual o vídeo não é reproduzido quando tento transmiti-lo?

R: um dos motivos mais comuns é não ter o ponto de extremidade de streaming do qual você está tentando reproduzir no estado de **Execução**.  

P: posso fazer a composição em uma transmissão ao vivo?

R: a composição em transmissões ao vivo atualmente não é oferecida nos Serviços de Mídia do Azure, ou seja, é necessário realizar a pré-composição em seu computador.

P: posso usar a CDN do Azure com transmissão ao vivo?

R: os Serviços de Mídia dão suporte à integração com o Azure CDN (para obter mais informações, confira [Como gerenciar pontos de extremidade de streaming em uma conta dos Serviços de Mídia](media-services-portal-manage-streaming-endpoints.md)).  Você pode usar a transmissão ao vivo o com CDN. Os Serviços de Mídia do Azure fornecem saídas de Smooth Streaming, HLS e MPEG-DASH. Todos esses formatos usam HTTP para transferir dados e obtêm os benefícios do cache de HTTP. Na transmissão ao vivo real, os dados de áudio/vídeo são divididos em fragmentos, e esses fragmentos individuais são armazenados em cache na CDN. Os únicos dados que precisam ser atualizados são os dados de manifesto. A CDN atualiza periodicamente os dados de manifesto.

P: os Serviços de Mídia do Azure dão suporte ao armazenamento de imagens?

R: se quiser apenas armazenar imagens JPEG ou PNG, você deverá mantê-las no Armazenamento de Blobs do Azure. Não há benefício em colocá-las em sua conta dos Serviços de Mídia, a menos que deseje mantê-las associadas a seus ativos de vídeo ou áudio. Ou, se você puder precisar usar as imagens como sobreposições no codificador de vídeo, o Media Encoder Standard oferece suporte à sobreposição de imagens sobre vídeos, assim, ele lista JPEG e PNG como formatos de entrada com suporte. Para obter mais informações, consulte [Criando sobreposições](media-services-advanced-encoding-with-mes.md#overlay).

P: Como fazer para copiar ativos de uma conta dos Serviços de Mídia para outra?

R: Para copiar ativos de uma conta dos Serviços de Mídia para outra usando o .NET, use o método de extensão [IAsset.Copy](https://github.com/Azure/azure-sdk-for-media-services-extensions/blob/dev/MediaServices.Client.Extensions/IAssetExtensions.cs#L354) disponível no repositório [Extensões do SDK do .NET dos Serviços de Mídia do Azure](https://github.com/Azure/azure-sdk-for-media-services-extensions/). Para saber mais, consulte o thread [deste](https://social.msdn.microsoft.com/Forums/azure/28912d5d-6733-41c1-b27d-5d5dff2695ca/migrate-media-services-across-subscription?forum=MediaServices) fórum.

P: Ao trabalhar com os AMS, quais são os caracteres aceitos nos nomes de arquivos?

R: Os serviços de mídia usam o valor da propriedade IAssetFile.Name ao construir URLs para o conteúdo de streaming (por exemplo, http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) Por esse motivo, não é permitida a codificação por porcentagem. O valor da propriedade **Name** não pode ter quaisquer dos seguintes [caracteres reservados para codificação de percentual](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters): !*'();:@&=+$,/?%#[]". Além disso, pode haver somente um ‘.’ Além disso, pode haver somente um '.' para a extensão de nome de arquivo.

P: Como se conectar usando o REST?

R: Para saber mais sobre como conectar-se à API do AMS, veja [Acessar a API dos Serviços de Mídia do Azure com a autenticação do Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

P: Como fazer para girar um vídeo durante o processo de codificação?

R: O [Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md) dá suporte à rotação em ângulos de 90/180/270. O comportamento padrão é "Auto", em que ele tentará detectar os metadados de rotação no arquivo MP4/MOV de entrada e compensá-lo. Inclua o seguinte elemento de **Fontes** em uma das predefinições de json definidas [aqui](media-services-mes-presets-overview.md):

    "Version": 1.0,
    "Sources": [
    {
      "Streams": [],
      "Filters": {
        "Rotation": "90"
      }
    }
    ],
    "Codecs": [

    ...


## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
