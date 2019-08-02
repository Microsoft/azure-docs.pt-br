---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: load balancer
author: KumudD
ms.service: load-balancer
ms.topic: include
ms.date: 02/08/2018
ms.author: kumud
ms.custom: include file
ms.openlocfilehash: 1d3ce900f7354b31e999c12b8e1eb0e23d391fcb
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172343"
---
| | SKU Standard | SKU Básico |
| --- | --- | --- |
| Tamanho do pool de back-end | Dá suporte a até 1.000 instâncias. | Dá suporte a até 100 instâncias. |
| Pontos de extremidade de pool de back-end | Qualquer máquina virtual em uma rede virtual única, incluindo a mistura de máquinas virtuais, conjuntos de disponibilidade, conjuntos de dimensionamento de máquina virtual. | Máquinas virtuais em um conjunto de disponibilidade ou conjunto de dimensionamento da máquina virtual. |
| [Investigações de integridade](../articles/load-balancer/load-balancer-custom-probe-overview.md#types) | TCP, HTTP, HTTPS | TCP, HTTP |
| [Comportamento de investigação de integridade inoperante](../articles/load-balancer/load-balancer-custom-probe-overview.md#probedown) | As conexões TCP permanecem ativas com investigação inoperante __e__ todas as investigações inoperantes. | As conexões TCP permanecem ativas com investigação de instância inoperante. Todas as conexões TCP terminam com todas as investigações estão inoperantes. |
| Zonas de Disponibilidades | No SKU Standard, os front-ends com redundância de zona e por zona para entrada e saída e os mapeamentos de fluxos de saída sobrevivem à falha de zona, balanceamento de carga entre zonas. | Não disponível. |
| Diagnósticos | Azure Monitor, métricas multidimensionais incluindo contadores byte e pacote, status de investigação de integridade, tentativas de conexão (TCP SYN), integridade de conexão de saída (SNAT bem sucedido e fluxos com falha), medidas de plano de dados ativo | Log Analytics do Azure apenas para o balanceador de carga público, alerta de exaustão SNAT, conta de integridade de pool back-end. |
| Portas de alta disponibilidade | Balanceador de Carga Interno | Não disponível. |
| Segurança por padrão | O IP público, os pontos de extremidade do Balanceador de Carga público e os pontos de extremidade do Balanceador de Carga interno estão fechados para fluxos de entrada, a menos que estejam na lista de permissões de um grupo de segurança de rede. | Aberto por padrão, grupo de segurança de rede opcional. |
| [Conexões de saída](../articles/load-balancer/load-balancer-outbound-connections.md) | Você pode definir explicitamente com base no pool de NAT de saída com [regras de saída](../articles/load-balancer/load-balancer-outbound-rules-overview.md). Você pode usar vários front-ends com recusa de regra de balanceamento de carga. Um cenário de saída _precisa_ ser explicitamente criado para a máquina virtual, o conjunto de disponibilidade e o conjunto de dimensionamento de máquinas virtuais para usar a conectividade de saída.  Os Pontos de Extremidade de Serviço de Rede Virtual podem ser acessados sem definir a conectividade de saída e não são considerados dados processados.  Quaisquer endereços de IP públicos, incluindo serviços de PaaS do Azure não estão disponíveis como pontos de extremidade de VNet, devem ser acessados por meio de conectividade de saída e contam para dados processados. Quando apenas um Balanceador de Carga interno estiver atendendo a uma máquina virtual, um conjunto de disponibilidade ou um conjunto de dimensionamento de máquinas virtuais, as conexões de saída via SNAT padrão não estarão disponíveis. Nesse caso, use [regras de saída](../articles/load-balancer/load-balancer-outbound-rules-overview.md). A programação de saída SNAT é o protocolo de transporte específicos com base no protocolo da regra de balanceamento de carga de entrada. | Único front-end, selecionado aleatoriamente quando vários front-ends estiverem presentes.  Quando apenas um Balanceador de Carga interno estiver atendendo a uma máquina virtual, um conjunto de disponibilidade ou um conjunto de dimensionamento de máquinas virtuais, um SNAT padrão será usado. |
| [Regras de saída](../articles/load-balancer/load-balancer-outbound-rules-overview.md) | Configuração do NAT de saída declarativa usando endereços IP públicos, prefixos de IP público ou ambos, tempo limite de ociosidade de saída configurável (4-120 minutos), alocação personalizada de porta do SNAT | Não disponível. |
|  [Redefinição de TCP quando ocioso](../articles/load-balancer/load-balancer-tcp-reset.md) | Habilitar a Redefinição de TCP (TCP RST) quando o tempo limite de ociosidade for atingido em qualquer regra | Não disponível |
| [Vários front-ends](../articles/load-balancer/load-balancer-multivip-overview.md) | Entrada e [saída](../articles/load-balancer/load-balancer-outbound-connections.md) | Somente entrada |
| Operações de Gerenciamento | Maioria das operações < 30 segundos | Normalmente, 60 a 90 segundos ou mais. |
| Contrato de Nível de Serviço | 99,99% para o caminho de dados com duas máquinas virtuais íntegras. | Não aplicável. | 
| Preços | Cobrado com base no número de regras, dados processados de entrada e saída associados ao recurso.  | Sem encargos. |
|  |  |  |
