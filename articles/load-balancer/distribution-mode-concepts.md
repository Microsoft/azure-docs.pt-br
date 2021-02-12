---
title: Modos de distribuição Azure Load Balancer
description: Comece a aprender sobre os diferentes modos de distribuição do Azure Load Balancer.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: article
ms.date: 02/04/2021
ms.custom: template-concept
ms.openlocfilehash: fcea2e962722773f59e73df898e0188c3f6454b6
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99551190"
---
# <a name="azure-load-balancer-distribution-modes"></a>Modos de distribuição Azure Load Balancer

O Azure Load Balancer dá suporte a dois modos de distribuição para conexões de roteamento para seu aplicativo com balanceamento de carga:

* Baseado em hash
* Afinidade do IP de origem

## <a name="hash-based"></a>Baseado em hash

O modo de distribuição padrão para o Azure Load Balancer é um hash de cinco tuplas. 

A tupla é composta pelo:
* **IP de origem**
* **Porta de origem**
* **IP de destino**
* **Porta de destino**
* **Tipo de protocolo**

O hash é usado para mapear o tráfego para os servidores disponíveis. O algoritmo fornece adesão somente dentro de uma sessão de transporte. Os pacotes que estão na mesma sessão são direcionados para o mesmo IP do datacenter por trás do ponto de extremidade com balanceamento de carga. Quando o cliente inicia uma nova sessão do mesmo IP de origem, a porta de origem é alterada e faz com que o tráfego vá para um ponto de extremidade de datacenter diferente.

![Modo de distribuição baseado em hash de cinco tuplas](./media/distribution-mode-concepts/load-balancer-distribution.png)

O modo baseado em hash tem um tipo de configuração:

* **Nenhum (baseado em hash)** – especifica que as solicitações sucessivas do mesmo cliente podem ser tratadas por qualquer máquina virtual.

## <a name="source-ip-affinity"></a>Afinidade do IP de origem

Esse modo de distribuição também é conhecido como afinidade de sessão ou afinidade do IP do cliente. O modo usa um hash de duas tuplas (IP de origem e IP de destino) ou de três tuplas (IP de origem, IP de destino e tipo de protocolo) para mapear o tráfego para os servidores disponíveis. 

Ao usar a afinidade de IP de origem, as conexões iniciadas no mesmo computador cliente vão para o mesmo ponto de extremidade de datacenter.

A figura a seguir ilustra uma configuração de duas tuplas. Observe como as duas tuplas são executadas por meio do balanceador de carga para a máquina virtual 1 (VM1). O backup da VM1 é feito pela VM2 e VM3.

![Modo de distribuição de afinidade de sessão de duas tuplas](./media/load-balancer-distribution-mode/load-balancer-session-affinity.png)

O modo de afinidade de IP de origem tem dois tipos de configuração:

* **IP do cliente (afinidade de IP de origem 2-tupla)** – especifica que as solicitações sucessivas do mesmo endereço IP do cliente serão tratadas pela mesma máquina virtual.
* **IP do cliente e protocolo (afinidade de IP de origem 3-tupla)** – especifica que solicitações sucessivas do mesmo endereço IP do cliente e combinação de protocolos serão manipuladas pela mesma máquina virtual.

## <a name="use-cases"></a>Casos de uso

A afinidade de IP de origem com IP do cliente e protocolo (afinidade de IP de origem 3-tupla), resolve uma incompatibilidade entre Azure Load Balancer e Área de Trabalho Remota gateway (Gateway RD). 

Outro cenário de caso de uso é o upload de mídia. O upload de dados ocorre por meio de UDP, mas o plano de controle é obtido por meio de TCP:

* Um cliente inicia uma sessão TCP para o endereço público com balanceamento de carga e é direcionado para um DIP específico. O canal permanece ativo para monitorar a integridade da conexão.
* Uma nova sessão UDP do mesmo computador cliente é iniciada para o mesmo ponto de extremidade público com balanceamento de carga. A conexão é direcionada para o mesmo ponto de extremidade DIP que a conexão TCP anterior. O upload da mídia pode ser executado com alta taxa de transferência, mantendo um canal de controle por meio de TCP.

> [!NOTE]
> Quando um conjunto com balanceamento de carga for alterado, removendo ou adicionando uma máquina virtual, a distribuição de solicitações de cliente será recalculada. Não é possível depender de novas conexões de clientes existentes que terminam no mesmo servidor. Além disso, o uso do modo de distribuição de afinidade do IP de origem pode causar uma distribuição desigual de tráfego. Os clientes que executam atrás de proxies podem ser vistos como um aplicativo cliente exclusivo.


## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como configurar o modo de distribuição de Azure Load Balancer, consulte [Configurar o modo de distribuição para Azure Load Balancer](load-balancer-distribution-mode.md).

