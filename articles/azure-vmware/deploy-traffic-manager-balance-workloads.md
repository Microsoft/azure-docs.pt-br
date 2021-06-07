---
title: Implantar o Gerenciador de tráfego para balancear as cargas de trabalho da solução Azure VMware
description: Saiba como integrar o Gerenciador de tráfego à solução do Azure VMware para balancear cargas de trabalho de aplicativos em vários pontos de extremidade em regiões diferentes.
ms.topic: how-to
ms.date: 02/08/2021
ms.openlocfilehash: 46570c5a61fc0a641d83126fd0f8ef35b3dc42cc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99988601"
---
# <a name="deploy-traffic-manager-to-balance-azure-vmware-solution-workloads"></a>Implantar o Gerenciador de tráfego para balancear as cargas de trabalho da solução Azure VMware

Este artigo descreve as etapas de como integrar o [Gerenciador de tráfego do Azure](../traffic-manager/traffic-manager-overview.md) com a solução VMware do Azure. A integração balanceia cargas de trabalho de aplicativo em vários pontos de extremidade. Este artigo também percorre as etapas de como configurar o Gerenciador de tráfego para direcionar o tráfego entre três [aplicativo Azure gateway](../application-gateway/overview.md) abrangendo várias regiões de solução do Azure VMware. 

Os gateways têm VMs (máquinas virtuais) da solução do Azure VMware configuradas como membros do pool de back-end para balancear a carga das solicitações de entrada da camada 7. Para obter mais informações, consulte [usar aplicativo Azure gateway para proteger seus aplicativos Web na solução VMware do Azure](protect-azure-vmware-solution-with-application-gateway.md)

O diagrama mostra como o Gerenciador de tráfego fornece balanceamento de carga para os aplicativos no nível DNS entre pontos de extremidade regionais. Os gateways têm membros do pool de back-end configurados como servidores IIS e referenciados como pontos de extremidade externos da solução Azure VMware. A conexão pela rede virtual entre as duas regiões de nuvem privada usa um gateway de ExpressRoute.   

:::image type="content" source="media/traffic-manager/traffic-manager-topology.png" alt-text="Diagrama de arquitetura da integração do Gerenciador de tráfego com a solução VMware do Azure" lightbox="media/traffic-manager/traffic-manager-topology.png" border="false":::

