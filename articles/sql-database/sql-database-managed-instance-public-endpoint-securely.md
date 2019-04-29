---
title: Proteger pontos de extremidade públicos a instância gerenciada - banco de dados SQL a instância gerenciada | Microsoft Docs
description: Usar com segurança os pontos de extremidade públicos no Azure com a instância gerenciada
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.topic: conceptual
author: WenJason
ms.author: v-jay
ms.reviewer: vanto, carlrab
manager: digimobile
origin.date: 04/16/2019
ms.date: 04/29/2019
ms.openlocfilehash: 9d5a3d18e8a7d3c5a6cb08e16e74dd4fbda9ca31
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61315030"
---
# <a name="using-azure-sql-database-managed-instance-securely-with-public-endpoint"></a>Usar o banco de dados SQL a instância gerenciada com segurança com o ponto de extremidade público

Banco de dados SQL do Azure a instância gerenciada poderia ser habilitada para fornecer conectividade de usuário pela [ponto de extremidade público](../virtual-network/virtual-network-service-endpoints-overview.md). Este artigo fornece orientações de como fazer essa configuração mais segura.

## <a name="scenarios"></a>Cenários

A instância gerenciada fornece pontos de extremidade privados para habilitar a conectividade de dentro de sua rede virtual. A opção padrão é fornecer isolamento máximo. No entanto, há cenários em que uma conexão de ponto de extremidade público é necessário:

- Integração com apenas ofertas de PaaS com vários locatários.
- Maior taxa de transferência de troca de dados que o uso de VPN.
- As políticas da empresa proíbem PaaS dentro de redes corporativas.

## <a name="deploying-managed-instance-for-public-endpoint-access"></a>Implantação de instância gerenciada para acesso de ponto de extremidade público

Embora não seja obrigatório, o modelo de implantação comuns para uma instância gerenciada com acesso de ponto de extremidade público é criar a instância em uma rede virtual isolada dedicada. Nessa configuração, a rede virtual é usada apenas para o isolamento de cluster virtual. Não é relevante se o espaço de endereço IP de instância gerenciada se sobrepõe a um espaço de endereço IP da rede corporativa.

## <a name="securing-data-in-motion"></a>Protegendo dados em movimento

Tráfego de dados de instância gerenciada sempre será criptografado se o driver cliente dá suporte à criptografia. Dados entre a instância gerenciada e outras máquinas virtuais do Azure ou serviços do Azure nunca deixa o backbone do Azure. Se houver uma instância gerenciada para uma conexão de rede local, recomendou usar a rota expressa com o emparelhamento da Microsoft. A rota expressa ajudará a evitar a movimentação de dados pela Internet pública (para conectividade privada de instância gerenciada, emparelhamento privado só pode ser usado).

## <a name="locking-down-inbound-and-outbound-connectivity"></a>Bloquear a conectividade de entrada e saída

O diagrama a seguir mostra as configurações de segurança recomendada.

![managed-instance-vnet.png](media/sql-database-managed-instance-public-endpoint-securely/managed-instance-vnet.png)

A instância gerenciada tem um [endereço do ponto de extremidade público dedicado](sql-database-managed-instance-find-management-endpoint-ip-address.md). Esse endereço IP deve ser definido no firewall de saída de lado do cliente e regras do grupo de segurança de rede para limitar a conectividade de saída.

Para garantir que o tráfego para a instância gerenciada é proveniente de fontes confiáveis, é recomendável para conectar-se de fontes com endereços IP bem conhecidos. Limitar o acesso para o ponto de extremidade público de instância gerenciada na porta 3342 usando um grupo de segurança de rede.

Quando os clientes precisam iniciar uma conexão de uma rede local, verifique se que o endereço de origem é convertido para um conjunto conhecido de IPs. Se isso não pode ser conseguido (por exemplo, força de trabalho móvel, sendo um cenário típico), é recomendável para usar [Point-to-site VPN conexões e um ponto de extremidade privado](sql-database-managed-instance-configure-p2s.md).

Se as conexões são iniciadas do Azure, é recomendável que o tráfego proveniente bem conhecidos atribuído [VIP](../virtual-network/virtual-networks-reserved-public-ip.md) (por exemplo, máquinas virtuais). Para facilitar o gerenciamento de endereços VIP, os clientes podem considerar usando [prefixo de endereço IP público](../virtual-network/public-ip-address-prefix.md).