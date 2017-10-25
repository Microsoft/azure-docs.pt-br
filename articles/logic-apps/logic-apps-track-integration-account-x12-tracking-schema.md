---
title: "Esquemas de acompanhamento X12 para monitoramento B2B - Aplicativo Lógico do Azure | Microsoft Docs"
description: "Use esquemas de acompanhamento X12 para monitorar mensagens de B2B de transações em sua Conta de Integração do Azure."
author: padmavc
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: a5413f80-eaad-4bcf-b371-2ad0ef629c3d
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: LADocs; padmavc
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3782c0a76ea8728a146b3d73774f74c31187cbfd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="start-or-enable-tracking-of-x12-messages-to-monitor-success-errors-and-message-properties"></a>Iniciar ou habilitar acompanhamento de mensagens X12 para monitorar o êxito, os erros e as propriedades da mensagem
Você pode usar esses esquemas de acompanhamento X12 em sua conta de integração do Azure para ajudá-lo a monitorar transações B2B (entre empresas):

* Esquema de acompanhamento do conjunto de transações X12
* Esquema de acompanhamento de confirmação do conjunto de transações X12
* Esquema de acompanhamento de intercâmbio X12
* Esquema de acompanhamento de confirmação do intercâmbio X12
* Esquema de acompanhamento de grupo funcional X12
* Esquema de acompanhamento de confirmação do grupo funcional X12

## <a name="x12-transaction-set-tracking-schema"></a>Esquema de acompanhamento do conjunto de transações X12
````java

    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "functionalGroupControlNumber": "",
                "transactionSetControlNumber": "",
                "CorrelationMessageId": "",
                "messageType": "",
                "isMessageFailed": "",
                "isTechnicalAcknowledgmentExpected": "",
                "isFunctionalAcknowledgmentExpected": "",
                "needAk2LoopForValidMessages":  "",
                "segmentsCount": ""
            }
    }
````

| Propriedade | Tipo | Descrição |
| --- | --- | --- |
| senderPartnerName | Cadeia de caracteres | O nome do parceiro do remetente da mensagem X12. (Opcional) |
| receiverPartnerName | Cadeia de caracteres | O nome do parceiro do destinatário da mensagem X12. (Opcional) |
| senderQualifier | Cadeia de caracteres | Qualificador de parceiro de envio. (Obrigatório) |
| senderIdentifier | Cadeia de caracteres | Identificador de parceiro de envio. (Obrigatório) |
| receiverQualifier | Cadeia de caracteres | Qualificador de parceiro de recebimento. (Obrigatório) |
| receiverIdentifier | Cadeia de caracteres | Identificador de parceiro de recebimento. (Obrigatório) |
| agreementName | Cadeia de caracteres | Nome do contrato X12 para o qual as mensagens são resolvidas. (Opcional) |
| direction | Enum | Indica a direção do fluxo de mensagens, receber ou enviar. (Obrigatório) |
| interchangeControlNumber | Cadeia de caracteres | Número de controle de intercâmbio. (Opcional) |
| functionalGroupControlNumber | Cadeia de caracteres | Número de controle funcional. (Opcional) |
| transactionSetControlNumber | Cadeia de caracteres | Número de controle de conjunto de transações. (Opcional) |
| CorrelationMessageId | Cadeia de caracteres | ID de mensagem de correlação. Uma combinação de {AgreementName}{*GroupControlNumber*}{TransactionSetControlNumber}. (Opcional) |
| messageType | Cadeia de caracteres | Tipo de documento ou conjunto de transações. (Opcional) |
| isMessageFailed | Booliano | Se a mensagem X12 falha ou não. (Obrigatório) |
| isTechnicalAcknowledgmentExpected | Booliano | Se a confirmação técnica está configurada ou não no contrato X12. (Obrigatório) |
| isFunctionalAcknowledgmentExpected | Booliano | Se a confirmação funcional está configurada ou não no contrato X12. (Obrigatório) |
| needAk2LoopForValidMessages | Booliano | Se o loop AK2 é ou não necessário para uma mensagem válida. (Obrigatório) |
| segmentsCount | Número inteiro | O número de segmentos no conjunto de transações do X12. (Opcional) |

