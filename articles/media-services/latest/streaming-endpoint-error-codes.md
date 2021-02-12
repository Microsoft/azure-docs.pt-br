---
title: Erros de empacotamento e origem dos serviços de mídia do Azure
description: Este tópico descreve os erros que você pode receber do serviço de ponto de extremidade de streaming dos serviços de mídia do Azure (Orgin).
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
ms.date: 05/07/2019
ms.author: inhenkel
ms.openlocfilehash: 994e5ae0647f350e0a64f35318bd5803f4ed79b2
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98894976"
---
# <a name="streaming-endpoint-origin-errors"></a>Erros de ponto de extremidade de streaming (origem)

Este tópico descreve os erros que você pode receber do [serviço de ponto de extremidade de streaming](streaming-endpoint-concept.md)dos serviços de mídia do Azure.

## <a name="400-bad-request"></a>400 Solicitação Inválida

A solicitação contém informações inválidas e é rejeitada com esses códigos de erro e devido a um dos seguintes motivos:

|Código do erro|Valor hexadecimal |Descrição do erro|
|---|---|---|
|MPE_BAD_URL_SYNTAX |0x80890201|Uma sintaxe de URL ou um erro de formato. Os exemplos incluem solicitações para um tipo inválido, um fragmento inválido ou uma faixa inválida. |
|MPE_ENC_ENCRYPTION_NOT_SPECIFIED_IN_URL |0x8088024C|A solicitação não tem nenhuma marca de criptografia na URL. Solicitações CMAF exigem uma marca de criptografia na URL. Outros protocolos configurados com mais de um tipo de criptografia também exigem a marca de criptografia para desambiguidade. |
|MPE_STORAGE_BAD_URL_SYNTAX |0x808900E9|A solicitação de armazenamento para atender à solicitação falhou com um erro de solicitação inválido. |

## <a name="403-forbidden"></a>403 Proibido

A solicitação não foi permitida devido a um dos seguintes motivos:

|Código do erro|Valor hexadecimal |Descrição do erro|
|---|---|---|
|MPE_STORAGE_AUTHENTICATION_FAILED |0x808900EA|A solicitação de armazenamento para atender à solicitação falhou com uma falha de autenticação. Isso pode acontecer se as chaves de armazenamento fossem giradas e o serviço não conseguir sincronizar as chaves de armazenamento. <br/><br/>Entre em contato com o suporte do Azure acessando [ajuda + suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) na portal do Azure.|
|MPE_STORAGE_INSUFFICIENT_ACCOUNT_PERMISSIONS |0x808900EB |Erro de operação de armazenamento, o acesso falhou devido a permissões de conta insuficientes. |
|MPE_STORAGE_ACCOUNT_IS_DISABLED |0x808900EC |A solicitação de armazenamento para atender à solicitação falhou porque a conta de armazenamento está desabilitada. |
|MPE_STORAGE_AUTHENTICATION_FAILURE |0x808900F3 |Erro de operação de armazenamento, o acesso falhou devido a erros genéricos. |
|MPE_OUTPUT_FORMAT_BLOCKED |0x80890207 |O formato de saída está bloqueado devido à configuração no StreamingPolicy. |
|MPE_ENC_ENCRYPTION_REQUIRED |0x8088021E |A criptografia é necessária para o conteúdo; a política de entrega é necessária para o formato de saída. |
|MPE_ENC_ENCRYPTION_NOT_SET_IN_DELIVERY_POLICY |0x8088024D |A criptografia não está definida nas configurações da política de entrega. |

## <a name="404-not-found"></a>404 Não Encontrado

A operação está tentando atuar em um recurso que não existe mais. Por exemplo, o recurso pode já ter sido excluído.

