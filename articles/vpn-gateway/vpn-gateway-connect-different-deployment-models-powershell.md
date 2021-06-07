---
title: 'Conectar redes virtuais clássicas a VNets do Azure Resource Manager: PowerShell'
description: Criar uma conexão VPN entre os VNets clássicas e o VNets do Gerenciador de Recursos usando Gateway de VPN e PowerShell.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/10/2021
ms.author: cherylmc
ms.openlocfilehash: 7012a696684a4e7bc1d3b52943b11ba413c43037
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100376481"
---
# <a name="connect-virtual-networks-from-different-deployment-models-using-powershell"></a>Conectar redes virtuais de diferentes modelos de implantação usando o PowerShell

Este artigo ajuda-o a conectar as VNets clássicas às VNets do Gerenciador de Recursos para permitir que os recursos localizados nos modelos de implantação separados comuniquem-se entre si. As etapas neste artigo usam o PowerShell, mas você também pode criar essa configuração usando o portal do Azure, selecionando o artigo desta lista.

> [!div class="op_single_selector"]
> * [Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
> 
> 

Conectar uma rede virtual clássica a outra rede virtual do Resource Manager é semelhante a conectar uma rede virtual a um site local. Os dois tipos de conectividade usam um gateway de VPN para fornecer um túnel seguro usando IPsec/IKE. Você pode criar uma conexão entre redes virtuais em assinaturas e regiões diferentes. Você também pode conectar redes virtuais que já têm conexões com redes locais, desde que o gateway com o qual foram configuradas seja dinâmico ou baseado em rota. Para saber mais sobre conexões de Rede Virtual a Rede Virtual, consulte as [Perguntas frequentes sobre Rede Virtual para Rede Virtual](#faq) no final deste artigo. 

Se você ainda não tem um gateway de rede virtual e não quer criar um, conecte, em vez disso, suas VNets usando o Emparelhamento VNET. O emparelhamento Vnet não usa um gateway de VPN. Para obter mais informações, consulte [Emparelhamento da VNet](../virtual-network/virtual-network-peering-overview.md).

## <a name="before-you-begin"></a><a name="before"></a>Antes de começar

As etapas a seguir mostrarão as configurações necessárias para configurar um gateway dinâmico ou baseado em rota para cada Rede Virtual, e para criar uma conexão VPN entre os gateways. Essa configuração não dá suporte a gateways estáticos ou baseados em política.

### <a name="prerequisites"></a><a name="pre"></a>Pré-requisitos

* Ambas as redes virtuais já foram criadas. Se você precisar criar uma rede virtual do Resource Manager, confira [Crie um grupo de recursos e uma rede virtual](../virtual-network/quick-create-powershell.md#create-a-resource-group-and-a-virtual-network). Para criar uma rede virtual clássica, consulte [Criar uma rede virtual clássica](/previous-versions/azure/virtual-network/create-virtual-network-classic).
* Os intervalos de endereços das redes virtuais não se sobrepõem entre eles ou aos intervalos de outras conexões às quais os gateways podem estar conectados.
* Você instalou os últimos cmdlets do PowerShell. Consulte [Como instalar e configurar o Azure PowerShell](/powershell/azure/) para obter mais informações. Instale os dois cmdlets do Resource Manager (RM) e do Gerenciamento de Serviços (SM). 

### <a name="example-settings"></a><a name="exampleref"></a>Configurações de exemplo

Você pode usar esses valores para criar um ambiente de teste ou consultá-los para compreender melhor os exemplos neste artigo.

**Configurações da Rede Virtual clássica**

Nome da rede virtual = ClassicVNet  <br>
Local = Oeste dos EUA <br>
Espaços de Endereço da Rede Virtual = 10.0.0.0/24 <br>
Subnet-1 = 10.0.0.0/27 <br>
GatewaySubnet = 10.0.0.32/29 <br>
Nome da rede local = RMVNetLocal <br>
 GatewayType = DynamicRouting

**Configurações de Rede Virtual do Resource Manager**

Nome da VNet = RMVNet  <br>
Grupo de recursos = RG1 <br>
Espaços de Endereço do IP da Rede Virtual = 192.168.0.0/16 <br>
Subnet-1 = 192.168.1.0/24 <br>
GatewaySubnet = 192.168.0.0/26 <br>
Local = Leste dos EUA <br>
Nome do IP Público do Gateway = gwpip <br>
Gateway de Rede Local = ClassicVNetLocal <br>
Nome do Gateway de Rede Virtual = RMGateway <br>
 Configuração de endereçamento IP do gateway = gwipconfig

## <a name="section-1---configure-the-classic-vnet"></a><a name="createsmgw"></a>Seção 1 - Configurar a rede virtual clássica
### <a name="1-download-your-network-configuration-file"></a>1. Baixar o arquivo de configuração de rede
1. Faça logon na sua conta do Azure no console do PowerShell com direitos elevados. O cmdlet a seguir solicita as credenciais de logon para sua conta do Azure. Depois de entrar, ele baixa as configurações da conta para que elas estejam disponíveis para o Azure PowerShell. Nesta seção, são usados os cmdlets clássicos do SM (Gerenciamento de Serviços) do Azure PowerShell.

   ```azurepowershell
   Add-AzureAccount
   ```

   Obtenha sua assinatura do Azure.

   ```azurepowershell
   Get-AzureSubscription
   ```

   Se você tiver mais de uma assinatura, selecione a assinatura que deseja usar.

   ```azurepowershell
   Select-AzureSubscription -SubscriptionName "Name of subscription"
   ```
2. Baixe o arquivo de configuração de rede do Azure executando o comando a seguir. Você pode alterar o local do arquivo a ser exportado para um local diferente, se necessário.

   ```azurepowershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
3. Abra o arquivo .xml baixado para editá-lo. Para obter um exemplo de arquivo de configuração de rede, confira o [Esquema de configuração de rede](/previous-versions/azure/reference/jj157100(v=azure.100)).

### <a name="2-verify-the-gateway-subnet"></a>2. Verificar a sub-rede do gateway
No elemento **VirtualNetworkSites** , adicione uma sub-rede de gateway à rede virtual se já não existir uma. Ao trabalhar com o arquivo de configuração de rede, a sub-rede do gateway DEVE ser nomeada como "GatewaySubnet", ou o Azure poderá não reconhecê-la e usá-la como uma sub-rede de gateway.

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

**Exemplo:**

```xml
<VirtualNetworkSites>
  <VirtualNetworkSite name="ClassicVNet" Location="West US">
    <AddressSpace>
      <AddressPrefix>10.0.0.0/24</AddressPrefix>
    </AddressSpace>
    <Subnets>
      <Subnet name="Subnet-1">
        <AddressPrefix>10.0.0.0/27</AddressPrefix>
      </Subnet>
      <Subnet name="GatewaySubnet">
        <AddressPrefix>10.0.0.32/29</AddressPrefix>
      </Subnet>
    </Subnets>
  </VirtualNetworkSite>
</VirtualNetworkSites>
```

### <a name="3-add-the-local-network-site"></a>3. Adicionar o site de rede local
O site de rede local que adicionar representará a rede virtual do RM ao qual você deseja se conectar. Adicione um elemento **LocalNetworkSites** ao arquivo, se ele ainda não existir. Nessa parte da configuração, o VPNGatewayAddress pode ser qualquer endereço IP público válido porque ainda não criamos o gateway para a rede virtual do Resource Manager. Depois de criarmos o gateway, poderemos substituir esse endereço IP de espaço reservado pelo endereço IP público correto que foi atribuído ao gateway do RM.

```xml
<LocalNetworkSites>
  <LocalNetworkSite name="RMVNetLocal">
    <AddressSpace>
      <AddressPrefix>192.168.0.0/16</AddressPrefix>
    </AddressSpace>
    <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
  </LocalNetworkSite>
</LocalNetworkSites>
```

### <a name="4-associate-the-vnet-with-the-local-network-site"></a>4. Associar a VNet ao site de rede local
Nesta seção, podemos especificar o local de rede local ao qual você deseja conectar a rede virtual. Nesse caso, é a rede virtual do Resource Manager que foi mencionada anteriormente. Verifique se os nomes correspondem. Esta etapa não cria um gateway. Ela especifica a rede local a que o gateway será conectado.

```xml
<Gateway>
  <ConnectionsToLocalNetwork>
    <LocalNetworkSiteRef name="RMVNetLocal">
      <Connection type="IPsec" />
    </LocalNetworkSiteRef>
  </ConnectionsToLocalNetwork>
</Gateway>
```

### <a name="5-save-the-file-and-upload"></a>5. Salvar o arquivo e carregar
Salve o arquivo e importe-o para o Azure executando o comando a seguir. Altere o caminho do arquivo conforme for necessário para seu ambiente.

```azurepowershell
Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
```

Você verá um resultado semelhante, mostrando que a importação teve êxito.

```output
OperationDescription        OperationId                      OperationStatus                                                
--------------------        -----------                      ---------------                                                
Set-AzureVNetConfig        e0ee6e66-9167-cfa7-a746-7casb9    Succeeded 
```

### <a name="6-create-the-gateway"></a>6. Criar o gateway

Antes de executar esse exemplo, consulte o arquivo de configuração de rede baixado para obter os nomes exatos que o Azure espera visualizar. O arquivo de configuração de rede contém os valores de suas redes virtuais clássicas. Às vezes, os nomes de redes virtuais clássicas são alterados no arquivo de configuração de rede durante a criação de configurações da rede virtual clássicas no portal do Azure devido a diferenças nos modelos de implantação. Por exemplo, se você usou o portal do Azure para criar uma rede virtual clássica denominada 'Rede virtual clássica' e a criou em um grupo de recursos denominado 'ClassicRG', o nome que está contido no arquivo de configuração de rede é convertido em 'Grupo ClassicRG Rede virtual clássica'. Ao especificar o nome de uma rede virtual que contenha espaços, use aspas ao redor do valor.


Use o exemplo a seguir para criar um gateway de roteamento dinâmico:

```azurepowershell
New-AzureVNetGateway -VNetName ClassicVNet -GatewayType DynamicRouting
```

Você pode verificar o status do gateway, usando o cmdlet **Get-AzureVNetGateway**.

## <a name="section-2---configure-the-rm-vnet-gateway"></a><a name="creatermgw"></a>Seção 2 – configurar o gateway de VNet do RM



Os pré-requisitos pressupõem que você já tenha criado umaVNet RM. Nesta etapa, você pode criar um gateway VPN para a VNet RM. Não comece as etapas a seguir até recuperar o endereço IP público do gateway da rede virtual clássica. 

1. Faça logon na sua conta do Azure no console do PowerShell. O cmdlet a seguir solicita as credenciais de logon para sua conta do Azure. Depois de entrar, as configurações da conta são baixadas para que estejam disponíveis para o Azure PowerShell. Opcionalmente, você pode usar o recurso de "Experimente" para iniciar o Azure Cloud Shell no navegador.

   Se você usar o Azure Cloud Shell, ignore o seguinte cmdlet:

   ```azurepowershell
   Connect-AzAccount
   ``` 
   Para verificar que você está usando a assinatura correta, execute o seguinte cmdlet:  

   ```azurepowershell-interactive
   Get-AzSubscription
   ```
   
   Se você tiver mais de uma assinatura, especifique a assinatura que deseja usar.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "Name of subscription"
   ```
2. Criar um gateway de rede local. Em uma rede virtual, o gateway de rede local geralmente se refere ao seu local. Nesse caso, o gateway de rede local refere-se à rede virtual clássica. Dê um nome pelo qual o Azure pode fazer referência a ele e especifique também o prefixo de espaço de endereço. O Azure usa o prefixo de endereço IP que você especifica para identificar qual tráfego enviar a seu local. Se você precisar ajustar as informações aqui posteriormente, antes de criar o gateway, poderá modificar os valores e executar o exemplo novamente.
   
   **-Name** é o nome que você deseja atribuir ao se referir ao gateway de rede local.<br>
   **-AddressPrefix** é o Espaço de Endereço para a VNet clássica.<br>
   **-GatewayIpAddress** é o endereço IP público do gateway da VNet clássica. Certifique-se de alterar o seguinte texto de exemplo "n.n.n. n" para refletir o endereço IP correto.<br>

   ```azurepowershell-interactive
   New-AzLocalNetworkGateway -Name ClassicVNetLocal `
   -Location "West US" -AddressPrefix "10.0.0.0/24" `
   -GatewayIpAddress "n.n.n.n" -ResourceGroupName RG1
   ```
3. Solicite um endereço IP público a ser alocado ao gateway de rede virtual para a VNet do Resource Manager. Você não pode especificar o endereço IP que deseja usar. O endereço IP é alocado dinamicamente para o gateway de rede virtual. No entanto, isso não significa que o endereço IP muda. A única vez em que o endereço IP do gateway de rede virtual é alterado é quando o gateway é excluído e recriado. Isso não altera o redimensionamento, a redefinição ou outras manutenções/atualizações internas do gateway.

   Nesta etapa, também definimos uma variável usada em uma etapa posterior.

   ```azurepowershell-interactive
   $ipaddress = New-AzPublicIpAddress -Name gwpip `
   -ResourceGroupName RG1 -Location 'EastUS' `
   -AllocationMethod Dynamic
   ```

4. Verifique se sua rede virtual tem uma sub-rede de gateway. Se não houver sub-rede de gateway, adicione uma. Verifique se a sub-rede de gateway se chama *GatewaySubnet*.
5. Recupere a sub-rede usada para o gateway executando o comando a seguir. Nesta etapa, também definimos uma variável a ser usada na próxima etapa.
   
   **-Name** é o nome de sua VNet do Resource Manager.<br>
   **-ResourceGroupName** é o grupo de recursos ao qual a VNet está associada. A sub-rede de gateway já deve existir para essa rede virtual e deve se chamar *GatewaySubnet* para funcionar corretamente.<br>

   ```azurepowershell-interactive
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name GatewaySubnet `
   -VirtualNetwork (Get-AzVirtualNetwork -Name RMVNet -ResourceGroupName RG1)
   ``` 

6. Criar a configuração de endereçamento IP do gateway. A configuração do gateway define a sub-rede e o endereço IP público a serem usados. Use o exemplo a seguir para criar a configuração do gateway.

   Nessa etapa, os parâmetros **-SubnetId** e **-PublicIpAddressId** devem receber a propriedade id da sub-rede e objetos de endereço IP, respectivamente. Você não pode usar uma cadeia de caracteres simples. Essas variáveis são definidas na etapa de solicitação de um IP público e na etapa de recuperação da sub-rede.

   ```azurepowershell-interactive
   $gwipconfig = New-AzVirtualNetworkGatewayIpConfig `
   -Name gwipconfig -SubnetId $subnet.id `
   -PublicIpAddressId $ipaddress.id
   ```
7. Crie o gateway de rede virtual do Resource Manager, executando o comando a seguir. O `-VpnType` deve ser *RouteBased*. Pode demorar 45 minutos ou mais para que o gateway seja criado.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1 `
   -Location "EastUS" -GatewaySKU Standard -GatewayType Vpn `
   -IpConfigurations $gwipconfig `
   -EnableBgp $false -VpnType RouteBased
   ```
8. Copie o endereço IP público depois que o gateway de VPN foi criado. Use-o quando você definir as configurações de rede local para sua Rede Virtual clássica. Você pode usar o cmdlet a seguir para recuperar o endereço IP público. O endereço IP público é listado no retorno como *IpAddress*.

   ```azurepowershell-interactive
   Get-AzPublicIpAddress -Name gwpip -ResourceGroupName RG1
   ```

## <a name="section-3---modify-the-classic-vnet-local-site-settings"></a><a name="localsite"></a>Seção 3 – Modificar as configurações de site local de VNet clássicas

Nesta seção, você trabalhará com a VNet clássica. Você substitui o endereço IP de espaço reservado pelo usado ao especificar as configurações de site local que serão utilizadas para conectar com gateway da VNET do Gerenciador de Recursos. Como você está trabalhando com a rede virtual clássica, use o PowerShell instalado localmente em seu computador, não o Azure Cloud Shell TryIt.

1. Exportar o arquivo de configuração de rede.

   ```azurepowershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
2. Usando um editor de texto, modifique o valor de VPNGatewayAddress. Substitua o endereço IP de espaço reservado com o endereço IP público do gateway do Resource Manager e salve as alterações.

   ```
   <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
   ```
3. Importe o arquivo de configuração de rede modificada para o Azure.

   ```azurepowershell
   Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
   ```

## <a name="section-4---create-a-connection-between-the-gateways"></a><a name="connect"></a>Seção 4 – criar uma conexão entre os gateways
A criação de uma conexão entre os gateways requer o PowerShell. Pode ser necessário adicionar sua Conta do Azure para usar a versão clássica do cmdlets do PowerShell. Para fazer isso, use **Add-AzureAccount**.

1. No console do PowerShell, defina a chave compartilhada. Antes de executar os cmdlets, consulte no arquivo de configuração de rede que você baixou os nomes exatos que o Azure espera ver. Ao especificar o nome de uma rede virtual que contenha espaços, use aspas simples ao redor do valor.<br><br>No exemplo a seguir, **-VNetName** é o nome da VNet clássica e **-LocalNetworkSiteName** é o nome que você especificou para o site da rede local. O **-SharedKey** é um valor que você gera e especifica. Neste exemplo, usamos ‘abc123’, mas você pode gerar e usar algo mais complexo. O importante é que o valor especificado aqui deve ser o mesmo que você especificará na próxima etapa, ao criar sua conexão. O retorno deve mostrar **Status: êxito**.

   ```azurepowershell
   Set-AzureVNetGatewayKey -VNetName ClassicVNet `
   -LocalNetworkSiteName RMVNetLocal -SharedKey abc123
   ```
2. Crie a conexão VPN executando os comandos a seguir:
   
   Defina as variáveis.

   ```azurepowershell-interactive
   $vnet01gateway = Get-AzLocalNetworkGateway -Name ClassicVNetLocal -ResourceGroupName RG1
   $vnet02gateway = Get-AzVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1
   ```
   
   Crie a conexão. Observe que o **-ConnectionType** é IPsec, não Vnet2Vnet.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGatewayConnection -Name RM-Classic -ResourceGroupName RG1 `
   -Location "East US" -VirtualNetworkGateway1 `
   $vnet02gateway -LocalNetworkGateway2 `
   $vnet01gateway -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
   ```

## <a name="section-5---verify-your-connections"></a><a name="verify"></a>Seção 5 – Verificar suas conexões

### <a name="to-verify-the-connection-from-your-classic-vnet-to-your-resource-manager-vnet"></a>Para verificar a conexão de sua VNET clássica para sua VNET do Resource Manager

#### <a name="powershell"></a>PowerShell

[!INCLUDE [vpn-gateway-verify-connection-ps-classic](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

#### <a name="azure-portal"></a>Portal do Azure

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]


### <a name="to-verify-the-connection-from-your-resource-manager-vnet-to-your-classic-vnet"></a>Para verificar a conexão de seu VNET do Resource Manager para sua rede virtual clássica

#### <a name="powershell"></a>PowerShell

[!INCLUDE [vpn-gateway-verify-ps-rm](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

#### <a name="azure-portal"></a>Portal do Azure

[!INCLUDE [vpn-gateway-verify-connection-portal-rm](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="vnet-to-vnet-faq"></a><a name="faq"></a>Perguntas frequentes sobre Rede Virtual para Rede Virtual

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]
