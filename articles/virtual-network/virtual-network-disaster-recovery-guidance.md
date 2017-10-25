---
title: "O que fazer caso uma interrupção de serviço do Azure afete as Redes Virtuais do Azure | Microsoft Docs"
description: "Saiba o que fazer caso uma interrupção de serviço do Azure afete as Redes Virtuais do Azure."
services: virtual-network
documentationcenter: 
author: NarayanAnnamalai
manager: jefco
editor: 
ms.assetid: ad260ab9-d873-43b3-8896-f9a1db9858a5
ms.service: virtual-network
ms.workload: virtual-network
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2016
ms.author: narayan;aglick
ms.openlocfilehash: 4e125406d2e798138c45e3fbbf61a610afab69fc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="virtual-network--business-continuity"></a>Rede Virtual – Continuidade de Negócios
## <a name="overview"></a>Visão geral
Uma Rede Virtual (VNet) é uma representação da sua rede na nuvem. Ela permite que você defina seu próprio espaço de endereço IP privado e segmente a rede em sub-redes. As VNets servem como um limite de confiança para hospedar seus recursos de computação, como as Máquinas Virtuais e os Serviços de Nuvem do Azure (funções web/de trabalho). Uma VNet permite a comunicação de IP privada direta entre os recursos hospedados nela. Uma Rede Virtual também pode ser vinculada a uma rede local por meio de uma das opções híbridas, como um Gateway de VPN ou ExpressRoute.

Uma VNet é criada dentro do escopo de uma região. Você pode criar VNets com o mesmo espaço de endereço em duas regiões diferentes (ou seja, Leste dos EUA e Oeste dos EUA, mas não é possível conectá-las entre si diretamente). 

## <a name="business-continuity"></a>Continuidade dos negócios
O seu aplicativo pode ser interrompido de várias maneiras diferentes. Uma determinada região poderia ser cortada completamente devido a um desastre natural ou a um desastre parcial devido a uma falha de vários dispositivos/serviços. O impacto sobre o serviço de Rede Virtual é diferente em cada uma dessas situações.

**P: O que fazer caso ocorra uma interrupção em uma região inteira? Por exemplo, se uma região fosse completamente cortada devido a um desastre natural? O que acontece com as Redes Virtuais hospedadas na região?**

R: A Rede Virtual e os recursos na região afetada permanecem inacessíveis durante o tempo de interrupção do serviço.

![Diagrama de Rede Virtual Simples](./media/virtual-network-disaster-recovery-guidance/vnet.png)

**P: O que posso fazer para recriar a mesma Rede Virtual em uma região diferente?**

R: A Rede Virtual (VNet) é um recurso relativamente leve. Você pode invocar as APIs do Azure para criar uma VNet com o mesmo espaço de endereço em uma região diferente. Para recriar o mesmo ambiente que estava presente na região afetada, você precisará fazer chamadas à API para reimplantar seus serviços de nuvem (funções web/de trabalho) e as máquinas virtuais que você tinha. Você também precisará criar um Gateway de VPN e se conectar à sua rede local, se você tiver conectividade local (como em uma implantação híbrida).

As instruções para criar uma VNet são encontradas [aqui](virtual-networks-create-vnet-arm-pportal.md). 

**P: Uma réplica de uma VNet em uma determinada região pode ser recriada em outra região antecipadamente?**

R: Sim, você pode criar duas VNets usando o mesmo espaço de endereço IP privado e os recursos em duas regiões diferentes antecipadamente. Se o cliente estivesse hospedando serviços voltados para a Internet na VNet, ele poderia ter configurado o Gerenciador de Tráfego para distribuir o tráfego geograficamente para a região que está ativa. No entanto, um cliente não pode conectar duas VNets com o mesmo espaço de endereço de rede local, uma vez que isso causaria problemas de roteamento. No momento de um desastre e da perda de uma VNet em uma região, o cliente pode se conectar a outra VNet na região disponível com espaço de endereço correspondente a rede local.

As instruções para criar uma VNet são encontradas [aqui](virtual-networks-create-vnet-arm-pportal.md).