|Código do erro|Valor hexadecimal |Descrição do erro|
|---|---|---|
|MPE_EGRESS_TRACK_NOT_FOUND |0x80890209 |A faixa solicitada não foi encontrada. |
|MPE_RESOURCE_NOT_FOUND |0x808901F9 |O recurso solicitado não foi encontrado. |
|MPE_UNAUTHORIZED |0x80890244 |O acesso não está autorizado. |
|MPE_EGRESS_TIMESTAMP_NOT_FOUND |0x8089020A |O carimbo de data/hora solicitado não foi encontrado. |
|MPE_EGRESS_FILTER_NOT_FOUND |0x8089020C |O filtro de manifesto dinâmico solicitado não foi encontrado. |
|MPE_FRAGMENT_BY_INDEX_NOT_FOUND |0x80890252 |O índice de fragmento solicitado está além do intervalo válido. |
|MPE_LIVE_MEDIA_ENTRIES_NOT_FOUND |0x80890254 |Não é possível encontrar entradas de mídia ao vivo para obter o buffer moov. |
|MPE_FRAGMENT_TIMESTAMP_NOT_FOUND |0x80890255 |Não é possível localizar o fragmento na hora solicitada para uma faixa específica.<br/><br/>Pode ser que o fragmento não esteja no armazenamento. Tente uma camada diferente da apresentação que possa ter um fragmento. |
|MPE_MANIFEST_MEDIA_ENTRY_NOT_FOUND |0x80890256 |Não é possível localizar a entrada de mídia para a taxa de bits solicitada no manifesto. <br/><br/>Pode ser que o Player solicitou uma faixa de vídeo de uma determinada taxa de bits que não estava no manifesto.|
|MPE_METADATA_NOT_FOUND |0x80890257 |Não é possível localizar determinados metadados no manifesto ou não é possível localizar a troca de base do armazenamento. |
|MPE_STORAGE_RESOURCE_NOT_FOUND |0x808900ED |Erro de operação de armazenamento, recurso não encontrado. |

## <a name="409-conflict"></a>409 Conflito

A ID fornecida para um recurso em uma `PUT` operação ou foi `POST` usada por um recurso existente. Use outra ID para o recurso para resolver esse problema.

|Código do erro|Valor hexadecimal |Descrição do erro|
|---|---|---|
|MPE_STORAGE_CONFLICT  |0x808900EE  |Erro de operação de armazenamento, erro de conflito.  |

## <a name="410"></a>410

|Código do erro|Valor hexadecimal |Descrição do erro|
|---|---|---|
|MPE_FILTER_FORCE_END_LEFT_EDGE_CROSSED_DVR_WINDOW|0x80890263|Para transmissão ao vivo, quando o filtro que tem forceEndTimestamp definido como true, o carimbo de data/hora de início ou término está fora da janela DVR atual.|

## <a name="412-precondition-failure"></a>412 Falha na Pré-condição

A operação especificou uma eTag que é diferente da versão disponível no servidor, ou seja, um erro de simultaneidade otimista. Repita a solicitação depois de ler a versão mais recente do recurso e atualizar o eTag na solicitação.

|Código do erro|Valor hexadecimal |Descrição do erro|
|---|---|---|
|MPE_FRAGMENT_NOT_READY |0x80890200 |O fragmento solicitado não está pronto.|
|MPE_STORAGE_PRECONDITION_FAILED| 0x808900EF|Erro de operação de armazenamento, uma falha de pré-condição.|

## <a name="415-unsupported-media-type"></a>415 tipo de mídia sem suporte

O formato de carga enviado pelo cliente está em um formato sem suporte.

