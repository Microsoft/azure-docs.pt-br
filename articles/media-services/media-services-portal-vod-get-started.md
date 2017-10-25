---
title: "Introdução ao fornecimento de vídeo sob demanda usando o portal do Azure | Microsoft Docs"
description: "Este tutorial o orienta ao longo das etapas de implementação de um serviço básico de fornecimento de conteúdo de vídeo sob demanda com o aplicativo dos Serviços de Mídia do Azure no portal do Azure."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 6c98fcfa-39e6-43a5-83a5-d4954788f8a4
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/07/2017
ms.author: juliako
ms.openlocfilehash: fb981f3240799c924464c828b2c835ac5d9879ed
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-delivering-content-on-demand-by-using-the-azure-portal"></a>Introdução ao fornecimento de conteúdo sob demanda usando o portal do Azure
[!INCLUDE [media-services-selector-get-started](../../includes/media-services-selector-get-started.md)]

Este tutorial o orienta ao longo das etapas de implementação de um serviço básico de fornecimento de conteúdo de vídeo sob demanda com o aplicativo dos Serviços de Mídia do Azure no portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos
Os seguintes itens são necessários para concluir o tutorial:

* Uma conta do Azure. Para obter detalhes, confira [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). 
* Uma conta dos Serviços de Mídia. Para criar uma conta de Serviços de Mídia, confira [Como criar uma conta dos Serviços de Mídia](media-services-portal-create-account.md).

Este tutorial inclui as seguintes tarefas:

1. Iniciar o ponto de extremidade de streaming.
2. Carregar um arquivo de vídeo.
3. Codificar o arquivo de origem em um conjunto de arquivos MP4 com taxa de bits adaptável.
4. Publicar o ativo e obter URLs de download progressivo e streaming.  
5. Reproduzir o conteúdo.

## <a name="start-the-streaming-endpoint"></a>Iniciar o ponto de extremidade de streaming

Um dos cenários mais comuns ao se trabalhar com os Serviços de Mídia do Azure é o fornecimento de vídeo via streaming de taxa de bits adaptável. Os Serviços de Mídia oferecem empacotamento dinâmico. Com o empacotamento dinâmico, você pode veicular seu conteúdo codificado em MP4 com taxa de bits adaptável em formatos de streaming just-in-time com suporte pelos Serviços de Mídia. Exemplos incluem Apple HLS (HTTP Live Streaming), Smooth Streaming e DASH (Dynamic Adaptive Streaming por HTTP), também chamado de MPEG-DASH. Usando o streaming de taxa de bits adaptável, você pode veicular seus vídeos sem armazenar versões pré-empacotadas de cada um desses formatos de streaming.

> [!NOTE]
> Ao criar sua conta dos Serviços de Mídia do Azure, um ponto de extremidade de streaming padrão será adicionado à sua conta em estado **Parado**. Para iniciar seu conteúdo de streaming e tirar proveito do empacotamento dinâmico e da criptografia dinâmica, o ponto de extremidade de streaming do qual você deseja transmitir o conteúdo deve estar em estado **Executando**. 

Para iniciar o ponto de extremidade de streaming:

