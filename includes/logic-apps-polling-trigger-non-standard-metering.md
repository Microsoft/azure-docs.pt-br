---
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 11/09/2018
ms.author: estfan
ms.openlocfilehash: 3fa71085d649ace95aa24ac87c8714a7268f5386
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172515"
---
Para estimar custos de consumo mais precisos, considere o número possível de mensagens ou eventos que podem chegar em qualquer dia, em vez de basear os cálculos somente no intervalo de votação. Quando um evento ou mensagem atende aos critérios do gatilho, muitos disparadores imediatamente tentam ler todos e quaisquer outros eventos ou mensagens em espera que atendam aos critérios. Esse comportamento significa que, mesmo quando você seleciona um intervalo de sondagem mais longo, o gatilho dispara com base no número de eventos ou mensagens em espera que qualificam-se para iniciar os fluxos de trabalho. Os disparadores que seguem esse comportamento incluem o Barramento de Serviço do Azure e o Hub de Eventos do Azure.

Então, por exemplo, suponha que você configure o gatilho que verifica um ponto de extremidade todos os dias. Quando o gatilho verifica o ponto de extremidade e encontra 15 eventos que atendem aos critérios, o gatilho dispara e executa o fluxo de trabalho correspondente 15 vezes. Os Aplicativos Lógicos medem todas as ações que esses 15 fluxos de trabalho executam, incluindo as solicitações de gatilho. Para calcular os custos potenciais, experimente a [calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/).