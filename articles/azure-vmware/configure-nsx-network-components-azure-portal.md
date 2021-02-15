---
title: Configurar os componentes de rede do NSX na solução VMware do Azure
description: Saiba como usar o console de solução VMware do Azure para configurar os segmentos de rede do NSX-T.
ms.topic: how-to
ms.date: 02/16/2021
ms.openlocfilehash: dbed29fb1063b78386f9ec1e2ee00d9c685a944e
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100416981"
---
# <a name="configure-nsx-network-components-in-azure-vmware-solution"></a>Configurar os componentes de rede do NSX na solução VMware do Azure

Uma nuvem privada da solução Azure VMware vem com o NSX-T como uma rede definida pelo software (SDDC) por padrão. Ele vem pré-configurado com um gateway da camada-0 do NSX-T no modo ativo/ativo e um gateway padrão do NSX-T camada-1 no modo ativo/em espera.  Esses gateways permitem que você conecte os segmentos (comutadores lógicos) e forneça East-West e North-South conectividade. 

Depois que a nuvem privada da solução Azure VMware for implantada, você poderá configurar os objetos NSX-T necessários no console de solução VMware do Azure em **rede de carga de trabalho**.  O console apresenta a visão simplificada das operações do NSX-T que um administrador do VMware precisa diariamente e direcionado a usuários que não estão familiarizados com o NSX-T.  

Você terá quatro opções para configurar os componentes do NSX-T no console de solução do Azure VMware:
- **Segmentos** – crie segmentos que são exibidos no Gerenciador de NSX-T e no vCenter.
- **DHCP** -crie um servidor DHCP ou retransmissão DHCP se você planeja usar o DHCP.
- **Espelhamento de porta** – crie o espelhamento de porta para ajudar a solucionar problemas de rede.
- **DNS** – crie um encaminhador DNS para enviar solicitações DNS a um servidor DNS designado para resolução.  

>[!NOTE]
>Você ainda terá acesso ao console do NSX-T Manager, no qual poderá usar as configurações avançadas mencionadas e outros recursos do NSX-T. 
 
:::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking.png" alt-text="Captura de tela mostrando quatro opções no console de solução do Azure VMware para configurar o NSX-T.":::

## <a name="prerequisites"></a>Pré-requisitos
As máquinas virtuais (VMs) criadas ou migradas para a nuvem privada da solução Azure VMware devem ser anexadas a um segmento. 

## <a name="create-an-nsx-t-segment-in-the-azure-portal"></a>Criar um segmento do NSX-T no portal do Azure
Você pode criar e configurar um segmento do NSX-T do console de solução do Azure VMware no portal do Azure.  Esses segmentos são conectados ao gateway de camada 1 padrão e as cargas de trabalho nesses segmentos recebem East-West e North-South conectividade. Depois de criar o segmento, ele é exibido no Gerenciador de NSX-T e no vCenter.

