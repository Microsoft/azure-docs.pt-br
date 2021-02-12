---
title: Adicionar regras para conjuntos de dimensionamento de máquinas virtuais e Standard Load Balancer do Azure
titleSuffix: Add rules for Azure Standard Load Balancer and virtual machine scale sets
description: Com este roteiro de aprendizagem, comece a usar o Azure Standard Load Balancer e conjuntos de dimensionamento de máquinas virtuais.
services: load-balancer
documentationcenter: na
author: irenehua
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/17/2020
ms.author: irenehua
ms.openlocfilehash: 7a2e0531427343a2ec267de54cee05b5eb25889f
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99592272"
---
# <a name="add-rules-for-azure-load-balancer-with-virtual-machine-scale-sets"></a>Adicionar regras para Azure Load Balancer com conjuntos de dimensionamento de máquinas virtuais

Ao trabalhar com conjuntos de dimensionamento de máquinas virtuais e Azure Load Balancer, considere as diretrizes a seguir.

## <a name="port-forwarding-and-inbound-nat-rules"></a>Encaminhamento de porta e regras NAT de entrada

Depois que o conjunto de dimensionamento tiver sido criado, a porta de back-end não poderá ser modificada para uma regra de balanceamento de carga usada por uma investigação de integridade do balanceador de carga. Para alterar a porta, remova a investigação de integridade atualizando o conjunto de dimensionamento de máquinas virtuais e atualizando a porta. Em seguida, configure a investigação de integridade novamente.

Quando você usa o conjunto de dimensionamento de máquinas virtuais no pool de back-end do balanceador de carga, as regras de NAT de entrada padrão são criadas automaticamente.
  
## <a name="inbound-nat-pool"></a>Pool de NAT de entrada

Cada conjunto de dimensionamento de máquinas virtuais deve ter pelo menos um pool de NAT de entrada. Um pool NAT de entrada é uma coleção de regras NAT de entrada. Um pool de NAT de entrada não pode dar suporte a vários conjuntos de dimensionamento de máquinas virtuais.

## <a name="load-balancing-rules"></a>Regras de balanceamento de carga

Quando você usa o conjunto de dimensionamento de máquinas virtuais no pool de back-end do balanceador de carga, a regra de balanceamento de carga padrão é criada automaticamente.
  
## <a name="outbound-rules"></a>Regras de saída

Para criar uma regra de saída para um pool de back-end que já é referenciado por uma regra de balanceamento de carga, selecione **não** em **criar regras de saída implícitas** no portal do Azure quando a regra de balanceamento de carga de entrada for criada.

  :::image type="content" source="./media/vm-scale-sets/load-balancer-and-vm-scale-sets.png" alt-text="Captura de tela que mostra a criação de regras de balanceamento de carga." border="true":::

Use os métodos a seguir para implantar um conjunto de dimensionamento de máquinas virtuais com uma instância existente do Load Balancer:

* [Configurar um conjunto de dimensionamento de máquinas virtuais com uma instância existente do Azure Load Balancer usando o portal do Azure](./configure-vm-scale-set-portal.md)
* [Configurar um conjunto de dimensionamento de máquinas virtuais com uma instância existente do Azure Load Balancer usando Azure PowerShell](./configure-vm-scale-set-powershell.md)
* [Configurar um conjunto de dimensionamento de máquinas virtuais com uma instância existente do Azure Load Balancer usando o CLI do Azure](./configure-vm-scale-set-cli.md)
* [Atualizar ou excluir uma instância existente do Azure Load Balancer usado por um conjunto de dimensionamento de máquinas virtuais](./update-load-balancer-with-vm-scale-set.md)
