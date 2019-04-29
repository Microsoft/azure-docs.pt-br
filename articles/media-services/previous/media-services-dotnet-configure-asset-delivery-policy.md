---
title: Configurar políticas de entrega de ativos usando o SDK do .NET | Microsoft Docs
description: Este tópico mostra como configurar políticas de entrega de ativos diferentes com o SDK do .NET dos Serviços de Mídia do Azure.
services: media-services
documentationcenter: ''
author: Mingfeiy
manager: femila
editor: ''
ms.assetid: 3ec46f58-6cbb-4d49-bac6-1fd01a5a456b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: b5e733c93fef8920c73c8cf460dac7a7051fddb5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61465602"
---
# <a name="configure-asset-delivery-policies-with-net-sdk"></a>Configurar políticas de entrega de ativos usando o SDK do .NET
[!INCLUDE [media-services-selector-asset-delivery-policy](../../../includes/media-services-selector-asset-delivery-policy.md)]

## <a name="overview"></a>Visão geral
Se você planeja entregar ativos criptografados, uma das etapas do fluxo de trabalho de fornecimento de conteúdo de Serviços de Mídia é configurar políticas de entrega de ativos. A política de entrega de ativos informa aos serviços de mídia como você deseja que o ativo seja entregue: em que protocolo de fluxo seu ativo deve ser dinamicamente empacotado (por exemplo, MPEG DASH, HLS, Smooth Streaming ou todos), se você deseja criptografar dinamicamente seu ativo ou não e como (criptografia de envelope ou comum).

Este artigo discute como e por que criar e configurar políticas de entrega de ativos.

>[!NOTE]
>Quando sua conta AMS é criada, um ponto de extremidade de streaming **padrão** é adicionado à sua conta no estado **Parado**. Para iniciar seu conteúdo de streaming e tirar proveito do empacotamento dinâmico e da criptografia dinâmica, o ponto de extremidade de streaming do qual você deseja transmitir o conteúdo deve estar em estado **Executando**. 
>
>Além disso, para poder usar o empacotamento dinâmico e a criptografia dinâmica, seu ativo deve conter um conjunto de arquivos MP4 de taxa de bits adaptável ou de Smooth Streaming de taxa de bits adaptável.

Você pode aplicar políticas diferentes para o mesmo ativo. Por exemplo, você poderia aplicar criptografia PlayReady para criptografia de Envelope AES e Smooth Streaming para MPEG DASH e HLS. Todos os protocolos que não são definidos em uma política de entrega (por exemplo, você adicionar uma única política que só especifica HLS como o protocolo) será bloqueado a partir do streaming. Só há exceção se você não tiver nenhuma política de entrega de ativos definida. Em seguida, todos os protocolos poderão ser criptografados.

Se você quiser entregar um ativo de armazenamento criptografado, configure a política de entrega do ativo. Antes que seu ativo possa ser transmitido, o servidor de streaming remove a criptografia de armazenamento e transmite o conteúdo usando a política de entrega especificada. Por exemplo, para entregar o ativo criptografado com chave de criptografia de envelope de AES (Criptografia Avançada Padrão), defina o tipo de política como **DynamicEnvelopeEncryption**. Para remover a criptografia de armazenamento e transmitir o ativo não criptografado, defina o tipo de política como **NoDynamicEncryption**. Seguem exemplos que mostram como configurar esses tipos de política.

Dependendo de como você configura a política de entrega de ativos, é possível empacotar, criptografar e transmitir dinamicamente os seguintes protocolos de transmissão: Smooth Streaming, HLS e MPEG DASH.

A lista a seguir mostra os formatos usados para transmitir Smooth, HLS e DASH.

Smooth Streaming:

{nome do ponto de extremidade de streaming - nome de conta do dos serviços de mídia}.streaming.mediaservices.windows.net/{ID do localizador}/{nome do arqui}.ism/Manifest

HLS:

{nome do ponto de extremidade de streaming - nome de conta dos serviços de mídia}.streaming.mediaservices.windows.net/{ID do localizador}/{nome do arquivo}.ism/Manifest(format=m3u8-aapl)

MPEG DASH

{nome do ponto de extremidade de streaming - nome de conta dos serviços de mídia}.streaming.mediaservices.windows.net/{ID do localizador}/{nome do arquivo}.ism/Manifest(format=mpd-time-csf)

