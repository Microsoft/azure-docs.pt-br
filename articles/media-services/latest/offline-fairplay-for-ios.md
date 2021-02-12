---
title: Streaming de FairPlay offline dos serviços de mídia V3 para iOS
description: Este tópico fornece uma visão geral e mostra como usar os serviços de mídia do Azure V3 para criptografar dinamicamente seu conteúdo de HTTP Live Streaming (HLS) com o Apple FairPlay no modo offline.
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
ms.topic: how-to
ms.date: 08/31/2020
ms.author: willzhan
ms.custom: devx-track-csharp
ms.openlocfilehash: 399f9b0184217db8180fcec38e4f78917d5c9bc8
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955220"
---
# <a name="offline-fairplay-streaming-for-ios-with-media-services-v3"></a>Streaming FairPlay offline para iOS com os serviços de mídia v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

 Os Serviços de Mídia do Azure fornecem um conjunto de [serviços de proteção de conteúdo](https://azure.microsoft.com/services/media-services/content-protection/) bem projetados, que abrangem:

- Microsoft PlayReady
- Google Widevine
    
    O Widevine é um serviço fornecido pela Google Inc. e está sujeito aos termos de serviço e à política de privacidade da Google, Inc.
- Apple FairPlay
- Criptografia AES-128

A criptografia de gerenciamento de direitos digitais (DRM)/AES do conteúdo é executada dinamicamente após a solicitação de vários protocolos de streaming. Os serviços de distribuição de chaves de descriptografia de licença DRM/AES também são fornecidos pelos Serviços de Mídia.

Além de proteger o conteúdo de streaming online por meio de vários protocolos de streaming, o modo offline para conteúdo protegido também é um recurso que normalmente é solicitado. O suporte ao modo offline é necessário para os seguintes cenários:

* Reprodução quando a conexão de Internet não estiver disponível, como durante viagens.
* Alguns provedores de conteúdo podem impedir a entrega de licença do DRM além da borda de um país/região. Se os usuários desejarem assistir ao conteúdo durante a viagem fora do país/região, o download offline será necessário.
* Em alguns países/regiões, a disponibilidade e/ou a largura de banda da Internet ainda é limitada. Os usuários podem optar por fazer o download primeiro para que seja possível assistir o conteúdo em uma resolução que seja alta o suficiente para uma experiência de exibição satisfatória. Nesse caso, o problema geralmente não é a disponibilidade da rede, mas largura de banda de rede limitada. Os provedores de over-the-top (OTT)/plataforma de vídeo online (OVP) solicitam suporte para o modo offline.

Este artigo aborda o suporte ao modo offline de Streaming de FairPlay (FPS), focando em dispositivos que executam o iOS 10 ou posterior. Não há suporte para esse recurso para outras plataformas da Apple, como watchOS, tvOS ou Safari no macOS.

> [!NOTE]
> O DRM offline é cobrado apenas para fazer uma única solicitação de licença quando você baixa o conteúdo. Quaisquer erros não são cobrados.

## <a name="prerequisites"></a>Pré-requisitos

Antes de implementar o DRM offline para FairPlay em um dispositivo iOS 10 +:

* Examine a proteção de conteúdo online para FairPlay: 

    - [Requisitos e configuração de licença do Apple FairPlay](fairplay-license-overview.md)
    - [Usar o serviço de entrega de licença e criptografia dinâmica do DRM](protect-with-drm.md)
    - Um exemplo do .NET que inclui a configuração do streaming online de FPS: [ConfigureFairPlayPolicyOptions](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L505)
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

Para habilitar o modo offline, crie um StreamingPolicy personalizado e use seu nome ao criar um StreamingLocator em [CreateStreamingLocatorAsync](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L561).
 
```csharp
CommonEncryptionCbcs objStreamingPolicyInput= new CommonEncryptionCbcs()
{
    Drm = new CbcsDrmConfiguration()
    {
        FairPlay = new StreamingPolicyFairPlayConfiguration()
        {
            AllowPersistentLicense = true // This enables offline mode
        }
    },
    EnabledProtocols = new EnabledProtocols()
    {
        Hls = true,
        Dash = true // Even though DASH under CBCS is not supported for either CSF or CMAF, HLS-CMAF-CBCS uses DASH-CBCS fragments in its HLS playlist
    },

    ContentKeys = new StreamingPolicyContentKeys()
    {
        // Default key must be specified if keyToTrackMappings is present
        DefaultKey = new DefaultKey()
        {
            Label = "CBCS_DefaultKeyLabel"
        }
    }
}

```

Agora, a sua conta de Serviços de Mídia está configurada para licenças de FairPlay distribuídas offline.

## <a name="sample-ios-player"></a>Exemplo de Player iOS

O suporte ao modo offline de FPS está disponível apenas no iOS 10 e posterior. O SDK do Servidor de FPS (versão 3.0 ou posterior) contém o documento e exemplo para o modo offline de FPS. Especificamente, o SDK do Servidor de FPS (versão 3.0 ou posterior) contém os dois seguintes itens relacionados ao modo offline:

* Documento: “Reprodução Offline com Streaming de FairPlay e HTTP Live Streaming”. Apple, 14 de setembro de 2016. Na versão 4.0 do SDK do Servidor de FPS, este documento é mesclado no documento FPS principal.
* Código de exemplo: exemplo de HLSCatalog (parte do SDK do servidor de FPS da Apple) para o modo offline de FPS no \FairPlay Streaming Server SDK versão 3.1 \ Development\Client\ HLSCatalog_With_FPS \HLSCatalog\. No aplicativo de exemplo HLSCatalog, os arquivos de código a seguir são usados para a implementação dos recursos do modo offline:

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

Veja as perguntas frequentes que [fornecem assistência com a solução de problemas](frequently-asked-questions.md#why-does-only-audio-play-but-not-video-during-offline-mode).

## <a name="next-steps"></a>Próximas etapas

Confira como [proteger com AES-128](protect-with-aes128.md)
