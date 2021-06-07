---
title: 'Tutorial: Implantar e configurar o Firewall do Azure em uma rede híbrida usando o portal do Azure'
description: Neste tutorial, você aprenderá a implantar e configurar o Firewall do Azure usando o portal do Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 11/17/2020
ms.author: victorh
customer intent: As an administrator, I want to control network access from an on-premises network to an Azure virtual network.
ms.openlocfilehash: 86e27c190b269763d8dd2f562a207b3f2020da29
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98051064"
---
# <a name="tutorial-deploy-and-configure-azure-firewall-in-a-hybrid-network-using-the-azure-portal"></a>Tutorial: Implantar e configurar o Firewall do Azure em uma rede híbrida usando o portal do Azure

Ao conectar sua rede local a uma rede virtual do Azure para criar uma rede híbrida, a capacidade de controlar o acesso aos recursos da rede do Azure é uma parte importante de um plano geral de segurança.

É possível usar o Firewall do Azure para controlar o acesso de rede em uma rede híbrida usando as regras que definem tráfegos de rede permitidos e negados.

Para este tutorial, você deve criar três redes virtuais:

- **VNet-Hub**: o firewall está nessa rede virtual.
- **VNet-Spoke**: a rede virtual spoke representa a carga de trabalho localizada no Azure.
- **VNet-Onprem**: a rede virtual local representa uma rede local. Em uma implantação real, pode ser conectada por uma conexão VPN ou ExpressRoute. Para simplificar, este tutorial usa uma conexão de gateway de VPN, e uma rede virtual localizada no Azure é usada para representar uma rede local.

![Firewall em uma rede híbrida](media/tutorial-hybrid-ps/hybrid-network-firewall.png)

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Declarar as variáveis
> * Criar a rede virtual do hub de firewall
> * Criar a rede virtual spoke
> * Criar a rede virtual local
> * Configurar e implantar o firewall
> * Criar e conectar os gateways de VPN
> * Emparelhar as redes virtuais hub e spoke
> * Criar as rotas
> * Criar as máquinas virtuais
> * Testar o firewall

Caso deseje usar o Azure PowerShell para concluir este procedimento, confira [Implantar e configurar o Firewall do Azure em uma rede híbrida usando o Azure PowerShell](tutorial-hybrid-ps.md).

## <a name="prerequisites"></a>Pré-requisitos

Uma rede híbrida usa o modelo de arquitetura hub e spoke para rotear o tráfego entre as VNets do Azure e as redes locais. A arquitetura hub e spoke tem os seguintes requisitos:

- Defina **AllowGatewayTransit** ao emparelhar o VNet-Hub com o VNet-Spoke. Em uma arquitetura de rede hub e spoke, um trânsito de gateway permite que redes virtuais spoke compartilhem o gateway de VPN no hub, em vez de implantar gateways de VPN em cada rede virtual spoke. 

   Além disso, as rotas para as redes virtuais conectadas pelo gateway ou das redes locais serão propagadas automaticamente às tabelas de roteiros para as redes virtuais emparelhadas usando o trânsito de gateway. Para saber mais, confira [Configurar o trânsito de gateway de VPN para o emparelhamento de rede virtual](../vpn-gateway/vpn-gateway-peering-gateway-transit.md).

- Defina **UseRemoteGateways** quando você emparelhar VNet-Spoke com VNet-Hub. Se **UseRemoteGateways** estiver definido e **AllowGatewayTransit** no emparelhamento remoto também estiver definido, a rede virtual spoke usará gateways da rede virtual remota para trânsito.
- Para rotear o tráfego da sub-rede spoke por meio do firewall do hub, você pode usar uma UDR (Rota Definida pelo Usuário) que aponte para o firewall com a opção **Propagação de rotas de gateway de rede virtual** desabilitada. A opção **Propagação de rotas de gateway de rede virtual** desabilitada impede a distribuição de rota para as sub-redes spoke. Isso impede que as rotas aprendidas entrem em conflito com sua UDR. Se quiser manter a **propagação de rota de gateway de rede virtual** habilitada, defina rotas específicas para o firewall para substituir aquelas publicadas do local sobre o BGP.
- Configure uma UDR na sub-rede do gateway do hub que aponta para o endereço IP do firewall como o próximo salto para as redes spoke. Nenhuma UDR é necessária na sub-rede do Firewall do Azure, já que ela aprende as rotas com o BGP.

