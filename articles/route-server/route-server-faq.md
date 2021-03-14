---
title: Perguntas frequentes sobre o servidor de rotas do Azure
description: Encontre respostas para perguntas frequentes sobre o servidor de rota do Azure.
services: route-server
author: duongau
ms.service: route-server
ms.topic: article
ms.date: 03/08/2021
ms.author: duau
ms.openlocfilehash: 6eed0ed3e936b0e9a534c82a3105c2ed37cab3d5
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102485412"
---
# <a name="azure-route-server-preview-faq"></a>FAQ do servidor de rota do Azure (visualização)

> [!IMPORTANT]
> No momento, o Servidor de Rota do Azure (versão prévia) está na versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="what-is-azure-route-server"></a>O que é o servidor de rota do Azure?

O servidor de rota do Azure é um serviço totalmente gerenciado que permite que você gerencie facilmente o roteamento entre sua NVA (solução de virtualização de rede) e sua rede virtual.

### <a name="is-azure-route-server-just-a-vm"></a>O servidor de rota do Azure é apenas uma VM?

Não. O servidor de rota do Azure é um serviço projetado com alta disponibilidade. Se ele for implantado em uma região do Azure que dá suporte a [zonas de disponibilidade](../availability-zones/az-overview.md), ele terá redundância no nível da zona.

### <a name="what-routing-protocols-does-azure-route-server-support"></a><a name = "protocol"></a>A quais protocolos de roteamento o Azure Route Server dá suporte?

O servidor de rota do Azure dá suporte apenas a Border Gateway Protocol (BGP). Seu NVA precisa dar suporte ao BGP externo de vários saltos porque você precisará implantar o servidor de rota do Azure em uma sub-rede dedicada em sua rede virtual. O [ASN](https://en.wikipedia.org/wiki/Autonomous_system_(Internet)) escolhido deve ser diferente daquele usado pelo servidor de rota do Azure quando você configura o BGP em seu NVA.

### <a name="does-azure-route-server-route-data-traffic-between-my-nva-and-my-vms"></a>O Azure Route Server roteia o tráfego de dados entre meu NVA e minhas VMs?

Não. O servidor de rota do Azure troca apenas rotas BGP com seu NVA. O tráfego de dados passa diretamente do NVA para a VM escolhida e diretamente da VM para o NVA.

### <a name="if-azure-route-server-receives-the-same-route-from-more-than-one-nva-will-it-program-all-copies-of-the-route-but-each-with-a-different-next-hop-to-the-vms-in-the-virtual-network"></a>Se o servidor de rota do Azure receber a mesma rota de mais de um NVA, ele programará todas as cópias da rota (mas cada uma com um próximo salto diferente) para as VMs na rede virtual?

Sim, somente se a rota tiver o mesmo tamanho do caminho. Quando as VMs enviam tráfego para o destino dessa rota, os hosts de VM farão Equal-Cost o roteamento de vários caminhos (ECMP). No entanto, se um NVA enviar a rota com um comprimento de caminho menor do que outros NVAs. O servidor de rota do Azure apenas programará a rota que tem o próximo salto definido para esse NVA para as VMs na rede virtual.

### <a name="does-azure-route-server-support-vnet-peering"></a>O servidor de rota do Azure dá suporte ao emparelhamento VNet?

Sim. Se você emparelhar uma VNet hospedando o servidor de rota do Azure para outra VNet e habilitar usar o gateway remoto nessa VNet. O servidor de rota do Azure aprenderá os espaços de endereço dessa VNet e os enviará a todos os NVAs emparelhados.

### <a name="what-autonomous-system-numbers-asns-can-i-use"></a>Quais números de sistema autônomo (ASNs) posso usar?

Você pode usar seu próprio ASNs público ou ASNs privado em sua solução de virtualização de rede. Não é possível usar os intervalos reservados pelo Azure nem pela IANA.
Os seguintes ASNs são reservados pelo Azure ou pela IANA:

* ASNs reservados pelo Azure:
    * ASNs públicos: 8074, 8075, 12076
    * ASNs privados: 65515, 65517, 65518, 65519, 65520
* Os ASNs [reservados pela IANA](http://www.iana.org/assignments/iana-as-numbers-special-registry/iana-as-numbers-special-registry.xhtml):
    * 23456, 64496-64511, 65535-65551

### <a name="can-i-use-32-bit-4-byte-asns"></a>Posso usar o ASNs de 32 bits (4 bytes)?

Não, o servidor de rota do Azure dá suporte apenas a ASNs de 16 bits (2 bytes).

## <a name="route-server-limits"></a><a name = "limitations"></a>Limites de servidor de rota

O servidor de rota do Azure tem os seguintes limites (por implantação).

| Recurso | Limite |
|----------|-------|
| Número de pares BGP com suporte | 8 |
| Número de rotas que cada par de BGP pode anunciar para o servidor de rota do Azure | 200 |
| Número de rotas que o servidor de rota do Azure pode anunciar para o ExpressRoute ou gateway de VPN | 200 |

Se o seu NVA anunciar mais rotas do que o limite, a sessão BGP será descartada. Se isso acontecer com o gateway e o servidor de rota do Azure, você perderá a conectividade de sua rede local para o Azure. Para obter mais informações, consulte [diagnosticar um problema de roteamento de máquina virtual do Azure](../virtual-network/diagnose-network-routing-problem.md).

## <a name="next-steps"></a>Próximas etapas

Saiba como [Configurar o servidor de rota do Azure](quickstart-configure-route-server-powershell.md).
