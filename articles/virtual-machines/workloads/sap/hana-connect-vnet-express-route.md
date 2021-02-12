---
title: Configuração de conectividade da rede virtual para SAP HANA no Azure (instâncias grandes) | Microsoft Docs
description: Configuração de conectividade da rede virtual a ser usada SAP HANA no Azure (instâncias grandes).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c5a8a8157721f34abf7761a85febc7bcea3abb88
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967832"
---
# <a name="connect-a-virtual-network-to-hana-large-instances"></a>Conecte uma rede virtual a instâncias grandes do HANA

Depois de criar uma rede virtual do Azure, você pode conectar essa rede ao SAP HANA em instâncias grandes do Azure. Crie um gateway do Azure ExpressRoute na rede virtual. Esse Gateway permite que você vincule a rede virtual ao circuito do ExpressRoute que se conecta ao locatário do cliente no carimbo de instância grande do HANA.

> [!NOTE] 
> Essa etapa pode levar até 30 minutos para ser concluída. O novo gateway é criado na assinatura do Azure designada e, em seguida, conectado à rede virtual do Azure especificada.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

Se um gateway já existir, verifique se é um gateway do ExpressRoute ou não. Se não for um gateway de ExpressRoute, exclua o gateway e recrie-o como um gateway de ExpressRoute. Se um gateway da Rota Expressa já estiver estabelecido, consulte a seção a seguir deste artigo, "Vincular redes virtuais". 

