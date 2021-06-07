---
title: 'Conectar redes locais a uma rede virtual: VPN site a site: CLI'
description: Crie uma conexão de gateway de VPN site a site IPsec de sua rede local para uma rede virtual do Azure pela Internet pública usando a CLI.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/23/2020
ms.author: cherylmc
ms.openlocfilehash: 2c59c67eb7b5ae5b26ac5517afba433fe8c028fa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104611739"
---
# <a name="create-a-virtual-network-with-a-site-to-site-vpn-connection-using-cli"></a>Criar uma rede virtual com uma conexão VPN Site a Site usando a CLI

Este artigo mostra como usar a CLI do Azure para criar uma conexão de gateway de VPN Site a Site de sua rede local para a rede virtual. As etapas neste artigo se aplicam ao modelo de implantação do Resource Manager. Você também pode criar essa configuração usando uma ferramenta de implantação ou um modelo de implantação diferente, selecionando uma opção diferente na lista a seguir:<br>

> [!div class="op_single_selector"]
> * [Azure portal](./tutorial-site-to-site-portal.md)
> * [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Portal do Azure (clássico)](vpn-gateway-howto-site-to-site-classic-portal.md)

![Diagrama de conexão Site a Site de Gateway de VPN entre locais](./media/vpn-gateway-howto-site-to-site-resource-manager-cli/site-to-site-diagram.png)

Uma conexão de gateway de VPN Site a Site é usada para conectar a rede local a uma rede virtual do Azure por um túnel VPN IPsec/IKE (IKEv1 ou IKEv2). Esse tipo de conexão exige um dispositivo VPN localizado no local que tenha um endereço IP público voltado para o exterior atribuído a ele. Para saber mais sobre os gateways de VPN, veja [Sobre o gateway de VPN](vpn-gateway-about-vpngateways.md).

## <a name="before-you-begin"></a>Antes de começar

Verifique se você atende aos seguintes critérios antes de iniciar a configuração:

* Verifique se você possui um dispositivo VPN compatível e alguém que possa configurá-lo. Para obter mais informações sobre dispositivos VPN compatíveis e a configuração de dispositivo, confira [Sobre dispositivos VPN](vpn-gateway-about-vpn-devices.md).
* Verifique se você possui um endereço IPv4 público voltado para o exterior para seu dispositivo VPN.
* Se não estiver familiarizado com os intervalos de endereços IP localizados na configuração de rede local, você precisará trabalhar em conjunto com alguém que possa lhe fornecer os detalhes. Ao criar essa configuração, você deve especificar os prefixos de intervalo de endereços IP que o Azure roteará para seu local. Nenhuma das sub-redes da rede local podem se sobrepor às sub-redes de rede virtual às quais você deseja se conectar.
[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]
* Este artigo exige a versão 2.0 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

### <a name="example-values"></a><a name="example"></a>Valores de exemplo

Você pode usar os seguintes valores para criar um ambiente de teste ou fazer referência a esses valores para entender melhor os exemplos neste artigo:

```
#Example values

VnetName                = TestVNet1 
ResourceGroup           = TestRG1 
Location                = eastus 
AddressSpace            = 10.11.0.0/16 
SubnetName              = Subnet1 
Subnet                  = 10.11.0.0/24 
GatewaySubnet           = 10.11.255.0/27 
LocalNetworkGatewayName = Site2 
LNG Public IP           = <VPN device IP address>
LocalAddrPrefix1        = 10.0.0.0/24
LocalAddrPrefix2        = 20.0.0.0/24   
GatewayName             = VNet1GW 
PublicIP                = VNet1GWIP 
VPNType                 = RouteBased 
GatewayType             = Vpn 
ConnectionName          = VNet1toSite2
```

## <a name="1-connect-to-your-subscription"></a><a name="Login"></a>1. conectar-se à sua assinatura

Se você optar por executar a CLI localmente, conecte-se à sua assinatura. Se você estiver usando o Azure Cloud Shell no navegador, você não precisa se conectar à sua assinatura. Você se conectará automaticamente no Azure Cloud Shell. No entanto, você pode querer verificar se está usando a assinatura correta depois de se conectar.

[!INCLUDE [CLI login](../../includes/vpn-gateway-cli-login-include.md)]

## <a name="2-create-a-resource-group"></a><a name="rg"></a>2. criar um grupo de recursos

O exemplo a seguir cria um grupo de recursos denominado 'TestRG1' no local 'eastus'. Se já tiver um grupo de recursos na região em que deseja criar a rede virtual, você poderá usá-lo.

```azurecli-interactive
az group create --name TestRG1 --location eastus
```

## <a name="3-create-a-virtual-network"></a><a name="VNet"></a>3. criar uma rede virtual