|Código do erro|Valor hexadecimal |Descrição do erro|
|---|---|---|
|MPE_ENC_ALREADY_ENCRYPTED| 0x8088021F| Não deve aplicar a criptografia no conteúdo já criptografado.|
|MPE_ENC_INVALID_INPUT_ENCRYPTION_FORMAT|0x8088021D |A criptografia é inválida para o formato de entrada.|
|MPE_INVALID_ASSET_DELIVERY_POLICY_TYPE|0x8088021C| O tipo de política de entrega é inválido.|
|MPE_ENC_MULTIPLE_SAME_DELIVERY_TYPE|0x8088024E |As configurações originais podem ser compartilhadas por vários formatos de saída.|
|MPE_FORMAT_NOT_SUPPORTED|0x80890205|Não há suporte para o formato ou tipo de mídia. Por exemplo, os serviços de mídia não dão suporte à contagem de nível de qualidade acima de 64. Na marca de vídeo FLV, os serviços de mídia não oferecem suporte a um quadro de vídeo com vários SPS e vários PPS.|
|MPE_INPUT_FORMAT_NOT_SUPPORTED|0x80890218| Não há suporte para o formato de entrada do ativo solicitado. Os serviços de mídia dão suporte aos formatos Smooth (Live), MP4 (VoD) e de download progressivo.|
|MPE_OUTPUT_FORMAT_NOT_SUPPORTED|0x8089020D|Não há suporte para o formato de saída solicitado. Os serviços de mídia dão suporte aos formatos Smooth, DASH (CSF, CMAF), HLS (v3, v4, CMAF) e de download progressivo.|
|MPE_ENCRYPTION_NOT_SUPPORTED|0x80890208|Encontrado tipo de criptografia sem suporte.|
|MPE_MEDIA_TYPE_NOT_SUPPORTED|0x8089020E|Não há suporte para o tipo de mídia solicitado pelo formato de saída. Os tipos com suporte são o subtítulo vídeo, áudio ou "sutil".|
|MPE_MEDIA_ENCODING_NOT_SUPPORTED|0x8089020F|A mídia de ativos de origem foi codificada com um formato de mídia que não é compatível com o formato de saída.|
|MPE_VIDEO_ENCODING_NOT_SUPPORTED|0x80890210|O ativo de origem foi codificado com um formato de vídeo que não é compatível com o formato de saída. Há suporte para h. 264, AVC, H. 265 (HEVC, HEV1 ou hvc1).|
|MPE_AUDIO_ENCODING_NOT_SUPPORTED|0x80890211|O ativo de origem foi codificado com um formato de áudio que não é compatível com o formato de saída. Os formatos de áudio com suporte são AAC, E-AC3 (DD +), Dolby DTS.|
|MPE_SOURCE_PROTECTION_CONVERSION_NOT_SUPPORTED|0x80890212|O ativo protegido de origem não pode ser convertido no formato de saída.|
|MPE_OUTPUT_PROTECTION_FORMAT_NOT_SUPPORTED|0x80890213|Não há suporte para o formato de proteção no formato de saída.|
|MPE_INPUT_PROTECTION_FORMAT_NOT_SUPPORTED|0x80890219|Não há suporte para o formato de proteção no formato de entrada.|
|MPE_INVALID_VIDEO_NAL_UNIT|0x80890231|Unidade NAL de vídeo inválida, por exemplo, somente a primeira NAL no exemplo pode ser um AUD.|
|MPE_INVALID_NALU_SIZE|0x80890260|Tamanho da unidade NAL inválido.|
|MPE_INVALID_NALU_LENGTH_FIELD|0x80890261|Valor de comprimento de unidade NAL inválido.|
|MPE_FILTER_INVALID|0x80890236|Filtros de manifesto dinâmico inválidos.|
|MPE_FILTER_VERSION_INVALID|0x80890237|Versões de filtro inválidas ou sem suporte.|
|MPE_FILTER_TYPE_INVALID|0x80890238|Tipo de filtro inválido.|
|MPE_FILTER_RANGE_ATTRIBUTE_INVALID|0x80890239|Intervalo inválido especificado pelo filtro.|
|MPE_FILTER_TRACK_ATTRIBUTE_INVALID|0x8089023A|O atributo Track inválido está especificado pelo filtro.|
|MPE_FILTER_PRESENTATION_WINDOW_INVALID|0x8089023B|O comprimento da janela de apresentação inválido é especificado pelo filtro.|
|MPE_FILTER_LIVE_BACKOFF_INVALID|0x8089023C|O backup dinâmico inválido é especificado pelo filtro.|
|MPE_FILTER_MULTIPLE_SAME_TYPE_FILTERS|0x8089023D|Somente um elemento absTimeInHNS tem suporte em filtros herdados.|
|MPE_FILTER_REMOVED_ALL_STREAMS|0x8089023E|Não há mais fluxos depois de aplicar os filtros.|
|MPE_FILTER_LIVE_BACKOFF_OVER_DVRWINDOW|0x8089023F|A retirada dinâmica está além da janela DVR.|
|MPE_FILTER_LIVE_BACKOFF_OVER_PRESENTATION_WINDOW|0x80890262|A retirada dinâmica é maior do que a janela de apresentação.|
|MPE_FILTER_COMPOSITION_FILTER_COUNT_OVER_LIMIT|0x80890246|Excedeu dez (10) filtros padrão máximos permitidos.|
|MPE_FILTER_COMPOSITION_MULTIPLE_FIRST_QUALITY_OPERATOR_NOT_ALLOWED|0x80890248|O primeiro operador de qualidade de vídeo não é permitido em filtros de solicitação combinados.|
|MPE_FILTER_FIRST_QUALITY_ATTRIBUTE_INVALID|0x80890249|O número de atributos de taxa de bits de primeira qualidade deve ser um (1).|
|MPE_HLS_SEGMENT_TOO_LARGE|0x80890243|A duração do segmento HLS deve ser menor que um terço da janela DVR e HLS retirada.|
|MPE_KEY_FRAME_INTERVAL_TOO_LARGE|0x808901FE|As durações do fragmento devem ser menores ou iguais a aproximadamente 20 segundos, ou os níveis de qualidade de entrada não estão alinhados com o tempo.|
|MPE_DTS_RESERVEDBOX_EXPECTED|0x80890105|Erro específico do DTS, não é possível localizar o ReservedBox quando ele deve estar presente no DTSSpecficBox durante a análise da caixa DTS.|
|MPE_DTS_INVALID_CHANNEL_COUNT|0x80890106|Erro específico do DTS, nenhum canal encontrado na DTSSpecficBox durante a análise da caixa DTS.|
|MPE_DTS_SAMPLETYPE_MISMATCH|0x80890107|Erro específico do DTS, tipo de exemplo incompatível no DTSSpecficBox.|
|MPE_DTS_MULTIASSET_DTSH_MISMATCH|0x80890108|Erro específico do DTS, vários ativos é definido, mas o tipo de exemplo DTSH não corresponde.|
|MPE_DTS_INVALID_CORESTREAM_SIZE|0x80890109|Erro específico do DTS, o tamanho do fluxo principal é inválido.|
|MPE_DTS_INVALID_SAMPLE_RESOLUTION|0x8089010A|Erro específico do DTS, a resolução de amostra é inválida.|
|MPE_DTS_INVALID_SUBSTREAM_INDEX|0x8089010B|Erro específico do DTS, índice de extensão de Subfluxo inválido.|
|MPE_DTS_INVALID_BLOCK_NUM|0x8089010C|Erro específico do DTS, número de bloco de Subfluxo inválido.|
|MPE_DTS_INVALID_SAMPLING_FREQUENCE|0x8089010D|Erro específico do DTS, a frequência de amostragem é inválida.|
|MPE_DTS_INVALID_REFCLOCKCODE|0x8089010E|Erro específico do DTS, o código do relógio de referência na extensão de Subfluxo é inválido.|
|MPE_DTS_INVALID_SPEAKERS_REMAP|0x8089010F|Erro específico do DTS; o número do conjunto de remapeamentos de alto-falantes é inválido.|

