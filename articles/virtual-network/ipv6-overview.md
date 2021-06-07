---
title: Visão geral do IPv6 para a Rede Virtual do Microsoft Azure
titlesuffix: Azure Virtual Network
description: Descrição dos pontos de extremidade do IPv6 e caminhos de dados em uma rede virtual do Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: bb7769298940095900da25e61e13612b92bad00c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103419092"
---
# <a name="what-is-ipv6-for-azure-virtual-network"></a>O que é o IPv6 para a rede virtual do Azure?

O IPv6 para a Rede Virtual do Microsoft Azure (VNet) permite hospedar aplicativos no Azure com conectividade de IPv6 e IPv4 tanto dentro de uma rede virtual quanto de e para a Internet. Devido ao esgotamento de endereços IPv4 públicos, as novas redes para mobilidade e Internet das Coisas (IoT) são geralmente criadas no IPv6. Mesmo ISPs e redes móveis há muito tempo estabelecidas estão sendo transformadas em IPv6. Os serviços que usam somente IPv4 podem enfrentar uma real desvantagem nos mercados existentes e emergentes. A conectividade IPv4/IPv6 de pilha dupla permite que os serviços hospedados no Azure lidem com essa lacuna tecnológica por meio de serviços de pilha dupla disponíveis globalmente, que se conectam prontamente ao IPv4 existente e a esses novos dispositivos e redes IPv6.

A conectividade IPv6 original do Azure facilita o fornecimento de conectividade de Internet de pilha dupla (IPv4/IPv6) para aplicativos hospedados no Azure. Ela permite a implantação simples de VMs com conectividade IPv6 com balanceamento de carga para conexões iniciadas de entrada e de saída. Esse recurso ainda está disponível e mais informações estão disponíveis [aqui](../load-balancer/load-balancer-ipv6-overview.md).
O IPv6 para a rede virtual do Azure é muito mais completo, permitindo que as arquiteturas completas da solução IPv6 sejam implantadas no Azure.


O diagrama a seguir ilustra uma implantação simples de pilha dupla (IPv4/IPv6) no Azure:

![Diagrama de implantação de rede IPv6](./media/ipv6-support-overview/ipv6-sample-diagram.png)

## <a name="benefits"></a>Benefícios

Benefícios do IPv6 para a VNET do Azure:

- Ajuda a expandir o alcance de seus aplicativos hospedados no Azure para os mercados crescentes de dispositivos móveis e Internet das Coisas.
- As VMs IPv4/IPv6 com pilha dupla fornecem flexibilidade máxima de implantação de serviço. Uma única instância de serviço pode se conectar com clientes de Internet compatíveis com IPv4 e IPv6.
- Os builds se baseiam em uma conectividade IPv6 de VM para Internet do Azure já estabelecida e estável.
- Seguro por padrão, pois a conectividade IPv6 com a Internet só é estabelecida quando você a solicita explicitamente em sua implantação.

## <a name="capabilities"></a>Funcionalidades

O IPv6 para VNet do Azure inclui os seguintes recursos:

- Os clientes do Azure podem definir seu próprio espaço de endereço de rede virtual IPv6 para atender às necessidades de seus aplicativos e clientes, ou para se integrar de forma contínua em seu espaço IP local.
- Redes virtuais de pilha dupla (IPv4 e IPv6) com sub-redes de pilha dupla permitem que os aplicativos se conectem aos recursos IPv4 e IPv6 em sua rede virtual ou à Internet.
    > [!IMPORTANT]
    > As sub-redes para IPv6 devem ter exatamente /64 de tamanho.  Isso garante uma compatibilidade futura caso você decida habilitar o roteamento da sub-rede para uma rede local, já que alguns roteadores só podem aceitar rotas IPv6 de /64.  
- Proteja seus recursos com regras de IPv6 para Grupos de Segurança de Rede.
    - E as proteções de DDoS (Negação de Serviço Distribuído) da plataforma do Azure são estendidas para o IP público voltado para a Internet
- Personalize o roteamento do tráfego IPv6 em sua rede virtual com rotas definidas pelo usuário, especialmente ao aproveitar as soluções de virtualização de rede para expandir seu aplicativo.
- Linux e Máquinas Virtuais do Windows podem usar IPv6 para a VNET do Azure
- O [Load Balancer público do IPv6 padrão](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md) dá suporte para criar aplicativos resilientes e escalonáveis, que incluem:
    - Investigação de integridade IPv6 opcional, para determinar quais instâncias de pool de back-end estão íntegras e, portanto, podem receber novos fluxos.
    - Regras de saída opcionais, que fornecem total controle declarativo sobre a conectividade de saída para dimensionar e ajustar essa capacidade às suas necessidades específicas.
    - Várias configurações opcionais de front-end, que permitem que um único balanceador de carga use vários endereços IP públicos IPv6, fazendo com que o mesmo protocolo de front-end e porta possa ser reutilizado em endereços de front-end.
    - As portas IPv6 opcionais podem ser reutilizadas em instâncias de back-end usando o recurso de *IP Flutuante* de regras de balanceamento de carga 
    - Observação: O balanceamento de carga não executa nenhuma conversão de protocolo (sem NAT64). 
    - Observação: O IPv6 só pode ter balanceamento de carga para a NIC (interface de rede principal) em VMs do Azure. 
