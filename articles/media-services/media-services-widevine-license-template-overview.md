---
title: "Visão geral do modelo de licença do Widevine | Microsoft Docs"
description: "Este tópico fornece uma visão geral de um modelo de licença do Widevine usado para configurar as licenças do Widevine."
author: juliako
manager: cfowler
editor: 
services: media-services
documentationcenter: 
ms.assetid: 0e6f1f05-7ed6-4ed6-82a0-0cc2182b075a
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: juliako
ms.openlocfilehash: 667ff16dc7608dab2a5b8b1fd7df715da4620ca1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="widevine-license-template-overview"></a>Visão geral do modelo de licença do Widevine
## <a name="overview"></a>Visão geral
Agora, os Serviços de Mídia do Azure permitem que você configure e solicite licenças do Widevine. Quando o player do usuário final tentar reproduzir o conteúdo protegido do Widevine, uma solicitação será enviada ao serviço de entrega de licença para a obtenção de uma licença. Se o serviço de licença aprova a solicitação, ele emite a licença que é enviada ao cliente e pode ser usada para descriptografar e reproduzir o conteúdo especificado.

A solicitação de licença do Widevine é formatada como uma mensagem JSON.  

>[!NOTE]
> Você pode optar por criar uma mensagem vazia sem valores, apenas "{}" e um modelo de licença será criado com todos os padrões. O padrão funciona na maioria dos casos. Por exemplo, para cenários de entrega de licença com base em MS que sempre devem ser o padrão. Se você precisar definir os valores de "content_id" e "provedor", um provedor deverá corresponder às credenciais de Widevine do Google.

    {  
       “payload”:“<license challenge>”,
       “content_id”: “<content id>” 
       “provider”: ”<provider>”
       “allowed_track_types”:“<types>”,
       “content_key_specs”:[  
          {  
             “track_type”:“<track type 1>”
          },
          {  
             “track_type”:“<track type 2>”
          },
          …
       ],
       “policy_overrides”:{  
          “can_play”:<can play>,
          “can persist”:<can persist>,
          “can_renew”:<can renew>,
          “rental_duration_seconds”:<rental duration>,
          “playback_duration_seconds”:<playback duration>,
          “license_duration_seconds”:<license duration>,
          “renewal_recovery_duration_seconds”:<renewal recovery duration>,
          “renewal_server_url”:”<renewal server url>”,
          “renewal_delay_seconds”:<renewal delay>,
          “renewal_retry_interval_seconds”:<renewal retry interval>,
          “renew_with_usage”:<renew with usage>
       }
    }

## <a name="json-message"></a>Mensagem JSON
| Nome | Valor | Descrição |
| --- | --- | --- |
| payload |Cadeia codificada em Base64 |A solicitação de licença enviada por um cliente. |
| content_id |Cadeia codificada em Base64 |Identificador usado para gerar KeyId(s) e chaves de conteúdo para cada content_key_specs.track_type. |
| provider |string |Usado para pesquisar as políticas e chaves de conteúdo. Se a distribuição de chaves MS é usada para entrega de licença do Widevine, esse parâmetro é ignorado. |
| policy_name |string |Nome de uma política registrada anteriormente. Opcional |
| allowed_track_types |enum |SD_ONLY ou SD_HD. Controla quais chaves de conteúdo devem ser incluídas em uma licença |
| content_key_specs |matriz de estruturas JSON, confira **Especificações de chave de conteúdo** abaixo |Um controle mais refinado sobre quais chaves de conteúdo retornar. Confira Especificações de chave de conteúdo a seguir para obter detalhes.  Apenas um entre allowed_track_types e content_key_specs pode ser especificado. |
| use_policy_overrides_exclusively |booliano. true ou false |Use os atributos de política especificados por policy_overrides e omita todas as políticas armazenadas anteriormente. |
| policy_overrides |Estrutura JSON, confira **Substituições de política** abaixo |Configurações de política para esta licença.  Caso este ativo tenha uma política predefinida, esses valores especificados serão usados. |
| session_init |Estrutura JSON, confira **Inicialização da sessão** abaixo |Dados opcionais passados para a licença. |
| parse_only |booliano. true ou false |A solicitação de licença é analisada, mas nenhuma licença é emitida. No entanto, os valores da solicitação de licença retornam na resposta. |

