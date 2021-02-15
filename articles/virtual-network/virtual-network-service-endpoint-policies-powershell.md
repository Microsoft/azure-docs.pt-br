---
title: Restringir vazamento de dados ao armazenamento do Azure-Azure PowerShell
description: Neste artigo, você aprenderá a limitar e restringir vazamento de dados de rede virtual a recursos de armazenamento do Azure com políticas de ponto de extremidade de serviço de rede virtual usando Azure PowerShell.
services: virtual-network
documentationcenter: virtual-network
author: RDhillon
manager: narayan
editor: ''
tags: azure-resource-manager
Customer intent: I want only resources in a virtual network subnet to access an Azure PaaS resource, such as an Azure Storage account.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: rdhillon
ms.custom: ''
ms.openlocfilehash: d8e300c9be8f3e59dc9443bf1f1806e4228992ad
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98790335"
---
# <a name="manage-data-exfiltration-to-azure-storage-accounts-with-virtual-network-service-endpoint-policies-using-azure-powershell"></a>Gerenciar vazamento de dados para contas de armazenamento do Azure com políticas de ponto de extremidade de serviço de rede virtual usando Azure PowerShell

As políticas de ponto de extremidade de serviço de rede virtual permitem que você aplique o controle de acesso nas contas de armazenamento do Azure de dentro de uma rede virtual em pontos de extremidade de serviço. Essa é uma chave para proteger suas cargas de trabalho, gerenciar quais contas de armazenamento são permitidas e onde os dados vazamento são permitidos.
Neste artigo, você aprenderá como:

* Crie uma rede virtual.
* Adicione uma sub-rede e habilite o ponto de extremidade de serviço para o armazenamento do Azure.
* Crie duas contas de armazenamento do Azure e permita o acesso à rede por meio da sub-rede criada acima.
* Crie uma política de ponto de extremidade de serviço para permitir o acesso somente a uma das contas de armazenamento.
* Implante uma máquina virtual (VM) na sub-rede.
* Confirme o acesso à conta de armazenamento permitida da sub-rede.
* Confirme se o acesso foi negado à conta de armazenamento não permitida da sub-rede.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Caso não tenha uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar o PowerShell localmente, este artigo exigirá o módulo do Azure PowerShell versão 1.0.0 ou posterior. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Connect-AzAccount` para criar uma conexão com o Azure.

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

Antes de criar uma rede virtual, será necessário criar um grupo de recursos para a rede virtual e todos os outros recursos criados neste artigo. Crie um grupo de recursos com [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup*: 

```azurepowershell-interactive
New-AzResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Location EastUS
```

Crie uma rede virtual com [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). O exemplo a seguir cria uma rede virtual nomeada *myVirtualNetwork* com o prefixo de endereço *10.0.0.0/16*.

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

## <a name="enable-a-service-endpoint"></a>Habilitar um ponto de extremidade de serviço

Crie uma sub-rede na rede virtual. Neste exemplo, uma sub-rede denominada *Privada* é criada com um ponto de extremidade de serviço para *Microsoft.Storage*: 

```azurepowershell-interactive
$subnetConfigPrivate = Add-AzVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork `
  -ServiceEndpoint Microsoft.Storage

$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="restrict-network-access-for-the-subnet"></a>Restringir o acesso à rede para a sub-rede

Crie regras de segurança de grupo de segurança de rede com [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig). A seguinte regra permite o acesso de saída para os endereços IP públicos atribuídos ao serviço de Armazenamento do Microsoft Azure: 

```azurepowershell-interactive
$rule1 = New-AzNetworkSecurityRuleConfig `
  -Name Allow-Storage-All `
  -Access Allow `
  -DestinationAddressPrefix Storage `
  -DestinationPortRange * `
  -Direction Outbound `
  -Priority 100 -Protocol * `
  -SourceAddressPrefix VirtualNetwork `
  -SourcePortRange *
```

A regra a seguir nega acesso a todos os endereços IP públicos. A regra anterior substitui essa regra, devido à sua prioridade mais alta, o que permite acesso aos endereços IP públicos do Armazenamento do Microsoft Azure.

```azurepowershell-interactive
$rule2 = New-AzNetworkSecurityRuleConfig `
  -Name Deny-Internet-All `
  -Access Deny `
  -DestinationAddressPrefix Internet `
  -DestinationPortRange * `
  -Direction Outbound `
  -Priority 110 -Protocol * `
  -SourceAddressPrefix VirtualNetwork `
  -SourcePortRange *
