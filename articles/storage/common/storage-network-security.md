---
title: "Configurar redes virtuais e firewalls de armazenamento do Azure (versão prévia) | Microsoft Docs"
description: "Configure a segurança de rede em camadas para sua conta de armazenamento."
services: storage
documentationcenter: 
author: cbrooksmsft
manager: cbrooks
editor: cbrooks
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 09/25/2017
ms.author: cbrooks
ms.openlocfilehash: de52e9cb32e28d2f40a56743ed759b5d5d0a63f0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="configure-azure-storage-firewalls-and-virtual-networks-preview"></a>Configurar redes virtuais e firewalls de armazenamento do Azure (versão prévia)
O Armazenamento do Azure fornece um modelo de segurança em camadas, permitindo que você proteja suas contas de armazenamento para um conjunto específico de redes permitidas.  Quando as regras de rede são configuradas, somente aplicativos das redes permitidas podem acessar uma conta de armazenamento.  Ao chamar de uma rede permitida, os aplicativos continuam a exigir a autorização apropriada (uma chave de acesso ou token SAS válido) para acessar a conta de armazenamento.

## <a name="preview-availability-and-support"></a>Disponibilidade e suporte da versão prévia
As redes virtuais e os firewalls de armazenamento estão na versão prévia.  Essa capacidade está atualmente disponível para contas de armazenamento novas ou existentes nas seguintes regiões:
- Leste dos EUA
- Oeste dos EUA
- Oeste dos EUA 2
- Centro-Oeste dos EUA
- Leste da Austrália
- Sudeste da Austrália

> [!NOTE]
> Não há suporte para cargas de trabalho de produção durante a versão prévia.
>

## <a name="scenarios"></a>Cenários
As contas de armazenamento podem ser configuradas para negar acesso ao tráfego de todas as redes (incluindo o tráfego da Internet) por padrão.  O acesso pode ser concedido ao tráfego de redes virtuais do Azure específicas, permitindo que você crie um limite de rede segura para seus aplicativos.  O acesso também pode ser concedido a intervalos de endereço IP de Internet pública, permitindo conexões de clientes locais ou da Internet específicos.

As regras de rede são aplicadas em todos os protocolos de rede para o armazenamento do Azure, incluindo REST e SMB.  O acesso aos seus dados de ferramentas como o Portal do Azure, o Gerenciador de Armazenamento e o AZCopy exigem regras de rede explícitas concedendo acesso quando as regras de rede estão em vigor.

As regras de rede podem ser aplicadas a contas de armazenamento existentes ou durante a criação de novas contas de armazenamento.

Depois que as regras de rede são aplicadas, elas são impostas para todas as solicitações.  Os tokens SAS que concedem acesso a um serviço de endereço IP específico servem para **limitar** o acesso do proprietário do token, mas eles não concedem um novo acesso além das regras de rede configuradas. 

O tráfego de disco da máquina virtual (incluindo as operações de montagem e desmontagem e E/S de disco) **não** é afetado pelas regras de rede.  O backup de discos não gerenciados não tem suporte para contas de armazenamento protegidas durante a versão prévia.  O acesso REST a blobs de página (usados para discos de máquina virtual) é protegido pelas regras de rede.

As contas de armazenamento clássicas **não** dão suporte a firewalls e redes virtuais.

## <a name="change-the-default-network-access-rule"></a>Alterar a regra de acesso de rede padrão
Por padrão, as contas de armazenamento aceitam conexões de clientes em qualquer rede.  Para limitar o acesso a redes selecionadas, primeiro você deve alterar a ação padrão.

> [!WARNING]
> Fazer alterações em regras de rede pode afetar a capacidade de seus aplicativos se conectarem ao Armazenamento do Azure.  Definir a regra de rede padrão como **negar** bloqueia todo o acesso aos dados, a menos que regras específicas de rede *concedendo* acesso também sejam aplicadas.  Certifique-se de conceder acesso a qualquer uma das redes permitidas usando regras de rede antes de alterar a regra padrão para negar o acesso.
>

#### <a name="azure-portal"></a>Portal do Azure
1. Navegue até a conta de armazenamento que você deseja proteger.  
> [!NOTE]
> Certifique-se de que sua conta de armazenamento esteja em uma das regiões com suporte para a visualização pública.
>

2. Clique no menu de configurações chamado **Firewalls e redes virtuais**.
3. Para negar o acesso por padrão, opte por permitir o acesso de "Redes selecionadas".  Para permitir o tráfego de todas as redes, opte por permitir o acesso de “Todas as redes”.
4. Clique em *Salvar* para aplicar suas alterações.

