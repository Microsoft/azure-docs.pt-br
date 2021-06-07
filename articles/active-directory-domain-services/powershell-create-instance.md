---
title: Habilitar o Azure DS Domain Services usando o PowerShell | Microsoft Docs
description: Saiba como configurar e habilitar o Azure Active Directory Domain Services usando o PowerShell do Azure AD e o Azure PowerShell.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: d4bc5583-6537-4cd9-bc4b-7712fdd9272a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: sample
ms.date: 03/10/2021
ms.author: justinha
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 8056e95b731b1818e10d7415cb813d6aba0ec7fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103149060"
---
# <a name="enable-azure-active-directory-domain-services-using-powershell"></a>Habilitar o Azure Active Directory Domain Services usando PowerShell

O Azure AD DS (Azure Active Directory Domain Services) fornece serviços de domínio gerenciado, como ingresso no domínio, Política de Grupo, LDAP e autenticação Kerberos/NTLM, que são totalmente compatíveis com o Active Directory do Windows Server. Você consome esses serviços de domínio sem implantar, gerenciar e aplicar patches aos controladores de domínio por conta própria. O Azure AD DS integra-se com o seu locatário existente do Azure AD. Essa integração permite que os usuários entrem usando suas credenciais corporativas e você pode usar os grupos e as contas de usuário existentes para proteger o acesso aos recursos.

Este artigo mostra como habilitar o Azure AD DS usando o PowerShell.

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, você precisa dos seguintes recursos:

* Instalar e configurar o PowerShell do Azure.
    * Se necessário, siga as instruções para [instalar o módulo do Azure PowerShell e conectar-se à sua assinatura do Azure](/powershell/azure/install-az-ps).
    * Lembre-se de entrar em sua assinatura do Azure usando o cmdlet [Connect-AzAccount][Connect-AzAccount].
* Instale e configure o Azure AD PowerShell.
    * Se necessário, siga as instruções para [instalar o módulo do Azure AD PowerShell e conectar-se ao Azure AD](/powershell/azure/active-directory/install-adv2).
    * Lembre-se de entrar em seu locatário do Azure AD usando o cmdlet [Connect-AzureAD][Connect-AzureAD].
* É necessário ter privilégios de *administrador global* no locatário do Azure AD para habilitar o Azure AD DS.
* Você precisa de privilégios de *Colaborador* em sua assinatura do Azure para criar os recursos necessários do Azure AD DS.

  > [!IMPORTANT]
  > Enquanto o módulo **Az.ADDomainServices** do PowerShell estiver em versão prévia, você precisará instalá-lo separadamente usando o cmdlet `Install-Module`.

  ```azurepowershell-interactive
  Install-Module -Name Az.ADDomainServices
  ```

## <a name="create-required-azure-ad-resources"></a>Criar recursos do Azure AD necessários

O Azure AD DS requer uma entidade de serviço para se autenticar e se comunicar e um grupo do Azure AD para definir quais usuários têm permissões administrativas no domínio gerenciado.

Primeiro, crie uma entidade de serviço do Azure AD usando uma ID de aplicativo específica denominada *Serviços do Controlador de Domínio*. No Azure público, o valor da ID é *2565bd9d-da50-47d4-8b85-4c97f669dc36*. Em outras nuvens, o valor é *6ba9a5d4-8456-4118-b521-9c5ca10cdf84*. Não altere essa ID de aplicativo.

Crie uma entidade de serviço do Azure AD usando o cmdlet [New-AzureADServicePrincipal][New-AzureADServicePrincipal]:

```powershell
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
```

Agora crie um grupo do Azure AD chamado *Administradores do AAD DC*. Os usuários adicionados a esse grupo recebem permissões para executar tarefas de administração no domínio gerenciado.

