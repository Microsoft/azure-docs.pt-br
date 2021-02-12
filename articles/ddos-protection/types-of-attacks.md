---
title: Tipos de ataques que o padrão de proteção contra DDoS do Azure reduz
description: Saiba quais tipos de ataques a proteção contra DDoS do Azure Standard protege.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 8b213755aeed1590df3d1f5e91f44796c13c7711
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94991767"
---
# <a name="types-of-ddos-attacks-overview"></a>Tipos de ataques de DDoS-visão geral

A Proteção contra DDoS Standard pode mitigar os tipos de ataques a seguir:

- **Ataques de volumétricos**: esses ataques inundam a camada de rede com uma quantidade significativa de tráfego aparentemente legítimo. Eles incluem inundações de UDP, inundações de amplificação e outras inundações de pacotes falsificados. A proteção contra DDoS Standard atenua esses ataques potenciais de vários gigabytes, exportando-os e Depurando-os, com a escala de rede global do Azure, automaticamente.
- **Ataques de protocolo**: esses ataques renderizam um destino inacessível explorando uma vulnerabilidade na pilha de protocolos das camadas 3 e 4. Eles incluem ataques de inundação de SYN, ataques de reflexo e outros ataques de protocolo. A Proteção contra DDoS Standard mitiga esses ataques diferenciando entre o tráfego mal-intencionado e o legítimo, interagindo com o cliente e bloqueando o tráfego mal-intencionado. 
- **Ataques de camada de recursos (aplicativo)**: esses ataques são direcionados a pacotes de aplicativo Web para interromper a transmissão de dados entre os hosts. Eles incluem violações de protocolo HTTP, injeção de SQL, script entre sites e outros ataques de camada 7. Use um firewall do aplicativo Web, como o [Firewall do aplicativo Web do gateway de aplicativo](../web-application-firewall/ag/ag-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)do Azure, bem como o padrão de proteção contra DDoS para fornecer defesa contra esses ataques. Também há ofertas de firewall do aplicativo Web de terceiros disponível no [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall).

## <a name="azure-ddos-protection-standard"></a>Proteção contra DDoS do Azure Standard

A Proteção contra DDoS Standard protege os recursos em uma rede virtual, incluindo endereços IP públicos associados a máquinas virtuais, balanceadores de carga e gateways de aplicativo. Quando combinado com o Firewall do aplicativo Web do gateway de aplicativo ou um firewall de aplicativo Web de terceiros implantado em uma rede virtual com um IP público, a proteção contra DDoS Standard pode fornecer a capacidade de mitigação completa da camada 3 para a camada 7.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [criar um plano de proteção contra DDoS](manage-ddos-protection.md).