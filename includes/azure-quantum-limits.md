---
title: incluir arquivo
description: incluir arquivo
author: danielstocker
ms.service: azure-quantum
ms.topic: include
ms.date: 01/08/2021
ms.author: dasto
ms.openlocfilehash: 2106a48a583f120f8b4dde4eb32a30f1a1b1d85b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98948289"
---
### <a name="provider-limits--quota"></a>Limites de provedor & cota

O serviço Quantum do Azure dá suporte a provedores de serviços primeiro e de terceiros. Os provedores de terceiros possuem seus limites e cotas. Os usuários podem exibir ofertas e limites no portal do Azure ao configurar provedores de terceiros na folha do provedor. 

Você pode encontrar os limites de cota publicados para o provedor de soluções de otimização de primeira parte da Microsoft abaixo. 

#### <a name="learn--develop-sku"></a>Saiba & desenvolver a SKU

| Recurso | Limite |
| --- | --- |
| Trabalhos simultâneos baseados em CPU | até 5 trabalhos simultâneos |
| Trabalhos simultâneos baseados em FPGA | até 2 trabalhos simultâneos |
| Horas do resolvedor baseado em CPU | 20 horas por mês  |
| Horas do resolvedor baseado em FPGA | 1 hora por mês  |

Se você estiver usando a SKU saiba & desenvolver, **não poderá** solicitar um aumento nos limites de cota. Em vez disso, você deve mudar para o desempenho em SKU de escala.

#### <a name="performance-at-scale-sku"></a>Desempenho na SKU de escala

| Recurso | Limite padrão | Limite máximo |
| --- | --- | --- |
| Trabalhos simultâneos baseados em CPU | até 100 trabalhos simultâneos | igual ao limite padrão |
| Trabalhos simultâneos baseados em FPGA | até 10 trabalhos simultâneos | igual ao limite padrão |
| Horas do resolvedor | 1.000 horas por mês  | até 50.000 horas por mês |

Se você precisar solicitar um aumento de limite, entre em contato com o suporte do Azure. 

Para obter mais informações, consulte a [página de preços do azure Quantum](https://aka.ms/AQ/Pricing).
Para obter informações sobre ofertas de terceiros, consulte a página do provedor relevante na portal do Azure.
