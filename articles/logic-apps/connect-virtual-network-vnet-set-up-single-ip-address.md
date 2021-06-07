---
title: Configurar um endereço IP de saída público para ISEs
description: Saiba como configurar um único endereço IP de saída público para ambientes do serviço de integração (ISEs) no serviço Aplicativos Lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 05/06/2020
ms.openlocfilehash: e88c4bf05d88007a6e19b568f1bc1085e24b0325
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102211049"
---
# <a name="set-up-a-single-ip-address-for-one-or-more-integration-service-environments-in-azure-logic-apps"></a>Configurar um único endereço IP para um ou mais ambientes de serviço de integração no serviço Aplicativos Lógicos do Azure

Quando trabalhar com o serviço Aplicativos Lógicos do Azure, você pode configurar um [*ambiente do serviço de integração* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) para hospedar aplicativos lógicos que precisam acessar recursos em uma [rede virtual do Azure](../virtual-network/virtual-networks-overview.md). Quando você tem várias instâncias do ISE que precisam de acesso a outros pontos de extremidade que têm restrições de IP, implante um [Firewall do Azure](../firewall/overview.md) ou uma [solução de virtualização de rede](../virtual-network/virtual-networks-overview.md#filter-network-traffic) em sua rede virtual e roteie o tráfego de saída por meio do firewall ou da solução de virtualização de rede. Você pode fazer com que todas as instâncias do ISE em sua rede virtual usem um único endereço IP público, estático e previsível para se comunicar com os sistemas de destino desejados. Dessa forma, você não precisa configurar aberturas adicionais do firewall nos seus sistemas de destino para cada ISE.

Este tópico mostra como rotear o tráfego de saída por meio de um Firewall do Azure, mas você pode aplicar conceitos semelhantes a uma solução de virtualização de rede e, como um firewall de terceiros do Azure Marketplace. Embora este tópico se concentre na configuração de várias instâncias do ISE, você também pode usar essa abordagem para um único ISE quando seu cenário exigir a limitação do número de endereços IP que precisam de acesso. Considere se os custos adicionais para o firewall ou solução de virtualização de rede fazem sentido para o seu cenário. Saiba mais sobre os [preços do Firewall do Azure](https://azure.microsoft.com/pricing/details/azure-firewall/).

## <a name="prerequisites"></a>Pré-requisitos

* Um firewall do Azure que é executado na mesma rede virtual que o ISE. Se você não tiver um firewall, você deve primeiro [adicionar uma sub-rede](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet) denominada `AzureFirewallSubnet` à sua rede virtual. Em seguida, você pode [criar e implantar um firewall](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall) em sua rede virtual.

* Uma [tabela de rotas](../virtual-network/manage-route-table.md) do Azure. Se não tiver uma, você deve primeiro [criar uma tabela de rotas](../virtual-network/manage-route-table.md#create-a-route-table). Para saber mais sobre roteamento, consulte [Roteamento de tráfego de rede virtual](../virtual-network/virtual-networks-udr-overview.md).

## <a name="set-up-route-table"></a>Configurar tabela de rotas

1. No [portal do Azure](https://portal.azure.com), selecione a tabela de rotas, por exemplo:

   ![Selecionar a tabela de rotas com a regra para direcionar o tráfego de saída](./media/connect-virtual-network-vnet-set-up-single-ip-address/select-route-table-for-virtual-network.png)

1. Para [adicionar uma nova rota](../virtual-network/manage-route-table.md#create-a-route), no menu tabela de rotas, selecione **Rotas** > **Adicionar**.

   ![Adicionar rota para direcionar o tráfego de saída](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-route-to-route-table.png)

1. No painel **Adicionar rota**, [configure a nova rota](../virtual-network/manage-route-table.md#create-a-route) com uma regra que especifica que todo o tráfego de saída para o sistema de destino segue esse comportamento:

   * Usa a [**Solução de virtualização**](../virtual-network/virtual-networks-udr-overview.md#user-defined) como o tipo do próximo salto.

   * Vai para o endereço IP privado da instância de firewall como o endereço do próximo salto.

     Para localizar esse endereço IP, no menu do firewall, selecione **Visão geral**, localize o endereço em **Endereço IP privado**, por exemplo:

     ![Encontrar endereço IP privado do firewall](./media/connect-virtual-network-vnet-set-up-single-ip-address/find-firewall-private-ip-address.png)

   Veja um exemplo que mostra como pode ser essa regra:

   ![Configurar rota para direcionar o tráfego de saída](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-rule-to-route-table.png)

   | Propriedade | Valor | Descrição |
   |----------|-------|-------------|
   | **Nome da rota** | <*unique-route-name*> | Um nome exclusivo para a rota na tabela de rotas |
   | **Prefixo de endereço** | <*destination-address*> | O prefixo de endereço do sistema de destino para onde você deseja que vá o tráfego de saída. Certifique-se de usar [notação de Roteamento entre Domínios sem Classificação (CIDR)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) para esse endereço. Neste exemplo, esse prefixo de endereço é para um servidor SFTP, que é descrito na seção [Configurar a regra de rede](#set-up-network-rule). |
   | **Tipo do próximo salto** | **Solução de virtualização** | O [tipo de salto](../virtual-network/virtual-networks-udr-overview.md#next-hop-types-across-azure-tools) usado pelo tráfego de saída |
   | **Endereço do próximo salto** | <*firewall-private-IP-address*> | O endereço IP privado para o seu firewall |
   |||

<a name="set-up-network-rule"></a>

## <a name="set-up-network-rule"></a>Configurar regra de rede

1. No portal do Azure, encontre e selecione o seu firewall. No menu de firewall, em **Configurações**, selecione **Regras**. No painel de regras, selecione **Coleção de regras de rede** > **Adicionar coleção de regras de rede**.

   ![Adicionar coleção de regras de rede ao firewall](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-network-rule-collection.png)

1. Na coleção, adicione uma regra que permita o tráfego para o sistema de destino.

   Por exemplo, suponha que você tenha um aplicativo lógico executado em um ISE e que precise se comunicar com um servidor SFTP. Você cria uma coleção de regras de rede chamada `LogicApp_ISE_SFTP_Outbound`, que contém uma regra de rede chamada `ISE_SFTP_Outbound`. Essa regra permite o tráfego do endereço IP de qualquer sub-rede na qual o ISE é executado em sua rede virtual para o servidor de destino SFTP usando o endereço IP privado do firewall.

   ![Configurar regra de rede para o firewall](./media/connect-virtual-network-vnet-set-up-single-ip-address/set-up-network-rule-for-firewall.png)

   **Propriedades da coleção de regras de rede**

   | Propriedade | Valor | Descrição |
   |----------|-------|-------------|
   | **Nome** | <*network-rule-collection-name*> | O nome da coleção de regras de rede |
   | **Prioridade** | <*priority-level*> | A ordem de prioridade a ser usada para executar a coleção de regras. Para obter mais informações, consulte [Quais são alguns dos conceitos do Firewall do Azure](../firewall/firewall-faq.yml#what-are-some-azure-firewall-concepts)? |
   | **Ação** | **Permitir** | O tipo de ação a ser executado para esta regra |
   |||

   **Propriedades da regra de rede**

   | Propriedade | Valor | Descrição |
   |----------|-------|-------------|
   | **Nome** | <*network-rule-name*> | O nome da regra de rede |
   | **Protocolo** | <*connection-protocols*> | Os protocolos de conexão a serem usados. Por exemplo, se você estiver usando regras NSG, selecione **TCP** e **UDP**, não apenas **TCP**. |
   | **Endereços de origem** | <*ISE-subnet-addresses*> | Os endereços IP de sub-rede onde o ISE é executado e onde o tráfego do seu aplicativo lógico é originado |
   | **Endereços de destino** | <*destination-IP-address*> | O endereço IP do sistema de destino para onde você deseja que vá o tráfego de saída. Neste exemplo, esse endereço IP é para o servidor SFTP. |
   | **Portas de destino** | <*destination-ports*> | Todas as portas que o sistema de destino usa para comunicação de entrada |
   |||

   Para saber mais sobre as regras de rede, consulte estes artigos:

   * [Configurar uma regra de rede](../firewall/tutorial-firewall-deploy-portal.md#configure-a-network-rule)
   * [Lógica de processamento de regra do Firewall do Azure](../firewall/rule-processing.md#network-rules-and-applications-rules)
   * [Perguntas frequentes sobre o Firewall do Azure](../firewall/firewall-faq.yml)
   * [Azure PowerShell: New-AzFirewallNetworkRule](/powershell/module/az.network/new-azfirewallnetworkrule)
   * [CLI do Azure: az network firewall network-rule](/cli/azure/ext/azure-firewall/network/firewall/network-rule#ext-azure-firewall-az-network-firewall-network-rule-create)

## <a name="next-steps"></a>Próximas etapas

* [Conectar-se a redes virtuais do Azure a partir do serviço Aplicativos Lógicos do Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
