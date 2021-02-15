---
title: Códigos de erro de eventos dinâmicos dos serviços de mídia do Azure
description: Este artigo lista códigos de erro de eventos ao vivo.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: error-reference
ms.date: 02/12/2020
ms.author: inhenkel
ms.openlocfilehash: b3be465c488bdd3c5dbd62f757733939d1bee393
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100393506"
---
# <a name="media-services-live-event-error-codes"></a>Códigos de erro de eventos dinâmicos dos serviços de mídia

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

As tabelas a seguir listam os códigos de erro de [evento ao vivo](live-events-outputs-concept.md) .

## <a name="liveeventconnectionrejected"></a>LiveEventConnectionRejected

Ao assinar os eventos da [grade de eventos](../../event-grid/index.yml) para um evento ao vivo, você poderá ver um dos erros a seguir do evento [LiveEventConnectionRejected](media-services-event-schemas.md\#liveeventconnectionrejected) .
> [!div class="mx-tdCol2BreakAll"]
>| Erro | Informações |
>|--|--|
>|**MPE_RTMP_APPID_AUTH_FAILURE** ||
>|Descrição | URL de ingestão incorreta |
>|Solução sugerida| APPID é um token GUID na URL de ingestão RTMP. Verifique se ele corresponde à URL de ingestão da API. |
>|**MPE_INGEST_ENCODER_CONNECTION_DENIED** ||
>| Descrição |O IP do codificador não está presente na lista de permissões de IP configurada |
>| Solução sugerida| Verifique se o IP do codificador está na lista de permissões de IP. Use uma ferramenta online como *whoismyip* ou a *calculadora de CIDR* para definir o valor adequado.  Verifique se o codificador pode alcançar o servidor antes do evento real ao vivo. |
>|**MPE_INGEST_RTMP_SETDATAFRAME_NOT_RECEIVED** ||
>| Descrição|O codificador RTMP não enviou o `setDataFrame` comando. |
>| Solução sugerida|A maioria dos codificadores comerciais envia metadados de fluxo. Para um codificador que envia uma ingestão de taxa de bits única, isso pode não ser um problema. O LiveEvent é capaz de calcular a taxa de bits de entrada quando os metadados do fluxo estão ausentes.  Para a ingestão de múltiplas taxas de bits para um canal de PassThru ou um cenário de push duplo, você pode tentar acrescentar a cadeia de caracteres de consulta com ' videodatarate ' e ' audiodatarate ' na URL de ingestão. O valor aproximado pode funcionar. A unidade está em kbit. Por exemplo, `rtmp://hostname:1935/live/GUID_APPID/streamname?videodatarate=5000&audiodatarate=192` |
>|**MPE_INGEST_CODEC_NOT_SUPPORTED** ||
>| Descrição|Não há suporte para o codec especificado.|
>| Solução sugerida| O LiveEvent recebeu um codec sem suporte. Por exemplo, uma ingestão RTMP, LiveEvent recebeu codec de vídeo não AVC.  Verifique a predefinição do codificador. |
>|**MPE_INGEST_DESCRIPTION_INFO_NOT_RECEIVED** ||
>| Descrição |As informações de descrição de mídia não foram recebidas antes de os dados de mídia reais serem entregues. |
>| Solução sugerida|O LiveEvent não recebe a descrição do fluxo (cabeçalho ou a marca FLV) do codificador. Essa é uma violação de protocolo. Contate o fornecedor do codificador. |
>|**MPE_INGEST_MEDIA_QUALITIES_EXCEEDED** ||
>| Descrição|A contagem de qualidades para o tipo de áudio ou vídeo excedeu o limite máximo permitido. |
>| Solução sugerida|Quando o modo de evento ao vivo é uma codificação ativa, o codificador deve enviar por push uma taxa de bits única de vídeo e áudio.  Observe que um push redundante da mesma taxa de bits é permitido. Verifique a predefinição do codificador ou as configurações de saída para verificar se ele gera um fluxo de taxa de bits única. |
>|**MPE_INGEST_BITRATE_AGGREGATED_EXCEEDED** ||
>| Descrição|A taxa de bits de entrada total em um serviço de canal ou evento ao vivo excedeu o limite máximo permitido. |
>| Solução sugerida|O codificador excedeu a taxa de bits de entrada máxima. Esse limite agrega todos os dados de entrada do codificador colaborador. Verifique as configurações de predefinição ou saída do codificador para reduzir a taxa de bits. |
>|**MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID** ||
>| Descrição|O carimbo de data/hora para FLVTag de vídeo ou áudio é inválido no codificador RTMP. |
>| Solução sugerida|Preterido. |
>|**MPE_INGEST_FRAMERATE_EXCEEDED** ||
>| Descrição|Os fluxos ingeridos do codificador de entrada com taxas de quadros excederam o máximo permitido de 30 fps para codificar eventos/canais ao vivo. |
>| Solução sugerida|Marque a predefinição do codificador para reduzir a taxa de quadros para menos de 36 fps. |
>|**MPE_INGEST_VIDEO_RESOLUTION_NOT_SUPPORTED** ||
>| Descrição|Os fluxos ingeridos do codificador de entrada excederam as seguintes resoluções permitidas: 1920x1088 para codificação de eventos/canais ao vivo e 4096 x 2160 para eventos/canais ao vivo de passagem. |
>| Solução sugerida|Verifique a predefinição do codificador para reduzir a resolução de vídeo para que ele não exceda o limite. |
>|**MPE_INGEST_RTMP_TOO_LARGE_UNPROCESSED_FLV** |
>| Descrição|O evento ao vivo recebeu uma grande quantidade de dados de áudio ao mesmo tempo, ou uma grande quantidade de dados de vídeo sem quadros-chave. Desconectamos o codificador para dar a oportunidade de tentar novamente com os dados corretos. |
>| Solução sugerida|Certifique-se de que o codificador envie um quadro-chave para cada intervalo de quadro chave (GOP).  Habilite configurações como "taxa de bits constante (CBR)" ou "alinhar quadros-chave". Às vezes, a redefinição do codificador colaborador pode ajudar. Se não ajudar, entre em contato com o fornecedor do codificador. |

## <a name="liveeventencoderdisconnected"></a>LiveEventEncoderDisconnected

Você pode ver um dos erros a seguir do evento [LiveEventEncoderDisconnected](media-services-event-schemas.md\#liveeventencoderdisconnected) .

> [!div class="mx-tdCol2BreakAll"]
>| Erro | Informações |
>|--|--|
>|**MPE_RTMP_SESSION_IDLE_TIMEOUT** |
>| Descrição|A sessão RTMP atingiu o tempo limite depois de ficar ociosa por um limite de tempo permitido. |
>|Solução sugerida|Isso normalmente acontece quando um codificador para de receber o feed de entrada para que a sessão se torne ociosa porque não há dados para enviar por push. Verifique se o codificador ou o status do feed de entrada está em um estado íntegro. |
>|**MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID** |
>|Descrição| O carimbo de data/hora do FLVTag de vídeo ou áudio é inválido do codificador RTMP. |
>| Solução sugerida| Preterido. |
>|**MPE_CAPACITY_LIMIT_REACHED** |
>| Descrição|Envio muito rápido de dados pelo codificador. |
>| Solução sugerida|Isso acontece quando o codificador aumenta um grande conjunto de fragmentos em um breve período.  Teoricamente, isso pode acontecer quando o codificador não pode enviar dados por push devido a um problema de rede e os dados de intermitências quando a rede está disponível. Localize o motivo do log do codificador ou do log do sistema. |
>|**Códigos de erro desconhecidos** |
>| Descrição| Esses códigos de erro podem variar de erro de memória para entradas duplicadas no mapa de hash. |

## <a name="other-error-codes"></a>Outros códigos de erro

> [!div class="mx-tdCol2BreakAll"]
>| Erro | Informações |Evento rejeitado/desconectado|
>|--|--|--|
>|**ERROR_END_OF_MEDIA** ||Sim|
>| Descrição|Esse é um erro geral. ||
>|Solução sugerida| Nenhum.||
>|**MPI_SYSTEM_MAINTENANCE** ||Sim|
>| Descrição|O codificador foi desconectado devido à atualização do serviço ou à manutenção do sistema. ||
>|Solução sugerida|Verifique se o codificador habilita ' conexão automática '. Esse é o recurso de codificador para recuperar a desconexão de sessão inesperada. ||
>|**MPE_BAD_URL_SYNTAX** ||Sim|
>| Descrição|A URL de ingestão está formatada incorretamente. ||
>|Solução sugerida|Verifique se a URL de ingestão está formatada corretamente. Para RTMP, deve ser `rtmp[s]://hostname:port/live/GUID_APPID/streamname` ||
>|**MPE_CLIENT_TERMINATED_SESSION** ||Sim|
>| Descrição|O codificador desconectou a sessão.  ||
>|Solução sugerida|Isso não é erro. Esse é o caso em que o codificador iniciou a desconexão, incluindo a desconexão normal. Se essa for uma desconexão inesperada, verifique o log do codificador ou do sistema. |
>|**MPE_INGEST_BITRATE_NOT_MATCH** ||Não|
>| Descrição|A taxa de dados de entrada não coincide com a taxa de bits esperada. ||
>|Solução sugerida|Esse é um aviso que ocorre quando a taxa de dados de entrada é muito lenta ou rápida. Verifique o log do codificador ou do sistema.||
>|**MPE_INGEST_DISCONTINUITY** ||Não|
>| Descrição| Há discontinuty em dados de entrada.||
>|Solução sugerida| Esse é um aviso de que o codificador descarta dados devido a um problema de rede ou a um problema de recurso do sistema. Verifique o log do codificador ou do sistema. Monitore o recurso do sistema (CPU, memória ou rede) também. Se a CPU do sistema for muito alta, tente reduzir a taxa de bits ou use a opção de codificador H/W da placa gráfica do sistema.||

## <a name="see-also"></a>Consulte também

[Códigos de erro de ponto de extremidade de streaming (origem)](streaming-endpoint-error-codes.md)

## <a name="next-steps"></a>Próximas etapas

[Tutorial: Transmissão ao vivo com os Serviços de Mídia](stream-live-tutorial-with-api.md)
