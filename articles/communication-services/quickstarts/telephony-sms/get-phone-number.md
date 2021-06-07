---
title: Guia de Início Rápido – Gerenciar números de telefone usando os Serviços de Comunicação do Azure
description: Saiba como gerenciar números de telefone usando os Serviços de Comunicação do Azure
author: prakulka
manager: nmurav
services: azure-communication-services
ms.author: prakulka
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.custom: references_regions
zone_pivot_groups: acs-azp-java-net-python-csharp-js
ms.openlocfilehash: 19bb79f9a4deaebfacc75918c46a5516d2d398be
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107498179"
---
# <a name="quickstart-manage-phone-numbers"></a>Guia de Início Rápido: Gerenciar números de telefone

[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

[!INCLUDE [Bulk Acquisition Instructions](../../includes/phone-number-special-order.md)]

::: zone pivot="platform-azp"
[!INCLUDE [Azure portal](./includes/phone-numbers-portal.md)]
::: zone-end

::: zone pivot="programming-language-csharp"
[!INCLUDE [Azure portal](./includes/phone-numbers-net.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/phone-numbers-java.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/phone-numbers-python.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/phone-numbers-js.md)]
::: zone-end

## <a name="troubleshooting"></a>Solução de problemas

Perguntas e problemas comuns:

- Só há suporte para a compra de telefone nos EUA. Para comprar números de telefone, verifique se:
  - O endereço de cobrança associado da assinatura do Azure está localizado nos Estados Unidos. No momento, não é possível mover um recurso para outra assinatura.
  - O recurso de Serviços de Comunicação é provisionado no local de dados do Estados Unidos. No momento, não é possível mover um recurso para outro local de dados.

- Quando um número de telefone é liberado, ele não é liberado nem pode ser readquirido até o final do período de cobrança.

- Quando um recurso dos Serviços de Comunicação é excluído, os números de telefone associados a ele são liberados automaticamente ao mesmo tempo.

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você aprendeu a:

> [!div class="checklist"]
> * Comprar um número de telefone
> * Gerenciar seu número de telefone
> * Liberar um número de telefone

> [!div class="nextstepaction"]
> [Enviar SMS](../telephony-sms/send.md)
> [Introdução às chamadas](../voice-video-calling/getting-started-with-calling.md)