## <a name="content-key-specs"></a>Especificações de chave de conteúdo
Se já houver uma política, não será necessário especificar qualquer um dos valores nas Especificações de Chave de Conteúdo.  A política existente associada a este conteúdo será usada para determinar a proteção da saída, como HDCP e CGMS.  Se uma política existente não estiver registrada no Servidor de Licenças do Widevine, o provedor de conteúdo poderá injetar os valores na solicitação de licença.   

Cada content_key_specs deve ser especificado para todos os controles, independentemente da opção use_policy_overrides_exclusively. 

| Nome | Valor | Descrição |
| --- | --- | --- |
| content_key_specs. track_type |string |Um nome de tipo de controle. Se content_key_specs for especificado na solicitação de licença, especifique de forma explícita todos os tipos de controle. Se você não fizer isso, haverá uma falha de reprodução após 10 segundos. |
| content_key_specs  <br/> security_level |uint32 |Define os requisitos de robustez de reprodução do cliente. <br/> 1 - É necessário aplicar a criptografia whitebox baseada em software. <br/> 2 - É necessário aplicar a criptografia de software e um decodificador ofuscado. <br/> 3 - As principais operações de criptografia e de materiais devem ser executadas em um ambiente de execução confiável com suporte de hardware. <br/> 4 - A criptografia e decodificação do conteúdo devem ser executadas em um ambiente de execução confiável com suporte de hardware.  <br/> 5 - A criptografia, decodificação e qualquer manipulação da mídia (compactada e descompactada) devem ser tratadas em um ambiente de execução confiável com suporte de hardware. |
| content_key_specs <br/> required_output_protection.hdc |cadeia de caracteres - uma das seguintes: HDCP_NONE, HDCP_V1, HDCP_V2 |Indica se HDCP é necessário |
| content_key_specs <br/>chave |Cadeia codificada em  <br/>Base64 |Chave de conteúdo a ser usada para este controle. Se for especificado, o track_type ou a key_id será obrigatório.  Essa opção permite que o provedor de conteúdo insira a chave de conteúdo para este controle em vez de deixar o servidor de licença do Widevine gerar ou procurar uma chave. |
| content_key_specs.key_id |Binário de cadeia de caracteres codificada em Base64, 16 bytes |Identificador exclusivo para a chave. |

## <a name="policy-overrides"></a>Substituições de política
| Nome | Valor | Descrição |
| --- | --- | --- |
| policy_overrides. can_play |booliano. true ou false |Indica que a reprodução do conteúdo é permitida. O padrão é falso. |
| policy_overrides. can_persist |booliano. true ou false |Indica que a licença pode ser persistente para o armazenamento não volátil para uso offline. O padrão é falso. |
| policy_overrides. can_renew |booliano true ou false |Indica que a renovação dessa licença é permitida. Se for true, a duração da licença poderá ser estendida por pulsação. O padrão é falso. |
| policy_overrides. license_duration_seconds |int64 |Indica o período para esta licença específica. Um valor 0 indica que não há qualquer limite para a duração. O padrão é 0. |
| policy_overrides. rental_duration_seconds |int64 |Indica o período em que a reprodução é permitida. Um valor 0 indica que não há qualquer limite para a duração. O padrão é 0. |
| policy_overrides. playback_duration_seconds |int64 |O período de exibição após o início da reprodução dentro da duração da licença. Um valor 0 indica que não há qualquer limite para a duração. O padrão é 0. |
| policy_overrides. renewal_server_url |string |Todas as solicitações de pulsação (renovação) para esta licença devem ser direcionadas à URL especificada. Este campo só será usado se can_renew for true. |
| policy_overrides. renewal_delay_seconds |int64 |O número de segundos após license_start_time, antes da primeira tentativa de renovação. Este campo só será usado se can_renew for true. O padrão é 0 |
| policy_overrides. renewal_retry_interval_seconds |int64 |Especifica o atraso em segundos entre as solicitações de renovação da licença subsequentes, em caso de falha. Este campo só será usado se can_renew for true. |
| policy_overrides. renewal_recovery_duration_seconds |int64 |O período durante o qual a reprodução é permitida enquanto ocorre a tentativa de renovação, porém sem êxito devido a problemas de back-end com o servidor de licença. Um valor 0 indica que não há qualquer limite para a duração. Este campo só será usado se can_renew for true. |
| policy_overrides. renew_with_usage |booliano true ou false |Indica que a licença deve ser enviada para renovação quando o uso for iniciado. Este campo só será usado se can_renew for true. |

