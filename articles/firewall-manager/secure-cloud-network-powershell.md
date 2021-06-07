---
title: 'Tutorial: Proteger seu hub virtual usando o Azure PowerShell'
description: Este artigo descreve como criar uma WAN Virtual do Azure em uma região com o Firewall do Azure habilitado no hub.
services: firewall-manager
author: jomore
ms.topic: tutorial
ms.service: firewall-manager
ms.date: 10/22/2020
ms.author: victorh
ms.openlocfilehash: 5ef39118d16d47aeb46294658adf616515547ea6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105729517"
---
# <a name="tutorial-secure-your-virtual-hub-using-azure-powershell"></a>Tutorial: Proteger seu hub virtual usando o Azure PowerShell

Neste tutorial, você criará uma instância da WAN Virtual com um Hub Virtual em uma região e implantará um Firewall do Azure no Hub Virtual para proteger a conectividade. Neste exemplo, você demonstra a conectividade segura entre Redes Virtuais. O tráfego entre redes virtuais e branches site a site, ponto a site ou do ExpressRoute também tem suporte do hub seguro virtual.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Implantar a WAN Virtual
> * Implantar o Firewall do Azure e configurar o roteamento personalizado
> * Testar a conectividade

## <a name="prerequisites"></a>Pré-requisitos

- Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

- PowerShell 7

   Este tutorial requer que você execute o Azure PowerShell localmente no PowerShell 7. Para instalar o PowerShell 7, confira [Como migrar do Windows PowerShell 5.1 para o PowerShell 7](/powershell/scripting/install/migrating-from-windows-powershell-51-to-powershell-7?view=powershell-7&preserve-view=true).
- Az.Network versão 3.2.0

    Se você tiver o Az.Network versão 3.4.0 ou posterior, será necessário fazer downgrade para usar alguns comandos neste tutorial. Você pode verificar a versão do módulo Az.Network com o comando `Get-InstalledModule -Name Az.Network`. Para desinstalar o módulo Az.Network, execute `Uninstall-Module -name az.network`. Para instalar o módulo Az.Network 3.2.0, execute `Install-Module az.network -RequiredVersion 3.2.0 -force`.

## <a name="sign-in-to-azure"></a>Entrar no Azure

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="initial-virtual-wan-deployment"></a>Implantação inicial da WAN Virtual

Como primeira etapa, você precisa definir algumas variáveis e criar o grupo de recursos, a instância da WAN Virtual e o hub virtual:

```azurepowershell
# Variable definition
$RG = "vwan-rg"
$Location = "westeurope"
$VwanName = "vwan"
$HubName =  "hub1"
# Create Resource Group, Virtual WAN and Virtual Hub
New-AzResourceGroup -Name $RG -Location $Location
$Vwan = New-AzVirtualWan -Name $VwanName -ResourceGroupName $RG -Location $Location -AllowVnetToVnetTraffic -AllowBranchToBranchTraffic -VirtualWANType "Standard"
$Hub = New-AzVirtualHub -Name $HubName -ResourceGroupName $RG -VirtualWan $Vwan -Location $Location -AddressPrefix "192.168.1.0/24" -Sku "Standard"
```

Crie duas redes virtuais e conecte-as ao hub como spokes:

```azurepowershell
# Create Virtual Network
$Spoke1 = New-AzVirtualNetwork -Name "spoke1" -ResourceGroupName $RG -Location $Location -AddressPrefix "10.1.1.0/24"
$Spoke2 = New-AzVirtualNetwork -Name "spoke2" -ResourceGroupName $RG -Location $Location -AddressPrefix "10.1.2.0/24"
# Connect Virtual Network to Virtual WAN
$Spoke1Connection = New-AzVirtualHubVnetConnection -ResourceGroupName $RG -ParentResourceName  $HubName -Name "spoke1" -RemoteVirtualNetwork $Spoke1
$Spoke2Connection = New-AzVirtualHubVnetConnection -ResourceGroupName $RG -ParentResourceName  $HubName -Name "spoke2" -RemoteVirtualNetwork $Spoke2
```

Neste ponto, você tem uma WAN Virtual totalmente funcional fornecendo conectividade any-to-any. Para aprimorá-la com a segurança, você precisa implantar um Firewall do Azure para cada Hub Virtual. As Políticas de Firewall podem ser usadas para gerenciar com eficiência a instância de Firewall do Azure da WAN Virtual. Portanto, uma política de firewall também é criada neste exemplo:

```azurepowershell
# New Firewall Policy
$FWPolicy = New-AzFirewallPolicy -Name "VwanFwPolicy" -ResourceGroupName $RG -Location $Location
# New Firewall Public IP
$AzFWPIPs = New-AzFirewallHubPublicIpAddress -Count 1
$AzFWHubIPs = New-AzFirewallHubIpAddress -PublicIP $AzFWPIPs
# New Firewall
$AzFW = New-AzFirewall -Name "azfw1" -ResourceGroupName $RG -Location $Location `
            -VirtualHubId $Hub.Id -FirewallPolicyId $FWPolicy.Id `
            -Sku AZFW_Hub -HubIPAddress $AzFWHubIPs
```

Habilitar o registro em log do Firewall do Azure para Azure Monitor é opcional, mas, neste exemplo, você usa os logs de firewall para provar que o tráfego está atravessando o firewall:

```azurepowershell
# Optionally, enable looging of Azure Firewall to Azure Monitor
$LogWSName = "vwan-" + (Get-Random -Maximum 99999) + "-" + $RG
$LogWS = New-AzOperationalInsightsWorkspace -Location $Location -Name $LogWSName -Sku Standard -ResourceGroupName $RG
Set-AzDiagnosticSetting -ResourceId $AzFW.Id -Enabled $True -Category AzureFirewallApplicationRule, AzureFirewallNetworkRule -WorkspaceId $LogWS.ResourceId
```

## <a name="deploy-azure-firewall-and-configure-custom-routing"></a>Implantar o Firewall do Azure e configurar o roteamento personalizado

Agora que você tem um Firewall do Azure no hub, mas ainda precisa modificar o roteamento para que a WAN Virtual envie o tráfego das redes virtuais e dos branches por meio do firewall. Faça isso em duas etapas:

1. Configure todas as conexões de rede virtual (e as conexões de branch, se houver alguma) para fazer a propagação para a Tabela de Rotas `None`. O efeito dessa configuração é que outras redes virtuais e branches não aprenderão os prefixos deles e, portanto, ela não tem roteamento para acessá-los.
1. Agora, você pode inserir rotas estáticas na Tabela de Rotas `Default` (em que todas as redes virtuais e branches estão associados por padrão), para que todo o tráfego seja enviado para o Firewall do Azure.

> [!NOTE]
> Esta é a configuração implantada quando você protege a conectividade do portal do Azure com o Gerenciador de Firewall do Azure

Comece pela primeira etapa para configurar suas conexões de rede virtual para fazer a propagação para a Tabela de Rotas `None`:

```azurepowershell
# Configure Virtual Network connections in hub to propagate to None
$VnetRoutingConfig = $Spoke1Connection.RoutingConfiguration    # We take $Spoke1Connection as baseline for the future vnet config, all vnets will have an identical config
$NoneRT = Get-AzVhubRouteTable -ResourceGroupName $RG -HubName $HubName -Name "noneRouteTable"
$NewPropRT = @{}
$NewPropRT.Add('Id', $NoneRT.id)
$PropRTList = @()
$PropRTList += $NewPropRT
$VnetRoutingConfig.PropagatedRouteTables.Ids = $PropRTList
$VnetRoutingConfig.PropagatedRouteTables.Labels = @()
$Spoke1Connection = Update-AzVirtualHubVnetConnection -ResourceGroupName $RG -ParentResourceName  $HubName -Name "spoke1" -RoutingConfiguration $VnetRoutingConfig
$Spoke2Connection = Update-AzVirtualHubVnetConnection -ResourceGroupName $RG -ParentResourceName  $HubName -Name "spoke2" -RoutingConfiguration $VnetRoutingConfig
```

Agora, você pode passar para a segunda etapa e adicionar as rotas estáticas à tabela de rotas `Default`. Neste exemplo, você aplica a configuração padrão que o Gerenciador de Firewall do Azure geraria ao proteger a conectividade em uma WAN Virtual, mas você pode alterar a lista de prefixos na rota estática para se adequarem aos seus requisitos específicos:

```azurepowershell
# Create static routes in default Route table
$AzFWId = $(Get-AzVirtualHub -ResourceGroupName $RG -name  $HubName).AzureFirewall.Id
$AzFWRoute = New-AzVHubRoute -Name "private-traffic" -Destination @("0.0.0.0/0", "10.0.0.0/8", "172.16.0.0/12", "192.168.0.0/16") -DestinationType "CIDR" -NextHop $AzFWId -NextHopType "ResourceId"
$DefaultRT = Update-AzVHubRouteTable -Name "defaultRouteTable" -ResourceGroupName $RG -VirtualHubName  $HubName -Route @($AzFWRoute)
```

