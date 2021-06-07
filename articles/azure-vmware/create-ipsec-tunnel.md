---
title: Criar um túnel IPSec na solução VMware do Azure
description: Saiba como estabelecer um túnel de site a site de VPN (IPsec IKEv1 e IKEv2) para soluções VMware do Azure.
ms.topic: how-to
ms.date: 03/23/2021
ms.openlocfilehash: 280ffdd3fec77208d5b49c8e624b7b22bca1daaf
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105026952"
---
# <a name="create-an-ipsec-tunnel-into-azure-vmware-solution"></a>Criar um túnel IPSec na solução VMware do Azure

Neste artigo, vamos percorrer as etapas para estabelecer um encerramento de túnel site a site de VPN (IKEv1 e IKEv2), no Microsoft Azure Hub de WAN virtual. O Hub contém o gateway de ExpressRoute da solução VMware do Azure e o gateway de VPN site a site. Ele conecta um dispositivo VPN local a um ponto de extremidade de solução VMware do Azure.

:::image type="content" source="media/create-ipsec-tunnel/vpn-s2s-tunnel-architecture.png" alt-text="Diagrama mostrando a arquitetura de túnel VPN site a site." border="false":::

Neste "como", você vai:
- Crie um hub de WAN virtual do Azure e um gateway de VPN com um endereço IP público anexado a ele. 
- Crie um gateway de ExpressRoute do Azure e estabeleça um ponto de extremidade de solução VMware do Azure. 
- Habilite uma configuração local de VPN baseada em políticas. 

## <a name="prerequisites"></a>Pré-requisitos
Você deve ter um endereço IP voltado para o público terminando em um dispositivo VPN local.

## <a name="step-1-create-an-azure-virtual-wan"></a>Etapa 1. Criar uma WAN virtual do Azure

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="step-2-create-a-virtual-wan-hub-and-gateway"></a>Etapa 2. Criar um gateway e um hub de WAN virtual

