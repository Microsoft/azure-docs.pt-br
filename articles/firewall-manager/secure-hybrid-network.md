---
title: 'Tutorial: Proteger sua rede virtual de hub usando o Gerenciador de Firewall do Azure'
description: Neste tutorial, você aprenderá a proteger sua rede virtual com o Gerenciador de Firewall do Azure usando o portal do Azure.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: tutorial
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: 5d662efc927235711c4dd5e8a424d8775279c30b
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98048038"
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
4. Selecione **Próximo: Regras**.
5. Selecione **Adicionar uma coleção de regras**.
6. Em **Nome**, digite **RCNet01**.
7. Para **Tipo de coleção de regras**, selecione **Rede**.
8. Em **Prioridade**, digite **100**.
9. Em **Ação**, selecione **Permitir**.
10. Em **Regras**, em **Nome**, digite **AllowWeb**.
11. Em **Endereços de Origem**, digite **192.168.1.0/24**.
12. Em **Protocolo**, selecione **TCP**.
13. Em **Portas de Destino**, digite **80**.
14. Para **Tipo de Destino**, selecione **Endereço IP**.
15. Para **Destino**, digite **10.6.0.0/16**.
16. Na próxima linha de regra, insira as seguintes informações:
 
    Nome: digite **AllowRDP**<br>
    Endereço IP de origem: digite **192.168.1.0/24**.<br>
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
2. Em **Rede**, selecione **Rede virtual**.
4. Em **Nome**, digite **VNet-hub**.
5. Em **Espaço de endereço**, digite **10.5.0.0/16**.
6. Em **Assinatura**, selecione sua assinatura.
7. Em **Grupo de recursos**, selecione **FW-Hybrid-Test**.
8. Em **Localização**, selecione **Leste dos EUA**.
9. Em **Sub-rede**, digite **AzureFirewallSubnet** em **Nome**. O firewall estará nessa sub-rede e o nome da sub-rede **precisa** ser AzureFirewallSubnet.
10. Em **Intervalo de endereços**, digite **10.5.0.0/26**. 
11. Aceite as outras configurações padrão e selecione **Criar**.

## <a name="create-the-spoke-virtual-network"></a>Criar a rede virtual spoke

1. Na página inicial do portal do Azure, selecione **Criar um recurso**.
2. Em **Rede**, selecione **Rede virtual**.
4. Em **Nome**, digite **VNet-Spoke**.
5. Em **Espaço de endereço**, digite **10.6.0.0/16**.
6. Em **Assinatura**, selecione sua assinatura.
7. Em **Grupo de recursos**, selecione **FW-Hybrid-Test**.
8. Em **local**, selecione o mesmo local usado anteriormente.
9. Em **Sub-rede**, em **Nome**, digite **SN-Workload**.
10. Em **Intervalo de endereços**, digite **10.6.0.0/24**.
11. Aceite as outras configurações padrão e selecione **Criar**.

## <a name="create-the-on-premises-virtual-network"></a>Criar a rede virtual local

1. Na página inicial do portal do Azure, selecione **Criar um recurso**.
2. Em **Rede**, selecione **Rede virtual**.
4. Em **Nome**, digite **VNet-OnPrem**.
5. Em **Espaço de endereço**, digite **192.168.0.0/16**.
6. Em **Assinatura**, selecione sua assinatura.
7. Em **Grupo de recursos**, selecione **FW-Hybrid-Test**.
8. Em **local**, selecione o mesmo local usado anteriormente.
9. Em **Sub-rede**, em **Nome**, digite **SN-Corp**.
10. Em **Intervalo de endereços**, digite **192.168.1.0/24**.
11. Aceite as outras configurações padrão e selecione **Criar**.

Depois que a rede virtual for implantada, crie uma segunda sub-rede para o gateway.

1. Na página **VNet-Onprem**, selecione **Sub-redes**.
2. Selecione **+Sub-rede**.
3. Em **Nome**, digite **GatewaySubnet**.
4. Em **Intervalo de endereços (bloco CIDR)** , digite **192.168.2.0/24**.
5. Selecione **OK**.

