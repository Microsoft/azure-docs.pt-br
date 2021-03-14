---
title: Configure a sua conta para streaming offline ou conteúdo protegido por PlayReady - Azure.
description: Esse artigo mostra como configurar sua conta de Serviços de Mídia do Azure para streaming offline de PlayReady para Windows 10.
services: media-services
keywords: DASH, DRM, Modo Offline do Widevine, ExoPlayer, Android
documentationcenter: ''
author: IngridAtMicrosoft
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: willzhan
ms.custom: devx-track-csharp
ms.openlocfilehash: 828895894464eafc53f74e7c8b5798db4db03533
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103016127"
---
# <a name="offline-playready-streaming-for-windows-10"></a>PlayReady offline para Windows 10

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector" title1="Selecione a versão dos serviços de mídia que você está usando:"]
> * [Versão 3](../latest/offline-plaready-streaming-for-windows-10.md)
> * [Versão 2](offline-playready-streaming-windows-10.md)

> [!NOTE]
> Não estão sendo adicionados novos recursos ou funcionalidades aos Serviços de Mídia v2. <br/>Confira a versão mais recente, [Serviços de Mídia v3](../latest/index.yml). Além disso, consulte s [diretrizes de migração da v2 para a v3](../latest/migrate-v-2-v-3-migration-introduction.md)

Os serviços de mídia do Azure dão suporte offline ao download/reprodução com a proteção de DRM. Esse artigo aborda o suporte offline dos Serviços de Mídia do Azure para Windows 10 para os clientes do PlayReady. Você pode ler sobre o suporte ao modo offline para FairPlay/iOS e dispositivos Android/Widevine nos seguintes artigos:

- [Streaming de FairPlay Offline para iOS](media-services-protect-hls-with-offline-fairplay.md)
- [Streaming de Widevine offline para Android](offline-widevine-for-android.md)

## <a name="overview"></a>Visão geral

Esta seção fornece algumas informações básicas sobre a reprodução de modo offline, especialmente porque:

* Em alguns países/regiões, a disponibilidade e/ou a largura de banda da Internet ainda é limitada. Os usuários podem optar por fazer o download primeiro para que seja possível assistir o conteúdo em resolução alta o suficiente para uma experiência de exibição satisfatória. Nesse caso, com mais frequência, o problema não é a disponibilidade da rede, mas a limitação da largura de banda. Provedores OTT/OVP solicitando suporte ao modo offline.
* Conforme divulgado na conferência do acionista da Netflix 2016 Q3, fazer o download de conteúdo é um "recurso muito solicitado" e "estamos abertos para que ele" de acordo com Reed Hastings, CEO da Netflix.
* Alguns provedores de conteúdo podem impedir a entrega de licença do DRM além da borda de um país/região. Se um usuário quiser ver o conteúdo durante uma viagem para o exterior, é necessário fazer o download offline.
 
O desafio que enfrentamos em implementar o modo offline é o seguinte:

* O Mp4 é compatível com vários jogadores, ferramentas de codificador, mas não há nenhuma associação entre o contêiner MP4 e o DRM;
* A longo prazo, o CFF com CENC é a melhor opção. No entanto, o ecossistema de suporte de ferramentas/player não ainda existe ainda. Precisamos de uma solução no momento.
 
A ideia é: formato de arquivo de streaming suave ([PIFF](/iis/media/smooth-streaming/protected-interoperable-file-format)) com H264/AAC que possui uma associação com o PlayReady (AES-128 CTR). O arquivo individual Smooth streaming. ismv (supondo que o áudio está em vídeo) é um fMP4 que pode ser usado para reprodução. Se um conteúdo de streaming suave passar pela criptografia do PlayReady, cada arquivo. ismv se torna um MP4 fragmentado e protegido por PlayReady. Pode-se escolher um arquivo. ismv com a taxa de bits preferencial e renomeá-lo como .mp4 para fazer o download.

Há duas opções para hospedar o MP4 protegido por PlayReady para fazer o download progressivo:

* É possível colocar esse MP4 no mesmo ativo de serviço de contêiner/mídia e aproveitar o ponto de extremidade de streaming dos Serviços de Mídia do Azure para fazer o download progressivo;
* É possível usar o localizador de SAS para fazer o download progressivo diretamente do Armazenamento do Azure, ignorando os Serviços de Mídia do Azure.
 
