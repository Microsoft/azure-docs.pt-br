---
title: "Rotas definidas pelo usuário e Encaminhamento IP no Azure | Microsoft Docs"
description: "Saiba como configurar UDR (Rotas definidas pelo usuário) e Encaminhamento de IP para encaminhar o tráfego para as soluções de virtualização da rede no Azure."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
ms.assetid: c39076c4-11b7-4b46-a904-817503c4b486
ms.service: virtual-network
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6274e0101f6fb0864c8d1efaef7fcde78b8760c3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="user-defined-routes-and-ip-forwarding"></a>Encaminhamento IP e rotas definidas pelo usuário

Ao adicionar VMs (máquinas virtuais) a uma VNet (rede virtual) no Azure, você observará que as VMs podem se comunicar automaticamente com outras VMs na rede. Não é necessário especificar um gateway, mesmo que as VMs estejam em sub-redes diferentes. O mesmo vale para a comunicação entre as VMs para a Internet pública e até mesmo em suas instalações de rede quando houver uma conexão híbrida do Azure para o seu próprio datacenter.

Esse fluxo de comunicação é possível porque o Azure usa uma série de rotas do sistema para definir como o tráfego IP flui. As rotas de sistema controlam o fluxo de comunicação nos seguintes cenários:

* De dentro da mesma sub-rede.
* De uma sub-rede para outra em uma VNet.
* De VMs com a Internet.
* De uma VNet para outra VNet por meio de um gateway VPN.
* De uma rede virtual para outra rede virtual por meio de emparelhamento de rede virtual (encadeamento de serviço).
* De uma VNet à sua rede local por meio de um gateway de VPN.

A figura a seguir mostra uma configuração simples com uma VNet, duas sub-redes e algumas VMs, juntamente com as rotas do sistema que permitem o fluxo do tráfego IP.

![Rotas de sistema no Azure](./media/virtual-networks-udr-overview/Figure1.png)

Embora o uso de rotas do sistema facilite o tráfego automaticamente para a sua implantação, há casos em que você deseja controlar o roteamento de pacotes por meio de um dispositivo virtual. Você pode fazer isso criando rotas definidas pelo usuário que especificam o próximo salto para os pacotes que fluem para uma sub-rede específica indo, então, para o dispositivo virtual e habilitar o encaminhamento de IP para a VM em execução como o dispositivo virtual.

A figura abaixo mostra um exemplo das rotas definidas pelo usuário e do encaminhamento IP para forçar os pacotes enviados para uma sub-rede de outra a passar por um dispositivo virtual em uma terceira sub-rede.

![Rotas de sistema no Azure](./media/virtual-networks-udr-overview/Figure2.png)

> [!IMPORTANT]
> Rotas definidas pelo usuário são aplicadas ao tráfego que deixam uma sub-rede de qualquer recurso (como interfaces de rede conectadas às máquinas virtuais) na sub-rede. Não é possível criar rotas para especificar como o tráfego entra em uma sub-rede a partir da Internet, por exemplo. O dispositivo para onde você está encaminhando o tráfego não pode estar na mesma sub-rede onde se origina o tráfego. Sempre crie uma sub-rede separada para seus dispositivos. 
> 
> 

## <a name="route-resource"></a>Recurso de rota
Os pacotes são roteados através de uma rede TCP/IP com base em uma tabela de rotas definida em cada nó na rede física. Uma tabela de rotas é uma coleção de rotas individuais usadas para decidir para onde encaminhar pacotes com base no endereço IP de destino. Uma rota consiste no seguinte:

| Propriedade | Descrição | Restrições | Considerações |
| --- | --- | --- | --- |
| Prefixo de Endereço |O CIDR de destino ao qual a rota se aplica, como 10.1.0.0/16. |Deve ser um intervalo CIDR válido que represente endereços na Internet pública, na rede virtual do Azure ou no datacenter local. |Verifique se o **Prefixo do endereço** não contém o **Endereço do próximo salto**, caso contrário, seus pacotes entrarão em um loop, indo da origem para o próximo salto sem jamais chegar ao destino. |
| Tipo do próximo salto |O tipo de salto do Azure ao qual o pacote deve ser enviado. |Deve ser um dos seguintes valores:  <br/> **Rede Virtual**. Representa a rede virtual local. Por exemplo, se você tiver duas sub-redes, 10.1.0.0/16 e 10.2.0.0/16 na mesma rede virtual, a rota para cada sub-rede na tabela de rotas terá um valor do próximo salto da *Rede Virtual*. <br/> **Gateway de Rede Virtual**. Representa um Gateway de VPN S2S do Azure. <br/> **Internet**. Representa o gateway de Internet padrão fornecido pela Infraestrutura do Azure. <br/> **Dispositivo Virtual**. Representa um dispositivo virtual que você adicionou à sua rede virtual do Azure. <br/> **None**. Representa um buraco negro. Pacotes encaminhados a um buraco negro não serão encaminhados. |Considere o uso de uma **Solução de Virtualização** para direcionar o tráfego para uma VM ou para um endereço IP interno do Azure Load Balancer.  Esse tipo permite a especificação de um endereço IP, conforme descrito abaixo. Considere usar um tipo **None** para impedir que os pacotes sigam para um determinado destino. |
| Endereço do próximo salto |O endereço do próximo salto contém o endereço IP para o qual os pacotes devem ser encaminhados. Os valores de próximas salto são permitidos apenas em rotas em que o próximo salto é um *Dispositivo Virtual*. |Deve ser um endereço IP acessível na Rede Virtual onde a Rota Definida pelo Usuário é aplicada, sem passar por um **Gateway de Rede Virtual**. O endereço IP deve estar na mesma Rede Virtual onde ele é aplicado, ou em uma rede Virtual emparelhada. |Se o endereço IP representar uma VM, habilite o [encaminhamento IP](#IP-forwarding) no Azure para a VM. Se o endereço IP representa o endereço IP interno do Azure Load Balancer, verifique se você tem uma regra de balanceamento de carga correspondente para cada porta que você deseja fazer o balanceamento de carga.|

No Azure PowerShell, alguns dos valores "NextHopType" têm nomes diferentes:

* A Rede Virtual é VnetLocal
* O Gateway de Rede Virtual é VirtualNetworkGateway
* O Dispositivo Virtual é VirtualAppliance
* A Internet é Internet
* Nenhum é None

### <a name="system-routes"></a>Rotas do sistema
Cada sub-rede criada em uma rede virtual é associada automaticamente a uma tabela de rota que contém as seguintes regras de rota do sistema:

* **Regra de VNet local**: essa regra é criada automaticamente para todas as sub-redes em uma rede virtual. Ela especifica que há um link direto entre as VMs na VNet e não há nenhum próximo salto intermediário.
* **Regra local**: essa regra se aplica a todo o tráfego destinado ao intervalo de endereços locais e usa o gateway de VPN como o destino do próximo salto.
* **Regra de Internet**: essa regra manipula todo o tráfego destinado à Internet pública (prefixo de endereço 0.0.0.0/0) e usa o gateway de Internet de infraestrutura como o próximo salto para todo o tráfego destinado à Internet.

### <a name="user-defined-routes"></a>Rotas definidas pelo usuário
Para a maioria dos ambientes, serão necessárias apenas as rotas de sistema já definidas pelo Azure. No entanto, talvez seja necessário criar uma tabela de rotas e adicionar uma ou mais rotas em casos específicos, como:

* Túnel à força para a Internet através de sua rede local.
* Uso de dispositivos virtuais em seu ambiente do Azure.

Nos cenários acima, você precisará criar uma tabela de rotas e adicionar rotas definidas pelo usuário a ela. Você pode ter várias tabelas de rotas, e a mesma tabela de rotas pode ser associada a uma ou mais sub-redes. Cada sub-rede só pode ser associada a uma única tabela de rotas. Todas as VMs e serviços em nuvem em uma sub-rede usam a tabela de rotas associada a essa sub-rede.

As sub-redes contam com rotas de sistema até que uma tabela de rotas seja associada à sub-rede. Quando existe uma associação, o roteamento é feito com base em LPM (Correspondência de Prefixo mais Longo) entre as rotas definidas pelo usuário e as rotas de sistema. Se houver mais de uma rota com a mesma correspondência LPM, uma rota será selecionada com base em sua origem na seguinte ordem:

1. Rota definida pelo usuário
2. Rota BGP (quando o ExpressRoute é usado)
3. Rota de sistema

Para saber como criar rotas definidas pelo usuário, veja [Como criar rotas e habilitar o encaminhamento IP no Azure](virtual-network-create-udr-arm-template.md).

> [!IMPORTANT]
> As rotas definidas pelo usuário são aplicadas apenas a VMs do Azure e a serviços de nuvem. Por exemplo, se desejar adicionar um dispositivo virtual de firewall entre sua rede local e o Azure, você terá que criar uma rota definida pelo usuário para as tabelas de rotas do Azure que encaminham todo o tráfego direcionado ao espaço de endereço local para o dispositivo virtual. Você também pode adicionar uma UDR (rota definida pelo usuário) ao GatewaySubnet para encaminhar todo o tráfego do local para o Azure por meio do dispositivo virtual. Isso é uma adição recente.
> 
> 

### <a name="bgp-routes"></a>Rotas BGP
Se houver uma conexão ExpressRoute entre sua rede local e o Azure, você poderá habilitar o BGP para propagar rotas da rede local para o Azure. Essas rotas BGP são usadas da mesma maneira que as rotas do sistema e as rotas definidas pelo usuário em cada sub-rede do Azure. Para obter mais informações, consulte [Introdução ao ExpressRoute](../expressroute/expressroute-introduction.md).

> [!IMPORTANT]
> Você pode configurar seu ambiente do Azure para usar um túnel à força por meio de sua rede local, criando uma rota definida pelo usuário para a sub-rede 0.0.0.0/0 que usa o gateway de VPN como o próximo salto. No entanto, isso só funcionará se você estiver usando um gateway de VPN, não o ExpressRoute. Para o ExpressRoute, o túnel à força é configurado por meio do BGP.
> 
> 

## <a name="ip-forwarding"></a>Encaminhamento IP
Conforme descrito acima, uma das principais razões para criar uma rota definida pelo usuário é encaminhar o tráfego para um dispositivo virtual. Um dispositivo virtual é nada mais do que uma VM que executa um aplicativo usado para lidar com o tráfego de rede de alguma forma, como um firewall ou um dispositivo NAT.

Essa VM de dispositivo virtual deve ser capaz de receber o tráfego de entrada não endereçado a si mesma. Para permitir que uma VM receba o tráfego endereçado a outros destinos, você deve habilitar o Encaminhamento IP para a VM. Esta é uma configuração do Azure, não uma configuração no sistema operacional convidado.

## <a name="next-steps"></a>Próximas etapas
* Saiba como [criar rotas no modelo de implantação do Gerenciador de Recursos](virtual-network-create-udr-arm-template.md) e associá-las a sub-redes. 
* Saiba como [criar rotas no modelo de implantação clássico](virtual-network-create-udr-classic-ps.md) e associá-las a sub-redes.

