---
title: Diretrizes para Azure NetApp Files planejamento de rede | Microsoft Docs
description: Descreve as diretrizes que podem ajudá-lo a criar uma arquitetura de rede eficaz usando Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: b-juche
ms.openlocfilehash: 087ecee053069a02e4d4dd6f636d05ea15269e2e
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68383493"
---
# <a name="guidelines-for-azure-netapp-files-network-planning"></a>Diretrizes para planejamento de rede do Azure NetApp Files

O planejamento da arquitetura de rede é um elemento fundamental da criação de qualquer infraestrutura de aplicativo. Este artigo ajuda você a criar uma arquitetura de rede eficaz para suas cargas de trabalho para se beneficiar dos recursos avançados do Azure NetApp Files.

Azure NetApp Files volumes são projetados para serem contidos em uma sub-rede de finalidade especial chamada [sub-rede delegada](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet) dentro de sua rede virtual do Azure. Portanto, você pode acessar os volumes diretamente de sua VNet, do VNets emparelhado na mesma região ou do local por meio de um gateway de rede virtual (ExpressRoute ou gateway de VPN), conforme necessário. A sub-rede é dedicada a Azure NetApp Files e não há conectividade com outros serviços do Azure ou com a Internet.

## <a name="considerations"></a>Considerações  

Você deve entender algumas considerações ao planejar Azure NetApp Files rede.

### <a name="constraints"></a>Restrições

Atualmente, não há suporte para os recursos abaixo para Azure NetApp Files: 

* NSGs (grupos de segurança de rede) aplicados à sub-rede delegada
* UDRs (rotas definidas pelo usuário) com o próximo salto como sub-rede de arquivos do Azure NetApp
* Políticas do Azure (por exemplo, políticas de nomenclatura personalizadas) na interface Azure NetApp Files
* Balanceadores de carga para tráfego de Azure NetApp Files

As seguintes restrições de rede se aplicam a Azure NetApp Files:

* O número de IPs em uso em uma VNet com Azure NetApp Files (incluindo VNets emparelhados) não pode exceder 1000.
* Em cada VNet (Rede virtual do Azure), apenas uma sub-rede pode ser delegada para o Azure NetApp Files.


### <a name="supported-network-topologies"></a>Topologias de rede com suporte

A tabela a seguir descreve as topologias de rede com suporte pelo Azure NetApp Files.  Ele também descreve as soluções alternativas para as topologias sem suporte. 

|    Topologias    |    Tem suporte    |     Solução alternativa    |
|-------------------------------------------------------------------------------------------------------------------------------|--------------------|-----------------------------------------------------------------------------|
|    Conectividade com o volume em uma VNet local    |    Sim    |         |
|    Conectividade com o volume em uma VNet emparelhada (mesma região)    |    Sim    |         |
|    Conectividade com o volume em uma VNet emparelhada (entre regiões ou emparelhamento global)    |    Não    |    Nenhum    |
|    Conectividade com um volume por meio do gateway de ExpressRoute    |    Sim    |         |
|    Conectividade do local para um volume em uma VNet spoke em um gateway de ExpressRoute e emparelhamento VNet com trânsito de gateway    |    Sim    |        |
|    Conectividade do local para um volume em uma VNet do spoke em um gateway de VPN    |    Sim    |         |
|    Conectividade do local para um volume em uma VNet do spoke por meio de gateway de VPN e emparelhamento VNet com trânsito de gateway    |    Sim    |         |


## <a name="virtual-network-for-azure-netapp-files-volumes"></a>Rede virtual para volumes de Azure NetApp Files

Esta seção explica os conceitos que o ajudam com o planejamento de rede virtual.

### <a name="azure-virtual-networks"></a>Redes virtuais do Azure