Para obter os artigos de criptografia e exemplos, consulte:

- [Conceito: proteção de conteúdo](content-protection-overview.md)
- [Conceito: políticas de chave de conteúdo](content-key-policy-concept.md)
- [Conceito: políticas de streaming](streaming-policy-concept.md)
- [Exemplo: proteger com criptografia AES](protect-with-aes128.md)
- [Exemplo: proteger com DRM](protect-with-drm.md)

Para obter diretrizes de filtro, consulte:

- [Conceito: manifestos dinâmicos](filters-dynamic-manifest-overview.md)
- [Conceito: filtros](filters-concept.md)
- [Exemplo: criar filtros com APIs REST](filters-dynamic-manifest-rest-howto.md)
- [Exemplo: criar filtros com o .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Exemplo: criar filtros com a CLI](filters-dynamic-manifest-cli-howto.md)

Para obter artigos e exemplos ao vivo, consulte:

- [Conceito: visão geral da transmissão ao vivo](live-streaming-overview.md)
- [Conceito: eventos ao vivo e saídas ao vivo](live-events-outputs-concept.md)
- [Exemplo: tutorial de transmissão ao vivo](stream-live-tutorial-with-api.md)

## <a name="416-range-not-satisfiable"></a>416 Intervalo não satisfatório

