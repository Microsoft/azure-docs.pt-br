---
title: Criar um ponto de extremidade privado usando o Azure PowerShell
description: Criar um ponto de extremidade privado para o Atestado do Azure usando o Azure PowerShell
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 03/26/2021
ms.author: mbaldwin
ms.openlocfilehash: 8ff2e73a8557c6b1761c852ac58a46037a122ddb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105628519"
---
# <a name="quickstart-create-a-private-endpoint-using-azure-powershell"></a>Início Rápido: Criar um ponto de extremidade privado usando o Azure PowerShell

Introdução ao Link Privado do Azure usando um ponto de extremidade privado para se conectar com segurança ao Atestado do Azure.

Neste guia de início rápido, você criará um ponto de extremidade privado para o Atestado do Azure e implantará uma máquina virtual para testar a conexão privada.  

> [!NOTE]
> A implementação atual inclui apenas a opção de aprovação automática. A assinatura precisa estar em uma lista de permitidos para que seja possível continuar com a criação do ponto de extremidade privado. Entre em contato com a equipe de serviço ou envie uma solicitação de suporte do Azure na [página de suporte do Azure](https://azure.microsoft.com/support/options/), antes de prosseguir com as etapas a seguir.

## <a name="prerequisites"></a>Pré-requisitos

* Saiba mais sobre o [Link Privado do Azure](../private-link/private-link-overview.md)
* [Configurar o Atestado do Azure com o Azure PowerShell](./quickstart-powershell.md)

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

Crie um grupo de recursos com [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup):

```azurepowershell-interactive
## Create to your Azure account subscription and create a resource group in a desired location. ##
Connect-AzAccount
Set-AzSubscription “mySubscription”
$rg = “CreateAttestationPrivateLinkTutorial-rg”
$loc= "eastus”
New-AzResourceGroup -Name $rg -Location $loc
```

## <a name="create-a-virtual-network-and-bastion-host"></a>Criar uma rede virtual e um bastion host

Nesta seção, você criará uma rede virtual, uma sub-rede e um bastion host. 

O bastion host será usado para se conectar com segurança à máquina virtual para testar o ponto de extremidade privado.

Criar uma rede virtual e um bastion host com:

* [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)
* [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)
* [New-AzBastion](/powershell/module/az.network/new-azbastion)

```azurepowershell-interactive
## Create backend subnet config. ##
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name myBackendSubnet -AddressPrefix 10.0.0.0/24

## Create Azure Bastion subnet. ##
$bastsubnetConfig = New-AzVirtualNetworkSubnetConfig -Name AzureBastionSubnet -AddressPrefix 10.0.1.0/24

## Create the virtual network. ##
$vnet = New-AzVirtualNetwork -Name "myAttestationTutorialVNet" -ResourceGroupName $rg -Location $loc -AddressPrefix "10.0.0.0/16" -Subnet $subnetConfig, $bastsubnetConfig

## Create public IP address for bastion host. ##
$publicip = New-AzPublicIpAddress -Name "myBastionIP" -ResourceGroupName $rg -Location $loc -Sku "Standard" -AllocationMethod "Static"

## Create bastion host ##
New-AzBastion -ResourceGroupName $rg -Name "myBastion" -PublicIpAddress $publicip -VirtualNetwork $vnet
```

Levará alguns minutos para que o host do Azure Bastion seja implantado.

## <a name="create-test-virtual-machine"></a>Criar máquina virtual de teste

Nesta seção, você criará uma máquina virtual que será usada para testar o ponto de extremidade privado.

Crie a máquina virtual com:

  * [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential)
  * [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) 
  * [New-AzVM](/powershell/module/az.compute/new-azvm)
  * [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
  * [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
  * [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
  * [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)


```azurepowershell-interactive
## Set credentials for server admin and password. ##
$cred = Get-Credential

## Command to create network interface for VM ##
$nicVM = New-AzNetworkInterface -Name "myNicVM" -ResourceGroupName $rg -Location $loc -Subnet $vnet.Subnets[0] 

## Create a virtual machine configuration.##
$vmConfig = New-AzVMConfig -VMName "myVM" -VMSize "Standard_DS1_v2" | Set-AzVMOperatingSystem -Windows -ComputerName "myVM" -Credential $cred | Set-AzVMSourceImage -PublisherName "MicrosoftWindowsServer" -Offer "WindowsServer" -Skus "2019-Datacenter" -Version "latest" | Add-AzVMNetworkInterface -Id $nicVM.Id 

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Location $loc -VM $vmConfig
```

## <a name="create-an-attestation-provider"></a>Criar um provedor de atestado

```azurepowershell-interactive
## Create an attestation provider ##
$attestationProviderName = "myattestationprovider"
$attestationProvider = New-AzAttestation -Name $attestationProviderName -ResourceGroupName $rg -Location $loc
$attestationProviderId = $attestationProvider.Id

## Access the attestation provider from local machine ##
Enter nslookup <provider-name>.attest.azure.net. Replace <provider-name> with the name of the attestation provider instance you created in the previous steps. 

You'll receive a message similar to what is displayed below:

## PowerShell copy. ##
nslookup myattestationprovider.eus.attest.azure.net

Server:  cdns01.comcast.net
Address:  2001:558:feed::1

Non-authoritative answer:
Name:    eus.service.attest.azure.net
Address:  20.62.219.160
Aliases:  myattestationprovider.eus.attest.azure.net
    attesteusatm.trafficmanager.net
```

## <a name="create-private-endpoint"></a>Criar um ponto de extremidade privado

Nesta seção, você criará o ponto de extremidade privado e a conexão usando:

* [New-AzPrivateLinkServiceConnection](/powershell/module/az.network/New-AzPrivateLinkServiceConnection)
* [New-AzPrivateEndpoint](/powershell/module/az.network/new-azprivateendpoint)

```azurepowershell-interactive
## Create private endpoint connection. ##
$privateEndpointConnection = New-AzPrivateLinkServiceConnection -Name "myConnection" -PrivateLinkServiceId $attestationProviderId -GroupID "Standard"

## Disable private endpoint network policy ##
 $vnet.Subnets[0].PrivateEndpointNetworkPolicies = "Disabled" 
$vnet | Set-AzVirtualNetwork

## Create private endpoint
New-AzPrivateEndpoint  -ResourceGroupName $rg -Name "myPrivateEndpoint" -Location $loc -Subnet $vnet.Subnets[0] -PrivateLinkServiceConnection $privateEndpointConnection
```
## <a name="configure-the-private-dns-zone"></a>Configurar a zona DNS privada

Nesta seção, você criará e configurará a zona DNS privada usando:

* [New-AzPrivateDnsZone](/powershell/module/az.privatedns/new-azprivatednszone)
* [New-AzPrivateDnsVirtualNetworkLink](/powershell/module/az.privatedns/new-azprivatednsvirtualnetworklink)
* [New-AzPrivateDnsZoneConfig](/powershell/module/az.network/new-azprivatednszoneconfig)
* [New-AzPrivateDnsZoneGroup](/powershell/module/az.network/new-azprivatednszonegroup)

```azurepowershell-interactive
## Create private dns zone. ##
$zone = New-AzPrivateDnsZone -ResourceGroupName $rg -Name "privatelink.attest.azure.net"

## Create dns network link. ##
$link = New-AzPrivateDnsVirtualNetworkLink -ResourceGroupName $rg -ZoneName "privatelink.attest.azure.net" -Name "myLink" -VirtualNetworkId $vnet.Id

## Create DNS configuration ##
$config = New-AzPrivateDnsZoneConfig -Name "privatelink.attest.azure.net" -PrivateDnsZoneId $zone.ResourceId

## Create DNS zone group. ##
New-AzPrivateDnsZoneGroup -ResourceGroupName $rg -PrivateEndpointName "myPrivateEndpoint" -Name "myZoneGroup" -PrivateDnsZoneConfig $config
```

## <a name="test-connectivity-to-private-endpoint"></a>Testar a conectividade com o ponto de extremidade privado

Nesta seção, você usará a máquina virtual criada na etapa anterior para se conectar ao SQL Server pelo ponto de extremidade privado.

1. Entre no [Portal do Azure](https://portal.azure.com) 
 
2. Selecione **Grupos de recursos** no painel de navegação à esquerda.

3. Selecione **CreateAttestationPrivateLinkTutorial-rg**.

4. Selecione **myVM**.

5. Na página de visão geral de **myVM**, escolha **Conectar** e **Bastion**.

6. Selecione o botão azul **Usar Bastion**.

7. Insira o nome de usuário e a senha que você inseriu durante a criação da máquina virtual.

8. Abra o Windows PowerShell no servidor depois de se conectar.

9. Digite `nslookup <provider-name>.attest.azure.net`. Substitua **\<provider-name>** pelo nome da instância do provedor de atestado que você criou nas etapas anteriores. Você receberá uma mensagem semelhante à mostrada abaixo:

    ```powershell

    ## Access the attestation provider from local machine ##
    nslookup myattestationprovider.eus.attest.azure.net

    Server:  cdns01.comcast.net
    Address:  2001:558:feed::1

    cdns01.comcast.net can't find myattestationprovider.eus.attest.azure.net: Non-existent domain

    ## Access the attestation provider from the VM created in the same virtual network as the private endpoint.   ##
    nslookup myattestationprovider.eus.attest.azure.net

    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myattestationprovider.eastus.test.attest.azure.net
    ```