Antes de provisionar um volume Azure NetApp Files, você precisa criar uma rede virtual do Azure (VNet) ou usar uma que já exista em sua assinatura. A VNet define o limite de rede do volume.  Para obter mais informações sobre como criar redes virtuais, consulte a [documentação da rede virtual do Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

### <a name="subnets"></a>Sub-redes

Sub-redes segmentam a rede virtual em espaços de endereço separados que são utilizáveis pelos recursos do Azure nelas.  Azure NetApp Files volumes estão contidos em uma sub-rede de finalidade especial chamada [sub-rede delegada](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet). 

A delegação de sub-rede fornece permissões explícitas ao serviço de Azure NetApp Files para criar recursos específicos de serviço na sub-rede.  Ele usa um identificador exclusivo na implantação do serviço. Nesse caso, uma interface de rede é criada para permitir a conectividade com Azure NetApp Files.

Se você usar uma nova VNet, poderá criar uma sub-rede e delegar a sub-rede para Azure NetApp Files seguindo as instruções em [delegar uma sub-rede para Azure NetApp files](azure-netapp-files-delegate-subnet.md). Você também pode delegar uma sub-rede vazia existente que ainda não esteja delegada a outros serviços.

Se a VNet estiver emparelhada com outra VNet, você não poderá expandir o espaço de endereço da VNet. Por esse motivo, a nova sub-rede delegada precisa ser criada dentro do espaço de endereço da VNet. Se você precisar estender o espaço de endereço, deverá excluir o emparelhamento VNet antes de expandir o espaço de endereço.

### <a name="udrs-and-nsgs"></a>UDRs e NSGs

Não há suporte para UDRs (rotas definidas pelo usuário) e NSGs (grupos de segurança de rede) em sub-redes delegadas para Azure NetApp Files.

Como alternativa, você pode aplicar NSGs a outras sub-redes que permitem ou negam o tráfego de e para a sub-rede Azure NetApp Files delegada.  

## <a name="azure-native-environments"></a>Ambientes nativos do Azure

O diagrama a seguir ilustra um ambiente nativo do Azure:

![Ambiente de rede nativo do Azure](../media/azure-netapp-files/azure-netapp-files-network-azure-native-environment.png)

### <a name="local-vnet"></a>VNet local

Um cenário básico é criar ou conectar-se a um volume de Azure NetApp Files de uma VM (máquina virtual) na mesma VNet. Para a VNet 2 no diagrama acima, o volume 1 é criado em uma sub-rede delegada e pode ser montado na VM 1 na sub-rede padrão.

### <a name="vnet-peering"></a>Emparelhamento VNet

Se você tiver VNets adicionais na mesma região que precisam de acesso aos recursos uns dos outros, o VNets poderá ser conectado usando o [emparelhamento de VNet](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) para habilitar a conectividade segura por meio da infraestrutura do Azure. 

Considere VNet 2 e VNet 3 no diagrama acima. Se a VM 2 precisar se conectar à VM 3 ou ao volume 2, ou se a VM 3 precisar se conectar à VM 2 ou ao volume 1, você precisará habilitar o emparelhamento VNet entre VNet 2 e VNet 3. 

Além disso, considere um cenário em que a VNet 1 está emparelhada com VNet 2 e a VNet 2 é emparelhada com VNet 3 na mesma região. Os recursos da VNet 1 podem se conectar aos recursos na VNet 2, mas não podem se conectar aos recursos na VNet 3, a menos que VNet 1 e VNet 3 estejam emparelhados. 

No diagrama acima, embora a VM 3 possa se conectar ao volume 1, a VM 4 não pode se conectar ao volume 2.  O motivo disso é que os VNets spoke não são emparelhados e o _Roteamento de trânsito não tem suporte por emparelhamento VNet_.

## <a name="hybrid-environments"></a>Ambientes híbridos

O diagrama a seguir ilustra um ambiente híbrido: 

![Ambiente de rede híbrida](../media/azure-netapp-files/azure-netapp-files-network-hybrid-environment.png)

No cenário híbrido, os aplicativos de data centers locais precisam de acesso aos recursos no Azure.  Esse é o caso que você deseja estender seu datacenter para o Azure, ou você deseja usar os serviços nativos do Azure ou para a recuperação de desastres. Consulte [Opções de planejamento do gateway de VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=%2fazure%2fvirtual-network%2ftoc.json#planningtable) para obter informações sobre como conectar vários recursos locais a recursos no Azure por meio de uma VPN site a site ou um ExpressRoute.

Em uma topologia de hub-spoke híbrida, a VNet do Hub no Azure atua como um ponto central de conectividade para sua rede local. Os spokes são VNets emparelhados com o Hub e podem ser usados para isolar cargas de trabalho.

Dependendo da configuração, você pode conectar recursos locais a recursos no Hub e nos spokes.

Na topologia ilustrada acima, a rede local está conectada a uma VNet de Hub no Azure e há 2 spoke VNets na mesma região emparelhada com a VNet do Hub.  Nesse cenário, as opções de conectividade com suporte para volumes Azure NetApp Files são as seguintes:

* Os recursos locais VM 1 e VM 2 podem se conectar ao volume 1 no Hub por meio de um circuito VPN site a site ou de ExpressRoute. 
* Os recursos locais VM 1 e VM 2 podem se conectar ao volume 2 ou volume 3 em um VPN site a site e um emparelhamento de vnet regional.
* A VM 3 na VNet do Hub pode se conectar ao volume 2 na VNet 1 do spoke e no volume 3 na VNet 2 do spoke.
* A VM 4 do spoke VNet 1 e a VM 5 da VNet 2 do spoke podem se conectar ao volume 1 na VNet do Hub.

A VM 4 na VNet do spoke 1 não pode se conectar ao volume 3 na VNet 2 do spoke. Além disso, a VM 5 no spoke VNet2 não pode se conectar ao volume 2 na VNet 1 do spoke. Esse é o caso porque os VNets spoke não são emparelhados e o _Roteamento de trânsito não tem suporte por emparelhamento VNet_.

## <a name="next-steps"></a>Próximas etapas

[Delegar uma sub-rede ao Azure NetApp Files](azure-netapp-files-delegate-subnet.md)
