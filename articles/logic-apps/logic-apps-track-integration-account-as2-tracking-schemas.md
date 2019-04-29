---
title: Esquemas de acompanhamento AS2 para mensagens B2B – Aplicativos Lógicos do Azure | Microsoft Docs
description: Criar esquemas de acompanhamento AS2 que monitorem mensagens B2B nas contas de integração para os Aplicativos Lógicos do Azure com o Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: f169c411-1bd7-4554-80c1-84351247bf94
ms.date: 01/27/2017
ms.openlocfilehash: 180d90450497b38f107f3601944385a003f50282
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60845776"
---
# <a name="create-schemas-for-tracking-as2-messages-and-mdns-in-integration-accounts-for-azure-logic-apps"></a>Criar esquemas para acompanhamento de mensagens AS2 e MDNs em contas de integração para os Aplicativos Lógicos do Azure

Você pode usar esses esquemas de acompanhamento AS2 em sua conta de integração para ajudá-lo a monitorar êxito, erros e propriedades de mensagem para transações B2B (entre empresas):

* Esquema de acompanhamento de mensagens AS2
* Esquema de acompanhamento MDN AS2

## <a name="as2-message-tracking-schema"></a>Esquema de acompanhamento de mensagens AS2

```json
{
   "agreementProperties": {  
      "senderPartnerName": "",  
      "receiverPartnerName": "",  
      "as2To": "",  
      "as2From": "",  
      "agreementName": ""  
   },  
   "messageProperties": {
      "direction": "",
      "messageId": "",
      "dispositionType": "",
      "fileName": "",
      "isMessageFailed": "",
      "isMessageSigned": "",
      "isMessageEncrypted": "",
      "isMessageCompressed": "",
      "correlationMessageId": "",
      "incomingHeaders": {
       },
      "outgoingHeaders": {
       },
      "isNrrEnabled": "",
      "isMdnExpected": "",
      "mdnType": ""
    }
}
```

| Propriedade | Type | DESCRIÇÃO |
| --- | --- | --- |
| senderPartnerName | Cadeia de caracteres | O nome do parceiro do remetente da mensagem AS2. (Opcional) |
| receiverPartnerName | Cadeia de caracteres | O nome do parceiro do destinatário da mensagem AS2. (Opcional) |
| as2To | Cadeia de caracteres | Nome do destinatário da mensagem AS2, dos cabeçalhos da mensagem AS2. (Obrigatório) |
| as2From | Cadeia de caracteres | Nome do remetente da mensagem AS2, dos cabeçalhos da mensagem AS2. (Obrigatório) |
| agreementName | Cadeia de caracteres | Nome do contrato AS2 para o qual as mensagens são resolvidas. (Opcional) |
| direction | Cadeia de caracteres | Indica a direção do fluxo de mensagens, receber ou enviar. (Obrigatório) |
| messageId | Cadeia de caracteres | ID da mensagem AS2, dos cabeçalhos da mensagem AS2 (Opcional) |
| dispositionType |Cadeia de caracteres | Valor do tipo de disposição MDN (notificação de disposição de mensagem). (Opcional) |
| fileName | Cadeia de caracteres | Nome do arquivo, do cabeçalho da mensagem AS2. (Opcional) |
| isMessageFailed |Boolean | Se a mensagem AS2 falha ou não. (Obrigatório) |
| isMessageSigned | Boolean | Se a mensagem AS2 foi ou não assinada. (Obrigatório) |
| isMessageEncrypted | Boolean | Se a mensagem AS2 foi ou não criptografada. (Obrigatório) |
| isMessageCompressed |Boolean | Se a mensagem AS2 foi ou não comprimida. (Obrigatório) |
| correlationMessageId | Cadeia de caracteres | ID da mensagem AS2 para correlacionar mensagens com MDNs. (Opcional) |
| incomingHeaders |Dicionário de JToken | Detalhes do cabeçalho da mensagem de entrada AS2. (Opcional) |
| outgoingHeaders |Dicionário de JToken | Detalhes do cabeçalho da mensagem de saída AS2. (Opcional) |
| isNrrEnabled | Boolean | Use o valor padrão se o valor não for conhecido. (Obrigatório) |
| isMdnExpected | Booliano | Use o valor padrão se o valor não for conhecido. (Obrigatório) |
| mdnType | Enum | Os valores permitidos são **NotConfigured**, **Sync** e **Async**. (Obrigatório) |
||||

## <a name="as2-mdn-tracking-schema"></a>Esquema de acompanhamento MDN AS2

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "as2To": "",
      "as2From": "",
      "agreementName": "g"
   },
   "messageProperties": {
      "direction": "",
      "messageId": "",
      "originalMessageId": "",
      "dispositionType": "",
      "isMessageFailed": "",
      "isMessageSigned": "",
      "isNrrEnabled": "",
      "statusCode": "",
      "micVerificationStatus": "",
      "correlationMessageId": "",
      "incomingHeaders": {
      },
      "outgoingHeaders": {
      }
   }
}
```

| Propriedade | Type | DESCRIÇÃO |
| --- | --- | --- |
| senderPartnerName | Cadeia de caracteres | O nome do parceiro do remetente da mensagem AS2. (Opcional) |
| receiverPartnerName | Cadeia de caracteres | O nome do parceiro do destinatário da mensagem AS2. (Opcional) |
| as2To | Cadeia de caracteres | O nome do parceiro que recebe a mensagem AS2. (Obrigatório) |
| as2From | Cadeia de caracteres | O nome do parceiro que envia a mensagem AS2. (Obrigatório) |
| agreementName | Cadeia de caracteres | Nome do contrato AS2 para o qual as mensagens são resolvidas. (Opcional) |
| direction |Cadeia de caracteres | Indica a direção do fluxo de mensagens, receber ou enviar. (Obrigatório) |
| messageId | Cadeia de caracteres | ID da mensagem AS2. (Opcional) |
| originalMessageId |Cadeia de caracteres | ID da mensagem AS2 original. (Opcional) |
| dispositionType | Cadeia de caracteres | Valor do tipo de disposição MDN. (Opcional) |
| isMessageFailed |Boolean | Se a mensagem AS2 falha ou não. (Obrigatório) |
| isMessageSigned |Boolean | Se a mensagem AS2 foi ou não assinada. (Obrigatório) |
| isNrrEnabled | Boolean | Use o valor padrão se o valor não for conhecido. (Obrigatório) |
| statusCode | Enum | Os valores aceitos são **Accepted**, **Rejected**, **AcceptedWithErrors**. (Obrigatório) |
| micVerificationStatus | Enum | Os valores permitidos são **NotApplicable**, **Succeeded** ou **Failed**. (Obrigatório) |
| correlationMessageId | Cadeia de caracteres | ID de correlação. A ID da mensagem original (a ID da mensagem para a qual o MDN está configurado). (Opcional) |
| incomingHeaders | Dicionário de JToken | Indica detalhes do cabeçalho da mensagem de entrada. (Opcional) |
| outgoingHeaders |Dicionário de JToken | Indica detalhes do cabeçalho da mensagem de saída. (Opcional) |
||||

## <a name="b2b-protocol-tracking-schemas"></a>Esquemas de acompanhamento do protocolo B2B

Para obter informações sobre esquemas de acompanhamento do protocolo B2B, veja:

* [Esquemas de acompanhamento de X12](logic-apps-track-integration-account-x12-tracking-schema.md)
* [Esquemas de acompanhamento B2B personalizados](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre o [monitoramento de mensagens B2B](logic-apps-monitor-b2b-message.md)
* Saiba mais sobre [rastreando mensagens B2B nos logs do Azure Monitor](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)