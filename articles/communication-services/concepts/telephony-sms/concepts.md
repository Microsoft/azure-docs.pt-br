---
title: Conceitos de SMS nos Serviços de Comunicação do Azure
titleSuffix: An Azure Communication Services concept document
description: Saiba mais sobre os conceitos de SMS dos Serviços de Comunicação.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 7a02acb01e72f594f6c0fe601c4aeea25591c0e4
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102487518"
---
# <a name="sms-concepts"></a>Conceitos de SMS

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]


[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

Os Serviços de Comunicação do Azure permitem que você envie e receba mensagens de texto SMS usando as bibliotecas de cliente SMS dos Serviços de Comunicação. Essas bibliotecas de cliente podem ser usadas para dar suporte a cenários de atendimento ao cliente, lembretes de compromissos, autenticação de dois fatores e outras necessidades de comunicação em tempo real. O recurso Serviços de Comunicação de SMS permite que você envie mensagens de maneira confiável enquanto expõe as informações de taxa de envio e resposta relativas às suas campanhas.

Os principais recursos das bibliotecas de clientes de SMS dos Serviços de Comunicação do Azure incluem:

-  Experiência de configuração **simples** para adicionar funcionalidade de SMS aos seus aplicativos.
- Suporte para mensagem de **alta velocidade** em números de chamadas gratuitas para casos de uso A2P (aplicativo para pessoa) no Estados Unidos.
- Conversas **de duas vias** para oferecer suporte a cenários como suporte ao cliente, alertas e lembretes de compromissos.
- **Entrega confiável** com relatórios de entrega em tempo real para mensagens enviadas do seu aplicativo.
- **Análises** para acompanhar seus padrões de uso e a participação do cliente.
- Suporte para processamento de **recusa** para detectar e respeitar automaticamente as aceitações de números de chamadas gratuitas. As recusas de números de chamada gratuita dos EUA são obrigatórias e impostas por operadoras dos EUA.
  - STOP: se um destinatário da mensagem de texto quiser recusar a chamada, ele poderá enviar ‘STOP’ para o número de chamada gratuita. A operadora envia a seguinte resposta padrão para STOP: *"MENSAGEM DA REDE: Você respondeu com a palavra "parar", que bloqueia todos os textos enviados deste número. Envie o texto "unstop" para receber mensagens novamente."*
  - START/UNSTOP: se o destinatário quiser assinar novamente as mensagens de texto de um número de chamada gratuita, ele poderá enviar ‘START’ ou ‘UNSTOP’ para o número de chamada gratuita. A operadora envia a seguinte resposta padrão para START/UNSTOP: *“MENSAGEM DA REDE: Você respondeu “unstop” e começará a receber mensagens novamente deste número.”*
  - Os Serviços de Comunicação do Azure detectarão a mensagem STOP e bloquearão todas as mensagens posteriores ao destinatário. O relatório de entrega indicará uma falha na entrega da mensagem de status como “Remetente bloqueado para o destinatário especificado”.
  - As mensagens STOP, UNSTOP e START serão retransmitidas para você. Os Serviços de Comunicação do Azure incentivam você a monitorar e implementar essas recusas para garantir que mais nenhuma tentativa de envio de mensagem seja feita aos destinatários que recusaram suas comunicações.


## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Introdução ao envio de SMS](../../quickstarts/telephony-sms/send.md)

Os seguintes documentos podem ser do seu interesse:

- Familiarize-se com a [biblioteca de clientes de SMS](../telephony-sms/sdk-features.md)
- Obter um [número de telefone](../../quickstarts/telephony-sms/get-phone-number.md) compatível com SMS
- [Tipos de número de telefone nos Serviços de Comunicação do Azure](../telephony-sms/plan-solution.md)
