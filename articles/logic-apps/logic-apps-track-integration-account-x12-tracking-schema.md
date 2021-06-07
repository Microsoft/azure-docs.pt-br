---
title: Esquemas de acompanhamento de X12 para mensagens B2B
description: Criar esquemas de acompanhamento para monitorar mensagens X12 para aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/01/2020
ms.openlocfilehash: 5b2df194761ebc167e67498a985960a4fce35f19
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "76905293"
---
# <a name="create-schemas-for-tracking-x12-messages-in-azure-logic-apps"></a>Criar esquemas para acompanhamento de mensagens X12 em aplicativos lógicos do Azure

Você pode usar esses esquemas de acompanhamento X12 em sua conta de integração para ajudá-lo a monitorar êxito, erros e propriedades de mensagem para transações B2B (entre empresas):

* Esquema de acompanhamento do conjunto de transações X12
* Esquema de acompanhamento de confirmação do conjunto de transações X12
* Esquema de acompanhamento de intercâmbio X12
* Esquema de controle de confirmações do X12 Interchange
* Esquema de acompanhamento de grupo funcional X12
* Esquema de acompanhamento de confirmação de grupo funcional X12

## <a name="x12-transaction-set-tracking-schema"></a>Esquema de acompanhamento do conjunto de transações X12

```json
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
      "needAk2LoopForValidMessages": "",
      "segmentsCount": ""
   }
}
```

| Propriedade | Obrigatório | Type | Descrição |
|----------|----------|------|-------------|
| senderPartnerName | Não | String | Nome do parceiro do remetente da mensagem do X12 |
| receiverPartnerName | Não | String | Nome do parceiro do destinatário da mensagem do X12 |
| senderQualifier | Sim | String | Qualificador de parceiro de envio |
| senderIdentifier | Sim | String | Identificador de parceiro de envio |
| receiverQualifier | Sim | String | Qualificador de parceiro de recebimento |
| receiverIdentifier | Sim | String | Identificador de parceiro de recebimento |
| agreementName | Não | String | Nome do contrato X12 para o qual as mensagens são resolvidas |
| direction | Sim | Enumeração | Direção do fluxo de mensagens, que é `receive` ou `send` |
| interchangeControlNumber | Não | String | Número de controle de intercâmbio |
| functionalGroupControlNumber | Não | String | Número de controle funcional |
| transactionSetControlNumber | Não | String | Número de controle de conjunto de transação |
| CorrelationMessageId | Não | String | ID da mensagem de correlação, que é uma combinação de {Agreementname} {*GroupControlNumber*} {TransactionSetControlNumber} |
| messageType | Não | String | Conjunto de transações ou tipo de documento |
| isMessageFailed | Sim | Boolean | Se a mensagem X12 falhou |
| isTechnicalAcknowledgmentExpected | Sim | Boolean | Se a confirmação técnica está configurada no contrato X12 |
| isFunctionalAcknowledgmentExpected | Sim | Boolean | Se a confirmação funcional está configurada no contrato X12 |
| needAk2LoopForValidMessages | Sim | Boolean | Se o loop AK2 é necessário para uma mensagem válida |
| segmentsCount | Não | Inteiro | Número de segmentos no conjunto de transações X12 |
|||||

## <a name="x12-transaction-set-acknowledgment-tracking-schema"></a>Esquema de acompanhamento de confirmação do conjunto de transações X12

```json
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
      "CorrelationMessageId": "",
      "isMessageFailed": "",
      "ak2Segment": "",
      "ak3Segment": "",
      "ak5Segment": ""
   }
}
```