|Código do erro|Valor hexadecimal |Descrição do erro|
|---|---|---|
|MPE_STORAGE_INVALID_RANGE|0x808900F1|Erro de operação de armazenamento, retornou erro http 416, intervalo inválido.|

## <a name="500-internal-server-error"></a>Erro interno de servidor 500

Durante o processamento da solicitação, os Serviços de Mídia encontram algum erro que impede o processamento de continuar.  

|Código do erro|Valor hexadecimal |Descrição do erro|
|---|---|---|
|MPE_STORAGE_SOCKET_TIMEOUT|0x808900F4|Recebido e convertido do código de erro WinHTTP de ERROR_WINHTTP_TIMEOUT (0x00002ee2).|
|MPE_STORAGE_SOCKET_CONNECTION_ERROR|0x808900F5|Recebido e convertido do código de erro WinHTTP de ERROR_WINHTTP_CONNECTION_ERROR (0x00002efe).|
|MPE_STORAGE_SOCKET_NAME_NOT_RESOLVED|0x808900F6|Recebido e convertido do código de erro WinHTTP de ERROR_WINHTTP_NAME_NOT_RESOLVED (0x00002ee7).|
|MPE_STORAGE_INTERNAL_ERROR|0x808900E6|Erro de operação de armazenamento, InternalError geral de um dos erros HTTP 500.|
|MPE_STORAGE_OPERATION_TIMED_OUT|0x808900E7|Erro de operação de armazenamento, OperationTimedOut geral de um dos erros HTTP 500.|
|MPE_STORAGE_FAILURE|0x808900F2|Erro de operação de armazenamento, outros erros HTTP 500 que InternalError ou OperationTimedOut.|

## <a name="503-service-unavailable"></a>503 Serviço Indisponível

Atualmente, o servidor não pode receber solicitações. Esse erro pode ter sido causado por excesso de solicitações ao serviço. O mecanismo de aceleração dos Serviços de Mídia restringe o uso dos recursos para aplicativos que fazem solicitações excessivas ao serviço.

> [!NOTE]
> Verifique a mensagem de erro e a cadeia de caracteres do código de erro para obter informações mais detalhadas sobre o motivo pelo qual você obteve o erro 503. Esse erro nem sempre significa limitação.
> 

|Código do erro|Valor hexadecimal |Descrição do erro|
|---|---|---|
|MPE_STORAGE_SERVER_BUSY|0x808900E8|Erro de operação de armazenamento, erro de servidor HTTP ocupado 503.|

## <a name="ask-questions-give-feedback-get-updates"></a>Fazer perguntas, comentar, obter atualizações

Confira o artigo [comunidade dos Serviços de Mídia do Azure](media-services-community.md) para ver diferentes maneiras de fazer perguntas, comentários e obter atualizações sobre os serviços de mídia.

## <a name="see-also"></a>Confira também

- [Códigos de erro de codificação](/rest/api/media/jobs/get#joberrorcode)
- [Conceitos dos Serviços de Mídia do Azure](concepts-overview.md)
- [Cotas e limites](limits-quotas-constraints.md)

## <a name="next-steps"></a>Próximas etapas

[Exemplo: acessar ErrorCode e Message de ApiException com .NET](configure-connect-dotnet-howto.md#connect-to-the-net-client)
