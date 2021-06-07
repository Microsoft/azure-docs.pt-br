---
author: cherylmc
ms.date: 12/13/2019
ms.service: expressroute
ms.topic: include
ms.author: cherylmc
ms.openlocfilehash: 7aee201b0419b65c7ea8ddcb6f2d42ffaff4711e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101750040"
---
|  | **Emparelhamento privado** | **Emparelhamento da Microsoft** |  **Pareamento público** (preterido para novos circuitos) |
| --- | --- | --- | --- |
| **Número máximo de prefixos com suporte por emparelhamento** |4000 por padrão, 10.000 com o ExpressRoute Premium |200 |200 |
| **Intervalos de endereços IP com suporte** |Todos os endereços IP válidos em sua WAN. |Os endereços IP públicos pertencentes a você ou ao seu provedor de conectividade. |Os endereços IP públicos pertencentes a você ou ao seu provedor de conectividade. |
| **Requisitos do número do AS** |Números públicos e privados do AS. Você deve possuir número público do AS, se você optar por usar um. |Números públicos e privados do AS. No entanto, você deve comprovar a propriedade de endereços IP públicos. |Números públicos e privados do AS. No entanto, você deve comprovar a propriedade de endereços IP públicos. |
| **Protocolos IP com suporte**| IPv4, IPv6 (versão prévia) |  IPv4, IPv6 | IPv4 |
| **Roteando endereços IP de interface** |RFC1918 e endereços IP públicos |Endereços IP públicos registrados para você em registros de roteamento. |Endereços IP públicos registrados para você em registros de roteamento. |
| **Suporte a Hash MD5** |Sim |Sim |Sim |