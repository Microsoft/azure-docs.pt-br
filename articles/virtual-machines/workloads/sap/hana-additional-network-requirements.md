---
title: Requisitos de rede adicionais para SAP HANA no Azure (instâncias grandes) | Microsoft Docs
description: Requisitos de rede adicionais para SAP HANA no Azure (instâncias grandes).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 953ee5d40a3a4c49d7cc01de804ae5c76ceedc7a
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67709788"
---
# <a name="additional-network-requirements-for-large-instances"></a>Requisitos de rede adicionais para instâncias grandes

Você pode ter requisitos de rede adicionais como parte de uma implantação de instâncias grandes do SAP HANA no Azure.

## <a name="add-more-ip-addresses-or-subnets"></a>Adicionar mais endereços IP ou sub-redes

Use o portal do Azure, o PowerShell ou a CLI do Azure quando adicionar mais endereços IP ou sub-redes.

Adicione o novo intervalo de endereços IP como um novo intervalo para o espaço de endereço de rede virtual, em vez de gerar um novo intervalo agregado. Envie essa alteração para a Microsoft. Isso permite que você se conecte desse novo intervalo de endereços IP com as unidades de instância grande HANA no seu cliente. Abra uma solicitação de suporte do Azure para obter o novo espaço de endereço da rede virtual adicionado. Após receber a confirmação, realize as próximas etapas.

Para criar uma sub-rede adicional do portal do Azure, confira [Criar uma rede virtual usando o portal do Azure](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network). Para criar um do PowerShell, confira [Criar uma rede virtual usando o PowerShell](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network).

## <a name="add-virtual-networks"></a>Adicionar redes virtuais

Depois de conectar inicialmente uma ou mais redes virtuais do Azure, talvez você queira conectar mais que acessem o SAP HANA no Azure (instâncias grandes). Primeiro, envie uma solicitação de suporte do Azure. Nessa solicitação, inclua informações específicas identificando a implantação do Azure específica. Também inclua o intervalo de espaço de endereços IP ou intervalos do espaço de endereços de rede virtual do Azure. O SAP HANA no Gerenciamento de Serviços da Microsoft então fornece as informações necessárias para se conectar a redes virtuais adicionais e ao Azure ExpressRoute. Para cada rede virtual, você precisa de uma chave de autorização exclusiva para se conectar ao circuito do ExpressRoute nas Instâncias grandes do HANA.

## <a name="increase-expressroute-circuit-bandwidth"></a>Aumentar a largura de banda do circuito de ExpressRoute

Consulte com o SAP HANA no Gerenciamento de Serviço da Microsoft. Se você for aconselhado a aumentar a largura de banda do SAP HANA no circuito de ExpressRoute do Azure (instâncias grandes), crie uma solicitação de suporte do Azure. (Você pode solicitar um aumento para uma largura de banda do circuito único até um máximo de 10 Gbps). Em seguida, você recebe uma notificação após a operação ter sido concluída. Você não precisa fazer mais nada para habilitar essa velocidade superior no Azure.

## <a name="add-an-additional-expressroute-circuit"></a>Adicionar um circuito de ExpressRoute adicional

Consulte com o SAP HANA no Gerenciamento de Serviço da Microsoft. Se você for aconselhado a adicionar um circuito adicional do ExpressRoute, crie uma solicitação de suporte do Azure (incluindo uma solicitação para obter informações de autorização para conectar-se ao novo circuito). Antes de fazer a solicitação, você deve definir o espaço de endereço usado nas redes virtuais. O SAP HANA no Gerenciamento de Serviços da Microsoft então pode fornecer autorização.

Quando o novo circuito for criado e o SAP HANA na configuração de Gerenciamento de Serviços da Microsoft estiver concluído, você receberá uma notificação com as informações que você precisa para continuar. Você não poderá se conectar às redes virtuais do Azure com esse circuito adicional se elas já estiverem conectadas a outro circuito do ExpressRoute do SAP HANA no Azure (instância grande) na mesma região do Azure.

## <a name="delete-a-subnet"></a>Excluir uma sub-rede

Para remover uma sub-rede da rede virtual, você pode usar o portal do Azure, o PowerShell ou a CLI do Azure. Se o espaço de endereços ou intervalo de endereços IP de rede virtual do Azure estiver em um intervalo agregado, não haverá nenhum acompanhamento para você com a Microsoft. (Observe, entretanto, que a rede virtual ainda está propagando o espaço de endereços de rota BGP que inclui a sub-rede excluída.) Você pode ter definido o espaço de endereços ou o intervalo de endereços de rede virtual do Azure como vários intervalos de endereços IP dos quais um foi atribuído à sua sub-rede excluída. Exclua esses elementos de seu espaço de endereço de rede virtual. Em seguida, informe o SAP HANA no Gerenciamento de Serviços da Microsoft para removê-lo dos intervalos com que o SAP HANA no Azure (instâncias grandes) tem permissão para se comunicar.

Para obter informações, confira [Excluir uma sub-rede](../../../virtual-network/virtual-network-manage-subnet.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#delete-a-subnet).

## <a name="delete-a-virtual-network"></a>Excluir uma rede virtual

Para obter informações, confira [Excluir uma rede virtual](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#delete-a-virtual-network).

O SAP HANA no Gerenciamento de Serviços Microsoft remove as autorizações existentes no SAP HANA no circuito de ExpressRoute do Azure (instâncias grandes). Também remove o espaço de endereço ou o intervalo de endereços IP da rede virtual do Azure para a comunicação com instâncias grandes HANA.

Depois de remover a rede virtual, abra uma solicitação de suporte do Azure para fornecer os intervalos de espaço de endereços IP a serem removidos.

Para garantir que você remova tudo, exclua a conexão do ExpressRoute, gateway de rede virtual, o IP público do gateway de rede virtual e a rede virtual.

## <a name="delete-an-expressroute-circuit"></a>Excluir um circuito do ExpressRoute

Para remover um SAP HANA adicional no circuito de ExpressRoute do Azure (instâncias grandes), abra uma solicitação de suporte do Azure com o SAP HANA no Gerenciamento de Serviços da Microsoft. Solicite que o circuito seja excluído. Na assinatura do Azure, você pode excluir ou manter a rede virtual conforme necessário. No entanto, você deve excluir a conexão entre o circuito de ExpressRoute de instâncias grandes HANA e o gateway de rede virtual vinculado.

## <a name="next-steps"></a>Próximas etapas

- [Como instalar e configurar o SAP HANA (instâncias grandes) no Azure](hana-installation.md)