>[!TIP]
>Você também pode [criar um gateway em um Hub existente](../virtual-wan/virtual-wan-expressroute-portal.md#existinghub).

1. Selecione a WAN virtual que você criou na etapa anterior.

1. Selecione **criar Hub virtual**, insira os campos obrigatórios e, em seguida, selecione **Avançar: site a site**. 

   Insira a sub-rede usando um `/24` (mínimo).

   :::image type="content" source="media/create-ipsec-tunnel/create-virtual-hub.png" alt-text="Captura de tela mostrando a página Criar Hub virtual.":::

4. Selecione a guia **site a site** , defina o Gateway site a site definindo a taxa de transferência agregada na lista suspensa **unidades de escala do gateway** . 

   >[!TIP]
   >As unidades de escala estão em pares para redundância, cada uma com suporte a 500 Mbps (unidade de escala = 500 Mbps). 
  
   :::image type="content" source="../../includes/media/virtual-wan-tutorial-hub-include/site-to-site.png" alt-text="Captura de tela mostrando os detalhes de site a site.":::

5. Selecione a guia **expressroute** , crie um gateway de expressroute. 

   :::image type="content" source="../../includes/media/virtual-wan-tutorial-er-hub-include/hub2.png" alt-text="Captura de tela das configurações do ExpressRoute.":::

   >[!TIP]
   >Um valor de unidade de escala é 2 Gbps. 

    Leva aproximadamente 30 minutos para criar cada Hub. 

## <a name="step-3-create-a-site-to-site-vpn"></a>Etapa 3. Criar uma VPN site a site

1. Na portal do Azure, selecione a WAN virtual que você criou anteriormente.

2. Na **visão geral** do Hub virtual, selecione **conectividade**  >  **VPN (site a site)**  >  **criar novo site VPN**.

   :::image type="content" source="media/create-ipsec-tunnel/create-vpn-site-basics.png" alt-text="Captura de tela da página de visão geral do Hub virtual, com VPN (site a site) e criar novo site de VPN selecionado.":::  
 
3. Na guia **noções básicas** , insira os campos obrigatórios. 

   :::image type="content" source="media/create-ipsec-tunnel/create-vpn-site-basics2.png" alt-text="Captura de tela da guia noções básicas para o novo site de VPN.":::  

   1. Defina a **Border Gateway Protocol** a ser **habilitada**.  Quando habilitada, ela garante que a solução do Azure VMware e os servidores locais anunciem suas rotas no túnel. Se desabilitado, as sub-redes que precisam ser anunciadas devem ser mantidas manualmente. Se as sub-redes forem perdidas, o HCX não conseguirá formar a malha de serviço. Para obter mais informações, consulte  [sobre o BGP com o gateway de VPN do Azure](../vpn-gateway/vpn-gateway-bgp-overview.md).
   
   1. Para o **espaço de endereço privado**, insira o bloco CIDR local. Ele é usado para rotear todo o tráfego associado para o local pelo túnel. O bloco CIDR só será necessário se você não habilitar o BGP.

1. Selecione **Avançar: links** e preencha os campos obrigatórios. A especificação de nomes de link e provedor permite que você Diferencie entre qualquer número de gateways que possam eventualmente ser criados como parte do Hub. O BGP e o número do sistema autônomo (ASN) devem ser exclusivos dentro de sua organização.

   :::image type="content" source="media/create-ipsec-tunnel/create-vpn-site-links.png" alt-text="Captura de tela que mostra detalhes do link.":::

1. Selecione **Examinar + criar**. 

1. Navegue até o hub virtual desejado e desmarque a **Associação de hub** para conectar o seu site de VPN ao hub.
 
   :::image type="content" source="../../includes/media/virtual-wan-tutorial-site-include/connect.png" alt-text="Captura de tela que mostra o painel sites conectados para o HUB virtual pronto para chave pré-compartilhada e configurações associadas.":::   

## <a name="step-4-optional-create-policy-based-vpn-site-to-site-tunnels"></a>Etapa 4. Adicional Criar túneis VPN site a site baseados em políticas

>[!IMPORTANT]
>Essa é uma etapa opcional e aplica-se somente a VPNs baseadas em políticas. 

As configurações de VPN baseadas em políticas exigem que as redes locais e da solução do Azure VMware sejam especificadas, incluindo os intervalos de Hub.  Esses intervalos de Hub especificam o domínio de criptografia do ponto de extremidade local do túnel VPN baseado em políticas.  O lado da solução do Azure VMware requer que o indicador de seletor de tráfego baseado em políticas esteja habilitado. 

1. No portal do Azure, acesse o site do Hub WAN virtual. Em **conectividade**, selecione **VPN (site a site)**.

2. Selecione o nome do site de VPN, as reticências (...) na extrema direita e, em seguida, **edite a conexão VPN para esse Hub**.
 
   :::image type="content" source="media/create-ipsec-tunnel/edit-vpn-section-to-this-hub.png" alt-text="Captura de tela da página no Azure para o site do Hub WAN virtual mostrando uma elipse selecionada para acessar editar conexão VPN com esse Hub." lightbox="media/create-ipsec-tunnel/edit-vpn-section-to-this-hub.png":::

3. Edite a conexão entre o site VPN e o Hub e, em seguida, selecione **salvar**.
   - IPSec (Internet Protocol Security), selecione **personalizado**.
   - Use o seletor de tráfego baseado em políticas, selecione **habilitar**
   - Especifique os detalhes da **fase 1 do Ike** e do **Ike fase 2 (IPSec)**. 
 
   :::image type="content" source="media/create-ipsec-tunnel/edit-vpn-connection.png" alt-text="Captura de tela da página Editar conexão VPN."::: 
 
   Seus seletores de tráfego ou sub-redes que fazem parte do domínio de criptografia baseada em políticas devem ser:
    
   - Hub WAN virtual `/24`
   - Nuvem privada da solução Azure VMware `/22`
   - Rede virtual do Azure conectada (se presente)

## <a name="step-5-connect-your-vpn-site-to-the-hub"></a>Etapa 5. Conectar seu site VPN ao Hub

1. Selecione o nome do site VPN e, em seguida, selecione **conectar sites VPN**. 

1. No campo **chave pré-compartilhada** , insira a chave definida anteriormente para o ponto de extremidade local. 

   >[!TIP]
   >Se você não tiver uma chave definida anteriormente, poderá deixar esse campo em branco. Uma chave é gerada automaticamente para você. 

   :::image type="content" source="../../includes/media/virtual-wan-tutorial-connect-vpn-site-include/connect.png" alt-text="Captura de tela que mostra o painel sites conectados para que o HUB virtual esteja pronto para uma chave pré-compartilhada e configurações associadas. "::: 

1. Se você estiver implantando um firewall no Hub e for o próximo salto, defina a opção **propagar rota padrão** como **habilitar**. 

   Quando habilitado, o Hub WAN virtual se propaga para uma conexão somente se o Hub já tiver aprendido a rota padrão ao implantar um firewall no Hub ou se outro site conectado tiver o túnel forçado habilitado. A rota padrão não se origina no hub da WAN virtual.  

1. Selecione **Conectar**. Depois de alguns minutos, o site mostra o status de conexão e conectividade.

   :::image type="content" source="../../includes/media/virtual-wan-tutorial-connect-vpn-site-include/status.png" alt-text="Captura de tela que mostra um status de conectividade e conexão site a site." lightbox="../../includes/media/virtual-wan-tutorial-connect-vpn-site-include/status.png":::

1. [Baixe o arquivo de configuração de VPN](../virtual-wan/virtual-wan-site-to-site-portal.md#device) para o ponto de extremidade local.  

3. Corrija a solução do Azure VMware ExpressRoute no Hub de WAN virtual. 

   >[!IMPORTANT]
   >Você deve primeiro ter uma nuvem privada criada antes de poder corrigir a plataforma. 

   [!INCLUDE [request-authorization-key](includes/request-authorization-key.md)]

4. Vincule a solução VMware do Azure e o gateway de VPN juntos no Hub WAN virtual. Você usará a chave de autorização e a ID do ExpressRoute (URI do circuito par) da etapa anterior.

   1. Selecione o gateway do ExpressRoute e selecione **resgatar a chave de autorização**.

      :::image type="content" source="media/create-ipsec-tunnel/redeem-authorization-key.png" alt-text="Captura de tela da página do ExpressRoute para a nuvem privada, com a chave de autorização resgatar selecionada.":::

   1. Cole a chave de autorização no campo **chave de autorização** .
   1. Cole a ID do ExpressRoute no campo **URI do circuito par** . 
   1. Selecione **associar automaticamente este circuito do ExpressRoute ao hub caixa de** seleção. 
   1. Selecione **Adicionar** para estabelecer o link. 

5. Teste sua conexão [criando um segmento do NSX-T](./tutorial-nsx-t-network-segment.md) e provisionando uma VM na rede. Execute ping nos pontos de extremidade da solução local e do Azure VMware.
