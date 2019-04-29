---
title: Visão geral do TAP da rede virtual do Azure | Microsoft Docs
description: Saiba mais sobre o TAP de rede virtual. O TAP de rede virtual fornece uma cópia profunda do tráfego de rede da máquina virtual que pode ser transmitido por streaming a um coletor de pacotes.
services: virtual-network
documentationcenter: na
author: karthikananth
manager: ganesr
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/14/2019
ms.author: kaanan
ms.openlocfilehash: ff5c8c4d3f6a0c87afae67404a5a39d4fe3757d9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60593176"
---
# <a name="virtual-network-tap"></a>TAP de rede virtual

O TAP (ponto de acesso do terminal) da rede virtual do Azure permite que você transmita seu tráfego de rede por streaming continuamente da máquina virtual para uma ferramenta de coleta de pacotes ou de análise de rede. A ferramenta de análise ou de coleta é fornecida por um parceiro de [solução de virtualização de rede](https://azure.microsoft.com/solutions/network-appliances/). Para obter uma lista de soluções de parceiros validadas para trabalhar com o TAP de rede virtual, consulte [soluções de parceiros](#virtual-network-tap-partner-solutions).

> [!IMPORTANT]
> Rede virtual toque está atualmente em versão prévia em todas as regiões do Azure. Para usar o TAP de rede virtual, você deve se inscrever na visualização enviando um email para <azurevnettap@microsoft.com> com sua ID de assinatura. Após a inscrição da sua assinatura, você receberá um email. Você somente poderá usar a funcionalidade quando receber um email de confirmação. Essa visualização é fornecida sem um contrato de nível de serviço e não deve ser usada para cargas de trabalho de produção. Determinados recursos podem não ter suporte, podem ter restrição ou podem não estar disponíveis em todos os locais do Azure. Consulte a [termos complementares de uso para visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter detalhes.

## <a name="virtual-network-tap-partner-solutions"></a>Soluções de parceiros de TAP de rede virtual

### <a name="network-packet-brokers"></a>Agentes de pacote de rede

- [Big Switch Big Monitoring Fabric](https://www.bigswitch.com/products/big-monitoring-fabric/public-cloud/microsoft-azure)
- [Gigamon GigaSECURE](https://blog.gigamon.com/2018/09/13/why-microsofts-new-vtap-service-works-even-better-with-gigasecure-for-azure)
- [Ixia CloudLens](https://www.ixiacom.com/cloudlens/cloudlens-azure)
- [Nubeva Prisms](https://www.nubeva.com/azurevtap)

### <a name="security-analytics-networkapplication-performance-management"></a>Análise de segurança, gerenciamento de desempenho de rede/aplicativo

- [Segurança de ativo](https://awakesecurity.com/technology-partners/microsoft-azure/)
- [Cisco Stealthwatch Cloud](https://blogs.cisco.com/security/cisco-stealthwatch-cloud-and-microsoft-azure-reliable-cloud-infrastructure-meets-comprehensive-cloud-security)
- [Darktrace](https://www.darktrace.com/en/azure/)
- [ExtraHop Reveal(x)](https://www.extrahop.com/company/tech-partners/microsoft/)
- [Fidelis Cybersecurity](https://www.fidelissecurity.com/technology-partners/microsoft-azure )
- [Flowmon](https://www.flowmon.com/blog/azure-vtap)
- [NetFort LANGuardian](https://www.netfort.com/languardian/solutions/visibility-in-azure-network-tap/)
- [Netscout vSTREAM]( https://www.netscout.com/technology-partners/microsoft/azure-vtap)
- [RSA NetWitness® Platform](https://www.rsa.com/azure)
- [Vectra Cognito](https://vectra.ai/microsoftazure)

A imagem seguir mostra como o TAP de rede virtual funciona. Você pode adicionar uma configuração de TAP em um [adaptador de rede](virtual-network-network-interface.md) anexado a uma máquina virtual implantada em sua rede virtual. O destino é um endereço IP de rede virtual na mesma rede virtual que o adaptador de rede monitorado ou em uma rede [virtual emparelhada](virtual-network-peering-overview.md). A solução de coleta para TAP de rede virtual pode ser implantada com base em um [balanceador de carga interno do Azure](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#concepts) para oferecer alta disponibilidade. Para avaliar as opções de implantação de solução individuais, confira [soluções de parceiros](#virtual-network-tap-partner-solutions).

![Como o TAP de rede virtual funciona](./media/virtual-network-tap/architecture.png)

## <a name="prerequisites"></a>Pré-requisitos

Antes de criar uma TAP de rede virtual, você deve ter recebido um email de confirmação que são registrados na versão prévia e ter uma ou mais máquinas virtuais criadas usando [do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) modelo de implantação e um parceiro solução para agregar o toque de tráfego na mesma região do azure. Se não houver uma solução de parceiro em sua rede virtual, confira [soluções de parceiros](#virtual-network-tap-partner-solutions) para implantar uma. Você pode usar o mesmo recurso de TAP de rede virtual para o tráfego agregado por meio de vários adaptadores de rede nas mesmas assinaturas ou em outras. Se os adaptadores de rede monitorados estiverem em assinaturas diferentes, as assinaturas precisarão estar associadas ao mesmo locatário do Azure Active Directory. Além disso, os adaptadores de rede monitorados e o ponto de extremidade de destino para agregar o tráfego do TAP podem estar em redes virtuais emparelhadas na mesma região. Se você estiver usando esse modelo de implantação, verifique se o [emparelhamento de rede virtual](virtual-network-peering-overview.md) está habilitado antes de configurar o TAP de rede virtual.

## <a name="permissions"></a>Permissões

As contas usadas para aplicar a configuração de TAP em adaptadores de rede precisam ser atribuídas à função [colaborador de rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou a uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) com as ações necessárias da tabela a seguir atribuídas:

| Ação | NOME |
|---|---|
| Microsoft.Network/virtualNetworkTaps/* | Necessária para criar, atualizar, ler e excluir um recurso de TAP de rede virtual |
| Microsoft.Network/networkInterfaces/read | Necessária para ler o recurso de adaptador de rede no qual o TAP será configurado |
| Microsoft.Network/tapConfigurations/* | Necessária para criar, atualizar, ler e excluir a configuração de TAP em um adaptador de rede |

## <a name="next-steps"></a>Próximas etapas

- Saiba como [Criar um TAP de rede virtual](tutorial-tap-virtual-network-cli.md).
