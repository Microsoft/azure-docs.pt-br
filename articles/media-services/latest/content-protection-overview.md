---
title: Proteger seu conteúdo usando a criptografia dinâmica dos serviços de mídia – Azure | Microsoft Docs
description: Este artigo fornece uma visão geral da proteção de conteúdo com criptografia dinâmica. Ele também fala sobre protocolos de streaming e tipos de criptografia.
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
ms.date: 07/17/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 174184993e40b60dc89022d360f0c09fb31bc60b
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68501275"
---
# <a name="protect-your-content-by-using-media-services-dynamic-encryption"></a>Proteger seu conteúdo usando a criptografia dinâmica dos serviços de mídia

Você pode usar os serviços de mídia do Azure para ajudar a proteger sua mídia desde o momento em que ele deixa o computador por meio de armazenamento, processamento e entrega. Com os Serviços de Mídia, é possível entregar o conteúdo ao vivo e sob demanda criptografado dinamicamente com a criptografia AES (AES-128) ou qualquer um dos três principais sistemas DRM (gerenciamento de direitos digitais): Microsoft PlayReady, Google Widevine e Apple FairPlay. Os serviços de mídia também fornecem um serviço de distribuição de chaves AES e licenças DRM (PlayReady, Widevine e FairPlay) para os clientes autorizados.  

No Media Services V3, uma chave de conteúdo é associada ao localizador de streaming (consulte [Este exemplo](protect-with-aes128.md)). Se estiver usando o serviço de distribuição de chaves dos serviços de mídia, você poderá permitir que os serviços de mídia do Azure gerem a chave de conteúdo para você. Você mesmo deve gerar a chave de conteúdo se estiver usando o serviço de distribuição de chaves próprio, ou se precisar lidar com um cenário de alta disponibilidade em que você precisa ter a mesma chave de conteúdo em dois data centers.

Quando um fluxo é solicitado por um player, os Serviços de Mídia usam a chave especificada para criptografar dinamicamente o conteúdo usando a chave não criptografada AES ou a criptografia DRM. Para descriptografar o fluxo, o player solicita a chave do serviço de entrega de chave dos Serviços de Mídia ou do serviço de entrega de chaves especificado. Para decidir se o usuário está autorizado ou não a obter a chave, o serviço avalia a política de chave de conteúdo que você especificou para a chave.

Você pode usar o API REST ou biblioteca de cliente de Serviços de Mídia para configurar políticas de autenticação e autorização para suas licenças e chaves.

A imagem a seguir ilustra o fluxo de trabalho para proteção de conteúdo dos serviços de mídia:

![Fluxo de trabalho para proteção de conteúdo dos serviços de mídia](./media/content-protection/content-protection.svg)
  
&#42;*A criptografia dinâmica dá suporte ao AES-128 Clear Key, CBCs e Cenc. Para obter detalhes, consulte a [matriz de suporte](#streaming-protocols-and-encryption-types).*

Este artigo explica os conceitos e a terminologia que o ajudarão a entender a proteção de conteúdo com os serviços de mídia.

## <a name="main-components-of-a-content-protection-system"></a>Componentes principais de um sistema de proteção de conteúdo

Para concluir com êxito o seu sistema de proteção de conteúdo, você precisa compreender totalmente o escopo do esforço. As seções a seguir fornecem uma visão geral de três partes que você precisa implementar. 

> [!NOTE]
> É altamente recomendável que você focalize e teste completamente cada parte das seções a seguir antes de passar para a próxima parte. Para testar seu sistema de proteção de conteúdo, use as ferramentas especificadas nas seções.

### <a name="media-services-code"></a>Código dos serviços de mídia
  
O [exemplo de DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs) mostra como implementar um sistema de vários DRM com os serviços de mídia v3 usando o .net. Ele também mostra como usar o serviço de distribuição de chaves/licença dos serviços de mídia.   
  
