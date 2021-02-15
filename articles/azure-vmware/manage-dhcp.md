---
title: Gerenciar o DHCP para a solução do Azure VMware
description: Saiba como criar e gerenciar o DHCP para sua nuvem privada da solução Azure VMware.
ms.topic: how-to
ms.custom: contperf-fy21q2
ms.date: 11/09/2020
ms.openlocfilehash: bcaba4274b0e6b423e9fa490c80fc57204d4e153
ms.sourcegitcommit: d488a97dc11038d9cef77a0235d034677212c8b3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/21/2020
ms.locfileid: "97708544"
---
# <a name="manage-dhcp-for-azure-vmware-solution"></a>Gerenciar o DHCP para a solução do Azure VMware

Aplicativos e cargas de trabalho em execução em um ambiente de nuvem privada exigem serviços DHCP para atribuições de endereço IP.  Este artigo mostra como criar e gerenciar o DHCP na solução VMware do Azure de duas maneiras:

- Caso esteja usando o NSX-T para hospedar o servidor DHCP, será necessário [criar um servidor DHCP](#create-a-dhcp-server) e executar a [retransmissão para esse servidor](#create-dhcp-relay-service). Ao criar o servidor DHCP, você também adicionará um segmento de rede e especificará o intervalo de endereços IP do DHCP.   

- Caso esteja usando um servidor DHCP externo de terceiros em sua rede, será necessário [criar um serviço de retransmissão DHCP](#create-dhcp-relay-service). Ao criar uma retransmissão para um servidor DHCP, seja usando o NSX-T ou um de terceiros para hospedar o servidor DHCP, você precisará especificar o intervalo de endereços IP do DHCP.

>[!IMPORTANT]
>O DHCP não funciona para VMs (máquinas virtuais) na rede de Stretch L2 do VMware HCX quando o servidor DHCP está no datacenter local.  O NSX, por padrão, bloqueia todas as solicitações DHCP de atravessar o Stretch L2. Para a solução, consulte o procedimento [enviar solicitações DHCP para o servidor DHCP local](#send-dhcp-requests-to-the-on-premises-dhcp-server) .


## <a name="create-a-dhcp-server"></a>Criar um servidor DHCP

Se você quiser usar o NSX-T para hospedar o servidor DHCP, você criará um servidor DHCP. Em seguida, você adicionará um segmento de rede e especificará o intervalo de endereços IP do DHCP.

1. No Gerenciador de NSX-T, selecione **rede**  >  **DHCP** e, em seguida, selecione **Adicionar servidor**.

1. Selecione **DHCP** para o **tipo de servidor**, forneça o nome do servidor e o endereço IP e, em seguida, selecione **salvar**.

   :::image type="content" source="./media/manage-dhcp/dhcp-server-settings.png" alt-text="Adicionar servidor DHCP" border="true":::

1. Selecione **gateways da camada 1**, selecione as reticências verticais no gateway de camada 1 e, em seguida, selecione **Editar**.

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway.png" alt-text="Selecione o gateway a ser usado" border="true":::

1. Selecione **nenhuma alocação de IP definida** para adicionar uma sub-rede.

   :::image type="content" source="./media/manage-dhcp/add-subnet.png" alt-text="Adicionar uma sub-rede" border="true":::

1. Para **tipo**, selecione **servidor DHCP local**. 
   
1. Para o **servidor DHCP**, selecione **DHCP padrão** e, em seguida, selecione **salvar**.

1. Selecione **salvar** novamente e selecione **fechar edição**.

### <a name="add-a-network-segment"></a>Adicionar um segmento de rede

[!INCLUDE [add-network-segment-steps](includes/add-network-segment-steps.md)]


## <a name="create-dhcp-relay-service"></a>Criar serviço de retransmissão DHCP

Se você quiser usar um servidor DHCP externo de terceiros, será necessário criar um serviço de retransmissão DHCP. Você também especificará o intervalo de endereços IP DHCP no Gerenciador de NSX-T. 

1. No Gerenciador de NSX-T, selecione **rede**  >  **DHCP** e, em seguida, selecione **Adicionar servidor**.

1. Selecione **retransmissão DHCP** para o **tipo de servidor**, forneça o nome do servidor e o endereço IP e, em seguida, selecione **salvar**.

   :::image type="content" source="./media/manage-dhcp/create-dhcp-relay.png" alt-text="Criar serviço de retransmissão DHCP" border="true":::

1. Selecione **gateways da camada 1**, selecione as reticências verticais no gateway de camada 1 e, em seguida, selecione **Editar**.

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway-relay.png" alt-text="editar gateway da camada 1" border="true":::

1. Selecione **nenhuma alocação de IP definida** para definir a alocação de endereço IP.

   :::image type="content" source="./media/manage-dhcp/edit-ip-address-allocation.png" alt-text="editar alocação de endereço IP" border="true":::

1. Para **tipo**, selecione **servidor DHCP**. 
   
1. Para o **servidor DHCP**, selecione **retransmissão DHCP** e, em seguida, selecione **salvar**.

1. Selecione **salvar** novamente e selecione **fechar edição**.


## <a name="specify-the-dhcp-ip-address-range"></a>Especificar o intervalo de endereços IP do DHCP

1. No NSX-T Manager, selecione **Rede** > **Segmentos**. 
   
1. Selecione as reticências verticais no nome do segmento e selecione **Editar**.
   
1. Selecione **definir sub-redes** para especificar o endereço IP DHCP para a sub-rede. 
   
   :::image type="content" source="./media/manage-dhcp/network-segments.png" alt-text="segmentos de rede" border="true":::
      
1. Modifique o endereço IP do gateway, se necessário, e insira o IP do intervalo DHCP. 
      
   :::image type="content" source="./media/manage-dhcp/edit-subnet.png" alt-text="editar sub-redes" border="true":::
      
1. Selecione **aplicar** e, em seguida, **salvar**. O segmento recebe um pool de servidores DHCP.
      
   :::image type="content" source="./media/manage-dhcp/assigned-to-segment.png" alt-text="Pool de servidores DHCP atribuído ao segmento" border="true":::


## <a name="send-dhcp-requests-to-the-on-premises-dhcp-server"></a>Enviar solicitações DHCP para o servidor DHCP local

Se você quiser enviar solicitações DHCP de suas VMs de solução do Azure VMware no segmento estendido do L2 para o servidor DHCP local, você criará um perfil de segmento de segurança. 

1. Entre no vCenter local e, na **Página Inicial**, selecione **HCX**.

1. Selecione a **extensão de rede** em **Serviços**.

1. Selecione a extensão de rede que você deseja dar suporte a solicitações DHCP da solução Azure VMware para o local. 

1. Anote o nome da rede de destino.  

   :::image type="content" source="media/manage-dhcp/hcx-find-destination-network.png" alt-text="Captura de tela de uma extensão de rede no cliente VMware vSphere" lightbox="media/manage-dhcp/hcx-find-destination-network.png":::

1. Na solução do Azure VMware NSX-T Manager, selecione segmentos de **rede**  >    >  **perfis de segmento**. 

1. Selecione **Adicionar perfil de segmento** e, em seguida, **segurança de segmento**.

   :::image type="content" source="media/manage-dhcp/add-segment-profile.png" alt-text="Captura de tela de como adicionar um perfil de segmento no NSX-T" lightbox="media/manage-dhcp/add-segment-profile.png":::

1. Forneça um nome e uma marca e, em seguida, defina a alternância de **filtro BPDU** como ativado e todas as alternâncias do DHCP como desativado.

   :::image type="content" source="media/manage-dhcp/add-segment-profile-bpdu-filter-dhcp-options.png" alt-text="Captura de tela mostrando o filtro BPDU alternado e as desativações do DHCP" lightbox="media/manage-dhcp/add-segment-profile-bpdu-filter-dhcp-options.png":::

1. Remova todos os endereços MAC, se houver, na **lista de permissões do filtro BPDU**.  Em seguida, selecione **Salvar**.

   :::image type="content" source="media/manage-dhcp/add-segment-profile-bpdu-filter-allow-list.png" alt-text="Captura de tela mostrando endereços MAC na lista de permissões do filtro BPDU":::

1. Em **rede**  >  **segmentos segmentos**  >  , na área Pesquisar, insira o nome da rede de definição.

   :::image type="content" source="media/manage-dhcp/networking-segments-search.png" alt-text="Captura de tela do campo de filtro segmentos de > de rede":::

1. Selecione as reticências verticais no nome do segmento e selecione **Editar**.

   :::image type="content" source="media/manage-dhcp/edit-network-segment.png" alt-text="Captura de tela do botão Editar do segmento" lightbox="media/manage-dhcp/edit-network-segment.png":::

1. Altere a **segurança do segmento** para o perfil de segmento que você criou anteriormente.

   :::image type="content" source="media/manage-dhcp/edit-segment-security.png" alt-text="Captura de tela do campo de segurança do segmento" lightbox="media/manage-dhcp/edit-segment-security.png":::

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [manutenção do host e gerenciamento do ciclo de vida](concepts-private-clouds-clusters.md#host-maintenance-and-lifecycle-management).