## <a name="x12-transaction-set-acknowledgement-tracking-schema"></a>Esquema de acompanhamento de confirmação do conjunto de transações X12
````java

    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "functionalGroupControlNumber": "",
                "isaSegment": "",
                "gsSegment": "",
                "respondingfunctionalGroupControlNumber": "",
                "respondingFunctionalGroupId": "",
                "respondingtransactionSetControlNumber": "",
                "respondingTransactionSetId": "",
                "statusCode": "",
                "processingStatus": "",
                "CorrelationMessageId": ""
                "isMessageFailed": "",
                "ak2Segment": "",
                "ak3Segment": "",
                "ak5Segment": ""
            }
    }
````

| Propriedade | Tipo | Descrição |
| --- | --- | --- |
| senderPartnerName | Cadeia de caracteres | O nome do parceiro do remetente da mensagem X12. (Opcional) |
| receiverPartnerName | Cadeia de caracteres | O nome do parceiro do destinatário da mensagem X12. (Opcional) |
| senderQualifier | Cadeia de caracteres | Qualificador de parceiro de envio. (Obrigatório) |
| senderIdentifier | Cadeia de caracteres | Identificador de parceiro de envio. (Obrigatório) |
| receiverQualifier | Cadeia de caracteres | Qualificador de parceiro de recebimento. (Obrigatório) |
| receiverIdentifier | Cadeia de caracteres | Identificador de parceiro de recebimento. (Obrigatório) |
| agreementName | Cadeia de caracteres | Nome do contrato X12 para o qual as mensagens são resolvidas. (Opcional) |
| direction | Enum | Indica a direção do fluxo de mensagens, receber ou enviar. (Obrigatório) |
| interchangeControlNumber | Cadeia de caracteres | O número de controle de intercâmbio da confirmação funcional. O valor popula apenas o lado de envio em que a confirmação funcional é recebida para as mensagens enviadas ao parceiro. (Opcional) |
| functionalGroupControlNumber | Cadeia de caracteres | O número de controle do grupo funcional da confirmação funcional. O valor popula apenas o lado de envio em que a confirmação funcional é recebida para as mensagens enviadas ao parceiro. (Opcional) |
| isaSegment | Cadeia de caracteres | O segmento ISA da mensagem. O valor popula apenas o lado de envio em que a confirmação funcional é recebida para as mensagens enviadas ao parceiro. (Opcional) |
| gsSegment | Cadeia de caracteres | O segmento GS da mensagem. O valor popula apenas o lado de envio em que a confirmação funcional é recebida para as mensagens enviadas ao parceiro. (Opcional) |
| respondingfunctionalGroupControlNumber | Cadeia de caracteres | O número de controle de intercâmbio de resposta. (Opcional) |
| respondingFunctionalGroupId | Cadeia de caracteres | A ID de grupo funcional de resposta, que mapeia para AK101 na confirmação. (Opcional) |
| respondingtransactionSetControlNumber | Cadeia de caracteres | Número de controle de conjunto de transações de resposta. (Opcional) |
| respondingTransactionSetId | Cadeia de caracteres | A ID de conjunto de transações de resposta, que mapeia para AK201 na confirmação. (Opcional) |
| statusCode | Booliano | O código de status de confirmação do conjunto de transações. (Obrigatório) |
| segmentsCount | Enum | O código de status de confirmação. Os valores aceitos são **Accepted**, **Rejected**, **AcceptedWithErrors**. (Obrigatório) |
| processingStatus | Enum | O status de processamento da confirmação. Os valores permitidos são **Received**, **Generated**, **Sent**. (Obrigatório) |
| CorrelationMessageId | Cadeia de caracteres | ID de mensagem de correlação. Uma combinação de {AgreementName}{*GroupControlNumber*}{TransactionSetControlNumber}. (Opcional) |
| isMessageFailed | Booliano | Se a mensagem X12 falha ou não. (Obrigatório) |
| ak2Segment | Cadeia de caracteres | A confirmação de um conjunto de transações no grupo funcional recebido. (Opcional) |
| ak3Segment | Cadeia de caracteres | Relata erros em um segmento de dados. (Opcional) |
| ak5Segment | Cadeia de caracteres | Relata se o conjunto de transações identificado no segmento AK2 foi aceito ou rejeitado e a razão. (Opcional) |