Se você ainda não tiver uma rede virtual, crie uma usando o comando [az network vnet create](/cli/azure/network/vnet). Ao criar uma rede virtual, certifique-se de que os espaços de endereço que você especificar não se sobreponham nenhum espaço de endereço que você tenha na rede local.

>[!NOTE]
>Para que essa rede virtual conecte a um local, é necessário coordenar com o administrador de rede local para que ele consiga um intervalo de endereços IP que você pode usar especificamente para essa rede virtual. Se um intervalo de endereços duplicado existir em ambos os lados da conexão de VPN, o tráfego não será roteado da maneira esperada. Além disso, se você deseja se conectar esta rede virtual a outra rede virtual, o espaço de endereços não pode sobrepor outra rede virtual. Tome cuidado e planeje sua configuração de rede de forma adequada.
>
>

O exemplo a seguir cria uma rede virtual chamada 'TestVNet1' e uma sub-rede 'Subrede 1'.

```azurecli-interactive
az network vnet create --name TestVNet1 --resource-group TestRG1 --address-prefix 10.11.0.0/16 --location eastus --subnet-name Subnet1 --subnet-prefix 10.11.0.0/24
```

## <a name="4-create-the-gateway-subnet"></a>4. <a name="gwsub"></a> criar a sub-rede de gateway


[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-include.md)]

Execute o comando [azure network vnet subnet create](/cli/azure/network/vnet/subnet) para criar uma sub-rede de gateway.

```azurecli-interactive
az network vnet subnet create --address-prefix 10.11.255.0/27 --name GatewaySubnet --resource-group TestRG1 --vnet-name TestVNet1
```

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="5-create-the-local-network-gateway"></a><a name="localnet"></a>5. criar o gateway de rede local

O gateway de rede local geralmente se refere ao seu local. Você atribui um nome ao site pelo qual o Azure pode fazer referência a ele e especifica o endereço IP do dispositivo VPN local para o qual você criará uma conexão. Você também pode especificar os prefixos de endereço IP que serão roteados por meio do gateway de VPN para o dispositivo VPN. Os prefixos de endereço que você especifica são os prefixos localizados em sua rede local. Se sua rede local for alterada, você poderá atualizar facilmente os prefixos.

Use os seguintes valores:

* O *--gateway-IP-address* é o endereço IP do seu dispositivo VPN local.
* Os *--local-address-prefixes* são seus espaços de endereços locais.

Use o comando [az network local-gateway create](/cli/azure/network/local-gateway) para adicionar um gateway de rede local com vários prefixos de endereço:

```azurecli-interactive
az network local-gateway create --gateway-ip-address 23.99.221.164 --name Site2 --resource-group TestRG1 --local-address-prefixes 10.0.0.0/24 20.0.0.0/24
```

## <a name="6-request-a-public-ip-address"></a><a name="PublicIP"></a>6. solicitar um endereço IP público

Um gateway de VPN deve ter um endereço IP público. Você primeiro solicita o recurso de endereço IP e, em seguida, faz referência a ele ao criar seu gateway de rede virtual. O endereço IP é atribuído dinamicamente ao recurso quando o gateway de VPN é criado. O gateway de VPN atualmente suporta apenas alocação de endereços IP público *Dinâmico*. Você não pode solicitar uma atribuição de endereço IP Público Estático. No entanto, isso não significa que o endereço IP é alterado depois que ele foi atribuído ao seu gateway de VPN. A única vez em que o endereço IP Público é alterado é quando o gateway é excluído e recriado. Isso não altera o redimensionamento, a redefinição ou outras manutenções/atualizações internas do seu gateway de VPN.

Use o comando [az network public-ip create](/cli/azure/network/public-ip) para solicitar um endereço IP público dinâmico.

```azurecli-interactive
az network public-ip create --name VNet1GWIP --resource-group TestRG1 --allocation-method Dynamic
```

## <a name="7-create-the-vpn-gateway"></a><a name="CreateGateway"></a>7. criar o gateway de VPN

Crie o gateway de VPN da rede virtual. A criação de um gateway de VPN pode demorar até 45 minutos ou mais para ser concluída.

Use os seguintes valores:

* O *--Gateway-Type* para uma configuração site a site é *VPN*. O tipo de gateway é sempre específico para a configuração que você está implementando. Para obter mais informações, consulte [Tipos de gateway](vpn-gateway-about-vpn-gateway-settings.md#gwtype).
* O *--vpn-type* pode ser *RouteBased* (referido como Gateway Dinâmico em alguns documentos) ou *PolicyBased* (referido como Gateway Estático em alguns documentos). A configuração é específica para os requisitos do dispositivo ao qual você está se conectando. Para obter mais informações sobre tipos de gateway de VPN, confira [Sobre definições de configuração de gateway de VPN](vpn-gateway-about-vpn-gateway-settings.md#vpntype).
* Selecione a SKU de Gateway que você deseja usar. Há limitações de configuração para alguns SKUs. Para obter mais informações, confira [SKUs de gateway](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

Criar o gateway de VPN usando o comando [az network vnet-gateway create](/cli/azure/network/vnet-gateway). Se você executar esse comando usando o parâmetro '--no-wait', você não receberá nenhum feedback ou saída. Esse parâmetro permite que o gateway seja criado em segundo plano. Demora aproximadamente 45 minutos para criar um gateway.

```azurecli-interactive
az network vnet-gateway create --name VNet1GW --public-ip-address VNet1GWIP --resource-group TestRG1 --vnet TestVNet1 --gateway-type Vpn --vpn-type RouteBased --sku VpnGw1 --no-wait 
```

## <a name="8-configure-your-vpn-device"></a><a name="VPNDevice"></a>8. configurar seu dispositivo VPN

As conexões Site a Site para uma rede local exigem um dispositivo VPN. Nesta etapa, você deve configurar seu dispositivo VPN. Ao configurar seu dispositivo VPN, você precisará dos seguintes itens:

- Uma chave compartilhada. Essa é a mesma chave compartilhada especificada ao criar a conexão VPN Site a Site. Em nossos exemplos, usamos uma chave compartilhada básica. Recomendamos gerar uma chave mais complexa para uso.
- O endereço IP público do seu gateway de rede virtual. Você pode exibir o endereço IP público usando o portal do Azure, o PowerShell ou a CLI.   Para localizar o endereço IP público do seu gateway de rede virtual, use o comando [az network public-ip list](/cli/azure/network/public-ip). Para facilitar a leitura, a saída é formatada para exibir a lista de IPs públicos em formato de tabela.

  ```azurecli-interactive
  az network public-ip list --resource-group TestRG1 --output table
  ```


[!INCLUDE [Configure VPN device](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]


## <a name="9-create-the-vpn-connection"></a><a name="CreateConnection"></a>9. criar a conexão VPN

Crie a conexão VPN Site a Site entre o gateway de rede virtual e o dispositivo VPN local. Preste atenção especial ao valor de chave compartilhada, que deve corresponder ao valor de chave compartilhada configurado para seu dispositivo VPN.

Crie a conexão usando o comando [az network vpn-connection create](/cli/azure/network/vpn-connection).

```azurecli-interactive
az network vpn-connection create --name VNet1toSite2 --resource-group TestRG1 --vnet-gateway1 VNet1GW -l eastus --shared-key abc123 --local-gateway2 Site2
```

Após um instante, a conexão será estabelecida.

## <a name="10-verify-the-vpn-connection"></a><a name="toverify"></a>10. verificar a conexão VPN

[!INCLUDE [verify connection](../../includes/vpn-gateway-verify-connection-cli-rm-include.md)]

Se quiser usar outro método para verificar a conexão, confira [Verificar uma conexão de gateway de VPN](vpn-gateway-verify-connection-resource-manager.md).

## <a name="to-connect-to-a-virtual-machine"></a><a name="connectVM"></a>Para conectar-se a uma máquina virtual

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm.md)]

## <a name="common-tasks"></a><a name="tasks"></a>Tarefas comuns

Esta seção contém os comandos comuns que são úteis ao trabalhar com configurações de site a site. Para obter a lista completa dos comandos de rede da CLI, consulte [CLI do Azure - Rede](/cli/azure/network).

[!INCLUDE [local network gateway common tasks](../../includes/vpn-gateway-common-tasks-cli-include.md)]

## <a name="next-steps"></a>Próximas etapas

* Quando sua conexão for concluída, você poderá adicionar máquinas virtuais às suas redes virtuais. Para saber mais, veja [Máquinas virtuais](../index.yml).
* Para obter informações sobre o BGP, consulte a [Visão Geral do BGP](vpn-gateway-bgp-overview.md) e [Como configurar o BGP](vpn-gateway-bgp-resource-manager-ps.md).
* Para obter informações sobre o túnel forçado, consulte [sobre o túnel forçado](vpn-gateway-forced-tunneling-rm.md).
* Para obter informações sobre Conexões Altamente Disponíveis Ativo-Ativo, consulte [Conectividade Altamente Disponível entre locais e Rede Virtual para Rede Virtual](vpn-gateway-highlyavailable.md).
* Para obter uma lista de comandos da CLI do Azure de rede, confira [CLI do Azure](/cli/azure/network).
* Para obter informações sobre como criar uma conexão VPN site a site usando Azure Resource Manager modelo, consulte [criar uma conexão VPN site a site](https://azure.microsoft.com/resources/templates/101-site-to-site-vpn-create/).
* Para obter informações sobre como criar uma conexão VPN de vnet para vnet usando Azure Resource Manager modelo, consulte [implantar a replicação geográfica do HBase](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-geo/).