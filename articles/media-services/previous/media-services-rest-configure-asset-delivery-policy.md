---
title: Configuração de políticas de entrega de ativos usando API REST dos Serviços de Mídia | Microsoft Docs
description: Este tópico mostra como configurar políticas de entrega de ativos diferentes usando a API REST dos Serviços de Mídia.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 5cb9d32a-e68b-4585-aa82-58dded0691d0
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 5e4e565b0b5272de19458617a9c4bd3509907cce
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60817384"
---
# <a name="configuring-asset-delivery-policies"></a>Configuração de políticas de entrega de ativos
[!INCLUDE [media-services-selector-asset-delivery-policy](../../../includes/media-services-selector-asset-delivery-policy.md)]

Se você planeja entregar dinamicamente ativos criptografados, uma das etapas do fluxo de trabalho de distribuição de conteúdo de Serviços de Mídia é configurar políticas de entrega de ativos. A política de entrega de ativos informa aos serviços de mídia como você deseja que o ativo seja entregue: em que protocolo de fluxo seu ativo deve ser dinamicamente empacotado (por exemplo, MPEG DASH, HLS, Smooth Streaming ou todos), se você deseja criptografar dinamicamente seu ativo ou não e como (criptografia de envelope ou comum).

Este tópico discute como e por que criar e configurar políticas de entrega de ativos.

> [!NOTE]
> Quando sua conta AMS é criada, um ponto de extremidade de streaming **padrão** é adicionado à sua conta em estado **Parado**. Para iniciar seu conteúdo de streaming e tirar proveito do empacotamento dinâmico e da criptografia dinâmica, o ponto de extremidade de streaming do qual você deseja transmitir o conteúdo deve estar em estado **Executando**. 
>
> Além disso, para poder usar o empacotamento dinâmico e a criptografia dinâmica, seu ativo deve conter um conjunto de arquivos MP4 de taxa de bits adaptável ou de Smooth Streaming de taxa de bits adaptável.

Você pode aplicar políticas diferentes para o mesmo ativo. Por exemplo, você poderia aplicar criptografia PlayReady para criptografia de Envelope AES e Smooth Streaming para MPEG DASH e HLS. Todos os protocolos que não são definidos em uma política de entrega (por exemplo, você adicionar uma única política que só especifica HLS como o protocolo) será bloqueado a partir do streaming. A exceção a isso é se você não tiver nenhuma política de entrega de ativos definida em todos. Em seguida, todos os protocolos poderão ser criptografados.

Se você quiser entregar um ativo de armazenamento criptografado, configure a política de entrega do ativo. Antes que seu ativo possa ser transmitido, o servidor de streaming remove a criptografia de armazenamento e transmite o conteúdo usando a política de entrega especificada. Por exemplo, para entregar o ativo criptografado com chave de criptografia de envelope de AES (Criptografia Avançada Padrão), defina o tipo de política como **DynamicEnvelopeEncryption**. Para remover a criptografia de armazenamento e transmitir o ativo não criptografado, defina o tipo de política como **NoDynamicEncryption**. Seguem exemplos que mostram como configurar esses tipos de política.

Dependendo de como você configura a política de entrega de ativos, poderá empacotar e criptografar dinamicamente, bem como transmitir os seguintes protocolos de streaming: Transmissões Smooth Streaming, HLS e MPEG DASH.

A lista a seguir mostra os formatos usados para transmitir Smooth, HLS e DASH.

Smooth Streaming:

{nome do ponto de extremidade de streaming - nome de conta do dos serviços de mídia}.streaming.mediaservices.windows.net/{ID do localizador}/{nome do arqui}.ism/Manifest

HLS:

{nome do ponto de extremidade de streaming - nome de conta dos serviços de mídia}.streaming.mediaservices.windows.net/{ID do localizador}/{nome do arquivo}.ism/Manifest(format=m3u8-aapl)

MPEG DASH

{nome do ponto de extremidade de streaming - nome de conta dos serviços de mídia}.streaming.mediaservices.windows.net/{ID do localizador}/{nome do arquivo}.ism/Manifest(format=mpd-time-csf)