## <a name="test-connectivity"></a>Testar a conectividade

Agora você tem um hub seguro totalmente operacional. Para testar a conectividade, você precisa de uma máquina virtual em cada rede virtual spoke conectada ao hub:

```azurepowershell
# Create VMs in spokes for testing
$VMLocalAdminUser = "lab-user"
$VMLocalAdminSecurePassword = ConvertTo-SecureString -AsPlainText -Force
$VMCredential = New-Object System.Management.Automation.PSCredential ($VMLocalAdminUser, $VMLocalAdminSecurePassword);
$VMSize = "Standard_B2ms"
# Spoke1
$Spoke1 = Get-AzVirtualNetwork -ResourceGroupName $RG -Name "spoke1"
Add-AzVirtualNetworkSubnetConfig -Name "vm" -VirtualNetwork $Spoke1 -AddressPrefix "10.1.1.0/26"
$Spoke1 | Set-AzVirtualNetwork
$VM1 = New-AzVM -Name "spoke1-vm" -ResourceGroupName $RG -Location $Location `
            -Image "UbuntuLTS" -credential $VMCredential `
            -VirtualNetworkName "spoke1" -SubnetName "vm" -PublicIpAddressName "spoke1-pip"
$NIC1 = Get-AzNetworkInterface -ResourceId $($VM1.NetworkProfile.NetworkInterfaces[0].Id)
$Spoke1VMPrivateIP = $NIC1.IpConfigurations[0].PrivateIpAddress
$Spoke1VMPIP = $(Get-AzPublicIpAddress -ResourceGroupName $RG -Name "spoke1-pip")
# Spoke2
$Spoke2 = Get-AzVirtualNetwork -ResourceGroupName $RG -Name "spoke2"
Add-AzVirtualNetworkSubnetConfig -Name "vm" -VirtualNetwork $Spoke2 -AddressPrefix "10.1.2.0/26"
$Spoke2 | Set-AzVirtualNetwork
$VM2 = New-AzVM -Name "spoke2-vm" -ResourceGroupName $RG -Location $Location `
            -Image "UbuntuLTS" -credential $VMCredential `
            -VirtualNetworkName "spoke2" -SubnetName "vm" -PublicIpAddressName "spoke2-pip"
$NIC2 = Get-AzNetworkInterface -ResourceId $($VM2.NetworkProfile.NetworkInterfaces[0].Id)
$Spoke2VMPrivateIP = $NIC2.IpConfigurations[0].PrivateIpAddress
$Spoke2VMPIP = $(Get-AzPublicIpAddress -ResourceGroupName $RG -Name "spoke2-pip")
```

A configuração padrão na política de firewall é descartar tudo. Portanto, você precisa de algumas regras. Comece com as regras DNAT para que as máquinas virtuais de teste estejam acessíveis no endereço IP público do firewall:

```azurepowershell
# Adding DNAT rules for virtual machines in the spokes
$AzFWPublicAddress = $AzFW.HubIPAddresses.PublicIPs.Addresses[0].Address
$NATRuleSpoke1 = New-AzFirewallPolicyNatRule -Name "Spoke1SSH" -Protocol "TCP" `
        -SourceAddress "*" -DestinationAddress $AzFWPublicAddress -DestinationPort 10001 `
        -TranslatedAddress $Spoke1VMPrivateIP -TranslatedPort 22
$NATRuleSpoke2 = New-AzFirewallPolicyNatRule -Name "Spoke2SSH" -Protocol "TCP" `
        -SourceAddress "*" -DestinationAddress $AzFWPublicAddress -DestinationPort 10002 `
        -TranslatedAddress $Spoke2VMPrivateIP -TranslatedPort 22
$NATCollection = New-AzFirewallPolicyNatRuleCollection -Name "SSH" -Priority 100 `
        -Rule @($NATRuleSpoke1, $NATRuleSpoke2) -ActionType "Dnat"