Você pode criptografar cada ativo com vários tipos de criptografia (AES-128, PlayReady, Widevine, FairPlay). Para ver o que faz sentido combinar, consulte [protocolos de streaming e tipos de criptografia](#streaming-protocols-and-encryption-types).

O exemplo mostra como:

1. Criar e configurar uma [política de chave de conteúdo](content-key-policy-concept.md).    

   Você cria uma política de chave de conteúdo para configurar como a chave de conteúdo (que fornece acesso seguro aos seus ativos) é entregue aos clientes finais:  
 
   * Defina a autorização de entrega de licença. Especifique a lógica da verificação de autorização com base em declarações no token Web JSON (JWT).
   * Configure as licenças [PlayReady](playready-license-template-overview.md), [Widevine](widevine-license-template-overview.md)e/ou [Fairplay](fairplay-license-overview.md) . Os modelos permitem que você configure direitos e permissões para cada um dos DRMs.

     ```
     ContentKeyPolicyPlayReadyConfiguration playReadyConfig = ConfigurePlayReadyLicenseTemplate();
     ContentKeyPolicyWidevineConfiguration widevineConfig = ConfigureWidevineLicenseTempate();
     ContentKeyPolicyFairPlayConfiguration fairPlayConfig = ConfigureFairPlayPolicyOptions();
     ```
2. Crie um [localizador de streaming](streaming-locators-concept.md) configurado para transmitir o ativo criptografado. 
  
   O localizador de streaming deve ser associado a uma [política de streaming](streaming-policy-concept.md). No exemplo, definimos `StreamingLocator.StreamingPolicyName` a política "Predefined_MultiDrmCencStreaming". 
      
   As criptografias PlayReady e Widevine são aplicadas e a chave é entregue ao cliente de reprodução com base nas licenças DRM configuradas. Se você também quiser criptografar seu fluxo com CBCS (FairPlay), use a política "Predefined_MultiDrmStreaming".

   O localizador de streaming também está associado à política de chave de conteúdo que você definiu.
3. Crie um token de teste.

   O `GetTokenAsync` método mostra como criar um token de teste.
4. Crie a URL de streaming.

   O `GetDASHStreamingUrlAsync` método mostra como criar a URL de streaming. Nesse caso, a URL transmite o conteúdo do traço.

### <a name="player-with-an-aes-or-drm-client"></a>Player com um cliente AES ou DRM 

Um aplicativo de player de vídeo baseado em um player SDK (nativo ou baseado em navegador) precisa atender aos seguintes requisitos:

* O SDK do Player dá suporte aos clientes DRM necessários.
* O SDK de player suporta os protocolos de streaming necessários: Smooth, DASH e/ou HLS.
* O SDK do player pode lidar com a passagem de um token JWT em uma solicitação de aquisição de licença.

Você consegue criar um player usando a [API do Player de Mídia do Azure](https://amp.azure.net/libs/amp/latest/docs/). Use a [API ProtectionInfo do Player de Mídia do Azure](https://amp.azure.net/libs/amp/latest/docs/) para especificar qual tecnologia de DRM usar em diferentes plataformas de DRM.

Para testar conteúdo criptografado AES ou CENC (Widevine e/ou PlayReady), é possível usar o [Player de Mídia do Azure](https://aka.ms/azuremediaplayer). Certifique-se de selecionar **Opções avançadas** e verifique suas opções de criptografia.

Se você quiser testar o conteúdo do FairPlay criptografado, use [esse teste player](https://aka.ms/amtest). O Player dá suporte a Widevine, PlayReady e FairPlay DRMs, juntamente com criptografia de chave não criptografada AES-128. 

Escolha o navegador certo para testar diferentes DRMs:

* Chrome, Opera ou Firefox para Widevine
* Microsoft Edge ou Internet Explorer 11 para PlayReady
* Safari no macOS para FairPlay

### <a name="security-token-service"></a>Serviço de token de segurança

Um serviço de token de segurança (STS) emite JWT como o token de acesso para acesso a recursos de back-end. Você pode usar o serviço de entrega de chave/licença dos serviços de mídia do Azure como o recurso de back-end. Um STS deve definir o seguinte:

* Emissor e público-alvo (ou escopo)
* Declarações, que são dependentes nos requisitos de negócios na proteção de conteúdo
* Verificação simétrica ou assimétrica para verificação de assinatura
* Suporte de substituição de chave (se necessário)

Você pode usar [essa ferramenta STS](https://openidconnectweb.azurewebsites.net/DRMTool/Jwt) para testar o STS. Ele dá suporte a todos os três tipos de chaves de verificação: simétrico, assimétrico ou Azure Active Directory (Azure AD) com substituição de chave. 

## <a name="streaming-protocols-and-encryption-types"></a>Tipos de criptografia e protocolos de streaming

É possível usar os Serviços de Mídia para entregar conteúdo criptografado dinamicamente com chave não criptografada AES ou criptografia DRM usando o PlayReady, o Widevine ou o FairPlay. No momento, você pode criptografar os formatos HLS (HTTP Live Streaming), MPEG DASH e Smooth Streaming. Cada protocolo dá suporte aos seguintes métodos de criptografia.

### <a name="hls"></a>HLS

O protocolo HLS dá suporte aos seguintes formatos de contêiner e esquemas de criptografia.

|Formato de contêiner|Esquema de criptografia|Exemplo de URL|
|---|---|---|
|Todas|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cbc)`|
|MPG2-TS |CBCS (FairPlay) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cbcs-aapl)`|
|CMAF(fmp4) |CBCS (FairPlay) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`|
|MPG2-TS |CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cenc)`|
|CMAF(fmp4) |CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-cmaf,encryption=cenc)`|

HLS/CMAF + FairPlay (incluindo HEVC/H. 265) tem suporte nos seguintes dispositivos:

* iOS 11 ou posterior 
* iPhone 8 ou posterior
* MacOS High Sierra com CPU Intel 7 geração

### <a name="mpeg-dash"></a>MPEG-DASH

O protocolo MPEG-DASH dá suporte aos seguintes formatos de contêiner e esquemas de criptografia.

|Formato de contêiner|Esquema de criptografia|Exemplos de URL
|---|---|---|
|Todas|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-csf,encryption=cbc)`|
|CSF(fmp4) |CENC (Widevine + PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-csf,encryption=cenc)`|
|CMAF(fmp4)|CENC (Widevine + PlayReady)|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-cmaf,encryption=cenc)`|

### <a name="smooth-streaming"></a>Smooth Streaming

O protocolo Smooth Streaming dá suporte aos seguintes formatos de contêiner e esquemas de criptografia.

|Protocol|Formato de contêiner|Esquema de criptografia|
|---|---|---|
|fMP4|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=cbc)`|
|fMP4 | CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=cenc)`|

### <a name="browsers"></a>Navegadores

Os navegadores comuns dão suporte aos seguintes clientes DRM:

|Navegador|Criptografia|
|---|---|
|Chrome|Widevine|
|Microsoft Edge, Internet Explorer 11|PlayReady|
|Firefox|Widevine|
|Opera|Widevine|
|Safari|FairPlay|

## <a name="controlling-content-access"></a>Controlando o acesso ao conteúdo

É possível controlar quem tem acesso ao seu conteúdo, configurando a política de chave de conteúdo. Os serviços de mídia oferecem suporte a várias maneiras de autorizar os usuários que fazem solicitações de chave. Você deve configurar a política de chave de conteúdo. O cliente (player) deve estar em conformidade com a política antes de a chave poder ser entregue ao cliente. A política de chave de conteúdo pode ter *abra* ou *token* restrição. 

Uma política de chave de conteúdo com restrição aberta pode ser usada quando você deseja emitir a licença para qualquer pessoa sem autorização. Por exemplo, se sua receita for baseada no AD e não for baseada em assinatura.  

Com uma política de chave de conteúdo restrita por token, a chave de conteúdo é enviada somente para um cliente que apresenta um token JWT válido ou um token Web simples na solicitação de licença/chave. Esse token deve ser emitido por um STS. 

Você pode usar o Azure AD como um STS ou implantar um STS personalizado. O STS deve ser configurado para criar um token assinado com as a chave especificada e declarações de emissão que você especificou na configuração de restrição do token. O serviço de distribuição de chaves/licença dos serviços de mídia retorna a licença ou chave solicitada ao cliente se ambas as condições existirem:

* O token é válido. 
* As declarações no token correspondem àquelas configuradas para a licença ou chave.

Ao configurar a política restrita do token, você deve especificar os parâmetros de chave de verificação primária, emissor e público. A chave de verificação primária contém a chave com a qual o token foi assinado. O emissor é o STS que emite o token. O público, às vezes chamado de escopo, descreve a intenção do token ou o recurso ao qual o token autoriza o acesso. O serviço de distribuição de chaves/licença dos serviços de mídia valida que esses valores no token correspondem aos valores no modelo.

### <a name="token-replay-prevention"></a>Prevenção de reprodução de token

O recurso de *prevenção de reprodução de token* permite que os clientes dos serviços de mídia definam um limite de quantas vezes o mesmo token pode ser usado para solicitar uma chave ou uma licença. O cliente pode adicionar uma declaração do tipo `urn:microsoft:azure:mediaservices:maxuses` no token, em que o valor é o número de vezes que o token pode ser usado para adquirir uma licença ou chave. Todas as solicitações subsequentes com o mesmo token para a distribuição de chaves retornarão uma resposta não autorizada. Consulte Como adicionar a declaração no exemplo de [DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L601).
 
#### <a name="considerations"></a>Considerações

* Os clientes devem ter controle sobre a geração de tokens. A declaração precisa ser colocada no próprio token.
* Ao usar esse recurso, as solicitações com tokens cuja hora de expiração é mais de uma hora longe da hora em que a solicitação é recebida são rejeitadas com uma resposta não autorizada.
* Os tokens são identificados exclusivamente por sua assinatura. Qualquer alteração na carga (por exemplo, atualizar para a hora de expiração ou a declaração) altera a assinatura do token e ela conta como um novo token que a entrega de chave não foi encontrada antes.
* A reprodução falhará se o token exceder `maxuses` o valor definido pelo cliente.
* Esse recurso pode ser usado para todo o conteúdo protegido existente (somente o token emitido precisa ser alterado).
* Esse recurso funciona com o JWT e o SWT.

## <a name="using-a-custom-sts"></a>Usando um STS personalizado

Um cliente pode optar por usar um STS personalizado para fornecer tokens. Os motivos incluem:

* O IDP usado pelo cliente não é compatível com o STS. Nesse caso, um STS personalizado poderá ser uma opção.
* O cliente poderá precisar de controle mais flexível ou mais restrito na integração do STS com o sistema de cobrança de assinante do cliente. Por exemplo, um operador de MVPD poderá oferecer vários pacotes de assinante OTT, como Premium, básico e esportes. O operador poderá corresponder as declarações de um token com o pacote do assinante para que somente o conteúdo de um pacote específico seja disponibilizado. Nesse caso, um STS personalizado oferece a flexibilidade e o controle necessários.
* Para incluir declarações personalizadas no token para selecionar entre diferentes ContentKeyPolicyOptions com parâmetros de licença de DRM diferentes (uma licença de assinatura em vez de uma licença de aluguel).
* Para incluir uma declaração que representa o identificador de chave de conteúdo da chave à qual o token concede acesso.

Ao usar um STS personalizado, duas alterações devem ser feitas:

* Ao configurar o serviço de entrega de licença para um ativo, você precisa especificar a chave de segurança usada para verificação pelo STS personalizado em vez da chave atual do Azure AD.
* Quando um token JTW é gerado, uma chave de segurança é especificada em vez da chave privada do certificado X509 atual no Azure AD.

Há dois tipos de chaves de segurança:

* Chave simétrica: a mesma chave é usada para gerar e verificar um JWT.
* Chave assimétrica: um par de chaves pública/privada em um certificado X509 é usado com uma chave privada para criptografar/gerar um JWT e, com a chave pública, para verificar o token.

Se você usa o .NET Framework /C# como sua plataforma de desenvolvimento, o certificado X509 usado para uma chave assimétrica de segurança deve ter um comprimento de chave de pelo menos 2048. Este é um requisito da classe System.IdentityModel.Tokens.X509AsymmetricSecurityKey no .NET Framework. Caso contrário, a seguinte exceção é lançada: IDX10630: 'System.IdentityModel.Tokens.X509AsymmetricSecurityKey' para a assinatura não pode ter menos de '2048' bits.

## <a name="custom-key-and-license-acquisition-url"></a>URL de aquisição de licença e chave personalizada

Use os modelos a seguir se desejar especificar um serviço de entrega de chave/licença diferente (não serviços de mídia). Os dois campos substituíveis nos modelos existem para que você possa compartilhar sua política de streaming em vários ativos, em vez de criar uma política de streaming por ativo. 

* `EnvelopeEncryption.CustomKeyAcquisitionUrlTemplate`: Modelo para a URL do serviço personalizado que fornece chaves para os players do usuário final. Ele não é necessário quando você está usando os serviços de mídia do Azure para emitir chaves. 

   O modelo oferece suporte a tokens substituíveis que o serviço atualizará em tempo de execução com o valor específico para a solicitação.  Os valores de token com suporte no momento são:
   * `{AlternativeMediaId}`, que é substituído pelo valor de StreamingLocatorId. AlternativeMediaId.
   * `{ContentKeyId}`, que é substituído pelo valor do identificador da chave solicitada.
* `StreamingPolicyPlayReadyConfiguration.CustomLicenseAcquisitionUrlTemplate`: Modelo para a URL do serviço personalizado que entrega licenças para players do usuário final. Ele não é necessário quando você está usando os serviços de mídia do Azure para emitir licenças. 

   O modelo oferece suporte a tokens substituíveis que o serviço atualizará em tempo de execução com o valor específico para a solicitação. Os valores de token com suporte no momento são:  
   * `{AlternativeMediaId}`, que é substituído pelo valor de StreamingLocatorId. AlternativeMediaId.
   * `{ContentKeyId}`, que é substituído pelo valor do identificador da chave solicitada. 
* `StreamingPolicyWidevineConfiguration.CustomLicenseAcquisitionUrlTemplate`: O mesmo que o modelo anterior, somente para Widevine. 
* `StreamingPolicyFairPlayConfiguration.CustomLicenseAcquisitionUrlTemplate`: O mesmo que o modelo anterior, somente para FairPlay.  

Por exemplo:

```csharp
streamingPolicy.EnvelopEncryption.customKeyAcquisitionUrlTemplate = "https://mykeyserver.hostname.com/envelopekey/{AlternativeMediaId}/{ContentKeyId}";
```

`ContentKeyId`tem um valor da chave solicitada. Você pode usar `AlternativeMediaId` se quiser mapear a solicitação para uma entidade no seu lado. Por exemplo, `AlternativeMediaId` pode ser usado para ajudá-lo a Pesquisar permissões.

 Para obter exemplos de REST que usam URLs de licença/chave personalizadas, consulte [políticas de streaming – criar](https://docs.microsoft.com/rest/api/media/streamingpolicies/create).

## <a name="troubleshoot"></a>Solução de problemas

Se você receber o `MPE_ENC_ENCRYPTION_NOT_SET_IN_DELIVERY_POLICY` erro, certifique-se de especificar a política de streaming apropriada.

Se você receber erros que terminam `_NOT_SPECIFIED_IN_URL`com o, certifique-se de especificar o formato de criptografia na URL. Um exemplo é `…/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`. Consulte [protocolos de streaming e tipos de criptografia](#streaming-protocols-and-encryption-types).

## <a name="ask-questions-give-feedback-get-updates"></a>Fazer perguntas, comentar, obter atualizações

Confira o artigo [comunidade dos Serviços de Mídia do Azure](media-services-community.md) para ver diferentes maneiras de fazer perguntas, comentários e obter atualizações sobre os serviços de mídia.

## <a name="next-steps"></a>Próximas etapas

* [Proteger com criptografia AES](protect-with-aes128.md)
* [Proteger com DRM](protect-with-drm.md)
* [Projetar o sistema de proteção de conteúdo de vários DRM com o controle de acesso](design-multi-drm-system-with-access-control.md)
* [Criptografia do lado do armazenamento](storage-account-concept.md#storage-side-encryption)
* [Perguntas frequentes](frequently-asked-questions.md)

