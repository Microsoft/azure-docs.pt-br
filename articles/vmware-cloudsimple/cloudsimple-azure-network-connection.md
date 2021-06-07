---
title: Solução VMware por CloudSimple-conexões de rede do Azure
description: Saiba como conectar sua rede virtual do Azure à sua rede de região CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: cfd4d65b07cf255ac2b60d6bf8376723a997374e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "77025071"
---
# <a name="azure-network-connections-overview"></a>Visão geral das conexões de rede do Azure

Ao criar um serviço CloudSimple em uma região e criar nós, você pode:

* Solicite um circuito do Azure ExpressRoute e anexe-o à rede CloudSimple nessa região.
* Conecte sua rede de região do CloudSimple à sua rede virtual do Azure ou à sua rede local usando o Azure ExpressRoute.
* Forneça acesso aos serviços em execução na sua assinatura do Azure ou na rede local de seu ambiente de nuvem privada.

A conexão do ExpressRoute é de alta largura de banda com baixa latência.

## <a name="benefits"></a>Benefícios

A conexão de rede do Azure permite que você:

* Use o Azure como um destino de backup para máquinas virtuais em sua nuvem privada.
* Implante servidores KMS em sua assinatura do Azure para criptografar seu repositório de armazenamento vSAN de nuvem privada.
* Use aplicativos híbridos em que a camada da Web do aplicativo é executada na nuvem pública enquanto as camadas do aplicativo e do banco de dados são executadas em sua nuvem privada.

## <a name="azure-virtual-network-connection"></a>Conexão de rede virtual do Azure

Nuvens privadas podem ser conectadas aos recursos do Azure usando o ExpressRoute.  A conexão do ExpressRoute permite que você acesse recursos em execução na sua assinatura do Azure de sua nuvem privada.  Essa conexão permite que você estenda sua rede de nuvem privada para sua rede virtual do Azure.  As rotas da rede CloudSimple serão trocadas com sua rede virtual do Azure via BGP.  Se você tiver configurado o emparelhamento de rede virtual, todas as redes virtuais emparelhadas poderão ser acessadas da sua rede CloudSimple.

![Conexão do Azure ExpressRoute com a rede virtual](media/cloudsimple-azure-network-connection.png)

## <a name="expressroute-connection-to-on-premises-network"></a>Conexão do ExpressRoute à rede local

Você pode conectar seu circuito do Azure ExpressRoute existente à sua região do CloudSimple. O recurso de Alcance Global ExpressRoute é usado para conectar os dois circuitos entre si.  Uma conexão é estabelecida entre os circuitos do ExpressRoute local e CloudSimple.  Essa conexão permite que você estenda suas redes locais para a rede de nuvem privada. As rotas de sua rede CloudSimple serão trocadas por meio de BGP com sua rede local.

![Conexão do ExpressRoute local-Alcance Global](media/cloudsimple-global-reach-connection.png)

## <a name="connection-to-on-premises-network-and-azure-virtual-network"></a>Conexão à rede local e à rede virtual do Azure

As conexões com a rede local e a rede virtual do Azure podem coexistir de sua rede CloudSimple.  A conexão usa BGP para trocar rotas entre a rede local, a rede virtual do Azure e a rede CloudSimple.  Quando você conecta sua rede CloudSimple à rede virtual do Azure em presença de Alcance Global conexão, as rotas de rede virtual do Azure ficarão visíveis em sua rede local.  A troca de rota ocorre no Azure entre os roteadores de borda.

![Conexão do ExpressRoute local com a conexão de rede virtual do Azure](media/cloudsimple-global-reach-and-vnet-connection.png)

### <a name="important-considerations"></a>Considerações importantes

Conectar-se à rede CloudSimple da rede local e da rede virtual do Azure permite a troca de rotas entre todas as redes.

* A rede virtual do Azure ficará visível na rede local e na rede CloudSimple.
* Se você se conectou à rede virtual do Azure da rede local, a conexão à rede CloudSimple usando Alcance Global permitirá o acesso a redes virtuais da rede CloudSimple.
* Os endereços de sub-rede **não devem** se sobrepor entre as redes conectadas.
* CloudSimple **não** anunciará a rota padrão para as conexões do ExpressRoute
* Se o roteador local anunciar a rota padrão, o tráfego da rede CloudSimple e da rede virtual do Azure usará a rota padrão anunciada.  Como resultado, as máquinas virtuais no Azure não podem ser acessadas usando endereços IP públicos.

## <a name="next-steps"></a>Próximas etapas

* [Conectar a rede virtual do Azure ao CloudSimple usando o ExpressRoute](virtual-network-connection.md)
* [Conectar de local para CloudSimple usando o ExpressRoute](on-premises-connection.md)
