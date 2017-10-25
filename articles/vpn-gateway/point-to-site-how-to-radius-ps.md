---
title: "Conectar um computador a uma rede virtual do Azure usando Ponto a Site e autenticação RADIUS: PowerShell | Azure"
description: "Conectar com segurança um computador à Rede Virtual do Azure criando uma conexão de gateway de VPN Ponto a Site que utiliza autenticação RADIUS."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/13/2017
ms.author: cherylmc
ms.translationtype: HT
ms.sourcegitcommit: 44e9d992de3126bf989e69e39c343de50d592792
ms.openlocfilehash: 9ca423e8d752271fadbb5b51f38e691a0316576c
ms.contentlocale: pt-br
ms.lasthandoff: 09/25/2017

---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-radius-authentication-powershell-preview"></a>Configurar uma conexão Ponto a Site a uma VNet usando a autenticação RADIUS: PowerShell (Versão Prévia)

Este artigo mostra como criar uma rede virtual com uma conexão Ponto a Site que usa a autenticação RADIUS. Essa configuração está disponível somente para o modelo de implantação do Gerenciador de Recursos.

>[!NOTE]
>Atualmente, a autenticação RADIUS P2S está em Versão Prévia.
>

Um gateway VPN Ponto a Site (P2S) permite que você crie uma conexão segura para sua rede virtual a partir de um computador cliente individual. As conexões VPN Ponto a Site são úteis quando você deseja se conectar à rede virtual de um local remoto, como ao trabalhar de casa ou em uma conferência. Uma VPN P2S também é uma solução útil para usar em vez de uma VPN Site a Site, quando você tiver apenas alguns clientes que precisam se conectar a uma rede virtual.

Uma conexão VPN P2S é iniciada em dispositivos Windows e Mac. Os clientes que se conectam podem usar os seguintes métodos de autenticação:

* Servidor RADIUS
* Autenticação de certificado nativo de Gateway de VPN

Este artigo o ajudará a configurar uma configuração de P2S com autenticação usando o servidor RADIUS. Se você deseja autenticar usando certificados gerados e autenticação de certificado nativo de gateway de VPN, confira [Configurar uma conexão Ponto a Site para uma rede virtual usando a autenticação de certificado nativo de gateway de VPN](vpn-gateway-howto-point-to-site-rm-ps.md).

![Diagrama de conexão - RADIUS](./media/point-to-site-how-to-radius-ps/p2sradius.png)

As conexões Ponto a Site não exigem um dispositivo VPN ou um endereço IP voltado para o público. A P2S cria a conexão VPN no SSTP (Secure Socket Tunneling Protocol) ou IKEv2.

* O SSTP é um túnel de VPN baseado em SSL que tem suporte apenas em plataformas de cliente do Windows. Ele pode entrar em firewalls, tornando-o uma opção ideal para se conectar ao Azure de qualquer lugar. No lado do servidor, há suporte para as versões 1.0, 1.1 e 1.2 do SSTP. O cliente decide qual versão usar. Por padrão, para Windows 8.1 e posterior, o SSTP usa 1.2.

* VPN IKEv2, uma solução de VPN IPsec baseada em padrões. VPN IKEv2 pode ser usada para se conectar de dispositivos Mac (OSX versões 10.11 e acima).

Conexões P2S exigem o seguinte:

* Gateway de VPN RouteBased. 
* Servidor RADIUS para lidar com a autenticação do usuário. O servidor RADIUS pode ser implantado localmente ou na rede virtual do Azure.
* Um pacote de configuração de cliente VPN para os dispositivos Windows que irão se conectar à rede virtual. Um pacote de configuração de cliente VPN fornece as configurações necessárias para um cliente VPN conectar-se ao P2S.

## <a name="aboutad"></a>Sobre a autenticação de Domínio do Active Directory (AD) para as VPNs de P2S

A autenticação de Domínio do AD permite que os usuários façam logon Azure usando suas credenciais de domínio da organização. Ela requer um servidor RADIUS que integra-se com o servidor do AD. As empresas também podem aproveitar sua implantação existente do RADIUS.
 
O servidor RADIUS podem residir no local ou na rede virtual do Azure. Durante a autenticação, o gateway de VPN atua como uma passagem e encaminha as mensagens de autenticação entre o servidor RADIUS e o dispositivo de conexão. É importante para o gateway de VPN poder acessar o servidor RADIUS. Se o servidor RADIUS for local, é necessária uma conexão VPN Site a Site do Azure para o site local.

