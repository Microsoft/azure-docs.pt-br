---
title: Visão geral do modelo de licença Widevine dos serviços de mídia v3
description: Saiba mais sobre os serviços de mídia do Azure com o modelo de licença do Widevine e como ele é usado para configurar licenças do Widevine.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 9ef45e804b593f36171907395c564c8c6058c286
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2021
ms.locfileid: "102453726"
---
# <a name="media-services-v3-with-widevine-license-template-overview"></a>Visão geral do modelo de licença do Media Services V3 com Widevine

Os Serviços de Mídia do Azure permitem criptografar o conteúdo com **Google Widevine**. Os Serviços de Mídia também fornecem um serviço para fornecer licenças Widevine. É possível usar APIs dos Serviços de Mídia do Azure para configurar licenças Widevine. Quando um player tenta reproduzir o conteúdo protegido por Widevine, uma solicitação é enviada ao serviço de entrega de licenças para obter a licença. Se o serviço de licença aprovar a solicitação, o serviço emitirá a licença. Ela é enviada ao cliente e é usada para descriptografar e reproduzir o conteúdo especificado.

[!INCLUDE [Widevine is not available in the GovCloud region.](./includes/widevine-not-available-govcloud.md)]

A solicitação de licença do Widevine é formatada como uma mensagem JSON.  



```json
{  
    "payload":"<license challenge>",
    "content_id": "<content id>"
    "provider": "<provider>"
    "allowed_track_types":"<types>",
    "content_key_specs":[  
        {  
            "track_type":"<track type 1>"
        },
        {  
            "track_type":"<track type 2>"
        },
        …
    ],
    "policy_overrides":{  
        "can_play":<can play>,
        "can persist":<can persist>,
        "can_renew":<can renew>,
        "rental_duration_seconds":<rental duration>,
        "playback_duration_seconds":<playback duration>,
        "license_duration_seconds":<license duration>,
        "renewal_recovery_duration_seconds":<renewal recovery duration>,
        "renewal_server_url":"<renewal server url>",
        "renewal_delay_seconds":<renewal delay>,
        "renewal_retry_interval_seconds":<renewal retry interval>,
        "renew_with_usage":<renew with usage>
    }
}
```

>[!NOTE]
> Você pode criar uma mensagem vazia sem valores, apenas "{}". Em seguida, é criado um modelo de licença com os padrões. O padrão funciona na maioria dos casos. Cenários de entrega de licença baseada na Microsoft sempre devem usar os padrões. Se você precisar definir os valores de "content_id" e "provedor", um provedor deverá corresponder às credenciais de Widevine.

## <a name="json-message"></a>Mensagem JSON

| Nome | Valor | Descrição |
| --- | --- | --- |
| payload |Cadeia de caracteres codificada em Base64 |A solicitação de licença enviada por um cliente. |
| content_id |Cadeia de caracteres codificada em Base64 |Identificador usado para gerar a(s) ID(s) de chave e chaves de conteúdo para cada content_key_specs.track_type. |
| provider |string |Usado para pesquisar as políticas e chaves de conteúdo. Se a distribuição de chaves da Microsoft é usada para entrega de licença do Widevine, esse parâmetro é ignorado. |
| policy_name |string |Nome de uma política registrada anteriormente. Opcional. |
| allowed_track_types |enum |SD_ONLY ou SD_HD. Controla quais chaves de conteúdo são incluídas em uma licença. |
| content_key_specs |Matriz de estruturas JSON, confira a seção “Especificações de chave de conteúdo”.  |Um controle mais refinado sobre quais chaves de conteúdo retornar. Para obter mais informações, consulte a seção "Especificações de chave de conteúdo". Apenas um dos valores allowed_track_types e content_key_specs pode ser especificado. |
| use_policy_overrides_exclusively |Booliano, true ou false |Use os atributos de política especificados por policy_overrides e omita todas as políticas armazenadas anteriormente. |
| policy_overrides |Estrutura JSON, confira a seção “Substituições de política”. |Configurações de política para esta licença.  Caso este ativo tenha uma política predefinida, esses valores especificados serão usados. |
| session_init |Estrutura JSON, confira a seção "Inicialização da sessão". |Dados opcionais são passados para a licença. |
| parse_only |Booliano, true ou false |A solicitação de licença é analisada, mas nenhuma licença é emitida. No entanto, os valores da solicitação de licença retornam na resposta. |