```

A seguinte regra permite o tráfego de entrada Remote Desktop Protocol (RDP) para a sub-rede de qualquer lugar. Conexões de área de trabalho remota são permitidas na sub-rede, para que você possa confirmar o acesso à rede para um recurso em uma etapa posterior.

```azurepowershell-interactive
$rule3 = New-AzNetworkSecurityRuleConfig `
  -Name Allow-RDP-All `
  -Access Allow `
  -DestinationAddressPrefix VirtualNetwork `
  -DestinationPortRange 3389 `
  -Direction Inbound `
  -Priority 120 `
  -Protocol * `
  -SourceAddressPrefix * `
  -SourcePortRange *
```

Crie um grupo de segurança de rede com [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup). O seguinte exemplo cria um grupo de segurança de rede chamado *myNsgPrivate*.

```azurepowershell-interactive
$nsg = New-AzNetworkSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myNsgPrivate `
  -SecurityRules $rule1,$rule2,$rule3
```

Associe o grupo de segurança de rede à sub-rede *privada* com [set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) e, em seguida, grave a configuração de sub-rede na rede virtual. O exemplo a seguir associa o grupo de segurança de rede *myNsgPrivate* à sub-rede *Privada*:

```azurepowershell-interactive
Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $VirtualNetwork `
  -Name Private `
  -AddressPrefix 10.0.0.0/24 `
  -ServiceEndpoint Microsoft.Storage `
  -NetworkSecurityGroup $nsg

$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="restrict-network-access-to-azure-storage-accounts"></a>Restringir o acesso à rede para contas de armazenamento do Azure

As etapas necessárias para restringir o acesso de rede a recursos criados por meio de serviços do Azure habilitados para pontos de extremidade do serviço variam de acordo com os serviços. Confira a documentação de serviços individuais para obter as etapas específicas para cada serviço. O restante deste artigo inclui etapas para restringir o acesso de rede para uma conta de Armazenamento do Microsoft Azure, como exemplo.

### <a name="create-two-storage-accounts"></a>Criar duas contas de armazenamento

Crie uma conta de armazenamento do Azure com [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount).

```azurepowershell-interactive
$storageAcctName1 = 'allowedaccount'

New-AzStorageAccount `
  -Location EastUS `
  -Name $storageAcctName1 `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

Depois que a conta de armazenamento for criada, recupere a chave da conta de armazenamento em uma variável com [Get-AzStorageAccountKey](/powershell/module/az.storage/get-azstorageaccountkey):

```azurepowershell-interactive
$storageAcctKey1 = (Get-AzStorageAccountKey -ResourceGroupName myResourceGroup -AccountName $storageAcctName1).Value[0]
```

A chave é usada para criar um compartilhamento de arquivos em uma etapa posterior. Insira `$storageAcctKey` e observe o valor, já que você também precisará inseri-lo manualmente em uma etapa posterior ao mapear o compartilhamento de arquivos para uma unidade em uma VM.

Agora, repita as etapas acima para criar uma segunda conta de armazenamento.

```azurepowershell-interactive
$storageAcctName2 = 'notallowedaccount'

New-AzStorageAccount `
  -Location EastUS `
  -Name $storageAcctName2 `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

Também recupere a chave da conta de armazenamento desta conta para usar o mais tarde para criar um compartilhamento de arquivos.

```azurepowershell-interactive
$storageAcctKey2 = (Get-AzStorageAccountKey -ResourceGroupName myResourceGroup -AccountName $storageAcctName2).Value[0]
```

### <a name="create-a-file-share-in-each-of-the-storage-account"></a>Criar um compartilhamento de arquivos em cada conta de armazenamento

Crie um contexto para sua conta de armazenamento e a chave com [New-AzStorageContext](/powershell/module/az.storage/new-AzStoragecontext). O contexto encapsula o nome da conta de armazenamento e a chave da conta:

```azurepowershell-interactive
$storageContext1 = New-AzStorageContext $storageAcctName1 $storageAcctKey1

$storageContext2 = New-AzStorageContext $storageAcctName2 $storageAcctKey2
```

Crie um compartilhamento de arquivos com [New-AzStorageShare](/powershell/module/az.storage/new-azstorageshare):

```azurepowershell-interactive
$share1 = New-AzStorageShare my-file-share -Context $storageContext1

$share2 = New-AzStorageShare my-file-share -Context $storageContext2
```

