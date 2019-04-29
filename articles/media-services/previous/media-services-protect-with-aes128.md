---
title: Usar a criptografia dinâmica AES-128 e o serviço de distribuição de chaves | Microsoft Docs
description: Entregue o conteúdo criptografado com chaves de criptografia AES de 128 bits usando os Serviços de Mídia do Microsoft Azure. Os Serviços de Mídia também fornecem o serviço de distribuição de chaves, que distribui chaves de criptografia para usuários autorizados. Este tópico mostra como criptografar dinamicamente com o AES-128 e usar o serviço de distribuição de chaves.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 4d2c10af-9ee0-408f-899b-33fa4c1d89b9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: 6f76d6aed8dc5eed3dbf673b265c404f27b0536d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60557165"
---
# <a name="use-aes-128-dynamic-encryption-and-the-key-delivery-service"></a>Use a criptografia dinâmica AES-128 e o serviço de distrbuição de chaves
> [!div class="op_single_selector"]
> * [.NET](media-services-protect-with-aes128.md)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
>  

> [!NOTE]
> Não estão sendo adicionados novos recursos ou funcionalidades aos Serviços de Mídia v2. <br/>Confira a versão mais recente, [Serviços de Mídia v3](https://docs.microsoft.com/azure/media-services/latest/). Consulte também [diretrizes de migração da v2 para v3](../latest/migrate-from-v2-to-v3.md)

Você pode usar os Serviços de Mídia para a distribuição HTTP Live Streaming (HLS) e Smooth Streaming criptografado com o AES usando chaves de criptografia de 128 bits. Os Serviços de Mídia também fornecem o serviço de distribuição de chaves, que distribui chaves de criptografia para usuários autorizados. Se você desejar que os Serviços de Mídia criptografem um ativo, você associa uma chave de criptografia ao ativo e também configurar políticas de autorização para a chave. Quando um fluxo é solicitado por um player, os Serviços de Mídia usam a chave especificada para criptografar dinamicamente o conteúdo usando a criptografia AES. Para descriptografar o fluxo, o player solicita a chave do serviço de distribuição de chaves. Para determinar se o usuário está autorizado a obter a chave, o serviço avalia as políticas de autorização que você especificou para a chave.

Os serviços de mídia oferecem suporte a várias maneiras de autenticar os usuários que fazem solicitações de chave. A política de autorização da chave de conteúdo pode ter uma ou mais restrições de autorização: abertas ou de token. A política restrita do token deve ser acompanhada por um token emitido por um Serviço de Token de Segurança (STS). Os Serviços de Mídia oferecem suporte a tokens nos formatos [Simple Web Token](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2) (SWT) e [Token Web JSON](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (JWT). Para saber mais, veja [Configure a política de autorização da chave de conteúdo](media-services-protect-with-aes128.md#configure_key_auth_policy).

Para tirar proveito da criptografia dinâmica, você precisa ter um ativo que contenha um conjunto de arquivos MP4 com múltiplas taxas de bits ou arquivos de origem de Smooth Streaming com múltiplas taxas de bits. Você também precisa configurar a política de entrega para o ativo (descrita mais adiante neste artigo). Em seguida, com base no formato especificado na URL de streaming, o servidor de streaming sob demanda garante que você receba o fluxo no protocolo escolhido. Como resultado, você precisa armazenar e pagar por arquivos em um único formato de armazenamento. Os Serviços de Mídia criam e fornecem a resposta apropriada com base nas solicitações de um cliente.

Este artigo é útil para desenvolvedores que trabalham em aplicativos que distribuem mídia protegida. O artigo mostra como configurar o serviço de distribuição de chaves com políticas de autorização para que somente clientes autorizados possam receber as chaves de criptografia. Ele também mostra como usar criptografia dinâmica.

Para obter informações sobre como criptografar conteúdo com a criptografia AES para entrega para o Safari no macOS, consulte [esta postagem no blog](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).
Para ter uma visão geral de como proteger seu conteúdo de mídia com a criptografia AES, consulte [este vídeo](https://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-Protecting-your-Media-Content-with-AES-Encryption).


## <a name="aes-128-dynamic-encryption-and-key-delivery-service-workflow"></a>Criptografia dinâmica AES-128 e fluxo de trabalho de serviço de distribuição de chaves

Execute as seguintes etapas gerais ao criptografar seus ativos com AES usando o serviço de distribuição de chaves dos Serviços de Mídia, bem como usando a criptografia dinâmica:

1. [Crie um ativo e carregue os arquivos no ativo](media-services-protect-with-aes128.md#create_asset).

2. [Codifique o ativo que contém o arquivo para o conjunto de MP4 da taxa de bits adaptável](media-services-protect-with-aes128.md#encode_asset).

3. [Crie uma chave de conteúdo e associe-a ao ativo codificado](media-services-protect-with-aes128.md#create_contentkey). Nos Serviços de Mídia, a chave de conteúdo contém a chave de criptografia do ativo.

4. [Configure a política de autorização da chave de conteúdo](media-services-protect-with-aes128.md#configure_key_auth_policy). Você deve configurar a política de autorização da chave de conteúdo. O cliente deve estar em conformidade com a política antes de a chave de conteúdo ser entregue ao cliente.

5. [Configure a política de entrega para um ativo](media-services-protect-with-aes128.md#configure_asset_delivery_policy). A configuração de política de entrega inclui a URL de aquisição de chave e um vetor de inicialização (IV). (AES-128 exige o mesmo IV para criptografia e descriptografia). A configuração também inclui o protocolo de entrega (por exemplo, MPEG DASH, HLS, Smooth Streaming ou todos) e o tipo de criptografia dinâmica (por exemplo, envelope ou sem criptografia dinâmica).

    Você pode aplicar uma política diferente a cada protocolo no mesmo ativo. Por exemplo, você poderia aplicar a criptografia PlayReady a Smooth/DASH e aplicar Envelope de AES a HLS. Todos os protocolos que não estão definidos em uma política de entrega são impedidos de fazer transmissão por streaming. (Um exemplo é se você adicionar uma única política que especifica somente HLS como o protocolo). Só há exceção se você não tiver nenhuma política de entrega de ativos definida. Em seguida, todos os protocolos podem ser criptografados.

6. [Criar um localizador OnDemand](media-services-protect-with-aes128.md#create_locator) para obter uma URL de streaming.

O artigo também mostra [como um aplicativo cliente pode solicitar uma chave do serviço de distribuição de chaves](media-services-protect-with-aes128.md#client_request).

Você encontra um [exemplo de .NET](media-services-protect-with-aes128.md#example) completo no final do artigo.

A imagem a seguir demonstra o fluxo de trabalho descrito acima. Aqui, o token é usado para a autenticação.

![Proteger com o AES-128](./media/media-services-content-protection-overview/media-services-content-protection-with-aes.png)

O restante deste artigo fornece explicações, exemplos de código e links para tópicos que mostram como realizar as tarefas descritas anteriormente.

## <a name="current-limitations"></a>Limitações atuais
Se você adicionar ou atualizar a política de entrega do ativo, deverá excluir um localizador existente e criar um novo localizador.

## <a id="create_asset"></a>Criar um ativo e carregar arquivos no ativo
Para gerenciar, codificar e transmitir seus vídeos, você deve primeiro carregar o conteúdo nos Serviços de Mídia. Depois de carregado, seu conteúdo é armazenado com segurança na nuvem para processamento adicional e transmissão. 

Para mais informações, veja [Carregar arquivos em uma conta dos Serviços de Mídia](media-services-dotnet-upload-files.md).

## <a id="encode_asset"></a>Codificar o ativo que contém o arquivo para o conjunto de MP4 da taxa de bits adaptável
Com a criptografia dinâmica, você cria um ativo que contenha um conjunto de arquivos MP4 com múltiplas taxas de bits ou arquivos de origem de Smooth Streaming com múltiplas taxas de bits. Em seguida, com base no formato especificado na solicitação de fragmento ou de manifesto, o servidor de Streaming OnDemand garante que você receba o fluxo no protocolo escolhido por você. Depois, você só precisa armazenar e pagar por arquivos em um único formato de armazenamento. Os Serviços de Mídia criam e fornecem a resposta apropriada com base nas solicitações de um cliente. Para saber mais, confira [Visão geral do empacotamento dinâmico](media-services-dynamic-packaging-overview.md).

>[!NOTE]
>Quando sua conta dos Serviços de Mídia é criada, um ponto de extremidade de streaming padrão é adicionado à sua conta em estado "Parado". Para iniciar seu conteúdo de streaming e aproveitar o empacotamento dinâmico e a criptografia dinâmica, o ponto de extremidade de streaming do qual você deseja transmitir o conteúdo deve estar no estado "Executando". 
>
>Além disso, para usar o empacotamento dinâmico e a criptografia dinâmica, seu ativo deve conter um conjunto de arquivos MP4 de taxa de bits adaptável ou de Smooth Streaming de taxa de bits adaptável.

Para obter instruções sobre como codificar, veja [Codificar um ativo usando o Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md).

## <a id="create_contentkey"></a>Criar uma chave de conteúdo e associá-la ao ativo codificado
Nos Serviços de Mídia, a chave de conteúdo contém a chave com a qual você deseja criptografar um ativo.

Para saber mais, consute [Criar uma chave de conteúdo](media-services-dotnet-create-contentkey.md).

## <a id="configure_key_auth_policy"></a>Configurar a política de autorização da chave de conteúdo
Os serviços de mídia oferecem suporte a várias maneiras de autenticar os usuários que fazem solicitações de chave. Você deve configurar a política de autorização da chave de conteúdo. O cliente (player) deve estar em conformidade com a política antes de a chave poder ser entregue ao cliente. A política de autorização de chave de conteúdo pode ter uma ou mais restrições de autorização, aberta, restrição de token ou restrição de IP.

Para saber mais, veja [Configurar uma política de autorização da chave de conteúdo](media-services-dotnet-configure-content-key-auth-policy.md).

## <a id="configure_asset_delivery_policy"></a>Configurar uma política de entrega de ativos
Configure a política de entrega para seu ativo. Algumas coisas incluídas na configuração de política de entrega de ativos são:

* A URL de aquisição de chave. 
* O vetor de inicialização (IV) a ser usado para a criptografia de envelope. AES-128 exige o mesmo IV para criptografia e descriptografia. 
* O protocolo de entrega de ativos (por exemplo, MPEG DASH, HLS, Smooth Streaming ou todos).
* O tipo de criptografia dinâmica (por exemplo, envelope de AES) ou ausência de criptografia dinâmica. 

Para obter mais informações, consulte [Configurar a política de entrega de ativos](media-services-dotnet-configure-asset-delivery-policy.md).

## <a id="create_locator"></a>Criar um localizador de streaming OnDemand para obter uma URL de streaming
Você precisa fornecer ao seu usuário a URL de streaming para Smooth Streaming, DASH ou HLS.

> [!NOTE]
> Se você adicionar ou atualizar a política de entrega do ativo, deverá excluir um localizador existente e criar um novo localizador.
> 
> 

Para obter instruções sobre como publicar um ativo e criar uma URL de streaming, consulte [Criar uma URL de streaming](media-services-deliver-streaming-content.md).

## <a name="get-a-test-token"></a>Obter um token de teste
Obtenha um token de teste com base na restrição de token que foi usada para a política de autorização da chave.

```csharp
    // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
    // back into a TokenRestrictionTemplate class instance.
    TokenRestrictionTemplate tokenTemplate = 
        TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

    // Generate a test token based on the data in the given TokenRestrictionTemplate.
    //The GenerateTestToken method returns the token without the word "Bearer" in front
    //so you have to add it in front of the token string. 
    string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate);
    Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
```

Pode usar o [Azure Media Services Player](https://amsplayer.azurewebsites.net/azuremediaplayer.html) para testar seu fluxo.

## <a id="client_request"></a>Como seu cliente pode solicitar uma chave do serviço de distribuição de chaves?
Na etapa anterior, você construiu a URL que aponta para um arquivo de manifesto. O cliente precisa extrair as informações necessárias dos arquivos de manifesto de streaming para fazer uma solicitação para o serviço de distribuição de chaves.

### <a name="manifest-files"></a>Arquivos de manifesto
O cliente precisa extrair o valor da URL (que também contém a ID da chave de conteúdo [kid]) do arquivo de manifesto. O cliente então tenta obter a chave de criptografia por meio do serviço de distribuição de chaves. O cliente também precisa extrair o valor do IV e usá-lo para descriptografar o fluxo. O snippet de código a seguir mostra o elemento `<Protection>` do manifesto Smooth Streaming:

```xml
    <Protection>
      <ProtectionHeader SystemID="B47B251A-2409-4B42-958E-08DBAE7B4EE9">
        <ContentProtection xmlns:sea="urn:mpeg:dash:schema:sea:2012" schemeIdUri="urn:mpeg:dash:sea:2012">
          <sea:SegmentEncryption schemeIdUri="urn:mpeg:dash:sea:aes128-cbc:2013"/>
          <sea:KeySystem keySystemUri="urn:mpeg:dash:sea:keysys:http:2013"/>
          <sea:CryptoPeriod IV="0xD7D7D7D7D7D7D7D7D7D7D7D7D7D7D7D7" 
                            keyUriTemplate="https://wamsbayclus001kd-hs.cloudapp.net/HlsHandler.ashx?
                                            kid=da3813af-55e6-48e7-aa9f-a4d6031f7b4d"/>
        </ContentProtection>
      </ProtectionHeader>
    </Protection>
```

No caso de HLS, o manifesto raiz é dividido em arquivos de segmento. 

Por exemplo, o manifesto raiz é: http:\//test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/manifest(format=m3u8-aapl). Ele contém uma lista de nomes de arquivo de segmento.

    . . . 
    #EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=630133,RESOLUTION=424x240,CODECS="avc1.4d4015,mp4a.40.2",AUDIO="audio"
    QualityLevels(514369)/Manifest(video,format=m3u8-aapl)
    #EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=965441,RESOLUTION=636x356,CODECS="avc1.4d401e,mp4a.40.2",AUDIO="audio"
    QualityLevels(842459)/Manifest(video,format=m3u8-aapl)
    …

Se você abre um dos arquivos de segmento em um editor de texto (por exemplo, http:\//test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/QualityLevels(514369)/Manifest(video,format=m3u8-aapl), ele contém #EXT-X-KEY, que indica que o arquivo está criptografado.

    #EXTM3U
    #EXT-X-VERSION:4
    #EXT-X-ALLOW-CACHE:NO
    #EXT-X-MEDIA-SEQUENCE:0
    #EXT-X-TARGETDURATION:9
    #EXT-X-KEY:METHOD=AES-128,
    URI="https://wamsbayclus001kd-hs.cloudapp.net/HlsHandler.ashx?
         kid=da3813af-55e6-48e7-aa9f-a4d6031f7b4d",
            IV=0XD7D7D7D7D7D7D7D7D7D7D7D7D7D7D7D7
    #EXT-X-PROGRAM-DATE-TIME:1970-01-01T00:00:00.000+00:00
    #EXTINF:8.425708,no-desc
    Fragments(video=0,format=m3u8-aapl)
    #EXT-X-ENDLIST

>[!NOTE] 
>Se você pretende executar um HLS criptografado para AES no Safari, consulte [este blog](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

### <a name="request-the-key-from-the-key-delivery-service"></a>Solicitar a chave ao serviço de distribuição de chaves

O código a seguir mostra como enviar uma solicitação ao serviço de distribuição de chaves dos Serviços de Mídia usando um URI de distribuição de chaves (que foi extraído do manifesto) e um token. (Este artigo não explica como obter SWTs de um STS).

```csharp
    private byte[] GetDeliveryKey(Uri keyDeliveryUri, string token)
    {
        HttpWebRequest request = (HttpWebRequest)WebRequest.Create(keyDeliveryUri);

        request.Method = "POST";
        request.ContentType = "text/xml";
        if (!string.IsNullOrEmpty(token))
        {
            request.Headers[AuthorizationHeader] = token;
        }
        request.ContentLength = 0;

        var response = request.GetResponse();

        var stream = response.GetResponseStream();
        if (stream == null)
        {
            throw new NullReferenceException("Response stream is null");
        }

        var buffer = new byte[256];
        var length = 0;
        while (stream.CanRead && length <= buffer.Length)
        {
            var nexByte = stream.ReadByte();
            if (nexByte == -1)
            {
                break;
            }
            buffer[length] = (byte)nexByte;
            length++;
        }
        response.Close();

        // AES keys must be exactly 16 bytes (128 bits).
        var key = new byte[length];
        Array.Copy(buffer, key, length);
        return key;
    }
```

## <a name="protect-your-content-with-aes-128-by-using-net"></a>Proteger o conteúdo com o AES-128 usando o .NET

### <a name="create-and-configure-a-visual-studio-project"></a>Criar e configurar um projeto do Visual Studio

1. Configure seu ambiente de desenvolvimento e preencha o arquivo de configuração app.config com as informações de conexão, conforme descrito em [Desenvolvimento dos Serviços de Mídia com o .NET](media-services-dotnet-how-to-use.md).

2. Adicione os seguintes elementos para appSettings, conforme definidos no seu arquivo app.config:

    ```xml
    <add key="Issuer" value="http://testissuer.com"/>
    <add key="Audience" value="urn:test"/>
    ```

### <a id="example"></a>Exemplo

Substitua o código no seu arquivo Program.cs pelo código mostrado nesta seção.
 
>[!NOTE]
>Há um limite de 1 milhão de políticas para diferentes políticas dos Serviços de Mídia (por exemplo, para política de Localizador ou ContentKeyAuthorizationPolicy). Use a mesma ID de política se você sempre usa as mesmas permissões de acesso/dias. Podemos citar como exemplo as políticas de localizadores que devam permanecer em vigor por um longo período (políticas sem carregamento). Para obter mais informações, consulte a seção “Limitar políticas de acesso” na seção [Gerenciar ativos e entidades relacionadas ao SDK dos Serviços de Mídia .NET](media-services-dotnet-manage-entities.md#limit-access-policies).

Certifique-se de atualizar as variáveis para que indiquem as pastas onde estão localizados os arquivos de entrada.

[!code-csharp[Main](../../../samples-mediaservices-encryptionaes/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs)]

## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