## <a name="x12-interchange-tracking-schema"></a>Esquema de acompanhamento de intercâmbio X12
````java

    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "isaSegment": "",
                "isTechnicalAcknowledgmentExpected": "",
                "isMessageFailed": "",
                "isa09": "",
                "isa10": "",
                "isa11": "",
                "isa12": "",
                "isa14": "",
                "isa15": "",
                "isa16": ""
            }
    }
````

| Propriedade | Tipo | Descrição |
| --- | --- | --- |
| senderPartnerName | Cadeia de caracteres | O nome do parceiro do remetente da mensagem X12. (Opcional) |
| receiverPartnerName | Cadeia de caracteres | O nome do parceiro do destinatário da mensagem X12. (Opcional) |
| senderQualifier | Cadeia de caracteres | Qualificador de parceiro de envio. (Obrigatório) |
| senderIdentifier | Cadeia de caracteres | Identificador de parceiro de envio. (Obrigatório) |
| receiverQualifier | Cadeia de caracteres | Qualificador de parceiro de recebimento. (Obrigatório) |
| receiverIdentifier | Cadeia de caracteres | Identificador de parceiro de recebimento. (Obrigatório) |
| agreementName | Cadeia de caracteres | Nome do contrato X12 para o qual as mensagens são resolvidas. (Opcional) |
| direction | Enum | Indica a direção do fluxo de mensagens, receber ou enviar. (Obrigatório) |
| interchangeControlNumber | Cadeia de caracteres | Número de controle de intercâmbio. (Opcional) |
| isaSegment | Cadeia de caracteres | Segmento ISA de mensagem. (Opcional) |
| isTechnicalAcknowledgmentExpected | Booliano | Se a confirmação técnica está configurada ou não no contrato X12. (Obrigatório) |
| isMessageFailed | Booliano | Se a mensagem X12 falha ou não. (Obrigatório) |
| isa09 | Cadeia de caracteres | A data de intercâmbio do documento X12. (Opcional) |
| isa10 | Cadeia de caracteres | A hora de intercâmbio do documento X12. (Opcional) |
| isa11 | Cadeia de caracteres | O identificador de Padrões de Controle de intercâmbio X12. (Opcional) |
| isa12 | Cadeia de caracteres | O número de versão de controle de intercâmbio X12. (Opcional) |
| isa14 | Cadeia de caracteres | A confirmação do X12 é solicitada. (Opcional) |
| isa15 | Cadeia de caracteres | O indicador de teste ou produção. (Opcional) |
| isa16 | Cadeia de caracteres | Separador de elementos. (Opcional) |

## <a name="x12-interchange-acknowledgement-tracking-schema"></a>Esquema de acompanhamento de confirmação do intercâmbio X12
````java
    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "isaSegment": "",
                "respondingInterchangeControlNumber": "",
                "isMessageFailed": "",
                "statusCode": "",
                "processingStatus": "",
                "ta102": "",
                "ta103": "",
                "ta105": ""
            }
    }
````