Para obter instruções sobre como publicar um ativo e criar uma URL de streaming, consulte [Criar uma URL de streaming](media-services-deliver-streaming-content.md).

## <a name="considerations"></a>Considerações
* Você não pode excluir um AssetDeliveryPolicy associado a um ativo enquanto um localizador OnDemand (streaming) existir para esse ativo. A recomendação é remover a política do ativo antes de excluir a política.
* Não é possível criar um localizador de streaming em um ativo criptografado para armazenamento quando nenhuma política de entrega de ativo estiver definida.  Se o Ativo não estiver criptografado para armazenamento, o sistema permitirá que você crie um localizador e transmita o ativo sem uma política de entrega de ativos.
* Você pode ter várias políticas de entrega de ativos associadas a um único ativo, mas pode especificar apenas uma maneira de lidar com um determinado AssetDeliveryProtocol.  Isso significa que se você tentar vincular duas políticas de entrega que especificam o protocolo AssetDeliveryProtocol.SmoothStreaming, o resultado será um erro pois o sistema não sabe qual delas você deseja aplicar quando um cliente fizer uma solicitação de Smooth Streaming.
* Se você tiver um ativo com um localizador de streaming existente, não poderá vincular uma nova política ao ativo, desvincular uma política existente do ativo ou atualizar uma política de entrega associada ao ativo.  Primeiro, você precisa remover o localizador de streaming, ajustar as políticas e, em seguida, recriar o localizador de streaming.  Você pode usar o mesmo locatorId quando recriar o localizador de streaming, mas certifique-se de que isso não causará problemas para os clientes uma vez que o conteúdo pode ser armazenado em cache pelo CDN de origem ou downstream.

> [!NOTE]
> 
> Ao acessar entidades nos serviços de mídia, você deve definir valores e campos de cabeçalho específicos nas suas solicitações HTTP. Para obter mais informações, consulte [Configuração para desenvolvimento da API REST dos Serviços de Mídia](media-services-rest-how-to-use.md).

## <a name="connect-to-media-services"></a>Conectar-se aos Serviços de Mídia

