---
title: Configurações de mensagem do AS2 - aplicativos lógicos do Azure
description: Guia de referência para o AS2 enviar e receber as configurações de aplicativo lógico do Azure com o Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 04/22/2019
ms.openlocfilehash: ead92094b9af1dff56ff68e1ff58a3a4cdd9dca5
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63769445"
---
# <a name="reference-for-as2-message-settings-in-azure-logic-apps-with-enterprise-integration-pack"></a>Referência para as configurações de mensagem do AS2 nos aplicativos de lógicos do Azure com o Enterprise Integration Pack

Esta referência descreve as propriedades que podem ser definidas para especificar como um contrato AS2 lida com mensagens enviadas e recebidas entre parceiros comerciais. Configure essas propriedades com base no seu contrato com o parceiro que troca mensagens com você.

<a name="AS2-incoming-messages"></a>

## <a name="as2-receive-settings"></a>Configurações de recebimento AS2

![Selecione "Configurações de recebimento"](./media/logic-apps-enterprise-integration-as2-message-settings/receive-settings.png)

| Propriedade | Obrigatório | DESCRIÇÃO |
|----------|----------|-------------|
| **Substituir as propriedades de mensagem** | Não  | Substitui as propriedades nas mensagens de entrada com as configurações de propriedade. |
| **Mensagem deve ser assinada** | Não  | Especifica se todas as mensagens de entrada devem ser assinadas digitalmente. Se você precisar entrar, do **certificado** , selecione um certificado de público de parceiro existente do convidado para validar a assinatura nas mensagens. Se você não tiver um certificado, saiba mais sobre [adicionando certificados](../logic-apps/logic-apps-enterprise-integration-certificates.md). |
| **Mensagem deve ser criptografada** | Não  | Especifica se todas as mensagens de entrada devem ser criptografadas. Mensagens não criptografadas são rejeitadas. Se você precisar de criptografia, do **certificado** , selecione um certificado de privado de parceiro existente do host para descriptografar as mensagens de entrada. Se você não tiver um certificado, saiba mais sobre [adicionando certificados](../logic-apps/logic-apps-enterprise-integration-certificates.md). |
| **A mensagem deve ser compactada** | Não  | Especifica se todas as mensagens de entrada devem ser compactadas. Mensagens não compactadas são rejeitadas. |
| **Recusar duplicatas de ID da mensagem** | Não  | Especifica se deve permitir mensagens com IDs duplicadas. Se você não permitir IDs duplicadas, selecione o número de dias entre as verificações. Você também pode escolher se deve suspender as duplicatas. |
| **MDN Text** | Não  | Especifica a padrão mensagem notificação de disposição (MDN) ser enviada ao remetente da mensagem. |
| **Enviar MDN** | Não  | Especifica se deve enviar MDNs síncronas para mensagens recebidas.  |
| **Enviar MDN assinada** | Não  | Especifica se deve enviar MDNs assinadas para mensagens recebidas. Se você precisar entrar, do **algoritmo MIC** , selecione o algoritmo a ser usado para assinar mensagens. |
| **Enviar MDN assíncrona** | Não  | Especifica se deve enviar MDNs de forma assíncrona. Se você selecionar MDNs assíncronas, o **URL** caixa, especifique a URL para onde enviar as MDNs. |
||||

<a name="AS2-outgoing-messages"></a>

## <a name="as2-send-settings"></a>Configurações de envio AS2

![Selecione "Configurações de envio"](./media/logic-apps-enterprise-integration-as2-message-settings/send-settings.png)

| Propriedade | Obrigatório | DESCRIÇÃO |
|----------|----------|-------------|
| **Habilitar assinatura de mensagens** | Não  | Especifica se todas as mensagens de saída devem ser assinadas digitalmente. Se você precisar de assinatura, selecione estes valores: <p>-From a **algoritmo de assinatura de** , selecione o algoritmo a ser usado para assinar mensagens. <br>-From a **certificado** , selecione um certificado de privado de parceiro existente do host para assinar mensagens. Se você não tiver um certificado, saiba mais sobre [adicionando certificados](../logic-apps/logic-apps-enterprise-integration-certificates.md). |
| **Habilitar a criptografia de mensagem** | Não  | Especifica se todas as mensagens de saída devem ser criptografadas. Se você precisar de criptografia, selecione estes valores: <p>-From a **algoritmo de criptografia** , selecione o algoritmo de certificado público do parceiro convidado a ser usado para criptografar mensagens. <br>-From a **certificado** , selecione um certificado de privado de parceiro existente do convidado para criptografar mensagens de saída. Se você não tiver um certificado, saiba mais sobre [adicionando certificados](../logic-apps/logic-apps-enterprise-integration-certificates.md). |
| **Habilitar a compactação de mensagem** | Não  | Especifica se todas as mensagens enviadas devem ser compactadas. |
| **Desdobrar cabeçalhos HTTP** | Não  | Coloca o HTTP `content-type` cabeçalho em uma única linha. |
| **Transmitir o nome do arquivo no cabeçalho MIME** | Não  | Especifica se deve incluir o nome do arquivo no cabeçalho MIME. |
| **Solicitar MDN** | Não  | Especifica se deseja receber notificações de disposição de mensagem (MDNs) para todas as mensagens de saída. |
| **Solicitar MDN assinada** | Não  | Especifica se é receber MDNs assinadas para todas as mensagens enviadas. Se você precisar entrar, do **algoritmo MIC** , selecione o algoritmo a ser usado para assinar mensagens. |
| **Solicitar MDN assíncrona** | Não  | Especifica se deve receber MDNs de forma assíncrona. Se você selecionar MDNs assíncronas, o **URL** caixa, especifique a URL para onde enviar as MDNs. |
| **Enable NRR** | Não  | Especifica se é necessário não-repúdio recebimento (NRR). Esse atributo de comunicação fornece evidência de que os dados foram recebidos conforme endereçados. |
| **Formato de algoritmo SHA2** | Não  | Especifica o formato de algoritmo MIC usado para a assinatura nos cabeçalhos para as mensagens de saída AS2 ou MDN |
||||

## <a name="next-steps"></a>Próximos passos

[Trocar mensagens AS2](../logic-apps/logic-apps-enterprise-integration-as2.md)