Primeiro, obtenha a ID do objeto do grupo *Administradores do AAD DC* usando o cmdlet [Get-AzureADGroup][Get-AzureADGroup]. Se o grupo não existir, crie-o com o grupo *Administradores do AAD DC* usando o cmdlet [New-AzureADGroup][New-AzureADGroup]:

```powershell
# First, retrieve the object ID of the 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# If the group doesn't exist, create it
if (!$GroupObjectId) {
  $GroupObjectId = New-AzureADGroup -DisplayName "AAD DC Administrators" `
    -Description "Delegated group to administer Azure AD Domain Services" `
    -SecurityEnabled $true `
    -MailEnabled $false `
    -MailNickName "AADDCAdministrators"
  }
else {
  Write-Output "Admin group already exists."
}
```

Com o grupo *Administradores do AAD DC* criado, obtenha a ID do objeto do usuário desejado usando o cmdlet [Get-AzureADUser][Get-AzureADUser] e adicione o usuário ao grupo usando o cmdlet [Add-AzureADGroupMember][Add-AzureADGroupMember].

No exemplo a seguir, a ID de objeto do usuário para a conta com um UPN de `admin@contoso.onmicrosoft.com`. Substitua essa conta de usuário pelo UPN do usuário que você deseja adicionar ao grupo *Administradores do AAD DC*:

```powershell
# Retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq 'admin@contoso.onmicrosoft.com'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId
```

## <a name="create-network-resources"></a>Criar recursos da rede

Primeiro, registre o provedor de recursos do Azure AD Domain Services usando o cmdlet [Register-AzResourceProvider][Register-AzResourceProvider]:

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD
```

Em seguida, crie um grupo de recursos usando o cmdlet [New-AzResourceGroup][New-AzResourceGroup]. No exemplo a seguir, o grupo de recursos é chamado *myResourceGroup* e é criado na região *westus*. Use seu nome e sua região desejada:

```azurepowershell-interactive
$ResourceGroupName = "myResourceGroup"
$AzureLocation = "westus"

# Create the resource group.
New-AzResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation
```

Crie a rede virtual e as sub-redes para o Azure AD Domain Services. Duas sub-redes são criadas: uma para *DomainServices* e outra para *Workloads*. O Azure AD DS é implantado na sub-rede *DomainServices* dedicada. Não implante outros aplicativos ou cargas de trabalho na sub-rede. Use a *Workloads* separada ou outras sub-redes para o restante de suas VMs.

Crie as sub-redes usando o cmdlet [New-AzVirtualNetworkSubnetConfig][New-AzVirtualNetworkSubnetConfig] e, em seguida, crie o rede virtual usando o cmdlet [New-AzVirtualNetwork][New-AzVirtualNetwork].

```azurepowershell-interactive
$VnetName = "myVnet"

# Create the dedicated subnet for Azure AD Domain Services.
$SubnetName = "DomainServices"
$AaddsSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name $SubnetName `
  -AddressPrefix 10.0.0.0/24

# Create an additional subnet for your own VM workloads
$WorkloadSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name Workloads `
  -AddressPrefix 10.0.1.0/24

# Create the virtual network in which you will enable Azure AD Domain Services.
$Vnet= New-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location westus `
  -Name $VnetName `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $AaddsSubnet,$WorkloadSubnet
```

### <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

O Azure AD DS precisa de um grupo de segurança de rede para proteger as portas necessárias para o domínio gerenciado e bloquear todo o resto do tráfego de entrada. Um [NSG (grupo de segurança de rede)][nsg-overview] contém uma lista de regras que permitem ou negam o tráfego de rede para o tráfego na rede virtual do Azure. No Azure AD DS, o grupo de segurança de rede atua como uma camada extra de proteção para bloquear o acesso ao domínio gerenciado. Para ver as portas exigidas, confira [Grupos de segurança de rede e portas exigidas][network-ports].

Os cmdlets do PowerShell a seguir usam [New-AzNetworkSecurityRuleConfig][New-AzNetworkSecurityRuleConfig] para criar as regras e [New-AzNetworkSecurityGroup][New-AzNetworkSecurityGroup] para criar o grupo de segurança de rede. O grupo de segurança de rede e as regras são então associados à sub-rede da rede virtual usando o cmdlet [Set-AzVirtualNetworkSubnetConfig][Set-AzVirtualNetworkSubnetConfig].

```azurepowershell-interactive
$NSGName = "aaddsNSG"

