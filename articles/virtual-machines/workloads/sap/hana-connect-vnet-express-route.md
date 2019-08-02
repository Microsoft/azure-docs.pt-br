---
title: Conectividade configurada da rede virtual para o SAP HANA no Azure (instâncias grandes) | Microsoft Docs
description: Conectividade configurada da rede virtual para usar o SAP HANA no Azure (instâncias grandes).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ebe303b24c497fe8ac52ac90a236a23c279ea2e9
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67709669"
---
# <a name="connect-a-virtual-network-to-hana-large-instances"></a>Conecte uma rede virtual a instâncias grandes do HANA

Depois de criar uma rede virtual do Azure, você pode conectar essa rede ao SAP HANA em instâncias grandes do Azure. Crie um gateway do Azure ExpressRoute na rede virtual. Esse gateway permite que você vincular a rede virtual ao circuito de ExpressRoute que conecta-se ao locatário do cliente no carimbo de instância grande do HANA.

> [!NOTE] 
> Essa etapa pode levar até 30 minutos para ser concluída. O novo gateway é criado na assinatura do Azure designada e, em seguida, conectado à rede virtual do Azure especificada.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

Se um gateway já existir, verifique se é um gateway do ExpressRoute ou não. Se não for um gateway de ExpressRoute, exclua o gateway e recriá-la como um gateway de ExpressRoute. Se um gateway da Rota Expressa já estiver estabelecido, consulte a seção a seguir deste artigo, "Vincular redes virtuais". 

