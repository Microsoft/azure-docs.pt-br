---
title: 'Tutorial: Filtrar o tráfego da Internet de entrada com o DNAT do Firewall do Azure usando o portal'
description: Neste tutorial, você aprenderá a implantar e configurar o DNAT do Firewall do Azure usando o portal do Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 03/01/2021
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: a1d3bdae1e870b094472a63d4b808d9df95c129d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101741898"
---
# <a name="tutorial-filter-inbound-internet-traffic-with-azure-firewall-dnat-using-the-azure-portal"></a>Tutorial: Filtrar o tráfego da Internet de entrada com o DNAT do Firewall do Azure usando o portal do Azure

Você pode configurar o DNAT (conversão de endereço de rede de destino) do Firewall do Azure para converter e filtrar o tráfego da Internet de entrada para as sub-redes. Quando você configura o modo DNAT, a ação da coleção de regras da NAT é definida como **Dnat**. Assim, cada regra na coleção de regras NAT pode ser usada para converter o endereço IP e a porta públicos em endereço IP e porta privados do firewall. As regras DNAT adicionam implicitamente uma regra de rede correspondente para permitir o tráfego convertido. Por motivos de segurança, a abordagem recomendada é adicionar uma fonte específica de Internet para permitir o acesso DNAT à rede e evitar o uso de caracteres curinga. Para saber mais sobre a lógica de processamento de regra do Firewall do Azure, confira [Lógica de processamento de regra do Firewall no Azure](rule-processing.md).

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Configurar um ambiente de rede de teste
> * Implantar um firewall
> * Criar uma rota padrão
> * Configurar uma regra de DNAT
> * Testar o firewall

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.



## <a name="create-a-resource-group"></a>Criar um grupo de recursos

1. Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. Na Página Inicial do portal do Azure, selecione **Grupos de recursos** e, em seguida, **Adicionar**.
4. Em **Assinatura**, selecione sua assinatura.
1. Em **Nome do grupo de recursos**, digite **RG-DNAT-Test**.
5. Em **Região**, selecione uma região. Todos os demais recursos criados devem estar na mesma região.
6. Selecione **Examinar + criar**.
1. Selecione **Criar**.

## <a name="set-up-the-network-environment"></a>Configurar o ambiente de rede

Neste tutorial, você criará duas redes virtuais emparelhadas:

- **VN-Hub** - o firewall está nessa rede virtual.
- **VN-Spoke** - o servidor de carga de trabalho está nessa rede virtual.

Primeiro, crie as redes virtuais e, em seguida, emparelhe-as.

### <a name="create-the-hub-vnet"></a>Criar a rede virtual do hub

