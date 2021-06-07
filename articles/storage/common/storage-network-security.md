---
title: Configurar redes virtuais e firewalls do Armazenamento do Microsoft Azure | Microsoft Docs
description: Configure a segurança de rede em camadas para sua conta de armazenamento usando os firewalls de armazenamento do Azure e a rede virtual do Azure.
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 03/16/2021
ms.author: normesta
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: 3d71a7ad2507909dacf54e7f1c49b6e768033113
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104600472"
---
# <a name="configure-azure-storage-firewalls-and-virtual-networks"></a>Configurar redes virtuais e firewalls do Armazenamento do Microsoft Azure

O Armazenamento do Microsoft Azure fornece um modelo de segurança em camadas. Esse modelo permite que você proteja e controle o nível de acesso às suas contas de armazenamento que seus aplicativos e ambientes empresariais exigem, com base no tipo e no subconjunto de redes ou recursos usados. Quando as regras de rede são configuradas, somente os aplicativos que solicitam dados no conjunto especificado de redes ou por meio do conjunto especificado de recursos do Azure podem acessar uma conta de armazenamento. Você pode limitar o acesso à sua conta de armazenamento a solicitações originadas de endereços IP especificados, intervalos de IP, sub-redes em uma VNet (rede virtual) do Azure ou instâncias de recursos de alguns serviços do Azure.

As contas de armazenamento têm um ponto de extremidade público que pode ser acessado pela Internet. Você também pode criar [pontos de extremidade privados para sua conta de armazenamento](storage-private-endpoints.md), que atribui um endereço IP privado de sua VNet à conta de armazenamento e protege todo o tráfego entre a VNet e a conta de armazenamento por meio de um link privado. O Firewall do armazenamento do Azure fornece controle de acesso para o ponto de extremidade público da sua conta de armazenamento. Você também pode usar o firewall para bloquear todo o acesso por meio do ponto de extremidade público ao usar pontos de extremidades privados. A configuração do firewall de armazenamento também permite selecionar serviços confiáveis da plataforma Azure para acessar a conta de armazenamento com segurança.

Um aplicativo que acessa uma conta de armazenamento quando as regras de rede estão em vigor ainda requer autorização adequada para a solicitação. A autorização é compatível com as credenciais do Azure AD (Azure Active Directory) para blobs e filas, com uma chave de acesso de conta válida ou um token SAS.

> [!IMPORTANT]
> Ativar regras de firewall para sua conta de armazenamento bloqueia solicitações de entrada para os dados por padrão, a menos que as solicitações sejam provenientes de um serviço que está operando em uma VNet (Rede Virtual) do Azure ou de endereços IP públicos permitidos. Solicitações que estão bloqueadas incluem as de outros serviços do Azure, do portal do Azure, de registro em log e serviços de métricas e assim por diante.
>
> Você pode permitir acesso aos serviços do Azure que operam de dentro de uma rede virtual, permitindo tráfego da sub-rede hospedando a instância do serviço. Você também pode habilitar um número limitado de cenários por meio do mecanismo de exceções descrito abaixo. O acesso a dados da conta de armazenamento por meio do portal do Azure precisa ser feito de um computador dentro do limite confiável (IP ou VNet) que você configurou.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="scenarios"></a>Cenários

Para proteger sua conta de armazenamento, primeiro você deve configurar uma regra para negar o acesso ao tráfego de todas as redes (incluindo o tráfego da Internet) no ponto de extremidade público, por padrão. Em seguida, você deve configurar regras que permitem acesso ao tráfego de VNets específicas. Você também pode configurar regras para conceder acesso ao tráfego de intervalos de endereços IP de Internet públicos selecionados, permitindo conexões de clientes locais ou da Internet específicos. Essa configuração permite que você crie um limite de rede seguro para seus aplicativos.

Você pode combinar regras de firewall que permitem o acesso de redes virtuais específicas e de intervalos de endereços IP públicos na mesma conta de armazenamento. As regras de firewall de armazenamento podem ser aplicadas a contas de armazenamento existentes ou durante a criação de contas de armazenamento.

As regras de firewall de armazenamento se aplicam ao ponto de extremidade público de uma conta de armazenamento. Você não precisa de nenhuma regra de acesso de firewall para permitir o tráfego para pontos de extremidade privados de uma conta de armazenamento. O processo de aprovar a criação de um ponto de extremidade privado concede acesso implícito ao tráfego da sub-rede que hospeda o ponto de extremidade privado.

As regras de rede são impostas em todos os protocolos de rede para o armazenamento do Azure, incluindo REST e SMB. Para acessar os dados usando ferramentas como o portal do Azure, o Gerenciador de Armazenamento e o AZCopy, é necessário configurar regras de rede explícitas.

Depois que as regras de rede são aplicadas, elas são impostas para todas as solicitações. Os tokens SAS que concedem acesso a um serviço de endereço IP específico limitam o acesso do proprietário do token, mas não concedem um novo acesso além das regras de rede configuradas.

O tráfego de disco da máquina virtual (incluindo as operações de montagem e desmontagem e E/S de disco) não é afetado pelas regras de rede. O acesso REST a blobs de página é protegido pelas regras de rede.

