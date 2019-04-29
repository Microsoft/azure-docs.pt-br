---
title: Publicar conteúdo no Portal do Azure | Microsoft Docs
description: Este tutorial orienta você pelas etapas de publicar o conteúdo no Portal do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 92c364eb-5a5f-4f4e-8816-b162c031bb40
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 2639d47a6c9d8da53c9d6bccde0d317698d5d9fd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61127835"
---
# <a name="publish-content-in-the-azure-portal"></a>Publicar conteúdo no Portal do Azure  
> [!div class="op_single_selector"]
> * [Portal](media-services-portal-publish.md)
> * [.NET](media-services-deliver-streaming-content.md)
> * [REST](media-services-rest-deliver-streaming-content.md)
> 
> 

## <a name="overview"></a>Visão geral
> [!NOTE]
> Para concluir este tutorial, você precisa de uma conta do Azure. Para obter detalhes, confira [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

Para fornecer a seus usuários uma URL que eles podem usar para transmitir ou baixar seu conteúdo, primeiro você deve publicar o ativo criando um localizador. Os localizadores fornecem acesso aos arquivos de ativo. Os Serviços de Mídia do Azure dão suporte a dois tipos de localizadores: 

* **Localizadores de streaming (OnDemandOrigin)**. Os localizadores de streaming são usados para streaming adaptável. Exemplos de streaming adaptável incluem Apple HLS (HTTP Live Streaming), Microsoft Smooth Streaming e DASH (Dynamic Adaptive Streaming por HTTP), também chamado de MPEG-DASH. Para criar um localizador de streaming, seu ativo deve conter um arquivo .ism. Por exemplo, http://amstest.streaming.mediaservices.windows.net/61b3da1d-96c7-489e-bd21-c5f8a7494b03/scott.ism/manifest.
* **Localizadores de URL por SAS (Assinatura de Acesso Compartilhado)**. Localizadores progressivos são usados para a entrega de vídeo por meio do download progressivo.

Para criar uma URL de streaming de HLS, anexe *(format=m3u8-aapl)* à URL:

    {streaming endpoint name-media services account name}/{locator ID}/{file name}.ism/Manifest(format=m3u8-aapl)

Para criar uma URL de streaming para reproduzir ativos de Smooth Streaming, use o formato de URL a seguir:

    {streaming endpoint name-media services account name}/{locator ID}/{file name}.ism/Manifest

Para criar uma URL de streaming MPEG DASH, anexe *(format=mpd-time-csf)* à URL:

    {streaming endpoint name-media services account name}/{locator ID}/{file name}.ism/Manifest(format=mpd-time-csf)

Uma URL de assinatura de acesso compartilhado tem o seguinte formato:

    {blob container name}/{asset name}/{file name}/{shared access signature}

Para obter mais informações, consulte [visão geral sobre entrega de conteúdo](media-services-deliver-content-overview.md).

> [!NOTE]
> Os localizadores que foram criados no Portal do Azure antes de março de 2015 têm uma data de validade de dois anos.  
> 
> 

Para atualizar uma data de validade em um localizador, você pode usar uma [API REST](https://docs.microsoft.com/rest/api/media/operations/locator#update_a_locator) ou uma [API .NET](https://go.microsoft.com/fwlink/?LinkID=533259). 

> [!NOTE]
> Quando você atualiza a data de validade de um localizador SAS, a URL é alterada.

### <a name="to-use-the-portal-to-publish-an-asset"></a>Para usar o portal para publicar um ativo
1. No [Portal do Azure](https://portal.azure.com/), selecione sua conta dos Serviços de Mídia do Azure.
2. Selecione **Configurações** > **Ativos**. Selecione o ativo que você deseja publicar.
3. Clique no botão **Publicar**.
4. Selecione o tipo de localizador.
5. Selecione **Adicionar**.
   
    ![Publicar o vídeo](./media/media-services-portal-vod-get-started/media-services-publish1.png)

A URL é adicionada à lista de **URLs publicadas**.

## <a name="play-content-in-the-portal"></a>Reproduzir conteúdo no portal
Você pode testar o vídeo em um player de conteúdo no Portal do Azure.

Selecione o vídeo e, em seguida, selecione o botão **Reproduzir**.

![Reproduzir o vídeo no Portal do Azure](./media/media-services-portal-vod-get-started/media-services-play.png)

Algumas considerações se aplicam:

* Verifique se que o vídeo foi publicado.
* O player de mídia do Portal do Azure reproduz do ponto de extremidade de streaming padrão. Se você quiser reproduzir de um ponto de extremidade de streaming não padrão, selecione e copie a URL e cole-a em outro player. Por exemplo, você pode testar o vídeo no [Player de Mídia do Azure](https://amsplayer.azurewebsites.net/azuremediaplayer.html).
* O ponto de extremidade de streaming do qual você estiver transmitindo deverá estar em execução.  

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Próximas etapas
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

