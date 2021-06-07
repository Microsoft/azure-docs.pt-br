---
title: 'Tutorial: Proteger sua rede virtual de hub usando o Gerenciador de Firewall do Azure'
description: Neste tutorial, você aprenderá a proteger sua rede virtual com o Gerenciador de Firewall do Azure usando o portal do Azure.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: tutorial
ms.date: 03/03/2021
ms.author: victorh
ms.openlocfilehash: f631100003a4ea6e191a5bdc13a11eb9aa327268
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102212528"
---
# <a name="tutorial-secure-your-hub-virtual-network-using-azure-firewall-manager"></a>Tutorial: Proteger sua rede virtual de hub usando o Gerenciador de Firewall do Azure

Ao conectar sua rede local a uma rede virtual do Azure para criar uma rede híbrida, a capacidade de controlar o acesso aos recursos da rede do Azure é uma parte importante de um plano geral de segurança.

Com o Gerenciador de Firewall do Azure, é possível criar uma rede virtual de hub para proteger o tráfego de rede híbrida destinado a endereços IP privados, PaaS do Azure e à Internet. É possível usar o Gerenciador de Firewall do Azure para controlar o acesso de rede em uma rede híbrida usando as políticas que definem tráfegos de rede permitidos e negados.

O Gerenciador de Firewall também dá suporte a uma arquitetura de hub virtual seguro. Para obter uma comparação dos tipos de arquitetura de rede virtual de hub e hub virtual seguro, confira [quais são as opções de arquitetura do Gerenciador de Firewall do Azure?](vhubs-and-vnets.md)

Para este tutorial, você deve criar três redes virtuais:

- **VNet-Hub**: o firewall está nessa rede virtual.
- **VNet-Spoke**: a rede virtual spoke representa a carga de trabalho localizada no Azure.
- **VNet-Onprem**: a rede virtual local representa uma rede local. Em uma implantação real, pode ser conectada por uma conexão VPN ou ExpressRoute. Para simplificar, este tutorial usa uma conexão de gateway de VPN, e uma rede virtual localizada no Azure é usada para representar uma rede local.

![Rede híbrida](media/tutorial-hybrid-portal/hybrid-network-firewall.png)

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma política de firewall
> * Criar as redes virtuais
> * Configurar e implantar o firewall
> * Criar e conectar os gateways de VPN
> * Emparelhar as redes virtuais hub e spoke
> * Criar as rotas
> * Criar as máquinas virtuais
> * Testar o firewall


## <a name="prerequisites"></a>Pré-requisitos

Uma rede híbrida usa o modelo de arquitetura hub e spoke para rotear o tráfego entre as VNets do Azure e as redes locais. A arquitetura hub e spoke tem os seguintes requisitos:

- Defina **AllowGatewayTransit** ao emparelhar o VNet-Hub com o VNet-Spoke. Em uma arquitetura de rede hub e spoke, um trânsito de gateway permite que redes virtuais spoke compartilhem o gateway de VPN no hub, em vez de implantar gateways de VPN em cada rede virtual spoke. 

   Além disso, as rotas para as redes virtuais conectadas pelo gateway ou das redes locais serão propagadas automaticamente às tabelas de roteiros para as redes virtuais emparelhadas usando o trânsito de gateway. Para saber mais, confira [Configurar o trânsito de gateway de VPN para o emparelhamento de rede virtual](../vpn-gateway/vpn-gateway-peering-gateway-transit.md).

- Defina **UseRemoteGateways** quando você emparelhar VNet-Spoke com VNet-Hub. Se **UseRemoteGateways** estiver definido e **AllowGatewayTransit** no emparelhamento remoto também estiver definido, a rede virtual spoke usará gateways da rede virtual remota para trânsito.
- Para rotear o tráfego de sub-rede spoke por meio do firewall do hub, você precisa de uma UDR (Rota Definida pelo Usuário) que aponte para o firewall com a configuração **Propagação de rotas de gateway de rede virtual** desabilitada. Essa opção impede a distribuição de rota para as sub-redes spoke. Isso impede que as rotas aprendidas entrem em conflito com sua UDR.
- Configure uma UDR na sub-rede do gateway do hub que aponta para o endereço IP do firewall como o próximo salto para as redes spoke. Nenhuma UDR é necessária na sub-rede do Firewall do Azure, já que ela aprende as rotas com o BGP.