Para saber mais sobre como conectar-se à API do AMS, veja [Acessar a API dos Serviços de Mídia do Azure com a autenticação do Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="clear-asset-delivery-policy"></a>Política de entrega de ativos clara
### <a id="create_asset_delivery_policy"></a>Criar política de entrega de ativos
A solicitação HTTP a seguir cria uma política de entrega de ativos que especifica a não aplicação de criptografia dinâmica e a entrega do fluxo em qualquer um dos seguintes protocolos:  Protocolos de MPEG DASH, HLS e Smooth Streaming. 

Para obter informações sobre os valores que você pode especificar ao criar um AssetDeliveryPolicy, consulte a seção [Tipos usados ao definir AssetDeliveryPolicy](#types) .   

Solicitação:

    POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: 4651882c-d7ad-4d5e-86ab-f07f47dcb41e
    Host: media.windows.net

    {"Name":"Clear Policy",
    "AssetDeliveryProtocol":7,
    "AssetDeliveryPolicyType":2,
    "AssetDeliveryConfiguration":null}

Resposta:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 363
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3A92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd')
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: 4651882c-d7ad-4d5e-86ab-f07f47dcb41e
    request-id: 6aedbf93-4bc2-4586-8845-fd45590136af
    x-ms-request-id: 6aedbf93-4bc2-4586-8845-fd45590136af
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 08 Feb 2015 06:21:27 GMT

    {"odata.metadata":"https://media.windows.net/api/$metadata#AssetDeliveryPolicies/@Element",
    "Id":"nb:adpid:UUID:92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd",
    "Name":"Clear Policy",
    "AssetDeliveryProtocol":7,
    "AssetDeliveryPolicyType":2,
    "AssetDeliveryConfiguration":null,
    "Created":"2015-02-08T06:21:27.6908329Z",
    "LastModified":"2015-02-08T06:21:27.6908329Z"}

### <a id="link_asset_with_asset_delivery_policy"></a>Ativos de link com a política de entrega de ativos
A seguinte solicitação HTTP vincula o ativo especificado na política de entrega de ativos.

Solicitação:

    POST https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A86933344-9539-4d0c-be7d-f842458693e0')/$links/DeliveryPolicies HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Content-Type: application/json
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: 56d2763f-6e72-419d-ba3c-685f6db97e81
    Host: media.windows.net

    {"uri":"https://media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3A92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd')"}

Resposta:

    HTTP/1.1 204 No Content


## <a name="dynamicenvelopeencryption-asset-delivery-policy"></a>Política de entrega de ativos DynamicEnvelopeEncryption
### <a name="create-content-key-of-the-envelopeencryption-type-and-link-it-to-the-asset"></a>Cria chave de conteúdo do tipo EnvelopeEncryption e a vincula ao ativo
Ao especificar a política de entrega DynamicEnvelopeEncryption, você precisa certificar-se de vincular seu ativo a uma chave de conteúdo do tipo EnvelopeEncryption. Para obter mais informações, consulte: [Criar uma chave de conteúdo](media-services-rest-create-contentkey.md)).

### <a id="get_delivery_url"></a>Obter URL de entrega
Obter a URL de entrega para o método de entrega especificado da chave de conteúdo criado na etapa anterior. Um cliente usa a URL retornada para solicitar uma chave AES ou uma licença PlayReady a fim de reproduzir o conteúdo protegido.

Especifique o tipo da URL para obter no corpo da solicitação HTTP. Se você estiver protegendo o conteúdo com PlayReady, solicite uma URL de aquisição de licenças PlayReady dos Serviços de Mídia usando 1 para o keyDeliveryType: {"keyDeliveryType":1}. Se você estiver protegendo seu conteúdo com a criptografia de envelope, solicite uma URL de aquisição de chave especificando 2 para keyDeliveryType: {"keyDeliveryType":2}.

Solicitação:

    POST https://media.windows.net/api/ContentKeys('nb:kid:UUID:dc88f996-2859-4cf7-a279-c52a9d6b2f04')/GetKeyDeliveryUrl HTTP/1.1
    Content-Type: application/json
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: 569d4b7c-a446-4edc-b77c-9fb686083dd8
    Host: media.windows.net
    Content-Length: 21

    {"keyDeliveryType":2}

Resposta:

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 198
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: 569d4b7c-a446-4edc-b77c-9fb686083dd8
    request-id: d26f65d2-fe65-4136-8fcf-31545be68377
    x-ms-request-id: d26f65d2-fe65-4136-8fcf-31545be68377
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 08 Feb 2015 21:42:30 GMT

    {"odata.metadata":"media.windows.net/api/$metadata#Edm.String","value":"https://amsaccount1.keydelivery.mediaservices.windows.net/?KID=dc88f996-2859-4cf7-a279-c52a9d6b2f04"}


### <a name="create-asset-delivery-policy"></a>Criar política de entrega de ativos
A solicitação HTTP a seguir cria a **AssetDeliveryPolicy** que é configurada para aplicar a criptografia de envelope dinâmico (**DynamicEnvelopeEncryption**) para o protocolo **HLS** (neste exemplo, outros protocolos serão impedidos de realizar streaming). 

Para obter informações sobre os valores que você pode especificar ao criar um AssetDeliveryPolicy, consulte a seção [Tipos usados ao definir AssetDeliveryPolicy](#types) .   

Solicitação:

    POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
    Host: media.windows.net

    {"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":4,"AssetDeliveryPolicyType":3,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\\/\"}]"}


Resposta:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 460
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3Aec9b994e-672c-4a5b-8490-a464eeb7964b')
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
    request-id: c2a1ac0e-9644-474f-b38f-b9541c3a7c5f
    x-ms-request-id: c2a1ac0e-9644-474f-b38f-b9541c3a7c5f
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 09 Feb 2015 05:24:38 GMT

    {"odata.metadata":"media.windows.net/api/$metadata#AssetDeliveryPolicies/@Element","Id":"nb:adpid:UUID:ec9b994e-672c-4a5b-8490-a464eeb7964b","Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":4,"AssetDeliveryPolicyType":3,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\\/\"}]","Created":"2015-02-09T05:24:38.9167436Z","LastModified":"2015-02-09T05:24:38.9167436Z"}


### <a name="link-asset-with-asset-delivery-policy"></a>Ativos de link com a política de entrega de ativos
Confira [Ativos de link com a política de entrega de ativos](#link_asset_with_asset_delivery_policy)

## <a name="dynamiccommonencryption-asset-delivery-policy"></a>Política de entrega de ativos DynamicCommonEncryption
### <a name="create-content-key-of-the-commonencryption-type-and-link-it-to-the-asset"></a>Cria chave de conteúdo do tipo CommonEncryption e a vincule ao ativo
Ao especificar a política de entrega DynamicCommonEncryption, você precisa certificar-se de vincular seu ativo a uma chave de conteúdo do tipo CommonEncryption. Para obter mais informações, consulte: [Criar uma chave de conteúdo](media-services-rest-create-contentkey.md)).

### <a name="get-delivery-url"></a>Obter URL de entrega
Obter a URL de entrega para o método de entrega PlayReady da chave de conteúdo criada na etapa anterior. Um cliente usa a URL retornada para solicitar uma licença do PlayReady a fim de reproduzir o conteúdo protegido. Para saber mais, confira [Obter a URL de entrega](#get_delivery_url)

### <a name="create-asset-delivery-policy"></a>Criar política de entrega de ativos
A solicitação HTTP a seguir cria o **AssetDeliveryPolicy** que é configurado para aplicar a criptografia comum dinâmica (**DynamicCommonEncryption**) para o protocolo **Smooth Streaming** (neste exemplo, outros protocolos serão impedidos de realizar streaming). 

Para obter informações sobre os valores que você pode especificar ao criar um AssetDeliveryPolicy, consulte a seção [Tipos usados ao definir AssetDeliveryPolicy](#types) .   

Solicitação:

    POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
    Host: media.windows.net

    {"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":1,"AssetDeliveryPolicyType":4,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\/PlayReady\/"}]"}


Se você deseja proteger o conteúdo usando Widevine DRM, atualize os valores de AssetDeliveryConfiguration para usar WidevineLicenseAcquisitionUrl (que tem o valor de 7) e especifique a URL de um serviço de fornecimento de licença. Você pode usar os seguintes parceiros do AMS para ajudá-lo a fornecer licenças Widevine: [Axinom](https://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](https://ezdrm.com/), [castLabs](https://castlabs.com/company/partners/azure/).

Por exemplo:  

    {"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":2,"AssetDeliveryPolicyType":4,"AssetDeliveryConfiguration":"[{\"Key\":7,\"Value\":\"https:\\/\\/example.net\/WidevineLicenseAcquisition\/"}]"}

> [!NOTE]
> Ao criptografar com Widevine, você só seria capaz de fornecer usando um DASH. Certifique-se de especificar DASH (2) no protocolo de fornecimento de ativos.
> 
> 

### <a name="link-asset-with-asset-delivery-policy"></a>Ativos de link com a política de entrega de ativos
Confira [Ativos de link com a política de entrega de ativos](#link_asset_with_asset_delivery_policy)

## <a id="types"></a>Tipos usados ao definir AssetDeliveryPolicy

### <a name="assetdeliveryprotocol"></a>AssetDeliveryProtocol

O enum a seguir descreve os valores que podem ser definidos para o protocolo de entrega de ativo.

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

### <a name="assetdeliverypolicytype"></a>AssetDeliveryPolicyType

O enum a seguir descreve os valores que podem ser definidos para o tipo de política de entrega de ativo.  

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

### <a name="contentkeydeliverytype"></a>ContentKeyDeliveryType

O enum a seguir descreve os valores que você pode usar para configurar o método de entrega da chave de conteúdo para o cliente.
    
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


### <a name="assetdeliverypolicyconfigurationkey"></a>AssetDeliveryPolicyConfigurationKey

O enum a seguir descreve os valores que você pode definir para configurar as chaves usadas para obter uma configuração específica para uma política de entrega de ativo.

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

## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

