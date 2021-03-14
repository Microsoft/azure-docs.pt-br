---
title: Tutorial – Emparelhar ambientes locais com uma nuvem privada
description: Saiba como criar um emparelhamento de Alcance Global do ExpressRoute com uma nuvem privada em uma Solução VMware no Azure.
ms.topic: tutorial
ms.date: 01/27/2021
ms.openlocfilehash: 04be94849f1cd78ff915ca271845b283c8cfc64c
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100558828"
---
# <a name="tutorial-peer-on-premises-environments-to-a-private-cloud"></a>Tutorial: Emparelhar ambientes locais com uma nuvem privada

O Alcance Global do ExpressRoute conecta seu ambiente local à nuvem privada da Solução VMware no Azure. A conexão de Alcance Global do ExpressRoute é estabelecida entre um circuito do ExpressRoute de nuvem privada e uma conexão existente do ExpressRoute para os ambientes locais. 

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Usar o portal do Azure para habilitar o emparelhamento de Alcance Global do ExpressRoute do local para a nuvem privada.


## <a name="before-you-begin"></a>Antes de começar

Antes de habilitar a conectividade entre dois circuitos do ExpressRoute usando o Alcance Global do ExpressRoute, examine a documentação sobre como [habilitar a conectividade em diferentes assinaturas do Azure](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions).  


## <a name="prerequisites"></a>Pré-requisitos

- Um circuito do ExpressRoute em funcionamento e separado, usado para conectar ambientes locais ao Azure.
- Verifique se todos os gateways, incluindo o serviço do provedor de ExpressRoute, dão suporte ao ASN (número do sistema autônomo) de 4 bytes. A Solução VMware no Azure usa ASNs públicos de 4 bytes para anunciar rotas.


## <a name="create-an-expressroute-authorization-key-in-the-on-premises-expressroute-circuit"></a>Criar uma chave de autorização do ExpressRoute no circuito local do ExpressRoute

1. Na folha **Circuitos do ExpressRoute**, em Configurações, selecione **Autorizações**.

2. Insira o nome da chave de autorização e selecione **Salvar**.

    :::image type="content" source="media/expressroute-global-reach/start-request-auth-key-on-premises-expressroute.png" alt-text="Selecionar Autorizações e inserir o nome da chave de autorização.":::
  
     Depois de criada, a chave será exibida na lista de chaves de autorização do circuito.
 
 4. Anote a chave de autorização e a ID do ExpressRoute. Você os usará na próxima etapa para concluir o emparelhamento.
 
 
 ## <a name="peer-private-cloud-to-on-premises"></a>Emparelhar uma nuvem privada com o ambiente local

1. Na **Visão geral** da nuvem privada, em "Gerenciar", selecione **Conectividade > Alcance Global do ExpressRoute > Adicionar**.

    :::image type="content" source="./media/expressroute-global-reach/expressroute-global-reach-tab.png" alt-text="No menu, selecione &quot;Conectividade&quot;, a guia &quot;Alcance Global do ExpressRoute&quot; e &quot;Adicionar&quot;.":::

2. Insira a ID do ExpressRoute e a chave de autorização criada na seção anterior.

    :::image type="content" source="./media/expressroute-global-reach/on-premises-cloud-connections.png" alt-text="Inserir a ID do ExpressRoute e a chave de autorização e selecionar Criar.":::

3. Selecione **Criar**. A nova conexão é mostrada na lista conexões de nuvem locais.  

>[!TIP]
>Você pode excluir ou desconectar uma conexão da lista selecionando **Mais**.  
>
> :::image type="content" source="./media/expressroute-global-reach/on-premises-connection-disconnect.png" alt-text="Desconectar ou excluir uma conexão local":::


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você viu como habilitar o emparelhamento de Alcance Global do ExpressRoute do local para a nuvem privada. 

Siga para o próximo tutorial para aprender a implantar e configurar a solução VMware HCX para a nuvem privada da Solução VMware no Azure.

> [!div class="nextstepaction"]
> [Implantar e configurar o VMware HCX](tutorial-deploy-vmware-hcx.md)


<!-- LINKS - external-->

<!-- LINKS - internal -->
