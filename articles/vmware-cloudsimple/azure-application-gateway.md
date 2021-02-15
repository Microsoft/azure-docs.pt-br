---
title: Usar Aplicativo Azure gateway com máquinas virtuais VMware
description: Descreve como usar o gateway de aplicativo do Azure para gerenciar o tráfego da Web de entrada para servidores Web em execução em máquinas virtuais VMware ganhe o ambiente de nuvem privada do CloudSimple
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d14fdc4e2b2b1dfca8194077acc0453808611946
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/05/2021
ms.locfileid: "97897936"
---
# <a name="use-azure-application-gateway-with-vmware-virtual-machines-in-the-cloudsimple-private-cloud-environment"></a>Usar Aplicativo Azure gateway com máquinas virtuais VMware no ambiente de nuvem privada do CloudSimple

Você pode usar o gateway de Aplicativo Azure para gerenciar o tráfego da Web de entrada para seus servidores Web em execução em máquinas virtuais VMware em seu ambiente de nuvem privada do CloudSimple.

Ao aproveitar Aplicativo Azure gateway em uma implantação híbrida pública privada, você pode gerenciar o tráfego da Web para seus aplicativos, fornecer um front-end seguro e descarregar o processamento de TLS para seus serviços em execução no ambiente VMware. Aplicativo Azure gateway roteia o tráfego da Web de entrada para instâncias do pool de back-end que residem em ambientes VMware de acordo com as regras e investigações de integridade configuradas.

Essa solução de gateway Aplicativo Azure requer que você:

* Tenho uma assinatura do Azure.
* Crie e configure uma rede virtual do Azure e uma sub-rede dentro da rede virtual.
* Crie e configure regras de NSG e emparelhe sua vNet usando o ExpressRoute para sua nuvem privada do CloudSimple.
* Crie & configure sua nuvem privada.
* Criar & configurar seu gateway de Aplicativo Azure.

## <a name="azure-application-gateway-deployment-scenario"></a>Cenário de implantação de gateway Aplicativo Azure

Nesse cenário, o gateway de Aplicativo Azure é executado em sua rede virtual do Azure. A rede virtual está conectada à sua nuvem privada por um circuito do ExpressRoute. Todas as sub-redes na nuvem privada são acessíveis por IP das sub-redes da rede virtual.

![Azure Load Balancer na rede virtual do Azure](media/load-balancer-use-case.png)

## <a name="how-to-deploy-the-solution"></a>Como implantar a solução

O processo de implantação consiste nas seguintes tarefas:

1. [Verificar se os pré-requisitos foram atendidos](#1-verify-prerequisites)
2. [Conectar sua conexão virtual do Azure à nuvem privada](#2-connect-your-azure-virtual-network-to-your-private-cloud)
3. [Implantar um gateway de aplicativo do Azure](#3-deploy-an-azure-application-gateway)
4. [Criar e configurar o pool de VM do servidor Web em sua nuvem privada](#4-create-and-configure-a-web-server-vm-pool-in-your-private-cloud)

## <a name="1-verify-prerequisites"></a>1. verificar pré-requisitos

Verifique se esses pré-requisitos foram atendidos:

* Um Azure Resource Manager e uma rede virtual já foram criados.
* Uma sub-rede dedicada (para o gateway de aplicativo) em sua rede virtual do Azure já foi criada.
* Uma nuvem privada do CloudSimple já foi criada.
* Não há nenhum conflito de IP entre as sub-redes IP na rede virtual e sub-redes na nuvem privada.

## <a name="2-connect-your-azure-virtual-network-to-your-private-cloud"></a>2. Conecte sua rede virtual do Azure à sua nuvem privada

Para conectar sua rede virtual do Azure à sua nuvem privada, siga este processo.

1. [No portal do CloudSimple, copie as informações de emparelhamento do ExpressRoute](virtual-network-connection.md).

2. [Configure um gateway de rede virtual para sua rede virtual do Azure](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md).

3. [Vincule sua rede virtual ao circuito do ExpressRoute CloudSimple](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription).

4. [Use as informações de emparelhamento que você copiou para vincular sua rede virtual ao circuito do ExpressRoute](virtual-network-connection.md).

## <a name="3-deploy-an-azure-application-gateway"></a>3. implantar um gateway de aplicativo do Azure

As instruções detalhadas para isso estão disponíveis em [criar um gateway de aplicativo com regras de roteamento com base em caminho usando o portal do Azure](../application-gateway/create-url-route-portal.md). Aqui está um resumo das etapas necessárias:

1. Crie uma rede virtual em sua assinatura e grupo de recursos.
2. Crie uma sub-rede (para ser usada como sub-rede dedicada) em sua rede virtual.
3. Criar um gateway de aplicativo padrão (opcionalmente, habilitar WAF): na página inicial do portal do Azure, clique em **recursos**  >  **rede**  >  **Gateway de aplicativo** no canto superior esquerdo da página. Selecione o SKU e o tamanho padrão e forneça a assinatura do Azure, o grupo de recursos e as informações de local. Se necessário, crie um novo IP público para esse gateway de aplicativo e forneça detalhes sobre a rede virtual e a sub-rede dedicada para o gateway de aplicativo.
4. Adicione um pool de back-end com máquinas virtuais e adicione-a ao seu gateway de aplicativo.

## <a name="4-create-and-configure-a-web-server-vm-pool-in-your-private-cloud"></a>4. criar e configurar um pool de VM do servidor Web em sua nuvem privada

No vCenter, crie VMs com o sistema operacional e o servidor Web de sua escolha (como Windows/IIS ou Linux/Apache). Escolha uma sub-rede/VLAN designada para a camada da Web em sua nuvem privada. Verifique se pelo menos um vNIC das VMs do servidor Web está na sub-rede da camada da Web.