## <a name="content-key-specs"></a>Especificações de chave de conteúdo
Se houver uma política pré-existente, não será necessário especificar nenhum dos valores na especificação de chave de conteúdo. A política pré-existente associada a esse conteúdo é usada para determinar a proteção de saída, como o Proteção de Conteúdo Digital de alta largura de banda (HDCP) e o sistema de gerenciamento geral de cópia (CGMS). Se uma política pré-existente não estiver registrada no servidor de licenças Widevine, o provedor de conteúdo poderá injetar os valores na solicitação de licença.   

Cada valor content_key_specs deve ser especificado para todos os controles, independentemente da opção use_policy_overrides_exclusively. 

| Nome | Valor | Descrição |
| --- | --- | --- |
| content_key_specs. track_type |string |Um nome de tipo de controle. Se content_key_specs for especificado na solicitação de licença, especifique de forma explícita todos os tipos de controle. Se você não fizer isso, haverá uma falha de reprodução após 10 segundos. |
| content_key_specs  <br/> security_level |uint32 |Define os requisitos de robustez de reprodução do cliente. <br/> - É necessário aplicar a criptografia whitebox baseada em software. <br/> - É necessário aplicar a criptografia de software e um decodificador ofuscado. <br/> - As principais operações de criptografia e de materiais devem ser executadas em um ambiente de execução confiável com suporte de hardware. <br/> - A criptografia e decodificação do conteúdo devem ser executadas em um ambiente de execução confiável com suporte de hardware.  <br/> - A criptografia, decodificação e qualquer manipulação da mídia (compactada e descompactada) devem ser tratadas em um ambiente de execução confiável com suporte de hardware. |
| content_key_specs <br/> required_output_protection.hdc |cadeia de caracteres, uma de HDCP_NONE, HDCP_V1, HDCP_V2 |Indica se HDCP é necessário. |
| content_key_specs <br/>chave |Base64-<br/>Base64 |Chave de conteúdo a ser usada para esta faixa. Se especificado, o track_type ou key_id é necessário. O provedor de conteúdo pode usar essa opção para insirir a chave de conteúdo para este controle em vez de deixar o servidor de licença do Widevine gerar ou procurar uma chave. |
| content_key_specs.key_id |Binário de cadeia de caracteres codificada em Base64, 16 bytes |Identificador exclusivo para a chave. |

## <a name="policy-overrides"></a>Substituições de política
| Nome | Valor | Descrição |
| --- | --- | --- |
| policy_overrides&#46;can_play |Booliano, true ou false |Indica que a reprodução do conteúdo é permitida. O padrão é false. |
| policy_overrides&#46;can_persist |Booliano, true ou false |Indica que a licença pode ser persistente para o armazenamento não volátil para uso offline. O padrão é false. |
| policy_overrides&#46;can_renew |Booliano, true ou false |Indica que a renovação dessa licença é permitida. Se for true, a duração da licença poderá ser estendida por pulsação. O padrão é false. |
| policy_overrides&#46;license_duration_seconds |int64 |Indica o período para esta licença específica. Um valor 0 indica que não há qualquer limite para a duração. O padrão é 0. |
| policy_overrides&#46;rental_duration_seconds |int64 |Indica o período em que a reprodução é permitida. Um valor 0 indica que não há qualquer limite para a duração. O padrão é 0. |
| policy_overrides&#46;playback_duration_seconds |int64 |O período de exibição após o início da reprodução dentro da duração da licença. Um valor 0 indica que não há qualquer limite para a duração. O padrão é 0. |
| policy_overrides&#46;renewal_server_url |string |Todas as solicitações (renovação) de pulsação dessa licença são direcionadas para a URL especificada. Este campo só será usado se can_renew for true. |
| policy_overrides&#46;renewal_delay_seconds |int64 |O número de segundos após license_start_time antes da primeira tentativa de renovação. Este campo só será usado se can_renew for true. O padrão é 0. |
| policy_overrides&#46;renewal_retry_interval_seconds |int64 |Especifica o atraso em segundos entre as solicitações de renovação da licença subsequentes, em caso de falha. Este campo só será usado se can_renew for true. |
| policy_overrides&#46;renewal_recovery_duration_seconds |int64 |O período durante o qual a reprodução pode continuar enquanto ocorre a tentativa de renovação, porém sem êxito devido a problemas de back-end com o servidor de licença. Um valor 0 indica que não há qualquer limite para a duração. Este campo só será usado se can_renew for true. |
| policy_overrides&#46;renew_with_usage |Booliano, true ou false |Indica que a licença é enviada para renovação quando o uso inicia. Este campo só será usado se can_renew for true. |

