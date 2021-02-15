---
title: O que é um hub virtual seguro?
description: Saiba mais sobre hubs virtuais seguros
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 10/12/2020
ms.author: victorh
ms.openlocfilehash: c840bf9e82b8dcdb1fbf9b380ea847b3d1b08dd9
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/12/2020
ms.locfileid: "91948061"
---
# <a name="what-is-a-secured-virtual-hub"></a>O que é um hub virtual seguro?

Um hub virtual é uma rede virtual gerenciada pela Microsoft que permite a conectividade de outros recursos. Quando um hub virtual é criado de uma WAN virtual no portal do Azure, uma VNet de Hub virtual e gateways (opcional) são criados como seus componentes.

Um hub virtual *protegido* é um [Hub de WAN virtual do Azure](../virtual-wan/virtual-wan-about.md#resources) com políticas de roteamento e segurança associadas configuradas pelo Gerenciador de firewall do Azure. Use hubs virtuais protegidos para criar facilmente arquiteturas de Hub e spoke e transitivas com serviços de segurança nativos para governança e proteção de tráfego. 

Você pode usar um hub virtual seguro para filtrar o tráfego entre redes virtuais (V2V), redes virtuais e filiais (B2V) e o tráfego para a Internet (B2I/V2I). Um hub virtual protegido fornece roteamento automatizado. Não é necessário configurar seu próprio UDRs (rotas definidas pelo usuário) para rotear o tráfego pelo firewall.

Você pode escolher os provedores de segurança necessários para proteger e governar o tráfego de rede, incluindo o Firewall do Azure, provedores de SECaaS (segurança como serviço) de terceiros ou ambos. Atualmente, um hub protegido não oferece suporte a filtragem e filtragem de B2B (Branch para Branch) em vários hubs. Para saber mais, consulte [o que é o Gerenciador de firewall do Azure?](overview.md#known-issues). 

## <a name="create-a-secured-virtual-hub"></a>Criar um hub virtual seguro

Usando o Gerenciador de firewall no portal do Azure, você pode criar um novo hub virtual protegido ou converter um hub virtual existente que você criou anteriormente usando a WAN virtual do Azure.

## <a name="next-steps"></a>Próximas etapas

Para criar um hub virtual seguro e usá-lo para proteger e controlar uma rede de Hub e spoke, consulte [tutorial: proteger sua rede de nuvem com o Gerenciador de firewall do Azure usando o portal do Azure](secure-cloud-network.md).