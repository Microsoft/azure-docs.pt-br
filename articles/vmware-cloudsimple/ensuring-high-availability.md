---
title: Garantir alta disponibilidade do aplicativo ao executar no VMware no Azure
description: Descreve os recursos de alta disponibilidade do CloudSimple para resolver cenários comuns de falha de aplicativo para aplicativos executados em uma nuvem privada do CloudSimple
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8bb1f8bb2aaeab88e5a9ea19534c8983af8c1626
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/05/2021
ms.locfileid: "97895743"
---
# <a name="ensure-application-high-availability-when-running-in-vmware-on-azure"></a>Garantir alta disponibilidade do aplicativo ao executar no VMware no Azure

A solução CloudSimple fornece alta disponibilidade para seus aplicativos em execução no VMware no ambiente do Azure. A tabela a seguir lista os cenários de falha e os recursos de alta disponibilidade associados.

| Cenário de falha | Aplicativo protegido? | Recurso de HA da plataforma | Recurso VMware HA | Recurso de HA do Azure |
------------ | ------------- | ------------ | ------------ | ------------- |
| Falha no Disco | YES | Substituição rápida do nó com falha | [Sobre a política de armazenamento padrão do vSAN](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.virtualsan.doc/GUID-C228168F-6807-4C2A-9D74-E584CAF49A2A.html) |
| Falha do ventilador | YES | Ventiladores redundantes, substituição rápida do nó com falha |  |  |
| Falha de NIC | YES | NIC redundante, substituição rápida do nó com falha
| Falha de energia do host | YES | Fonte de energia redundante |  |  |
| Falha no host ESXi | YES | substituição rápida do nó com falha | [VMware vSphere alta disponibilidade](https://www.vmware.com/products/vsphere/high-availability.html) |  |  |
| Falha de VM | YES | [Balanceadores de carga](load-balancers.md)  | [VMware vSphere alta disponibilidade](https://www.vmware.com/products/vsphere/high-availability.html) | Azure Load Balancer para VMs VMware sem estado |
| Falha na porta do comutador de folha | YES | NIC redundante |  |  |
| Falha de comutador de folha | YES | Opções de folha redundantes |  |  |
| Falha no rack | YES | Grupos de posicionamento |  |  |
| Conectividade de rede para o DC local | YES  | Serviços de rede redundantes |  | Circuitos de ER redundantes |
| Conectividade de rede para o Azure | YES | |  | Circuitos de ER redundantes |
| Falha no datacenter | YES |  |  | Zonas de disponibilidade |
| Falha regional | YES  |  |  | Regiões do Azure |

A solução Azure VMware da CloudSimple fornece os seguintes recursos de alta disponibilidade.

## <a name="fast-replacement-of-failed-node"></a>Substituição rápida do nó com falha

O software plano de controle CloudSimple monitora continuamente a integridade dos clusters VMware e detecta quando um nó ESXi falha. Em seguida, ele adiciona automaticamente um novo host ESXi ao cluster do VMware afetado de seu pool de nós prontamente disponíveis e leva o nó com falha do cluster. Essa funcionalidade garante que a capacidade de reposição no cluster VMware seja restaurada rapidamente para que a resiliência do cluster fornecida pelo vSAN e VMware HA seja restaurada.

## <a name="placement-groups"></a>Grupos de posicionamento

Um usuário que cria uma nuvem privada pode selecionar uma região do Azure e um grupo de posicionamento dentro da região selecionada. Um grupo de posicionamento é um conjunto de nós espalhados por vários racks, mas dentro do mesmo segmento de rede de linhagem. Os nós dentro do mesmo grupo de posicionamento podem alcançar um ao outro com um máximo de dois saltos de interruptor extras. Um grupo de posicionamento sempre está dentro de uma única zona de disponibilidade do Azure e abrange vários racks. O plano de controle CloudSimple distribui nós de uma nuvem privada em vários racks com base no melhor esforço. Os nós em diferentes grupos de posicionamento têm garantia de serem colocados em diferentes racks.

## <a name="availability-zones"></a>Zonas de disponibilidade

As zonas de disponibilidade são uma oferta de alta disponibilidade que protege os aplicativos e dados contra falhas do datacenter. As zonas de disponibilidade são locais físicos especiais em uma região do Azure. Cada zona é composta por um ou mais datacenters equipados com energia, resfriamento e rede independentes. Cada região tem uma zona de disponibilidade. Para obter mais informações, consulte [o que são zonas de disponibilidade no Azure?](../availability-zones/az-overview.md).

## <a name="redundant-azure-expressroute-circuits"></a>Circuitos do Azure ExpressRoute redundantes

A conectividade do Data Center com a vNet do Azure usando o ExpressRoute tem circuitos redundantes para fornecer um link de conectividade de rede altamente disponível.

## <a name="redundant-networking-services"></a>Serviços de rede redundantes

Todos os serviços de rede CloudSimple para a nuvem privada (incluindo VLAN, firewall, endereços IP públicos, Internet e VPN) foram projetados para serem altamente disponíveis e capazes de dar suporte ao SLA de serviço.

## <a name="azure-layer-7-load-balancer-for-stateless-vmware-vms"></a>Load Balancer de camada 7 do Azure para VMs VMware sem estado

Os usuários podem colocar uma Load Balancer de camada 7 do Azure na frente das VMs da camada da Web sem estado em execução no ambiente VMware para obter alta disponibilidade para a camada da Web.

## <a name="azure-regions"></a>Regiões do Azure

Uma região do Azure é um conjunto de data centers implantados em um perímetro definido por latência e conectados por meio de uma rede regional de baixa latência. Para obter detalhes, consulte [regiões do Azure](https://azure.microsoft.com/global-infrastructure/regions).
