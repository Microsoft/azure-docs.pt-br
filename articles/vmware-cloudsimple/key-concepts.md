---
title: Principais conceitos para administrar a solução do Azure VMware por CloudSimple
titleSuffix: Azure VMware Solution by CloudSimple
description: Descreve os principais conceitos para administrar soluções do Azure VMware por CloudSimple
author: Ajayan1008
ms.author: v-hborys
ms.date: 04/24/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e5544ef7725855d28e20d39ff345db6bb07671a2
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/05/2021
ms.locfileid: "97895318"
---
# <a name="key-concepts-for-administration-of-azure-vmware-solutions-by-cloudsimple"></a>Principais conceitos para administração de soluções do Azure VMware por CloudSimple

Administrar as soluções do Azure VMware pelo CloudSimple requer uma compreensão dos seguintes conceitos:

* Serviço CloudSimple, que é exibido como soluções VMware do Azure por CloudSimple-Service
* Nó CloudSimple, que é exibido como soluções VMware do Azure por CloudSimple-node
* Nuvem privada CloudSimple
* Rede de serviço
* Máquina virtual CloudSimple, que é exibida como soluções VMware do Azure por CloudSimple-máquina virtual

## <a name="cloudsimple-service"></a>Serviço CloudSimple

Com o serviço CloudSimple, você pode criar e gerenciar todos os recursos associados às soluções VMware pelo CloudSimple da portal do Azure. Crie um recurso de serviço em todas as regiões em que você pretende usar o serviço.

Saiba mais sobre o [serviço CloudSimple](cloudsimple-service.md).

## <a name="cloudsimple-node"></a>Nó CloudSimple

Um nó CloudSimple é um host de armazenamento e de computação com e sem sistema operacional dedicado, em que o hipervisor VMware ESXi é implantado. Esse nó é então incorporado às plataformas VMware vSphere, vCenter, vSAN e NSX. Os serviços de rede do CloudSimple e os serviços de rede de borda também estão habilitados. Cada nó serve como uma unidade de computação e capacidade de armazenamento que você pode provisionar para criar [nuvens privadas do CloudSimple](cloudsimple-private-cloud.md). Você provisiona ou reserva nós em uma região onde o serviço CloudSimple está disponível.

Saiba mais sobre [nós CloudSimple](cloudsimple-node.md).

## <a name="cloudsimple-private-cloud"></a>Nuvem privada CloudSimple

Uma nuvem privada CloudSimple é um ambiente de pilha VMware isolado gerenciado por um vCenter Server em seu próprio domínio de gerenciamento. O VMware Stack inclui hosts ESXi, vSphere, vCenter, vSAN e NSX. A pilha é executada em nós dedicados (hardware bare-metal dedicado e isolado) e é consumida pelos usuários por meio de ferramentas nativas do VMware que incluem o vCenter e o NSX Manager. Os nós dedicados são implantados em locais do Azure e são gerenciados pelo Azure. Cada nuvem privada pode ser segmentada e protegida usando serviços de rede, como VLANs e sub-redes e tabelas de firewall. As conexões com seu ambiente local e a rede do Azure são criadas usando conexões seguras, VPN privadas e Azure ExpressRoute.

Saiba mais sobre a [nuvem privada do CloudSimple](cloudsimple-private-cloud.md).

## <a name="service-networking"></a>Rede de serviço

O serviço CloudSimple fornece uma rede por região em que o serviço CloudSimple é implantado. A rede é um único espaço de endereço de camada TCP 3 com roteamento habilitado por padrão. Todas as nuvens e sub-redes privadas criadas nessa região se comunicam entre si sem nenhuma configuração adicional. Você cria grupos de portas distribuídas no vCenter usando as VLANs. Você pode usar os seguintes recursos de rede para configurar e proteger seus recursos de carga de trabalho em sua nuvem privada:

* [VLANs e sub-redes](cloudsimple-vlans-subnets.md)
* [Tabelas de firewall](cloudsimple-firewall-tables.md)
* [Gateways VPN](cloudsimple-vpn-gateways.md)
* [IP público](cloudsimple-public-ip-address.md)
* [Conexão de rede do Azure](cloudsimple-azure-network-connection.md)

## <a name="cloudsimple-virtual-machine"></a>Máquina virtual CloudSimple

Com o serviço CloudSimple, você pode gerenciar máquinas virtuais VMware do portal do Azure. Um ou mais clusters ou pools de recursos do seu ambiente vSphere podem ser mapeados para a assinatura na qual o serviço é criado.

Saiba mais sobre:

* [CloudSimple máquinas virtuais](cloudsimple-virtual-machines.md)
* [Mapeamento de assinatura do Azure](./azure-subscription-mapping.md)
