---
title: Visão geral do IPv6 para a rede virtual do Azure (visualização)
titlesuffix: Azure Virtual Network
description: Descrição de IPv6 de pontos de extremidade IPv6 e caminhos de dados em uma rede virtual do Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.workload: infrastructure-services
ms.date: 07/15/2019
ms.author: kumud
ms.openlocfilehash: 5093b74484cd04a0c0c7afed8e2ebc725af033f5
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68249826"
---
# <a name="what-is-ipv6-for-azure-virtual-network-preview"></a>O que é IPv6 para a rede virtual do Azure? (Visualização)

O IPv6 para VNet (rede virtual) do Azure permite hospedar aplicativos no Azure com conectividade IPv6 e IPv4 tanto dentro de uma rede virtual quanto de e para a Internet. Devido ao esgotamento de endereços IPv4 públicos, as novas redes para mobilidade e Internet das Coisas (IoT) geralmente são criadas no IPv6. O ISP e as redes móveis demoradas são transformadas no IPv6. Os serviços somente IPv4 podem se encontrar em uma desvantagem real nos mercados existentes e emergentes. A conectividade de IPv4/IPv6 de pilha dupla permite que os serviços hospedados no Azure percorram essa lacuna de tecnologia com serviços disponíveis globalmente e com pilha dupla que se conectam prontamente ao IPv4 existente e a esses novos dispositivos e redes IPv6.

A conectividade IPv6 original do Azure facilita o fornecimento de conectividade de Internet de pilha dupla (IPv4/IPv6) para aplicativos hospedados no Azure. Ele permite a implantação simples de VMs com conectividade IPv6 com balanceamento de carga para conexões iniciadas de entrada e de saída. Este recurso ainda está disponível e mais informações estão disponíveis [aqui](../load-balancer/load-balancer-ipv6-overview.md).
O IPv6 para a rede virtual do Azure é muito mais completo – permitindo que as arquiteturas completas da solução IPv6 sejam implantadas no Azure.

> [!Important]
> O IPv6 para a rede virtual do Azure está atualmente em visualização pública. Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Veja os [Termos de Uso Adicionais para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter detalhes.

O diagrama a seguir ilustra uma implantação simples de pilha dupla (IPv4/IPv6) no Azure:

![Diagrama de implantação de rede IPv6](./media/ipv6-support-overview/ipv6-sample-diagram.png)

## <a name="benefits"></a>Benefícios

Benefícios do IPv6 da rede virtual do Azure:

- Ajuda a expandir o alcance de seus aplicativos hospedados no Azure para os mercados móveis e Internet das Coisas crescentes.
- As VMs IPv4/IPv6 com pilha dupla fornecem flexibilidade máxima de implantação de serviço. Uma única instância de serviço pode se conectar com clientes de Internet compatíveis com IPv4 e IPv6.
- O se baseia em uma conectividade IPv6 de VM para Internet do Azure demorada e estável.
- Seguro por padrão, uma vez que a conectividade IPv6 com a Internet só é estabelecida quando você a solicita explicitamente em sua implantação.

## <a name="capabilities"></a>Capacidades

O IPv6 para VNet inclui os seguintes recursos:

- Os clientes do Azure podem definir seu próprio espaço de endereço de rede virtual IPv6 para atender às necessidades de seus aplicativos, clientes ou integrar diretamente em seu espaço IP local.
- Redes virtuais de pilha dupla (IPv4 e IPv6) com sub-redes de pilha dupla permitem que os aplicativos se conectem aos recursos IPv4 e IPv6 em sua rede virtual ou à Internet.
- Proteger seus recursos com regras IPv6 para grupos de segurança de rede
- Personalize o roteamento do tráfego IPv6 em sua rede virtual com rotas definidas pelo usuário, especialmente ao aproveitar as soluções de virtualização de rede para aumentar seu aplicativo.
- Deixe os clientes da Internet acessarem diretamente seu aplicativo de pilha dupla usando o protocolo de sua escolha com o suporte do DNS do Azure para registros IPv6 (AAAA). 
- Suporte de Load Balancer público do IPv6 padrão para criar aplicativos resilientes e escalonáveis que incluem:
    - Investigação de integridade IPv6 opcional para determinar quais instâncias de pool de back-end são integridade e, portanto, podem receber novos fluxos. .  
    - Regras de saída opcionais que fornecem total controle declarativo sobre a conectividade de saída para dimensionar e ajustar essa capacidade às suas necessidades específicas.
    - Várias configurações opcionais de front-end que permitem que um único balanceador de carga use vários endereços IP públicos IPv6-o mesmo protocolo de front-end e porta podem ser reutilizados em endereços de front-end.
- O IP público em nível de instância fornece conectividade de Internet IPv6 diretamente a VMs individuais.
- Adicione facilmente conectividade IPv6 a implantações somente IPv4 existentes com upgrade-in-Place.
- Crie aplicativos de pilha dupla que dimensionam automaticamente para sua carga com conjuntos de dimensionamento de máquinas virtuais.
- O suporte do portal para a versão prévia inclui criação/edição/exclusão interativa de redes virtuais de pilha dupla (IPv4 + IPv6) e sub-redes, regras de grupo de segurança de rede IPv6, rotas definidas pelo usuário IPv6 e IPs públicos IPv6.  

## <a name="limitations"></a>Limitações
A versão de visualização do IPv6 para a rede virtual do Azure tem as seguintes limitações:
- O IPv6 para a rede virtual do Azure (versão prévia) está disponível em todas as regiões globais do Azure, mas somente no Azure global – não nas nuvens do governo.
- O suporte do portal para componentes Standard Load Balancer é somente exibição.  No entanto, o suporte completo e a documentação (com exemplos) estão disponíveis para implantações de Standard Load Balancer usando o Azure PowerShell e a CLI (interface de linha de comando).   
- O suporte do observador de rede para a versão prévia é limitado a logs de fluxo NSG e capturas de pacotes de rede.
- O emparelhamento de rede virtual (regional ou globalmente) não tem suporte na versão prévia.
- Ao usar o Load Balancer externo IPv6 padrão, os seguintes limites se aplicam: 
  - As regras de saída podem fazer referência a vários IPs públicos de front-end, mas **não** podem fazer referência a um prefixo público IPv6. O prefixo público de IP dá suporte apenas a prefixos IPv4.
  - As regras de balanceamento de carga de IPv6 **não** podem usar o recurso de *IP flutuante* . A reutilização de porta em instâncias de back-end só tem suporte com IPv4.
- Reservar um bloco de endereços IPv6 voltados para a Internet não é suportado pelo recurso de prefixo de endereço IP público do Azure.

## <a name="pricing"></a>Preços

Os recursos e a largura de banda do Azure IPv6 são cobrados com as mesmas tarifas que o IPv4. Não há encargos adicionais ou diferentes para o IPv6. Você pode encontrar detalhes sobre os preços de [endereços IP públicos](https://azure.microsoft.com/pricing/details/ip-addresses/), [largura de banda de rede](https://azure.microsoft.com/pricing/details/bandwidth/)ou [Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/).

## <a name="next-steps"></a>Próximas etapas

- Saiba como [implantar um aplicativo de pilha dupla IPv6 usando o Azure PowerShell](virtual-network-ipv4-ipv6-dual-stack-powershell.md).
- Saiba como [implantar um aplicativo de pilha dupla IPv6 usando o CLI do Azure](virtual-network-ipv4-ipv6-dual-stack-cli.md).