# Create a rule to allow inbound TCP port 3389 traffic from Microsoft secure access workstations for troubleshooting
$nsg201 = New-AzNetworkSecurityRuleConfig -Name AllowRD `
    -Access Allow `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 201 `
    -SourceAddressPrefix CorpNetSaw `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389

# Create a rule to allow TCP port 5986 traffic for PowerShell remote management
$nsg301 = New-AzNetworkSecurityRuleConfig -Name AllowPSRemoting `
    -Access Allow `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 301 `
    -SourceAddressPrefix AzureActiveDirectoryDomainServices `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 5986

# Create the network security group and rules
$nsg = New-AzNetworkSecurityGroup -Name $NSGName `
    -ResourceGroupName $ResourceGroupName `
    -Location $AzureLocation `
    -SecurityRules $nsg201,$nsg301

# Get the existing virtual network resource objects and information
$vnet = Get-AzVirtualNetwork -Name $VnetName -ResourceGroupName $ResourceGroupName
$subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name $SubnetName
$addressPrefix = $subnet.AddressPrefix

# Associate the network security group with the virtual network subnet
Set-AzVirtualNetworkSubnetConfig -Name $SubnetName `
    -VirtualNetwork $vnet `
    -AddressPrefix $addressPrefix `
    -NetworkSecurityGroup $nsg
$vnet | Set-AzVirtualNetwork
```

## <a name="create-a-managed-domain"></a>Criar um domínio gerenciado

Agora, vamos criar um domínio gerenciado. Defina sua ID de assinatura do Azure e forneça um nome para o domínio gerenciado, como *aaddscontoso.com*. Você pode obter a ID da assinatura usando o cmdlet [Get-AzSubscription][Get-AzSubscription].

Se você escolher uma região com suporte a Zonas de Disponibilidade, os recursos do Azure AD DS serão distribuídos entre as zonas para proporcionar redundância adicional.

As Zonas de Disponibilidade são locais físicos exclusivos em uma região do Azure. Cada zona é composta por um ou mais datacenters equipados com energia, resfriamento e rede independentes. Para garantir a resiliência, há um mínimo de três zonas separadas em todas as regiões habilitadas.

Não é preciso configurar nada para que o Azure AD DS seja distribuído entre as zonas. A plataforma do Azure lida automaticamente com a distribuição de recursos na zona. Para obter mais informações e ver a disponibilidade da região, confira [O que são as Zonas de Disponibilidade no Azure?][availability-zones].

```azurepowershell-interactive
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "aaddscontoso.com"

# Enable Azure AD Domain Services for the directory.
$replicaSetParams = @{
  Location = $AzureLocation
  SubnetId = "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"
}
$replicaSet = New-AzADDomainServiceReplicaSetObject @replicaSetParams

