---
title: Início Rápido – Enviar uma mensagem SMS
titleSuffix: An Azure Communication Services quickstart
description: Saiba como enviar uma mensagem SMS usando os Serviços de Comunicação do Azure.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.custom: tracking-python, devx-track-js
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: ee6892af2a7ea119eb4110fa28301b08320f8b9f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105726389"
---
# <a name="quickstart-send-an-sms-message"></a>Início Rápido: Enviar uma mensagem SMS

[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

> [!IMPORTANT]
> As mensagens SMS podem ser enviadas e recebidas de números de telefone nos Estados Unidos. Ainda não há suporte para os números de telefone localizados em outras regiões geográficas no SMS dos Serviços de Comunicação.
> Para obter mais informações, confira **[Tipos de número de telefone](../../concepts/telephony-sms/plan-solution.md)** .

::: zone pivot="programming-language-csharp"
[!INCLUDE [Send SMS with .NET SDK](./includes/send-sms-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [Send SMS with JavaScript SDK](./includes/send-sms-js.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Send SMS with Python SDK](./includes/send-sms-python.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Send SMS with Java SDK](./includes/send-sms-java.md)]
::: zone-end

## <a name="troubleshooting"></a>Solução de problemas

Para solucionar problemas relacionados à entrega de SMS, você pode [habilitar o relatório de entrega com a Grade de Eventos](./handle-sms-events.md) para capturar os detalhes da entrega.

## <a name="clean-up-resources"></a>Limpar os recursos

Se quiser limpar e remover uma assinatura dos Serviços de Comunicação, exclua o recurso ou o grupo de recursos. Excluir o grupo de recursos também exclui todos os recursos associados a ele. Saiba mais sobre [como limpar recursos](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu a enviar mensagens SMS usando os Serviços de Comunicação do Azure.

> [!div class="nextstepaction"]
> [Receber eventos de notificação de entrega e SMS](./handle-sms-events.md)

> [!div class="nextstepaction"]
> [Tipos de número de telefone](../../concepts/telephony-sms/plan-solution.md)

> [!div class="nextstepaction"]
> [Saiba mais sobre SMS](../../concepts/telephony-sms/concepts.md)
