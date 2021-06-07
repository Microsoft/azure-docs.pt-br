---
title: PERGUNTAS FREQUENTES SOBRE SMS
titleSuffix: An Azure Communication Services concept document
description: PERGUNTAS FREQUENTES SOBRE SMS
author: prakulka
manager: nmurav
services: azure-communication-services
ms.author: prakulka
ms.date: 03/26/2021
ms.topic: reference
ms.service: azure-communication-services
ms.openlocfilehash: 1ba7c730542adb74356d71f2482cce57e633cb65
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105645966"
---
# <a name="sms-faq"></a>PERGUNTAS FREQUENTES SOBRE SMS

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]
## <a name="can-a-customer-use-azure-communication-services-for-emergency-purposes"></a>Um cliente pode usar os serviços de comunicação do Azure para fins de emergência?

Os serviços de comunicação do Azure não dão suporte à funcionalidade de texto para 911 no Estados Unidos, mas é possível que você tenha a obrigação de fazer isso sob as regras da FCC (Federal Communications Commission).  Você deve avaliar se as regras de texto para 911 da FCC se aplicam ao seu serviço ou aplicativo. Na medida em que você é coberto por essas regras, você será responsável por rotear mensagens de texto 911 para centros de chamadas de emergência que as solicitem. Você está livre para determinar seu próprio modelo de entrega de texto para 911, mas uma abordagem aceita pela FCC envolve a inicialização automática da discagem nativa no dispositivo móvel do usuário para fornecer textos de 911 por meio da operadora de celular subjacente.

## <a name="are-there-any-limits-on-sending-messages"></a>Há algum limite no envio de mensagens?

Para garantir que continuemos oferecendo a alta qualidade de serviço consistente com nossos SLAs, os serviços de comunicação do Azure aplicam limites de taxa (diferentes para cada primitivo). Os desenvolvedores que chamam nossas APIs além do limite receberão uma resposta de código de status HTTP 429. Se sua empresa tiver requisitos que excedem os limites de taxa, envie um email para phone@microsoft.com .

Limites de taxa para o SMS:

|Operação|Escopo|Período (s)| Limite (solicitação n º) | Unidades de mensagem por minuto|
|---------|-----|-------------|-------------------|-------------------------|
|Enviar Mensagem|Por número|60|200|200|

## <a name="how-does-azure-communication-services-handle-opt-outs-for-toll-free-numbers"></a>Como os serviços de comunicação do Azure lidam com a aceitação de números gratuitos?

As recusas de números de chamada gratuita dos EUA são obrigatórias e impostas por operadoras dos EUA.
- STOP: se um destinatário da mensagem de texto quiser recusar a chamada, ele poderá enviar ‘STOP’ para o número de chamada gratuita. A operadora envia a seguinte resposta padrão para STOP: "mensagem de rede: você respondeu com a palavra" Stop "que bloqueia todos os textos enviados deste número. Faça o retorno de texto de "parar" para receber mensagens novamente. "
- START/UNSTOP: se o destinatário quiser assinar novamente as mensagens de texto de um número de chamada gratuita, ele poderá enviar ‘START’ ou ‘UNSTOP’ para o número de chamada gratuita. A operadora envia a seguinte resposta padrão para START/UNSTOP: “MENSAGEM DA REDE: Você respondeu “unstop” e começará a receber mensagens novamente deste número.”
- Os Serviços de Comunicação do Azure detectarão a mensagem STOP e bloquearão todas as mensagens posteriores ao destinatário. O relatório de entrega indicará uma falha na entrega da mensagem de status como “Remetente bloqueado para o destinatário especificado”.
- As mensagens STOP, UNSTOP e START serão retransmitidas para você. Os Serviços de Comunicação do Azure incentivam você a monitorar e implementar essas recusas para garantir que mais nenhuma tentativa de envio de mensagem seja feita aos destinatários que recusaram suas comunicações.

## <a name="how-can-i-receive-messages-using-azure-communication-services"></a>Como posso receber mensagens usando os serviços de comunicação do Azure?

Os clientes dos serviços de comunicação do Azure podem usar a grade de eventos do Azure para receber mensagens de entrada. Siga este guia de [início rápido](https://docs.microsoft.com/azure/communication-services/quickstarts/telephony-sms/handle-sms-events) para configurar sua grade de eventos para receber mensagens.

## <a name="can-i-sendreceive-long-messages-2048-chars"></a>Posso enviar/receber mensagens longas (>caracteres de 2048)?

Os serviços de comunicação do Azure dão suporte ao envio e recebimento de mensagens longas por SMS. No entanto, algumas operadoras ou dispositivos sem fio podem atuar de forma diferente ao receber mensagens longas.

## <a name="how-are-messages-sent-to-landline-numbers-treated"></a>Como as mensagens enviadas para números fixos são tratadas?

No Estados Unidos, os serviços de comunicação do Azure não verificam números de telefone fixo e tentarão enviá-los para as operadoras para entrega. Os clientes serão cobrados pelas mensagens enviadas aos números fixos. 

## <a name="can-i-send-messages-to-multiple-recipients"></a>Posso enviar mensagens para vários destinatários?


Sim, você pode fazer uma solicitação com vários destinatários. Siga este guia de [início rápido](https://docs.microsoft.com/azure/communication-services/quickstarts/telephony-sms/send?pivots=programming-language-csharp) para enviar mensagens para vários destinatários.