$domainServiceParams = @{
  Name = $ManagedDomainName
  ResourceGroupName = $ResourceGroupName
  DomainName = $ManagedDomainName
  ReplicaSet = $replicaSet
}
New-AzADDomainService @domainServiceParams
```

Leva alguns minutos para criar o recurso e retornar o controle para o prompt do PowerShell. O domínio gerenciado continua sendo provisionado em segundo plano e a implantação pode levar até uma hora para ser concluída. No portal do Azure, a página **Visão Geral** do seu domínio gerenciado mostra o status atual durante essa fase de implantação.

Quando o portal do Azure mostra que o domínio gerenciado concluiu o provisionamento, as seguintes tarefas precisam ser concluídas:

* Atualize as configurações de DNS da rede virtual, de modo que as máquinas virtuais possam encontrar o domínio gerenciado para ingresso no domínio ou autenticação.
    * Para configurar o DNS, selecione o domínio gerenciado no portal. Na janela **Visão Geral**, você deve definir automaticamente essas configurações de DNS.
* [Habilite a sincronização de senha para o Azure AD DS](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) para que os usuários possam entrar no domínio gerenciado usando as credenciais corporativas deles.

## <a name="complete-powershell-script"></a>Concluir script do PowerShell

O script completo do PowerShell a seguir combina todas as tarefas mostradas neste artigo. Copie o script e salve-o em um arquivo com uma extensão `.ps1`. Execute o script em um console local do PowerShell ou no [Azure Cloud Shell][cloud-shell].

> [!NOTE]
> Para habilitar o Azure AD DS, você deve ser um administrador global do locatário do Azure AD. Você também precisa, pelo menos, de privilégios de *Colaborador* na assinatura do Azure.

```azurepowershell-interactive
# Change the following values to match your deployment.
$AaddsAdminUserUpn = "admin@contoso.onmicrosoft.com"
$ResourceGroupName = "myResourceGroup"
$VnetName = "myVnet"
$AzureLocation = "westus"
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "aaddscontoso.com"

# Connect to your Azure AD directory.
Connect-AzureAD

# Login to your Azure subscription.
Connect-AzAccount

# Create the service principal for Azure AD Domain Services.
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"

# First, retrieve the object ID of the 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Create the delegated administration group for Azure AD Domain Services if it doesn't already exist.
if (!$GroupObjectId) {
  $GroupObjectId = New-AzureADGroup -DisplayName "AAD DC Administrators" `
    -Description "Delegated group to administer Azure AD Domain Services" `
    -SecurityEnabled $true `
    -MailEnabled $false `
    -MailNickName "AADDCAdministrators"
  }
else {
  Write-Output "Admin group already exists."
}

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq '$AaddsAdminUserUpn'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId

# Register the resource provider for Azure AD Domain Services with Resource Manager.
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD

# Create the resource group.
New-AzResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation

# Create the dedicated subnet for AAD Domain Services.
$SubnetName = "DomainServices"
$AaddsSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name DomainServices `
  -AddressPrefix 10.0.0.0/24

$WorkloadSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name Workloads `
  -AddressPrefix 10.0.1.0/24

# Create the virtual network in which you will enable Azure AD Domain Services.
$Vnet=New-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location $AzureLocation `
  -Name $VnetName `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $AaddsSubnet,$WorkloadSubnet

$NSGName = "aaddsNSG"

# Create a rule to allow inbound TCP port 3389 traffic from Microsoft secure access workstations for troubleshooting
$nsg201 = New-AzNetworkSecurityRuleConfig -Name AllowRD `
    -Access Allow `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 201 `
    -SourceAddressPrefix CorpNetSaw `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389

# Create a rule to allow TCP port 5986 traffic for PowerShell remote management
$nsg301 = New-AzNetworkSecurityRuleConfig -Name AllowPSRemoting `
    -Access Allow `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 301 `
    -SourceAddressPrefix AzureActiveDirectoryDomainServices `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 5986

# Create the network security group and rules
$nsg = New-AzNetworkSecurityGroup -Name $NSGName `
    -ResourceGroupName $ResourceGroupName `
    -Location $AzureLocation `
    -SecurityRules $nsg201,$nsg301

# Get the existing virtual network resource objects and information
$vnet = Get-AzVirtualNetwork -Name $VnetName -ResourceGroupName $ResourceGroupName
$subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name $SubnetName
$addressPrefix = $subnet.AddressPrefix

# Associate the network security group with the virtual network subnet
Set-AzVirtualNetworkSubnetConfig -Name $SubnetName `
    -VirtualNetwork $vnet `
    -AddressPrefix $addressPrefix `
    -NetworkSecurityGroup $nsg