Antes de começar, primeiro examine os [pré-requisitos](#prerequisites) e, em seguida, percorreremos os procedimentos para:

> [!div class="checklist"]
> * Verificar a configuração dos gateways de aplicativo e o segmento NSX-T
> * Criar seu perfil do Gerenciador de tráfego
> * Adicionar pontos de extremidade externos ao seu perfil do Gerenciador de tráfego

## <a name="prerequisites"></a>Pré-requisitos

- Três VMs configuradas como servidores IIS da Microsoft em execução em diferentes regiões de solução do Azure VMware: 
   - Oeste dos EUA
   - Europa Ocidental
   - Leste dos EUA (local) 

- Um gateway de aplicativo com pontos de extremidade externos nas regiões de solução do Azure VMware mencionadas acima.

- Hospede com conectividade com a Internet para verificação. 

- Um [segmento de rede do NSX-T criado na solução VMware do Azure](tutorial-nsx-t-network-segment.md).

## <a name="verify-your-application-gateways-configuration"></a>Verificar a configuração de gateways de aplicativo

As etapas a seguir verificam a configuração de seus gateways de aplicativo.

1. Na portal do Azure, selecione **gateways de aplicativo** para exibir uma lista de seus gateways de aplicativo atuais:

   - AVS-GW-WUS
   - AVS-GW-EUS (local)
   - AVS-GW-WEU

   :::image type="content" source="media/traffic-manager/app-gateways-list-1.png" alt-text="Captura de tela da página do gateway de aplicativo mostrando a lista de gateways de aplicativo configurados." lightbox="media/traffic-manager/app-gateways-list-1.png":::

1. Selecione um dos gateways de aplicativo implantados anteriormente. 

   Uma janela é aberta mostrando várias informações no gateway de aplicativo. 

   :::image type="content" source="media/traffic-manager/backend-pool-config.png" alt-text="Captura de tela da página do gateway de aplicativo mostrando detalhes do gateway de aplicativo selecionado." lightbox="media/traffic-manager/backend-pool-config.png":::

1. Selecione **pools de back-end** para verificar a configuração de um dos pools de back-end. Você verá um membro do pool de back-end da VM configurado como um servidor Web com um endereço IP de 172.29.1.10.
 
   :::image type="content" source="media/traffic-manager/backend-pool-ip-address.png" alt-text="Captura de tela da página Editar pool de back-end com endereço IP de destino realçado.":::

1. Verifique a configuração dos outros gateways de aplicativo e membros do pool de back-end. 

## <a name="verify-the-nsx-t-segment-configuration"></a>Verificar a configuração do segmento do NSX-T

As etapas a seguir verificam a configuração do segmento NSX-T no ambiente da solução VMware do Azure.

1. Selecione **segmentos** para exibir seus segmentos configurados.  Você vê o contoso-segment1 conectado ao gateway contoso-T01, um roteador flexível de camada 1.

   :::image type="content" source="media/traffic-manager/nsx-t-segment-avs.png" alt-text="Captura de tela mostrando perfis de segmento no Gerenciador de NSX-T." lightbox="media/traffic-manager/nsx-t-segment-avs.png":::    

1. Selecione **gateways de camada 1** para ver uma lista de gateways de camada 1 com o número de segmentos vinculados. 

   :::image type="content" source="media/traffic-manager/nsx-t-segment-linked-2.png" alt-text="Captura de tela mostrando o endereço do gateway do segmento selecionado.":::    

1. Selecione o segmento vinculado ao contoso-T01. Uma janela é aberta mostrando a interface lógica configurada no roteador camada-01. Ele serve como um gateway para a VM do membro do pool de back-end conectada ao segmento.

1. No cliente vSphere, selecione a VM para exibir seus detalhes. 

   >[!NOTE]
   >Seu endereço IP corresponde ao membro do pool de back-end da VM configurado como um servidor Web da seção anterior: 172.29.1.10.

   :::image type="content" source="media/traffic-manager/nsx-t-vm-details.png" alt-text="Captura de tela mostrando detalhes da VM no cliente vSphere." lightbox="media/traffic-manager/nsx-t-vm-details.png":::    

4. Selecione a VM e, em seguida, selecione **ações > editar configurações** para verificar a conexão com o segmento NSX-T.

## <a name="create-your-traffic-manager-profile"></a>Criar seu perfil do Gerenciador de tráfego

1. Entre no [portal do Azure](https://rc.portal.azure.com/#home). Em **Serviços do Azure > rede**, selecione **perfis do Gerenciador de tráfego**.

2. Selecione **+ Adicionar** para criar um novo perfil do Gerenciador de tráfego.
 
3. Forneça as seguintes informações e, em seguida, selecione **criar**:

   - Nome do perfil
   - Método de roteamento (uso [ponderado](../traffic-manager/traffic-manager-routing-methods.md)
   - Subscription
   - Resource group

## <a name="add-external-endpoints-into-the-traffic-manager-profile"></a>Adicionar pontos de extremidade externos ao perfil do Gerenciador de tráfego

1. Selecione o perfil do Gerenciador de tráfego no painel de resultados da pesquisa, selecione **pontos de extremidade** e **+ Adicionar**.

1. Para cada um dos pontos de extremidade externos nas regiões diferentes, insira os detalhes necessários e, em seguida, selecione **Adicionar**: 
   - Type
   - Nome
   - FQDN (nome de domínio totalmente qualificado) ou IP
   - Peso (atribua um peso de 1 a cada ponto de extremidade). 

   Depois de criadas, todas as três mostradas no perfil do Gerenciador de tráfego. O status do monitor de todos os três deve estar **online**.

3. Selecione **visão geral** e copie a URL em **nome DNS**.

   :::image type="content" source="media/traffic-manager/traffic-manager-endpoints.png" alt-text="Captura de tela mostrando uma visão geral do ponto de extremidade do Traffic Manager com nome DNS realçado" lightbox="media/traffic-manager/traffic-manager-endpoints.png"::: 

4. Cole a URL do nome DNS em um navegador. A captura de tela mostra o tráfego direcionado para a região de Europa Ocidental.

   :::image type="content" source="media/traffic-manager/traffic-to-west-europe.png" alt-text="Captura de tela da janela do navegador mostrando o tráfego roteado para Europa Ocidental."::: 

5. Atualize seu navegador. A captura de tela mostra o tráfego direcionado para outro conjunto de membros do pool de back-end na região oeste dos EUA.

   :::image type="content" source="media/traffic-manager/traffic-to-west-us.png" alt-text="Captura de tela da janela do navegador mostrando o tráfego roteado para o oeste dos EUA."::: 

6. Atualize o navegador novamente. A captura de tela mostra o tráfego direcionado para o conjunto final de membros do pool de back-end no local.

   :::image type="content" source="media/traffic-manager/traffic-to-on-premises.png" alt-text="Captura de tela da janela do navegador mostrando o tráfego roteado para o local.":::

## <a name="next-steps"></a>Próximas etapas

Agora que você já abordou a integração do Gerenciador de tráfego do Azure com a solução VMware do Azure, talvez queira saber mais sobre:

- [Usando aplicativo Azure gateway na solução VMware do Azure](protect-azure-vmware-solution-with-application-gateway.md).
- [Métodos de roteamento do Traffic Manager](../traffic-manager/traffic-manager-routing-methods.md).
- [Combinando serviços de balanceamento de carga no Azure](../traffic-manager/traffic-manager-load-balancing-azure.md).
- [Medindo o desempenho do Gerenciador de tráfego](../traffic-manager/traffic-manager-performance-considerations.md).
