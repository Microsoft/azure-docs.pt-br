---
title: O que são provedores de parceiros de segurança do Azure firewall Manager?
description: Saiba mais sobre os provedores de parceiros de segurança do Gerenciador de firewall do Azure
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: victorh
ms.openlocfilehash: 923c6b685d20ff68788e7d9cfcb45ebaecb535e3
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96490101"
---
# <a name="what-are-security-partner-providers"></a>O que são provedores de parceiro de segurança?

Os *provedores de parceiros de segurança* no Gerenciador de firewall do Azure permitem que você use suas ofertas de SECaaS (segurança como serviço) mais conhecidas e de terceiros para proteger o acesso à Internet para seus usuários.

Com uma configuração rápida, você pode proteger um hub com um parceiro de segurança com suporte e rotear e filtrar o tráfego de Internet de suas redes virtuais (VNets) ou locais de ramificação em uma região. Você pode fazer isso com o gerenciamento automatizado de rotas, sem configurar e gerenciar UDRs (rotas definidas pelo usuário).

Você pode implantar hubs protegidos configurados com o parceiro de segurança de sua escolha em várias regiões do Azure para obter conectividade e segurança para seus usuários em qualquer lugar do mundo nessas regiões. Com a capacidade de usar a oferta do parceiro de segurança para o tráfego de aplicativos de Internet/SaaS e o Firewall do Azure para tráfego privado nos hubs protegidos, agora você pode começar a criar sua borda de segurança no Azure que está perto de seus usuários e aplicativos distribuídos globalmente.

Os parceiros de segurança com suporte são **Zscaler**, **[Check Point](check-point-overview.md)** e **iboss**.

![Provedores de parceiro de segurança](media/trusted-security-partners/trusted-security-partners.png)

## <a name="key-scenarios"></a>Principais cenários

Você pode usar os parceiros de segurança para filtrar o tráfego de Internet nos seguintes cenários:

- Rede virtual (VNet) para a Internet

   Aproveite a proteção avançada da Internet com reconhecimento de usuário para suas cargas de trabalho na nuvem em execução no Azure.

- Branch para a Internet

   Aproveite sua conectividade do Azure e a distribuição global para adicionar facilmente filtragem de NSaaS de terceiros para cenários de Branch para Internet. Você pode criar sua rede de trânsito global e borda de segurança usando a WAN virtual do Azure.

Os cenários a seguir têm suporte:
- VNet/Branch para a Internet por meio de um provedor de parceiros de segurança e o outro tráfego (spoke para spoke, spoke para Branch, ramificação para spoke) por meio do firewall do Azure.
- VNet/Branch para Internet por meio do provedor de parceiros de segurança

## <a name="best-practices-for-internet-traffic-filtering-in-secured-virtual-hubs"></a>Práticas recomendadas para filtragem de tráfego de Internet em hubs virtuais protegidos

O tráfego da Internet normalmente inclui o tráfego da Web. Mas também inclui o tráfego destinado a aplicativos SaaS como Microsoft 365 e serviços de PaaS públicos do Azure, como o armazenamento do Azure, o SQL do Azure e assim por diante. Veja a seguir as recomendações de práticas recomendadas para lidar com o tráfego para esses serviços:

### <a name="handling-azure-paas-traffic"></a>Lidando com o tráfego de PaaS do Azure
 
- Use o Firewall do Azure para proteção se seu tráfego consistir principalmente da PaaS do Azure e o acesso aos recursos para seus aplicativos puder ser filtrado usando endereços IP, FQDNs, marcas de serviço ou marcas de FQDN.

- Use uma solução de parceiro de terceiros nos hubs se seu tráfego consistir em acesso ao aplicativo SaaS ou se você precisar de filtragem de reconhecimento de usuário (por exemplo, para suas cargas de trabalho de VDI (Virtual Desktop Infrastructure)) ou precisar de recursos avançados de filtragem da Internet.

![Todos os cenários do Gerenciador de firewall do Azure](media/trusted-security-partners/all-scenarios.png)

## <a name="handling-microsoft-365-traffic"></a>Manipulando o tráfego de Microsoft 365

Em cenários de localização de Branch distribuídos globalmente, você deve redirecionar Microsoft 365 tráfego diretamente no Branch antes de enviar o tráfego de Internet restante para o Hub protegido do Azure.

Por Microsoft 365, a latência de rede e o desempenho são essenciais para uma experiência de usuário bem-sucedida. Para atingir essas metas em relação ao desempenho e à experiência de usuário ideais, os clientes devem implementar o Microsoft 365 direto e o escape local antes de considerar o roteamento do restante do tráfego de Internet por meio do Azure.

[Microsoft 365 os princípios de conectividade de rede](/microsoft-365/enterprise/microsoft-365-network-connectivity-principles) chamam as conexões de rede Microsoft 365 chave a serem roteadas localmente da ramificação do usuário ou do dispositivo móvel e diretamente pela Internet para o ponto de presença mais próximo da rede da Microsoft.

Além disso, Microsoft 365 conexões são criptografadas para privacidade e usar protocolos proprietários e eficientes por motivos de desempenho. Isso torna impraticável e impactado a entidade dessas conexões com soluções de segurança de nível de rede tradicionais. Por esses motivos, é altamente recomendável que os clientes enviem Microsoft 365 tráfego diretamente de ramificações, antes de enviar o restante do tráfego por meio do Azure. A Microsoft estabeleceu uma parceria com vários provedores de soluções SD-WAN, que se integram ao Azure e Microsoft 365 e facilitam para os clientes a habilitação de Microsoft 365 a interconexão de Internet direta e local. Para obter detalhes, consulte [o que é a WAN virtual do Azure?](../virtual-wan/virtual-wan-about.md)

## <a name="next-steps"></a>Próximas etapas

[Implante uma oferta de parceiro de segurança em um hub protegido usando o Gerenciador de firewall do Azure](deploy-trusted-security-partner.md).
