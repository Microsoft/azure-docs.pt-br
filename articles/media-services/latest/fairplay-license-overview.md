---
title: Suporte de licença do Apple FairPlay e Serviços de Mídia - Azure | Microsoft Docs
description: Este tópico fornece uma visão geral de uma licença de FairPlay da Apple requisitos e configuração.
author: juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2018
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 6d4b7ba842d08723b90a4f2491d9e79e68dd932e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60733565"
---
# <a name="apple-fairplay-license-requirements-and-configuration"></a>Requisitos e configuração de licença do Apple FairPlay 

Os Serviços de Mídia do Azure permitem criptografar o conteúdo HLS com **Apple FairPlay** (AES-128 CBC). Os Serviços de Mídia do Azure também fornecem um serviço para fornecer licenças do FairPlay. Quando um player tenta reproduzir o conteúdo protegido pelo FairPlay, uma solicitação é enviada ao serviço de entrega de licenças para obter uma licença. Se o serviço de licença aprovar a solicitação, ele emite a licença que é enviada ao cliente e é usada para descriptografar e reproduzir o conteúdo especificado.

Os Serviços de Mídia também fornecem as APIs que podem ser usadas para configurar suas licenças do FairPlay. Este tópico aborda os requisitos de licença do FairPlay e demonstra como é possível configurar uma licença do **FairPlay** usando APIs dos Serviços de Mídia. 

## <a name="requirements"></a>Requisitos

Os itens a seguir são necessários ao usar os Serviços de Mídia para criptografar o conteúdo HLS com **Apple FairPlay** e usar os Serviços de Mídia para fornecer licenças do FairPlay:

