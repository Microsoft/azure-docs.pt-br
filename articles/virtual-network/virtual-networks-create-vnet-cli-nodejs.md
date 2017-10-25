---
title: Criar uma rede virtual usando a CLI do Azure 1.0 | Microsoft Docs
description: Saiba como criar uma rede virtual usando a CLI do Azure 1.0 | Resource Manager.
services: virtual-network
documentationcenter: 
author: jimdial
manager: carmonm
editor: 
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/16/2017
ms.author: jdial
ms.openlocfilehash: f0649c5c8c04dda72d2f147601efb37217f9bade
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-virtual-network-using-the-azure-cli"></a>Criar uma rede virtual usando a CLI do Azure

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnet-intro-include.md)]

O Azure tem dois modelos de implantação: Azure Resource Manager e clássico. A Microsoft recomenda criar recursos por meio do modelo de implantação do Gerenciador de Recursos. Para saber mais sobre as diferenças entre os dois modelos, leia o artigo [Entender os modelos de implantação do Azure](../azure-resource-manager/resource-manager-deployment-model.md).

## <a name="cli-versions-to-complete-the-task"></a>Versões da CLI para concluir a tarefa
Você pode concluir a tarefa usando uma das seguintes versões da CLI:

- [CLI do Azure 2.0](virtual-networks-create-vnet-arm-cli.md) – nossa próxima geração de CLI para o modelo de implantação do resource manager
- [CLI 1.0 do Azure](#create-a-virtual-network) – nossa CLI para os modelos de implantação clássico e de gerenciamento de recursos (este artigo)

 
[!INCLUDE [virtual-networks-create-vnet-scenario-include](../../includes/virtual-networks-create-vnet-scenario-include.md)]

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

Para criar uma rede virtual usando a CLI do Azure, conclua as seguintes etapas:

1. Instale e configure a CLI do Azure seguindo as etapas do artigo [Instalar e configurar a CLI do Azure](../cli-install-nodejs.md).

2. Crie uma VNet e uma sub-rede:

    ```azurecli
    azure network vnet create --vnet TestVNet -e 192.168.0.0 -i 16 -n FrontEnd -p 192.168.1.0 -r 24 -l "Central US"
    ```

    Saída esperada:
   
            info:    Executing command network vnet create
            + Looking up network configuration
            + Looking up locations
            + Setting network configuration
            info:    network vnet create command OK

    Parâmetros usados:

   * **--vnet**. Nome da rede virtual a ser criada. Para o nosso cenário, *TestVNet*
   * **-e (ou --address-space)**. Espaço de endereço da rede virtual. Para o nosso cenário, *192.168.0.0*
   * **-i (ou -cidr)**. Máscara de rede no formato CIDR. Para o nosso cenário, *16*.
   * **-n (ou --subnet-name**). Nome da primeira sub-rede. Para o nosso cenário, *FrontEnd*.
   * **-p (ou --subnet-start-ip)**. Endereço IP inicial da sub-rede ou espaço de endereço da sub-rede. Em nosso cenário, *192.168.1.0*.
   * **-r (ou --subnet-cidr)**. Máscara de rede no formato CIDR para a sub-rede. Para o nosso cenário, *24*.
   * **-l (or --location)**. Região do Azure em que a VNet é criada. Para o nosso cenário, *Central US*.

3. Crie uma sub-rede:

    ```azurecli
    azure network vnet subnet create -t TestVNet -n BackEnd -a 192.168.2.0/24
    ```
   
    Saída esperada:

            info:    Executing command network vnet subnet create
            + Looking up network configuration
            + Creating subnet "BackEnd"
            + Setting network configuration
            + Looking up the subnet "BackEnd"
            + Looking up network configuration
            data:    Name                            : BackEnd
            data:    Address prefix                  : 192.168.2.0/24
            info:    network vnet subnet create command OK

    Parâmetros usados:

   * **-t (ou --vnet-name**. Nome da rede virtual onde a sub-rede será criada. Para o nosso cenário, *TestVNet*.
   * **-n (ou --name)**. Nome da nova sub-rede. Para o nosso cenário, *BackEnd*.
   * **-a (ou --address-prefix)**. Bloco CIDR da sub-rede. Para o nosso cenário, *192.168.2.0/24*.
   
4. Para exibir as propriedades da nova VNet:

    ```azurecli
    azure network vnet show
    ```
   
    Saída esperada:
   
            info:    Executing command network vnet show
            Virtual network name: TestVNet
            + Looking up the virtual network sites
            data:    Name                            : TestVNet
            data:    Location                        : Central US
            data:    State                           : Created
            data:    Address space                   : 192.168.0.0/16
            data:    Subnets:
            data:      Name                          : FrontEnd
            data:      Address prefix                : 192.168.1.0/24
            data:
            data:      Name                          : BackEnd
            data:      Address prefix                : 192.168.2.0/24
            data:
            info:    network vnet show command OK

## <a name="next-steps"></a>Próximas etapas

Saiba como se conectar:

- Uma VM (máquina virtual) em uma rede virtual lendo o artigo [Criar uma VM Linux](../virtual-machines/linux/quick-create-cli.md). Em vez de criar uma rede virtual e sub-rede nas etapas dos artigos, você pode selecionar uma rede virtual e uma sub-rede existente para se conectar a uma VM.
- A rede virtual para outras redes virtuais, lendo o artigo [Conectar VNets](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md).
- A rede virtual para uma rede local usando uma VPN (rede privada virtual) site a site ou um circuito ExpressRoute. Saiba como lendo [Conectar uma VNet a uma rede local usando uma VPN site a site](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md) e [Vincular uma VNet a um circuito ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md).