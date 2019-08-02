---
title: Principais conceitos para administrar a solução CloudSimple do VMware do Azure
description: Descreve os principais conceitos para administrar a solução CloudSimple do VMware do Azure
author: sharaths-cs
ms.author: b-shsury
ms.date: 04/24/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6d87871fe8faaaab2e56d4a0426cd5e5f0899c8f
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595614"
---
# <a name="key-concepts-for-administration-of-azure-vmware-solution-by-cloudsimple"></a>Principais conceitos para administração de solução CloudSimple do VMware do Azure

Administrando a solução VMware do Azure CloudSimple requer uma compreensão dos conceitos a seguir:

* Serviço CloudSimple, que é exibido como solução de VMware do Azure por CloudSimple - serviço
* Nó CloudSimple, que é exibido como solução de VMware do Azure por CloudSimple - nó
* CloudSimple de nuvem privada
* Rede de serviço
* Máquina virtual CloudSimple, que é exibida como solução de VMware do Azure por CloudSimple - Máquina Virtual

## <a name="cloudsimple-service"></a>Serviço CloudSimple

Com o serviço CloudSimple, você pode criar e gerenciar todos os recursos associados às soluções do VMware por CloudSimple do portal do Azure. Crie um recurso de serviço em todas as regiões em que você pretende usar o serviço.

Saiba mais sobre o [CloudSimple serviço](cloudsimple-service.md).

## <a name="cloudsimple-node"></a>Nó CloudSimple

Um nó de CloudSimple é um host de computação e armazenamento hiperconvergente dedicado e bare-metal, em que o hipervisor VMware ESXi é implantado. Esse nó, em seguida, é incorporado no VMware vSphere vCenter, vSAN e plataformas NSX. Serviços de rede CloudSimple e serviços de rede de borda também são habilitados. Cada nó serve como uma unidade de capacidade de computação e armazenamento que você pode provisionar para criar [CloudSimple de nuvens privadas](cloudsimple-private-cloud.md). Provisionar ou reserva de nós em uma região em que o serviço de CloudSimple está disponível.


Saiba mais sobre [CloudSimple nós](cloudsimple-node.md).

## <a name="cloudsimple-private-cloud"></a>CloudSimple de nuvem privada

Uma nuvem privada de CloudSimple é um ambiente isolado de pilha VMware gerenciado por um servidor do vCenter em seu próprio domínio de gerenciamento. A pilha do VMware inclui hosts ESXi, vSphere, vCenter, vSAN e NSX. As execuções de pilha em (hardware dedicado e isolado bare-metal) de nós dedicados e é consumido por usuários por meio de ferramentas nativas do VMware que incluem o vCenter e NSX Manager. Nós dedicados são implantados em locais do Azure e são gerenciados pelo Azure. Cada nuvem privada pode ser segmentado e protegido usando serviços de rede, como VLANs e sub-redes e tabelas de firewall. Conexões com o seu ambiente local e a rede do Azure são criadas usando seguras, privadas VPN e ExpressRoute do Azure, conexões.

Saiba mais sobre [CloudSimple de nuvem privada](cloudsimple-private-cloud.md).

## <a name="service-networking"></a>Rede de serviço

O serviço de CloudSimple fornece uma rede por região em que o seu serviço CloudSimple é implantado. A rede é um único espaço de endereço de TCP de camada 3 com roteamento ativado por padrão. Todas as nuvens privadas e as sub-redes criadas nessa região se comunicam entre si sem qualquer configuração adicional. Você pode criar grupos de portas distribuído no vCenter usando as VLANs. Você pode usar os seguintes recursos de rede para configurar e proteger seus recursos de carga de trabalho em sua nuvem privada:

* [VLANs e sub-redes](cloudsimple-vlans-subnets.md)
* [Tabelas de firewall](cloudsimple-firewall-tables.md)
* [Gateways de VPN](cloudsimple-vpn-gateways.md)
* [IP público](cloudsimple-public-ip-address.md)
* [Conexão de rede do Azure](cloudsimple-azure-network-connection.md)

## <a name="cloudsimple-virtual-machine"></a>Máquina de virtual CloudSimple

Com o serviço CloudSimple, você pode gerenciar máquinas virtuais VMware no portal do Azure. Um ou mais clusters ou pools de recursos do seu ambiente do vSphere podem ser mapeados para a assinatura na qual o serviço é criado.

Saiba mais sobre:

* [Máquinas virtuais de CloudSimple](cloudsimple-virtual-machines.md)
* [Mapeamento de assinatura do Azure](https://docs.azure.cloudsimple.com/azure-subscription-mapping/)