Consulte a seção [Criar Rotas](#create-the-routes) deste tutorial para ver como essas rotas são criadas.

>[!NOTE]
>O Firewall do Azure deve ter conectividade direta com a Internet. Se seu AzureFirewallSubnet aprender uma rota padrão para sua rede local via BGP, você deve substituir isso por um UDR 0.0.0.0/0 com o valor **NextHopType** definido como **Internet** para manter a conectividade direta com a Internet.
>
>O Firewall do Azure pode ser configurado para dar suporte a túnel forçado. Para obter mais informações, confira [Túnel forçado do Firewall do Azure](forced-tunneling.md).

>[!NOTE]
>O tráfego entre VNETs diretamente emparelhadas é roteado diretamente, mesmo se uma UDR aponta para o Firewall do Azure como o gateway padrão. Para enviar o tráfego de sub-rede para sub-rede para o firewall nesse cenário, uma UDR precisa conter o prefixo de rede da sub-rede de destino explicitamente em ambas as sub-redes.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-the-firewall-hub-virtual-network"></a>Criar a rede virtual do hub de firewall

Primeiro, crie um grupo de recursos para conter os recursos deste tutorial:

1. Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. Na página inicial do portal do Azure, selecione **Grupos de recursos** > **Adicionar**.
3. Em **Assinatura**, selecione sua assinatura.
1. Em **Nome do grupo de recursos**, digite **FW-Hybrid-Test**.
2. Em **Região**, selecione **EUA (Leste dos EUA)** . Todos os recursos criados posteriormente precisam estar na mesma localização.
3. Selecione **Examinar + criar**.
4. Selecione **Criar**.

Agora crie a VNET:

> [!NOTE]
> O tamanho da sub-rede AzureFirewallSubnet é /26. Para obter mais informações sobre o tamanho da sub-rede, confira [Perguntas frequentes sobre o Firewall do Azure](firewall-faq.yml#why-does-azure-firewall-need-a--26-subnet-size).

1. Na página inicial do portal do Azure, selecione **Criar um recurso**.
2. Em **Rede**, selecione **Rede virtual**.
1. Selecione **Criar**.
1. Em **Grupo de recursos**, selecione **FW-Hybrid-Test**.
1. Em **Nome**, digite **VNet-hub**.
1. Selecione **Avançar: Endereços IP**.
1. Em **Espaço de endereço IPv4**, exclua o endereço padrão e digite **10.5.0.0/16**.
1. Em **Nome da sub-rede**, selecione **Adicionar sub-rede**.
1. Em **Nome da sub-rede**, digite **AzureFirewallSubnet**. O firewall estará nessa sub-rede e o nome da sub-rede **precisa** ser AzureFirewallSubnet.
1. Em **Intervalo de endereços da sub-rede**, digite **10.5.0.0/26**. 
1. Selecione **Adicionar**.
1. Selecione **Examinar + criar**.
1. Selecione **Criar**.

## <a name="create-the-spoke-virtual-network"></a>Criar a rede virtual spoke

1. Na página inicial do portal do Azure, selecione **Criar um recurso**.
2. Em **Rede**, selecione **Rede virtual**.
7. Em **Grupo de recursos**, selecione **FW-Hybrid-Test**.
1. Em **Nome**, digite **VNet-Spoke**.
2. Em **Região**, selecione **EUA (Leste dos EUA)** .
3. Selecione **Avançar: Endereços IP**.
4. Em **Espaço de endereço IPv4**, exclua o endereço padrão e digite **10.6.0.0/16**.
6. Em **Nome da sub-rede**, selecione **Adicionar sub-rede**.
7. Em **Nome da sub-rede**, digite **SN-Workload**.
8. Em **Intervalo de endereços da sub-rede**, digite **10.6.0.0/24**. 
9. Selecione **Adicionar**.
10. Selecione **Examinar + criar**.
11. Selecione **Criar**.

## <a name="create-the-on-premises-virtual-network"></a>Criar a rede virtual local

1. Na página inicial do portal do Azure, selecione **Criar um recurso**.
2. Em **Rede**, selecione **Rede virtual**.
7. Em **Grupo de recursos**, selecione **FW-Hybrid-Test**.
1. Em **Nome**, digite **VNet-OnPrem**.
2. Em **Região**, selecione **EUA (Leste dos EUA)** .
3. Selecione **Avançar: Endereços IP**
4. Em **Espaço de endereço IPv4**, exclua o endereço padrão e digite **192.168.0.0/16**.
5. Em **Nome da sub-rede**, selecione **Adicionar sub-rede**.
7. Em **Nome da sub-rede**, digite **SN-Corp**.
8. Em **Intervalo de endereços da sub-rede**, digite **192.168.1.0/24**. 
9. Selecione **Adicionar**.
10. Selecione **Examinar + criar**.
11. Selecione **Criar**.

Agora crie uma segunda sub-rede para o gateway.

1. Na página **VNet-Onprem**, selecione **Sub-redes**.
2. Selecione **+Sub-rede**.
3. Em **Nome**, digite **GatewaySubnet**.
4. Em **Intervalo de endereços da sub-rede**, digite **192.168.2.0/24**.
5. Selecione **OK**.

## <a name="configure-and-deploy-the-firewall"></a>Configurar e implantar o firewall

Agora implante o firewall na rede virtual do hub de firewall.

1. Na página inicial do portal do Azure, selecione **Criar um recurso**.
2. Na coluna à esquerda, selecione **Rede** e procure e escolha **Firewall**.
4. Na página **Criar um Firewall**, use a tabela abaixo para configurar o firewall:

   |Configuração  |Valor  |
   |---------|---------|
   |Subscription     |\<your subscription\>|
   |Resource group     |**FW-Hybrid-Test** |
   |Nome     |**AzFW01**|
   |Região     |**Leste dos EUA**|
   |Escolher uma rede virtual     |**Usar existente**:<br> **VNet-hub**|
   |Endereço IP público     |Adicionar nova: <br>**fw-pip**. |

5. Selecione **Examinar + criar**.
6. Examine o resumo e selecione **Criar** para criar o firewall.

   Isso leva alguns minutos para ser implantado.
7. Após a conclusão da implantação, acesse o grupo de recursos **FW-Hybrid-Test** e selecione o firewall **AzFW01**.
8. Anote o endereço IP privado. Você o usará mais tarde quando criar a rota padrão.

### <a name="configure-network-rules"></a>Configurar regras de rede

Primeiro, adicione uma regra de rede para permitir o tráfego da Web.

1. Na página **AzFW01**, selecione **Regras**.
2. Selecione a guia **Coleção de regras de rede**.
3. Selecione **Adicionar coleção de regras de rede**.
4. Em **Nome**, digite **RCNet01**.
5. Em **Prioridade**, digite **100**.
6. Em **Ação**, selecione **Permitir**.
6. Em **Regras**, em **Nome**, digite **AllowWeb**.
7. Em **Protocolo**, selecione **TCP**.
8. Em **Tipo de origem**, selecione **Endereço IP**.
9. Em **Origem**, digite **192.168.1.0/24**.
10. Em **Tipo de destino**, selecione **Endereço IP**.
11. Em **Endereço de destino**, digite **10.6.0.0/16**
12. Em **Portas de Destino**, digite **80**.

Agora adicione uma regra para permitir o tráfego RDP.

Na segunda linha de regra, digite as seguintes informações:

1. **Nome**, digite **AllowRDP**.
2. Em **Protocolo**, selecione **TCP**.
3. Em **Tipo de origem**, selecione **Endereço IP**.
4. Em **Origem**, digite **192.168.1.0/24**.
5. Em **Tipo de destino**, selecione **Endereço IP**.
6. Em **Endereço de destino**, digite **10.6.0.0/16**
7. Em **Portas de Destino**, digite **3389**.
8. Selecione **Adicionar**.

## <a name="create-and-connect-the-vpn-gateways"></a>Criar e conectar os gateways de VPN

As redes virtuais locais e de hub são conectadas por meio de gateways VPN.

### <a name="create-a-vpn-gateway-for-the-hub-virtual-network"></a>Criar um gateway de VPN para a rede virtual do hub

Agora crie o gateway de VPN para a rede virtual do hub. As configurações de rede a rede exigem um RouteBased VpnType. A criação de um gateway de VPN pode levar 45 minutos ou mais, dependendo da SKU do gateway de VPN selecionado.

1. Na página inicial do portal do Azure, selecione **Criar um recurso**.
2. Na caixa de texto de pesquisa, digite **gateway de rede virtual**.
3. Selecione **Gateway de rede virtual** e selecione **Criar**.
4. Em **Nome**, digite **GW-hub**.
5. Em **Região**, selecione a mesma região usada anteriormente.
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
5. Em **Região**, selecione a mesma região usada anteriormente.
6. Em **Tipo de gateway**, selecione **VPN**.
7. Em **Tipo de VPN**, selecione **Baseada em rota**.
8. Em **SKU**, selecione **Básico**.
9. Em **Rede virtual**, selecione **VNet-Onprem**.
10. Em **Endereço IP público**, selecione **Criar** e digite **VNet-Onprem-GW-pip** para o nome.
11. Aceite os padrões restantes e, em seguida, selecione **Examinar + criar**.
12. Examine a configuração e, em seguida, selecione **Criar**.

### <a name="create-the-vpn-connections"></a>Criar conexões de VPN

Agora você pode criar as conexões VPN entre o hub e os gateways locais.

Nesta etapa, você criará a conexão da rede virtual do hub à rede virtual local. Você verá uma chave compartilhada referenciada nos exemplos. Você pode usar seus próprios valores para a chave compartilhada. O importante é que a chave compartilhada deve corresponder em ambas as conexões. Criar uma conexão pode levar alguns minutos para ser concluída.

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

![Conexões de gateway](media/tutorial-hybrid-portal/gateway-connections.png)

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
   |Gateway de rede virtual     |  Usar este gateway da rede virtual       |
    
5. Em **Rede virtual remota**:

   |Nome da configuração  |Valor  |
   |---------|---------|
   |Nome do link de emparelhamento | SpoketoHub|
   |Modelo de implantação de rede virtual| Gerenciador de recursos|
   |Assinatura|\<your subscription\>|
   |Rede virtual| VNet-Spoke
   |Tráfego para a rede virtual remota     |   Permitir (padrão)      |
   |Tráfego encaminhado da rede virtual remota    |   Permitir (padrão)      |
   |Gateway de rede virtual     |  Usar o gateway da rede virtual remota       |

5. Selecione **Adicionar**.

   :::image type="content" source="media/tutorial-hybrid-portal/firewall-peering.png" alt-text="Emparelhamento VNET":::

## <a name="create-the-routes"></a>Criar as rotas

Em seguida, crie duas rotas:

- Uma rota da sub-rede do gateway do hub até a sub-rede do spoke pelo endereço IP de firewall
- Uma rota padrão da sub-rede do spoke pelo endereço IP de firewall

1. Na página inicial do portal do Azure, selecione **Criar um recurso**.
2. Na caixa de texto de pesquisa, digite **tabela de rotas** e pressione **Enter**.
3. Selecione **Tabela de rotas**.
4. Selecione **Criar**.
6. Selecione o **FW-Hybrid-Test** para o grupo de recursos.
8. Em **Região**, selecione a mesma localização usada anteriormente.
1. Para o nome, digite **UDR-Hub-Spoke**.
9. Selecione **Examinar + criar**.
10. Selecione **Criar**.
11. Depois que a tabela de rotas for criada, selecione-a para abrir a página da tabela de rotas.
12. Selecione **Rotas** na coluna à esquerda.
13. Selecione **Adicionar**.
14. Para o nome da rota, digite **ToSpoke**.
15. Para o prefixo de endereço, digite **10.6.0.0/16**.
16. Para o tipo do próximo salto, selecione **Solução de virtualização**.
17. Para o endereço do próximo salto, digite o endereço IP privado do firewall anotado anteriormente.
18. Selecione **OK**.

Agora associe a rota à sub-rede.

1. Na página **UDR-Hub-Spoke – Rotas**, selecione **Sub-redes**.
2. Selecione **Associar**.
3. Em **Rede virtual**, selecione **VNet-hub**.
1. Em **Sub-rede**, selecione **GatewaySubnet**.
2. Selecione **OK**.

Agora crie a rota padrão com base na sub-rede spoke.

1. Na página inicial do portal do Azure, selecione **Criar um recurso**.
2. Na caixa de texto de pesquisa, digite **tabela de rotas** e pressione **Enter**.
3. Selecione **Tabela de rotas**.
5. Selecione **Criar**.
7. Selecione o **FW-Hybrid-Test** para o grupo de recursos.
8. Em **Região**, selecione a mesma localização usada anteriormente.
1. Para o nome, digite **UDR-DG**.
4. Em **Propagar rota do gateway**, selecione **Não**.
5. Selecione **Examinar + criar**.
6. Selecione **Criar**.
7. Depois que a tabela de rotas for criada, selecione-a para abrir a página da tabela de rotas.
8. Selecione **Rotas** na coluna à esquerda.
9. Selecione **Adicionar**.
10. Para o nome da rota, digite **ToHub**.
11. Para o prefixo de endereço, digite **0.0.0.0/0**.
12. Para o tipo do próximo salto, selecione **Solução de virtualização**.
13. Para o endereço do próximo salto, digite o endereço IP privado do firewall anotado anteriormente.
14. Selecione **OK**.

Agora associe a rota à sub-rede.

1. Na página **UDR-DG – Rotas**, selecione **Sub-redes**.
2. Selecione **Associar**.
3. Em **Rede virtual**, selecione **VNet-spoke**.
1. Em **Sub-rede**, escolha **SN-Workload**.
2. Selecione **OK**.

## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Agora crie a carga de trabalho do spoke e as máquinas virtuais locais e coloque-as nas sub-redes apropriadas.

### <a name="create-the-workload-virtual-machine"></a>Criar a máquina virtual de carga de trabalho

Crie uma máquina virtual na rede virtual spoke, que executa o IIS, sem nenhum endereço IP público.

1. Na página inicial do portal do Azure, selecione **Criar um recurso**.
2. Em **Popular**, selecione **Windows Server 2016 Datacenter**.
3. Insira esses valores para a máquina virtual:
    - **Grupo de recursos** – selecione **FW-Hybrid-Test**.
    - **Nome da máquina virtual**: *VM-Spoke-01*.
    - **Região** – a mesma região usada anteriormente.
    - **Nome de usuário**: \<type a user name\>.
    - **Senha**: \<type a password\>
4. Em **Portas de entrada públicas**, selecione **Permitir portas selecionadas** e, em seguida, selecione **HTTP (80)** e **RDP (3389)**
4. Selecione **Avançar:Discos**.
5. Aceite os padrões e selecione **Avançar: Rede**.
6. Selecione **VNet-Spoke** para a rede virtual; a sub-rede é **SN-Workload**.
7. Em **IP Público**, selecione **Nenhum**. 
9. Selecione **Avançar: Gerenciamento**.
10. Em **Diagnóstico de inicialização**, selecione **Desabilitar**.
11. Selecione **Examinar+Criar**, examine as configurações na página de resumo e, em seguida, selecione **Criar**.

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
    - **Grupo de recursos** – selecione existente e, em seguida, selecione **FW-Hybrid-Test**.
    - **Nome da máquina virtual** - *VM-Onprem*.
    - **Região** – a mesma região usada anteriormente.
    - **Nome de usuário**: \<type a user name\>.
    - **Senha**: \<type a user password\>.
7. Em **Portas de entrada públicas**, selecione **Permitir portas selecionadas** e, em seguida, **RDP (3389)**
4. Selecione **Avançar:Discos**.
5. Aceite os padrões e selecione **Avançar: Rede**.
6. Selecione **VNet-Onprem** para a rede virtual; a sub-rede é **SN-Corp**.
8. Selecione **Avançar: Gerenciamento**.
10. Em **Diagnóstico de inicialização**, selecione **Desabilitar**.
10. Selecione **Examinar+Criar**, examine as configurações na página de resumo e, em seguida, selecione **Criar**.

## <a name="test-the-firewall"></a>Testar o firewall

1. Primeiro, anote o endereço IP privado da máquina virtual **VM-spoke-01**.

2. No portal do Azure, conecte a máquina virtual **VM-Onprem**.
<!---2. Open a Windows PowerShell command prompt on **VM-Onprem**, and ping the private IP for **VM-spoke-01**.

   You should get a reply.--->
3. Abra um navegador da Web em **VM-Onprem** e navegue até http://\<VM-spoke-01 private IP\>.

   Você deverá ver a página da Web **VM-spoke-01**: ![Página da Web de VM-Spoke-01](media/tutorial-hybrid-portal/VM-Spoke-01-web.png)

4. Na máquina virtual **VM-Onprem**, abra uma Área de Trabalho Remota para **VM-spoke-01** no endereço IP privado.

   Sua conexão deverá ter sucesso e você deverá conseguir entrar.

Agora que você verificou se as regras de firewall estão funcionando:

<!---- You can ping the server on the spoke VNet.--->
- Você pode procurar o servidor Web na rede virtual spoke.
- Você pode conectar o servidor na rede virtual spoke usando o RDP.

Em seguida, altere a ação de coleção de regras da rede do firewall para **Deny** a fim de verificar se as regras do firewall funciona como o esperado.

1. Selecione o firewall **AzFW01**.
2. Selecione **Regras**.
3. Selecione a guia **Coleção de regras de rede** e selecione a coleção de regras **RCNet01**.
4. Em **Ação**, selecione **Negar**.
5. Clique em **Salvar**.

Feche as áreas de trabalho remotas existentes antes de testar a alteração das regras. Agora execute os testes novamente. Todos devem falhar nesse momento.

## <a name="clean-up-resources"></a>Limpar os recursos

Você pode manter seus recursos de firewall para o próximo tutorial ou se não forem mais necessários, exclua o grupo de recursos **FW-Hybrid-Test** para excluir todos os recursos relacionados ao firewall.

## <a name="next-steps"></a>Próximas etapas

Em seguida,você pode monitorar os logs do Firewall do Azure.

> [!div class="nextstepaction"]
> [Tutorial: Monitorar os logs do Firewall do Azure](./firewall-diagnostics.md)