---
title: Tutorial – Emparelhar ambientes locais com uma nuvem privada
description: Saiba como criar um emparelhamento de Alcance Global do ExpressRoute com uma nuvem privada em uma Solução VMware no Azure.
ms.topic: tutorial
ms.date: 03/17/2021
ms.openlocfilehash: 798b822989127ccbb00e971de2cc4147ac234259
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449555"
---
# <a name="tutorial-peer-on-premises-environments-to-a-private-cloud"></a>Tutorial: Emparelhar ambientes locais com uma nuvem privada

O Alcance Global do ExpressRoute conecta seu ambiente local à nuvem privada da Solução VMware no Azure. A conexão de Alcance Global do ExpressRoute é estabelecida entre um circuito do ExpressRoute de nuvem privada e uma conexão existente do ExpressRoute para os ambientes locais. 

O circuito do ExpressRoute que você usa ao [configurar a rede para sua nuvem privada do VMware no Azure](tutorial-configure-networking.md) exige que você crie e use chaves de autorização.  Você já usou uma chave de autorização do circuito do ExpressRoute e, neste tutorial, criará uma segunda chave de autorização para emparelhar com o circuito do ExpressRoute local.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma segunda chave de autorização para o _circuito 2_, o circuito do ExpressRoute na nuvem privada.
> * Usar o portal do Azure ou a CLI do Azure em um método do Cloud Shell na assinatura do _circuito 1_ para habilitar o emparelhamento do Alcance Global do ExpressRoute entre o local e a nuvem privada.


## <a name="before-you-begin"></a>Antes de começar

Antes de habilitar a conectividade entre dois circuitos do ExpressRoute usando o Alcance Global do ExpressRoute, examine a documentação sobre como [habilitar a conectividade em diferentes assinaturas do Azure](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions).  

## <a name="prerequisites"></a>Pré-requisitos

- Estabelecer a conectividade entre a nuvem privada da Solução VMware no Azure e o circuito do ExpressRoute emparelhado com o gateway do ExpressRoute em uma rede virtual do Azure (VNet) – o circuito 2 dos procedimentos de emparelhamento.
- Um circuito do ExpressRoute em funcionamento e separado, usado para conectar ambientes locais ao Azure – esse é o circuito 1 da perspectiva dos procedimentos de emparelhamento.
- [Bloco de endereço de rede](../expressroute/expressroute-routing.md#ip-addresses-used-for-peerings) A/29 não sobreposto para o emparelhamento de Alcance Global do ExpressRoute.
- Verifique se todos os gateways, incluindo o serviço do provedor de ExpressRoute, dão suporte ao ASN (número do sistema autônomo) de 4 bytes. A Solução VMware no Azure usa ASNs públicos de 4 bytes para anunciar rotas.

>[!IMPORTANT]
>No contexto desses pré-requisitos, o circuito do ExpressRoute local é o _circuito 1_ e o circuito do ExpressRoute na nuvem privada está em uma assinatura diferente rotulada como _circuito 2_.

## <a name="create-an-expressroute-authorization-key-in-the-private-cloud-expressroute-circuit"></a>Criar uma chave de autorização do ExpressRoute no circuito do ExpressRoute da nuvem privada

[!INCLUDE [request-authorization-key](includes/request-authorization-key.md)]
 
## <a name="peer-private-cloud-to-on-premises-with-authorization-key"></a>Emparelhar a nuvem privada com o local com a chave de autorização
Agora que você criou uma chave de autorização para o circuito do ExpressRoute da nuvem privada, poderá emparelhá-lo com o circuito do ExpressRoute local. O emparelhamento é feito da perspectiva do circuito do ExpressRoute local no **portal do Azure** ou na **CLI do Azure em um Cloud Shell**. Nos dois métodos, você usará a ID do recurso e a chave de autorização do circuito do ExpressRoute na nuvem privada para concluir o emparelhamento.

### <a name="portal"></a>[Portal](#tab/azure-portal)
 
1. Entre no [portal do Azure](https://portal.azure.com) usando a mesma assinatura do circuito do ExpressRoute local.

1. Na **Visão geral** da nuvem privada, em Gerenciar, selecione **Conectividade** > **Alcance Global do ExpressRoute** > **Adicionar**.

    :::image type="content" source="./media/expressroute-global-reach/expressroute-global-reach-tab.png" alt-text="Captura de tela mostrando a guia do Alcance Global do ExpressRoute na nuvem privada da Solução VMware no Azure.":::

1. Crie uma conexão de nuvem local. Execute uma das seguintes ações e selecione **Criar**:

   - Selecione o **circuito do ExpressRoute** na lista ou
   - Se já tiver a ID do circuito, cole-a no campo e forneça a chave de autorização que você acabou de criar.

   :::image type="content" source="./media/expressroute-global-reach/on-premises-cloud-connections.png" alt-text="Inserir a ID do ExpressRoute e a chave de autorização e selecionar Criar.":::   
   
   A nova conexão é mostrada na lista conexões de nuvem locais.

>[!TIP]
>Você pode excluir ou desconectar uma conexão da lista selecionando **Mais**.  
>
> :::image type="content" source="./media/expressroute-global-reach/on-premises-connection-disconnect.png" alt-text="Desconectar ou excluir uma conexão local":::

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Ampliamos os [comandos da CLI](../expressroute/expressroute-howto-set-global-reach-cli.md) com detalhes e exemplos específicos para ajudar você a configurar o emparelhamento do Alcance Global do ExpressRoute entre ambientes locais e uma nuvem privada da Solução VMware no Azure.

>[!TIP]
>Para ser breve quanto à saída do comando da CLI do Azure, essas instruções podem usar um [argumento `–query`](/cli/azure/query-azure-cli) para executar uma consulta JMESPath e mostrar apenas os resultados necessários.

1. Entre no [portal do Azure](https://portal.azure.com) usando a mesma assinatura do circuito do ExpressRoute local. 

1. Abra um Cloud Shell e deixe o shell como Bash.

   :::image type="content" source="media/expressroute-global-reach/azure-portal-cloud-shell.png" alt-text="Captura de tela mostrando o Cloud Shell no portal do Azure.":::

1. Crie o emparelhamento no circuito 1, passando a ID do recurso e a chave de autorização do circuito 2. 

   ```azurecli-interactive
   az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29> --authorization-key <authorizationKey>
   ```

   :::image type="content" source="media/expressroute-global-reach/azure-command-with-results.png" alt-text="Captura de tela mostrando o comando e os resultados de um emparelhamento bem-sucedido entre o circuito 1 e o circuito 2.":::

Você pode se conectar dos ambientes locais à sua nuvem privada por meio do emparelhamento de Alcance Global do ExpressRoute.

>[!TIP]
>Você pode excluir o emparelhamento seguindo as instruções em [Desabilitar a conectividade entre suas redes locais](../expressroute/expressroute-howto-set-global-reach-cli.md#disable-connectivity-between-your-on-premises-networks).


---

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você viu como habilitar o emparelhamento de Alcance Global do ExpressRoute do local para a nuvem privada. 

Siga para o próximo tutorial para aprender a implantar e configurar a solução VMware HCX para a nuvem privada da Solução VMware no Azure.

> [!div class="nextstepaction"]
> [Implantar e configurar o VMware HCX](tutorial-deploy-vmware-hcx.md)


<!-- LINKS - external-->

<!-- LINKS - internal -->