$NATGroup = New-AzFirewallPolicyRuleCollectionGroup -Name "NAT" -Priority 100 -RuleCollection $NATCollection -FirewallPolicyObject $FWPolicy
```

Agora, você pode configurar algumas regras de exemplo. Defina uma regra de rede que permita o tráfego SSH, além de uma regra de aplicativo que permita o acesso da Internet ao Nome de Domínio Totalmente Qualificado `ifconfig.co`. Essa URL retorna o endereço IP de origem que vê na solicitação HTTP:

```azurepowershell
# Add Network Rule
$SSHRule = New-AzFirewallPolicyNetworkRule -Name PermitSSH -Protocol TCP `
        -SourceAddress "10.0.0.0/8" -DestinationAddress "10.0.0.0/8" -DestinationPort 22
$NetCollection = New-AzFirewallPolicyFilterRuleCollection -Name "Management" -Priority 100 -ActionType Allow -Rule $SSHRule
$NetGroup = New-AzFirewallPolicyRuleCollectionGroup -Name "Management" -Priority 200 -RuleCollection $NetCollection -FirewallPolicyObject $FWPolicy
# Add Application Rul
$ifconfigRule = New-AzFirewallPolicyApplicationRule -Name PermitIfconfig -SourceAddress "10.0.0.0/8" -TargetFqdn "ifconfig.co" -Protocol "http:80","https:443"
$AppCollection = New-AzFirewallPolicyFilterRuleCollection -Name "TargetURLs" -Priority 300 -ActionType Allow -Rule $ifconfigRule
$NetGroup = New-AzFirewallPolicyRuleCollectionGroup -Name "TargetURLs" -Priority 300 -RuleCollection $AppCollection -FirewallPolicyObject $FWPolicy
```

Antes de enviar qualquer tráfego, você pode inspecionar as rotas efetivas das máquinas virtuais. Eles devem conter os prefixos aprendidos da WAN Virtual (`0.0.0.0/0` mais RFC1918), mas não o prefixo do outro spoke:

```azurepowershell
# Check effective routes in the VM NIC in spoke 1
# Note that 10.1.2.0/24 (the prefix for spoke2) should not appear
Get-AzEffectiveRouteTable -ResourceGroupName $RG -NetworkInterfaceName $NIC1.Name | ft
# Check effective routes in the VM NIC in spoke 2
# Note that 10.1.1.0/24 (the prefix for spoke1) should not appear
Get-AzEffectiveRouteTable -ResourceGroupName $RG -NetworkInterfaceName $NIC2.Name | ft
```

Agora, gere o tráfego de uma máquina virtual para outra e verifique se ele foi descartado no Firewall do Azure. Nos comandos SSH a seguir, você precisa aceitar as impressões digitais das máquinas virtuais e fornecer a senha que definiu quando criou as máquinas virtuais. Neste exemplo, você vai enviar cinco pacotes de solicitação de eco ICMP da máquina virtual em spoke1 para spoke2, além de uma tentativa de conexão TCP na porta 22 usando o utilitário do Linux `nc` (com os sinalizadores `-vz`, ele apenas envia uma solicitação de conexão e mostra o resultado). Você deve ver que ocorre falha no ping e êxito na tentativa de conexão TCP na porta 22, pois ela é permitida pela regra de rede configurada anteriormente:

```azurepowershell
# Connect to one VM and ping the other. It shouldnt work, because the firewall should drop the traffic, since no rule for ICMP is configured
ssh $AzFWPublicAddress -p 10001 -l $VMLocalAdminUser "ping $Spoke2VMPrivateIP -c 5"
# Connect to one VM and send a TCP request on port 22 to the other. It should work, because the firewall is configured to allow SSH traffic (port 22)
ssh $AzFWPublicAddress -p 10001 -l $VMLocalAdminUser "nc -vz $Spoke2VMPrivateIP 22"
```

Você também pode verificar o tráfego da Internet. As solicitações HTTP por meio do utilitário `curl` para o FQDN permitido na política de firewall (`ifconfig.co`) devem funcionar, mas as solicitações HTTP para qualquer outro destino devem falhar (neste exemplo, você faz o teste com `bing.com`):

```azurepowershell
# This HTTP request should succeed, since it is allowed in an app rule in the AzFW, and return the public IP of the FW
ssh $AzFWPublicAddress -p 10001 -l $VMLocalAdminUser "curl -s4 ifconfig.co"
# This HTTP request should fail, since the FQDN bing.com is not in any app rule in the firewall policy
ssh $AzFWPublicAddress -p 10001 -l $VMLocalAdminUser "curl -s4 bing.com"
```

A maneira mais fácil de verificar se os pacotes são descartados pelo firewall é verificar os logs. Como você configurou o Firewall do Azure para enviar logs para o Azure Monitor, você pode usar a Linguagem da Consulta Kusto para recuperar os logs relevantes do Azure Monitor:

> [!NOTE]
> Pode levar cerca de 1 minuto para que os logs apareçam para serem enviados para o Azure Monitor

