---
title: VLANs e sub-redes na solução do Azure VMware por CloudSimple
description: Saiba mais sobre VLANs e sub-redes na nuvem privada do CloudSimple e sobre a rede que o CloudSimple fornece onde o serviço CloudSimple está implantado.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e3235b394f739e8af846f30dfe51705d0926c843
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88140659"
---
# <a name="vlans-and-subnets-overview"></a>Visão geral de VLANs e sub-redes

O CloudSimple fornece uma rede por região na qual o serviço CloudSimple é implantado.  A rede é um único espaço de endereço de camada TCP 3 com roteamento habilitado por padrão.  Todas as nuvens e sub-redes privadas criadas nessa região podem se comunicar entre si sem nenhuma configuração adicional.  Você pode criar grupos de portas distribuídas no vCenter usando as VLANs.

![Topologia de rede CloudSimple](media/cloudsimple-network-topology.png)

## <a name="vlans"></a>VLANs

Uma VLAN (rede de camada 2) é criada para cada nuvem privada.  O tráfego de camada 2 permanece dentro do limite de uma nuvem privada, permitindo que você isole o tráfego local na nuvem privada.  Uma VLAN criada na nuvem privada pode ser usada para criar grupos de portas distribuídas somente nessa nuvem privada.  Uma VLAN criada em uma nuvem privada é configurada automaticamente em todas as opções conectadas aos hosts de uma nuvem privada.

## <a name="subnets"></a>Sub-redes

Você pode criar uma sub-rede ao criar uma VLAN definindo o espaço de endereço da sub-rede. Um endereço IP do espaço de endereço é atribuído como um gateway de sub-rede. Um único espaço de endereço de camada privada 3 é atribuído por cliente e região. Você pode configurar qualquer espaço de endereço não sobreposto RFC 1918, com sua rede local ou rede virtual do Azure, na sua região de rede.

Todas as sub-redes podem se comunicar entre si por padrão, reduzindo a sobrecarga de configuração para roteamento entre nuvens privadas. Os dados East-oeste em PCs na mesma região permanecem na mesma rede de camada 3 e são transferidos pela infraestrutura de rede local dentro da região. Nenhuma saída é necessária para a comunicação entre nuvens privadas em uma região. Essa abordagem elimina qualquer penalidade de desempenho de WAN/egresso na implantação de diferentes cargas de trabalho em nuvens privadas diferentes.

## <a name="vspherevsan-subnets-cidr-range"></a>intervalo de CIDR de sub-redes vSphere/vSAN

Uma nuvem privada é criada como um ambiente isolado do VMware Stack (hosts ESXi, vCenter, vSAN e NSX) gerenciado por um servidor vCenter.  Os componentes de gerenciamento são implantados na rede selecionada para o CIDR de sub-redes vSphere/vSAN.  O intervalo de CIDR de rede é dividido em sub-redes diferentes durante a implantação.

* Prefixo de intervalo CIDR de sub-redes vSphere/vSAN mínima: **/24**
* Prefixo de intervalo CIDR de sub-redes vSphere/vSAN máxima: **/21**

> [!CAUTION]
> Os endereços IP no intervalo CIDR vSphere/vSAN são reservados para uso pela infraestrutura de nuvem privada.  Não use o endereço IP nesse intervalo em qualquer máquina virtual.

### <a name="vspherevsan-subnets-cidr-range-limits"></a>limites de intervalo CIDR de sub-redes vSphere/vSAN

A seleção do tamanho do intervalo CIDR das sub-redes vSphere/vSAN tem um impacto no tamanho da sua nuvem privada.  A tabela a seguir mostra o número máximo de nós que você pode ter com base no tamanho do CIDR de sub-redes vSphere/vSAN.

| Comprimento do prefixo CIDR de sub-redes vSphere/vSAN especificadas | Número máximo de nós |
|---------------------------------------------------|-------------------------|
| /24 | 26 |
| /23 | 58 |
| /22 | 118 |
| /21 | 220 |

### <a name="management-subnets-created-on-a-private-cloud"></a>Sub-redes de gerenciamento criadas em uma nuvem privada

As seguintes sub-redes de gerenciamento são criadas quando você cria uma nuvem privada.

* **Gerenciamento do sistema**. VLAN e sub-rede para os hosts ESXi rede de gerenciamento, servidor DNS, servidor vCenter.
* **VMotion**. VLAN e sub-rede para ESXi hospeda a rede do ' vMotion '.
* **VSAN**. VLAN e sub-rede para a rede vSAN de hosts ESXi.
* **NsxtEdgeUplink1**. VLAN e sub-rede para uplinks de VLAN para uma rede externa.
* **NsxtEdgeUplink2**. VLAN e sub-rede para uplinks de VLAN para uma rede externa.
* **NsxtEdgeTransport**. VLAN e sub-rede para zonas de transporte controlam o alcance de redes de camada 2 no NSX-T.
* **NsxtHostTransport**. VLAN e sub-rede para zona de transporte de host.

### <a name="management-network-cidr-range-breakdown"></a>Divisão de intervalo CIDR de rede de gerenciamento

o intervalo CIDR de sub-redes vSphere/vSAN especificado é dividido em várias sub-redes.  A tabela a seguir mostra um exemplo da divisão para prefixos permitidos.  O exemplo usa 192.168.0.0 como o intervalo CIDR.

Exemplo:

| CIDR/prefixo de sub-redes vSphere/vSAN especificadas | 192.168.0.0/21 | 192.168.0.0/22 | 192.168.0.0/23 | 192.168.0.0/24 |
|---------------------------------|----------------|----------------|----------------|----------------|
| Gerenciamento do sistema | 192.168.0.0/24 | 192.168.0.0/24 | 192.168.0.0/25 | 192.168.0.0/26 |
| vMotion | 192.168.1.0/24 | 192.168.1.0/25 | 192.168.0.128/26 | 192.168.0.64/27 |
| vSAN | 192.168.2.0/24 | 192.168.1.128/25 | 192.168.0.192/26 | 192.168.0.96/27 |
| Servidor de transporte do NSX-T | 192.168.4.0/23 | 192.168.2.0/24 | 192.168.1.0/25 | 192.168.0.128/26 |
| Transporte de borda do NSX-T | 192.168.7.208/28 | 192.168.3.208/28 | 192.168.1.208/28 | 192.168.0.208/28 |
| NSX-T Edge Uplink1 | 192.168.7.224/28 | 192.168.3.224/28 | 192.168.1.224/28 | 192.168.0.224/28 |
| NSX-T Edge uplink2 | 192.168.7.240/28 | 192.168.3.240/28 | 192.168.1.240/28 | 192.168.0.240/28 |

## <a name="next-steps"></a>Próximas etapas

* [Criar e gerenciar VLANs e sub-redes](create-vlan-subnet.md)