### <a name="deny-all-network-access-to-a-storage-accounts"></a>Negar todo acesso à rede para contas de armazenamento

Por padrão, as contas de armazenamento aceitam conexões de clientes em qualquer rede. Para limitar o acesso às redes selecionadas, altere a ação padrão para *negar* com [Update-AzStorageAccountNetworkRuleSet](/powershell/module/az.storage/update-azstorageaccountnetworkruleset). Depois que o acesso à rede for negado, a conta de armazenamento não estará acessível em nenhuma rede.

```azurepowershell-interactive
Update-AzStorageAccountNetworkRuleSet `
  -ResourceGroupName myresourcegroup `
  -Name $storageAcctName1 `
  -DefaultAction Deny

Update-AzStorageAccountNetworkRuleSet  `
  -ResourceGroupName myresourcegroup `
  -Name $storageAcctName2 `
  -DefaultAction Deny
```

### <a name="enable-network-access-only-from-the-vnet-subnet"></a>Habilitar o acesso à rede somente da sub-rede VNet

Recupere a rede virtual criada com [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) e, em seguida, recupere o objeto de sub-rede privada em uma variável com [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig):

```azurepowershell-interactive
$privateSubnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Name myVirtualNetwork `
  | Get-AzVirtualNetworkSubnetConfig -Name Private
```

Permita o acesso à rede para a conta de armazenamento da sub-rede *privada* com [Add-AzStorageAccountNetworkRule](/powershell/module/az.network/add-aznetworksecurityruleconfig).

```azurepowershell-interactive
Add-AzStorageAccountNetworkRule `
  -ResourceGroupName myresourcegroup `
  -Name $storageAcctName1 `
  -VirtualNetworkResourceId $privateSubnet.Id

Add-AzStorageAccountNetworkRule `
  -ResourceGroupName myresourcegroup `
  -Name $storageAcctName2 `
  -VirtualNetworkResourceId $privateSubnet.Id
```

## <a name="apply-policy-to-allow-access-to-valid-storage-account"></a>Aplicar política para permitir o acesso a uma conta de armazenamento válida

Para garantir que os usuários na rede virtual possam acessar somente as contas de armazenamento do Azure que são seguras e permitidas, você pode criar uma política de ponto de extremidade de serviço com a lista de contas de armazenamento permitidas na definição. Essa política é aplicada à sub-rede da rede virtual que está conectada ao armazenamento por meio de pontos de extremidade de serviço.

### <a name="create-a-service-endpoint-policy"></a>Criar uma política de ponto de extremidade de serviço

Esta seção cria a definição de política com a lista de recursos permitidos para acessar o ponto de extremidade de serviço

Recuperar a ID de recurso para a primeira conta de armazenamento (permitido) 

```azurepowershell-interactive
$resourceId = (Get-AzStorageAccount -ResourceGroupName myresourcegroup -Name $storageAcctName1).id
```

Criar a definição de política para permitir o recurso acima

```azurepowershell-interactive
$policyDefinition = New-AzServiceEndpointPolicyDefinition -Name mypolicydefinition `
  -Description "Service Endpoint Policy Definition" `
  -Service "Microsoft.Storage" `
  -ServiceResource $resourceId
```

Criar a política de ponto de extremidade de serviço usando a definição de política criada acima

```azurepowershell-interactive
$sepolicy = New-AzServiceEndpointPolicy -ResourceGroupName myresourcegroup `
  -Name mysepolicy -Location EastUS
  -ServiceEndpointPolicyDefinition $policyDefinition
```

### <a name="associate-the-service-endpoint-policy-to-the-virtual-network-subnet"></a>Associar a política de ponto de extremidade de serviço à sub-rede da rede virtual

Depois de criar a política de ponto de extremidade de serviço, você a associará à sub-rede de destino com a configuração de ponto de extremidade de serviço para o armazenamento do Azure.

```azurepowershell-interactive
Set-AzVirtualNetworkSubnetConfig -VirtualNetwork $VirtualNetwork `
  -Name Private `
  -AddressPrefix 10.0.0.0/24 `
  -NetworkSecurityGroup $nsg `
  -ServiceEndpoint Microsoft.Storage `
  -ServiceEndpointPolicy $sepolicy

$virtualNetwork | Set-AzVirtualNetwork
```
## <a name="validate-access-restriction-to-azure-storage-accounts"></a>Validar a restrição de acesso às contas de armazenamento do Azure