| Propriedade | Obrigatório | Type | Descrição |
|----------|----------|------|-------------|
| senderPartnerName | Não | String | Nome do parceiro do remetente da mensagem do X12 |
| receiverPartnerName | Não | String | Nome do parceiro do destinatário da mensagem do X12 |
| senderQualifier | Sim | String | Qualificador de parceiro de envio |
| senderIdentifier | Sim | String | Identificador de parceiro de envio |
| receiverQualifier | Sim | String | Qualificador de parceiro de recebimento |
| receiverIdentifier | Sim | String | Identificador de parceiro de recebimento |
| agreementName | Não | String | Nome do contrato X12 para o qual as mensagens são resolvidas |
| direction | Sim | Enumeração | Direção do fluxo de mensagens, que é `receive` ou `send` |
| interchangeControlNumber | Não | String | O número de controle de intercâmbio da confirmação funcional. O valor é preenchido apenas para o lado de envio onde a confirmação funcional é recebida para as mensagens enviadas ao parceiro. |
| functionalGroupControlNumber | Não | String | Número de controle de grupo funcional da confirmação funcional. O valor é preenchido apenas para o lado de envio onde a confirmação funcional é recebida para as mensagens enviadas ao parceiro |
| isaSegment | Não | String | O segmento ISA da mensagem. O valor é preenchido apenas para o lado de envio onde a confirmação funcional é recebida para as mensagens enviadas ao parceiro |
| gsSegment | Não | String | O segmento GS da mensagem. O valor é preenchido apenas para o lado de envio onde a confirmação funcional é recebida para as mensagens enviadas ao parceiro |
| respondingfunctionalGroupControlNumber | Não | String | O número de controle de intercâmbio de resposta |
| respondingFunctionalGroupId | Não | String | A ID do grupo funcional de resposta, que é mapeada para AK101 na confirmação |
| respondingtransactionSetControlNumber | Não | String | O número de controle do conjunto de transações de resposta |
| respondingTransactionSetId | Não | String | A ID do conjunto de transações de resposta, que é mapeada para AK201 na confirmação |
| statusCode | Sim | Boolean | Código de status de confirmação do conjunto de transações |
| segmentsCount | Sim | Enumeração | Código de status de confirmação com estes valores permitidos: `Accepted` , `Rejected` e `AcceptedWithErrors` |
| processingStatus | Sim | Enumeração | Status de processamento da confirmação com estes valores permitidos: `Received` , `Generated` e `Sent` |
| CorrelationMessageId | Não | String | ID da mensagem de correlação, que é uma combinação de {Agreementname} {*GroupControlNumber*} {TransactionSetControlNumber} |
| isMessageFailed | Sim | Boolean | Se a mensagem X12 falhou |
| ak2Segment | Não | String | Confirmação de um conjunto de transações dentro do grupo funcional recebido |
| ak3Segment | Não | String | Relata erros em um segmento de dados |
| ak5Segment | Não | String | Relata se o conjunto de transações identificado no segmento AK2 é aceito ou rejeitado, e por quê |
|||||

## <a name="x12-interchange-tracking-schema"></a>Esquema de acompanhamento de intercâmbio X12

```json
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
```

| Propriedade | Obrigatório | Type | Descrição |
|----------|----------|------|-------------|
| senderPartnerName | Não | String | Nome do parceiro do remetente da mensagem do X12 |
| receiverPartnerName | Não | String | Nome do parceiro do destinatário da mensagem do X12 |
| senderQualifier | Sim | String | Qualificador de parceiro de envio |
| senderIdentifier | Sim | String | Identificador de parceiro de envio |
| receiverQualifier | Sim | String | Qualificador de parceiro de recebimento |
| receiverIdentifier | Sim | String | Identificador de parceiro de recebimento |
| agreementName | Não | String | Nome do contrato X12 para o qual as mensagens são resolvidas |
| direction | Sim | Enumeração | Direção do fluxo de mensagens, que é `receive` ou `send` |
| interchangeControlNumber | Não | String | Número de controle de intercâmbio |
| isaSegment | Não | String | Segmento ISA de mensagem |
| isTechnicalAcknowledgmentExpected | Boolean | Se a confirmação técnica está configurada no contrato X12  |
| isMessageFailed | Sim | Boolean | Se a mensagem X12 falhou |
| isa09 | Não | String | Data de intercâmbio do documento X12 |
| isa10 | Não | String | Horário de intercâmbio do documento X12 |
| isa11 | Não | String | Identificador de padrões de controle de intercâmbio X12 |
| isa12 | Não | String | Número de versão do controle de intercâmbio X12 |
| isa14 | Não | String | A confirmação do X12 é solicitada |
| isa15 | Não | String | Indicador para teste ou produção |
| isa16 | Não | String | Separador de elemento |
|||||

## <a name="x12-interchange-acknowledgment-tracking-schema"></a>Esquema de controle de confirmações do X12 Interchange

```json
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
```

| Propriedade | Obrigatório | Type | Descrição |
|----------|----------|------|-------------|
| senderPartnerName | Não | String | Nome do parceiro do remetente da mensagem do X12 |
| receiverPartnerName | Não | String | Nome do parceiro do destinatário da mensagem do X12 |
| senderQualifier | Sim | String | Qualificador de parceiro de envio |
| senderIdentifier | Sim | String | Identificador de parceiro de envio |
| receiverQualifier | Sim | String | Qualificador de parceiro de recebimento |
| receiverIdentifier | Sim | String | Identificador de parceiro de recebimento |
| agreementName | Não | String | Nome do contrato X12 para o qual as mensagens são resolvidas |
| direction | Sim | Enumeração | Direção do fluxo de mensagens, que é `receive` ou `send` |
| interchangeControlNumber | Não | String | Número de controle de intercâmbio da confirmação técnica recebida de parceiros |
| isaSegment | Não | String | Segmento ISA para confirmação técnica recebida de parceiros |
| respondingInterchangeControlNumber | Não | String | Número de controle de intercâmbio para a confirmação técnica recebida de parceiros |
| isMessageFailed | Sim | Boolean | Se a mensagem X12 falhou |
| statusCode | Sim | Enumeração | Código de status de confirmação do intercâmbio com estes valores permitidos: `Accepted` , `Rejected` e `AcceptedWithErrors` |
| processingStatus | Sim | Enumeração | Status de confirmação com estes valores permitidos: `Received` , `Generated` e `Sent` |
| ta102 | Não | String | Data de intercâmbio |
| ta103 | Não | String | Hora do intercâmbio |
| ta105 | Não | String | Código de nota de intercâmbio |
|||||

