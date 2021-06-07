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
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/14/2019
ms.author: kaanan
ms.openlocfilehash: 6160dd09edc57f2f52306d4dad0dde413fff0616
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102617175"
---
# <a name="virtual-network-tap"></a>TAP de rede virtual
> [!IMPORTANT]
> A visualização do toque de rede virtual está em espera no momento em todas as regiões do Azure. Você pode enviar um email para nós <azurevnettap@microsoft.com> com sua ID de assinatura e notificaremos você com atualizações futuras sobre a versão prévia. No ínterim, você pode usar soluções baseadas em agente ou NVA que fornecem funcionalidade de visibilidade de toque/rede por meio de nossas [soluções de parceiros de agente de pacotes](#virtual-network-tap-partner-solutions) disponíveis nas [ofertas do Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances%3Ball&search=Network%20Traffic&filters=partners).

O TAP (Ponto de Acesso do Terminal) de rede virtual do Azure permite que você transmita o tráfego de rede por streaming continuamente da máquina virtual para uma ferramenta de coleta de pacotes ou de análise de rede. A ferramenta de análise ou de coleta é fornecida por um parceiro de [solução de virtualização de rede](https://azure.microsoft.com/solutions/network-appliances/). Para obter uma lista de soluções de parceiros validadas para trabalhar com o TAP de rede virtual, consulte [soluções de parceiros](#virtual-network-tap-partner-solutions).
A imagem seguir mostra como o TAP de rede virtual funciona. Você pode adicionar uma configuração de TAP em um [adaptador de rede](virtual-network-network-interface.md) anexado a uma máquina virtual implantada em sua rede virtual. O destino é um endereço IP de rede virtual na mesma rede virtual que o adaptador de rede monitorado ou em uma rede [virtual emparelhada](virtual-network-peering-overview.md). A solução de coleta para TAP de rede virtual pode ser implantada com base em um balanceador de carga interno do Azure para oferecer alta disponibilidade.
![Como o TAP de rede virtual funciona](./media/virtual-network-tap/architecture.png)

## <a name="prerequisites"></a>Pré-requisitos

Antes de criar um toque de rede virtual, você deve ter recebido um email de confirmação que você está inscrito na versão prévia e ter uma ou mais máquinas virtuais criadas usando [Azure Resource Manager](../azure-resource-manager/management/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) modelo de implantação e uma solução de parceiro para agregar o tráfego de toque na mesma região do Azure. Se não houver uma solução de parceiro em sua rede virtual, confira [soluções de parceiros](#virtual-network-tap-partner-solutions) para implantar uma. Você pode usar o mesmo recurso de TAP de rede virtual para o tráfego agregado por meio de vários adaptadores de rede nas mesmas assinaturas ou em outras. Se os adaptadores de rede monitorados estiverem em assinaturas diferentes, as assinaturas precisarão estar associadas ao mesmo locatário do Azure Active Directory. Além disso, os adaptadores de rede monitorados e o ponto de extremidade de destino para agregar o tráfego do TAP podem estar em redes virtuais emparelhadas na mesma região. Se você estiver usando esse modelo de implantação, verifique se o [emparelhamento de rede virtual](virtual-network-peering-overview.md) está habilitado antes de configurar o TAP de rede virtual.

## <a name="permissions"></a>Permissões

As contas usadas para aplicar a configuração de TAP em adaptadores de rede precisam ser atribuídas à função [colaborador de rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou a uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) com as ações necessárias da tabela a seguir atribuídas:

| Ação | Nome |
|---|---|
| Microsoft.Network/virtualNetworkTaps/* | Necessária para criar, atualizar, ler e excluir um recurso de TAP de rede virtual |
| Microsoft.Network/networkInterfaces/read | Necessária para ler o recurso de adaptador de rede no qual o TAP será configurado |
| Microsoft.Network/tapConfigurations/* | Necessária para criar, atualizar, ler e excluir a configuração de TAP em um adaptador de rede |


## <a name="virtual-network-tap-partner-solutions"></a>Soluções de parceiros de TAP de rede virtual

### <a name="network-packet-brokers"></a>Agentes de pacote de rede

- [GigaVUE Cloud Suite para Azure](https://www.gigamon.com/solutions/cloud/public-cloud/gigavue-cloud-suite-azure.html)
- [Ixia CloudLens](https://www.ixiacom.com/cloudlens/cloudlens-azure)
- [Nubeva Prisms](https://www.nubeva.com/azurevtap)
- [Big Switch Big Monitoring Fabric](https://www.bigswitch.com/products/big-monitoring-fabric/public-cloud/microsoft-azure)

### <a name="security-analytics-networkapplication-performance-management"></a>Análise de segurança, gerenciamento de desempenho de rede/aplicativo

- [Segurança de ativação](https://awakesecurity.com/technology-partners/microsoft-azure/)
- [Nuvem do Cisco Stealthwatch](https://blogs.cisco.com/security/cisco-stealthwatch-cloud-and-microsoft-azure-reliable-cloud-infrastructure-meets-comprehensive-cloud-security)
- [Darktrace](https://www.darktrace.com/en/azure/)
- [ExtraHop Reveal(x)](https://www.extrahop.com/partners/tech-partners/microsoft/)
- [Fidelis Cybersecurity](https://www.fidelissecurity.com/technology-partners/microsoft-azure )
- [Flowmon](https://www.flowmon.com/blog/azure-vtap)
- [LANGuardian NetFort](https://www.netfort.com/languardian/solutions/visibility-in-azure-network-tap/)
- [Netscout vSTREAM]( https://www.netscout.com/marketplace-azure)
- [Segurança do NoName](https://nonamesecurity.com/)
- [Riverbed SteelCentral AppResponse]( https://www.riverbed.com/products/steelcentral/steelcentral-appresponse-11.html)
- [RSA NetWitness® Platform](https://www.rsa.com/content/dam/en/solution-brief/rsa-netwitness-platform-overview-for-federal-agencies.pdf)
- [Vectra Cognito](https://vectra.ai/microsoftazure)



## <a name="next-steps"></a>Próximas etapas

- Saiba como [Criar um TAP de rede virtual](tutorial-tap-virtual-network-cli.md).