1. Na página inicial do portal do Azure, selecione **Todos os serviços**.
2. Em **Rede**, selecione **Redes virtuais**.
3. Selecione **Adicionar**.
7. Em **Grupo de recursos**, selecione **RG-DNAT-Test**.
1. Em **Nome**, digite **VN-Hub**.
1. Em **Região**, selecione a mesma região usada antes.
1. Selecione **Avançar: Endereços IP**.
1. Em **Espaço de endereço IPv4**, aceite o padrão **10.0.0.0/16**.
1. Em **Nome da sub-rede**, selecione padrão.
1. Edite o **nome da sub-rede** e digite **AzureFirewallSubnet**.

     O firewall estará nessa sub-rede e o nome da sub-rede **precisa** ser AzureFirewallSubnet.
     > [!NOTE]
     > O tamanho da sub-rede AzureFirewallSubnet é /26. Para obter mais informações sobre o tamanho da sub-rede, confira [Perguntas frequentes sobre o Firewall do Azure](firewall-faq.yml#why-does-azure-firewall-need-a--26-subnet-size).

10. Em **Intervalo de endereços da sub-rede**, digite **10.0.1.0/26**.
11. Clique em **Salvar**.
1. Selecione **Examinar + criar**.
1. Selecione **Criar**.

### <a name="create-a-spoke-vnet"></a>Criar uma rede virtual do spoke

1. Na página inicial do portal do Azure, selecione **Todos os serviços**.
2. Em **Rede**, selecione **Redes virtuais**.
3. Selecione **Adicionar**.
1. Em **Grupo de recursos**, selecione **RG-DNAT-Test**.
1. Em **Nome**, digite **VN-Spoke**.
1. Em **Região**, selecione a mesma região usada antes.
1. Selecione **Avançar: Endereços IP**.
1. Em **Espaço de endereço IPv4**, edite o padrão e digite **192.168.0.0/16**.
1. Selecione **Adicionar sub-rede**.
1. Em **Nome da sub-rede**, digite **SN-Workload**.
10. Em **Intervalo de endereços da sub-rede**, digite **192.168.1.0/24**.
11. Selecione **Adicionar**.
1. Selecione **Examinar + criar**.
1. Selecione **Criar**.

### <a name="peer-the-vnets"></a>Emparelhar as redes virtuais

Agora, emparelhe as duas redes virtuais.

1. Selecione a rede virtual **VN-Hub**.
2. Em **Configurações**, selecione **Emparelhamentos**.
3. Selecione **Adicionar**.
4. Em **Esta rede virtual**, em **Nome do link de emparelhamento**, digite **Peer-HubSpoke**.
5. Em **Rede virtual remota**, em **Nome do link de emparelhamento**, digite **Peer-SpokeHub**. 
1. Escolha **VN-Spoke** para a rede virtual.
1. Aceite todos os outros padrões e, em seguida, selecione **Adicionar**.

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Crie uma máquina virtual de carga de trabalho e coloque-a na sub-rede **SN-Workload**.

1. No menu do portal do Azure, selecione **Criar um recurso**.
2. Em **Popular**, selecione **Windows Server 2016 Datacenter**.

**Noções básicas**

1. Em **Assinatura**, selecione sua assinatura.
1. Em **Grupo de recursos**, selecione **RG-DNAT-Test**.
1. Em **Nome da máquina virtual**, digite **Srv-Workload**.
1. Em **Região**, selecione a mesma localização usada anteriormente.
1. Digite um nome de usuário e uma senha.
1. Selecione **Avançar: Discos**.

**Discos**
1. Selecione **Avançar: Rede**.

**Rede**

1. Em **Rede virtual**, selecione **VN-Spoke**.
2. Em **Sub-rede**, escolha **SN-Workload**.
3. Em **IP Público**, selecione **Nenhum**.
4. Em **Portas de entrada públicas**, selecione **Nenhuma**. 
2. Mantenha as outras configurações padrão e selecione **Avançar: Gerenciamento**.

**Gerenciamento**

1. Em **Diagnóstico de inicialização**, selecione **Desabilitar**.
1. Selecione **Examinar + criar**.

**Examinar + Criar**

Examine o resumo e, em seguida, selecione **Criar**. Isso levará alguns minutos para ser concluído.

Após a conclusão da implantação, observe o endereço IP privado da máquina virtual. Ele será usado mais tarde ao configurar o firewall. Selecione o nome da máquina virtual e, em **Configurações**, selecione **Rede** para encontrar o endereço IP privado.

## <a name="deploy-the-firewall"></a>Implantar o firewall

1. Na página inicial do portal, selecione **Criar um recurso**.
1. Pesquise **Firewall** e, em seguida, selecione **Firewall**.
1. Selecione **Criar**. 
1. Na página **Criar um Firewall**, use a tabela abaixo para configurar o firewall:

   |Configuração  |Valor  |
   |---------|---------|
   |Subscription     |\<your subscription\>|
   |Resource group     |Selecione **RG-DNAT-Test** |
   |Name     |**FW-DNAT-test**|
   |Região     |Selecionar o mesmo local usado anteriormente|
   |Gerenciamento do firewall|**Use regras de Firewall (clássicas) para gerenciar este firewall**|
   |Escolher uma rede virtual     |**Usar existente**: VN-Hub|
   |Endereço IP público     |**Adicionar novo**, Nome: **fw-pip**.|

5. Aceite os outros padrões e selecione **Revisar + criar**.
6. Examine o resumo e selecione **Criar** para criar o firewall.

   Isso levará alguns minutos para ser implantado.
7. Depois que a implantação for concluída, acesse o grupo de recursos **RG-DNAT-Test** e selecione o firewall **FW-DNAT-test**.
8. Anote os endereços IP públicos e privados do firewall. Você os usará posteriormente quando criar a regra padrão de rota e NAT.

## <a name="create-a-default-route"></a>Criar uma rota padrão

Para a sub-rede **SN-Workload**, configure a rota de saída padrão para atravessar o firewall.

1. Na página inicial do portal do Azure, selecione **Todos os serviços**.
2. Em **Rede**, selecione **Tabelas de rotas**.
3. Selecione **Adicionar**.
5. Em **Assinatura**, selecione sua assinatura.
1. Em **Grupo de recursos**, selecione **RG-DNAT-Test**.
1. Em **Região**, selecione a mesma região usada anteriormente.
1. Em **Nome**, digite **RT-FWroute**.
1. Selecione **Examinar + criar**.
1. Selecione **Criar**.
1. Selecione **Ir para o recurso**.
1. Selecione **Sub-redes** e, em seguida, **Associar**.
1. Em **Rede virtual**, selecione **VN-Spoke**.
1. Em **Sub-rede**, escolha **SN-Workload**.
1. Selecione **OK**.
1. Selecione **Rotas** e, em seguida, **Adicionar**.
1. Em **Nome da rota**, digite **FW-DG**.
1. Em **Prefixo de endereço**, digite **0.0.0.0/0**.
1. Em **Tipo do próximo salto**, selecione **Solução de virtualização** .

    O Firewall do Azure é, na verdade, um serviço gerenciado, mas a solução de virtualização funciona nessa situação.
18. Em **endereço do próximo salto**, digite o endereço IP privado do firewall anotado anteriormente.
19. Selecione **OK**.

## <a name="configure-a-nat-rule"></a>Configurar uma regra NAT

1. Abra o grupo de recursos **RG-DNAT-Test** e selecione o firewall **FW-DNAT-test**. 
2. Na página **FW-DNAT-test**, em **Configurações**, selecione **Regras (clássicas)** . 
3. Selecione **Adicionar coleção de regras do NAT**. 
4. Para **Nome**, digite **RC-DNAT-01**. 
5. Digite **200** em **Prioridade**. 
6. Em **Regras**, para **Nome**, digite **RL-01**.
7. Em **Protocolo**, selecione **TCP**.
1. Em **Tipo de origem**, selecione **Endereço IP**.
1. Em **Origem**, digite *. 
1. Em **Endereços de destino**, digite o endereço IP público do firewall. 
1. Para **Portas de Destino**, digite **3389**. 
1. Para **Endereço Convertido** digite o endereço IP privado da máquina virtual Srv-Workload. 
1. Para **Porta traduzida**, digite **3389**. 
1. Selecione **Adicionar**. Essa ação levará alguns minutos para ser concluída.

## <a name="test-the-firewall"></a>Testar o firewall

1. Conecte uma área de trabalho remota ao endereço IP público do firewall. Você deve estar conectado à máquina virtual **Srv-Workload**.
2. Feche a área de trabalho remota.

## <a name="clean-up-resources"></a>Limpar recursos

Você pode manter seus recursos de firewall para o próximo tutorial ou, se não forem mais necessários, exclua o grupo de recursos **RG-DNAT-Test** para excluir todos os recursos relacionados ao firewall.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Configurar um ambiente de rede de teste
> * Implantar um firewall
> * Criar uma rota padrão
> * Configurar uma regra de DNAT
> * Testar o firewall

Em seguida,você pode monitorar os logs do Firewall do Azure.

> [!div class="nextstepaction"]
> [Tutorial: Monitorar os logs do Firewall do Azure](./firewall-diagnostics.md)
