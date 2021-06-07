---
title: Conceitos de SMS nos Serviços de Comunicação do Azure
titleSuffix: An Azure Communication Services concept document
description: Saiba mais sobre os conceitos de SMS dos Serviços de Comunicação.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: e879b7938eb778d4cdbbef4a970325501a6124db
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105932141"
---
# <a name="sms-concepts"></a>Conceitos de SMS

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include-phone-numbers.md)]

[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

Os Serviços de Comunicação do Azure permitem que você envie e receba mensagens de texto SMS usando os SDKs de SMS dos Serviços de Comunicação. Esses SDKs podem ser usados para dar suporte a cenários de atendimento ao cliente, lembretes de compromissos, autenticação de dois fatores e outras necessidades de comunicação em tempo real. O SMS dos Serviços de Comunicação permite que você envie mensagens de maneira confiável enquanto expõe métricas de taxa de envio e resposta.

Os principais recursos dos SDKs de SMS dos Serviços de Comunicação do Azure incluem:

-  Experiência de configuração **simples** para adicionar funcionalidade de SMS aos seus aplicativos.
- Suporte para mensagem de **alta velocidade** em números de chamadas gratuitas para casos de uso A2P (aplicativo para pessoa) no Estados Unidos.
- Suporte para **Mensagens em Massa**, para habilitar o envio de mensagens para vários destinatários por vez.
- Conversas **de duas vias** para oferecer suporte a cenários como suporte ao cliente, alertas e lembretes de compromissos.
- **Entrega confiável** com relatórios de entrega em tempo real para mensagens enviadas do seu aplicativo.
- **Análise** para rastrear seus padrões de uso de SMS.
- Suporte para processamento de **recusa** para detectar e respeitar automaticamente as aceitações de números de chamadas gratuitas. As recusas de números de chamada gratuita dos EUA são obrigatórias e impostas por operadoras dos EUA.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Introdução ao envio de SMS](../../quickstarts/telephony-sms/send.md)

Os seguintes documentos podem ser do seu interesse:

- Familiarizar-se com o [SDK de SMS](../telephony-sms/sdk-features.md)
- Obter um [número de telefone](../../quickstarts/telephony-sms/get-phone-number.md) compatível com SMS
- [Tipos de número de telefone nos Serviços de Comunicação do Azure](../telephony-sms/plan-solution.md)