## <a name="session-initialization"></a>Inicialização da sessão
| Nome | Valor | Descrição |
| --- | --- | --- |
| provider_session_token |Cadeia codificada em Base64 |Este token de sessão é repassado na licença e continuará a existir em renovações subsequentes.  O token de sessão não persistirá além das sessões. |
| provider_client_token |Cadeia codificada em Base64 |Token de cliente para envio na resposta da licença.  Se a solicitação de licença contiver um token de cliente, esse valor será ignorado. O token do cliente persistirá além das sessões da licença. |
| override_provider_client_token |booliano. true ou false |Se for false e a solicitação de licença contiver um token de cliente, use o token da solicitação mesmo se houver um token do cliente especificado nessa estrutura.  Se for true, sempre use o token especificado nessa estrutura. |

## <a name="configure-your-widevine-licenses-using-net-types"></a>Configurar suas licenças do Widevine usando tipos .NET
Os Serviços de Mídia fornecem APIs .NET que permitem a configuração de suas licenças do Widevine. 

### <a name="classes-as-defined-in-the-media-services-net-sdk"></a>Classes, como definido no SDK do .NET dos Serviços de Mídia
Veja a seguir as definições desses tipos.

    public class WidevineMessage
    {
        public WidevineMessage();

        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public AllowedTrackTypes? allowed_track_types { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public ContentKeySpecs[] content_key_specs { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public object policy_overrides { get; set; }
    }

    [JsonConverter(typeof(StringEnumConverter))]
    public enum AllowedTrackTypes
    {
        SD_ONLY = 0,
        SD_HD = 1
    }
    public class ContentKeySpecs
    {
        public ContentKeySpecs();

        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public string key_id { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public RequiredOutputProtection required_output_protection { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public int? security_level { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public string track_type { get; set; }
    }

    public class RequiredOutputProtection
    {
        public RequiredOutputProtection();

        public Hdcp hdcp { get; set; }
    }

    [JsonConverter(typeof(StringEnumConverter))]
    public enum Hdcp
    {
        HDCP_NONE = 0,
        HDCP_V1 = 1,
        HDCP_V2 = 2
    }

### <a name="example"></a>Exemplo
O exemplo a seguir mostra como usar as APIs do .NET para configurar uma licença simples do Widevine.

    private static string ConfigureWidevineLicenseTemplate()
    {
        var template = new WidevineMessage
        {
            allowed_track_types = AllowedTrackTypes.SD_HD,
            content_key_specs = new[]
            {
                new ContentKeySpecs
                {
                    required_output_protection = new RequiredOutputProtection { hdcp = Hdcp.HDCP_NONE},
                    security_level = 1,
                    track_type = "SD"
                }
            },
            policy_overrides = new
            {
                can_play = true,
                can_persist = true,
                can_renew = false
            }
        };

        string configuration = JsonConvert.SerializeObject(template);
        return configuration;
    }


## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Consulte também
[Usando a PlayReady e/ou a Criptografia Comum Dinâmica Widevine](media-services-protect-with-drm.md)