### <a name="create-a-public-ip-address"></a>Criar um endereço IP público

Este é o endereço IP público usado para o gateway local.

1. Na página inicial do portal do Azure, selecione **Criar um recurso**.
2. Na caixa de texto de pesquisa, digite **endereço IP público** e pressione **Enter**.
3. Selecione **Endereço IP público** e, em seguida, selecione **Criar**.
4. Para o nome, digite **VNet-Onprem-GW-pip**.
5. Para o grupo de recursos, digite **FW-Hybrid-Test**.
6. Em **Localização**, selecione **Leste dos EUA**.
7. Aceite os outros padrões e, em seguida, selecione **Criar**.

## <a name="configure-and-deploy-the-firewall"></a>Configurar e implantar o firewall

Quando as políticas de segurança são associadas a um hub, ele é chamado de *rede virtual de hub*.

Converta a rede virtual **VNet-Hub** em uma *rede virtual de hub* e proteja-a com o Firewall do Azure.

1. Na barra de pesquisa do portal do Azure, digite **Gerenciador de Firewall** e pressione **Enter**.
3. Na página do Gerenciador de Firewall do Azure, em **Adicionar segurança a redes virtuais**, selecione **Exibir redes virtuais de hub**.
4. Selecione **Converter redes virtuais**.
5. Selecione **VNet-hub** e, em seguida, selecione **Próximo: Firewall do Azure**.
6. Para a **Política de Firewall**, selecione **Pol-Net01**.
7. Selecione **Próximo "Examinar + confirmar**
8. Examine os detalhes e, em seguida, selecione **Confirmar**.


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
10. Em **Endereço IP público**, selecione **Usar existente* e selecione **VNet-Onprem-GW-pip** como o nome.
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

![Conexões de gateway](media/secure-hybrid-network/gateway-connections.png)

## <a name="peer-the-hub-and-spoke-virtual-networks"></a>Emparelhar as redes virtuais hub e spoke

Agora emparelhe as redes virtuais hub e spoke.

1. Abra o grupo de recursos **FW-Hybrid-Test** e selecione a rede virtual **VNet-hub**.
2. Na coluna à esquerda, selecione **Emparelhamentos**.
3. Selecione **Adicionar**.
4. Em **Nome**, digite **HubtoSpoke**.
5. Para a **Rede virtual**, selecione **VNet-spoke**
6. Para o nome do emparelhamento de VNetSpoke para VNet-hub, digite **SpoketoHub**.
7. Selecione **Permitir o trânsito de gateway**.
8. Selecione **OK**.

### <a name="configure-additional-settings-for-the-spoketohub-peering"></a>Definir as configurações adicionais para o emparelhamento SpoketoHub

Você precisará habilitar o **Permitir o tráfego encaminhado** no emparelhamento SpoketoHub.

1. Abra o grupo de recursos **FW-Hybrid-Test** e selecione a rede virtual **VNet-Spoke**.
2. Na coluna à esquerda, selecione **Emparelhamentos**.
3. Selecione o emparelhamento **SpoketoHub**.
4. Em **Permitir o tráfego encaminhado do VNet-hub para o VNet-Spoke**, selecione **Habilitado**.
5. Clique em **Salvar**.

## <a name="create-the-routes"></a>Criar as rotas

Em seguida, crie duas rotas:

- Uma rota da sub-rede do gateway do hub até a sub-rede do spoke pelo endereço IP de firewall
- Uma rota padrão da sub-rede do spoke pelo endereço IP de firewall