* Inscreva-se no [Programa de Desenvolvimento da Apple](https://developer.apple.com/).
* A Apple exige que o proprietário do conteúdo obtenha o [pacote de implantação](https://developer.apple.com/contact/fps/). Declare que você já implementou o KSM (Módulo de Segurança de Chave) com os Serviços de Mídia e que está solicitando o pacote final do FPS. Há instruções no pacote final do FPS para gerar certificação e obter a ASK (Chave de Segredo do Aplicativo). Você usa a ASK para configurar o FairPlay.
* Os itens a seguir devem ser definidos no lado de entrega de chave/licença dos Serviços de Mídia:

    * **Certificado do aplicativo (AC)**: Trata-se de um arquivo .pfx que contém a chave privada. Você cria esse arquivo e o criptografa com uma senha. O arquivo .pfx deve estar no formato Base64.

        As etapas a seguir descrevem como gerar um arquivo de certificado .pfx para FairPlay:

        1. Instale o OpenSSL a partir de https://slproweb.com/products/Win32OpenSSL.html.

            Vá para a pasta onde se encontram o certificado FairPlay e outros arquivos enviados pela Apple.
        2. Execute o comando a seguir na linha de comando. Isso converte o arquivo .cer em um arquivo .pem.

            "C:\OpenSSL-Win32\bin\openssl.exe" x509 -inform der -in FairPlay.cer -out FairPlay-out.pem
        3. Execute o comando a seguir na linha de comando. Isso converte o arquivo .pem em um arquivo .pfx com a chave privada. A senha para o arquivo .pfx é solicitada pelo OpenSSL.

            "C:\OpenSSL-Win32\bin\openssl.exe" pkcs12 -export -out FairPlay-out.pfx -inkey privatekey.pem -in FairPlay-out.pem -passin file:privatekey-pem-pass.txt
            
    * **Senha do certificado do aplicativo**: A senha para criar o arquivo .pfx.
    * **ASK**: Essa chave é recebida quando você gera a certificação usando o portal do Desenvolvedor da Apple. Cada equipe de desenvolvimento receberá uma ASK exclusiva. Salve uma cópia da ASK e armazene-a em um local seguro. É necessário configurar ASK como FairPlayAsk com Serviços de Mídia.
    
* Os seguintes itens devem ser definidos pelo lado do cliente FPS:

  * **Certificado do aplicativo (AC)**: Trata-se de um arquivo .cer/.der que contém a chave pública que o sistema operacional usa para criptografar conteúdo. Os Serviços de Mídia precisam ter conhecimento sobre ele, uma vez que ele é exibido pelo player. O serviço de distribuição de chaves descriptografa-o usando a chave privada correspondente.

* Para reproduzir uma transmissão criptografada do FairPlay, obtenha a ASK real primeiro e, em seguida, gere um certificado real. Esse processo cria três partes:

  * arquivo .der
  * arquivo .pfx
  * senha do .pfx

## <a name="fairplay-and-player-apps"></a>Aplicativos Player e FairPlay

Quando o conteúdo é criptografado com **Apple FairPlay**, os exemplos individuais de áudio e vídeo são criptografadas usando o modo **AES-128 CBC**. **FPS** (FairPlay Streaming) é integrado aos sistemas operacionais de dispositivos, com suporte nativo no iOS e na Apple TV. O Safari no OS X habilita o FPS usando o suporte à interface EME (Extensões de Mídia Criptografada).

O Player de Mídia do Azure também dá suporte à reprodução do FairPlay. Para mais informações, consulte a [documentação do Player de Mídia do Azure](https://amp.azure.net/libs/amp/latest/docs/index.html).

É possível desenvolver os próprios aplicativos de player usando SDK de iOS. Para poder reproduzir conteúdo do FairPlay, você precisa implementar o protocolo de troca de licenças. Esse protocolo não é especificado pela Apple. Depende de cada aplicativo o modo de enviar solicitações de distribuição de chaves. O serviço de distribuição de chaves do FairPlay nos Serviços de Mídia espera que o SPC seja recebido como um mensagem de postagem codificada www-form-url da seguinte forma:

```
spc=<Base64 encoded SPC>
```

## <a name="fairplay-configuration-net-example"></a>Exemplo .NET de configuração do FairPlay

É possível usar a API dos Serviços de Mídia para configurar as licenças do FairPlay. Quando o player tenta reproduzir o conteúdo protegido por FairPlay, uma solicitação é enviada ao serviço de entrega de licenças para obter a licença. Se o serviço de licença aprovar a solicitação, o serviço emitirá a licença. Ela é enviada ao cliente e é usada para descriptografar e reproduzir o conteúdo especificado.

> [!NOTE]
> Normalmente, convém configurar as opções de política do FairPlay apenas uma vez, visto que você terá apenas um conjunto de uma certificação e uma ASK.

O exemplo a seguir usa o [SDK do .NET dos Serviços de Mídia](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models?view=azure-dotnet) para configurar a licença.

```csharp
private static ContentKeyPolicyFairPlayConfiguration ConfigureFairPlayPolicyOptions()
{

    string askHex = "";
    string FairPlayPfxPassword = "";

    var appCert = new X509Certificate2("FairPlayPfxPath", FairPlayPfxPassword, X509KeyStorageFlags.Exportable);

    byte[] askBytes = Enumerable
        .Range(0, askHex.Length)
        .Where(x => x % 2 == 0)
        .Select(x => Convert.ToByte(askHex.Substring(x, 2), 16))
        .ToArray();

    ContentKeyPolicyFairPlayConfiguration fairPlayConfiguration =
    new ContentKeyPolicyFairPlayConfiguration
    {
        Ask = askBytes,
        FairPlayPfx =
                Convert.ToBase64String(appCert.Export(X509ContentType.Pfx, FairPlayPfxPassword)),
        FairPlayPfxPassword = FairPlayPfxPassword,
        RentalAndLeaseKeyType =
                ContentKeyPolicyFairPlayRentalAndLeaseKeyType
                .PersistentUnlimited,
        RentalDuration = 2249
    };

    return fairPlayConfiguration;
}
```

## <a name="next-steps"></a>Próximas etapas

Confira como [proteger com DRM](protect-with-drm.md)