- Use o [Portal do Azure](https://portal.azure.com/) ou o PowerShell para criar um gateway de VPN do ExpressRoute conectado à sua rede virtual.
  - Se você usar o portal do Azure, adicione um novo **Gateway de Rede Virtual** e, em seguida, selecione **ExpressRoute** como o tipo de gateway.
  - Se você usar o PowerShell, primeiro Baixe e use a versão mais recente [SDK do Azure PowerShell](https://azure.microsoft.com/downloads/). 
 
Os comandos a seguir criam um gateway de ExpressRoute. Os textos precedidos por uma _$_ são variáveis definidas pelo usuário que devem ser atualizadas com suas informações específicas.

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

A rede virtual do Azure agora tem um gateway da Rota Expressa. Use as informações de autorização fornecidas pela Microsoft para conectar o gateway do ExpressRoute ao circuito SAP HANA em Instâncias Grandes ExpressRoute. Você pode se conectar usando o portal do Azure ou o PowerShell. As instruções do PowerShell são as seguintes. 

Execute os comandos a seguir para cada gateway de ExpressRoute usando um AuthGUID diferente para cada conexão. As duas primeiras entradas mostradas no script a seguir são provenientes das informações fornecidas pela Microsoft. Além disso, o AuthGUID é específico para cada rede virtual e seu gateway. Se você quiser adicionar outra rede virtual do Azure, precisará obter outra authid para o circuito do ExpressRoute que conecta as instâncias grandes do HANA ao Azure da Microsoft. 

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
> O último parâmetro no comando New-AzVirtualNetworkGatewayConnection, **ExpressRouteGatewayBypass** é um novo parâmetro que habilita o caminho rápido do ExpressRoute. Uma funcionalidade que reduz a latência de rede entre suas unidades de instância grande do HANA e VMs do Azure. A funcionalidade foi adicionada em maio de 2019. Para obter mais detalhes, consulte o artigo [SAP Hana arquitetura de rede (instâncias grandes)](./hana-network-architecture.md). Verifique se você está executando a versão mais recente dos cmdlets do PowerShell antes de executar os comandos.

Para conectar o gateway a mais de um circuito do ExpressRoute associado à sua assinatura, talvez seja necessário executar essa etapa mais de uma vez. Por exemplo, você provavelmente conectará o mesmo gateway de rede virtual ao circuito da Rota Expressa que conecta a rede virtual à sua rede local.

## <a name="applying-expressroute-fast-path-to-existing-hana-large-instance-expressroute-circuits"></a>Aplicando o caminho rápido do ExpressRoute a circuitos de ExpressRoute de instância grande do HANA existentes
A documentação até agora explicou como conectar um novo circuito do ExpressRoute criado com uma implantação do SAP HANA em instâncias grandes em um gateway de ExpressRoute do Azure de uma das suas redes virtuais do Azure. Mas muitos clientes já têm sua configuração de circuitos do ExpressRoute e já têm suas redes virtuais conectadas ao HANA em instâncias grandes. Como o novo caminho rápido do ExpressRoute está reduzindo a latência de rede, é recomendável aplicar a alteração para usar essa funcionalidade. Os comandos para conectar um novo circuito ExpreesRoute e alterar um circuito de ExpressRoute existente são os mesmos. Como resultado, você precisa executar essa sequência de comandos do PowerShell para alterar um circuito existente a ser usado 

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

É importante que você adicione o último parâmetro, conforme exibido acima, para habilitar a funcionalidade de caminho rápido do ExpressRoute


## <a name="expressroute-global-reach"></a>Alcance Global do ExpressRoute
Como você deseja habilitar Alcance Global para um ou ambos os dois cenários:

 - Replicação do sistema HANA sem proxies ou firewalls adicionais
 - Copiando backups entre unidades de instância grande do HANA em duas regiões diferentes para executar cópias do sistema ou atualizações do sistema

Você precisa considerar que:

- Você precisa fornecer um intervalo de espaço de endereço de um espaço de endereço/29. Esse intervalo de endereços não pode se sobrepor a nenhum dos outros intervalos de espaço de endereço que você usou até agora conectar as instâncias grandes do HANA ao Azure e pode não se sobrepor a nenhum dos seus intervalos de endereços IP que você usou em outro lugar no Azure ou no local.
- Há uma limitação no ASNs (número do sistema autônomo) que pode ser usado para anunciar suas rotas locais para instâncias grandes do HANA. Seu local não deve anunciar nenhuma rota com ASNs privada no intervalo de 65000 a 65020 ou 65515. 
- Para o cenário de conexão do acesso direto local às instâncias grandes do HANA, você precisa calcular uma taxa para o circuito que conecta você ao Azure. Para preços, verifique os preços do [complemento de alcance global](https://azure.microsoft.com/pricing/details/expressroute/).

Para obter um ou ambos os cenários aplicados à sua implantação, abra uma mensagem de suporte com o Azure, conforme descrito em [abrir uma solicitação de suporte para instâncias grandes do Hana](./hana-li-portal.md#open-a-support-request-for-hana-large-instances)

Os dados necessários e as palavras-chave que você precisa usar para que a Microsoft seja capaz de rotear e executar em sua solicitação, são semelhantes a:

- Serviço: SAP HANA instância grande
- Tipo de problema: configuração e configuração
- Subtipo de problema: meu problema não está listado acima
- Assunto ' modificar minha rede-adicionar Alcance Global '
- Detalhes: ' Adicionar Alcance Global ao HANA grande instância para o locatário de instância grande do HANA ou ' Adicionar Alcance Global ao locatário de instância grande do Hana no local.
- Detalhes adicionais da instância grande do HANA para o caso de locatário de instância grande do Hana: você precisa definir as **duas regiões do Azure** em que os dois locatários se conectam estão localizados **e** você precisa enviar o **intervalo de endereços IP/29**
- Detalhes adicionais para o caso de locatário de instância grande do SAP para HANA: você precisa definir a **região do Azure** onde o locatário de instância grande do Hana está implantado que você deseja conectar diretamente. Além disso, você precisa fornecer o **GUID de autenticação** e a ID de par de **circuitos** que você recebeu quando estabeleceu o circuito de ExpressRoute entre o local e o Azure. Além disso, você precisa nomear seu **ASN**. A última entrega é um **intervalo de endereços IP/29** para o ExpressRoute alcance global.

> [!NOTE]
> Se você quiser que ambos os casos sejam manipulados, será necessário fornecer dois intervalos de endereços IP diferentes/29 que não se sobrepõem a nenhum outro intervalo de endereços IP usado até o momento. 




## <a name="next-steps"></a>Próximas etapas

- [Requisitos de rede adicionais para HLI](hana-additional-network-requirements.md)
