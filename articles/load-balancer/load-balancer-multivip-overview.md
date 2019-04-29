---
title: Vários front-ends para Azure Load Balancer
titlesuffix: Azure Load Balancer
description: Visão geral de vários front-ends no Azure Load Balancer
services: load-balancer
documentationcenter: na
author: chkuhtz
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2018
ms.author: chkuhtz
ms.openlocfilehash: b9a140314b8eba6386c37bdbcf2bb3de58589335
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60594176"
---
# <a name="multiple-frontends-for-azure-load-balancer"></a>Vários front-ends para Azure Load Balancer

O Azure Load Balancer permite que você equilibre a carga de serviços em várias portas, vários endereços IP ou ambos. Você pode usar definições de balanceador de carga públicas e internas para fluxos de balanceamento de carga em um conjunto de VMs.

Este artigo descreve os conceitos básicos dessa capacidade, conceitos importantes e restrições. Se você pretende expor serviços em um endereço IP, encontre instruções simplificadas para configurações do balanceador de carga [público](load-balancer-get-started-internet-portal.md) ou [interno](load-balancer-get-started-ilb-arm-portal.md). A adição de vários front-ends é um complemento a uma única configuração de front-end. Com os conceitos deste artigo, você pode expandir uma configuração simplificada quanto quiser.

Quando você define um Azure Load Balancer, uma configuração de pool de front-end e back-end são conectadas às regras. A investigação de integridade referenciada pela regra é usada para determinar como os novos fluxos serão enviados para um nó no pool de back-end. O front-end (ou seja, VIP) é definido por uma tupla de 3 composta por um endereço IP (público ou interno), um protocolo de transporte (UDP ou TCP) e um número de porta da regra de balanceamento de carga. O pool de back-end é uma coleção de configurações de IP de Máquinas Virtuais (parte do recurso NIC) que referenciam o pool de back-end do Load Balancer.

A tabela a seguir contém alguns exemplos de configurações de front-end:

| Front-end | Endereço IP | protocol | porta |
| --- | --- | --- | --- |
| 1 |65.52.0.1 |TCP |80 |
| 2 |65.52.0.1 |TCP |*8080* |
| 3 |65.52.0.1 |*UDP* |80 |
| 4 |*65.52.0.2* |TCP |80 |

A tabela mostra quatro front-ends diferentes. Os front-ends 1, 2 e 3 são um único front-ends com várias regras. O mesmo endereço IP é usado, mas a porta ou o protocolo é diferente para cada front-end. Os front-ends 1 e 4 são um exemplo de vários front-ends, onde o mesmo protocolo de front-end e porta são reutilizados em vários front-ends.

O Azure Load Balancer fornece flexibilidade na definição de regras de balanceamento de carga. Uma regra declara como um endereço e porta no front-end são mapeados para o endereço de destino e porta no back-end. A reutilização de portas de back-end nas regras depende do tipo de regra. Cada tipo de regra tem requisitos específicos que podem afetar a configuração do host o design da investigação. Há dois tipos de regras:

1. A regra padrão sem reutilização de porta de back-end
2. A regra de IP Flutuante onde as portas de back-end são reutilizadas

O Azure Load Balancer permite que você misture os dois tipos de regra na mesma configuração de balanceador de carga. O balanceador de carga pode usá-los simultaneamente para uma determinada VM ou qualquer combinação, desde que você concorde com as restrições da regra. O tipo de regra que você escolhe depende dos requisitos de seu aplicativo e da complexidade do suporte a essa configuração. Você deve avaliar quais tipos de regras são melhores para seu cenário.

Vamos explorar esses cenários ainda mais, começando com o comportamento padrão.

## <a name="rule-type-1-no-backend-port-reuse"></a>Tipo de regra nº 1: Nenhuma reutilização de porta de back-end

![Ilustração de vários front-ends com front-end verde e roxo](./media/load-balancer-multivip-overview/load-balancer-multivip.png)

Nesse cenário, os front-ends são configurados da seguinte maneira:

| Front-end | Endereço IP | protocol | porta |
| --- | --- | --- | --- |
| ![front-end verde](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |
| ![front-end roxo](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |*65.52.0.2* |TCP |80 |

O DIP é o destino do fluxo de entrada. No pool de back-end, cada VM expõe o serviço desejado em uma porta exclusiva em um DIP. Esse serviço é associado ao front-end por meio de uma definição de regra.

Definimos duas regras:

| Regra | Mapear front-end | Para pool de back-end |
| --- | --- | --- |
| 1 |![front-end verde](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Frontend1:80 |![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) DIP1:80, ![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-green.png)  DIP2:80 |
| 2 |![VIP](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Frontend2:80 |![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) DIP1:81, ![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png)  DIP2:81 |

O mapeamento completo no Azure Load Balancer agora é o seguinte:

| Regra | Endereço IP de front-end | protocol | porta | Destino | porta |
| --- | --- | --- | --- | --- | --- |
| ![regra de verde](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |Endereço IP DIP |80 |
| ![regra de roxo](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |65.52.0.2 |TCP |80 |Endereço IP DIP |81 |

Cada regra deve produzir um fluxo com uma combinação exclusiva de endereço IP de destino e porta de destino. Ao variar a porta de destino do fluxo, várias regras podem proporcionar fluxos ao mesmo DIP em portas diferentes.

Investigações de integridade são sempre direcionadas ao DIP de uma VM. É necessário garantir que sua investigação reflita a integridade da VM.

## <a name="rule-type-2-backend-port-reuse-by-using-floating-ip"></a>Tipo de regra 2: reutilização de porta de back-end usando o IP Flutuante

O Azure Load Balancer oferece a flexibilidade para reutilizar a porta de front-end em vários front-ends, independentemente do tipo de regra usado. Além disso, alguns cenários de aplicativos preferem ou exigem a utilização da mesma porta por várias instâncias do aplicativo em uma única VM no pool de back-end. Exemplos comuns de reutilização de porta incluem clusters de alta disponibilidade, dispositivos virtuais de rede e exposição de vários pontos de extremidade TLS sem nova criptografia.

Se você quiser reutilizar a porta de back-end em várias regras, habilite o IP Flutuante na definição da regra.

"IP flutuante" é a terminologia do Azure para uma parte do que é conhecido como DSR (Retorno de Servidor Direto). O DSR é formado por duas partes: uma topologia de fluxo e um esquema de mapeamento de endereço IP. Em um nível de plataforma, o Azure Load Balancer sempre opera em uma topologia de fluxo de DSR, independentemente de o IP Flutuante estar habilitado ou não. Isso significa que a parte de saída de um fluxo é sempre reescrita corretamente para fluir diretamente de volta para a origem.

Com o tipo de regra padrão, o Azure expõe um esquema de mapeamento de endereço IP de balanceamento de carga tradicional para facilitar o uso. A habilitação do IP Flutuante altera o esquema de mapeamento de endereço IP a fim de permitir flexibilidade adicional, conforme explicado abaixo.

O diagrama a seguir ilustra essa configuração:

![Ilustração de vários front-ends com front-end verde e roxo com DSR](./media/load-balancer-multivip-overview/load-balancer-multivip-dsr.png)

Para este cenário, todas as VMs no pool de back-end têm três interfaces de rede:

* DIP: uma NIC Virtual associada à VM (configuração de IP do recurso NIC do Azure)
* Front-end 1: uma interface de loopback no SO Convidado que está configurado com o endereço IP do Front-end 1
* Front-end 2: uma interface de loopback no SO Convidado que está configurado com o endereço IP do Front-end 2

> [!IMPORTANT]
> A configuração das interfaces de loopback é executada no SO Convidado. Essa configuração não é executada ou gerenciada pelo Azure. Sem essa configuração, as regras não funcionarão. As definições de investigação de integridade usam o DIP da VM em vez da interface de loopback que representa o front-end de DSR. Portanto, o serviço deve fornecer respostas de investigação em uma porta DIP que refletem o status do serviço oferecido na interface de loopback que representa o front-end de DSR.

Vamos supor a mesma configuração de front-end que no cenário anterior:

| Front-end | Endereço IP | protocol | porta |
| --- | --- | --- | --- |
| ![front-end verde](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |
| ![front-end roxo](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |*65.52.0.2* |TCP |80 |

Definimos duas regras:

| Regra | Front-end | Mapa para pool de back-end |
| --- | --- | --- |
| 1 |![Regra](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Frontend1:80 |![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Frontend1:80 (em VM1 e VM2) |
| 2 |![Regra](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Frontend2:80 |![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Frontend2:80 (em VM1 e VM2) |

A tabela a seguir mostra o mapeamento completo no balanceador de carga:

| Regra | Endereço IP de front-end | protocol | porta | Destino | porta |
| --- | --- | --- | --- | --- | --- |
| ![regra de verde](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |mesmo que front-end (65.52.0.1) |mesmo que front-end (80) |
| ![regra de roxo](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |65.52.0.2 |TCP |80 |mesmo que front-end (65.52.0.2) |mesmo que front-end (80) |

O destino do fluxo de entrada é o endereço IP de front-end na interface de loopback na VM. Cada regra deve produzir um fluxo com uma combinação exclusiva de endereço IP de destino e porta de destino. Ao variar o endereço IP de destino do fluxo, a reutilização de porta é possível na mesma VM. O serviço é exposto ao balanceador de carga vinculando-o à porta e endereço IP de front-end da respectiva interface de loopback.

Observe que este exemplo não altera a porta de destino. Embora esse seja um cenário de IP Flutuante, o Azure Load Balancer também oferece suporte à definição de uma regra para reescrever a porta de destino de back-end e diferenciar da porta de destino do front-end.

O tipo de regra de IP Flutuante é a base de vários padrões de configuração do balanceador de carga. Um exemplo disponível atualmente é a configuração [AlwaysOn com vários ouvintes](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md) . Ao longo do tempo, documentaremos mais esses cenários.

## <a name="limitations"></a>Limitações

* Há suporte para várias configurações de front-ends apenas com VMs de IaaS.
* Com a regra de IP flutuante, seu aplicativo deve usar a configuração de IP primário para fluxos de saída. Se o seu aplicativo se associar ao endereço IP de front-end configurado na interface de loopback no SO Convidado, o SNAT do Azure não estará disponível para reescrever o fluxo de saída e o fluxo falhará.
* Endereços IP públicos têm um efeito sobre a cobrança. Para saber mais, confira [Preços de endereço IP](https://azure.microsoft.com/pricing/details/ip-addresses/)
* Limites de assinatura são aplicados. Para saber mais, confira [Limites de serviço](../azure-subscription-service-limits.md#networking-limits) .

## <a name="next-steps"></a>Próximas etapas

- Consulte [Conexões de saída](load-balancer-outbound-connections.md) para entender o impacto de vários front-ends no comportamento da conexão de saída.
