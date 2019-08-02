---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/18/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: f93e22012a4855257f5372c1fc1dbc05ad29a6cd
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172069"
---
### <a name="virtual-networks"></a>Redes Virtuais

|  |  |
|---------|---------|
| [SKUs de Gateway de Aplicativo aprovadas](../articles/governance/policy/samples/allowed-app-gateway-sku.md) | Exige que os gateways de aplicativo usem uma SKU aprovada. Especifique uma matriz de SKUs aprovados. |
| [SKUs de gateway de rede virtual permitidas](../articles/governance/policy/samples/allowed-vnet-gateway-sku.md) | Exige que os gateways de rede virtual usem uma SKU aprovada. Especifique uma matriz de SKUs aprovados. |
| [SKUs do Load Balancer permitidas](../articles/governance/policy/samples/allowed-load-balancer-skus.md) | Exige que os balanceadores de carga de rede virtual usem uma SKU aprovada. Especifique uma matriz de SKUs aprovados. |
| [Nenhum emparelhamento de rede para a rede do Express Route](../articles/governance/policy/samples/no-peering-express-route-network.md) | Proíbe associar um emparelhamento de rede a uma rede em um grupo de recursos especificado. Use para impedir a conexão à infraestrutura de rede gerenciada central. Especifique o nome do grupo de recursos para impedir a associação. |
| [Nenhuma Tabela de Rotas Definida pelo Usuário](../articles/governance/policy/samples/no-user-defined-route-table.md)  | Proíbe que redes virtuais sejam implantadas com uma tabela de rotas definida pelo usuário. |
| [NSG X em cada sub-rede](../articles/governance/policy/samples/nsg-on-subnet.md) | Exige que um grupo de segurança de rede específico seja usado em cada sub-rede virtual. Você especifica a ID do grupo de segurança de rede a ser usada. |
| [Usar sub-rede aprovada para adaptadores de rede de VM](../articles/governance/policy/samples/use-approved-subnet-vm-nics.md) | Exige que os adaptadores de rede usem uma sub-rede aprovada. Especifique a ID da sub-rede aprovada. |
| [Usar vNet aprovada para adaptadores de rede de VM](../articles/governance/policy/samples/use-approved-vnet-vm-nics.md) | Exige que os adaptadores de rede usem uma rede virtual aprovada. Especifique a ID da rede virtual aprovada. |