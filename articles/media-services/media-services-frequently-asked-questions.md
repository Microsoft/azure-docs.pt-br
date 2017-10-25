---
title: "Perguntas frequentes sobre os Serviços de Mídia do Azure | Microsoft Docs"
description: Perguntas frequentes (FAQs)
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 5374f7f4-c189-43ef-8b7f-f2f4141e2748
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.author: juliako
ms.openlocfilehash: e2a580b835496c677f048599ae83287dbb61621d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="frequently-asked-questions"></a>Perguntas frequentes

Este artigo aborda perguntas frequentes geradas pela comunidade de usuários dos Serviços de Mídia do Azure (AMS).

## <a name="general-ams-faqs"></a>Perguntas frequentes sobre o AMS geral

P: Como fazer streaming para dispositivos iOS da Apple?

A: Adicione o caminho "(format = m3u8-aapl)" à parte "/Manifest" da URL para informar o servidor de origem de streaming para retornar o conteúdo HLS para consumo nos dispositivos nativos Apple iOS. Para saber mais detalhes, consulte (distribuição de conteúdo)[media-services-deliver-content-overview.md],

P: Como você dimensiona indexação?

R: As unidades reservadas são as mesmas para tarefas de codificação e indexação. Siga as instruções em [Como dimensionar unidades reservadas de codificação](media-services-scale-media-processing-overview.md). **Observação** o desempenho do indexador não é afetado pelo tipo de unidade reservada.

P: Carreguei, codifiquei e publiquei um vídeo. Qual seria o motivo pelo qual o vídeo não é reproduzido quando tento transmiti-lo?

R: Um dos motivos mais comuns é que você não tem o ponto de extremidade de streaming do qual está tentando reproduzir no estado **Executando**.  

P: Posso fazer a composição em um fluxo ao vivo?

R: A composição de fluxos ao vivo atualmente não é oferecida nos Serviços de Mídia do Azure. Portanto, seria necessário realizar a pré-composição em seu computador.

P: Pode usar o CDN do Azure com a transmissão ao vivo?

R: Os Serviços de Mídia dão suporte à integração com o Azure CDN (para obter mais informações, consulte [Como gerenciar pontos de extremidade de streaming em uma conta dos Serviços de Mídia](media-services-portal-manage-streaming-endpoints.md)).  Você pode usar a transmissão ao vivo o com CDN. Os Serviços de Mídia do Azure fornecem saídas de Smooth Streaming, HLS e MPEG-DASH. Todos esses formatos usam HTTP para transferir dados e obtêm os benefícios do cache de HTTP. Na transmissão ao vivo real, os dados de áudio/vídeo são divididos em fragmentos, e esses fragmentos individuais são armazenados em cache na CDN. Os únicos dados que precisam ser atualizados são os dados de manifesto. A CDN atualiza periodicamente os dados de manifesto.

P: Os Serviços de Mídia do Azure dão suporte ao armazenamento de imagens?

R: Se quiser apenas armazenar imagens JPEG ou PNG, você deverá mantê-las no Armazenamento de Blob do Azure. Não há benefício em colocá-las em sua conta dos Serviços de Mídia, a menos que deseje mantê-las associadas a seus ativos de vídeo ou áudio. Ou, se você puder precisar usar as imagens como sobreposições no codificador de vídeo, o Media Encoder Standard oferece suporte à sobreposição de imagens sobre vídeos, assim, ele lista JPEG e PNG como formatos de entrada com suporte. Para obter mais informações, consulte [Criando sobreposições](media-services-advanced-encoding-with-mes.md#overlay).

P: Como posso copiar ativos de uma conta de serviços de mídia para outra.

R: Para copiar ativos de uma conta de Serviços de Mídia para outra usando. NET, use o método de extensão [IAsset.Copy](https://github.com/Azure/azure-sdk-for-media-services-extensions/blob/dev/MediaServices.Client.Extensions/IAssetExtensions.cs#L354) disponível no repositório de [Extensões do SDK do .NET dos Serviços de Mídia do Azure](https://github.com/Azure/azure-sdk-for-media-services-extensions/). Para saber mais, consulte o thread [deste](https://social.msdn.microsoft.com/Forums/azure/28912d5d-6733-41c1-b27d-5d5dff2695ca/migrate-media-services-across-subscription?forum=MediaServices) fórum.

P: quais são os caracteres com suporte para a nomeação de arquivos ao trabalhar com o AMS?

R: os Serviços de Mídia usam o valor da propriedade IAssetFile.Name ao construir URLs para o conteúdo de streaming (por exemplo, http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) Por esse motivo, não é permitida a codificação por porcentagem. O valor da propriedade **Name** não pode ter quaisquer dos seguintes [caracteres reservados para codificação de percentual](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters): !*'();:@&=+$,/?%#[]". Além disso, pode haver somente um ‘.’ Além disso, pode haver somente um '.' para a extensão de nome de arquivo.

P: como se conectar usando o REST?

R: Para saber mais sobre como conectar-se à API do AMS, veja [Acessar a API dos Serviços de Mídia do Azure com a autenticação do Azure AD](media-services-use-aad-auth-to-access-ams-api.md). Depois de se conectar com êxito em https://media.windows.net, você receberá um redirecionamento 301 especificando outro URI dos serviços de mídia. Você deve fazer chamadas subsequentes para o novo URI. 

P: Como é possível girar um vídeo durante o processo de codificação.

R: o [Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md) dá suporte à rotação em ângulos de 90/180/270. O comportamento padrão é "Auto", em que ele tentará detectar os metadados de rotação no arquivo MP4/MOV de entrada e compensá-lo. Inclua o seguinte elemento de **Fontes** em uma das predefinições de json definidas [aqui](media-services-mes-presets-overview.md):

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
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
