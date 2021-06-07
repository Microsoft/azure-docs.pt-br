---
title: Configurar a BCDR (continuidade dos negócios e recuperação de desastre) na rede virtual privada (VPN) do Azure Stack Edge
description: Descreve como configurar o BCDR no Azure Stack VPN de borda.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/17/2020
ms.author: alkohli
ms.openlocfilehash: a35a7e5e5c7eccf006f18badad88656e8bc73453
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100367683"
---
# <a name="configure-business-continuity-and-disaster-recovery-for-azure-stack-edge-vpn"></a>Configurar a continuidade dos negócios e a recuperação de desastre para VPN Azure Stack Edge

[!INCLUDE [applies-to-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-pro-r-mini-r-sku.md)]

Este artigo descreve como configurar a BCDR (continuidade dos negócios e a recuperação de desastre) em uma VPN (rede virtual privada) configurada em um dispositivo de borda Azure Stack.

Este artigo se aplica ao dispositivo Azure Stack Edge pro R e Azure Stack o mini-R Edge.

## <a name="configure-failover-to-a-paired-region"></a>Configurar o failover para uma região emparelhada

O dispositivo Azure Stack Edge usa outros serviços do Azure, por exemplo, o armazenamento do Azure. Você pode configurar o BCDR em qualquer serviço específico do Azure que é usado pelo dispositivo Azure Stack Edge. Se um serviço do Azure usado pelo Azure Stack Edge fizer failover para sua região emparelhada, o dispositivo Azure Stack Edge agora se conectará aos novos endereços IP e a comunicação não será criptografada duplamente. 

O dispositivo Azure Stack Edge usa o túnel dividido e todos os dados e serviços configurados na região de residência (a região associada ao dispositivo Azure Stack Edge) passam pelo túnel VPN. Se os serviços do Azure fizerem failover para uma região emparelhada que está fora da região de residência, os dados não passarão por VPN e, portanto, não serão criptografados duplicadamente. 

Nesse cenário, normalmente apenas alguns serviços do Azure são afetados. Para resolver esse problema, as seguintes alterações devem ser feitas na configuração de VPN do Azure Stack Edge:

1. Adicione o intervalo de IPS de serviço do Azure de failover nas rotas inclusivas para VPN no Azure Stack Edge. Os serviços começarão a ser roteados pela VPN.

    Para adicionar as rotas inclusivas, você precisa baixar o arquivo JSON que tem as rotas específicas do serviço. Certifique-se de atualizar esse arquivo com as novas rotas.
2. Adicione o intervalo (s) IP do serviço do Azure correspondente na tabela de rotas do Azure.
3. Adicione as rotas ao firewall.

> [!NOTE]
>
> 1. O failover de um gateway de VPN do Azure e da VNET (rede virtual) do Azure é abordado na seção [recuperar de uma região do Azure que falhou devido a um desastre](#recover-from-a-failed-azure-region).
> 2. Os intervalos de IP adicionados na tabela de rotas do Azure podem cruzar o limite de 400. Se isso ocorrer, você precisará seguir as diretrizes na seção, [mover de uma região do Azure para outra região do Azure](#move-from-an-azure-region-to-another).

## <a name="recover-from-a-failed-azure-region"></a>Recuperar de uma região do Azure com falha

Caso toda a região do Azure falhe devido a um evento catastrófico, como o terremoto, todos os serviços do Azure nessa região, incluindo o serviço de borda do Azure Stack, realizarão failover. Como há vários serviços, as rotas inclusivas podem facilmente variar em alguns centenas. O Azure tem uma limitação de 400 rotas. 

Quando a região faz failover, a vnet (rede virtual) também faz failover para a nova região e, portanto, o gateway de rede virtual (gateway de VPN). Para resolver essa alteração, faça as seguintes alterações na configuração de VPN do Azure Stack Edge:

1. Mova sua vnet para a região de destino. Para obter mais informações, consulte: [mover uma rede virtual do Azure para outra região por meio do portal do Azure](../virtual-network/move-across-regions-vnet-portal.md).
2. Implante um novo gateway de VPN do Azure na região de destino onde você moveu a vnet. Para obter mais informações, consulte [criar um gateway de rede virtual](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw).
3. Atualize Azure Stack configuração de VPN de borda para usar o gateway de VPN acima na conexão VPN e, em seguida, selecione a região de destino para adicionar rotas que usam o gateway de VPN.
4. Atualize a tabela de rotas de entrada do Azure se o pool de endereços do cliente também for alterado. 

## <a name="move-from-an-azure-region-to-another"></a>Mover de uma região do Azure para outra

Você pode mover seu Azure Stack dispositivo de borda de um local para outro local. Para usar uma região mais próxima de onde o dispositivo está implantado, você precisará configurar o dispositivo para uma nova região de residência. Faça as seguintes alterações:

1. Você pode atualizar Azure Stack configuração de VPN de borda para usar o gateway de VPN de uma nova região e selecionar a nova região para adicionar rotas que usam o gateway de VPN.

## <a name="next-steps"></a>Próximas etapas

[Faça backup do dispositivo Azure Stack Edge](azure-stack-edge-gpu-prepare-device-failure.md).