## <a name="session-initialization"></a>Inicialização da sessão
| Nome | Valor | Descrição |
| --- | --- | --- |
| provider_session_token |Cadeia de caracteres codificada em Base64 |Este token de sessão é repassado na licença e continuará a existir em renovações subsequentes. O token de sessão não persiste além das sessões. |
| provider_client_token |Cadeia de caracteres codificada em Base64 |Token de cliente para envio na resposta da licença. Se a solicitação de licença contiver um token de cliente, esse valor será ignorado. O token do cliente persiste além das sessões da licença. |
| override_provider_client_token |Booliano, true ou false |Se for false e a solicitação de licença contiver um token de cliente, use o token da solicitação mesmo se houver um token do cliente especificado nessa estrutura. Se for true, sempre use o token especificado nessa estrutura. |

## <a name="configure-your-widevine-license-with-net"></a>Configurar suas licenças do Widevine com .NET 

Os Serviços de Mídia fornecem uma classe que permite a configuração de suas licenças do Widevine. Para construir a licença, passe o JSON para [WidevineTemplate](/dotnet/api/microsoft.azure.management.media.models.contentkeypolicywidevineconfiguration.widevinetemplate#Microsoft_Azure_Management_Media_Models_ContentKeyPolicyWidevineConfiguration_WidevineTemplate).

Para configurar o modelo, é possível:

### <a name="directly-construct-a-json-string"></a>Construir diretamente uma cadeia de cadeia de caracteres JSON

Esse método pode ser propenso a erro. É recomendável usar outro método, descrito em [Definir classes necessárias e serializar para JSON](#classes).

```csharp
ContentKeyPolicyWidevineConfiguration objContentKeyPolicyWidevineConfiguration = new ContentKeyPolicyWidevineConfiguration
{
    WidevineTemplate = @"{""allowed_track_types"":""SD_HD"",""content_key_specs"":[{""track_type"":""SD"",""security_level"":1,""required_output_protection"":{""hdcp"":""HDCP_V2""}}],""policy_overrides"":{""can_play"":true,""can_persist"":true,""can_renew"":false}}"
};
```

### <a name="define-needed-classes-and-serialize-to-json"></a><a id="classes"></a> Definir classes necessárias e serializar para JSON

#### <a name="define-classes"></a>Definir classes

O exemplo a seguir mostra um exemplo de definições de classes que mapeia para o esquema Widevine JSON. É possível instanciar as classes antes de serializá-las para a cadeia de caracteres JSON.  

```csharp
/// <summary>
/// Widevine PolicyOverrides class.
/// </summary>
public class PolicyOverrides
{
    /// <summary>
    /// Gets or sets a value indicating whether playback of the content is allowed. Default is false.
    /// </summary>
    [JsonProperty("can_play")]
    public bool CanPlay { get; set; }

    /// <summary>
    /// Gets or sets a value indicating whether the license might be persisted to nonvolatile storage for offline use. Default is false.
    /// </summary>
    [JsonProperty("can_persist")]
    public bool CanPersist { get; set; }

    /// <summary>
    /// Gets or sets a value indicating whether renewal of this license is allowed. If true, the duration of the license can be extended by heartbeat. Default is false.
    /// </summary>
    [JsonProperty("can_renew")]
    public bool CanRenew { get; set; }

    /// <summary>
    /// Gets or sets the time window while playback is permitted. A value of 0 indicates that there is no limit to the duration. Default is 0.
    /// </summary>
    [JsonProperty("rental_duration_seconds")]
    public int RentalDurationSeconds { get; set; }

    /// <summary>
    /// Gets or sets the viewing window of time after playback starts within the license duration. A value of 0 indicates that there is no limit to the duration. Default is 0.
    /// </summary>
    [JsonProperty("playback_duration_seconds")]
    public int PlaybackDurationSeconds { get; set; }

    /// <summary>
    /// Gets or sets the time window for this specific license. A value of 0 indicates that there is no limit to the duration. Default is 0.
    /// </summary>
    [JsonProperty("license_duration_seconds")]
    public int LicenseDurationSeconds { get; set; }
}

/// <summary>
/// Widevine ContentKeySpec class.
/// </summary>
public class ContentKeySpec
{
    /// <summary>
    /// Gets or sets track type.
    /// If content_key_specs is specified in the license request, make sure to specify all track types explicitly.
    /// Failure to do so results in failure to play back past 10 seconds.
    /// </summary>
    [JsonProperty("track_type")]
    public string TrackType { get; set; }

    /// <summary>
    /// Gets or sets client robustness requirements for playback.
    /// Software-based white-box cryptography is required.
    /// Software cryptography and an obfuscated decoder are required.
    /// The key material and cryptography operations must be performed within a hardware-backed trusted execution environment.
    /// The cryptography and decoding of content must be performed within a hardware-backed trusted execution environment.
    /// The cryptography, decoding, and all handling of the media (compressed and uncompressed) must be handled within a hardware-backed trusted execution environment.
    /// </summary>
    [JsonProperty("security_level")]
    public int SecurityLevel { get; set; }

    /// <summary>
    /// Gets or sets the OutputProtection.
    /// </summary>
    [JsonProperty("required_output_protection")]
    public OutputProtection RequiredOutputProtection { get; set; }
}

/// <summary>
/// OutputProtection Widevine class.
/// </summary>
public class OutputProtection
{
    /// <summary>
    /// Gets or sets HDCP protection.
    /// Supported values : HDCP_NONE, HDCP_V1, HDCP_V2
    /// </summary>
    [JsonProperty("hdcp")]
    public string HDCP { get; set; }

    /// <summary>
    /// Gets or sets CGMS.
    /// </summary>
    [JsonProperty("cgms_flags")]
    public string CgmsFlags { get; set; }
}

/// <summary>
/// Widevine template.
/// </summary>
public class WidevineTemplate
{
    /// <summary>
    /// Gets or sets the allowed track types.
    /// SD_ONLY or SD_HD.
    /// Controls which content keys are included in a license.
    /// </summary>
    [JsonProperty("allowed_track_types")]
    public string AllowedTrackTypes { get; set; }

    /// <summary>
    /// Gets or sets a finer-grained control on which content keys to return.
    /// For more information, see the section "Content key specs."
    /// Only one of the allowed_track_types and content_key_specs values can be specified.
    /// </summary>
    [JsonProperty("content_key_specs")]
    public ContentKeySpec[] ContentKeySpecs { get; set; }

    /// <summary>
    /// Gets or sets policy settings for the license.
    /// In the event this asset has a predefined policy, these specified values are used.
    /// </summary>
    [JsonProperty("policy_overrides")]
    public PolicyOverrides PolicyOverrides { get; set; }
}
```

#### <a name="configure-the-license"></a>Configurar a licença

Use as classes definidas na seção anterior para criar o JSON que é usado para configurar [WidevineTemplate](/dotnet/api/microsoft.azure.management.media.models.contentkeypolicywidevineconfiguration.widevinetemplate#Microsoft_Azure_Management_Media_Models_ContentKeyPolicyWidevineConfiguration_WidevineTemplate):

```csharp
private static ContentKeyPolicyWidevineConfiguration ConfigureWidevineLicenseTempate()
{
    WidevineTemplate template = new WidevineTemplate()
    {
        AllowedTrackTypes = "SD_HD",
        ContentKeySpecs = new ContentKeySpec[]
        {
            new ContentKeySpec()
            {
                TrackType = "SD",
                SecurityLevel = 1,
                RequiredOutputProtection = new OutputProtection()
                {
                    HDCP = "HDCP_V2"
                }
            }
        },
        PolicyOverrides = new PolicyOverrides()
        {
            CanPlay = true,
            CanPersist = true,
            CanRenew = false,
            RentalDurationSeconds = 2592000,
            PlaybackDurationSeconds = 10800,
            LicenseDurationSeconds = 604800,
        }
    };

    ContentKeyPolicyWidevineConfiguration objContentKeyPolicyWidevineConfiguration = new ContentKeyPolicyWidevineConfiguration
    {
        WidevineTemplate = Newtonsoft.Json.JsonConvert.SerializeObject(template)
    };
    return objContentKeyPolicyWidevineConfiguration;
}
```

## <a name="additional-notes"></a>Observações adicionais

* O Widevine é um serviço fornecido pela Google Inc. e está sujeito aos termos de serviço e à política de privacidade da Google, Inc.

## <a name="next-steps"></a>Próximas etapas

Confira como [proteger com DRM](protect-with-drm.md)
