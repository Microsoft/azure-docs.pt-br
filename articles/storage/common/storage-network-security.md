---
title: Configurar redes virtuais e firewalls do Armazenamento do Microsoft Azure | Microsoft Docs
description: Configure a segurança de rede em camadas para sua conta de armazenamento usando os firewalls de armazenamento do Azure e a rede virtual do Azure.
services: storage
author: santoshc
ms.service: storage
ms.topic: how-to
ms.date: 12/08/2020
ms.author: tamram
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: 13d1ad0b1b5e32ea2ca86e7556dd910c542bcbe2
ms.sourcegitcommit: 3af12dc5b0b3833acb5d591d0d5a398c926919c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/11/2021
ms.locfileid: "98070551"
---
# <a name="configure-azure-storage-firewalls-and-virtual-networks"></a>Configurar redes virtuais e firewalls do Armazenamento do Microsoft Azure

O Armazenamento do Microsoft Azure fornece um modelo de segurança em camadas. Esse modelo permite que você proteja e controle o nível de acesso às suas contas de armazenamento que seus aplicativos e ambientes empresariais exigem, com base no tipo e no subconjunto das redes usadas. Quando as regras de rede são configuradas, somente dados de solicitação de aplicativos do conjunto especificado de redes podem acessar uma conta de armazenamento. Você pode limitar o acesso à sua conta de armazenamento a solicitações originadas de endereços IP especificados, intervalos de IP ou de uma lista de sub-redes em uma VNet (Rede Virtual) do Azure.

As contas de armazenamento têm um ponto de extremidade público que pode ser acessado pela Internet. Você também pode criar [pontos de extremidade privados para sua conta de armazenamento](storage-private-endpoints.md), que atribui um endereço IP privado de sua VNet à conta de armazenamento e protege todo o tráfego entre a VNet e a conta de armazenamento por meio de um link privado. O Firewall do armazenamento do Azure fornece controle de acesso para o ponto de extremidade público da sua conta de armazenamento. Você também pode usar o firewall para bloquear todo o acesso por meio do ponto de extremidade público ao usar pontos de extremidades privados. A configuração do firewall de armazenamento também permite selecionar serviços confiáveis da plataforma Azure para acessar a conta de armazenamento com segurança.

Um aplicativo que acessa uma conta de armazenamento quando as regras de rede estão em vigor ainda requer autorização adequada para a solicitação. A autorização é compatível com as credenciais do Azure AD (Azure Active Directory) para blobs e filas, com uma chave de acesso de conta válida ou um token SAS.