## <a name="considerations"></a>Considerações
* Antes de excluir o AssetDeliveryPolicy, você deverá excluir todos os localizadores de streaming associados ao ativo. Posteriormente, você poderá criar novos localizadores de streaming, se desejar, com um novo AssetDeliveryPolicy.
* Não é possível criar um localizador de streaming em um ativo criptografado para armazenamento quando nenhuma política de entrega de ativo estiver definida.  Se o Ativo não estiver criptografado para armazenamento, o sistema permitirá que você crie um localizador e transmita o ativo sem uma política de entrega de ativos.
* Você pode ter várias políticas de entrega de ativos associadas a um único ativo, mas pode especificar apenas uma maneira de lidar com um determinado AssetDeliveryProtocol.  Isso significa que se você tentar vincular duas políticas de entrega que especificam o protocolo AssetDeliveryProtocol.SmoothStreaming, o resultado será um erro, pois o sistema não sabe qual delas você desejará aplicar quando um cliente fizer uma solicitação do Smooth Streaming.
* Se você tiver um ativo com um localizador de streaming existente, não será possível vincular uma nova política ao ativo (você pode desvincular uma política existente do ativo ou atualizar uma política de entrega associada ao ativo).  Primeiramente, você precisa remover o localizador de streaming, ajustar as políticas e recriar o localizador de streaming.  Você pode usar o mesmo locatorId quando recriar o localizador de streaming, mas certifique-se de que isso não causará problemas para os clientes, uma vez que o conteúdo pode ser armazenado em cache pela CDN de origem ou downstream.

## <a name="clear-asset-delivery-policy"></a>Política de entrega de ativos clara

O método **ConfigureClearAssetDeliveryPolicy** a seguir especifica para não aplicar criptografia dinâmica nem entregar a transmissão em qualquer um dos seguintes protocolos:  Protocolos de MPEG DASH, HLS e Smooth Streaming. Você talvez queira aplicar essa política para seus ativos de armazenamento criptografados.

