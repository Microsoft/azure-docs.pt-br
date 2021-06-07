---
title: incluir arquivo
description: arquivo de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/28/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: cf9d4c3fd96df83361e7d9aa89ba702d37265ec6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95553410"
---
**Não há suporte** para rotas definidas pelo usuário com um destino 0.0.0.0/0 e NSGs no GatewaySubnet. Os gateways criados com essa configuração terão a criação bloqueada. Os gateways exigem acesso aos controladores de gerenciamento para funcionar corretamente. A [Propagação de Rotas BGP](../articles/virtual-network/virtual-networks-udr-overview.md#border-gateway-protocol) deve ser definida como "Habilitada" no GatewaySubnet para garantir a disponibilidade do gateway. Se ela estiver definida como desabilitada, o gateway não funcionará.