$vnet | Set-AzVirtualNetwork

# Enable Azure AD Domain Services for the directory.
$replicaSetParams = @{
  Location = $AzureLocation
  SubnetId = "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"
}
$replicaSet = New-AzADDomainServiceReplicaSetObject @replicaSetParams

$domainServiceParams = @{
  Name = $ManagedDomainName
  ResourceGroupName = $ResourceGroupName
  DomainName = $ManagedDomainName
  ReplicaSet = $replicaSet
}
New-AzADDomainService @domainServiceParams
```

Leva alguns minutos para criar o recurso e retornar o controle para o prompt do PowerShell. O domínio gerenciado continua sendo provisionado em segundo plano e a implantação pode levar até uma hora para ser concluída. No portal do Azure, a página **Visão Geral** do seu domínio gerenciado mostra o status atual durante essa fase de implantação.

Quando o portal do Azure mostra que o domínio gerenciado concluiu o provisionamento, as seguintes tarefas precisam ser concluídas:

* Atualize as configurações de DNS da rede virtual, de modo que as máquinas virtuais possam encontrar o domínio gerenciado para ingresso no domínio ou autenticação.
    * Para configurar o DNS, selecione o domínio gerenciado no portal. Na janela **Visão Geral**, você deve definir automaticamente essas configurações de DNS.
* [Habilite a sincronização de senha para o Azure AD DS](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) para que os usuários possam entrar no domínio gerenciado usando as credenciais corporativas deles.

## <a name="next-steps"></a>Próximas etapas

Para ver o domínio gerenciado em ação, você pode [ingressar uma VM do Windows em domínio][windows-join], [configurar o LDAP Seguro][tutorial-ldaps] e [configurar a sincronização de hash de senha][tutorial-phs].

<!-- INTERNAL LINKS -->
[windows-join]: join-windows-vm.md
[tutorial-ldaps]: tutorial-configure-ldaps.md
[tutorial-phs]: tutorial-configure-password-hash-sync.md
[nsg-overview]: ../virtual-network/network-security-groups-overview.md
[network-ports]: network-considerations.md#network-security-groups-and-required-ports

<!-- EXTERNAL LINKS -->
[Connect-AzAccount]: /powershell/module/Az.Accounts/Connect-AzAccount
[Connect-AzureAD]: /powershell/module/AzureAD/Connect-AzureAD
[New-AzureADServicePrincipal]: /powershell/module/AzureAD/New-AzureADServicePrincipal
[New-AzureADGroup]: /powershell/module/AzureAD/New-AzureADGroup
[Add-AzureADGroupMember]: /powershell/module/AzureAD/Add-AzureADGroupMember
[Get-AzureADGroup]: /powershell/module/AzureAD/Get-AzureADGroup
[Get-AzureADUser]: /powershell/module/AzureAD/Get-AzureADUser
[Register-AzResourceProvider]: /powershell/module/Az.Resources/Register-AzResourceProvider
[New-AzResourceGroup]: /powershell/module/Az.Resources/New-AzResourceGroup
[New-AzVirtualNetworkSubnetConfig]: /powershell/module/Az.Network/New-AzVirtualNetworkSubnetConfig
[New-AzVirtualNetwork]: /powershell/module/Az.Network/New-AzVirtualNetwork
[Get-AzSubscription]: /powershell/module/Az.Accounts/Get-AzSubscription
[cloud-shell]: ../cloud-shell/cloud-shell-windows-users.md
[availability-zones]: ../availability-zones/az-overview.md
[New-AzNetworkSecurityRuleConfig]: /powershell/module/az.network/new-aznetworksecurityruleconfig
[New-AzNetworkSecurityGroup]: /powershell/module/az.network/new-aznetworksecuritygroup
[Set-AzVirtualNetworkSubnetConfig]: /powershell/module/az.network/set-azvirtualnetworksubnetconfig