Para obter informações sobre os valores que você pode especificar ao criar um AssetDeliveryPolicy, consulte a seção [Tipos usados ao definir AssetDeliveryPolicy](#types) .

```csharp
    static public void ConfigureClearAssetDeliveryPolicy(IAsset asset)
    {
        IAssetDeliveryPolicy policy =
        _context.AssetDeliveryPolicies.Create("Clear Policy",
        AssetDeliveryPolicyType.NoDynamicEncryption,
        AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.Dash, null);
        
        asset.DeliveryPolicies.Add(policy);
    }
```
## <a name="dynamiccommonencryption-asset-delivery-policy"></a>Política de entrega de ativos DynamicCommonEncryption

O método **CreateAssetDeliveryPolicy** a seguir cria o **AssetDeliveryPolicy**, que é configurado para aplicar a criptografia comum dinâmica (**DynamicCommonEncryption**) a um protocolo de streaming suave (outros protocolos de streaming serão bloqueados no streaming). O método utiliza dois parâmetros: **Asset** (o ativo ao qual você deseja aplicar a política de entrega) e **IContentKey** (a chave de conteúdo do tipo **CommonEncryption**. Para saber mais, confira: [Criar uma chave de conteúdo](media-services-dotnet-create-contentkey.md#common_contentkey)).

Para obter informações sobre os valores que você pode especificar ao criar um AssetDeliveryPolicy, consulte a seção [Tipos usados ao definir AssetDeliveryPolicy](#types) .

```csharp
    static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {
        Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);
        
        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
                new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
            {
                {AssetDeliveryPolicyConfigurationKey.PlayReadyLicenseAcquisitionUrl, acquisitionUrl.ToString()},
            };
    
            var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                    "AssetDeliveryPolicy",
                AssetDeliveryPolicyType.DynamicCommonEncryption,
                AssetDeliveryProtocol.SmoothStreaming,
                assetDeliveryPolicyConfiguration);
    
            // Add AssetDelivery Policy to the asset
            asset.DeliveryPolicies.Add(assetDeliveryPolicy);
    
            Console.WriteLine();
            Console.WriteLine("Adding Asset Delivery Policy: " +
                assetDeliveryPolicy.AssetDeliveryPolicyType);
     }
```

Os Serviços de Mídia do Azure também permitem que você adicione criptografia Widevine. O exemplo a seguir demonstra o PlayReady e o Widevine sendo adicionados à política de fornecimento de ativos.

```csharp
    static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {
        // Get the PlayReady license service URL.
        Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);


        // GetKeyDeliveryUrl for Widevine attaches the KID to the URL.
        // For example: https://amsaccount1.keydelivery.mediaservices.windows.net/Widevine/?KID=268a6dcb-18c8-4648-8c95-f46429e4927c.  
        // The WidevineBaseLicenseAcquisitionUrl (used below) also tells Dynamic Encryption 
        // to append /? KID =< keyId > to the end of the url when creating the manifest.
        // As a result Widevine license acquisition URL will have KID appended twice, 
        // so we need to remove the KID that in the URL when we call GetKeyDeliveryUrl.

        Uri widevineUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.Widevine);
        UriBuilder uriBuilder = new UriBuilder(widevineUrl);
        uriBuilder.Query = String.Empty;
        widevineUrl = uriBuilder.Uri;

        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
        {
            {AssetDeliveryPolicyConfigurationKey.PlayReadyLicenseAcquisitionUrl, acquisitionUrl.ToString()},
            {AssetDeliveryPolicyConfigurationKey.WidevineLicenseAcquisitionUrl, widevineUrl.ToString()}

        };

        var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                "AssetDeliveryPolicy",
            AssetDeliveryPolicyType.DynamicCommonEncryption,
            AssetDeliveryProtocol.Dash,
            assetDeliveryPolicyConfiguration);


        // Add AssetDelivery Policy to the asset
        asset.DeliveryPolicies.Add(assetDeliveryPolicy);

    }
```
> [!NOTE]
> Ao criptografar com Widevine, você só seria capaz de fornecer usando um DASH. Certifique-se de especificar DASH no protocolo de fornecimento de ativos.
> 
> 

## <a name="dynamicenvelopeencryption-asset-delivery-policy"></a>Política de entrega de ativos DynamicEnvelopeEncryption
O método **CreateAssetDeliveryPolicy** a seguir cria o **AssetDeliveryPolicy** que é configurado para aplicar a criptografia de envelope dinâmico (**DynamicEnvelopeEncryption**) para Smooth Streaming, protocolos HLS e DASH (se você optar por não especificar outros protocolos, eles serão bloqueados do streaming). O método utiliza dois parâmetros: **Asset** (o ativo ao qual você deseja aplicar a política de entrega) e **IContentKey** (a chave de conteúdo do tipo **EnvelopeEncryption**. Para saber mais, confira: [Criar uma chave de conteúdo](media-services-dotnet-create-contentkey.md#envelope_contentkey)).

Para obter informações sobre os valores que você pode especificar ao criar um AssetDeliveryPolicy, consulte a seção [Tipos usados ao definir AssetDeliveryPolicy](#types) .   

```csharp
    private static void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {

        //  Get the Key Delivery Base Url by removing the Query parameter.  The Dynamic Encryption service will
        //  automatically add the correct key identifier to the url when it generates the Envelope encrypted content
        //  manifest.  Omitting the IV will also cause the Dynamic Encryption service to generate a deterministic
        //  IV for the content automatically.  By using the EnvelopeBaseKeyAcquisitionUrl and omitting the IV, this
        //  allows the AssetDelivery policy to be reused by more than one asset.
        //
        Uri keyAcquisitionUri = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.BaselineHttp);
        UriBuilder uriBuilder = new UriBuilder(keyAcquisitionUri);
        uriBuilder.Query = String.Empty;
        keyAcquisitionUri = uriBuilder.Uri;

        // The following policy configuration specifies: 
        //   key url that will have KID=<Guid> appended to the envelope and
        //   the Initialization Vector (IV) to use for the envelope encryption.
        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string> 
        {
            {AssetDeliveryPolicyConfigurationKey.EnvelopeBaseKeyAcquisitionUrl, keyAcquisitionUri.ToString()},
        };

        IAssetDeliveryPolicy assetDeliveryPolicy =
            _context.AssetDeliveryPolicies.Create(
                        "AssetDeliveryPolicy",
                        AssetDeliveryPolicyType.DynamicEnvelopeEncryption,
                        AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.Dash,
                        assetDeliveryPolicyConfiguration);

        // Add AssetDelivery Policy to the asset
        asset.DeliveryPolicies.Add(assetDeliveryPolicy);

        Console.WriteLine();
        Console.WriteLine("Adding Asset Delivery Policy: " + assetDeliveryPolicy.AssetDeliveryPolicyType);
    }
```

## <a id="types"></a>Tipos usados ao definir AssetDeliveryPolicy

### <a id="AssetDeliveryProtocol"></a>AssetDeliveryProtocol

O enum a seguir descreve os valores que podem ser definidos para o protocolo de entrega de ativo.

```csharp
    [Flags]
    public enum AssetDeliveryProtocol
    {
        /// <summary>
        /// No protocols.
        /// </summary>
        None = 0x0,

        /// <summary>
        /// Smooth streaming protocol.
        /// </summary>
        SmoothStreaming = 0x1,

        /// <summary>
        /// MPEG Dynamic Adaptive Streaming over HTTP (DASH)
        /// </summary>
        Dash = 0x2,

        /// <summary>
        /// Apple HTTP Live Streaming protocol.
        /// </summary>
        HLS = 0x4,

        ProgressiveDownload = 0x10, 
 
        /// <summary>
        /// Include all protocols.
        /// </summary>
        All = 0xFFFF
    }
```
### <a id="AssetDeliveryPolicyType"></a>AssetDeliveryPolicyType

O enum a seguir descreve os valores que podem ser definidos para o tipo de política de entrega de ativo.  
```csharp
    public enum AssetDeliveryPolicyType
    {
        /// <summary>
        /// Delivery Policy Type not set.  An invalid value.
        /// </summary>
        None,

        /// <summary>
        /// The Asset should not be delivered via this AssetDeliveryProtocol. 
        /// </summary>
        Blocked, 

        /// <summary>
        /// Do not apply dynamic encryption to the asset.
        /// </summary>
        /// 
        NoDynamicEncryption,  

        /// <summary>
        /// Apply Dynamic Envelope encryption.
        /// </summary>
        DynamicEnvelopeEncryption,

        /// <summary>
        /// Apply Dynamic Common encryption.
        /// </summary>
        DynamicCommonEncryption
        }
```
### <a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType

O enum a seguir descreve os valores que você pode usar para configurar o método de entrega da chave de conteúdo para o cliente.
  ```csharp  
    public enum ContentKeyDeliveryType
    {
        /// <summary>
        /// None.
        ///
        </summary>
        None = 0,

        /// <summary>
        /// Use PlayReady License acquisition protocol
        ///
        </summary>
        PlayReadyLicense = 1,

        /// <summary>
        /// Use MPEG Baseline HTTP key protocol.
        ///
        </summary>
        BaselineHttp = 2,

        /// <summary>
        /// Use Widevine License acquisition protocol
        ///
        </summary>
        Widevine = 3

    }
```
### <a id="AssetDeliveryPolicyConfigurationKey"></a>AssetDeliveryPolicyConfigurationKey

O enum a seguir descreve os valores que você pode definir para configurar as chaves usadas para obter uma configuração específica para uma política de entrega de ativo.
```csharp
    public enum AssetDeliveryPolicyConfigurationKey
    {
        /// <summary>
        /// No policies.
        /// </summary>
        None,

        /// <summary>
        /// Exact Envelope key URL.
        /// </summary>
        EnvelopeKeyAcquisitionUrl,

        /// <summary>
        /// Base key url that will have KID=<Guid> appended for Envelope.
        /// </summary>
        EnvelopeBaseKeyAcquisitionUrl,

        /// <summary>
        /// The initialization vector to use for envelope encryption in Base64 format.
        /// </summary>
        EnvelopeEncryptionIVAsBase64,

        /// <summary>
        /// The PlayReady License Acquisition Url to use for common encryption.
        /// </summary>
        PlayReadyLicenseAcquisitionUrl,

        /// <summary>
        /// The PlayReady Custom Attributes to add to the PlayReady Content Header
        /// </summary>
        PlayReadyCustomAttributes,

        /// <summary>
        /// The initialization vector to use for envelope encryption.
        /// </summary>
        EnvelopeEncryptionIV,

        /// <summary>
        /// Widevine DRM acquisition url
        /// </summary>
        WidevineLicenseAcquisitionUrl
    }
```
## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