```azurepowershell
# Getting Azure Firewall network rule Logs
$LogWS = Get-AzOperationalInsightsWorkspace -ResourceGroupName $RG
$LogQuery = 'AzureDiagnostics 
| where Category == "AzureFirewallNetworkRule" 
| where TimeGenerated >= ago(5m) 
| parse msg_s with Protocol " request from " SourceIP ":" SourcePortInt:int " to " TargetIP ":" TargetPortInt:int * 
| parse msg_s with * ". Action: " Action1a 
| parse msg_s with * " was " Action1b " to " NatDestination 
| parse msg_s with Protocol2 " request from " SourceIP2 " to " TargetIP2 ". Action: " Action2 
| extend SourcePort = tostring(SourcePortInt),TargetPort = tostring(TargetPortInt) 
| extend Action = case(Action1a == "", case(Action1b == "",Action2,Action1b), Action1a),Protocol = case(Protocol == "", Protocol2, Protocol),SourceIP = case(SourceIP == "", SourceIP2, SourceIP),TargetIP = case(TargetIP == "", TargetIP2, TargetIP),SourcePort = case(SourcePort == "", "N/A", SourcePort),TargetPort = case(TargetPort == "", "N/A", TargetPort),NatDestination = case(NatDestination == "", "N/A", NatDestination) 
| project TimeGenerated, Protocol, SourceIP,SourcePort,TargetIP,TargetPort,Action, NatDestination, Resource 
| take 25 '
$(Invoke-AzOperationalInsightsQuery -Workspace $LogWS -Query $LogQuery).Results | ft
```

No comando anterior, você deve ver entradas diferentes:

* O DNAT de sua conexão SSH está sendo feito
* Pacotes ICMP descartados entre as VMs nos spokes (10.1.1.4 e 10.1.2.4)
* Conexões SSH permitidas entre as VMs nos spokes

Veja um exemplo de saída produzido pelo comando acima:

```
TimeGenerated            Protocol    SourceIP       SourcePort TargetIP      TargetPort Action  NatDestination Resource
-------------            --------    --------       ---------- --------      ---------- ------  -------------- --------
2020-10-04T20:53:02.41Z  TCP         109.125.122.99 62281      51.105.224.44 10001      DNAT'ed 10.1.1.4:22    AZFW1
2020-10-04T20:53:07.045Z TCP         10.1.1.4       35932      10.1.2.4      22         Allow   N/A            AZFW1
2020-10-04T20:53:50.119Z TCP         109.125.122.99 62293      51.105.224.44 10001      DNAT'ed 10.1.2.4:22    AZFW1
2020-10-04T20:52:47.475Z TCP         109.125.122.99 62273      51.105.224.44 10001      DNAT'ed 10.1.2.4:22    AZFW1
2020-10-04T20:51:04.682Z TCP         109.125.122.99 62200      51.105.224.44 10001      DNAT'ed 10.1.2.4:22    AZFW1
2020-10-04T20:51:17.031Z ICMP Type=8 10.1.1.4       N/A        10.1.2.4      N/A        Deny    N/A            AZFW1
2020-10-04T20:51:18.049Z ICMP Type=8 10.1.1.4       N/A        10.1.2.4      N/A        Deny    N/A            AZFW1
2020-10-04T20:51:19.075Z ICMP Type=8 10.1.1.4       N/A        10.1.2.4      N/A        Deny    N/A            AZFW1
2020-10-04T20:51:20.097Z ICMP Type=8 10.1.1.4       N/A        10.1.2.4      N/A        Deny    N/A            AZFW1
2020-10-04T20:51:21.121Z ICMP Type=8 10.1.1.4       N/A        10.1.2.4      N/A        Deny    N/A            AZFW1
2020-10-04T20:52:52.356Z TCP         10.1.1.4       53748      10.1.2.4      22         Allow   N/A            AZFW1
```

Se você quiser ver os logs das regras de aplicativo (descrevendo conexões HTTP permitidas e negadas) ou alterar a forma como os logs são exibidos, você poderá tentar fazer isso com outras consultas KQL. Você pode encontrar alguns exemplos em [logs do Azure Monitor para o Firewall do Azure](../firewall/firewall-workbook.md).


## <a name="clean-up-resources"></a>Limpar os recursos

Para excluir o ambiente de teste, você pode remover o grupo de recursos com todos os objetos contidos:

```azurepowershell
# Delete resource group and all contained resources
Remove-AzResourceGroup -Name $RG
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba mais sobre parceiros de segurança confiáveis](trusted-security-partners.md)