>[!NOTE]
>Se você planeja usar o DHCP, precisará [configurar um servidor DHCP ou uma retransmissão DHCP](#create-a-dhcp-server-or-dhcp-relay-in-the-azure-portal) antes de criar e configurar um segmento do NSX-T.

1. Em sua nuvem privada da solução Azure VMware, em **rede de carga de trabalho**, selecione **segmentos**  >  **Adicionar**.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/add-new-nsxt-segment.png" alt-text="Captura de tela mostrando como adicionar um novo segmento.":::

1. Forneça os detalhes para o novo segmento lógico.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/create-new-segment-details.png" alt-text="Captura de tela mostrando os detalhes do novo segmento.":::
   
   - **Nome do segmento** -nome do comutador lógico que é visível no vCenter.
   - **Gateway de sub-rede** -endereço IP do gateway para a sub-rede do comutador lógico com uma máscara de sub-rede. As VMs são anexadas a um comutador lógico e todas as VMs que se conectam a esse comutador pertencem à mesma sub-rede.  Além disso, todas as VMs anexadas a esse segmento lógico devem transportar um endereço IP do mesmo segmento.
   - **DHCP** (opcional)-intervalos DHCP para um segmento lógico. Um [servidor DHCP ou uma retransmissão DHCP](#create-a-dhcp-server-or-dhcp-relay-in-the-azure-portal) deve ser configurada para consumir DHCP em segmentos.  
   - **Gateway conectado**  -  *Selecionado por padrão e somente leitura.*  Gateway de camada 1 e tipo de informações de segmento. 
      - **T1** -nome do gateway de camada 1 no Gerenciador de NSX-T. Uma nuvem privada da solução Azure VMware vem com um gateway da camada-0 do NSX-T no modo ativo/ativo e um gateway padrão do NSX-T camada-1 no modo ativo/em espera.  Segmentos criados por meio do console de solução do Azure VMware só se conectam ao gateway de camada 1 padrão e as cargas de trabalho desses segmentos recebem East-West e North-South conectividade. Você só pode criar mais gateways de camada 1 por meio do Gerenciador de NSX-T. Os gateways de camada 1 criados no console do Gerenciador de NSX-T não estão visíveis no console de solução VMware do Azure. 
      - Segmento de sobreposição de **tipo** suportado pela solução Azure VMware.

1. Selecione **OK** para criar o segmento e anexá-lo ao gateway de camada 1. 

   O segmento agora está visível no console de solução VMware do Azure, no NSX-T Manager e no vCenter.

## <a name="create-a-dhcp-server-or-dhcp-relay-in-the-azure-portal"></a>Criar um servidor DHCP ou uma retransmissão DHCP no portal do Azure
Você pode criar um servidor DHCP ou uma retransmissão diretamente do console de solução do Azure VMware no portal do Azure. O servidor DHCP ou a retransmissão se conecta ao gateway de camada 1, que é criado quando você implanta a solução Azure VMware. Todos os segmentos em que você deu os intervalos DHCP serão parte desse DHCP.  Depois de criar um servidor DHCP ou uma retransmissão DHCP, você deve definir uma sub-rede ou intervalo no nível de segmento para consumi-lo.

1. Em sua nuvem privada da solução Azure VMware, em **rede de carga de trabalho**, selecione **DHCP**  >  **Adicionar**.

2. Selecione o **servidor DHCP** ou a **retransmissão DHCP** e forneça um nome para o servidor ou retransmissão e três endereços IP. 

   >[!NOTE]
   >Para a retransmissão DHCP, apenas um endereço IP é necessário para uma configuração bem-sucedida.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/add-dhcp-server-relay.png" alt-text="Captura de tela mostrando como adicionar um servidor DHCP ou uma retransmissão DHCP nas soluções VMware do Azure.":::

4. Conclua a configuração do DHCP [fornecendo intervalos DHCP nos segmentos lógicos](#create-an-nsx-t-segment-in-the-azure-portal) e selecione **OK**.
 
## <a name="configure-port-mirroring-in-the-azure-portal"></a>Configurar o espelhamento de porta no portal do Azure
Você pode configurar o espelhamento de porta para monitorar o tráfego de rede que envolve o encaminhamento de uma cópia de cada pacote de uma porta de comutador de rede para outra. Essa opção coloca um analisador de protocolo na porta que recebe os dados espelhados. Ele analisa o tráfego de uma origem, de uma VM ou de um grupo de VMs e, em seguida, enviado para um destino definido. 

Para configurar o espelhamento de porta no console de solução do Azure VMware, você:

* [Etapa 1. Criar VMs de origem e de destino ou grupos de VMs](#step-1-create-source-and-destination-vms-or-vm-groups) – o grupo de origem tem uma única VM ou várias VMs em que o tráfego é espelhado.

* [Etapa 2. Criar um perfil de espelhamento de porta](#step-2-create-a-port-mirroring-profile) – você definirá a direção do tráfego para os grupos de VMs de origem e de destino.

### <a name="step-1-create-source-and-destination-vms-or-vm-groups"></a>Etapa 1. Criar VMs de origem e de destino ou grupos de VMs

Nesta etapa, você criará um grupo de VMs de origem e um grupo de VMs de destino.

1. Em sua nuvem privada da solução Azure VMware, em **rede de carga de trabalho**, selecione **espelhamento de porta**  >  **grupos de VMs**  >  **Adicionar**.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/add-port-mirroring-vm-groups.png" alt-text="Captura de tela mostrando como criar um grupo de VMs para espelhamento de porta.":::

1. Forneça um nome para o novo grupo de VMs, selecione as VMs desejadas na lista e, em seguida, **OK**.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/add-vm-group.png" alt-text="Captura de tela mostrando a lista de VMs a serem adicionadas ao grupo de VMs.":::

1. Repita essas etapas para criar o grupo de VMs de destino.

### <a name="step-2-create-a-port-mirroring-profile"></a>Etapa 2. Criar um perfil de espelhamento de porta

Nesta etapa, você definirá um perfil para a direção do tráfego dos grupos de VMs de origem e destino.

>[!NOTE]
>Verifique se você tem os grupos de VMs de origem e de destino criados.

1. Selecione **espelhamento de porta**  >  **Adicionar** e, em seguida, fornecer:

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/add-port-mirroring-profile.png" alt-text="Captura de tela mostrando as informações necessárias para o perfil de espelhamento de porta.":::

   - **Nome de espelhamento de porta** -nome descritivo para o perfil.
   - **Direção** – selecione de entrada, saída ou bidirecional.
   - **Origem** – selecione o grupo de VMs de origem.
   - **Destino** -selecione o grupo de VMs de destino.
   - **Descrição** – Insira uma descrição para o espelhamento de porta.

1. Selecione **OK** para concluir o perfil. 

   O perfil e os grupos de VM são visíveis no console de solução do Azure VMware.

## <a name="configure-a-dns-forwarder-in-the-azure-portal"></a>Configurar um encaminhador DNS no portal do Azure
Você configurará um encaminhador DNS em que solicitações DNS específicas são encaminhadas para um servidor DNS designado para resolução.  Um encaminhador DNS é associado a uma **zona DNS padrão** e até três **zonas FQDN**.

>[!TIP]
>Você também pode usar o [console do Gerenciador de NSX-T para configurar um encaminhador DNS](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.5/administration/GUID-A0172881-BB25-4992-A499-14F9BE3BE7F2.html).

Para configurar um encaminhador DNS no console de solução VMware do Azure, você vai:

* [Etapa 1. Configurar uma zona DNS padrão e a zona FQDN](#step-1-configure-a-default-dns-zone-and-fqdn-zone) – quando uma consulta DNS é recebida, um encaminhador DNS compara o nome de domínio com os nomes de domínio na zona DNS FQDN. 

* [Etapa 2. Configurar o serviço DNS](#step-2-configure-dns-service) -você configurará o serviço encaminhador DNS.

### <a name="step-1-configure-a-default-dns-zone-and-fqdn-zone"></a>Etapa 1. Configurar uma zona DNS padrão e uma zona FQDN
Você configurará uma zona DNS padrão e a zona FQDN para enviar consultas DNS para o servidor upstream.  Quando uma consulta DNS é recebida, o encaminhador DNS compara o nome de domínio na consulta com os nomes de domínio das zonas DNS do FQDN. Se uma correspondência for encontrada, a consulta será encaminhada para os servidores DNS especificados na zona DNS FQDN. Se nenhuma correspondência for encontrada, a consulta será encaminhada para os servidores DNS especificados na zona DNS padrão.

>[!NOTE]
>Uma zona DNS padrão deve ser definida antes de configurar uma zona FQDN. 

1. Em sua nuvem privada da solução Azure VMware, em **rede de carga de trabalho**, selecione   >  **zonas DNS** DNS  >  **Adicionar**.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking-dns-zones.png" alt-text="Captura de tela mostrando como adicionar zonas DNS e um serviço DNS.":::

1. Selecione a **zona DNS padrão** e forneça:

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking-configure-dns-zones.png" alt-text="Captura de tela mostrando como adicionar uma zona DNS padrão.":::

   1. Um nome para a zona DNS.

   1. Até três endereços IP do servidor DNS no formato de **8.8.8.8**.

1. Selecione **FQDN Zone** e forneça:

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking-configure-fqdn-zone.png" alt-text="Captura de tela mostrando como adicionar uma zona FQDN. ":::

   1. Um nome para a zona DNS.

   1. O domínio FQDN.

   1. Até três endereços IP do servidor DNS no formato de **8.8.8.8**.

1. Selecione **OK** para concluir a adição da zona DNS padrão e do serviço DNS.

### <a name="step-2-configure-dns-service"></a>Etapa 2. Configurar o serviço DNS

1. Selecione a guia **serviço DNS** , selecione **Adicionar** e, em seguida, forneça:

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking-configure-dns-service.png" alt-text="Captura de tela mostrando as informações necessárias para o serviço DNS.":::

   1. Um nome para o serviço DNS.

   1. Insira o endereço IP para o serviço DNS.

   1. Selecione a zona DNS padrão que você criou na guia zonas DNS.

   1. Selecione as zonas de FQDN que você adicionou na guia zonas DNS.

   1. Selecione o **nível de log**.

   >[!TIP]
   >**Gateway de camada 1** é selecionado por padrão e reflete o gateway criado ao implantar a solução do Azure VMware.

1. Selecione **OK**. 

   O serviço DNS foi adicionado com êxito.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking-configure-dns-service-success.png" alt-text="Captura de tela mostrando o serviço DNS adicionado com êxito.":::