- Use o [Portal do Azure](https://portal.azure.com/) ou o PowerShell para criar um gateway de VPN do ExpressRoute conectado à sua rede virtual.
  - Se você usar o portal do Azure, adicione um novo **Gateway de Rede Virtual** e, em seguida, selecione **ExpressRoute** como o tipo de gateway.
  - Se você usar o PowerShell, primeiro Baixe e use a versão mais recente [SDK do Azure PowerShell](https://azure.microsoft.com/downloads/). 
 
Os comandos a seguir criam um gateway de ExpressRoute. Os textos precedidos por um _$_ são variáveis definidas pelo usuário que devem ser atualizadas com as informações específicas.

```powershell
# These Values should already exist, update to match your environment
$myAzureRegion = "eastus"
$myGroupName = "SAP-East-Coast"
$myVNetName = "VNet01"

# These values are used to create the gateway, update for how you wish the GW components to be named
$myGWName = "VNet01GW"
$myGWConfig = "VNet01GWConfig"
$myGWPIPName = "VNet01GWPIP"
$myGWSku = "HighPerformance" # Supported values for HANA large instances are: HighPerformance or UltraPerformance

# These Commands create the Public IP and ExpressRoute Gateway
$vnet = Get-AzVirtualNetwork -Name $myVNetName -ResourceGroupName $myGroupName
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
New-AzPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName `
-Location $myAzureRegion -AllocationMethod Dynamic
$gwpip = Get-AzPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName
$gwipconfig = New-AzVirtualNetworkGatewayIpConfig -Name $myGWConfig -SubnetId $subnet.Id `
-PublicIpAddressId $gwpip.Id

New-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName -Location $myAzureRegion `
-IpConfigurations $gwipconfig -GatewayType ExpressRoute `
-GatewaySku $myGWSku -VpnType PolicyBased -EnableBgp $true
```

Neste exemplo, a SKU de gateway HighPerformance foi usada. Suas opções são HighPerformance ou UltraPerformance como as únicas SKUs de gateway suportadas para o SAP HANA no Azure (grandes instâncias).

> [!IMPORTANT]
> Para instâncias grandes do HANA do SKU de classe Tipo II, você deve usar o SKU do Gateway UltraPerformance.

## <a name="link-virtual-networks"></a>Vincular redes virtuais

A rede virtual do Azure agora tem um gateway da Rota Expressa. Use as informações de autorização fornecidas pela Microsoft para conectar o gateway de ExpressRoute ao circuito de ExpressRoute de instâncias grandes do SAP HANA. Você pode se conectar usando o portal do Azure ou o PowerShell. As instruções do PowerShell são da seguinte maneira. 

Execute os seguintes comandos para cada gateway de ExpressRoute usando um AuthGUID diferente para cada conexão. As duas primeiras entradas mostradas no script a seguir são provenientes das informações fornecidas pela Microsoft. Além disso, o AuthGUID é específico para cada rede virtual e seu gateway. Se você quiser adicionar outra rede virtual do Azure, você precisa obter outro AuthID para o circuito de ExpressRoute que conecta HANA grandes instâncias no Azure da Microsoft. 

```powershell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID -ExpressRouteGatewayBypass
```

> [!NOTE]
> O último parâmetro no comando New-AzVirtualNetworkGatewayConnection **ExpressRouteGatewayBypass** é um novo parâmetro que permite que o caminho rápido de ExpressRoute. Uma funcionalidade que reduz a latência de rede entre suas unidades de instância grande do HANA e as VMs do Azure. A funcionalidade foi adicionada em maio de 2019. Para obter mais detalhes, verifique o artigo [arquitetura de rede do SAP HANA (instâncias grandes)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture). Certifique-se de que você está executando a versão mais recente dos cmdlets do PowerShell antes de executar os comandos.

Para conectar o gateway a mais de um circuito do ExpressRoute associado à sua assinatura, talvez seja necessário executar essa etapa mais de uma vez. Por exemplo, você provavelmente conectará o mesmo gateway de rede virtual ao circuito da Rota Expressa que conecta a rede virtual à sua rede local.

## <a name="applying-expressroute-fast-path-to-existing-hana-large-instance-expressroute-circuits"></a>Aplicação de caminho rápido de ExpressRoute para circuitos do ExpressRoute de instância grande do HANA
Até agora, a documentação explicou como se conectar a um novo circuito de ExpressRoute que foi criado com uma implantação de instância grande do HANA a um gateway de ExpressRoute do Azure de uma das suas redes virtuais do Azure. Mas muitos clientes já tiverem sua configuração de circuitos do ExpressRoute e tem suas redes virtuais conectadas a instâncias grandes HANA já. Como o novo ExpressRoute Fast caminho está reduzindo a latência de rede, é recomendável que você aplique a alteração para usar essa funcionalidade. Os comandos para se conectar a um novo circuito ExpreesRoute e alterar um circuito de ExpressRoute existente são os mesmos. Como resultado, você precisará executar essa sequência de comandos do PowerShell para alterar um circuito existente para usar 

```powershell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID -ExpressRouteGatewayBypass
```

É importante que você adicione o último parâmetro, conforme exibido acima para habilitar a funcionalidade de caminho rápido de ExpressRoute


## <a name="expressroute-global-reach"></a>Alcance Global de ExpressRoute
Como você deseja habilitar o alcance Global para um ou ambos os dois cenários:

 - Replicação de sistema do HANA sem outros proxies ou firewalls
 - Cópia de backups entre unidades de instância grande do HANA em duas regiões diferentes para executar cópias do sistema ou atualizações do sistema

Você precisa considerar que:

- Você precisa fornecer um intervalo de espaço de endereço de uma/29 espaço de endereço. Que intervalo de endereços não pode se sobrepor com qualquer um dos outros intervalos de endereços espaço que você usou até agora conectar a instâncias grandes do HANA com o Azure e não pode se sobrepor com qualquer um dos seus intervalos de endereços IP é usado em outro lugar no Azure ou no local.
- Há uma limitação no ASNs (número de sistema autônomo) que pode ser usado para anunciar rotas locais para instâncias grandes do HANA. Seu local não deve anunciar todas as rotas com ASNs privadas no intervalo de 65000 – 65020 ou 65515. 
- Para o cenário de conexão no local acesso direto a instâncias grandes do HANA, você precisa calcular uma taxa para o circuito que conecta você ao Azure. Para obter os preços, verifique os preços para [complemento de alcance Global](https://azure.microsoft.com/pricing/details/expressroute/).

Para obter um ou ambos os cenários aplicados à sua implantação, abra uma mensagem de suporte com o Azure, conforme descrito em [abrir uma solicitação de suporte para instâncias grandes HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#open-a-support-request-for-hana-large-instances)

Dados que são necessários e palavras-chave que você precisa usar para a Microsoft para poder rotear e executar em sua solicitação é semelhante a:

- Serviço: Instância Grande do SAP HANA
- Tipo de problema: Configuração e instalação
- Subtipo de problema: Meu problema não está listado acima
- Assunto 'Modificar minha rede – adicionar alcance Global'
- Detalhes: ' Adicionar alcance Global de instância grande do HANA para o locatário de instância grande do HANA ou ' Adicionar o recurso de alcance Global local ao locatário de instância grande do HANA.
- Detalhes adicionais de instância grande do HANA ao caso de locatário de instância grande do HANA: Você precisa definir a **duas regiões do Azure** onde se encontram os dois locatários conectem **AND** você precisa enviar o   **/29 intervalo de endereços IP**
- Detalhes adicionais para o local para o caso de locatário de instância grande do HANA: Você precisa definir a **região do Azure** onde o locatário de instância grande do HANA é implantado você deseja se conectar diretamente. Além disso, você precisará fornecer as **Auth GUID** e **ID do circuito par** que você recebeu ao estabelecer seu circuito do ExpressRoute entre local e o Azure. Além disso, você precisa nomear sua **ASN**. A última entrega é um **/29 intervalo de endereços IP** para alcance Global do ExpressRoute.

> [!NOTE]
> Se você quiser ter ambos os casos tratados, você precisará fornecer duas diferentes/29 intervalos de endereços IP que não se sobrepõem com qualquer outro IP usado até o momento de intervalo de endereços. 




## <a name="next-steps"></a>Próximas etapas

- [Requisitos de rede adicionais para HLI](hana-additional-network-requirements.md)