É possível usar dois tipos de entrega de licença do PlayReady:

* Serviço de entrega da licença do PlayReady nos Serviços de Mídia do Azure;
* Servidores de licença do PlayReady hospedados em qualquer lugar.

Abaixo estão dois conjuntos de ativos de teste, o primeiro deles usa a entrega de licença do PlayReady nos Serviços de Mídia do Azure enquanto o segundo usa o meu servidor de licença do PlayReady hospedado em uma máquina virtual do Azure:

Ativo #1:

* URL de download progressivo: [https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4](https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4)
* PlayReady da LA_URL (Serviços de Mídia do Azure): `https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/`

Ativo #2:

* URL de download progressivo: [https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4](https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4)
* PlayReady da LA_URL (local): `https://willzhan12.cloudapp.net/playready/rightsmanager.asmx`

Para reprodução de teste, usei um Aplicativo Universal do Windows no Windows 10. Nas [amostras do Windows 10 Universal](https://github.com/Microsoft/Windows-universal-samples), há um exemplo de player básico chamado [exemplo de Streaming adaptável](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/AdaptiveStreaming). Só é necessário adicionar o código para que seja possível escolher o vídeo que foi feito o download e usá-lo como a origem, em vez da origem de streaming adaptável. As alterações estão acontecem ao clicar no botão manipulador de eventos:

```csharp
private async void LoadUri_Click(object sender, RoutedEventArgs e)
{
    //Uri uri;
    //if (!Uri.TryCreate(UriBox.Text, UriKind.Absolute, out uri))
    //{
    // Log("Malformed Uri in Load text box.");
    // return;
    //}
    //LoadSourceFromUriTask = LoadSourceFromUriAsync(uri);
    //await LoadSourceFromUriTask;

    //willzhan change start
    // Create and open the file picker
    FileOpenPicker openPicker = new FileOpenPicker();
    openPicker.ViewMode = PickerViewMode.Thumbnail;
    openPicker.SuggestedStartLocation = PickerLocationId.ComputerFolder;
    openPicker.FileTypeFilter.Add(".mp4");
    openPicker.FileTypeFilter.Add(".ismv");
    //openPicker.FileTypeFilter.Add(".mkv");
    //openPicker.FileTypeFilter.Add(".avi");

    StorageFile file = await openPicker.PickSingleFileAsync();

    if (file != null)
    {
       //rootPage.NotifyUser("Picked video: " + file.Name, NotifyType.StatusMessage);
       this.mediaPlayerElement.MediaPlayer.Source = MediaSource.CreateFromStorageFile(file);
       this.mediaPlayerElement.MediaPlayer.Play();
       UriBox.Text = file.Path;
    }
    else
    {
       // rootPage.NotifyUser("Operation cancelled.", NotifyType.ErrorMessage);
    }

    // On small screens, hide the description text to make room for the video.
    DescriptionText.Visibility = (ActualHeight < 500) ? Visibility.Collapsed : Visibility.Visible;
}
```

 ![Reprodução de modo offline fMP4 protegido do PlayReady](./media/offline-playready/offline-playready1.jpg)

Já que o vídeo está sob a proteção do PlayReady, a captura de tela não poderá incluir o vídeo.

Em resumo, atingimos o modo offline nos Serviços de Mídia do Azure:

* A criptografia do PlayReady e a transcodificação de conteúdo podem ser feitas em Serviços de Mídia do Azure ou outras ferramentas;
* O conteúdo pode ser hospedado em Serviços de Mídia do Azure ou no Armazenamento do Azure para fazer o download progressivo;
* A entrega de licença do PlayReady pode ser nos Serviços de Mídia do Azure ou em outro lugar;
* O conteúdo de streaming suave preparado ainda pode ser usado para streaming online por meio de DASH ou smooth com o PlayReady, como o gerenciamento de direitos digitais.

## <a name="additional-notes"></a>Observações adicionais

* O Widevine é um serviço fornecido pela Google Inc. e está sujeito aos termos de serviço e à política de privacidade da Google, Inc.

## <a name="next-steps"></a>Próximas etapas

[Design do sistema de DRM híbrido](hybrid-design-drm-sybsystem.md)
