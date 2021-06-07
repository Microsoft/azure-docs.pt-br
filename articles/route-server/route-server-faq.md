---
title: Perguntas frequentes sobre o Servidor de Rota do Azure
description: Encontre respostas para perguntas frequentes sobre o Servidor de Rota do Azure.
services: route-server
author: duongau
ms.service: route-server
ms.topic: article
ms.date: 03/29/2021
ms.author: duau
ms.openlocfilehash: c4c36013f100d2fc5265024432cc01a6622a4024
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105932362"
---
# <a name="azure-route-server-preview-faq"></a>Perguntas frequentes sobre o Servidor de Rota do Azure (Versão Prévia)

> [!IMPORTANT]
> No momento, o Servidor de Rota do Azure (versão prévia) está na versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="what-is-azure-route-server"></a>O que é o Servidor de Rota do Azure?

O Servidor de Rota do Azure é um serviço totalmente gerenciado que permite que você gerencie facilmente o roteamento entre sua NVA (solução de virtualização de rede) e rede virtual.

### <a name="is-azure-route-server-just-a-vm"></a>O Servidor de Rota do Azure é apenas uma VM?

Não. O Servidor de Rota do Azure é um serviço projetado com alta disponibilidade. Se ele for implantado em uma região do Azure que dá suporte a [Zonas de Disponibilidade](../availability-zones/az-overview.md), ele terá redundância no nível da zona.

### <a name="what-routing-protocols-does-azure-route-server-support"></a><a name = "protocol"></a>Para quais protocolos de roteamento o Servidor de Rota do Azure dá suporte?

O Servidor de Rota do Azure dá suporte apenas ao BGP (Border Gateway Protocol). Seu NVA precisa dar suporte ao BGP externo de vários saltos, porque você precisará implantar o Servidor de Rota do Azure em uma sub-rede dedicada na sua rede virtual. O [ASN](https://en.wikipedia.org/wiki/Autonomous_system_(Internet)) escolhido deve ser diferente daquele usado pelo Servidor de Rota do Azure quando você configura o BGP em seu NVA.

### <a name="does-azure-route-server-route-data-traffic-between-my-nva-and-my-vms"></a>O Servidor de Rota do Azure roteia o tráfego de dados entre meu NVA e minhas VMs?

Não. O Servidor de Rota do Azure troca apenas rotas BGP com seu NVA. O tráfego de dados passa diretamente do NVA para a VM escolhida e da VM para o NVA.

### <a name="does-azure-route-server-store-customer-data"></a>O Servidor de Rota do Azure armazena dados do cliente?
Não. O Servidor de Rota do Azure apenas troca rotas BGP com seu NVA e as propaga para sua rede virtual.

### <a name="if-azure-route-server-receives-the-same-route-from-more-than-one-nva-will-it-program-all-copies-of-the-route-but-each-with-a-different-next-hop-to-the-vms-in-the-virtual-network"></a>Se o Servidor de Rota do Azure receber a mesma rota de mais de um NVA, ele programará todas as cópias da rota (mas cada uma com um próximo salto diferente) para as VMs na rede virtual?

Sim, somente se a rota tiver o mesmo tamanho do caminho AS. Quando as VMs enviam tráfego para o destino dessa rota, os hosts da VM farão o roteamento ECMP (Caminhos Múltiplos de Custo Igual). No entanto, se um NVA enviar a rota com um comprimento de caminho AS menor do que outros NVAs. O Servidor de Rota do Azure programará apenas a rota que tem o próximo salto definido para esse NVA para as VMs na rede virtual.

### <a name="does-azure-route-server-support-vnet-peering"></a>O Servidor de Rota do Azure dá suporte ao emparelhamento VNet?

Sim. Se você emparelhar uma VNet hospedando o Servidor de Rota do Azure com outra VNet e habilitar o “Usar Gateway Remoto” nessa VNet. O Servidor de Rota do Azure aprenderá os espaços de endereço dessa VNet e os enviará a todos os NVAs emparelhados.

### <a name="what-autonomous-system-numbers-asns-can-i-use"></a>Quais ASNs (Números do Sistema Autônomo) posso usar?

Você pode os próprios ASNs públicos ou privados no seu dispositivo de rede virtual. Não é possível usar os intervalos reservados pelo Azure nem pela IANA.
Os seguintes ASNs são reservados pelo Azure ou pela IANA:

* ASNs reservados pelo Azure:
    * ASNs públicos: 8074, 8075, 12076
    * ASNs privados: 65515, 65517, 65518, 65519, 65520
* Os ASNs [reservados pela IANA](http://www.iana.org/assignments/iana-as-numbers-special-registry/iana-as-numbers-special-registry.xhtml):
    * 23456, 64496-64511, 65535-65551

### <a name="can-i-use-32-bit-4-byte-asns"></a>Posso usar os ASNs de 32 bits (4 bytes)?

Não, o Servidor de Rota do Azure dá suporte apenas aos ASNs de 16 bits (2 bytes).

## <a name="route-server-limits"></a><a name = "limitations"></a>Limites de Servidor de Rota

O Servidor de Rota do Azure tem os seguintes limites (por implantação).

| Recurso | Limite |
|----------|-------|
| Número de pares BGP com suporte | 8 |
| Número de rotas que cada par de BGP pode anunciar para o Servidor de Rota do Azure | 200 |
| Número de rotas que o Servidor de Rota do Azure pode anunciar para o ExpressRoute ou gateway de VPN | 200 |

Se o seu NVA anunciar mais rotas do que o limite, a sessão BGP será removida. Se isso acontecer com o gateway e o Servidor de Rota do Azure, você perderá a conectividade de sua rede local para o Azure. Para saber mais, consulte [Diagnosticar um problema de roteamento da máquina virtual do Azure](../virtual-network/diagnose-network-routing-problem.md).

## <a name="next-steps"></a>Próximas etapas

Saiba como [configurar o Servidor de Rota do Azure](quickstart-configure-route-server-powershell.md).