| Propriedade | Tipo | Descrição |
| --- | --- | --- |
| senderPartnerName | Cadeia de caracteres | O nome do parceiro do remetente da mensagem X12. (Opcional) |
| receiverPartnerName | Cadeia de caracteres | O nome do parceiro do destinatário da mensagem X12. (Opcional) |
| senderQualifier | Cadeia de caracteres | Qualificador de parceiro de envio. (Obrigatório) |
| senderIdentifier | Cadeia de caracteres | Identificador de parceiro de envio. (Obrigatório) |
| receiverQualifier | Cadeia de caracteres | Qualificador de parceiro de recebimento. (Obrigatório) |
| receiverIdentifier | Cadeia de caracteres | Identificador de parceiro de recebimento. (Obrigatório) |
| agreementName | Cadeia de caracteres | Nome do contrato X12 para o qual as mensagens são resolvidas. (Opcional) |
| direction | Enum | Indica a direção do fluxo de mensagens, receber ou enviar. (Obrigatório) |
| interchangeControlNumber | Cadeia de caracteres | O número de controle de intercâmbio da confirmação técnica recebida dos parceiros. (Opcional) |
| isaSegment | Cadeia de caracteres | O segmento ISA da confirmação técnica recebida dos parceiros. (Opcional) |
| respondingInterchangeControlNumber |Cadeia de caracteres | O número de controle de intercâmbio da confirmação técnica recebida dos parceiros. (Opcional) |
| isMessageFailed | Booliano | Se a mensagem X12 falha ou não. (Obrigatório) |
| statusCode | Enum | O código de status de confirmação do intercâmbio. Os valores aceitos são **Accepted**, **Rejected**, **AcceptedWithErrors**. (Obrigatório) |
| processingStatus | Enum | Status de confirmação. Os valores permitidos são **Received**, **Generated**, **Sent**. (Obrigatório) |
| ta102 | Cadeia de caracteres | Data do intercâmbio. (Opcional) |
| ta103 | Cadeia de caracteres | Hora do intercâmbio. (Opcional) |
| ta105 | Cadeia de caracteres | Código de observação de intercâmbio. (Opcional) |

## <a name="x12-functional-group-tracking-schema"></a>Esquema de acompanhamento de grupo funcional X12
````java

    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "functionalGroupControlNumber": "",
                "gsSegment": "",
                "isTechnicalAcknowledgmentExpected": "",
                "isFunctionalAcknowledgmentExpected": "",
                "isMessageFailed": "",
                "gs01": "",
                "gs02": "",
                "gs03": "",
                "gs04": "",
                "gs05": "",
                "gs07": "",
                "gs08": ""
            }
    }
````

| Propriedade | Tipo | Descrição |
| --- | --- | --- |
| senderPartnerName | Cadeia de caracteres | O nome do parceiro do remetente da mensagem X12. (Opcional) |
| receiverPartnerName | Cadeia de caracteres | O nome do parceiro do destinatário da mensagem X12. (Opcional) |
| senderQualifier | Cadeia de caracteres | Qualificador de parceiro de envio. (Obrigatório) |
| senderIdentifier | Cadeia de caracteres | Identificador de parceiro de envio. (Obrigatório) |
| receiverQualifier | Cadeia de caracteres | Qualificador de parceiro de recebimento. (Obrigatório) |
| receiverIdentifier | Cadeia de caracteres | Identificador de parceiro de recebimento. (Obrigatório) |
| agreementName | Cadeia de caracteres | Nome do contrato X12 para o qual as mensagens são resolvidas. (Opcional) |
| direction | Enum | Indica a direção do fluxo de mensagens, receber ou enviar. (Obrigatório) |
| interchangeControlNumber | Cadeia de caracteres | Número de controle de intercâmbio. (Opcional) |
| functionalGroupControlNumber | Cadeia de caracteres | Número de controle funcional. (Opcional) |
| gsSegment | Cadeia de caracteres | Segmento GS de mensagem. (Opcional) |
| isTechnicalAcknowledgmentExpected | Booliano | Se a confirmação técnica está configurada ou não no contrato X12. (Obrigatório) |
| isFunctionalAcknowledgmentExpected | Booliano | Se a confirmação funcional está configurada ou não no contrato X12. (Obrigatório) |
| isMessageFailed | Booliano | Se a mensagem X12 falha ou não. (Obrigatório)|
| gs01 | Cadeia de caracteres | O código do identificador funcional. (Opcional) |
| gs02 | Cadeia de caracteres | O código do remetente do aplicativo. (Opcional) |
| gs03 | Cadeia de caracteres | O código do receptor do aplicativo. (Opcional) |
| gs04 | Cadeia de caracteres | Data de grupo funcional. (Opcional) |
| gs05 | Cadeia de caracteres | Hora de grupo funcional. (Opcional) |
| gs07 | Cadeia de caracteres | Código da agência responsável. (Opcional) |
| gs08 | Cadeia de caracteres | Código de identificador de versão/lançamento/setor. (Opcional) |