As contas de armazenamento clássicas não dão suporte a firewalls e redes virtuais.

Você pode usar discos não gerenciados em contas de armazenamento com regras de rede aplicadas para fazer backup e restaurar VMs criando uma exceção. Esse processo está documentado na seção [gerenciar exceções](#manage-exceptions) deste artigo. Exceções de firewall não são aplicáveis com discos gerenciados, pois eles já são gerenciados pelo Azure.

## <a name="change-the-default-network-access-rule"></a>Alterar a regra de acesso de rede padrão

Por padrão, as contas de armazenamento aceitam conexões de clientes em qualquer rede. Para limitar o acesso a redes selecionadas, primeiro você deve alterar a ação padrão.

> [!WARNING]
> Fazer alterações em regras de rede pode afetar a capacidade de seus aplicativos se conectarem ao Armazenamento do Azure. Definir a regra de rede padrão como **negar** bloqueia todo o acesso aos dados, a menos que regras específicas de rede que **concedam** o acesso também sejam aplicadas. Certifique-se de conceder acesso a qualquer uma das redes permitidas usando regras de rede antes de alterar a regra padrão para negar o acesso.

### <a name="managing-default-network-access-rules"></a>Como alterar as regras de acesso de rede padrão

Você pode gerar as regras de acesso à rede padrão para contas de armazenamento através do portal do Azure, PowerShell ou CLIv2.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Acesse a conta de armazenamento que você deseja proteger.

2. Selecione no menu configurações chamado **rede**.

3. Para negar o acesso por padrão, opte por permitir o acesso de **Redes selecionadas**. Para permitir o tráfego de todas as redes, opte por permitir o acesso de **Todas as redes**.

4. Selecione **Salvar** para salvar suas alterações.

<a id="powershell"></a>

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Instalar o [Azure PowerShell](/powershell/azure/install-Az-ps) e [entrar](/powershell/azure/authenticate-azureps).

2. Exiba o status da regra padrão para a conta de armazenamento.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").DefaultAction
    ```

3. Defina a regra padrão para negar o acesso à rede por padrão.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Deny
    ```

4. Defina a regra padrão para permitir o acesso à rede por padrão.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Allow
    ```

#### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

1. Instalar a [CLI do Azure](/cli/azure/install-azure-cli) e [entrar](/cli/azure/authenticate-azure-cli).

2. Exiba o status da regra padrão para a conta de armazenamento.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.defaultAction
    ```

3. Defina a regra padrão para negar o acesso à rede por padrão.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Deny
    ```

4. Defina a regra padrão para permitir o acesso à rede por padrão.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Allow
    ```
---

## <a name="grant-access-from-a-virtual-network"></a>Conceder acesso de uma rede virtual

Você pode configurar as contas de armazenamento para permitir o acesso somente de sub-redes específicas. As sub-redes permitidas podem pertencer a uma VNet na mesma assinatura ou àquelas em uma assinatura diferente, incluindo assinaturas que pertençam a um locatário diferente do Azure Active Directory.

Habilitar um [Ponto de extremidade de serviço](../../virtual-network/virtual-network-service-endpoints-overview.md) do Armazenamento do Microsoft Azure dentro da VNet. O ponto de extremidade de serviço roteia o tráfego da VNet por meio de um caminho ideal para o serviço de Armazenamento do Azure. As identidades da rede virtual e da sub-rede também são transmitidas com cada solicitação. Em seguida, os administradores podem configurar as regras de rede para a conta de armazenamento que as solicitações sejam recebidas de sub-redes específicas em uma VNet. Os clientes com o acesso concedido por meio dessas regras de rede devem continuar a atender aos requisitos de autorização da conta de armazenamento para acessar os dados.

Cada conta de armazenamento dá suporte a até 200 regras de rede virtual, que podem ser combinadas com [regras de rede IP](#grant-access-from-an-internet-ip-range).

### <a name="available-virtual-network-regions"></a>Regiões de rede virtual disponíveis

Em geral, os pontos de extremidade de serviço funcionam entre redes virtuais e instâncias de serviço na mesma região do Azure. Ao usar pontos de extremidade de serviço com o Armazenamento do Microsoft Azure, este escopo cresce para incluir a [região emparelhada](../../best-practices-availability-paired-regions.md). Pontos de extremidade de serviço permitem continuidade durante um failover regional e acesso a instâncias de armazenamento com redundância geográfica somente leitura (RA-GRS). As regras de rede que concedem acesso de uma rede virtual para uma conta de armazenamento também concedem acesso a qualquer instância de RA-GRS.

Ao planejar a recuperação de desastre durante uma interrupção regional, você deve criar as VNets na região emparelhada com antecedência. Habilite pontos de extremidade de serviço para o Armazenamento do Microsoft Azure, com as regras de rede concedendo acesso dessas redes virtuais alternativas. Em seguida, aplica essas regras às contas de armazenamento com redundância geográfica.

> [!NOTE]
> Os Pontos de Extremidade de Serviço não se aplicam ao tráfego de fora da região da rede virtual e ao par da região designada. Você pode aplicar as regras de rede concedendo acesso das redes virtuais para as conta de armazenamento na região primária de uma conta de armazenamento ou na região emparelhada designada.

### <a name="required-permissions"></a>Permissões necessárias

Para aplicar uma regra da rede virtual a uma conta de armazenamento, o usuário deve ter permissão para as sub-redes sendo adicionadas. A aplicação de uma regra pode ser executada por um [colaborador da conta de armazenamento](../../role-based-access-control/built-in-roles.md#storage-account-contributor) ou um usuário que tenha recebido permissão para a operação do provedor de `Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action` [recursos do Azure](../../role-based-access-control/resource-provider-operations.md#microsoftnetwork) por meio de uma função personalizada do Azure.

A conta de armazenamento e o acesso concedido às redes virtuais podem estar em assinaturas diferentes, incluindo assinaturas que são parte do mesmo locatário do Azure AD.

> [!NOTE]
> A configuração de regras que permitem acesso a sub-redes em redes virtuais que fazem parte de um locatário diferente do Azure Active Directory atualmente só é compatível por meio do PowerShell, da CLI e de APIs REST. Essas regras não podem ser configuradas por meio do portal do Azure, embora ele possa exibi-las.

### <a name="managing-virtual-network-rules"></a>Gerenciando regras da rede virtual

Você pode gerenciar as regras da rede virtual para contas de armazenamento através do portal do Azure, PowerShell ou CIv2.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Acesse a conta de armazenamento que você deseja proteger.

2. Selecione no menu configurações chamado **rede**.

3. Certifique-se de que você optou por permitir o acesso de **Redes selecionadas**.

4. Para conceder acesso a uma rede virtual com uma nova regra de rede, em **redes virtuais**, selecione **Adicionar rede virtual existente**, selecione opções de **redes virtuais** e **sub-redes** e, em seguida, selecione **Adicionar**. Para criar uma nova rede virtual e conceder acesso a ela, selecione **Adicionar nova rede virtual**. Forneça as informações necessárias para criar a nova rede virtual e, em seguida, selecione **criar**.

    > [!NOTE]
    > Se um ponto de extremidade de Armazenamento do Microsoft Azure não foi configurado anteriormente para a rede virtual selecionada e as sub-redes, você pode configurá-lo como parte dessa operação.
    >
    > Atualmente, somente as redes virtuais que pertencem ao mesmo locatário do Azure Active Directory são mostradas para seleção durante a criação de regras. Para permitir acesso a uma sub-rede em uma rede virtual que pertence a outro locatário, use o PowerShell, a CLI ou as APIs REST.

5. Para remover uma rede virtual ou regra de sub-rede, selecione **...** para abrir o menu de contexto da rede virtual ou sub-rede e selecione **remover**.

6. Selecione **salvar** para aplicar suas alterações.

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Instalar o [Azure PowerShell](/powershell/azure/install-Az-ps) e [entrar](/powershell/azure/authenticate-azureps).

2. Liste as regras da rede virtual.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").VirtualNetworkRules
    ```

3. Habilite o ponto de extremidade de serviço do Armazenamento do Microsoft Azure em uma rede virtual existente e sub-rede.

    ```powershell
    Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.0.0.0/24" -ServiceEndpoint "Microsoft.Storage" | Set-AzVirtualNetwork
    ```

4. Adicionar uma regra de rede para uma rede virtual e sub-rede.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

    > [!TIP]
    > Para adicionar uma regra de rede para uma sub-rede em uma VNet que pertence a outro locatário do Azure AD, use um parâmetro **VirtualNetworkResourceId** totalmente qualificado no formato "/subscriptions/subscription-ID/resourceGroups/resourceGroup-Name/providers/Microsoft.Network/virtualNetworks/vNet-name/subnets/nome-da-sub-rede".

5. Remova uma regra de rede para uma rede virtual e uma sub-rede.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

> [!IMPORTANT]
> Não se esqueça de [definir a regra padrão](#change-the-default-network-access-rule) para **negar** ou as regras de rede não terão efeito.

#### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

1. Instalar a [CLI do Azure](/cli/azure/install-azure-cli) e [entrar](/cli/azure/authenticate-azure-cli).

2. Liste as regras da rede virtual.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query virtualNetworkRules
    ```

3. Habilite o ponto de extremidade de serviço do Armazenamento do Microsoft Azure em uma rede virtual existente e sub-rede.

    ```azurecli
    az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.Storage"
    ```

4. Adicionar uma regra de rede para uma rede virtual e sub-rede.

    ```azurecli
    subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

    > [!TIP]
    > Para adicionar uma regra para uma sub-rede em uma VNet que pertence a outro locatário do Azure AD, use uma ID de sub-rede totalmente qualificada no formato "/subscriptions/ \<subscription-ID\> /ResourceGroups/ \<resourceGroup-Name\> /Providers/Microsoft.Network/virtualNetworks/ \<vNet-name\> /Subnets/ \<subnet-name\> ".
    >
    > Você pode usar o parâmetro **subscription** para recuperar a ID de sub-rede de uma VNet que pertence a outro locatário do Azure AD.

5. Remova uma regra de rede para uma rede virtual e uma sub-rede.

    ```azurecli
    subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

> [!IMPORTANT]
> Não se esqueça de [definir a regra padrão](#change-the-default-network-access-rule) para **negar** ou as regras de rede não terão efeito.

---

## <a name="grant-access-from-an-internet-ip-range"></a>Conceder acesso de um intervalo de IP de Internet

Você pode usar regras de rede IP para permitir o acesso de intervalos de endereços IP de Internet pública específicos criando regras de rede IP. Cada conta de armazenamento dá suporte a até 200 regras. Essas regras concedem acesso a serviços específicos baseados na Internet e redes locais e bloqueia o tráfego geral da Internet.

As restrições a seguir se aplicam a intervalos de endereços IP.

- As regras de rede IP são permitidas somente para endereços IP **públicos da Internet** . 

  Intervalos de endereços IP reservados para redes privadas (conforme definido em [RFC 1918](https://tools.ietf.org/html/rfc1918#section-3)) não são permitidos nas regras de IP. Redes privadas incluem endereços que começam com _10.*_ , _172.16.*_  - _172.31.*_ , e _192.168.*_ .

- Você deve fornecer intervalos de endereços de Internet permitidos usando a [notação CIDR](https://tools.ietf.org/html/rfc4632) no formato *16.17.18.0/24* ou como endereços IP individuais, como *16.17.18.19*. 

- Os intervalos de endereços pequenos usando o prefixo "/31" ou "/32" não têm suporte. Esses intervalos devem ser configurados usando regras de endereço IP individuais. 

- Somente endereços IPV4 são compatíveis com a configuração de regras de firewall de armazenamento.

As regras de rede IP não podem ser usadas nos seguintes casos:

- Para restringir o acesso a clientes na mesma região do Azure que a conta de armazenamento.
  
  As regras de rede IP não terão efeito sobre solicitações originadas da mesma região do Azure que a conta de armazenamento. Use as [regras de rede Virtual](#grant-access-from-a-virtual-network) para permitir solicitações da mesma região. 

- Restringir o acesso a clientes em uma [região emparelhada](../../best-practices-availability-paired-regions.md) que está em uma VNet que tem um ponto de extremidade de serviço.

- Para restringir o acesso aos serviços do Azure implantados na mesma região que a conta de armazenamento.

  Os serviços implantados na mesma região que a conta de armazenamento usam endereços IP privados do Azure para comunicação. Portanto, você não pode restringir o acesso a serviços específicos do Azure com base em seu intervalo de endereços IP de saída público.

### <a name="configuring-access-from-on-premises-networks"></a>Configurando o acesso de redes locais

Para conceder acesso de suas redes locais para sua conta de armazenamento com uma regra de rede IP, você deve identificar os endereços IP voltados para Internet usados por sua rede. Entre em contato com o administrador de rede para obter ajuda.

Se você estiver usando [ExpressRoute](../../expressroute/expressroute-introduction.md) de suas instalações, para emparelhamento público ou emparelhamento da Microsoft, será necessário identificar os endereços IP NAT usados. Para emparelhamento público, cada circuito do ExpressRoute usará dois endereços IP de NAT, que serão aplicados ao tráfego do serviço do Azure quando o tráfego entrar no backbone da rede do Microsoft Azure. Para emparelhamento da Microsoft, os endereços IP de NAT usados são fornecidos pelo cliente ou são fornecidos pelo provedor de serviço. Para permitir o acesso aos recursos do serviço, você deve permitir estes endereços IP públicos na configuração do firewall de IP do recurso. Para localizar os endereços IP do circuito do ExpressRoute de emparelhamento público, [abra um tíquete de suporte com o ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) por meio do Portal do Azure. Saiba mais sobre [NAT para emparelhamento público de ExpressRoute e emparelhamento da Microsoft.](../../expressroute/expressroute-nat.md#nat-requirements-for-azure-public-peering)

### <a name="managing-ip-network-rules"></a>Gerenciando regras de rede IP

Você pode gerenciar as regras de rede IP para contas de armazenamento através do portal do Azure, PowerShell ou CIv2.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Acesse a conta de armazenamento que você deseja proteger.

2. Selecione no menu configurações chamado **rede**.

3. Certifique-se de que você optou por permitir o acesso de **Redes selecionadas**.

4. Para conceder acesso a um intervalo de IP de Internet, insira o endereço IP ou o intervalo de endereços (no formato CIDR) em **Firewall** > **Intervalos de Endereços**.

5. Para remover uma regra de rede IP, selecione o ícone de Lixeira ao lado do intervalo de endereços.

6. Selecione **Salvar** para salvar suas alterações.

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Instalar o [Azure PowerShell](/powershell/azure/install-Az-ps) e [entrar](/powershell/azure/authenticate-azureps).

2. Liste as regras de rede IP.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").IPRules
    ```

3. Adicione uma regra de rede para um endereço IP individual.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

4. Adicione uma regra de rede para um intervalo de endereços IP.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

5. Remova uma regra de rede para um endereço IP individual.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

6. Remova uma regra de rede para um intervalo de endereços IP.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

> [!IMPORTANT]
> Não se esqueça de [definir a regra padrão](#change-the-default-network-access-rule) para **negar** ou as regras de rede não terão efeito.

#### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

1. Instalar a [CLI do Azure](/cli/azure/install-azure-cli) e [entrar](/cli/azure/authenticate-azure-cli).

1. Liste as regras de rede IP.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query ipRules
    ```

2. Adicione uma regra de rede para um endereço IP individual.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

3. Adicione uma regra de rede para um intervalo de endereços IP.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

4. Remova uma regra de rede para um endereço IP individual.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

5. Remova uma regra de rede para um intervalo de endereços IP.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

> [!IMPORTANT]
> Não se esqueça de [definir a regra padrão](#change-the-default-network-access-rule) para **negar** ou as regras de rede não terão efeito.

---

<a id="grant-access-specific-instances"></a>

## <a name="grant-access-from-azure-resource-instances-preview"></a>Conceder acesso de instâncias de recursos do Azure (versão prévia)

Em alguns casos, um aplicativo pode depender de recursos do Azure que não podem ser isolados por meio de uma rede virtual ou de uma regra de endereço IP. No entanto, você ainda gostaria de proteger e restringir o acesso à conta de armazenamento somente aos recursos do Azure de seu aplicativo. Você pode configurar contas de armazenamento para permitir o acesso a instâncias de recursos específicas de alguns serviços do Azure criando uma regra de instância de recurso. 

Os tipos de operações que uma instância de recurso pode executar nos dados da conta de armazenamento são determinados pelas [atribuições de função do Azure](storage-auth-aad.md#assign-azure-roles-for-access-rights) da instância de recurso. As instâncias de recurso devem ser do mesmo locatário como sua conta de armazenamento, mas podem pertencer a qualquer assinatura no locatário.

> [!NOTE]
> Esse recurso está em visualização pública e está disponível em todas as regiões de nuvem pública.

> [!NOTE]
> Atualmente, há suporte para regras de instância de recurso apenas para Synapse do Azure. O suporte para outros serviços do Azure listados na seção [acesso confiável baseado em identidade gerenciada atribuída pelo sistema](#trusted-access-system-assigned-managed-identity) deste artigo estará disponível nas próximas semanas.


### <a name="portal"></a>[Portal](#tab/azure-portal)

Você pode adicionar ou remover regras de rede de recursos no portal do Azure.

1. Entre no [portal do Azure](https://portal.azure.com/) para começar.

2. Localize sua conta de armazenamento e exiba a visão geral dela.

3. Selecione **rede** para exibir a página de configuração para rede.

4. Na lista suspensa **tipo de recurso** , escolha o tipo de recurso de sua instância de recurso. 

5. Na lista suspensa **nome da instância** , escolha a instância de recurso. Você também pode optar por incluir todas as instâncias de recurso no locatário ativo, na assinatura ou no grupo de recursos.

6. Selecione **Salvar** para salvar suas alterações. A instância de recurso aparece na seção **instâncias de recurso** da página Configurações de rede. 

Para remover a instância de recurso, selecione o ícone excluir ( :::image type="icon" source="media/storage-network-security/delete-icon.png"::: ) ao lado da instância de recurso.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Você pode usar comandos do PowerShell para adicionar ou remover regras de rede de recursos.

> [!IMPORTANT]
> Não se esqueça de [definir a regra padrão](#change-the-default-network-access-rule) para **negar** ou as regras de rede não terão efeito.

#### <a name="install-the-preview-module"></a>Instalar o módulo de versão prévia

Instale a versão mais recente do módulo PowershellGet. Em seguida, feche e reabra o console do PowerShell.

```powershell
install-Module PowerShellGet –Repository PSGallery –Force  
```

Instale o módulo de visualização **AZ. Storage** .

```powershell
Install-Module Az.Storage -Repository PsGallery -RequiredVersion 3.0.1-preview -AllowClobber -AllowPrerelease -Force 
```

Para obter mais informações sobre como instalar módulos do PowerShell, consulte [instalar o Azure PowerShell Module](/powershell/azure/install-az-ps)

#### <a name="grant-access"></a>Conceder acesso

Adicione uma regra de rede que conceda acesso de uma instância de recurso.

```powershell
$resourceId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.DataFactory/factories/myDataFactory"
$tenantId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

Add-AzStorageAccountNetworkRule -ResourceGroupName $resourceGroupName -Name $accountName -TenantId $tenantId -ResourceId $resourceId

```

Especifique várias instâncias de recurso ao mesmo tempo modificando o conjunto de regras de rede.

```powershell
$resourceId1 = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.DataFactory/factories/myDataFactory"
$resourceId2 = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/mySQLServer"
$tenantId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

Update-AzStorageAccountNetworkRuleSet -ResourceGroupName $resourceGroupName -Name $accountName -ResourceAccessRule (@{ResourceId=$resourceId1;TenantId=$tenantId},@{ResourceId=$resourceId2;TenantId=$tenantId}) 
```

#### <a name="remove-access"></a>Remover acesso

Remova uma regra de rede que conceda acesso de uma instância de recurso.

```powershell
$resourceId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.DataFactory/factories/myDataFactory"
$tenantId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

Remove-AzStorageAccountNetworkRule -ResourceGroupName $resourceGroupName -Name $accountName -TenantId $tenantId -ResourceId $resourceId  
```

Remova todas as regras de rede que concedem acesso de instâncias de recurso.

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

Update-AzStorageAccountNetworkRuleSet -ResourceGroupName $resourceGroupName -Name $accountName -ResourceAccessRule @()  
```

#### <a name="view-a-list-of-allowed-resource-instances"></a>Exibir uma lista de instâncias de recurso permitidas

Exiba uma lista completa de instâncias de recursos que receberam acesso à conta de armazenamento.

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

$rule = Get-AzStorageAccountNetworkRuleSet -ResourceGroupName $resourceGroupName -Name $accountName
$rule.ResourceAccessRules 
```

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Você pode usar CLI do Azure comandos para adicionar ou remover regras de rede de recursos.

#### <a name="install-the-preview-extension"></a>Instalar a extensão de visualização

1. Abra o [Azure Cloud Shell](../../cloud-shell/overview.md) ou, se você [instalou](/cli/azure/install-azure-cli) a CLI do Azure localmente, abra um aplicativo de console de comando, como Windows PowerShell.

2. Em seguida, verifique se a versão do CLI do Azure que você instalou é `2.13.0` ou superior usando o comando a seguir.

   ```azurecli
   az --version
   ```

   Se sua versão da CLI do Azure for inferior a `2.13.0`, instale uma versão posterior. Confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

3. Digite o comando a seguir para instalar a extensão de visualização.

   ```azurecli
   az extension add -n storage-preview
   ```

#### <a name="grant-access"></a>Conceder acesso

Adicione uma regra de rede que conceda acesso de uma instância de recurso.

```azurecli
az storage account network-rule add \
    --resource-id /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Synapse/workspaces/testworkspace \
    --tenant-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    -g myResourceGroup \
    --account-name mystorageaccount
```

#### <a name="remove-access"></a>Remover acesso

Remova uma regra de rede que conceda acesso de uma instância de recurso.

```azurecli
az storage account network-rule remove \
    --resource-id /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Synapse/workspaces/testworkspace \
    --tenant-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    -g myResourceGroup \
    --account-name mystorageaccount
```

#### <a name="view-a-list-of-allowed-resource-instances"></a>Exibir uma lista de instâncias de recurso permitidas

Exiba uma lista completa de instâncias de recursos que receberam acesso à conta de armazenamento.

```azurecli
az storage account network-rule list \
    -g myResourceGroup \
    --account-name mystorageaccount
```

---

<a id="exceptions"></a>
<a id="trusted-microsoft-services"></a>

## <a name="grant-access-to-trusted-azure-services"></a>Conceder acesso a serviços confiáveis do Azure 

Alguns serviços do Azure operam de redes que não podem ser incluídas em suas regras de rede. Você pode conceder a um subconjunto desses serviços confiáveis do Azure acesso à conta de armazenamento, mantendo as regras de rede para outros aplicativos. Esses serviços confiáveis usarão uma autenticação forte para se conectar com segurança à sua conta de armazenamento.

Você pode conceder acesso a serviços confiáveis do Azure criando uma exceção de regra de rede. Para obter diretrizes passo a passo, consulte a seção [gerenciar exceções](#manage-exceptions) deste artigo.

Ao conceder acesso a serviços confiáveis do Azure, você concede os seguintes tipos de acesso:

- Acesso confiável para operações SELECT para recursos que são registrados em sua assinatura.
- Acesso confiável a recursos com base na identidade gerenciada atribuída pelo sistema.

<a id="trusted-access-resources-in-subscription"></a>

### <a name="trusted-access-for-resources-registered-in-your-subscription"></a>Acesso confiável para recursos registrados em sua assinatura

Os recursos de alguns serviços, **quando registrados em sua assinatura**, podem acessar sua conta de armazenamento **na mesma assinatura** para um conjunto limitado de operações, como gravar logs ou fazer backup.  A tabela a seguir descreve cada serviço e as operações permitidas. 

| Serviço                  | Nome do provedor de recursos     | Operações permitidas                 |
|:------------------------ |:-------------------------- |:---------------------------------- |
| Serviço de Backup do Azure             | Microsoft.RecoveryServices | Execute backups e restaurações de discos não gerenciados em máquinas virtuais IAAS. (não é necessário para discos gerenciados). [Saiba mais](../../backup/backup-overview.md). |
| Azure Data Box           | Microsoft.DataBox          | Permite a importação de dados para o Azure usando o Data Box. [Saiba mais](../../databox/data-box-overview.md). |
| Azure DevTest Labs       | Microsoft.DevTestLab       | Criação de imagem personalizada e instalação de artefato. [Saiba mais](../../devtest-labs/devtest-lab-overview.md). |
| Grade de Eventos do Azure         | Microsoft.EventGrid        | Habilite a publicação de eventos do Armazenamento de Blobs e permita que a Grade de Eventos publique em filas de armazenamento. Saiba mais sobre [eventos de Armazenamento de Blobs](../../event-grid/overview.md#event-sources) e [publicação em filas](../../event-grid/event-handlers.md). |
| Hubs de eventos do Azure         | Microsoft.EventHub         | Arquivar dados com a Captura de Hubs de Evento. [Saiba mais](../../event-hubs/event-hubs-capture-overview.md). |
| Sincronização de Arquivos do Azure          | Microsoft.StorageSync      | Permite transformar seu servidor de arquivos local em um cache para compartilhamentos de Arquivos do Azure. Isso permite a sincronização de vários sites, recuperação rápida de desastre e backup no lado da nuvem. [Saiba mais](../files/storage-sync-files-planning.md) |
| Azure HDInsight          | Microsoft.HDInsight        | Provisione o conteúdo inicial do sistema de arquivos padrão para um novo cluster HDInsight. [Saiba mais](../../hdinsight/hdinsight-hadoop-use-blob-storage.md). |
| Importação/Exportação do Azure      | Microsoft.ImportExport     | Permite a importação de dados para o armazenamento do Azure ou a exportação de dados do armazenamento do Azure usando o serviço de importação/exportação do armazenamento do Azure. [Saiba mais](../../import-export/storage-import-export-service.md).  |
| Azure Monitor            | Microsoft.insights         | Permite gravar dados de monitoramento em uma conta de armazenamento protegida, incluindo logs de recursos, logs de entrada e de auditoria do Azure Active Directory e logs do Microsoft Intune. [Saiba mais](../../azure-monitor/roles-permissions-security.md). |
| Rede do Azure         | Microsoft.Network          | Armazene e analise logs de tráfego de rede, incluindo por meio do observador de rede e serviços de Análise de Tráfego. [Saiba mais](../../network-watcher/network-watcher-nsg-flow-logging-overview.md). |
| Azure Site Recovery      | Microsoft.SiteRecovery     | Habilite a replicação para recuperação de desastre de máquinas virtuais de IaaS do Azure ao usar as contas de armazenamento de cache, origem ou destino habilitadas para firewall.  [Saiba mais](../../site-recovery/azure-to-azure-tutorial-enable-replication.md). |

<a id="trusted-access-system-assigned-managed-identity"></a>

### <a name="trusted-access-based-on-system-assigned-managed-identity"></a>Acesso confiável com base na identidade gerenciada atribuída pelo sistema

A tabela a seguir lista os serviços que podem ter acesso aos dados da sua conta de armazenamento se as instâncias de recurso desses serviços recebem a permissão apropriada. Para conceder permissão, você deve [atribuir explicitamente uma função do Azure](storage-auth-aad.md#assign-azure-roles-for-access-rights) à [identidade gerenciada atribuída pelo sistema](../../active-directory/managed-identities-azure-resources/overview.md) para cada instância de recurso. Nesse caso, o escopo de acesso para a instância corresponde à função do Azure atribuída à identidade gerenciada. 

> [!TIP]
> A maneira recomendada para conceder acesso a recursos específicos é usar regras de instância de recurso. Para conceder acesso a instâncias de recurso específicas, consulte a seção [conceder acesso de instâncias de recursos do Azure (versão prévia)](#grant-access-specific-instances) deste artigo.


| Serviço                        | Nome do provedor de recursos                 | Finalidade            |
| :----------------------------- | :------------------------------------- | :----------------- |
| Gerenciamento de API do Azure           | Microsoft.ApiManagement/service        | Habilita o acesso do serviço de gerenciamento de API a contas de armazenamento por trás do firewall usando políticas. [Saiba mais](../../api-management/api-management-authentication-policies.md#use-managed-identity-in-send-request-policy). |
| Pesquisa Cognitiva do Azure         | Microsoft.Search/searchServices        | Permite que os serviços do Cognitive Search acessem contas de armazenamento para indexação, processamento e consulta. |
| Serviços Cognitivos do Azure       | Microsoft. CognitiveService/accounts    | Permite que serviços cognitivas acessem contas de armazenamento. |
| Tarefas do Registro de Contêiner do Azure | Microsoft.ContainerRegistry/registries | As Tarefas do ACR podem acessar contas de armazenamento ao criar imagens de contêiner. |
| Fábrica de dados do Azure             | Microsoft.DataFactory/factories        | Permite o acesso a contas de armazenamento por meio do runtime do ADF. |
| Azure Data Share               | Microsoft.DataShare/accounts           | Permite o acesso a contas de armazenamento por meio do Data Share. |
| Azure DevTest Labs             | Microsoft.DevTestLab/labs              | Permite o acesso a contas de armazenamento por meio do DevTest Labs. |
| Hub IoT do Azure                  | Microsoft.Devices/IotHubs              | Permite que os dados de um hub IoT sejam gravados no Armazenamento de Blobs. [Saiba mais](../../iot-hub/virtual-network-support.md#egress-connectivity-to-storage-account-endpoints-for-routing) |
| Aplicativos Lógicos do Azure               | Microsoft.Logic/workflows              | Permite que os aplicativos lógicos acessem contas de armazenamento. [Saiba mais](../../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity). |
| Serviço do Azure Machine Learning | Microsoft.MachineLearningServices      | Os workspaces autorizados do Azure Machine Learning gravam a saída, os modelos e os logs do experimento no Armazenamento de Blobs e leem os dados. [Saiba mais](../../machine-learning/how-to-network-security-overview.md#secure-the-workspace-and-associated-resources). |
| Serviços de Mídia do Azure           | Microsoft.Media/mediaservices          | Permite o acesso a contas de armazenamento por meio dos serviços de mídia. |
| Migrações para Azure                  | Microsoft. Migrate/migrateprojects      | Permite o acesso a contas de armazenamento por meio de migrações para Azure. |
| Azure Purview                  | Microsoft. alcance/accounts             | Permite que o alcance acesse contas de armazenamento. |
| Azure Remote Rendering         | Microsoft. MixedReality/remoteRenderingAccounts | Permite o acesso a contas de armazenamento por meio de renderização remota. |
| Azure Site Recovery            | Microsoft.RecoveryServices/vaults      | Permite o acesso a contas de armazenamento por meio de Site Recovery. |
| Banco de Dados SQL do Azure             | Microsoft.Sql                          | Permite [gravar](../../azure-sql/database/audit-write-storage-account-behind-vnet-firewall.md) dados de auditoria em contas de armazenamento por trás do firewall. |
| Azure Synapse Analytics        | Microsoft.Sql                          | Permite a importação e a exportação de dados de bancos de dado SQL específicos usando a instrução de cópia ou o polybase (no pool dedicado) ou a `openrowset` função e tabelas externas no pool sem servidor. [Saiba mais](../../azure-sql/database/vnet-service-endpoint-rule-overview.md). |
| Stream Analytics do Azure         | Microsoft.StreamAnalytics              | Permite que os dados de um trabalho de streaming sejam gravados no Armazenamento de Blobs. [Saiba mais](../../stream-analytics/blob-output-managed-identity.md). |
| Azure Synapse Analytics        | Microsoft.Synapse/workspaces           | Habilita o acesso a dados no armazenamento do Azure do Azure Synapse Analytics. |

## <a name="grant-access-to-storage-analytics"></a>Conceder acesso à análise de armazenamento

Em alguns casos, o acesso para ler as métricas e logs de recurso é necessário de fora do limite de rede. Ao configurar o acesso de serviços confiáveis à conta de armazenamento, você pode permitir o acesso de leitura para os arquivos de log, as tabelas de métricas ou ambos criando uma exceção de regra de rede. Para obter diretrizes passo a passo, consulte a seção **gerenciar exceções** abaixo. Para saber mais sobre como trabalhar com a análise de armazenamento, consulte [usar a análise de armazenamento do Azure para coletar dados de logs e métricas](./storage-analytics.md). 

<a id="manage-exceptions"></a>

## <a name="manage-exceptions"></a>Gerenciar exceções

Você pode gerenciar as exceções de regra da rede através do portal do Azure, PowerShell ou CLI do Azure v2.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Acesse a conta de armazenamento que você deseja proteger.

2. Selecione no menu configurações chamado **rede**.

3. Certifique-se de que você optou por permitir o acesso de **Redes selecionadas**.

4. Em **Exceções**, selecione as exceções que deseja conceder.

5. Selecione **Salvar** para salvar suas alterações.

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Instalar o [Azure PowerShell](/powershell/azure/install-Az-ps) e [entrar](/powershell/azure/authenticate-azureps).

2. Exiba as exceções para as regras de rede da conta de armazenamento.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount").Bypass
    ```

3. Configure as exceções para as regras de rede da conta de armazenamento.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass AzureServices,Metrics,Logging
    ```

4. Remova as exceções para as regras de rede da conta de armazenamento.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass None
    ```

> [!IMPORTANT]
> Não se esqueça de [definir a regra padrão](#change-the-default-network-access-rule) para **negar** ou a remoção das exceções não terá efeito.

#### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

1. Instalar a [CLI do Azure](/cli/azure/install-azure-cli) e [entrar](/cli/azure/authenticate-azure-cli).

2. Exiba as exceções para as regras de rede da conta de armazenamento.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.bypass
    ```

3. Configure as exceções para as regras de rede da conta de armazenamento.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass Logging Metrics AzureServices
    ```

4. Remova as exceções para as regras de rede da conta de armazenamento.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass None
    ```

> [!IMPORTANT]
> Não se esqueça de [definir a regra padrão](#change-the-default-network-access-rule) para **negar** ou a remoção das exceções não terá efeito.

---

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre os Pontos de Extremidade do Serviço de Rede do Azure em [Pontos de Extremidade de Serviço](../../virtual-network/virtual-network-service-endpoints-overview.md).

Aprofunde-se na segurança do Armazenamento do Microsoft Azure no [Guia de segurança do Armazenamento do Azure](../blobs/security-recommendations.md).