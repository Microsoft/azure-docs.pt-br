---
title: Conectar a Solução VMware no Azure ao seu ambiente local
description: Saiba como conectar a Solução VMware no Azure ao seu ambiente local.
ms.topic: tutorial
ms.date: 03/13/2021
ms.openlocfilehash: 0b26dc4756cb37544c2b2f8c5a75df0ac1a9d629
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103491785"
---
# <a name="connect-azure-vmware-solution-to-your-on-premises-environment"></a>Conectar a Solução VMware no Azure ao seu ambiente local

Neste artigo, você continuará usando as [informações coletadas durante o planejamento](production-ready-deployment-steps.md) para conectar a Solução VMware no Azure ao seu ambiente local.

Antes de começar, há dois pré-requisitos para conectar a Solução VMware no Azure ao seu ambiente local:

- Um circuito do ExpressRoute do seu ambiente local ao Azure.
- Um bloco de endereço de rede CIDR não sobreposto /29 para o emparelhamento do Alcance Global do ExpressRoute, que você definiu como parte da [fase de planejamento](production-ready-deployment-steps.md).

>[!NOTE]
> Você pode se conectar por meio de uma VPN, mas isso está fora do escopo deste documento de início rápido.

## <a name="establish-an-expressroute-global-reach-connection"></a>Estabelecer uma conexão do Alcance Global do ExpressRoute

Para estabelecer a conectividade local com sua nuvem privada da Solução VMware no Azure usando o Alcance Global do ExpressRoute, siga o tutorial [Parear ambientes locais com uma nuvem privada](tutorial-expressroute-global-reach-private-cloud.md).

Este tutorial resulta em uma conexão, conforme mostrado no diagrama.

:::image type="content" source="media/pre-deployment/azure-vmware-solution-on-premises-diagram.png" alt-text="Diagrama de conectividade de rede local do Alcance Global do ExpressRoute." lightbox="media/pre-deployment/azure-vmware-solution-on-premises-diagram.png" border="false":::

## <a name="verify-on-premises-network-connectivity"></a>Verificar a conectividade de rede local

Veja no **roteador de borda local** onde o ExpressRoute conecta os segmentos de rede do NSX-T e os segmentos de gerenciamento da Solução VMware no Azure.

>[!IMPORTANT]
>Todos têm um ambiente diferente e alguns precisarão permitir que essas rotas se propaguem de volta para a rede local.  

Alguns ambientes têm firewalls protegendo o circuito do ExpressRoute.  Se não houver firewall e nenhum atalho de rota ocorrer, você poderá executar ping no servidor vCenter da Solução VMware no Azure ou em uma [VM no segmento do NSX-T](deploy-azure-vmware-solution.md#add-a-vm-on-the-nsx-t-network-segment), partindo do ambiente local. Além disso, na VM no segmento do NSX-T, você pode acessar recursos do seu ambiente local.

## <a name="next-steps"></a>Próximas etapas

Continue na próxima seção para implantar e configurar o HCX da VMware

> [!div class="nextstepaction"]
> [Implantar e configurar o VMware HCX](tutorial-deploy-vmware-hcx.md)