Além do Active Directory, um servidor RADIUS também pode integrar-se com outros sistemas de identidade externa. Isso possibilita várias opções de autenticação para VPNs Ponto a Site, incluindo opções de MFA. Verifique a documentação do fornecedor de servidor RADIUS para obter a lista de sistemas de identidade aos quais ele pode ser integrado.

![Diagrama de conexão - RADIUS](./media/point-to-site-how-to-radius-ps/radiusimage.png)

> [!IMPORTANT]
>Somente uma conexão VPN Site a Site pode ser usada para se conectar a um servidor RADIUS local. Uma conexão ExpressRoute não pode ser usada.
>
>

## <a name="before"></a>Antes de começar

* Verifique se você tem uma assinatura do Azure. Se ainda não tiver uma assinatura do Azure, você poderá ativar os [Benefícios do assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) ou inscrever-se para obter uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial).

* Instale a versão mais recente dos cmdlets do PowerShell do Azure Resource Manager. Para saber mais sobre como instalar cmdlets do PowerShell, confira [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

### <a name="log-in"></a>Fazer logon

[!INCLUDE [Log in](../../includes/vpn-gateway-ps-login-include.md)]

### <a name="example"></a>Valores de exemplo

Você pode usar os valores do exemplo para criar um ambiente de teste ou fazer referência a esses valores para entender melhor os exemplos neste artigo. Você pode usar as etapas como um passo a passo e usar os valores sem alterá-los, ou alterá-los para refletir seu ambiente.

* **Nome: VNet1**
* **Espaço de endereço: 192.168.0.0/16** e **10.254.0.0/16**<br>Neste exemplo, usamos mais de um espaço de endereço para ilustrar que esta configuração funciona com vários espaços de endereço. No entanto, vários espaços de endereço não são necessários para esta configuração.
* **Nome da sub-rede: FrontEnd**
  * **Intervalo de endereços da sub-rede: 192.168.1.0/24**
* **Nome da sub-rede: BackEnd**
  * **Intervalo de endereços da sub-rede: 10.254.1.0/24**
* **Nome da sub-rede: GatewaySubnet**<br>O nome da Sub-rede *GatewaySubnet* é obrigatório para que o gateway de VPN funcione.
  * **Intervalo de endereços da GatewaySubnet: 192.168.200.0/24** 
* **Pool de endereços do cliente VPN: 172.16.201.0/24**<br>Os clientes VPN que se conectarem à rede virtual usando esta conexão Ponto a Site receberão um endereço IP do pool de endereços do cliente VPN.
* **Assinatura:** verifique se você tem mais de uma assinatura, verifique se está usando a correta.
* **Grupo de Recursos: TestRG**
* **Local: Leste dos EUA**
* **Servidor DNS: Endereço IP** do servidor DNS que você deseja usar para a resolução de nome para a sua rede virtual. (opcional)
* **Nome de GW: Vnet1GW**
* **Nome do IP público: VNet1GWPIP**
* **VpnType: RouteBased** 

## 1. <a name="vnet"></a>Criar o grupo de recursos, a rede virtual e o endereço IP público

As seguintes etapas criam um grupo de recursos e uma rede virtual no grupo de recursos com três sub-redes. Ao substituir valores, é importante que você sempre nomeie sua sub-rede de gateway especificamente como ‘GatewaySubnet’. Se você usar outro nome, a criação do gateway falhará;

1. Crie um grupos de recursos.

  ```powershell
  New-AzureRmResourceGroup -Name "TestRG" -Location "East US"
  ```
2. Crie as configurações de sub-rede para a rede virtual e dê a elas os nomes *FrontEnd*, *BackEnd* e *GatewaySubnet*. Esses prefixos devem fazer parte do espaço de endereço da rede virtual declarada por você.

  ```powershell
  $fesub = New-AzureRmVirtualNetworkSubnetConfig -Name "FrontEnd" -AddressPrefix "192.168.1.0/24"  
  $besub = New-AzureRmVirtualNetworkSubnetConfig -Name "Backend" -AddressPrefix "10.254.1.0/24"  
  $gwsub = New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix "192.168.200.0/24"
  ```
3. Crie a rede virtual.

  Neste exemplo, o parâmetro de servidor -DnsServer é opcional. A especificação de um valor não cria um novo servidor DNS. O endereço IP do servidor DNS especificado deve ser um servidor DNS que pode resolver os nomes dos recursos aos quais você está se conectando a partir da sua rede virtual. Neste exemplo, usamos um endereço IP privado, mas é provável que ele não seja o endereço IP do seu servidor DNS. Use seus próprios valores. O valor especificado é usado pelos recursos que são implantados para a rede virtual, não com a conexão de P2S.

  ```powershell
  New-AzureRmVirtualNetwork -Name "VNet1" -ResourceGroupName "TestRG" -Location "East US" -AddressPrefix "192.168.0.0/16","10.254.0.0/16" -Subnet $fesub, $besub, $gwsub -DnsServer 10.2.1.3
  ```
4. Um gateway de VPN deve ter um endereço IP público. Você primeiro solicita o recurso de endereço IP e, em seguida, faz referência a ele ao criar seu gateway de rede virtual. O endereço IP é atribuído dinamicamente ao recurso quando o gateway de VPN é criado. O gateway de VPN atualmente suporta apenas alocação de endereços IP público *Dinâmico*. Você não pode solicitar uma atribuição de endereço IP Público Estático. No entanto, isso não significa que o endereço IP é alterado depois que ele foi atribuído ao seu gateway de VPN. A única vez em que o endereço IP Público é alterado é quando o gateway é excluído e recriado. Isso não altera o redimensionamento, a redefinição ou outras manutenções/atualizações internas do seu gateway de VPN.

  Especifique as variáveis para solicitar um endereço IP público atribuído dinamicamente.

  ```powershell
  $vnet = Get-AzureRmVirtualNetwork -Name "VNet1" -ResourceGroupName "TestRG"  
  $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet 
  $pip = New-AzureRmPublicIpAddress -Name "VNet1GWPIP" -ResourceGroupName "TestRG" -Location "East US" -AllocationMethod Dynamic 
  $ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name "gwipconf" -Subnet $subnet -PublicIpAddress $pip
  ```

## 2. <a name="radius"></a>Configurar o seu servidor RADIUS

Antes de criar e configurar o gateway de rede virtual, o servidor RADIUS deve ser configurado corretamente para autenticação.

1. Se você não tiver um servidor RADIUS implantado, implante um. Para saber sobre as etapas de implantação, confira o guia de instalação do seu fornecedor RADIUS.  
2. Configure o gateway de VPN como um cliente RADIUS no RADIUS. Ao adicionar esse cliente RADIUS, especifique a rede virtual GatewaySubnet que você criou. 
3. Depois que o servidor RADIUS estiver configurado, obtenha o endereço IP do servidor RADIUS e o segredo compartilhado que os clientes RADIUS devem usar para se comunicar com o servidor RADIUS. Se o servidor RADIUS estiver na rede virtual do Azure, use o IP da autoridade de certificação da VM do servidor RADIUS.

O artigo sobre o [Servidor de Políticas de Rede (NPS)](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top) fornece orientações sobre como configurar um servidor RADIUS do Windows (NPS) para autenticação de domínio do AD.

## 3. <a name="creategw"></a>Criar o gateway de VPN

Configurar e criar o gateway de VPN para sua rede virtual.

* O -GatewayType deve ser 'Vpn' e o -VpnType deve ser 'RouteBased'.
* Um gateway de VPN pode levar até 45 minutos para ser concluído, dependendo do  [SKU de gateway](vpn-gateway-about-vpn-gateway-settings.md#gwsku)  selecionado.

```powershell
New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
-Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
-VpnType RouteBased -EnableBgp $false -GatewaySku VpnGw1
```

## 4. <a name="addradius"></a>Adicionar o servidor RADIUS e pool de endereços do cliente
 
* O -RadiusServer pode ser especificado por nome ou endereço IP. Se você especificar o nome e o servidor for local, o gateway de VPN não poderá resolver o nome. Se esse for o caso, é melhor especificar o endereço IP do servidor. 
* O -RadiusSecret deve corresponder ao que é configurado no servidor RADIUS.
* O -VpnClientAddressPool é o intervalo do qual os clientes VPN recebem um endereço IP ao se conectar. Use um intervalo de endereço IP privado que não coincida com o local que você irá se conectar a partir da, ou com a rede virtual que você deseja se conectar. Certifique-se de que você tenha um pool de endereços grande o suficiente configurado.  

1. Crie uma cadeia de caracteres segura para o segredo RADIUS.

  ```powershell
  $Secure_Secret=Read-Host -AsSecureString -Prompt "RadiusSecret"
  ```

2. Você precisará inserir o segredo do RADIUS. Os caracteres que você inserir não serão exibidos e, ao invés disso, serão substituídos pelo caractere "*".

  ```powershell
  RadiusSecret:***
  ```
3. Adicione as informações do pool de endereços de cliente VPN e do servidor RADIUS.

  Para configurações SSTP:

    ```powershell
    $Gateway = Get-AzureRmVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol "SSTP" `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```

  Para configurações IKEv2:

    ```powershell
    $Gateway = Get-AzureRmVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol "IKEv2" `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```

  Para SSTP + IKEv2

    ```powershell
    $Gateway = Get-AzureRmVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol @( "SSTP", "IkeV2" ) `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```

## 5. <a name="vpnclient"></a>Baixar o pacote de configuração do cliente VPN e configurar o cliente VPN

A configuração de cliente VPN permite que os dispositivos se conectem a uma rede virtual através de uma conexão de P2S. Para gerar um pacote de configuração de cliente VPN e configurar o cliente VPN, confira [Criar uma configuração de cliente VPN para autenticação RADIUS](point-to-site-vpn-client-configuration-radius.md).

## <a name="connect"></a>6. Conecte-se ao Azure

### <a name="to-connect-from-a-windows-vpn-client"></a>Para conectar-se a partir de um cliente de VPN do Windows

1. Para se conectar à sua rede virtual, no computador cliente, navegue até conexões VPN e localize a conexão VPN que você criou. Ele terá o mesmo nome da sua rede virtual. Insira suas credenciais de domínio e clique em 'Conectar'. É exibida uma mensagem pop-up solicitando direitos elevados. Aceite-a e insira as credenciais.

  ![Cliente VPN se conecta ao Azure](./media/point-to-site-how-to-radius-ps/client.png)
2. A conexão é estabelecida.

  ![Conexão estabelecida](./media/point-to-site-how-to-radius-ps/connected.png)

### <a name="connect-from-a-mac-vpn-client"></a>Conectar-se a partir de um cliente VPN do Mac

Na caixa de diálogo de Rede, localize o perfil de cliente que você deseja usar e, em seguida, clique em **Conectar**.

  ![Conexão Mac](./media/vpn-gateway-howto-point-to-site-rm-ps/applyconnect.png)

## <a name="verify"></a>Para verificar sua conexão

1. Para verificar se a conexão VPN está ativa, abra um prompt de comandos com privilégios elevados e execute *ipconfig/all*.
2. Exiba os resultados. Observe que o endereço IP que você recebeu está dentro do pool de endereços de cliente VPN Ponto a Site especificado em sua configuração. Os resultados são semelhantes a este exemplo:

  ```
  PPP adapter VNet1:
      Connection-specific DNS Suffix .:
      Description.....................: VNet1
      Physical Address................:
      DHCP Enabled....................: No
      Autoconfiguration Enabled.......: Yes
      IPv4 Address....................: 172.16.201.3(Preferred)
      Subnet Mask.....................: 255.255.255.255
      Default Gateway.................:
      NetBIOS over Tcpip..............: Enabled
  ```

## <a name="connectVM"></a>Para conectar-se a uma máquina virtual

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-include.md)]

## <a name="faq"></a>Perguntas frequentes

As Perguntas frequentes aplicam-se ao P2S usando a autenticação RADIUS

[!INCLUDE [Point-to-Site RADIUS FAQ](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>Próximas etapas

Quando sua conexão for concluída, você poderá adicionar máquinas virtuais às suas redes virtuais. Para saber mais, veja [Máquinas virtuais](https://docs.microsoft.com/azure/#pivot=services&panel=Compute). Para saber mais sobre redes e máquinas virtuais, consulte [Visão geral de rede do Azure e VM Linux](../virtual-machines/linux/azure-vm-network-overview.md).

