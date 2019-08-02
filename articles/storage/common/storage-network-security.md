---
title: Configurar redes virtuais e firewalls do Armazenamento do Microsoft Azure | Microsoft Docs
description: Configure a segurança de rede em camadas para sua conta de armazenamento.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: cc0ba80f7aef53568e048b8285800982c818b004
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68334585"
---
# <a name="configure-azure-storage-firewalls-and-virtual-networks"></a>Configurar redes virtuais e firewalls do Armazenamento do Microsoft Azure

O Armazenamento do Microsoft Azure fornece um modelo de segurança em camadas. Esse modelo permite que você proteja suas contas de armazenamento a um conjunto específico de redes com suporte. Quando as regras de rede são configuradas, somente dados de solicitação de aplicativos do conjunto especificado de redes podem acessar uma conta de armazenamento.

Um aplicativo que acessa uma conta de armazenamento quando as regras de rede estão em vigor requer autorização adequada na solicitação. Há suporte para autorização com as credenciais do Azure Active Directory (Azure AD) para BLOBs e filas, com uma chave de acesso de conta válida ou com um token SAS.

> [!IMPORTANT]
> Ativar regras de firewall para sua conta de armazenamento bloqueia solicitações de entrada para os dados por padrão, a menos que as solicitações sejam provenientes de um serviço que está operando em uma Rede Virtual do Microsoft Azure (VNet). Solicitações que estão bloqueadas incluem as de outros serviços do Azure, do portal do Azure, de registro em log e serviços de métricas e assim por diante.
>
> Você pode conceder acesso aos serviços do Azure que operam de dentro de uma rede virtual, permitindo a sub-rede da instância do serviço. Um número limitado de cenários pode ser habilitado por meio do mecanismo [Exceções](#exceptions) descrito na seção abaixo. Para acessar o portal do Azure, é necessário fazer isso partir de um computador dentro do limite confiável (IP ou VNet) que você configurou.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="scenarios"></a>Cenários

Configure as contas de armazenamento para negar acesso ao tráfego de todas as redes (incluindo o tráfego da Internet) por padrão. Em seguida, conceda acesso ao tráfego de VNets específicas. Essa configuração permite que você crie um limite de rede seguro para seus aplicativos. Você também pode acessar os intervalos de endereço IP de Internet pública, permitindo conexões de clientes locais ou específicos da Internet.

As regras de rede são aplicadas em todos os protocolos de rede para o armazenamento do Azure, incluindo REST e SMB. Para acessar os dados com ferramentas como o portal do Azure, o Gerenciador de Armazenamento e o AZCopy, são necessárias regras de rede explícitas.

Você pode aplicar as regras de rede para contas de armazenamento existentes ou ao criar novas contas de armazenamento.

Depois que as regras de rede são aplicadas, elas são impostas para todas as solicitações. Os tokens SAS que concedem acesso a um serviço de endereço IP específico limitam o acesso do proprietário do token, mas não concedem um novo acesso além das regras de rede configuradas.

O tráfego de disco da máquina virtual (incluindo as operações de montagem e desmontagem e E/S de disco) não é afetado pelas regras de rede. O acesso REST a blobs de página é protegido pelas regras de rede.

As contas de armazenamento clássicas não dão suporte a firewalls e redes virtuais.

Você pode usar discos não gerenciados nas contas de armazenamento com as regras de rede aplicadas às VMs de backup e restauração com a criação de uma exceção. Esse processo está documentado na seção [Exceções](#exceptions) deste artigo. Exceções de firewall não são aplicáveis com discos gerenciados, pois eles já são gerenciados pelo Azure.

## <a name="change-the-default-network-access-rule"></a>Alterar a regra de acesso de rede padrão

Por padrão, as contas de armazenamento aceitam conexões de clientes em qualquer rede. Para limitar o acesso a redes selecionadas, primeiro você deve alterar a ação padrão.

> [!WARNING]
> Fazer alterações em regras de rede pode afetar a capacidade de seus aplicativos se conectarem ao Armazenamento do Azure. Definir a regra de rede padrão como **negar** bloqueia todo o acesso aos dados, a menos que regras específicas de rede para **conceder** acesso também sejam aplicadas. Certifique-se de conceder acesso a qualquer uma das redes permitidas usando regras de rede antes de alterar a regra padrão para negar o acesso.

### <a name="managing-default-network-access-rules"></a>Como alterar as regras de acesso de rede padrão

Você pode gerar as regras de acesso à rede padrão para contas de armazenamento através do portal do Azure, PowerShell ou CLIv2.

#### <a name="azure-portal"></a>Portal do Azure

1. Vá até a conta de armazenamento que você deseja proteger.

1. Clique no menu de configurações chamado **Firewalls e redes virtuais**.

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

Você pode configurar as contas de armazenamento para permitir o acesso somente de VNets específicas.

Habilitar um [Ponto de extremidade de serviço](/azure/virtual-network/virtual-network-service-endpoints-overview) do Armazenamento do Microsoft Azure dentro da VNet. Esse ponto de extremidade fornece o tráfego de uma rota ideal para o serviço de Armazenamento do Microsoft Azure. As identidades de rede virtual e a sub-rede também são transmitidas com cada solicitação. Em seguida, os administradores podem configurar as regras de rede para a conta de armazenamento que as solicitações sejam recebidas de sub-redes específicas na VNet. Os clientes com o acesso concedido por meio dessas regras de rede devem continuar a atender aos requisitos de autorização da conta de armazenamento para acessar os dados.

Cada conta de armazenamento pode dar suporte a até 100 regras da rede virtual que podem ser combinadas com [regras de rede IP](#grant-access-from-an-internet-ip-range).

### <a name="available-virtual-network-regions"></a>Regiões de rede virtual disponíveis

Em geral, os pontos de extremidade de serviço funcionam entre redes virtuais e instâncias de serviço na mesma região do Azure. Ao usar pontos de extremidade de serviço com o Armazenamento do Microsoft Azure, este escopo cresce para incluir a [região emparelhada](/azure/best-practices-availability-paired-regions). Pontos de extremidade de serviço permitem continuidade durante um failover regional e acesso a instâncias de armazenamento com redundância geográfica somente leitura (RA-GRS). As regras de rede que concedem acesso de uma rede virtual para uma conta de armazenamento também concedem acesso a qualquer instância de RA-GRS.

Ao planejar a recuperação de desastre durante uma interrupção regional, você deve criar as VNets na região emparelhada com antecedência. Habilite pontos de extremidade de serviço para o Armazenamento do Microsoft Azure, com as regras de rede concedendo acesso dessas redes virtuais alternativas. Em seguida, aplica essas regras às contas de armazenamento com redundância geográfica.

> [!NOTE]
> Os Pontos de Extremidade de Serviço não se aplicam ao tráfego de fora da região da rede virtual e ao par da região designada. Você pode aplicar as regras de rede concedendo acesso das redes virtuais para as conta de armazenamento na região primária de uma conta de armazenamento ou na região emparelhada designada.

### <a name="required-permissions"></a>Permissões necessárias

Para aplicar uma regra da rede virtual a uma conta de armazenamento, o usuário deve ter permissão para as sub-redes sendo adicionadas. A permissão necessária é *Ingressar o Serviço em uma Sub-rede* e está incluída na função interna *Colaborador da conta de armazenamento*. Também podem ser adicionado às definições de função personalizada.

A conta de armazenamento e o acesso concedido às redes virtuais podem estar em assinaturas diferentes, mas as assinaturas devem fazer parte do mesmo locatário do Azure AD.

### <a name="managing-virtual-network-rules"></a>Gerenciando regras da rede virtual

Você pode gerenciar as regras da rede virtual para contas de armazenamento através do portal do Azure, PowerShell ou CIv2.

#### <a name="azure-portal"></a>Portal do Azure

1. Vá até a conta de armazenamento que você deseja proteger.

1. Clique no menu de configurações chamado **Firewalls e redes virtuais**.

1. Certifique-se de que você optou por permitir o acesso de **Redes selecionadas**.

1. Para conceder acesso a uma rede virtual com uma nova regra de rede, em **Redes virtuais**, clique em **Adicionar rede virtual existente**, selecione **Redes virtuais** e as opções **Sub-redes** e clique **Adicionar**. Para criar uma nova rede virtual e conceder acesso, clique em **Adicionar nova rede virtual**. Forneça as informações necessárias para criar a nova rede virtual e, em seguida, clique em **Criar**.

    > [!NOTE]
    > Se um ponto de extremidade de Armazenamento do Microsoft Azure não foi configurado anteriormente para a rede virtual selecionada e as sub-redes, você pode configurá-lo como parte dessa operação.

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
    $subnetid=(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

1. Remova uma regra de rede para uma rede virtual e uma sub-rede.

    ```azurecli
    $subnetid=(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
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

Só há suporte para endereços IPV4 no momento.

Cada conta de armazenamento pode dar suporte a até 100 regras da rede virtual de IP que podem ser combinadas com as [regras da rede virtual](#grant-access-from-a-virtual-network).

### <a name="configuring-access-from-on-premises-networks"></a>Configurando o acesso de redes locais

Para conceder acesso de suas redes locais para sua conta de armazenamento com uma regra de rede IP, você deve identificar os endereços IP voltados para Internet usados por sua rede. Entre em contato com o administrador de rede para obter ajuda.

Se você estiver usando [ExpressRoute](/azure/expressroute/expressroute-introduction) de suas instalações, para emparelhamento público ou emparelhamento da Microsoft, será necessário identificar os endereços IP NAT usados. Para emparelhamento público, cada circuito do ExpressRoute usará dois endereços IP de NAT, que serão aplicados ao tráfego do serviço do Azure quando o tráfego entrar no backbone da rede do Microsoft Azure. Para emparelhamento da Microsoft, os endereços IP de NAT usados são fornecidos pelo cliente ou são fornecidos pelo provedor de serviço. Para permitir o acesso aos recursos do serviço, você deve permitir estes endereços IP públicos na configuração do firewall de IP do recurso. Para localizar os endereços IP do circuito do ExpressRoute de emparelhamento público, [abra um tíquete de suporte com o ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) por meio do Portal do Azure. Saiba mais sobre [NAT para emparelhamento público de ExpressRoute e emparelhamento da Microsoft.](/azure/expressroute/expressroute-nat#nat-requirements-for-azure-public-peering)

### <a name="managing-ip-network-rules"></a>Gerenciando regras de rede IP

Você pode gerenciar as regras de rede IP para contas de armazenamento através do portal do Azure, PowerShell ou CIv2.

#### <a name="azure-portal"></a>Portal do Azure

1. Vá até a conta de armazenamento que você deseja proteger.

1. Clique no menu de configurações chamado **Firewalls e redes virtuais**.

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

As regras de rede podem habilitar uma configuração de rede segura para a maioria dos cenários. No entanto, há alguns casos em que as exceções devem ser concedidas para habilitar a funcionalidade completa. Você pode configurar as contas de armazenamento com exceções para serviços Microsoft confiáveis e para o acesso aos dados de análise de armazenamento.

### <a name="trusted-microsoft-services"></a>Serviços Microsoft confiáveis

Alguns serviços da Microsoft que interagem com contas de armazenamento operam a partir de redes que não podem ter o acesso concedido por meio de regras de rede.

Para ajudar esse tipo de serviço a funcionar como esperado, você pode permitir que o conjunto de serviços Microsoft confiáveis ignore as regras de rede. Esses serviços usarão então a autenticação forte para acessar a conta de armazenamento.

Se você habilitar a exceção **Permitir serviços Microsoft confiáveis...** , os serviços a seguir (quando registrados em sua assinatura), são concedidos acessos para a conta de armazenamento:

|Serviço|Nome do provedor de recursos|Finalidade|
|:------|:---------------------|:------|
|Backup do Azure|Microsoft.RecoveryServices|Execute backups e restaurações de discos não gerenciados em máquinas virtuais IAAS. (não é necessário para discos gerenciados). [Saiba mais](/azure/backup/backup-introduction-to-azure-backup).|
|Azure Data Box|Microsoft.DataBox|Permite a importação de dados para o Azure usando Data Box. [Saiba mais](/azure/databox/data-box-overview).|
|Azure DevTest Labs|Microsoft.DevTestLab|Criação de imagem personalizada e instalação de artefato. [Saiba mais](/azure/devtest-lab/devtest-lab-overview).|
|Grade de Eventos do Azure|Microsoft.EventGrid|Habilite a publicação de eventos do Armazenamento de Blobs e permita que a Grade de Eventos publique em filas de armazenamento. Saiba mais sobre [eventos de Armazenamento de Blobs](/azure/event-grid/event-sources) e [publicação em filas](/azure/event-grid/event-handlers).|
|Hubs de Eventos do Azure|Microsoft.EventHub|Arquivar dados com a Captura de Hubs de Evento. [Saiba mais](/azure/event-hubs/event-hubs-capture-overview).|
| Sincronização de Arquivos do Azure| Microsoft.StorageSync| Permite transformar seu servidor de arquivos local em um cache para compartilhamentos de arquivos do Azure. Permitir a sincronização de vários sites, recuperação rápida de desastres e backup no lado da nuvem. [Saiba mais](../files/storage-sync-files-planning.md)|
|Azure HDInsight|Microsoft.HDInsight|Provisione o conteúdo inicial do sistema de arquivos padrão para um novo cluster HDInsight. [Saiba mais](https://azure.microsoft.com/blog/enhance-hdinsight-security-with-service-endpoints/).|
|Azure Monitor|Microsoft.Insights|Permite a gravação de dados de monitoramento para uma conta de armazenamento protegida [Saiba mais](/azure/monitoring-and-diagnostics/monitoring-roles-permissions-security).|
|Rede do Azure|Microsoft.Network|Armazenar e analisar os logs de tráfego de rede. [Saiba mais](/azure/network-watcher/network-watcher-packet-capture-overview).|
|do Azure Site Recovery|Microsoft.SiteRecovery |Configure a recuperação de desastre, habilitando a replicação para máquinas virtuais IaaS do Azure. Isso é necessário se você estiver usando a conta de armazenamento de cache de firewall habilitado ou a conta de armazenamento de origem ou a conta de armazenamento de destino.  [Saiba mais](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication).|
|SQL Data Warehouse do Azure|Microsoft.Sql|Permite cenários de importação e exportação de instâncias específicas de bancos de dados SQL usando o polybase. [Saiba mais](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview).|

### <a name="storage-analytics-data-access"></a>Acesso a dados de análise de armazenamento

Em alguns casos, o acesso para ler as métricas e logs de diagnóstico é necessário de fora do limite de rede. Você pode conceder exceções às regras de rede para permitir o acesso de leitura para os arquivos de log da conta de armazenamento, tabelas de métricas ou ambos. [Saiba mais sobre como trabalhar com a análise de armazenamento.](/azure/storage/storage-analytics)

### <a name="managing-exceptions"></a>Gerenciando exceções

Você pode gerenciar as exceções de regra da rede através do portal do Azure, PowerShell ou CLI do Azure v2.

#### <a name="azure-portal"></a>Portal do Azure

1. Vá até a conta de armazenamento que você deseja proteger.

1. Clique no menu de configurações chamado **Firewalls e redes virtuais**.

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

Saiba mais sobre os Pontos de Extremidade do Serviço de Rede do Azure em [Pontos de Extremidade de Serviço](/azure/virtual-network/virtual-network-service-endpoints-overview).

Aprofunde-se na segurança do Armazenamento do Microsoft Azure no [Guia de segurança do Armazenamento do Azure](storage-security-guide.md).