> [!IMPORTANT]
> Ativar regras de firewall para sua conta de armazenamento bloqueia solicitações de entrada para os dados por padrão, a menos que as solicitações sejam provenientes de um serviço que está operando em uma VNet (Rede Virtual) do Azure ou de endereços IP públicos permitidos. Solicitações que estão bloqueadas incluem as de outros serviços do Azure, do portal do Azure, de registro em log e serviços de métricas e assim por diante.
>
> Você pode permitir acesso aos serviços do Azure que operam de dentro de uma rede virtual, permitindo tráfego da sub-rede hospedando a instância do serviço. Você também pode habilitar um número limitado de cenários por meio do mecanismo [Exceções](#exceptions) descrito abaixo. O acesso a dados da conta de armazenamento por meio do portal do Azure precisa ser feito de um computador dentro do limite confiável (IP ou VNet) que você configurou.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="scenarios"></a>Cenários

Para proteger sua conta de armazenamento, primeiro você deve configurar uma regra para negar o acesso ao tráfego de todas as redes (incluindo o tráfego da Internet) no ponto de extremidade público, por padrão. Em seguida, você deve configurar regras que permitem acesso ao tráfego de VNets específicas. Você também pode configurar regras para conceder acesso ao tráfego de intervalos de endereços IP de Internet públicos selecionados, permitindo conexões de clientes locais ou da Internet específicos. Essa configuração permite que você crie um limite de rede seguro para seus aplicativos.

Você pode combinar regras de firewall que permitem o acesso de redes virtuais específicas e de intervalos de endereços IP públicos na mesma conta de armazenamento. As regras de firewall de armazenamento podem ser aplicadas a contas de armazenamento existentes ou durante a criação de contas de armazenamento.

As regras de firewall de armazenamento se aplicam ao ponto de extremidade público de uma conta de armazenamento. Você não precisa de nenhuma regra de acesso de firewall para permitir o tráfego para pontos de extremidade privados de uma conta de armazenamento. O processo de aprovar a criação de um ponto de extremidade privado concede acesso implícito ao tráfego da sub-rede que hospeda o ponto de extremidade privado.

As regras de rede são impostas em todos os protocolos de rede para o armazenamento do Azure, incluindo REST e SMB. Para acessar os dados usando ferramentas como o portal do Azure, o Gerenciador de Armazenamento e o AZCopy, é necessário configurar regras de rede explícitas.

Depois que as regras de rede são aplicadas, elas são impostas para todas as solicitações. Os tokens SAS que concedem acesso a um serviço de endereço IP específico limitam o acesso do proprietário do token, mas não concedem um novo acesso além das regras de rede configuradas.

O tráfego de disco da máquina virtual (incluindo as operações de montagem e desmontagem e E/S de disco) não é afetado pelas regras de rede. O acesso REST a blobs de página é protegido pelas regras de rede.

As contas de armazenamento clássicas não dão suporte a firewalls e redes virtuais.

Você pode usar discos não gerenciados nas contas de armazenamento com as regras de rede aplicadas às VMs de backup e restauração com a criação de uma exceção. Esse processo está documentado na seção [Exceções](#exceptions) deste artigo. Exceções de firewall não são aplicáveis com discos gerenciados, pois eles já são gerenciados pelo Azure.

## <a name="change-the-default-network-access-rule"></a>Alterar a regra de acesso de rede padrão

Por padrão, as contas de armazenamento aceitam conexões de clientes em qualquer rede. Para limitar o acesso a redes selecionadas, primeiro você deve alterar a ação padrão.

> [!WARNING]
> Fazer alterações em regras de rede pode afetar a capacidade de seus aplicativos se conectarem ao Armazenamento do Azure. Definir a regra de rede padrão como **negar** bloqueia todo o acesso aos dados, a menos que regras específicas de rede que **concedam** o acesso também sejam aplicadas. Certifique-se de conceder acesso a qualquer uma das redes permitidas usando regras de rede antes de alterar a regra padrão para negar o acesso.

### <a name="managing-default-network-access-rules"></a>Como alterar as regras de acesso de rede padrão

Você pode gerar as regras de acesso à rede padrão para contas de armazenamento através do portal do Azure, PowerShell ou CLIv2.

#### <a name="azure-portal"></a>Portal do Azure

1. Vá até a conta de armazenamento que você deseja proteger.

1. Clique no menu configurações chamado **rede**.

1. Para negar o acesso por padrão, opte por permitir o acesso de **Redes selecionadas**. Para permitir o tráfego de todas as redes, opte por permitir o acesso de **Todas as redes**.

1. Clique em **Salvar** para aplicar suas alterações.

#### <a name="powershell"></a>PowerShell

1. Instalar o [Azure PowerShell](/powershell/azure/install-Az-ps) e [entrar](/powershell/azure/authenticate-azureps).

1. Exiba o status da regra padrão para a conta de armazenamento.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").DefaultAction
    ```

1. Defina a regra padrão para negar o acesso à rede por padrão.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Deny
    ```

1. Defina a regra padrão para permitir o acesso à rede por padrão.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Allow
    ```

#### <a name="cliv2"></a>CLIv2

1. Instalar a [CLI do Azure](/cli/azure/install-azure-cli) e [entrar](/cli/azure/authenticate-azure-cli).

1. Exiba o status da regra padrão para a conta de armazenamento.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.defaultAction
    ```

1. Defina a regra padrão para negar o acesso à rede por padrão.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Deny
    ```

1. Defina a regra padrão para permitir o acesso à rede por padrão.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Allow
    ```

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

Para aplicar uma regra da rede virtual a uma conta de armazenamento, o usuário deve ter permissão para as sub-redes sendo adicionadas. A permissão necessária é *Ingressar o Serviço em uma Sub-rede* e está incluída na função interna *Colaborador da conta de armazenamento*. Também podem ser adicionado às definições de função personalizada.

A conta de armazenamento e o acesso concedido às redes virtuais podem estar em assinaturas diferentes, incluindo assinaturas que são parte do mesmo locatário do Azure AD.

> [!NOTE]
> A configuração de regras que permitem acesso a sub-redes em redes virtuais que fazem parte de um locatário diferente do Azure Active Directory atualmente só é compatível por meio do PowerShell, da CLI e de APIs REST. Essas regras não podem ser configuradas por meio do portal do Azure, embora ele possa exibi-las.

### <a name="managing-virtual-network-rules"></a>Gerenciando regras da rede virtual

Você pode gerenciar as regras da rede virtual para contas de armazenamento através do portal do Azure, PowerShell ou CIv2.

#### <a name="azure-portal"></a>Portal do Azure

1. Vá até a conta de armazenamento que você deseja proteger.

1. Clique no menu configurações chamado **rede**.

1. Certifique-se de que você optou por permitir o acesso de **Redes selecionadas**.

1. Para conceder acesso a uma rede virtual com uma nova regra de rede, em **Redes virtuais**, clique em **Adicionar rede virtual existente**, selecione **Redes virtuais** e as opções **Sub-redes** e clique **Adicionar**. Para criar uma nova rede virtual e conceder acesso, clique em **Adicionar nova rede virtual**. Forneça as informações necessárias para criar a nova rede virtual e, em seguida, clique em **Criar**.

    > [!NOTE]
    > Se um ponto de extremidade de Armazenamento do Microsoft Azure não foi configurado anteriormente para a rede virtual selecionada e as sub-redes, você pode configurá-lo como parte dessa operação.
    >
    > Atualmente, somente as redes virtuais que pertencem ao mesmo locatário do Azure Active Directory são mostradas para seleção durante a criação de regras. Para permitir acesso a uma sub-rede em uma rede virtual que pertence a outro locatário, use o PowerShell, a CLI ou as APIs REST.

1. Para remover uma regra de rede virtual ou sub-rede, clique em **...** para abrir o menu de contexto da rede virtual ou da sub-rede e clique em **Remover**.

1. Clique em **Salvar** para aplicar suas alterações.

#### <a name="powershell"></a>PowerShell

1. Instalar o [Azure PowerShell](/powershell/azure/install-Az-ps) e [entrar](/powershell/azure/authenticate-azureps).

1. Liste as regras da rede virtual.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").VirtualNetworkRules
    ```

1. Habilite o ponto de extremidade de serviço do Armazenamento do Microsoft Azure em uma rede virtual existente e sub-rede.

    ```powershell
    Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.0.0.0/24" -ServiceEndpoint "Microsoft.Storage" | Set-AzVirtualNetwork
    ```

1. Adicionar uma regra de rede para uma rede virtual e sub-rede.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

    > [!TIP]
    > Para adicionar uma regra de rede para uma sub-rede em uma VNet que pertence a outro locatário do Azure AD, use um parâmetro **VirtualNetworkResourceId** totalmente qualificado no formato "/subscriptions/subscription-ID/resourceGroups/resourceGroup-Name/providers/Microsoft.Network/virtualNetworks/vNet-name/subnets/nome-da-sub-rede".

1. Remova uma regra de rede para uma rede virtual e uma sub-rede.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

> [!IMPORTANT]
> Não se esqueça de [definir a regra padrão](#change-the-default-network-access-rule) para **negar** ou as regras de rede não terão efeito.

#### <a name="cliv2"></a>CLIv2

1. Instalar a [CLI do Azure](/cli/azure/install-azure-cli) e [entrar](/cli/azure/authenticate-azure-cli).

1. Liste as regras da rede virtual.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query virtualNetworkRules
    ```

1. Habilite o ponto de extremidade de serviço do Armazenamento do Microsoft Azure em uma rede virtual existente e sub-rede.

    ```azurecli
    az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.Storage"
    ```

1. Adicionar uma regra de rede para uma rede virtual e sub-rede.

    ```azurecli
    subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

    > [!TIP]
    > Para adicionar uma regra para uma sub-rede em uma VNet que pertence a outro locatário do Azure AD, use uma ID de sub-rede totalmente qualificada no formato "/subscriptions/ \<subscription-ID\> /ResourceGroups/ \<resourceGroup-Name\> /Providers/Microsoft.Network/virtualNetworks/ \<vNet-name\> /Subnets/ \<subnet-name\> ".
    >
    > Você pode usar o parâmetro **subscription** para recuperar a ID de sub-rede de uma VNet que pertence a outro locatário do Azure AD.

1. Remova uma regra de rede para uma rede virtual e uma sub-rede.

    ```azurecli
    subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

> [!IMPORTANT]
> Não se esqueça de [definir a regra padrão](#change-the-default-network-access-rule) para **negar** ou as regras de rede não terão efeito.

## <a name="grant-access-from-an-internet-ip-range"></a>Conceder acesso de um intervalo de IP de Internet

Você pode configurar as contas de armazenamento para permitir acesso dos intervalos de endereço IP da Internet pública. Essa configuração garante acesso a serviços públicos baseados na Internet e redes locais e bloqueia o tráfego de Internet geral.

Forneça intervalos de endereços de Internet permitidos usando a [notação CIDR](https://tools.ietf.org/html/rfc4632) no formulário *16.17.18.0/24* ou como endereços IP individuas como *16.17.18.19*.

   > [!NOTE]
   > Os intervalos de endereços pequenos usando o prefixo "/31" ou "/32" não têm suporte. Esses intervalos devem ser configurados usando regras de endereço IP individuais.

As regras de rede de IP são permitidas apenas para endereços IP de **Internet pública**. Intervalos de endereços IP reservados para redes privadas (conforme definido em [RFC 1918](https://tools.ietf.org/html/rfc1918#section-3)) não são permitidos nas regras de IP. Redes privadas incluem endereços que começam com _10.*_ , _172.16.*_  - _172.31.*_ , e _192.168.*_ .

   > [!NOTE]
   > As regras de rede IP não terão efeito sobre solicitações originadas da mesma região do Azure que a conta de armazenamento. Use as [regras de rede Virtual](#grant-access-from-a-virtual-network) para permitir solicitações da mesma região.

  > [!NOTE]
  > Os serviços implantados na mesma região que a conta de armazenamento usam endereços IP privados do Azure para comunicação. Portanto, você não pode restringir o acesso a serviços específicos do Azure com base nos respectivos intervalos de endereços IP de saída públicos.

Somente endereços IPV4 são compatíveis com a configuração de regras de firewall de armazenamento.

Cada conta de armazenamento dá suporte a até 200 regras de rede IP.

### <a name="configuring-access-from-on-premises-networks"></a>Configurando o acesso de redes locais

Para conceder acesso de suas redes locais para sua conta de armazenamento com uma regra de rede IP, você deve identificar os endereços IP voltados para Internet usados por sua rede. Entre em contato com o administrador de rede para obter ajuda.

Se você estiver usando [ExpressRoute](../../expressroute/expressroute-introduction.md) de suas instalações, para emparelhamento público ou emparelhamento da Microsoft, será necessário identificar os endereços IP NAT usados. Para emparelhamento público, cada circuito do ExpressRoute usará dois endereços IP de NAT, que serão aplicados ao tráfego do serviço do Azure quando o tráfego entrar no backbone da rede do Microsoft Azure. Para emparelhamento da Microsoft, os endereços IP de NAT usados são fornecidos pelo cliente ou são fornecidos pelo provedor de serviço. Para permitir o acesso aos recursos do serviço, você deve permitir estes endereços IP públicos na configuração do firewall de IP do recurso. Para localizar os endereços IP do circuito do ExpressRoute de emparelhamento público, [abra um tíquete de suporte com o ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) por meio do Portal do Azure. Saiba mais sobre [NAT para emparelhamento público de ExpressRoute e emparelhamento da Microsoft.](../../expressroute/expressroute-nat.md#nat-requirements-for-azure-public-peering)

### <a name="managing-ip-network-rules"></a>Gerenciando regras de rede IP

Você pode gerenciar as regras de rede IP para contas de armazenamento através do portal do Azure, PowerShell ou CIv2.

#### <a name="azure-portal"></a>Portal do Azure

1. Vá até a conta de armazenamento que você deseja proteger.

1. Clique no menu configurações chamado **rede**.

1. Certifique-se de que você optou por permitir o acesso de **Redes selecionadas**.

1. Para conceder acesso a um intervalo de IP de Internet, insira o endereço IP ou o intervalo de endereços (no formato CIDR) em **Firewall** > **Intervalos de Endereços**.

1. Para remover uma regra de rede IP, clique no ícone de lixeira próximo da regra de endereço.

1. Clique em **Salvar** para aplicar suas alterações.

#### <a name="powershell"></a>PowerShell

1. Instalar o [Azure PowerShell](/powershell/azure/install-Az-ps) e [entrar](/powershell/azure/authenticate-azureps).

1. Liste as regras de rede IP.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").IPRules
    ```

1. Adicione uma regra de rede para um endereço IP individual.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

1. Adicione uma regra de rede para um intervalo de endereços IP.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

1. Remova uma regra de rede para um endereço IP individual.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

1. Remova uma regra de rede para um intervalo de endereços IP.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

> [!IMPORTANT]
> Não se esqueça de [definir a regra padrão](#change-the-default-network-access-rule) para **negar** ou as regras de rede não terão efeito.

#### <a name="cliv2"></a>CLIv2

1. Instalar a [CLI do Azure](/cli/azure/install-azure-cli) e [entrar](/cli/azure/authenticate-azure-cli).

1. Liste as regras de rede IP.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query ipRules
    ```

1. Adicione uma regra de rede para um endereço IP individual.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

1. Adicione uma regra de rede para um intervalo de endereços IP.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

1. Remova uma regra de rede para um endereço IP individual.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

1. Remova uma regra de rede para um intervalo de endereços IP.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

> [!IMPORTANT]
> Não se esqueça de [definir a regra padrão](#change-the-default-network-access-rule) para **negar** ou as regras de rede não terão efeito.

## <a name="exceptions"></a>Exceções

As regras de rede ajudam a criar um ambiente seguro para conexões entre seus aplicativos e seus dados para a maioria dos cenários. No entanto, alguns aplicativos dependem de serviços do Azure que não podem ser isolados exclusivamente por meio de regras de endereço IP ou de rede virtual. Mas, para habilitar a funcionalidade completa do aplicativo, é preciso permitir acesso ao armazenamento para esses serviços. Nessas situações, você pode usar o **_permitir serviços confiáveis da Microsoft..._* _ configure para permitir que esses serviços acessem seus dados, logs ou análises.

### <a name="trusted-microsoft-services"></a>Serviços Microsoft confiáveis

Alguns serviços da Microsoft operam de redes que não podem ser incluídas em suas regras de rede. Você pode conceder, a um subconjunto desses serviços confiáveis da Microsoft, acesso à conta de armazenamento, mantendo as regras de rede para outros aplicativos. Esses serviços confiáveis usarão a autenticação forte para se conectar à sua conta de armazenamento com segurança. Habilitamos dois modos de acesso confiável para serviços Microsoft.

- Os recursos de alguns serviços, _ * quando registrados em sua assinatura * *, podem acessar sua conta de armazenamento **na mesma assinatura** para operações SELECT, como gravar logs ou backup.
- Os recursos de alguns serviços podem receber acesso explícito à sua conta de armazenamento **atribuindo uma função do Azure** à sua identidade gerenciada atribuída pelo sistema.


Quando você habilita a configuração **Permitir serviços confiáveis da Microsoft...** , os recursos dos seguintes serviços registrados na mesma assinatura que sua conta de armazenamento recebem acesso a um conjunto limitado de operações, conforme descrito:

| Serviço                  | Nome do provedor de recursos     | Operações permitidas                 |
|:------------------------ |:-------------------------- |:---------------------------------- |
| Serviço de Backup do Azure             | Microsoft.RecoveryServices | Execute backups e restaurações de discos não gerenciados em máquinas virtuais IAAS. (não é necessário para discos gerenciados). [Saiba mais](../../backup/backup-overview.md). |
| Azure Data Box           | Microsoft.DataBox          | Permite a importação de dados para o Azure usando o Data Box. [Saiba mais](../../databox/data-box-overview.md). |
| Azure DevTest Labs       | Microsoft.DevTestLab       | Criação de imagem personalizada e instalação de artefato. [Saiba mais](../../devtest-labs/devtest-lab-overview.md). |
| Grade de Eventos do Azure         | Microsoft.EventGrid        | Habilite a publicação de eventos do Armazenamento de Blobs e permita que a Grade de Eventos publique em filas de armazenamento. Saiba mais sobre [eventos de Armazenamento de Blobs](../../event-grid/overview.md#event-sources) e [publicação em filas](../../event-grid/event-handlers.md). |
| Hubs de eventos do Azure         | Microsoft.EventHub         | Arquivar dados com a Captura de Hubs de Evento. [Saiba mais](../../event-hubs/event-hubs-capture-overview.md). |
| Sincronização de Arquivos do Azure          | Microsoft.StorageSync      | Permite transformar seu servidor de arquivos local em um cache para compartilhamentos de Arquivos do Azure. Isso permite a sincronização de vários sites, recuperação rápida de desastre e backup no lado da nuvem. [Saiba mais](../files/storage-sync-files-planning.md) |
| Azure HDInsight          | Microsoft.HDInsight        | Provisione o conteúdo inicial do sistema de arquivos padrão para um novo cluster HDInsight. [Saiba mais](../../hdinsight/hdinsight-hadoop-use-blob-storage.md). |
| Importação/Exportação do Azure      | Microsoft.ImportExport     | Permite a importação de dados para o armazenamento do Azure ou a exportação de dados do armazenamento do Azure usando o serviço de importação/exportação do armazenamento do Azure. [Saiba mais](./storage-import-export-service.md).  |
| Azure Monitor            | Microsoft.insights         | Permite gravar dados de monitoramento em uma conta de armazenamento protegida, incluindo logs de recursos, logs de entrada e de auditoria do Azure Active Directory e logs do Microsoft Intune. [Saiba mais](../../azure-monitor/platform/roles-permissions-security.md). |
| Rede do Azure         | Microsoft.Network          | Armazene e analise logs de tráfego de rede, incluindo por meio do observador de rede e serviços de Análise de Tráfego. [Saiba mais](../../network-watcher/network-watcher-nsg-flow-logging-overview.md). |
| Azure Site Recovery      | Microsoft.SiteRecovery     | Habilite a replicação para recuperação de desastre de máquinas virtuais de IaaS do Azure ao usar as contas de armazenamento de cache, origem ou destino habilitadas para firewall.  [Saiba mais](../../site-recovery/azure-to-azure-tutorial-enable-replication.md). |

A configuração **permitir serviços confiáveis da Microsoft...** também permite que uma instância específica dos serviços a seguir acesse a conta de armazenamento, se você [atribuir explicitamente uma função do Azure](storage-auth-aad.md#assign-azure-roles-for-access-rights) à [identidade gerenciada atribuída pelo sistema](../../active-directory/managed-identities-azure-resources/overview.md) para essa instância de recurso. Nesse caso, o escopo de acesso para a instância corresponde à função do Azure atribuída à identidade gerenciada.

| Serviço                        | Nome do provedor de recursos                 | Finalidade            |
| :----------------------------- | :------------------------------------- | :----------------- |
| Gerenciamento de API do Azure           | Microsoft.ApiManagement/service        | Habilita o acesso do serviço de gerenciamento de API a contas de armazenamento por trás do firewall usando políticas. [Saiba mais](../../api-management/api-management-authentication-policies.md#use-managed-identity-in-send-request-policy). |
| Pesquisa Cognitiva do Azure         | Microsoft.Search/searchServices        | Permite que os serviços do Cognitive Search acessem contas de armazenamento para indexação, processamento e consulta. |
| Serviços Cognitivos do Azure       | Microsoft. CognitiveService             | Permite que serviços cognitivas acessem contas de armazenamento. |
| Tarefas do Registro de Contêiner do Azure | Microsoft.ContainerRegistry/registries | As Tarefas do ACR podem acessar contas de armazenamento ao criar imagens de contêiner. |
| Fábrica de dados do Azure             | Microsoft.DataFactory/factories        | Permite o acesso a contas de armazenamento por meio do runtime do ADF. |
| Azure Data Share               | Microsoft.DataShare/accounts           | Permite o acesso a contas de armazenamento por meio do Data Share. |
| Hub IoT do Azure                  | Microsoft.Devices/IotHubs              | Permite que os dados de um hub IoT sejam gravados no Armazenamento de Blobs. [Saiba mais](../../iot-hub/virtual-network-support.md#egress-connectivity-to-storage-account-endpoints-for-routing) |
| Aplicativos Lógicos do Azure               | Microsoft.Logic/workflows              | Permite que os aplicativos lógicos acessem contas de armazenamento. [Saiba mais](../../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity). |
| Serviço do Azure Machine Learning | Microsoft.MachineLearningServices      | Os workspaces autorizados do Azure Machine Learning gravam a saída, os modelos e os logs do experimento no Armazenamento de Blobs e leem os dados. [Saiba mais](../../machine-learning/how-to-network-security-overview.md#secure-the-workspace-and-associated-resources). | 
| Azure Synapse Analytics       | Microsoft.Sql                          | Permite a importação e a exportação de dados de bancos de dado SQL específicos usando a instrução de cópia ou o polybase. [Saiba mais](../../azure-sql/database/vnet-service-endpoint-rule-overview.md). |
| Banco de Dados SQL do Azure       | Microsoft.Sql                          | Permite a [importação](/sql/t-sql/statements/bulk-insert-transact-sql#f-importing-data-from-a-file-in-azure-blob-storage) de dados de contas de armazenamento e a [gravação](../../azure-sql/database/audit-write-storage-account-behind-vnet-firewall.md) de dados de auditoria em contas de armazenamento por trás do firewall. |
| Stream Analytics do Azure         | Microsoft.StreamAnalytics             | Permite que os dados de um trabalho de streaming sejam gravados no Armazenamento de Blobs. [Saiba mais](../../stream-analytics/blob-output-managed-identity.md). |
| Azure Synapse Analytics        | Microsoft.Synapse/workspaces          | Habilita o acesso a dados no armazenamento do Azure do Azure Synapse Analytics. |


### <a name="storage-analytics-data-access"></a>Acesso a dados de análise de armazenamento

Em alguns casos, o acesso para ler as métricas e logs de recurso é necessário de fora do limite de rede. Ao configurar o acesso de serviços confiáveis à conta de armazenamento, você pode permitir o acesso de leitura aos arquivos de log, às tabelas de métricas ou a ambos. [Saiba mais sobre como trabalhar com a análise de armazenamento.](./storage-analytics.md)

### <a name="managing-exceptions"></a>Gerenciando exceções

Você pode gerenciar as exceções de regra da rede através do portal do Azure, PowerShell ou CLI do Azure v2.

#### <a name="azure-portal"></a>Portal do Azure

1. Vá até a conta de armazenamento que você deseja proteger.

1. Clique no menu configurações chamado **rede**.

1. Certifique-se de que você optou por permitir o acesso de **Redes selecionadas**.

1. Em **Exceções**, selecione as exceções que deseja conceder.

1. Clique em **Salvar** para aplicar suas alterações.

#### <a name="powershell"></a>PowerShell

1. Instalar o [Azure PowerShell](/powershell/azure/install-Az-ps) e [entrar](/powershell/azure/authenticate-azureps).

1. Exiba as exceções para as regras de rede da conta de armazenamento.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount").Bypass
    ```

1. Configure as exceções para as regras de rede da conta de armazenamento.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass AzureServices,Metrics,Logging
    ```

1. Remova as exceções para as regras de rede da conta de armazenamento.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass None
    ```

> [!IMPORTANT]
> Não se esqueça de [definir a regra padrão](#change-the-default-network-access-rule) para **negar** ou a remoção das exceções não terá efeito.

#### <a name="cliv2"></a>CLIv2

1. Instalar a [CLI do Azure](/cli/azure/install-azure-cli) e [entrar](/cli/azure/authenticate-azure-cli).

1. Exiba as exceções para as regras de rede da conta de armazenamento.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.bypass
    ```

1. Configure as exceções para as regras de rede da conta de armazenamento.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass Logging Metrics AzureServices
    ```

1. Remova as exceções para as regras de rede da conta de armazenamento.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass None
    ```

> [!IMPORTANT]
> Não se esqueça de [definir a regra padrão](#change-the-default-network-access-rule) para **negar** ou a remoção das exceções não terá efeito.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre os Pontos de Extremidade do Serviço de Rede do Azure em [Pontos de Extremidade de Serviço](../../virtual-network/virtual-network-service-endpoints-overview.md).

Aprofunde-se na segurança do Armazenamento do Microsoft Azure no [Guia de segurança do Armazenamento do Azure](../blobs/security-recommendations.md).
