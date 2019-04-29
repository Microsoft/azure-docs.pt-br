---
title: Proteger o conteúdo do HLS com o Apple FairPlay offline do Azure | Microsoft Docs
description: Este tópico fornece uma visão geral e mostra como usar os Serviços de Mídia do Azure para criptografar de forma dinâmica o seu conteúdo de HLS (HTTP Live Streaming) com o FairPlay da Apple no modo offline.
services: media-services
keywords: HBS, gerenciamento de direitos digitais, Streaming de FairPlay (FPS), Offline, iOS 10
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.assetid: 7c3b35d9-1269-4c83-8c91-490ae65b0817
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/08/2019
ms.author: willzhan
ms.openlocfilehash: 25bc7798853d350139a7802eaad68d52a1d7d99f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60996558"
---
# <a name="offline-fairplay-streaming-for-ios"></a>Streaming de FairPlay Offline para iOS 

 Os Serviços de Mídia do Azure fornecem um conjunto de [serviços de proteção de conteúdo](https://azure.microsoft.com/services/media-services/content-protection/) bem projetados, que abrangem:

- Microsoft PlayReady
- Google Widevine
- Apple FairPlay
- Criptografia AES-128

A criptografia de gerenciamento de direitos digitais (DRM)/AES do conteúdo é executada dinamicamente após a solicitação de vários protocolos de streaming. Os serviços de distribuição de chaves de descriptografia de licença DRM/AES também são fornecidos pelos Serviços de Mídia.

Além de proteger o conteúdo de streaming online por meio de vários protocolos de streaming, o modo offline para conteúdo protegido também é um recurso que normalmente é solicitado. O suporte ao modo offline é necessário para os seguintes cenários:

* Reprodução quando a conexão de Internet não estiver disponível, como durante viagens.
* Alguns provedores de conteúdo podem não permitir a entrega de licença do DRM além das fronteiras do país. Se os usuários desejarem inspecionar o conteúdo quando estiverem viajando fora do seu país, é necessário baixá-lo offline.
* Em alguns países, a disponibilidade de Internet e/ou a largura de banda ainda é limitada. Os usuários podem optar por fazer o download primeiro para que seja possível assistir o conteúdo em uma resolução que seja alta o suficiente para uma experiência de exibição satisfatória. Nesse caso, o problema geralmente não é a disponibilidade da rede, mas largura de banda de rede limitada. Os provedores de over-the-top (OTT)/plataforma de vídeo online (OVP) solicitam suporte para o modo offline.

Este artigo aborda o suporte ao modo offline de Streaming de FairPlay (FPS), focando em dispositivos que executam o iOS 10 ou posterior. Não há suporte para esse recurso para outras plataformas da Apple, como watchOS, tvOS ou Safari no macOS.

## <a name="prerequisites"></a>Pré-requisitos

Antes de implementar o DRM offline para FairPlay em um dispositivo iOS 10 +:

* Examine a proteção de conteúdo online para FairPlay: 

    - [Requisitos e configuração de licença do Apple FairPlay](fairplay-license-overview.md)
    - [Use criptografia dinâmica DRM e serviço de entrega de licenças](protect-with-drm.md)
    - Um exemplo de .NET que inclui a configuração de streaming FPS online: [ConfigureFairPlayPolicyOptions](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L505)
* Obter o SDK de FPS da Rede de Desenvolvedor da Apple. O SDK de FPS contém dois componentes:

    - O SDK de servidor de FPS, que contém o Módulo de Segurança de Chave (KSM), exemplos de cliente, uma especificação e um conjunto de vetores de teste.
    - O Pacote de Implantação de FPS, que contém a função D, a especificação com instruções sobre como gerar o certificado FPS, chave privada específica do cliente e a Chave de Segredo do Aplicativo. A Apple emite o Pacote de Implantação de FPS apenas para provedores de conteúdo licenciados.
* Clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git. 

    Você precisará modificar o código em [Criptografar com DRM usando .NET](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM) para adicionar configurações de FairPlay.  

## <a name="configure-content-protection-in-azure-media-services"></a>Configurar proteção de conteúdo nos Serviços de Mídia do Azure

No método [GetOrCreateContentKeyPolicyAsync](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L189), faça o seguinte:

Remova a marca de comentário do código que configura a opção de política do FairPlay:

```csharp
ContentKeyPolicyFairPlayConfiguration fairplayConfig = ConfigureFairPlayPolicyOptions();
```

Além disso, remova a marca de comentário do código que adiciona CBCS ContentKeyPolicyOption à lista de ContentKeyPolicyOptions

```csharp
options.Add(
    new ContentKeyPolicyOption()
    {
        Configuration = fairplayConfig,
        Restriction = restriction,
        Name = "ContentKeyPolicyOption_CBCS"
    });
```

## <a name="enable-offline-mode"></a>Habilitar modo offline

Para habilitar o modo offline, crie um StreamingPolicy personalizado e use seu nome ao criar um StreamingLocator em [CreateStreamingLocatorAsync](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L563).
 
```csharp
CommonEncryptionCbcs objStreamingPolicyInput= new CommonEncryptionCbcs()
{
    Drm = new CbcsDrmConfiguration()
    {
        FairPlay = new StreamingPolicyFairPlayConfiguration()
        {
            AllowPersistentLicense = true  //this enables offline mode
        }
    },
    EnabledProtocols = new EnabledProtocols()
    {
        Hls = true,
        Dash = true //Even though DASH under CBCS is not supported for either CSF or CMAF, HLS-CMAF-CBCS uses DASH-CBCS fragments in its HLS playlist
    },

    ContentKeys = new StreamingPolicyContentKeys()
    {
        //Default key must be specified if keyToTrackMappings is present
        DefaultKey = new DefaultKey()
        {
            Label = "CBCS_DefaultKeyLabel"
        }
    }

```

Agora, a sua conta de Serviços de Mídia está configurada para licenças de FairPlay distribuídas offline.

## <a name="sample-ios-player"></a>Exemplo de Player iOS

O suporte ao modo offline de FPS está disponível apenas no iOS 10 e posterior. O SDK do Servidor de FPS (versão 3.0 ou posterior) contém o documento e exemplo para o modo offline de FPS. Especificamente, o SDK do Servidor de FPS (versão 3.0 ou posterior) contém os dois seguintes itens relacionados ao modo offline:

* Documento: "Reprodução Offline com Streaming de FairPlay e HTTP Live Streaming". Apple, 14 de setembro de 2016. Na versão 4.0 do SDK do Servidor de FPS, este documento é mesclado no documento FPS principal.
* Exemplo de código: exemplo HLSCatalog (parte do SDK do FPS Server da Apple) para o modo offline de FPS em \FairPlay Streaming Server SDK version 3.1\Development\Client\HLSCatalog_With_FPS\HLSCatalog\. No aplicativo de exemplo HLSCatalog, os arquivos de código a seguir são usados para a implementação dos recursos do modo offline:

    - Arquivo de código AssetPersistenceManager.swift: AssetPersistenceManager é a classe principal neste exemplo que demonstra como:

        - Gerencie o download de fluxos HLS, como as APIs usadas para iniciar e cancelar downloads e excluir ativos existentes dos dispositivos.
        - Monitorar o andamento do download.
    - Arquivos de código AssetListTableViewController.swift e AssetListTableViewCell.swift: AssetListTableViewController é a principal interface deste exemplo. Ele fornece uma lista de ativos que o exemplo pode usar para reproduzir, baixar, excluir ou cancelar um download. 

Estas etapas mostram como configurar um player iOS em execução. Supondo que você começa com o exemplo HLSCatalog na versão 4.0.1 do SDK do Servidor de FPS, faça as alterações de código a seguir:

Em HLSCatalog\Shared\Managers\ContentKeyDelegate.swift, implemente o método `requestContentKeyFromKeySecurityModule(spcData: Data, assetID: String)` usando o código a seguir. Permitir que "drmUr" seja uma variável atribuída à URL do HLS.

```swift
    var ckcData: Data? = nil
    
    let semaphore = DispatchSemaphore(value: 0)
    let postString = "spc=\(spcData.base64EncodedString())&assetId=\(assetIDString)"
    
    if let postData = postString.data(using: .ascii, allowLossyConversion: true), let drmServerUrl = URL(string: self.drmUrl) {
        var request = URLRequest(url: drmServerUrl)
        request.httpMethod = "POST"
        request.setValue(String(postData.count), forHTTPHeaderField: "Content-Length")
        request.setValue("application/x-www-form-urlencoded", forHTTPHeaderField: "Content-Type")
        request.httpBody = postData
        
        URLSession.shared.dataTask(with: request) { (data, _, error) in
            if let data = data, var responseString = String(data: data, encoding: .utf8) {
                responseString = responseString.replacingOccurrences(of: "<ckc>", with: "").replacingOccurrences(of: "</ckc>", with: "")
                ckcData = Data(base64Encoded: responseString)
            } else {
                print("Error encountered while fetching FairPlay license for URL: \(self.drmUrl), \(error?.localizedDescription ?? "Unknown error")")
            }
            
            semaphore.signal()
            }.resume()
    } else {
        fatalError("Invalid post data")
    }
    
    semaphore.wait()
    return ckcData
```

Em HLSCatalog\Shared\Managers\ContentKeyDelegate.swift, implemente o método `requestApplicationCertificate()`. Essa implementação depende se você inseriu o certificado (apenas a chave pública) com o dispositivo ou hospedou o certificado na web. A implementação a seguir usa um certificado do aplicativo hospedado usado nos exemplos de teste. Permitir que “certUrl” seja uma variável que contém a URL do certificado do aplicativo.

```swift
func requestApplicationCertificate() throws -> Data {

        var applicationCertificate: Data? = nil
        do {
            applicationCertificate = try Data(contentsOf: URL(string: certUrl)!)
        } catch {
            print("Error loading FairPlay application certificate: \(error)")
        }
        
        return applicationCertificate
    }
```

Para o teste integrado final, tanto a URL do vídeo quanto a URL do certificado do aplicativo serão fornecidas na seção de "Teste Integrado".

No HLSCatalog\Shared\Resources\Streams.plist, adicione a URL do vídeo de teste. Para a ID da chave de conteúdo, use a URL de aquisição de licença FairPlay com o protocolo skd como o valor exclusivo.

![Streams do App de iOS FairPlay Offline](media/offline-fairplay-for-ios/offline-fairplay-ios-app-streams.png)

Use sua própria URL de vídeo de teste, URL de aquisição de licença FairPlay e URL de certificado do aplicativo, se você as tiver configurado. Ou você pode continuar para a próxima seção, que contém exemplos de teste.

## <a name="integrated-test"></a>Teste integrado

Três exemplos de teste nos Serviços de Mídia abrangem os três cenários a seguir:

* FPS protegido com faixa de vídeo, áudio e áudio alternativo
* FPS protegido com faixa de vídeo e áudio, mas sem áudio alternativo
* FPS protegido com vídeo somente e sem áudio

Você pode encontrar esses exemplos [neste site de demonstração](https://aka.ms/poc#22), com o certificado correspondente do aplicativo hospedado em um aplicativo Web do Azure.
Observamos que com o exemplo da versão 3 ou versão 4 de SDK do Servidor de FPS, se uma lista de reprodução mestre contiver áudio alternativo, durante o modo offline, ela toca somente o áudio. Portanto, você precisa da faixa de áudio alternativo. Em outras palavras, o segundo e terceiro exemplos listados anteriormente trabalham nos modos online e offline. O exemplo listado reproduz primeiro somente o áudio durante o modo offline, enquanto o streaming online funciona sem problemas.

## <a name="faq"></a>Perguntas frequentes

As perguntas frequentes a seguir fornecem assistência com a solução de problemas:

- **Por que apenas o áudio toca, mas nenhum vídeo aparece durante o modo offline?** Esse comportamento parece estar relacionado ao design do aplicativo de exemplo. Quando a faixa de áudio alternativo está presente (que é o caso para HLS) durante o modo offline, tanto iOS 10 e iOS 11 apresentarão por padrão uma faixa de áudio alternativo. Para compensar esse comportamento para o modo offline de FPS, é preciso remover a faixa de áudio alternativo da transmissão. Para fazer isso nos Serviços de Mídia, adicione o filtro de manifestação dinâmica “audio-only=false”. Em outras palavras, uma URL de HLS termina com .ism/manifest(format=m3u8-aapl,audio-only=false). 
- **Por que ela ainda toca áudio somente sem vídeo durante o modo offline após eu adicionar audio-only=false?** Dependendo do design da chave do cache de rede de distribuição de conteúdo (CDN), o conteúdo pode ser armazenado em cache. Limpe o cache.
- **O modo de FPS offline também tem suporte em iOS 11 além do iOS 10?** Sim. O modo offline de FPS tem suporte para iOS 10 e iOS 11.
- **Por que eu não consigo encontrar o documento “Reprodução Offline com o Streaming de FairPlay e HTTP Live Streaming no SDK do Servidor de FPS”?** Desde a versão 4 do SDK do Servidor de FPS, este documento foi mesclado no “Guia de Programação de Streaming de FairPlay”.
- **O que é a estrutura de arquivos offline/baixados em dispositivos iOS?** A estrutura do arquivo baixado em um dispositivo iOS tem a aparência da captura de tela abaixo. A pasta `_keys` armazena licenças de FPS baixadas, com um arquivo de armazenamento para cada host de serviço de licença. A pasta `.movpkg` armazena conteúdo de áudio e vídeo. A primeira pasta com o nome que termina com um traço seguido por um numero contém o conteúdo de vídeo. O valor numérico é o PeakBandwidth das representações de vídeo. A segunda pasta com um nome que termina com um traço seguido por 0 contém o conteúdo de áudio. A terceira pasta denominada "Dados" contém a lista de reprodução mestre do conteúdo de FPS. Por fim, boot.xml fornece uma descrição completa do conteúdo da pasta `.movpkg`. 

![Estrutura do arquivo de aplicativo iOS de Exemplo do FairPlay Offline](media/offline-fairplay-for-ios/offline-fairplay-file-structure.png)

Um arquivo de exemplo boot.xml:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<HLSMoviePackage xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://apple.com/IMG/Schemas/HLSMoviePackage" xsi:schemaLocation="http://apple.com/IMG/Schemas/HLSMoviePackage /System/Library/Schemas/HLSMoviePackage.xsd">
  <Version>1.0</Version>
  <HLSMoviePackageType>PersistedStore</HLSMoviePackageType>
  <Streams>
    <Stream ID="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" Path="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(127000)/Manifest(aac_eng_2_127,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
    <Stream ID="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" Path="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(161000)/Manifest(video,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
  </Streams>
  <MasterPlaylist>
    <NetworkURL>https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/manifest(format=m3u8-aapl,audio-only=false)</NetworkURL>
  </MasterPlaylist>
  <DataItems Directory="Data">
    <DataItem>
      <ID>CB50F631-8227-477A-BCEC-365BBF12BCC0</ID>
      <Category>Playlist</Category>
      <Name>master.m3u8</Name>
      <DataPath>Playlist-master.m3u8-CB50F631-8227-477A-BCEC-365BBF12BCC0.data</DataPath>
      <Role>Master</Role>
    </DataItem>
  </DataItems>
</HLSMoviePackage>
```

## <a name="next-steps"></a>Próximas etapas

Confira como [proteger com AES-128](protect-with-aes128.md)
