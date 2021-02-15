---
title: Quais são as opções de arquitetura do Gerenciador de firewall do Azure?
description: Compare e contraste usando a rede virtual do Hub ou arquiteturas de Hub virtual protegidas com o Gerenciador de firewall do Azure.
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: article
ms.date: 09/14/2020
ms.author: victorh
ms.openlocfilehash: 71ff23e749139087f24da406474403167dcc1c0d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90563141"
---
# <a name="what-are-the-azure-firewall-manager-architecture-options"></a>Quais são as opções de arquitetura do Gerenciador de firewall do Azure?

O Gerenciador de firewall do Azure pode fornecer gerenciamento de segurança para dois tipos de arquitetura de rede:

- **Hub virtual protegido**

   Um [Hub de WAN Virtual do Azure](../virtual-wan/virtual-wan-about.md#resources) é um recurso gerenciado pela Microsoft que possibilita criar arquiteturas de hub e spoke facilmente. Quando as políticas de segurança e roteamento são associadas a esse Hub, ele é conhecido como um *[Hub virtual protegido](secured-virtual-hub.md)*. 
- **rede virtual do Hub**

   Essa é uma rede virtual do Azure padrão que você cria e gerencia por conta própria. Quando as políticas de segurança são associadas a um hub como esse, ele é chamado de *rede virtual de hub*. Neste momento, apenas a Política de Firewall do Azure é compatível. Você pode emparelhar redes virtuais que contêm os serviços e servidores da sua carga de trabalho. Você também pode gerenciar firewalls em redes virtuais autônomas que não são emparelhadas com nenhum spoke.

## <a name="comparison"></a>Comparação

A tabela a seguir compara essas duas opções de arquitetura e pode ajudá-lo a decidir qual delas é a mais adequada para os requisitos de segurança de sua organização:


|  |**Rede virtual de hub**|**Hub virtual seguro**  |
|---------|---------|---------|
|**Recurso subjacente**     |Rede virtual|Hub WAN virtual|
|**Hub & spoke**     |Usa o emparelhamento de rede virtual|Automatizado usando conexão de rede virtual do Hub|
|**Conectividade local**     |Gateway de VPN de até 10 Gbps e 30 conexões S2S; ExpressRoute|Gateway de VPN mais escalonável de até 20 Gbps e 1000 conexões S2S; Rota expressa|
|**Conectividade de ramificação automatizada usando SDWAN**      |Sem suporte|Com suporte|
|**Hubs por região**     |Várias redes virtuais por região|Único Hub virtual por região. Vários hubs possíveis com várias WANs virtuais|
|**Firewall do Azure – vários endereços IP públicos**      |Fornecida pelo cliente|Gerado automaticamente|
|**Zonas de Disponibilidade de firewall do Azure**     |Com suporte|Ainda não está disponível|
|**Segurança avançada da Internet com segurança de terceiros como parceiros de serviço**     |Conectividade de VPN estabelecida e gerenciada pelo cliente para o serviço de parceiro de sua escolha|Automatizado por meio da experiência de gerenciamento de parceiros e de fluxo do provedor de segurança|
|**Gerenciamento de rota centralizado para rotear o tráfego para o Hub**     |Rota definida pelo usuário gerenciada pelo cliente|Com suporte usando BGP|
|**Suporte a vários provedores de segurança**|Com suporte com túnel forçado configurado manualmente para firewalls de terceiros|Suporte automatizado para dois provedores de segurança: Firewall do Azure para filtragem de tráfego privado e terceiros para filtragem da Internet|
|**Firewall do Aplicativo Web no Gateway de Aplicativo** |Com suporte na rede virtual|Atualmente com suporte na rede spoke|
|**Solução de virtualização de rede**|Com suporte na rede virtual|Atualmente com suporte na rede spoke|
|**Suporte padrão à proteção contra DDoS do Azure**|Sim|Não|

## <a name="next-steps"></a>Próximas etapas

- Examine [Visão geral da implantação do Gerenciador de Firewall do Azure](deployment-overview.md)
- Saiba mais sobre [Hubs virtuais seguros](secured-virtual-hub.md).