1. Na página inicial do portal do Azure, selecione **Criar um recurso**.
2. Na caixa de texto de pesquisa, digite **tabela de rotas** e pressione **Enter**.
3. Selecione **Tabela de rotas**.
4. Selecione **Criar**.
5. Para o nome, digite **UDR-Hub-Spoke**.
6. Selecione o **FW-Hybrid-Test** para o grupo de recursos.
8. Em **Localização**, selecione **(EUA) Leste dos EUA**.
9. Selecione **Criar**.
10. Depois que a tabela de rotas for criada, selecione-a para abrir a página da tabela de rotas.
11. Selecione **Rotas** na coluna à esquerda.
12. Selecione **Adicionar**.
13. Para o nome da rota, digite **ToSpoke**.
14. Para o prefixo de endereço, digite **10.6.0.0/16**.
15. Para o tipo do próximo salto, selecione **Solução de virtualização**.
16. Para o endereço do próximo salto, digite o endereço IP privado do firewall anotado anteriormente.
17. Selecione **OK**.

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
6. Para o nome, digite **UDR-DG**.
7. Selecione o **FW-Hybrid-Test** para o grupo de recursos.
8. Em **Localização**, selecione **(EUA) Leste dos EUA**.
4. Em **Propagação de rota de gateway de rede virtual**, selecione **Desabilitado**.
1. Selecione **Criar**.
2. Depois que a tabela de rotas for criada, selecione-a para abrir a página da tabela de rotas.
3. Selecione **Rotas** na coluna à esquerda.
4. Selecione **Adicionar**.
5. Para o nome da rota, digite **ToHub**.
6. Para o prefixo de endereço, digite **0.0.0.0/0**.
7. Para o tipo do próximo salto, selecione **Solução de virtualização**.
8. Para o endereço do próximo salto, digite o endereço IP privado do firewall anotado anteriormente.
9. Selecione **OK**.

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
    - **Grupo de recursos** – selecione **FW-Hybrid-Test**.
    - **Nome da máquina virtual**: *VM-Spoke-01*.
    - **Região** -  *(EUA) Leste dos EUA)* .
    - **Nome de usuário**: *azureuser*.
    - **Senha**: digite sua senha

4. Selecione **Avançar:Discos**.
5. Aceite os padrões e selecione **Avançar: Rede**.
6. Selecione **VNet-Spoke** para a rede virtual; a sub-rede é **SN-Workload**.
7. Em **IP Público**, selecione **Nenhum**.
8. Em **Portas de entrada públicas**, selecione **Permitir portas selecionadas** e, em seguida, selecione **HTTP (80)** e **RDP (3389)**
9. Selecione **Avançar: Gerenciamento**.
10. Em **Diagnóstico de inicialização**, selecione **Desativado**.
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
    - **Região** -  *(EUA) Leste dos EUA)* .
    - **Nome de usuário**: *azureuser*.
    - **Senha**: digite sua senha.

4. Selecione **Avançar:Discos**.
5. Aceite os padrões e selecione **Avançar: Rede**.
6. Selecione **VNet-Onprem** para a rede virtual e verifique se a sub-rede é **SN-Corp**.
7. Em **Portas de entrada públicas**, selecione **Permitir portas selecionadas** e, em seguida, **RDP (3389)**
8. Selecione **Avançar: Gerenciamento**.
9. Em **Diagnóstico de inicialização**, selecione **Desativado**.
10. Selecione **Examinar+Criar**, examine as configurações na página de resumo e, em seguida, selecione **Criar**.

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
2. Em **Configurações**, selecione **Regras**.
3. Em **Regras de rede**, selecione a coleção de regras **RCNet01**, selecione as reticências (...) e selecione **Editar**.
4. Para **Ação de coleção de regras**, selecione **Negar**.
5. Clique em **Salvar**.

Feche as áreas de trabalho remotas e navegadores existentes em **VM-Onprem** antes de testar as regras alteradas. Depois que a atualização da coleção de regras for concluída, execute os testes novamente. Todos devem falhar nesse momento.

## <a name="clean-up-resources"></a>Limpar os recursos

Você pode manter seus recursos de firewall para o próximo tutorial ou se não forem mais necessários, exclua o grupo de recursos **FW-Hybrid-Test** para excluir todos os recursos relacionados ao firewall.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial: Proteger sua WAN Virtual usando o Gerenciador de Firewall do Azure](secure-cloud-network.md)