#### <a name="powershell"></a>PowerShell
1. Instale o [Azure PowerShell](/powershell/azure/install-azurerm-ps) mais recente e [faça logon](/powershell/azure/authenticate-azureps).

2. Exiba o status da regra padrão para a conta de armazenamento.
```PowerShell
(Get-AzureRmStorageAccountNetworkRuleSet  -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").DefaultAction
``` 

3. Defina a regra padrão para negar o acesso à rede por padrão.  
```PowerShell
Update-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Deny
```    

4. Defina a regra padrão para permitir o acesso à rede por padrão.
```PowerShell
Update-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Allow
```    

#### <a name="cliv2"></a>CLIv2
1. [Instale a CLI do Azure 2.0](/cli/azure/install-azure-cli) e [faça logon](/cli/azure/authenticate-azure-cli).
2. Exiba o status da regra padrão para a conta de armazenamento.
```azurecli
az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkAcls.defaultAction
```

3. Defina a regra padrão para negar o acesso à rede por padrão.  
```azurecli
az storage account update --name "mystorageaccount" --resource-group "myresourcegroup" --default-action Deny
```

4. Defina a regra padrão para permitir o acesso à rede por padrão.
```azurecli
az storage account update --name "mystorageaccount" --resource-group "myresourcegroup" --default-action Allow
```

## <a name="grant-access-from-a-virtual-network"></a>Conceder acesso de uma rede virtual
Contas de armazenamento podem ser configuradas para permitir o acesso somente de redes virtuais do Azure específicas. 

Ao habilitar um [Ponto de Extremidade de Serviço](/azure/virtual-network/virtual-network-service-endpoints-overview) para o Armazenamento do Azure dentro da rede virtual, é garantida uma rota ideal para o tráfego para o serviço do Armazenamento do Azure. As identidades de rede virtual e a sub-rede também são transmitidas com cada solicitação.  Os administradores podem configurar subsequentemente regras de rede para a conta de armazenamento que permitem que solicitações sejam recebidas de determinadas sub-redes na rede virtual.  Os clientes com o acesso concedido por meio dessas regras de rede devem continuar a atender aos requisitos de autorização da conta de armazenamento para acessar os dados.

