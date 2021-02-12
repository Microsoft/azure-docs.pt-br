---
title: Proteger os hubs virtuais do Azure usando o Check Point Cloudguard Connect
description: Saiba mais sobre o Check Point CloudGuard Connect para proteger os hubs virtuais do Azure
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 10/30/2020
ms.author: victorh
ms.openlocfilehash: 3c61dc689d19e1a7d6f9b6dbefae846e9458d750
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/01/2020
ms.locfileid: "93146851"
---
# <a name="secure-virtual-hubs-using-check-point-cloudguard-connect"></a>Proteger os hubs virtuais usando o Check Point Cloudguard Connect

O Check Point CloudGuard Connect é um parceiro de segurança confiável no Gerenciador de firewall do Azure. Ele protege as conexões da B2I (rede virtual para a Internet) ou de redes virtuais para a Internet (V2I) distribuídas globalmente com a prevenção avançada de ameaças. 

Com uma configuração simples no Gerenciador de firewall do Azure, você pode rotear o Hub de ramificação e as conexões de rede virtual para a Internet por meio do SECaaS (CloudGuard Connect Security as a Service). O tráfego é protegido em trânsito do hub para o serviço de nuvem do Check Point em túneis de VPN IPsec.

Quando você habilita a sincronização automática no portal do Check Point, qualquer recurso marcado como *protegido* no portal do Azure é automaticamente protegido. Você não precisa gerenciar seus ativos duas vezes. Você simplesmente opta por protegê-los uma vez no portal do Azure.

O Check Point unifica vários serviços de segurança em um único ponto. O tráfego de segurança integrado é descriptografado uma vez e inspecionado em uma única passagem. O controle de aplicativos, a filtragem de URL e o reconhecimento de conteúdo (DLP) impõem o uso da Web seguro e protege seus dados. IPS e antivírus protegem os usuários contra explorações de rede conhecidas. O anti-bot bloqueia conexões para servidores de comando e controle e alerta você se um host estiver infectado.

A emulação de ameaças (área restrita) protege os usuários contra ameaças desconhecidas e de zero dias. O Check Point SandBlast Zero-Day proteção é uma tecnologia de areia-Boxing hospedada na nuvem onde os arquivos são rapidamente colocados em quarentena e inspecionados. Ele é executado em uma área restrita virtual para descobrir o comportamento mal-intencionado antes de entrar na sua rede. Ele impede ameaças antes que o dano seja feito para economizar tempo valioso da equipe ao responder a ameaças. 

## <a name="deployment-example"></a>Exemplo de implantação

Assista ao vídeo a seguir para ver como implantar o Check Point CloudGuard Connect como um parceiro de segurança do Azure confiável.

> [!VIDEO https://www.youtube.com/embed/C8AuN76DEmU]

## <a name="next-steps"></a>Próximas etapas

- [Implantar um provedor de parceiro de segurança](deploy-trusted-security-partner.md)