## <a name="x12-functional-group-acknowledgement-tracking-schema"></a>Esquema de acompanhamento de confirmação do grupo funcional X12
````java
    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "functionalGroupControlNumber": "",
                "isaSegment": "",
                "gsSegment": "",
                "respondingfunctionalGroupControlNumber": "",
                "respondingFunctionalGroupId": "",
                "isMessageFailed": "",
                "statusCode": "",
                "processingStatus": "",
                "ak903": "",
                "ak904": "",
                "ak9Segment": ""
            }
    }
````

| Propriedade | Tipo | Descrição |
| --- | --- | --- |
| senderPartnerName | Cadeia de caracteres | O nome do parceiro do remetente da mensagem X12. (Opcional) |
| receiverPartnerName | Cadeia de caracteres | O nome do parceiro do destinatário da mensagem X12. (Opcional) |
| senderQualifier | Cadeia de caracteres | Qualificador de parceiro de envio. (Obrigatório) |
| senderIdentifier | Cadeia de caracteres | Identificador de parceiro de envio. (Obrigatório) |
| receiverQualifier | Cadeia de caracteres | Qualificador de parceiro de recebimento. (Obrigatório) |
| receiverIdentifier | Cadeia de caracteres | Identificador de parceiro de recebimento. (Obrigatório) |
| agreementName | Cadeia de caracteres | Nome do contrato X12 para o qual as mensagens são resolvidas. (Opcional) |
| direction | Enum | Indica a direção do fluxo de mensagens, receber ou enviar. (Obrigatório) |
| interchangeControlNumber | Cadeia de caracteres | Número de controle de intercâmbio, que popula o lado de envio quando uma confirmação técnica é recebida de parceiros. (Opcional) |
| functionalGroupControlNumber | Cadeia de caracteres | Número de controle de grupo funcional da confirmação técnica, que popula o lado de envio quando uma confirmação técnica é recebida de parceiros. (Opcional) |
| isaSegment | Cadeia de caracteres | O mesmo que o número de controle de intercâmbio, só é populado em casos específicos. (Opcional) |
| gsSegment | Cadeia de caracteres | O mesmo que o número de controle de grupo funcional, só é populado em casos específicos. (Opcional) |
| respondingfunctionalGroupControlNumber | Cadeia de caracteres | Número de controle do grupo funcional original. (Opcional) |
| respondingFunctionalGroupId | Cadeia de caracteres | Mapeia para AK101 na ID do grupo funcional de confirmação. (Opcional) |
| isMessageFailed | Booliano | Se a mensagem X12 falha ou não. (Obrigatório) |
| statusCode | Enum | O código de status de confirmação. Os valores aceitos são **Accepted**, **Rejected**, **AcceptedWithErrors**. (Obrigatório) |
| processingStatus | Enum | O status de processamento da confirmação. Os valores permitidos são **Received**, **Generated**, **Sent**. (Obrigatório) |
| ak903 | Cadeia de caracteres | Número de conjuntos de transação recebidos. (Opcional) |
| ak904 | Cadeia de caracteres | Indica o número de conjuntos de transação aceitos no grupo funcional identificado. (Opcional) |
| ak9Segment | Cadeia de caracteres | Se o grupo funcional identificado no segmento AK1 foi aceito ou rejeitado e a razão. (Opcional) |

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre o [monitoramento de mensagens de B2B](logic-apps-monitor-b2b-message.md).
* Saiba mais sobre os [esquemas de acompanhamento AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md).
* Saiba mais sobre os esquemas de [acompanhamento personalizado B2B](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md).
* Saiba mais sobre [acompanhamento de mensagens B2B no portal do Operations Management Suite](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).
* Saiba mais sobre o [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md).  
