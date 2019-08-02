---
title: Atributos de segurança para o gateway de VPN do Azure
description: Uma lista de verificação de atributos de segurança para avaliar o gateway de VPN do Azure
services: sql-database
author: msmbaldwin
manager: barbkess
ms.service: load-balancer
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mbaldwin
ms.openlocfilehash: c355f70975f441609304a4c9ee2ead75f0e0ce25
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444594"
---
# <a name="security-attributes-for-azure-vpn-gateway"></a>Atributos de segurança para o gateway de VPN do Azure

Este artigo documenta os atributos de segurança internos do gateway de VPN do Azure.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]


## <a name="preventative"></a>Preventivo

| Atributo de segurança | Sim/Não | Observações |
|---|---|--|
| Criptografia em repouso (como criptografia do lado do servidor, criptografia do lado do servidor com chaves gerenciadas pelo cliente e outros recursos de criptografia) | N/D | Dados de cliente de trânsito de gateway de VPN, não armazena dados do cliente |
| Criptografia em trânsito (como criptografia de ExpressRoute, criptografia de vnet e criptografia vnet)| Sim | O gateway de VPN criptografa os pacotes do cliente entre os gateways de VPN do Azure e os dispositivos de VPN locais do cliente (S2S) ou clientes VPN (P2S). Os gateways de VPN também dão suporte à criptografia de VNet para VNet. |
| Tratamento de chave de criptografia (CMK, BYOK, etc.)| Não | As chaves pré-compartilhadas especificadas pelo cliente são criptografadas em repouso; Mas não integrado com o CMK ainda. |
| Criptografia em nível de coluna (serviços de dados do Azure)| N/D | |
| Chamadas à API criptografadas| Sim | Por meio de [Azure Resource Manager](../azure-resource-manager/index.yml) e https  |

## <a name="network-segmentation"></a>Segmentação de rede

| Atributo de segurança | Sim/Não | Observações |
|---|---|--|
| Suporte ao ponto de extremidade de serviço| N/D | |
| Suporte à injeção de VNet| N/D | . |
| Isolamento de rede e suporte de firewall| Sim | Gateways de VPN são instâncias de VM dedicadas para cada rede virtual de cliente  |
| Suporte a túnel forçado| Sim |  |

## <a name="detection"></a>Detecção

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Suporte ao monitoramento do Azure (log Analytics, app insights, etc.)| Sim | Consulte [Azure monitor logs de diagnóstico/alerta](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md) & [Azure monitor métricas/alerta](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).  |

## <a name="identity-and-access-management"></a>Gerenciamento de identidade e de acesso

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Autenticação| Sim | [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) para gerenciar o serviço e configurar o gateway de VPN do Azure. |
| Autorização| Sim | Suporte à autorização via [RBAC](../role-based-access-control/overview.md). |


## <a name="audit-trail"></a>Trilha de auditoria

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Registro e auditoria do plano de gerenciamento e controle| Sim | Log de atividades Azure Resource Manager. |
| Log e auditoria do plano de dados | Sim | [Azure monitor logs de diagnóstico](../azure-resource-manager/resource-group-audit.md) para auditoria e log de conectividade de VPN. |

## <a name="configuration-management"></a>Gerenciamento de configuração

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Suporte ao gerenciamento de configuração (controle de versão de configuração, etc.)| Sim | Para operações de gerenciamento, o estado de uma configuração de gateway de VPN do Azure pode ser exportado como um modelo de Azure Resource Manager e com controle de versão ao longo do tempo. | 