### <a name="deploy-the-virtual-machine"></a>Implantar a máquina virtual

Para testar o acesso à rede para uma conta de armazenamento, implante uma VM na sub-rede.

Crie uma máquina virtual na sub-rede *privada* com [New-AzVM](/powershell/module/az.compute/new-azvm). Ao executar o comando a seguir, as credenciais serão solicitadas. Os valores que você inseriu são configurados como o nome de usuário e senha para a VM. A opção `-AsJob` cria a VM em segundo plano para que você possa prosseguir para a próxima etapa.

```azurepowershell-interactive
New-AzVm -ResourceGroupName myresourcegroup `
  -Location "East US" `
  -VirtualNetworkName myVirtualNetwork `
  -SubnetName Private `
  -Name "myVMPrivate" -AsJob
```

É retornada uma saída semelhante ao exemplo a seguir:

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running... AzureLongRun... Running       True            localhost            New-AzVM
```

### <a name="confirm-access-to-the-allowed-storage-account"></a>Confirmar o acesso à conta de armazenamento *permitida*

Use [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) para retornar o endereço IP público de uma VM. O exemplo a seguir retorna o endereço IP público da VM *myVmPrivate*:

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVmPrivate `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Substitua `<publicIpAddress>` no seguinte comando pelo endereço IP público retornado do comando anterior e, em seguida, insira o comando a seguir:

```powershell
mstsc /v:<publicIpAddress>
```

Um arquivo .rdp (Remote Desktop Protocol) é criado e baixado para o computador. Abra o arquivo rdp baixado. Se solicitado, selecione **Conectar**. Insira o nome de usuário e senha que você especificou ao criar a VM. Talvez seja necessário selecionar **Mais escolhas** e, em seguida, **Usar uma conta diferente** para especificar as credenciais inseridas durante a criação da VM. Selecione **OK**. Você pode receber um aviso do certificado durante o processo de logon. Se você receber o aviso, selecione **Sim** ou **Continuar**, para prosseguir com a conexão.

Na VM *myVmPrivate* , mapeie o compartilhamento de arquivos do Azure da conta de armazenamento permitida para a unidade Z usando o PowerShell. 

```powershell
$acctKey = ConvertTo-SecureString -String $storageAcctKey1 -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList ("Azure\allowedaccount"), $acctKey
New-PSDrive -Name Z -PSProvider FileSystem -Root "\\allowedaccount.file.core.windows.net\my-file-share" -Credential $credential
```

O PowerShell retorna uma saída semelhante à seguinte saída de exemplo:

```powershell
Name           Used (GB)     Free (GB) Provider      Root
----           ---------     --------- --------      ----
Z                                      FileSystem    \\allowedaccount.file.core.windows.net\my-f...
```

O compartilhamento de arquivos do Azure foi mapeado com êxito para a unidade Z.

Feche a sessão da área de trabalho remota para a VM *myVmPrivate*.

### <a name="confirm-access-is-denied-to-non-allowed-storage-account"></a>Confirmar acesso negado à conta *de armazenamento não permitida*

Na mesma VM *myVmPrivate* , tente mapear o compartilhamento de arquivos do Azure para a unidade X. 

```powershell
$acctKey = ConvertTo-SecureString -String $storageAcctKey1 -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\notallowedaccount", $acctKey
New-PSDrive -Name X -PSProvider FileSystem -Root "\\notallowedaccount.file.core.windows.net\my-file-share" -Credential $credential
```

O acesso ao compartilhamento é negado, e você recebe um erro `New-PSDrive : Access is denied`. Acesso negado porque a conta de armazenamento *notallowedaccount* não está na lista de recursos permitidos na política de ponto de extremidade de serviço. 

Feche a sessão da área de trabalho remota para a VM *myVmPublic*.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não for mais necessário, você poderá usar [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos e todos os recursos que ele contém:

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aplicou uma política de ponto de extremidade de serviço em um ponto de extremidade de serviço de rede virtual do Azure para o armazenamento do Azure. Você criou contas de armazenamento do Azure e acesso limitado à rede a apenas determinadas contas de armazenamento (e, assim, negadas a outras) de uma sub-rede de rede virtual. Para saber mais sobre as políticas de ponto de extremidade de serviço, consulte [visão geral das políticas de pontos de extremidades de serviço](virtual-network-service-endpoint-policies-overview.md).