Cada conta de armazenamento pode dar suporte a até 100 regras de rede virtual que podem ser combinadas com [regras de rede IP](#grant-access-from-an-internet-ip-range).

### <a name="available-virtual-network-regions"></a>Regiões de rede virtual disponíveis
Em geral, os Pontos de Extremidade de Serviço funcionam entre redes virtuais e instâncias de serviço na mesma região do Azure.  Quando os Pontos de Extremidade de Serviço são usados com o Armazenamento do Azure, esse escopo é expandido para incluir a [região emparelhada](/azure/best-practices-availability-paired-regions).  Isso permite a continuidade durante um failover regional, bem como o acesso ininterrupto a instâncias de RA-GRS (armazenamento com redundância geográfica somente leitura).  As regras de rede que concedem acesso de uma rede virtual para uma conta de armazenamento também concedem acesso a qualquer instância de RA-GRS.

Ao planejar a recuperação de desastre durante uma interrupção regional, você deve provisionar as redes virtuais na região emparelhada com antecedência. Os Pontos de Extremidade de Serviço para Armazenamento do Azure devem ser habilitados e as regras de rede que concedem acesso dessas redes virtuais alternativas devem ser aplicadas às suas contas de armazenamento com redundância geográfica.

> [!NOTE]
> Os Pontos de Extremidade de Serviço não se aplicam ao tráfego de fora da região da rede virtual e ao par de região designado.  As regras de rede concedendo acesso de redes virtuais para contas de armazenamento podem ser aplicadas somente para redes virtuais na região primária de uma conta de armazenamento ou na região emparelhada designada.
>

### <a name="required-permissions"></a>Permissões necessárias
Para aplicar uma regra de rede virtual a uma conta de armazenamento, o usuário deve ter permissão para *Ingressar o Serviço em uma Sub-rede* para as sub-redes sendo adicionadas.  Essa permissão é incluída na função interna de *Colaborador da Conta de Armazenamento* e pode ser adicionada às definições de função personalizada.

A conta de armazenamento e o acesso concedido às redes virtuais **podem** estar em assinaturas diferentes, mas as assinaturas devem fazer parte do mesmo locatário do Azure Active Directory.

### <a name="managing-virtual-network-rules"></a>Gerenciando regras de rede virtual
As regras de rede virtual para contas de armazenamento podem ser gerenciadas por meio do Portal do Azure, do PowerShell ou do CLIv2.

#### <a name="azure-portal"></a>Portal do Azure
1. Navegue até a conta de armazenamento que você deseja proteger.  
2. Clique no menu de configurações chamado **Firewalls e redes virtuais**.
3. Certifique-se de que você optou por permitir o acesso de “Redes selecionadas”.
4. Para conceder acesso a uma rede virtual com uma nova regra de rede, em "Redes virtuais", clique em "Adicionar existentes" para selecionar uma rede virtual e sub-redes existentes, em seguida, clique em *Adicionar*.  Para criar uma nova rede virtual e conceder acesso a ela clique em *Adicionar nova*, forneça as informações necessárias para criar a nova rede virtual e, em seguida, clique em *Criar*.

> [!NOTE]
> Se um ponto de extremidade de serviço para Armazenamento do Azure não foi configurado anteriormente para a rede virtual e as sub-redes selecionadas, ele pode ser configurado como parte dessa operação.
>

5. Para remover uma regra de rede virtual ou sub-rede, clique em "..." para abrir o menu de contexto da rede virtual ou da sub-rede e clique em "Remover".
6. Clique em *Salvar* para aplicar suas alterações.

#### <a name="powershell"></a>PowerShell
1. Instale o [Azure PowerShell](/powershell/azure/install-azurerm-ps) mais recente e [faça logon](/powershell/azure/authenticate-azureps).
2. Liste as regras de rede virtual
```PowerShell
(Get-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").VirtualNetworkRules
```

3. Habilite o ponto de extremidade de serviço para o Armazenamento do Azure em uma rede virtual e uma sub-rede existentes
```PowerShell
Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet"  -AddressPrefix "10.1.1.0/24" -ServiceEndpoint "Microsoft.Storage" | Set-AzureRmVirtualNetwork
```

4. Adicione uma regra de rede para uma rede virtual e uma sub-rede.  
```PowerShell
$subnet = Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet"
Add-AzureRmStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id   
```    

5. Remova uma regra de rede para uma rede virtual e uma sub-rede.  
```PowerShell
$subnet = Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet"
Remove-AzureRmStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id   
```    

> [!IMPORTANT]
> Não se esqueça de [definir a regra padrão](#change-the-default-network-access-rule) para Negar ou as regras de rede não terão efeito.
>

#### <a name="cliv2"></a>CLIv2
1. [Instale a CLI do Azure 2.0](/cli/azure/install-azure-cli) e [faça logon](/cli/azure/authenticate-azure-cli).
2. Liste as regras de rede virtual
```azurecli
az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query virtualNetworkRules
```

2. Habilite o ponto de extremidade de serviço para o Armazenamento do Azure em uma rede virtual e uma sub-rede existentes
```azurecli
az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.Storage"
```

3. Adicione uma regra de rede para uma rede virtual e uma sub-rede.  
```azurecli
subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "TestVNET" --name "default" --query id --output tsv)
az storage account network-rule add --resource-group myresourcegroup --account-name mystorageaccount --subnet $subnetid
```

4. Remova uma regra de rede para uma rede virtual e uma sub-rede. 
```azurecli
subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "TestVNET" --name "default" --query id --output tsv)
az storage account network-rule remove --resource-group myresourcegroup --account-name mystorageaccount --subnet $subnetid
```

> [!IMPORTANT]
> Não se esqueça de [definir a regra padrão](#change-the-default-network-access-rule) para Negar ou as regras de rede não terão efeito.
>

## <a name="grant-access-from-an-internet-ip-range"></a>Conceder acesso de um intervalo de IP de Internet
As contas de armazenamento podem ser configuradas para permitir acesso pelos intervalos de endereço IP de Internet pública específicos.  Essa configuração permite que serviços específicos baseados na Internet e redes locais tenham o acesso concedido enquanto o tráfego de Internet geral é bloqueado.

Os intervalos de endereço de Internet permitidos podem ser fornecidos usando a [Notação CIDR](https://tools.ietf.org/html/rfc4632) no formato *16.17.18.0/24* ou como endereços IP individuais, como *16.17.18.19*.

> [!NOTE]
> Os intervalos de endereços pequenos usando o prefixo "/31" ou "/32" não têm suporte.  Esses intervalos devem ser configurados usando regras de endereço IP individuais.
>

As regras de rede de IP são permitidas apenas para endereços IP de **Internet pública**.  Os intervalos de endereços IP reservados para redes privadas (conforme definido na RFC 1918) não são permitidos em regras de IP.  As redes privadas incluem endereços que começam com *10.\**, *172.16.\** e *192.168.\**

Só há suporte para endereços IPV4 no momento.

Cada conta de armazenamento pode dar suporte a até 100 regras de rede IP que podem ser combinadas com [regras de rede virtual](#grant-access-from-a-virtual-network)

### <a name="configuring-access-from-on-premises-networks"></a>Configurando o acesso de redes locais
Para conceder acesso de suas redes locais para sua conta de armazenamento com uma regra de rede IP, você deve identificar os endereços IP voltados para Internet usados por sua rede.  Entre em contato com o administrador de rede para obter ajuda.

Se sua rede estiver conectada à rede do Azure usando [ExpressRoute](/azure/expressroute/expressroute-introduction), cada circuito será configurado com dois endereços IP públicos no Microsoft Edge que são usados para se conectar aos Serviços Microsoft como o Armazenamento do Azure usando o [Emparelhamento Público do Azure](/azure/expressroute/expressroute-circuit-peerings#expressroute-routing-domains).  Para permitir a comunicação do seu circuito para o Armazenamento do Azure, você deve criar regras de rede IP para os endereços IP públicos dos seus circuitos.  Para localizar os endereços IP públicos do seu circuito do ExpressRoute, [abra um tíquete de suporte com o ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) por meio do Portal do Azure.


### <a name="managing-ip-network-rules"></a>Gerenciando regras de rede IP
As regras de rede IP para contas de armazenamento podem ser gerenciadas por meio do Portal do Azure, do PowerShell ou do CLIv2.

#### <a name="azure-portal"></a>Portal do Azure
1. Navegue até a conta de armazenamento que você deseja proteger.  
2. Clique no menu de configurações chamado **Firewalls e redes virtuais**.
3. Certifique-se de que você optou por permitir o acesso de “Redes selecionadas”.
4. Para conceder acesso a um intervalo de IP de Internet, insira o endereço IP ou o intervalo de endereços (no formato CIDR) em Firewall, Intervalos de Endereços.
5. Para remover uma regra de rede IP, clique em "..." para abrir o menu de contexto para a regra e clique em "Remover".
6. Clique em *Salvar* para aplicar suas alterações.

#### <a name="powershell"></a>PowerShell
1. Instale o [Azure PowerShell](/powershell/azure/install-azurerm-ps) mais recente e [faça logon](/powershell/azure/authenticate-azureps).
2. Liste as regras de rede IP.
```PowerShell
(Get-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").IPRules
```

3. Adicione uma regra de rede para um endereço IP individual.  
```PowerShell
Add-AzureRMStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19" 
``` 

4. Adicione uma regra de rede para um intervalo de endereços IP.  
```PowerShell
Add-AzureRMStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24" 
```    

5. Remova uma regra de rede para um endereço IP individual. 
```PowerShell
Remove-AzureRMStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"  
```

6. Remova uma regra de rede para um intervalo de endereços IP.  
```PowerShell
Remove-AzureRMStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"  
```    

> [!IMPORTANT]
> Não se esqueça de [definir a regra padrão](#change-the-default-network-access-rule) para Negar ou as regras de rede não terão efeito.
>

#### <a name="cliv2"></a>CLIv2
1. [Instale a CLI do Azure 2.0](/cli/azure/install-azure-cli) e [faça logon](/cli/azure/authenticate-azure-cli).
2. Liste as regras de rede IP
```azurecli
az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query ipRules
```

3. Adicione uma regra de rede para um endereço IP individual.
```azurecli
az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
```

4. Adicione uma regra de rede para um intervalo de endereços IP.  
```azurecli
az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
```

5. Remova uma regra de rede para um endereço IP individual.  
```azurecli
az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
```

6. Remova uma regra de rede para um intervalo de endereços IP.  
```azurecli
az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
```

> [!IMPORTANT]
> Não se esqueça de [definir a regra padrão](#change-the-default-network-access-rule) para Negar ou as regras de rede não terão efeito.
>

## <a name="exceptions"></a>Exceções
Embora as regras de rede possam habilitar uma configuração de rede segura para a maioria dos cenários, há alguns casos em que as exceções devem ser concedidas para habilitar a funcionalidade completa.  As contas de armazenamento podem ser configuradas com exceções para Serviços Microsoft Confiáveis e para o acesso aos dados de análise de armazenamento.

### <a name="trusted-microsoft-services"></a>Serviços Microsoft Confiáveis
Alguns serviços da Microsoft que interagem com contas de armazenamento operam de redes que não podem ter o acesso concedido por meio de regras de rede. 

Para permitir que esse tipo de serviço funcione como esperado, você pode permitir que o conjunto de serviços Microsoft confiáveis ignore as regras de rede. Esses serviços usarão então a autenticação forte para acessar a conta de armazenamento.

Quando a exceção "Serviços Microsoft Confiáveis" estiver habilitada, os seguintes serviços (quando registrados em sua assinatura) terão o acesso à conta de armazenamento concedido:

|O Barramento de|Nome do provedor de recursos|Finalidade|
|:------|:---------------------|:------|
|Azure DevTest Labs|Microsoft.DevTestLab|Criação de imagem personalizada e instalação de artefato.  [Saiba mais](https://docs.microsoft.com/en-us/azure/devtest-lab/devtest-lab-overview).|
|Grade de Eventos do Azure|Microsoft.EventGrid|Habilitar a publicação de eventos do Armazenamento de Blobs.  [Saiba mais](https://docs.microsoft.com/en-us/azure/event-grid/overview).|
|Hubs de eventos do Azure|Microsoft.EventHub|Arquivar dados com a Captura de Hubs de Evento.  [Saiba mais](https://docs.microsoft.com/en-us/azure/event-hubs/event-hubs-capture-overview).|
|Azure HDInsight|Microsoft.HDInsight|Provisionamento e instalação de cluster.  [Saiba mais](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-use-blob-storage).|
|Rede do Azure|Microsoft.Networking|Armazenar e analisar os logs de tráfego de rede.  [Saiba mais](https://docs.microsoft.com/en-us/azure/network-watcher/network-watcher-packet-capture-overview).|
|SQL Data Warehouse do Azure|Microsoft.Sql|Importação e exportação de dados.  [Saiba mais](https://docs.microsoft.com/en-us/azure/sql-data-warehouse/sql-data-warehouse-overview-load#load-from-azure-blob-storage).|
||||

### <a name="storage-analytics-data-access"></a>Acesso a dados de análise de armazenamento
Em alguns casos, o acesso para ler as métricas e logs de diagnóstico é necessário de fora do limite de rede.  Exceções às regras de rede podem ser concedidas para permitir o acesso de leitura para os arquivos de log da conta de armazenamento, as tabelas de métricas ou ambos. [Saiba mais sobre como trabalhar com a análise de armazenamento.](/azure/storage/storage-analytics)

### <a name="managing-exceptions"></a>Gerenciando exceções
As exceções da regra de rede podem ser gerenciadas por meio do Portal do Azure, do PowerShell ou da CLI do Azure v2.

#### <a name="azure-portal"></a>Portal do Azure
1. Navegue até a conta de armazenamento que você deseja proteger.  
2. Clique no menu de configurações chamado **Firewalls e redes virtuais**.
3. Certifique-se de que você optou por permitir o acesso de “Redes selecionadas”.
4. Em exceções, selecione as exceções que deseja conceder.
5. Clique em *Salvar* para aplicar suas alterações.

#### <a name="powershell"></a>PowerShell
1. Instale o [Azure PowerShell](/powershell/azure/install-azurerm-ps) mais recente e [faça logon](/powershell/azure/authenticate-azureps).
2. Exiba as exceções para as regras de rede da conta de armazenamento.
```PowerShell
(Get-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount").Bypass
```

3. Configure as exceções para as regras de rede da conta de armazenamento.
```PowerShell
Update-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount"  -Bypass AzureServices,Metrics,Logging
```

4. Remova as exceções para as regras de rede da conta de armazenamento.
```PowerShell
Update-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount"  -Bypass None
```

> [!IMPORTANT]
> Não se esqueça de [definir a regra padrão](#change-the-default-network-access-rule) para Negar ou a remoção das exceções não terá efeito.
>

#### <a name="cliv2"></a>CLIv2
1. [Instale a CLI do Azure 2.0](/cli/azure/install-azure-cli) e [faça logon](/cli/azure/authenticate-azure-cli).
2. Exiba as exceções para as regras de rede da conta de armazenamento.
```azurecli
az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkAcls.bypass
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
> Não se esqueça de [definir a regra padrão](#change-the-default-network-access-rule) para Negar ou a remoção das exceções não terá efeito.
>

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre os Pontos de Extremidade do Serviço de Rede do Azure em [Pontos de Extremidade de Serviço](/azure/virtual-network/virtual-network-service-endpoints-overview).

Aprofunde-se na segurança do Armazenamento do Azure no [Guia de segurança do Armazenamento do Azure](storage-security-guide.md).
