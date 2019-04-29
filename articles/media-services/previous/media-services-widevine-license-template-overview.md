---
title: Visão geral do modelo de licença do Widevine | Microsoft Docs
description: Este tópico fornece uma visão geral de um modelo de licença do Widevine usado para configurar as licenças do Widevine.
author: juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 0e6f1f05-7ed6-4ed6-82a0-0cc2182b075a
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: d0bb72361e1bff3615f6785ac4c91a10ea773498
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60825519"
---
# <a name="widevine-license-template-overview"></a>Visão geral do modelo de licença do Widevine 
Você pode usar os Serviços de Mídia do Azure para configurar e solicitar licenças do Google Widevine. Quando o player tentar reproduzir o conteúdo protegido do Widevine, uma solicitação será enviada ao serviço de entrega de licença para a obtenção de uma licença. Se o serviço de licença aprovar a solicitação, o serviço emitirá a licença. Ela é enviada ao cliente e é usada para descriptografar e reproduzir o conteúdo especificado.

A solicitação de licença do Widevine é formatada como uma mensagem JSON.  

>[!NOTE]
> Você pode criar uma mensagem vazia sem valores, apenas "{}". Em seguida, é criado um modelo de licença com os padrões. O padrão funciona na maioria dos casos. Cenários de entrega de licença baseada na Microsoft sempre devem usar os padrões. Se você precisar definir os valores de "content_id" e "provedor", um provedor deverá corresponder às credenciais de Widevine.

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
| NOME | Value | DESCRIÇÃO |
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
Se já houver uma política, não será necessário especificar qualquer um dos valores nas especificações de chave de conteúdo. A política preexistente associada a este conteúdo é usada para determinar a proteção de saída, como Proteção de Conteúdo Digital de Alta Largura de Banda (HDCP) e Sistema de Gerenciamento Geral de Cópia (CGMS). Se uma política existente não estiver registrada no servidor de licenças do Widevine, o provedor de conteúdo poderá injetar os valores na solicitação de licença.   

Cada valor content_key_specs deve ser especificado para todos os controles, independentemente da opção use_policy_overrides_exclusively. 

| NOME | Value | DESCRIÇÃO |
| --- | --- | --- |
| content_key_specs. track_type |string |Um nome de tipo de controle. Se content_key_specs for especificado na solicitação de licença, especifique de forma explícita todos os tipos de controle. Se você não fizer isso, haverá uma falha de reprodução após 10 segundos. |
| content_key_specs  <br/> security_level |uint32 |Define os requisitos de robustez de reprodução do cliente. <br/> - É necessário aplicar a criptografia whitebox baseada em software. <br/> - É necessário aplicar a criptografia de software e um decodificador ofuscado. <br/> - As principais operações de criptografia e de materiais devem ser executadas em um ambiente de execução confiável com suporte de hardware. <br/> - A criptografia e decodificação do conteúdo devem ser executadas em um ambiente de execução confiável com suporte de hardware.  <br/> - A criptografia, decodificação e qualquer manipulação da mídia (compactada e descompactada) devem ser tratadas em um ambiente de execução confiável com suporte de hardware. |
| content_key_specs <br/> required_output_protection.hdc |cadeia de caracteres, uma de HDCP_NONE, HDCP_V1, HDCP_V2 |Indica se HDCP é necessário. |
| content_key_specs <br/>chave |Base64-<br/>Base64 |Chave de conteúdo a ser usada para este controle. Se for especificado, o track_type ou a key_id será obrigatório. O provedor de conteúdo pode usar essa opção para insirir a chave de conteúdo para este controle em vez de deixar o servidor de licença do Widevine gerar ou procurar uma chave. |
| content_key_specs.key_id |Binário de cadeia de caracteres codificada em Base64, 16 bytes |Identificador exclusivo para a chave. |

## <a name="policy-overrides"></a>Substituições de política
| NOME | Value | DESCRIÇÃO |
| --- | --- | --- |
| policy_overrides. can_play |Booliano, true ou false |Indica que a reprodução do conteúdo é permitida. O padrão é falso. |
| policy_overrides. can_persist |Booliano, true ou false |Indica que a licença pode ser persistente para o armazenamento não volátil para uso offline. O padrão é falso. |
| policy_overrides. can_renew |Booliano, true ou false |Indica que a renovação dessa licença é permitida. Se for true, a duração da licença poderá ser estendida por pulsação. O padrão é falso. |
| policy_overrides. license_duration_seconds |int64 |Indica o período para esta licença específica. Um valor 0 indica que não há qualquer limite para a duração. O padrão é 0. |
| policy_overrides. rental_duration_seconds |int64 |Indica o período em que a reprodução é permitida. Um valor 0 indica que não há qualquer limite para a duração. O padrão é 0. |
| policy_overrides. playback_duration_seconds |int64 |O período de exibição após o início da reprodução dentro da duração da licença. Um valor 0 indica que não há qualquer limite para a duração. O padrão é 0. |
| policy_overrides. renewal_server_url |string |Todas as solicitações de pulsação (renovação) para esta licença são direcionadas à URL especificada. Este campo só será usado se can_renew for true. |
| policy_overrides. renewal_delay_seconds |int64 |O número de segundos após license_start_time antes da primeira tentativa de renovação. Este campo só será usado se can_renew for true. O padrão é 0. |
| policy_overrides. renewal_retry_interval_seconds |int64 |Especifica o atraso em segundos entre as solicitações de renovação da licença subsequentes, em caso de falha. Este campo só será usado se can_renew for true. |
| policy_overrides. renewal_recovery_duration_seconds |int64 |O período durante o qual a reprodução pode continuar enquanto ocorre a tentativa de renovação, porém sem êxito devido a problemas de back-end com o servidor de licença. Um valor 0 indica que não há qualquer limite para a duração. Este campo só será usado se can_renew for true. |
| policy_overrides. renew_with_usage |Booliano, true ou false |Indica que a licença é enviada para renovação quando o uso inicia. Este campo só será usado se can_renew for true. |

## <a name="session-initialization"></a>Inicialização da sessão
| NOME | Value | DESCRIÇÃO |
| --- | --- | --- |
| provider_session_token |Cadeia de caracteres codificada em Base64 |Este token de sessão é repassado na licença e continuará a existir em renovações subsequentes. O token de sessão não persiste além das sessões. |
| provider_client_token |Cadeia de caracteres codificada em Base64 |Token de cliente para envio na resposta da licença. Se a solicitação de licença contiver um token de cliente, esse valor será ignorado. O token do cliente persiste além das sessões da licença. |
| override_provider_client_token |Booliano, true ou false |Se for false e a solicitação de licença contiver um token de cliente, use o token da solicitação mesmo se houver um token do cliente especificado nessa estrutura. Se for true, sempre use o token especificado nessa estrutura. |

## <a name="configure-your-widevine-licenses-by-using-net-types"></a>Configurar suas licenças do Widevine usando tipos .NET
Os Serviços de Mídia fornecem APIs .NET que você pode usar para configurar suas licenças do Widevine. 

### <a name="classes-as-defined-in-the-media-services-net-sdk"></a>Classes, como definido no SDK do .NET dos Serviços de Mídia
As classes a seguir são as definições desses tipos:

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
O exemplo a seguir mostra como usar as APIs do .NET para configurar uma licença simples do Widevine:

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
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Consulte também
[Usar a criptografia comum dinâmica PlayReady e/ou Widevine](media-services-protect-with-playready-widevine.md)