- O [balanceador de carga interno do IPv6 padrão](ipv6-dual-stack-standard-internal-load-balancer-powershell.md) dá suporte para criar aplicativos de várias camadas resilientes no nas VNETs do Azure.   
- Suporte básico ao Load Balancer público IPv6 para compatibilidade com implantações herdadas
- Os [endereços IP públicos de IPv6 reservados e os intervalos de endereços](ipv6-public-ip-address-prefix.md) fornecem endereços IPv6 estáveis e previsíveis que facilitam a filtragem de seus aplicativos hospedados no Azure para sua empresa e seus clientes.
- O IP Público em nível de instância fornece conectividade de Internet IPv6 diretamente a VMs individuais.
- [Adicionar IPv6 a implantações de somente IPv4 existentes](ipv6-add-to-existing-vnet-powershell.md) — este recurso permite que você adicione facilmente conectividade IPv6 a implantações de somente IPv4 existentes sem a necessidade de recriar implantações.  O tráfego de rede IPv4 não é afetado durante esse processo, portanto, dependendo do seu aplicativo e do sistema operacional, é possível adicionar o IPv6 mesmo aos serviços dinâmicos.    
- Permita que os clientes da Internet acessem diretamente seu aplicativo de pilha dupla usando o protocolo de sua escolha com o suporte do DNS do Azure para registros IPv6 (AAAA). 
- Crie aplicativos de pilha dupla que dimensionam automaticamente em sua carga com conjuntos de dimensionamento de máquinas virtuais com IPv6.
- O [Emparelhamento de Rede Virtual (VNET)](virtual-network-peering-overview.md) — dentro do emparelhamento regional e global — permite que você se conecte com VNETs de pilha dupla, com os pontos de extremidade IPv4 e IPv6, em VMs nas redes emparelhadas que poderão se comunicar entre si. Você pode até mesmo emparelhar a pilha dupla com VNETs de somente IPv4, pois você está fazendo a transição de suas implantações para pilha dupla. 
- Os diagnósticos e a solução de problemas de IPv6 estão disponíveis com os recursos de métricas e alertas do balanceador de carga e do Observador de Rede, como a captura de pacotes, logs de fluxo NSG, solução de problemas de conexão e monitoramento de conexão.   

## <a name="scope"></a>Escopo
O IPv6 para VNET do Azure é um conjunto de recursos básico que permite que os clientes hospedem aplicativos de pilha dupla (IPv4 + IPv6) no Azure.  Pretendemos adicionar suporte a IPv6 a mais recursos de rede do Azure ao longo do tempo e, por fim, oferecer versões de pilha dupla dos serviços de PaaS do Azure, mas, enquanto isso, todos os serviços de PaaS do Azure podem ser acessados por meio dos pontos de extremidade IPv4 em máquinas virtuais de pilha dupla.   

## <a name="limitations"></a>Limitações
A versão atual do IPv6 para a rede virtual do Azure tem as seguintes limitações:
- O IPv6 para a rede virtual do Azure está disponível em todas as regiões globais do Azure Comercial e do governo dos EUA usando todos os métodos de implantação.  
- Os gateways do ExpressRoute podem ser usados para tráfego de somente IPv4 em uma VNET com IPv6 habilitado.  O suporte para tráfego IPv6 está em nosso roteiro.   
- Os gateways de VPN não podem ser usados em uma VNET com o IPv6 habilitado, diretamente ou emparelhado com “UseRemoteGateway”.
- A plataforma do Azure (AKS, etc.) não oferece suporte à comunicação IPv6 para contêineres. 
- Não há suporte para Máquinas Virtuais de somente IPv6 ou Conjuntos de Dimensionamento de Máquinas Virtuais, cada NIC deve incluir pelo menos uma configuração de IP IPv4. 
- Ao adicionar IPv6 a implantações IPv4 existentes, os intervalos de IPv6 não podem ser adicionados a uma VNET com links de navegação de recursos existentes.  
- O DNS direto para IPv6 atualmente tem suporte para o DNS público do Azure, mas o DNS reverso ainda não tem suporte.   

## <a name="pricing"></a>Preços

Os recursos e a largura de banda do Azure IPv6 são cobrados com as mesmas tarifas do IPv4. Não há encargos adicionais ou diferentes para o IPv6. Você pode encontrar detalhes sobre os preços para [endereços IP públicos](https://azure.microsoft.com/pricing/details/ip-addresses/), [largura de banda de rede](https://azure.microsoft.com/pricing/details/bandwidth/) ou [Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/).

## <a name="next-steps"></a>Próximas etapas

- Saiba como [implantar um aplicativo de pilha dupla IPv6 usando o Azure PowerShell](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md).
- Saiba como [implantar um aplicativo de pilha dupla IPv6 usando o CLI do Azure](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-cli.md).
- Saiba como [implantar um aplicativo de pilha dupla IPv6 usando os modelos do Resource Manager (JSON)](ipv6-configure-standard-load-balancer-template-json.md)