## <a name="x12-functional-group-tracking-schema"></a>Esquema de acompanhamento de grupo funcional X12

```json
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
```

| Propriedade | Obrigatório | Type | Descrição |
|----------|----------|------|-------------|
| senderPartnerName | Não | String | Nome do parceiro do remetente da mensagem do X12 |
| receiverPartnerName | Não | String | Nome do parceiro do destinatário da mensagem do X12 |
| senderQualifier | Sim | String | Qualificador de parceiro de envio |
| senderIdentifier | Sim | String | Identificador de parceiro de envio |
| receiverQualifier | Sim | String | Qualificador de parceiro de recebimento |
| receiverIdentifier | Sim | String | Identificador de parceiro de recebimento |
| agreementName | Não | String | O nome do contrato X12 para o qual as mensagens são resolvidas |
| direction | Sim | Enumeração | Direção do fluxo de mensagens, receber ou enviar |
| interchangeControlNumber | Não | String | Número de controle de intercâmbio |
| functionalGroupControlNumber | Não | String | Número de controle funcional |
| gsSegment | Não | String | Segmento GS de mensagem |
| isTechnicalAcknowledgmentExpected | Sim | Boolean | Se a confirmação técnica está configurada no contrato X12 |
| isFunctionalAcknowledgmentExpected | Sim | Boolean | Se a confirmação funcional está configurada no contrato X12 |
| isMessageFailed | Sim | Boolean | Se a mensagem X12 falhou |
| gs01 | Não | String | Código de identificador funcional |
| gs02 | Não | String | Código do remetente do aplicativo |
| gs03 | Não | String | Código do receptor do aplicativo |
| gs04 | Não | String | Data do grupo funcional |
| gs05 | Não | String | Hora do grupo funcional |
| gs07 | Não | String | Código da agência responsável |
| gs08 | Não | String | Código de identificação para a versão, versão ou setor |
|||||

## <a name="x12-functional-group-acknowledgment-tracking-schema"></a>Esquema de acompanhamento de confirmação de grupo funcional X12

```json
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
```

| Propriedade | Obrigatório | Type | Descrição |
|----------|----------|------|-------------|
| senderPartnerName | Não | String | Nome do parceiro do remetente da mensagem do X12 |
| receiverPartnerName | Não | String | Nome do parceiro do destinatário da mensagem do X12 |
| senderQualifier | Sim | String | Qualificador de parceiro de envio |
| senderIdentifier | Sim | String | Identificador de parceiro de envio |
| receiverQualifier | Sim | String | Qualificador de parceiro de recebimento |
| receiverIdentifier | Sim | String | Identificador de parceiro de recebimento |
| agreementName | Não | String | Nome do contrato X12 para o qual as mensagens são resolvidas |
| direction | Sim | Enumeração | Direção do fluxo de mensagens, que é `receive` ou `send` |
| interchangeControlNumber | Não | String | Número de controle de intercâmbio, que preenche o lado de envio quando uma confirmação técnica é recebida de parceiros |
| functionalGroupControlNumber | Não | String | Número de controle de grupo funcional da confirmação técnica, que preenche o lado de envio quando uma confirmação técnica é recebida de parceiros |
| isaSegment | Não | String | Mesmo que o número de controle de intercâmbio, mas preenchido apenas em casos específicos |
| gsSegment | Não | String | Mesmo que o número de controle de grupo funcional, mas preenchido somente em casos específicos |
| respondingfunctionalGroupControlNumber | Não | String | Número de controle do grupo funcional original |
| respondingFunctionalGroupId | Não | String | Mapeia para AK101 na ID do grupo funcional de confirmação |
| isMessageFailed | Boolean | Se a mensagem X12 falhou |
| statusCode | Sim | Enumeração | Código de status de confirmação com estes valores permitidos: `Accepted` , `Rejected` e `AcceptedWithErrors` |
| processingStatus | Sim | Enumeração | Status de processamento da confirmação com estes valores permitidos: `Received` , `Generated` e `Sent` |
| ak903 | Não | String | Número de conjuntos de transações recebidos |
| ak904 | Não | String | Número de conjuntos de transações aceitos no grupo funcional identificado |
| ak9Segment | Não | String | Se o grupo funcional identificado no segmento AK1 é aceito ou rejeitado, e por quê |
|||||

## <a name="b2b-protocol-tracking-schemas"></a>Esquemas de acompanhamento do protocolo B2B

Para obter informações sobre esquemas de acompanhamento do protocolo B2B, veja:

* [Esquemas de acompanhamento do AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Esquemas de acompanhamento B2B personalizados](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Próximas etapas

* [Monitorar mensagens B2B com os logs do Azure Monitor](../logic-apps/monitor-b2b-messages-log-analytics.md)