1. Entre no [Portal do Azure](https://portal.azure.com/).
2. Selecione **Configurações** > **Pontos de extremidade de streaming**. 
3. Selecione o ponto de extremidade de streaming padrão. A janela **DETALHES DO PONTO DE EXTREMIDADE DE STREAMING PADRÃO** é exibida.
4. Selecione o ícone **Iniciar**.
5. Selecione o botão **Salvar**.

## <a name="upload-files"></a>Carregar arquivos
Para transmitir vídeos usando os Serviços de Mídia do Azure, carregue os vídeos de origem, codifique-os em várias taxas de bits e publique o resultado. A primeira etapa é abordada nesta seção. 

1. No [Portal do Azure](https://portal.azure.com/), selecione sua conta dos Serviços de Mídia do Azure.
2. Selecione **Configurações** > **Ativos**. Em seguida, selecione o botão **Carregar**.
   
    ![Carregar arquivos](./media/media-services-portal-vod-get-started/media-services-upload.png)
   
    A janela **Carregar um ativo de vídeo** é exibida.
   
   > [!NOTE]
   > Os Serviços de Mídia não limitam o tamanho do arquivo no carregamento de vídeos.
   > 
   > 
3. Em seu computador, vá para o vídeo que você deseja carregar. Selecione o vídeo e selecione **OK**.  
   
    O carregamento é iniciado. Você pode ver o progresso abaixo do nome do arquivo.  

Quando o carregamento é concluído, o novo ativo fica listado no painel **Ativos**. 

## <a name="encode-assets"></a>Codificar ativos
Para aproveitar o empacotamento dinâmico, você deve codificar o arquivo de origem em um conjunto de arquivos MP4 de múltiplas taxas de bits. As etapas de codificação são demonstradas nesta seção.

### <a name="encode-assets-in-the-portal"></a>Codificar ativos no portal
Para codificar o conteúdo usando o Media Encoder Standard no portal do Azure:

1. No [Portal do Azure](https://portal.azure.com/), selecione sua conta dos Serviços de Mídia do Azure.
2. Selecione **Configurações** > **Ativos**. Selecione o ativo que você deseja codificar.
3. Selecione o botão **Codificar**.
4. No painel **Codificar um ativo** , selecione o processador **Media Encoder Standard** e uma predefinição. Para saber mais sobre as predefinições, confira [Gerar automaticamente uma escada de taxa de bits](media-services-autogen-bitrate-ladder-with-mes.md) e [Predefinições de tarefa para Media Encoder Standard](media-services-mes-presets-overview.md). É importante escolher a predefinição que funcione melhor para o vídeo de entrada. Por exemplo, se você souber que o vídeo de entrada tem uma resolução de 1920 x 1080 pixels, poderá usar a predefinição **H264 Taxas de Bits Múltiplas 1080p**. Se você tiver um vídeo de resolução baixa (640 x 360), você não deverá usar a predefinição **H264 Taxas de Bits Múltiplas 1080p**.
   
   Para ajudá-lo a gerenciar seus recursos, você pode editar o nome do ativo de saída e o nome do trabalho.
   
   ![Codificar ativos](./media/media-services-portal-vod-get-started/media-services-encode1.png)
5. Selecione **Criar**.

### <a name="monitor-encoding-job-progress"></a>Monitorar o andamento do trabalho de codificação
Para monitorar o andamento do trabalho de codificação, selecione **Configurações** e selecione **Trabalhos**.

![Trabalhos](./media/media-services-portal-vod-get-started/media-services-jobs.png)

## <a name="publish-content"></a>Publicar conteúdo
Para fornecer a seus usuários uma URL que eles podem usar para transmitir ou baixar seu conteúdo, primeiro você deve publicar o ativo criando um localizador. Os localizadores fornecem acesso aos arquivos que estão no ativo. Os Serviços de Mídia dão suporte a dois tipos de localizadores: 

* **Localizadores de streaming (OnDemandOrigin)**. Os localizadores de streaming são usados para streaming adaptável. HLS, Smooth Streaming e MPEG-DASH são exemplos de streaming adaptável. Para criar um localizador de streaming, seu ativo deve conter um arquivo .ism. 
* **Localizadores de URL por SAS (Assinatura de Acesso Compartilhado)**. Localizadores progressivos são usados para a entrega de vídeo por meio do download progressivo.

Para criar uma URL de streaming de HLS, anexe *(format=m3u8-aapl)* à URL:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{file name}.ism/Manifest(format=m3u8-aapl)

Para criar uma URL de streaming para reproduzir ativos de Smooth Streaming, use o formato de URL a seguir:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{file name}.ism/Manifest

Para criar uma URL de streaming MPEG DASH, anexe *(format=mpd-time-csf)* à URL:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{file name}.ism/Manifest(format=mpd-time-csf)

Uma URL de assinatura de acesso compartilhado tem o seguinte formato:

    {blob container name}/{asset name}/{file name}/{shared access signature}

> [!NOTE]
> Os localizadores que foram criados no portal do Azure antes de março de 2015 têm uma data de validade de dois anos.  
> 
> 

Para atualizar uma data de validade em um localizador, você pode usar uma [API REST](https://docs.microsoft.com/rest/api/media/operations/locator#update_a_locator) ou uma [API .NET](http://go.microsoft.com/fwlink/?LinkID=533259). 

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

## <a name="play-content-from-the-portal"></a>Reproduzir conteúdo do portal
Você pode testar o vídeo em um player de conteúdo no Portal do Azure.

Selecione o vídeo e, em seguida, selecione o botão **Reproduzir**.

![Reproduzir o vídeo no Portal do Azure](./media/media-services-portal-vod-get-started/media-services-play.png)

Algumas considerações se aplicam:

* Para iniciar o streaming, inicie a execução do ponto de extremidade de streaming padrão.
* Verifique se que o vídeo foi publicado.
* O player de mídia do Portal do Azure reproduz do ponto de extremidade de streaming padrão. Se você quiser reproduzir de um ponto de extremidade de streaming não padrão, selecione e copie a URL e cole-a em outro player. Por exemplo, você pode testar o vídeo no [Player de Mídia do Azure](http://amsplayer.azurewebsites.net/azuremediaplayer.html).

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Próximas etapas
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]