Consulte a seção [Criar Rotas](#create-the-routes) deste tutorial para ver como essas rotas são criadas.

>[!NOTE]
>O Firewall do Azure deve ter conectividade direta com a Internet. Se seu AzureFirewallSubnet aprender uma rota padrão para sua rede local via BGP, você deve substituir isso por um UDR 0.0.0.0/0 com o valor **NextHopType** definido como **Internet** para manter a conectividade direta com a Internet.
>
>O Firewall do Azure pode ser configurado para dar suporte a túnel forçado. Para obter mais informações, confira [Túnel forçado do Firewall do Azure](../firewall/forced-tunneling.md).

>[!NOTE]
>O tráfego entre VNETs diretamente emparelhadas é roteado diretamente, mesmo se uma UDR aponta para o Firewall do Azure como o gateway padrão. Para enviar o tráfego de sub-rede para sub-rede para o firewall nesse cenário, uma UDR precisa conter o prefixo de rede da sub-rede de destino explicitamente em ambas as sub-redes.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-a-firewall-policy"></a>Criar uma política de firewall

1. Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. Na barra de pesquisa do portal do Azure, digite **Gerenciador de Firewall** e pressione **Enter**.
3. Na página Gerenciador de Firewall do Azure, selecione **Exibir políticas de firewall do Azure**.

   ![Política de firewall](media/tutorial-hybrid-portal/firewall-manager-policy.png)

1. Selecione **Criar uma Política de Firewall do Azure**.
1. Selecione sua assinatura e, para o Grupo de recursos, selecione **Criar** e crie um grupo de recursos chamado **FW-Hybrid-Test**.
2. Para o nome da política, digite **Pol-Net01**.
3. Em Região, selecione **Leste dos EUA**.
1. Selecione **Próximo: Configurações de DNS**.
1. Selecione **Próximo: Inspeção de TLS (versão prévia)**
1. Selecione **Próximo: Regras**.
1. Selecione **Adicionar uma coleção de regras**.
1. Em **Nome**, digite **RCNet01**.
1. Para **Tipo de coleção de regras**, selecione **Rede**.
1. Em **Prioridade**, digite **100**.
1. Em **Ação**, selecione **Permitir**.
1. Em **Regras**, em **Nome**, digite **AllowWeb**.
1. Em **Origem**, digite **192.168.1.0/24**.
1. Em **Protocolo**, selecione **TCP**.
1. Em **Portas de Destino**, digite **80**.
1. Para **Tipo de Destino**, selecione **Endereço IP**.
1. Para **Destino**, digite **10.6.0.0/16**.
1. Na próxima linha de regra, insira as seguintes informações:
 
    Nome: digite **AllowRDP**<br>
    Origem: digite **192.168.1.0/24**.<br>
    Protocolo: selecione **TCP**<br>
    Portas de Destino: digite **3389**<br>
    Tipo de Destino: selecione **Endereço IP**<br>
    Para Destino, digite **10.6.0.0/16**

1. Selecione **Adicionar**.
2. Selecione **Examinar + criar**.
3. Examine os detalhes e depois selecione **Criar**.

## <a name="create-the-firewall-hub-virtual-network"></a>Criar a rede virtual do hub de firewall

> [!NOTE]
> O tamanho da sub-rede AzureFirewallSubnet é /26. Para obter mais informações sobre o tamanho da sub-rede, confira [Perguntas frequentes sobre o Firewall do Azure](../firewall/firewall-faq.yml#why-does-azure-firewall-need-a--26-subnet-size).

1. Na página inicial do portal do Azure, selecione **Criar um recurso**.
2. Pesquise por **Rede virtual** e selecione **Rede virtual**.
1. Selecione **Criar**.
1. Em **Assinatura**, selecione sua assinatura.
1. Em **Grupo de recursos**, selecione **FW-Hybrid-Test**.
1. Em **Nome**, digite **VNet-hub**.
1. Em **Região**, selecione **Leste dos EUA**.
1. Selecione **Próximo: Endereços IP**.

1. Em **Espaço de endereço IPv4**, digite **10.5.0.0/16**.
1. Em **Nome da sub-rede**, selecione **padrão**.
1.  Altere o **Nome da sub-rede** para **AzureFirewallSubnet**. O firewall está nessa sub-rede, e o nome da sub-rede **precisa** ser AzureFirewallSubnet.
1. Em **Intervalo de endereços da sub-rede**, digite **10.5.0.0/26**. 
1. Aceite as outras configurações padrão e selecione **Salvar**.
1. Selecione **Examinar + criar**.
1. Selecione **Criar**.

## <a name="create-the-spoke-virtual-network"></a>Criar a rede virtual spoke

1. Na página inicial do portal do Azure, selecione **Criar um recurso**.
2. Pesquise por **Rede virtual** e selecione **Rede virtual**.
1. Selecione **Criar**.
1. Em **Assinatura**, selecione sua assinatura.
1. Em **Grupo de recursos**, selecione **FW-Hybrid-Test**.
1. Em **Nome**, digite **VNet-Spoke**.
1. Em **Região**, selecione **Leste dos EUA**.
1. Selecione **Próximo: Endereços IP**.

1. Em **Espaço de endereços IPv4**, digite **10.6.0.0/16**.
1. Em **Nome da sub-rede**, selecione **padrão**.
1. Altere o **Nome da sub-rede** para **SN-Workload**.
1. Em **Intervalo de endereços da sub-rede**, digite **10.6.0.0/24**. 
1. Aceite as outras configurações padrão e selecione **Salvar**.
1. Selecione **Examinar + criar**.
1. Selecione **Criar**.


## <a name="create-the-on-premises-virtual-network"></a>Criar a rede virtual local

1. Na página inicial do portal do Azure, selecione **Criar um recurso**.
2. Pesquise por **Rede virtual** e selecione **Rede virtual**.
1. Selecione **Criar**.
1. Em **Assinatura**, selecione sua assinatura.
1. Em **Grupo de recursos**, selecione **FW-Hybrid-Test**.
1. Em **Nome**, digite **VNet-OnPrem**.
1. Em **Região**, selecione **Leste dos EUA**.
1. Selecione **Próximo: Endereços IP**.

1. Em **Espaço de endereços IPv4**, digite **192.168.0.0/16**.
1. Em **Nome da sub-rede**, selecione **padrão**.
1. Altere o **Nome da sub-rede** para **SN-Corp**.
1. Em **Intervalo de endereços da sub-rede**, digite **192.168.1.0/24**. 
1. Aceite as outras configurações padrão e selecione **Salvar**.
2. Clique em **Adicionar Sub-rede**.
3. Para **Nome da sub-rede**, digite **GatewaySubnet**.
4. Em **Intervalo de endereços da sub-rede**, digite **192.168.2.0/24**.
5. Selecione **Adicionar**.
1. Selecione **Examinar + criar**.
1. Selecione **Criar**.




## <a name="configure-and-deploy-the-firewall"></a>Configurar e implantar o firewall

Quando políticas de segurança são associadas a um hub, ele é chamado de *rede virtual de hub*.

Converta a rede virtual **VNet-Hub** em uma *rede virtual de hub* e proteja-a com o Firewall do Azure.

1. Na barra de pesquisa do portal do Azure, digite **Gerenciador de Firewall** e pressione **Enter**.
3. Na página do Gerenciador de Firewall do Azure, em **Adicionar segurança a redes virtuais**, selecione **Exibir redes virtuais de hub**.
1. Em **Redes Virtuais**, marque a caixa de seleção para **VNet-hub**.
1. Selecione **Gerenciar Segurança** e **Implantar um Firewall com a Política de Firewall**.
1. Na página **Converter redes virtuais**, em **Política de Firewall**, marque a caixa de seleção para **Pol-Net01**.
1. Selecione **Próximo: Examinar + confirmar**
1. Examine os detalhes e, em seguida, selecione **Confirmar**.


   Isso leva alguns minutos para ser implantado.
7. Após a conclusão da implantação, acesse o grupo de recursos **FW-Hybrid-Test** e selecione o firewall.
9. Observe o endereço **IP privado do firewall** na página **Visão geral**. Você o usará mais tarde quando criar a rota padrão.

## <a name="create-and-connect-the-vpn-gateways"></a>Criar e conectar os gateways de VPN

As redes virtuais locais e de hub são conectadas por meio de gateways VPN.

### <a name="create-a-vpn-gateway-for-the-hub-virtual-network"></a>Criar um gateway de VPN para a rede virtual do hub

Agora crie o gateway de VPN para a rede virtual do hub. As configurações de rede a rede exigem um RouteBased VpnType. A criação de um gateway de VPN pode levar 45 minutos ou mais, dependendo da SKU do gateway de VPN selecionado.

1. Na página inicial do portal do Azure, selecione **Criar um recurso**.
2. Na caixa de texto de pesquisa, digite **gateway de rede virtual** e pressione **Enter**.
3. Selecione **Gateway de rede virtual** e selecione **Criar**.
4. Em **Nome**, digite **GW-hub**.
5. Em **Região**, selecione **EUA (Leste dos EUA)** .
6. Em **Tipo de gateway**, selecione **VPN**.
7. Em **Tipo de VPN**, selecione **Baseada em rota**.
8. Em **SKU**, selecione **Básico**.
9. Em **Rede virtual**, selecione **VNet-hub**.
10. Em **Endereço IP público**, selecione **Criar** e digite **VNet-hub-GW-pip** para o nome.
11. Aceite os padrões restantes e, em seguida, selecione **Examinar + criar**.
12. Examine a configuração e, em seguida, selecione **Criar**.

### <a name="create-a-vpn-gateway-for-the-on-premises-virtual-network"></a>Criar um gateway de VPN para a rede virtual local

Agora crie um gateway de VPN para a rede virtual local. As configurações de rede a rede exigem um RouteBased VpnType. A criação de um gateway de VPN pode levar 45 minutos ou mais, dependendo da SKU do gateway de VPN selecionado.

1. Na página inicial do portal do Azure, selecione **Criar um recurso**.
2. Na caixa de texto de pesquisa, digite **gateway de rede virtual** e pressione **Enter**.
3. Selecione **Gateway de rede virtual** e selecione **Criar**.
4. Em **Nome**, digite **GW-Onprem**.
5. Em **Região**, selecione **EUA (Leste dos EUA)** .
6. Em **Tipo de gateway**, selecione **VPN**.
7. Em **Tipo de VPN**, selecione **Baseada em rota**.
8. Em **SKU**, selecione **Básico**.
9. Em **Rede virtual**, selecione **VNet-Onprem**.
10. Em **Endereço IP público**, selecione **Criar** e digite **VNet-Onprem-GW-pip** para o nome.
11. Aceite os padrões restantes e, em seguida, selecione **Examinar + criar**.
12. Examine a configuração e, em seguida, selecione **Criar**.

### <a name="create-the-vpn-connections"></a>Criar conexões de VPN

Agora você pode criar as conexões VPN entre o hub e os gateways locais.

Nesta etapa, você criará a conexão da rede virtual do hub à rede virtual local. Você verá uma chave compartilhada referenciada nos exemplos. Você pode usar seus próprios valores para a chave compartilhada. O importante é que a chave compartilhada deve corresponder em ambas as conexões. Leva um tempo para criar a conexão.

1. Abra o grupo de recursos **FW-Hybrid-Test** e selecione o gateway **GW-hub**.
2. Selecione **Conexões** na coluna à esquerda.
3. Selecione **Adicionar**.
4. Para o nome da conexão, digite **Hub-to-Onprem**.
5. Selecione **VNET a VNET** em **Tipo de conexão**.
6. No **Segundo gateway de rede virtual**, selecione **GW-Onprem**.
7. Em **Chave compartilhada (PSK)** , digite **AzureA1b2C3**.
8. Selecione **OK**.

Crie a conexão da rede virtual local à de hub. Esta etapa é semelhante à anterior, exceto que você cria a conexão da VNet-Onprem à VNet-hub. Verifique se que as chaves compartilhadas correspondem. Depois de alguns minutos, a conexão deverá ser estabelecida.

1. Abra o grupo de recursos **FW-Hybrid-Test** e selecione o gateway **GW-Onprem**.
2. Selecione **Conexões** na coluna à esquerda.
3. Selecione **Adicionar**.
4. Para o nome da conexão, digite **Onprem-to-Hub**.
5. Selecione **VNET a VNET** em **Tipo de conexão**.
6. Para o **Segundo gateway de rede virtual**, selecione **GW-hub**.
7. Em **Chave compartilhada (PSK)** , digite **AzureA1b2C3**.
8. Selecione **OK**.


#### <a name="verify-the-connection"></a>Verificar conexão

Após cerca de cinco minutos ou mais, o status de ambas as conexões deve estar **Conectado**.

![Conexões de gateway](media/secure-hybrid-network/gateway-connections.png)

## <a name="peer-the-hub-and-spoke-virtual-networks"></a>Emparelhar as redes virtuais hub e spoke

Agora emparelhe as redes virtuais hub e spoke.

1. Abra o grupo de recursos **FW-Hybrid-Test** e selecione a rede virtual **VNet-hub**.
2. Na coluna à esquerda, selecione **Emparelhamentos**.
3. Selecione **Adicionar**.
4. Em **Esta rede virtual**:
 
   
   |Nome da configuração  |Valor  |
   |---------|---------|
   |Nome do link de emparelhamento| HubtoSpoke|
   |Tráfego para a rede virtual remota|   Permitir (padrão)      |
   |Tráfego encaminhado da rede virtual remota    |   Permitir (padrão)      |
   |Gateway de rede virtual ou servidor de rota    |  Usar este gateway da rede virtual       |
    
5. Em **Rede virtual remota**:

   |Nome da configuração  |Valor  |
   |---------|---------|
   |Nome do link de emparelhamento | SpoketoHub|
   |Modelo de implantação de rede virtual| Gerenciador de Recursos|
   |Assinatura|\<your subscription\>|
   |Rede virtual| VNet-Spoke
   |Tráfego para a rede virtual remota     |   Permitir (padrão)      |
   |Tráfego encaminhado da rede virtual remota    |   Permitir (padrão)      |
   |Gateway de rede virtual     |  Usar o gateway da rede virtual remota       |

5. Selecione **Adicionar**.

   :::image type="content" source="media/secure-hybrid-network/firewall-peering.png" alt-text="Emparelhamento VNET":::

## <a name="create-the-routes"></a>Criar as rotas

Em seguida, crie duas rotas:

- Uma rota da sub-rede do gateway do hub até a sub-rede do spoke pelo endereço IP de firewall
- Uma rota padrão da sub-rede do spoke pelo endereço IP de firewall

1. Na página inicial do portal do Azure, selecione **Criar um recurso**.
2. Na caixa de texto de pesquisa, digite **tabela de rotas** e pressione **Enter**.
3. Selecione **Tabela de rotas**.
4. Selecione **Criar**.
1. Selecione o **FW-Hybrid-Test** para o grupo de recursos.
1. Em **Região**, selecione **Leste dos EUA**.
1. Para o nome, digite **UDR-Hub-Spoke**.
1. Selecione **Examinar + criar**.
1. Selecione **Criar**.
1. Depois que a tabela de rotas for criada, selecione-a para abrir a página da tabela de rotas.
1. Selecione **Rotas** na coluna à esquerda.
1. Selecione **Adicionar**.
1. Para o nome da rota, digite **ToSpoke**.
1. Para o prefixo de endereço, digite **10.6.0.0/16**.
1. Para o tipo do próximo salto, selecione **Solução de virtualização**.
1. Para o endereço do próximo salto, digite o endereço IP privado do firewall anotado anteriormente.
1. Selecione **OK**.

Agora associe a rota à sub-rede.

1. Na página **UDR-Hub-Spoke – Rotas**, selecione **Sub-redes**.
2. Selecione **Associar**.
4. Em **Rede virtual**, selecione **VNet-hub**.
5. Em **Sub-rede**, selecione **GatewaySubnet**.
6. Selecione **OK**.

Agora crie a rota padrão com base na sub-rede spoke.

1. Na página inicial do portal do Azure, selecione **Criar um recurso**.
2. Na caixa de texto de pesquisa, digite **tabela de rotas** e pressione **Enter**.
3. Selecione **Tabela de rotas**.
5. Selecione **Criar**.
7. Selecione o **FW-Hybrid-Test** para o grupo de recursos.
8. Em **Região**, selecione **Leste dos EUA**.
1. Para o nome, digite **UDR-DG**.
4. Em **Propagar rotas do gateway**, selecione **Não**.
1. Selecione **Examinar + criar**.
1. Selecione **Criar**.
1. Depois que a tabela de rotas for criada, selecione-a para abrir a página da tabela de rotas.
1. Selecione **Rotas** na coluna à esquerda.
1. Selecione **Adicionar**.
1. Para o nome da rota, digite **ToHub**.
1. Para o prefixo de endereço, digite **0.0.0.0/0**.
1. Para o tipo do próximo salto, selecione **Solução de virtualização**.
1. Para o endereço do próximo salto, digite o endereço IP privado do firewall anotado anteriormente.
1. Selecione **OK**.

Agora associe a rota à sub-rede.

1. Na página **UDR-DG – Rotas**, selecione **Sub-redes**.
2. Selecione **Associar**.
4. Em **Rede virtual**, selecione **VNet-spoke**.
5. Em **Sub-rede**, escolha **SN-Workload**.
6. Selecione **OK**.

## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Agora crie a carga de trabalho do spoke e as máquinas virtuais locais e coloque-as nas sub-redes apropriadas.

### <a name="create-the-workload-virtual-machine"></a>Criar a máquina virtual de carga de trabalho

Crie uma máquina virtual na rede virtual spoke, que executa o IIS, sem nenhum endereço IP público.

1. Na página inicial do portal do Azure, selecione **Criar um recurso**.
2. Em **Popular**, selecione **Windows Server 2016 Datacenter**.
3. Insira esses valores para a máquina virtual:
    - **Grupo de recursos** – selecione **FW-Hybrid-Test**
    - **Nome da máquina virtual** – *VM-Spoke-01*
    - **Região** - *Leste dos EUA*
    - **Nome de usuário** – digite um nome de usuário
    - **Senha** – digite uma senha

4. Selecione **Avançar:Discos**.
5. Aceite os padrões e selecione **Avançar: Rede**.
6. Selecione **VNet-Spoke** para a rede virtual; a sub-rede é **SN-Workload**.
8. Em **Portas de entrada públicas**, selecione **Permitir portas selecionadas** e, em seguida, selecione **HTTP (80)** e **RDP (3389)**
1. Selecione **Avançar: Gerenciamento**.
1. Em **Diagnóstico de inicialização**, selecione **Desabilitar**.
1. Selecione **Examinar + Criar**, examine as configurações na página de resumo e selecione **Criar**.

### <a name="install-iis"></a>Instalar o IIS

1. No portal do Azure, abra o Cloud Shell e verifique se ele está definido como **PowerShell**.
2. Execute o comando a seguir para instalar o IIS na máquina virtual e altere a localização se necessário:

   ```azurepowershell-interactive
   Set-AzVMExtension `
           -ResourceGroupName FW-Hybrid-Test `
           -ExtensionName IIS `
           -VMName VM-Spoke-01 `
           -Publisher Microsoft.Compute `
           -ExtensionType CustomScriptExtension `
           -TypeHandlerVersion 1.4 `
           -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell      Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
           -Location EastUS
   ```

### <a name="create-the-on-premises-virtual-machine"></a>Criar a máquina virtual local

Trata-se de uma máquina virtual que pode ser usada para se conectar usando a Área de Trabalho Remota ao endereço IP público. A partir daí, você conecta o servidor local por meio do firewall.

1. Na página inicial do portal do Azure, selecione **Criar um recurso**.
2. Em **Popular**, selecione **Windows Server 2016 Datacenter**.
3. Insira esses valores para a máquina virtual:
    - **Grupo de recursos** – selecione existente e escolha **FW-Hybrid-Test**
    - **Nome da máquina virtual** - *VM-Onprem*
    - **Região** - *Leste dos EUA*
    - **Nome de usuário** – digite um nome de usuário
    - **Senha**: digite sua senha

4. Selecione **Avançar:Discos**.
5. Aceite os padrões e selecione **Avançar: Rede**.
6. Selecione **VNet-Onprem** para a rede virtual e verifique se a sub-rede é **SN-Corp**.
7. Em **Portas de entrada públicas**, selecione **Permitir portas selecionadas** e, em seguida, **RDP (3389)**
8. Selecione **Avançar: Gerenciamento**.
9. Em **Diagnóstico de inicialização**, selecione **Desabilitar**.
10. Selecione **Examinar + Criar**, examine as configurações na página de resumo e selecione **Criar**.

## <a name="test-the-firewall"></a>Testar o firewall

1. Primeiro, anote o endereço IP privado da máquina virtual VM-Spoke-01 na página de visão geral dessa VM.

2. No portal do Azure, conecte a máquina virtual **VM-Onprem**.
<!---2. Open a Windows PowerShell command prompt on **VM-Onprem**, and ping the private IP for **VM-spoke-01**.

   You should get a reply.--->
3. Abra um navegador da Web em **VM-Onprem** e navegue até http://\<VM-spoke-01 private IP\>.

   Você deverá ver a página da Web **VM-spoke-01**: ![Página da Web de VM-Spoke-01](media/secure-hybrid-network/vm-spoke-01-web.png)

4. Na máquina virtual **VM-Onprem**, abra uma Área de Trabalho Remota para **VM-spoke-01** no endereço IP privado.

   Sua conexão deverá ter sucesso e você deverá conseguir entrar.

Agora que você verificou se as regras de firewall estão funcionando:

<!---- You can ping the server on the spoke VNet.--->
- Você pode procurar o servidor Web na rede virtual spoke.
- Você pode conectar o servidor na rede virtual spoke usando o RDP.

Em seguida, altere a ação de coleção de regras da rede do firewall para **Deny** a fim de verificar se as regras do firewall funciona como o esperado.

1. Abra o grupo de recursos **FW-Hybrid-Test** e selecione a política de firewall **Pol-Net01**.
2. Em **Configurações**, selecione **Coleções de Regras**.
1. Escolha a coleção de regras **RCNet01**.
1. Para **Ação de coleção de regras**, selecione **Negar**.
1. Clique em **Salvar**.

Feche as áreas de trabalho remotas e navegadores existentes em **VM-Onprem** antes de testar as regras alteradas. Depois que a atualização da coleção de regras for concluída, execute os testes novamente. Todos devem apresentar falha na conexão nesse momento.

## <a name="clean-up-resources"></a>Limpar os recursos

Você pode manter seus recursos de firewall para investigação adicional ou, se não forem mais necessários, excluir o grupo de recursos **FW-Hybrid-Test** para excluir todos os recursos relacionados ao firewall.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial: Proteger sua WAN Virtual usando o Gerenciador de Firewall do Azure](secure-cloud-network.md)