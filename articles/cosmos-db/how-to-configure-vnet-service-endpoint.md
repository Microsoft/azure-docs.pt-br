---
title: Configurar o acesso baseado em rede virtual para uma conta do Azure Cosmos
description: Este documento descreve as etapas necessárias para configurar um ponto de extremidade de serviço de rede virtual para o Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/13/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1d63d21f4c49e3c7aef035208477ac9fc79f2e51
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94637176"
---
# <a name="configure-access-to-azure-cosmos-db-from-virtual-networks-vnet"></a>Configurar o acesso a Azure Cosmos DB de redes virtuais (VNet)
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Você pode configurar a conta do Azure Cosmos para permitir o acesso somente de uma sub-rede específica da rede virtual (VNet). Ao habilitar o [Service endpoint](../virtual-network/virtual-network-service-endpoints-overview.md) para acessar o Azure Cosmos DB na sub-rede em uma rede virtual, o tráfego dessa sub-rede é enviado ao Azure Cosmos DB com a identidade da sub-rede e da Virtual Network. Quando o ponto de extremidade do serviço do Azure Cosmos DB estiver habilitado, você poderá limitar o acesso à sub-rede adicionando-o à sua conta do Azure Cosmos.

Por padrão, uma conta do Azure Cosmos pode ser acessada de qualquer origem se a solicitação for acompanhada por um token de autorização válido. Quando você adiciona uma ou mais sub-redes dentro de VNets, somente solicitações originadas dessas sub-redes obterão uma resposta válida. Solicitações provenientes de qualquer outra fonte receberão uma resposta 403 (Proibido). 

Você pode configurar as contas de banco de dados do Microsoft Azure Cosmos DB para permitir o acesso apenas de uma sub-rede específica de uma rede virtual do Azure. Para limitar o acesso a uma conta do Azure Cosmos DB com conexões de uma sub-rede em uma rede virtual:

1. Habilite a sub-rede para enviar a identidade da sub-rede e da rede virtual para o Azure Cosmos DB. Você pode conseguir isso habilitando um ponto de extremidade de serviço para o Azure Cosmos DB na sub-rede específica.

1. Adicione uma regra na conta do Azure Cosmos DB para especificar a sub-rede como uma fonte da qual a conta pode ser acessada.

> [!NOTE]
> Quando um ponto de extremidade de serviço da sua conta do Azure Cosmos DB é habilitado em uma sub-rede, a origem do tráfego que chega ao Azure Cosmos DB muda de um IP público para uma rede virtual e uma sub-rede. A troca de tráfego se aplica a qualquer conta do Azure Cosmos DB acessada a partir dessa sub-rede. Se suas contas de banco de dados do Microsoft Azure Cosmos DB tiverem um firewall baseado em IP para permitir essa sub-rede, as solicitações da sub-rede habilitada para serviço não corresponderão mais às regras de firewall de IP e serão rejeitadas.
>
> Para saber mais, consulte as etapas descritas na seção [Migrando de uma regra de firewall de IP para uma lista de controle de acesso à rede virtual](#migrate-from-firewall-to-vnet) deste artigo.

As seções a seguir descrevem como configurar o ponto de extremidade do serviço de rede virtual para uma conta do Azure Cosmos DB.

## <a name="configure-a-service-endpoint-by-using-the-azure-portal"></a><a id="configure-using-portal"></a>Configurar um ponto de extremidade de serviço usando o portal do Azure

### <a name="configure-a-service-endpoint-for-an-existing-azure-virtual-network-and-subnet"></a>Configurar um ponto de extremidade de serviço para uma rede virtual e sub-rede do Azure existente

1. No blade **Todos os recursos**, localize a conta do Azure Cosmos DB que você deseja proteger.

1. Selecione **Firewalls e redes virtuais** no menu de configurações e opte por permitir o acesso a partir de **Redes selecionadas**.

1. Para conceder acesso à sub-rede de uma rede virtual existente, em **Redes virtuais**, selecione **Adicionar rede virtual existente do Azure**.

1. Selecione a **Assinatura** da qual você deseja adicionar uma rede virtual do Azure. Selecione as **Redes virtuais** do Azure e as **Sub-redes** que você deseja fornecer acesso à sua conta do Azure Cosmos DB. Em seguida, selecione **Habilitar** para habilitar as redes selecionadas com pontos de extremidade de serviço para "Microsoft.AzureCosmosDB". Quando estiver concluído, selecione **Adicionar**.

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet.png" alt-text="Selecionar rede virtual e sub-rede":::

1. Depois que a conta do Azure Cosmos DB estiver habilitada para acesso de uma rede virtual, ela permitirá o tráfego somente dessa sub-rede escolhida. A rede virtual e a sub-rede que você adicionou devem aparecer como mostrado na captura de tela a seguir:

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/vnet-and-subnet-configured-successfully.png" alt-text="Rede virtual e sub-rede configuradas com sucesso":::

> [!NOTE]
> Para habilitar pontos de extremidade de serviço de rede virtual, você precisaria das seguintes permissões de assinatura:
>   * Assinatura com rede virtual: colaborador da rede
>   * Assinatura com a conta do Azure Cosmos DB: contribuidor da conta do DocumentDB
>   * Se sua rede virtual e Azure Cosmos DB conta estiverem em assinaturas diferentes, certifique-se de que a assinatura que tem a rede virtual também tenha o `Microsoft.DocumentDB` provedor de recursos registrado. Para registrar um provedor de recursos, consulte o artigo [provedores e tipos de recursos do Azure](../azure-resource-manager/management/resource-providers-and-types.md) .

Aqui estão as instruções para registrar a assinatura com o provedor de recursos.

### <a name="configure-a-service-endpoint-for-a-new-azure-virtual-network-and-subnet"></a>Configurar o ponto de extremidade de serviço para uma rede virtual do Azure e uma sub-rede novas

1. No blade **Todos os recursos**, localize a conta do Azure Cosmos DB que você deseja proteger.  

1. Selecione **Firewalls e redes virtuais do Azure** no menu de configurações e escolha permitir o acesso a partir de **Redes selecionadas**.  

1. Para conceder acesso a uma nova rede virtual do Azure, em **Redes virtuais**, selecione **Adicionar nova rede virtual**.  

1. Forneça os detalhes necessários para criar uma nova rede virtual e, em seguida, selecione **Criar**. A sub-rede será criada com um ponto de extremidade de serviço para "Microsoft.AzureCosmosDB" habilitado.

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png" alt-text="Selecione uma rede virtual e uma sub-rede para uma nova rede virtual":::

Se sua conta de Azure Cosmos DB for usada por outros serviços do Azure, como o Pesquisa Cognitiva do Azure, ou for acessada do Stream Analytics ou Power BI, você permitirá o acesso selecionando **aceitar conexões de dentro de datacenters globais do Azure**.

Para garantir que você tenha acesso às métricas do Microsoft Azure Cosmos DB no portal, é necessário habilitar as opções **Permitir acesso do portal do Azure**. Para saber mais sobre essas opções, consulte o artigo [Configurar um firewall IP](how-to-configure-firewall.md). Depois de habilitar o acesso, selecione **salvar** para salvar as configurações.

## <a name="remove-a-virtual-network-or-subnet"></a><a id="remove-vnet-or-subnet"></a>Remover uma rede virtual ou sub-rede

1. No blade **Todos os recursos**, localize a conta do Microsoft Azure Cosmos DB para a qual você atribuiu pontos de extremidade de serviço.  

1. Para saber mais sobre essas opções, consulte o artigo **Configurar um firewall IP**.  

1. Para remover uma regra de rede virtual ou sub-rede, selecione **...** ao lado da rede virtual ou sub-rede e selecione **Remover**.

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/remove-a-vnet.png" alt-text="Remover uma rede virtual":::

1. Selecione **Salvar** para salvar suas alterações.

## <a name="configure-a-service-endpoint-by-using-azure-powershell"></a><a id="configure-using-powershell"></a>Configurar endpoint de serviço usando o Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Use as etapas a seguir para configurar um ponto de extremidade de serviço para uma conta de banco de dados do Microsoft Azure Cosmos DB usando o Azure PowerShell:  

1. Instale o [Azure PowerShell](/powershell/azure/install-Az-ps) e [faça login em](/powershell/azure/authenticate-azureps).  

1. Habilite o ponto de extremidade de serviço para uma sub-rede existente de uma rede virtual.  

   ```powershell
   $resourceGroupName = "<Resource group name>"
   $vnetName = "<Virtual network name>"
   $subnetName = "<Subnet name>"
   $subnetPrefix = "<Subnet address range>"
   $serviceEndpoint = "Microsoft.AzureCosmosDB"

   Get-AzVirtualNetwork `
      -ResourceGroupName $resourceGroupName `
      -Name $vnetName | Set-AzVirtualNetworkSubnetConfig `
      -Name $subnetName `
      -AddressPrefix $subnetPrefix `
      -ServiceEndpoint $serviceEndpoint | Set-AzVirtualNetwork
   ```

   > [!NOTE]
   > Quando você estiver usando o PowerShell ou a CLI do Azure, especifique a lista completa de filtros IP e ACLs de rede virtual em parâmetros, não apenas os que precisam ser adicionados.

1. Obtenha informações de rede virtual.

   ```powershell
   $vnet = Get-AzVirtualNetwork `
      -ResourceGroupName $resourceGroupName `
      -Name $vnetName

   $subnetId = $vnet.Id + "/subnets/" + $subnetName
   ```

1. Preparar uma regra de rede virtual Cosmos DB

   ```powershell
   $vnetRule = New-AzCosmosDBVirtualNetworkRule `
      -Id $subnetId
   ```

1. Atualize Azure Cosmos DB Propriedades da conta com a nova configuração de ponto de extremidade de rede virtual: 

   ```powershell
   $accountName = "<Cosmos DB account name>"

   Update-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName `
      -EnableVirtualNetwork $true `
      -VirtualNetworkRuleObject @($vnetRule)
   ```

1. Execute o seguinte comando para verificar se sua conta do Azure Cosmos DB está atualizada com o ponto de extremidade de serviço de rede virtual que você configurou na etapa anterior:

   ```powershell
   $account = Get-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName

   $account.IsVirtualNetworkFilterEnabled
   $account.VirtualNetworkRules
   ```

## <a name="configure-a-service-endpoint-by-using-the-azure-cli"></a><a id="configure-using-cli"></a> Configurar um ponto de extremidade de serviço usando a CLI do Azure

As contas do Azure Cosmos podem ser configuradas para pontos de extremidade de serviço quando forem criadas ou atualizadas posteriormente se a sub-rede já estiver configurada para elas. Os pontos de extremidade de serviço também podem ser habilitados na conta Cosmos em que a sub-rede ainda não está configurada para eles e começará a funcionar quando a sub-rede for configurada posteriormente. Essa flexibilidade permite aos administradores que não têm acesso à conta Cosmos e aos recursos de rede virtual para tornar suas configurações independentes umas das outras.

### <a name="create-a-new-cosmos-account-and-connect-it-to-a-back-end-subnet-for-a-new-virtual-network"></a>Criar uma nova conta do cosmos e conectá-la a uma sub-rede de back-end para uma nova rede virtual

Neste exemplo, a rede virtual e a sub-rede são criadas com pontos de extremidade de serviço habilitados para ambos quando eles são criados.

```azurecli-interactive
# Create an Azure Cosmos Account with a service endpoint connected to a backend subnet

# Resource group and Cosmos account variables
resourceGroupName='MyResourceGroup'
location='West US 2'
accountName='mycosmosaccount'

# Variables for a new Virtual Network with two subnets
vnetName='myVnet'
frontEnd='FrontEnd'
backEnd='BackEnd'

# Create a resource group
az group create -n $resourceGroupName -l $location

# Create a virtual network with a front-end subnet
az network vnet create \
   -n $vnetName \
   -g $resourceGroupName \
   --address-prefix 10.0.0.0/16 \
   --subnet-name $frontEnd \
   --subnet-prefix 10.0.1.0/24

# Create a back-end subnet with service endpoints enabled for Cosmos DB
az network vnet subnet create \
   -n $backEnd \
   -g $resourceGroupName \
   --address-prefix 10.0.2.0/24 \
   --vnet-name $vnetName \
   --service-endpoints Microsoft.AzureCosmosDB

svcEndpoint=$(az network vnet subnet show -g $resourceGroupName -n $backEnd --vnet-name $vnetName --query 'id' -o tsv)

# Create a Cosmos DB account with default values and service endpoints
az cosmosdb create \
   -n $accountName \
   -g $resourceGroupName \
   --enable-virtual-network true \
   --virtual-network-rules $svcEndpoint
```

### <a name="connect-and-configure-a-cosmos-account-to-a-back-end-subnet-independently"></a>Conectar e configurar uma conta do cosmos para uma sub-rede de back-end independentemente

Este exemplo destina-se a mostrar como conectar uma conta do Azure Cosmos a uma nova rede virtual existente na qual a sub-rede ainda não está configurada para pontos de extremidade de serviço. Isso é feito usando o `--ignore-missing-vnet-service-endpoint` parâmetro. Isso permite que a configuração da conta Cosmos seja concluída sem erros antes que a configuração da sub-rede da rede virtual seja concluída. Depois que a configuração da sub-rede for concluída, a conta do Cosmos poderá ser acessada por meio da sub-rede configurada.

```azurecli-interactive
# Create an Azure Cosmos Account with a service endpoint connected to a backend subnet
# that is not yet enabled for service endpoints.

# Resource group and Cosmos account variables
resourceGroupName='MyResourceGroup'
location='West US 2'
accountName='mycosmosaccount'

# Variables for a new Virtual Network with two subnets
vnetName='myVnet'
frontEnd='FrontEnd'
backEnd='BackEnd'

# Create a resource group
az group create -n $resourceGroupName -l $location

# Create a virtual network with a front-end subnet
az network vnet create \
   -n $vnetName \
   -g $resourceGroupName \
   --address-prefix 10.0.0.0/16 \
   --subnet-name $frontEnd \
   --subnet-prefix 10.0.1.0/24

# Create a back-end subnet but without configuring service endpoints (--service-endpoints Microsoft.AzureCosmosDB)
az network vnet subnet create \
   -n $backEnd \
   -g $resourceGroupName \
   --address-prefix 10.0.2.0/24 \
   --vnet-name $vnetName

svcEndpoint=$(az network vnet subnet show -g $resourceGroupName -n $backEnd --vnet-name $vnetName --query 'id' -o tsv)

# Create a Cosmos DB account with default values
az cosmosdb create -n $accountName -g $resourceGroupName

# Add the virtual network rule but ignore the missing service endpoint on the subnet
az cosmosdb network-rule add \
   -n $accountName \
   -g $resourceGroupName \
   --virtual-network $vnetName \
   --subnet svcEndpoint \
   --ignore-missing-vnet-service-endpoint true

read -p'Press any key to now configure the subnet for service endpoints'

az network vnet subnet update \
   -n $backEnd \
   -g $resourceGroupName \
   --vnet-name $vnetName \
   --service-endpoints Microsoft.AzureCosmosDB
```

## <a name="port-range-when-using-direct-mode"></a>Intervalo de portas ao usar o modo direto

Quando você estiver usando pontos de extremidade de serviço com uma conta do Azure Cosmos por meio de uma conexão de modo direto, será necessário garantir que o intervalo de portas TCP de 10000 a 20000 esteja aberto.

## <a name="migrating-from-an-ip-firewall-rule-to-a-virtual-network-acl"></a><a id="migrate-from-firewall-to-vnet"></a>Migrando de uma regra de firewall de IP para uma ACL de rede virtual

Para migrar uma conta de Azure Cosmos DB de usar regras de firewall IP para usar pontos de extremidade de serviço de rede virtual, use as etapas a seguir.

Depois que uma conta de Azure Cosmos DB é configurada para um ponto de extremidade de serviço para uma sub-rede, as solicitações dessa sub-rede são enviadas para Azure Cosmos DB com informações de origem de sub-rede e rede virtual em vez de um endereço IP público de origem. Essas solicitações não corresponderão mais a um filtro IP configurado na conta Azure Cosmos DB, motivo pelo qual as etapas a seguir são necessárias para evitar o tempo de inatividade.

Antes de continuar, habilite o ponto de extremidade de serviço Azure Cosmos DB na rede virtual e sub-rede usando a etapa mostrada acima em "habilitar o ponto de extremidade de serviço para uma sub-rede existente de uma rede virtual".

1. Obter informações de rede virtual e sub-rede:

   ```powershell
   $resourceGroupName = "myResourceGroup"
   $accountName = "mycosmosaccount"
   $vnetName = "myVnet"
   $subnetName = "mySubnet"

   $vnet = Get-AzVirtualNetwork `
      -ResourceGroupName $resourceGroupName `
      -Name $vnetName

   $subnetId = $vnet.Id + "/subnets/" + $subnetName
   ```

1. Prepare um novo objeto de regra de rede virtual para a conta de Azure Cosmos DB:

   ```powershell
   $vnetRule = New-AzCosmosDBVirtualNetworkRule `
      -Id $subnetId
   ```

1. Atualize a conta de Azure Cosmos DB para habilitar o acesso de ponto de extremidade de serviço da sub-rede:

   ```powershell
   Update-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName `
      -EnableVirtualNetwork $true `
      -VirtualNetworkRuleObject @($vnetRule)
   ```

1. Repita as etapas anteriores para todas as contas de Azure Cosmos DB acessadas da sub-rede.

1. Remova a regra de firewall IP da sub-rede das regras de firewall da conta Azure Cosmos DB.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

Aqui estão algumas perguntas frequentes sobre como configurar o acesso a partir de redes virtuais:

### <a name="are-notebooks-and-mongocassandra-shell-currently-compatible-with-virtual-network-enabled-accounts"></a>Os blocos de anotações e o Shell Mongo/Cassandra atualmente são compatíveis com contas habilitadas para rede virtual?

No momento, o [shell do Mongo](https://devblogs.microsoft.com/cosmosdb/preview-native-mongo-shell/) e o Shell do [Cassandra](https://devblogs.microsoft.com/cosmosdb/announcing-native-cassandra-shell-preview/) se integram no cosmos DB data Explorer e o [serviço do Jupyter notebooks](./cosmosdb-jupyter-notebooks.md), não têm suporte com o acesso à VNET. No momento, isso está em desenvolvimento.

### <a name="can-i-specify-both-virtual-network-service-endpoint-and-ip-access-control-policy-on-an-azure-cosmos-account"></a>Posso especificar o ponto de extremidade de serviço de rede virtual e a política de controle de acesso IP em uma conta do Azure Cosmos? 

Você pode habilitar o ponto de extremidade de serviço de rede virtual e uma política de controle de acesso IP (também conhecida como firewall) em sua conta do Azure Cosmos. Esses dois recursos são complementares e coletivamente garantem o isolamento e a segurança da sua conta do Azure Cosmos. Usar o firewall IP garante que os IPs estáticos possam acessar sua conta. 

### <a name="how-do-i-limit-access-to-subnet-within-a-virtual-network"></a>Como fazer para limitar o acesso à sub-rede dentro de uma rede virtual? 

Há duas etapas necessárias para limitar o acesso à conta do Azure Cosmos a partir de uma sub-rede. Primeiro, você permite que o tráfego da sub-rede carregue sua sub-rede e identidade de rede virtual para o Azure Cosmos DB. Isso é feito habilitando o ponto de extremidade de serviço do Azure Cosmos DB na sub-rede. Em seguida está adicionando uma regra na conta do Azure Cosmos especificando essa sub-rede como uma fonte da qual a conta pode ser acessada.

### <a name="will-virtual-network-acls-and-ip-firewall-reject-requests-or-connections"></a>As ACLs de rede virtual e o Firewall IP rejeitarão solicitações ou conexões? 

Quando o firewall IP ou as regras de acesso à rede virtual são adicionadas, somente solicitações de fontes permitidas obtêm respostas válidas. Outras solicitações são rejeitadas com um erro 403 (Proibido). É importante distinguir o firewall da conta do Azure Cosmos de um firewall no nível de conexão. A fonte ainda pode se conectar ao serviço e as conexões em si não são rejeitadas.

### <a name="my-requests-started-getting-blocked-when-i-enabled-service-endpoint-to-azure-cosmos-db-on-the-subnet-what-happened"></a>Minhas solicitações começaram a ser bloqueadas quando eu ativei o endpoint de serviço para o Azure Cosmos DB na sub-rede. O que aconteceu?

Depois que o ponto de extremidade de serviço do Azure Cosmos DB é habilitado em uma sub-rede, a origem do tráfego que chega à conta muda de IP público para rede virtual e sub-rede. Se a sua conta do Azure Cosmos tiver somente firewall baseado em IP, o tráfego da sub-rede habilitada para serviço não corresponderá mais às regras de firewall de IP e, portanto, será rejeitado. Siga os passos para migrar sem problemas do firewall baseado em IP para o controle de acesso baseado em rede virtual.

### <a name="are-additional-azure-rbac-permissions-needed-for-azure-cosmos-accounts-with-vnet-service-endpoints"></a>As permissões adicionais do RBAC do Azure são necessárias para contas do Azure cosmos com pontos de extremidade de serviço de VNET?

Após adicionar os pontos de extremidade do serviço de VNet a uma conta do Azure Cosmos, você precisa ter acesso à ação de `Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action` para todos os VNETs configurados em sua conta do Azure Cosmos para fazer alterações nas configurações da conta. Essa permissão é necessária porque o processo de autorização valida o acesso a recursos (como recursos de rede virtual e de banco de dados) antes de avaliar qualquer propriedade.
 
A autorização valida a permissão para a ação do recurso da VNet, mesmo se o usuário não especificar as ACLs da VNET usando a CLI do Azure. Atualmente, o plano de controle da conta do Azure Cosmos dá suporte à configuração do estado completo da conta do Azure Cosmos. Um dos parâmetros para as chamadas do plano de controle é o `virtualNetworkRules`. Se esse parâmetro não for especificado, a CLI do Azure fará uma chamada de banco de dados get para recuperar o `virtualNetworkRules` e usará esse valor na chamada de atualização.

### <a name="do-the-peered-virtual-networks-also-have-access-to-azure-cosmos-account"></a>As redes virtuais emparelhadas também têm acesso à conta do Azure Cosmos? 
Apenas a rede virtual e suas sub-redes adicionadas à conta do Azure Cosmos têm acesso. Suas VNets emparelhadas não podem acessar a conta até que as sub-redes em redes virtuais separadas sejam adicionadas à conta.

### <a name="what-is-the-maximum-number-of-subnets-allowed-to-access-a-single-cosmos-account"></a>Qual é o número máximo de sub-redes com permissão para acessar uma única conta do Cosmos? 
Atualmente, você pode ter no máximo 256 sub-redes permitidas para uma conta do Azure Cosmos.

### <a name="can-i-enable-access-from-vpn-and-express-route"></a>Posso habilitar o acesso da VPN e do Expresso Route? 
Para acessar a conta do Azure Cosmos pela rota Expressa no local, você precisa habilitar o emparelhamento da Microsoft. Depois de colocar as regras de firewall de IP ou de acesso à rede virtual, você poderá adicionar os endereços IP públicos usados para o emparelhamento da Microsoft em seu firewall IP da conta do Azure Cosmos para permitir acesso de serviços nas instalações à conta do Azure Cosmos. 

### <a name="do-i-need-to-update-the-network-security-groups-nsg-rules"></a>Preciso atualizar as regras do Network Security Groups (NSG)? 
As regras NSG são usadas para limitar a conectividade de e para uma sub-rede com rede virtual. Quando você adiciona o ponto de extremidade de serviço do Azure Cosmos DB à sub-rede, não há necessidade de abrir a conectividade de saída no NSG para sua conta do Azure Cosmos. 

### <a name="are-service-endpoints-available-for-all-vnets"></a>Os pontos de extremidade de serviço estão disponíveis para todas as VNets?
Não, somente as redes virtuais do Azure Resource Manager podem ter o ponto de extremidade do serviço ativado. As redes virtuais clássicas não suportam pontos de extremidade de serviço.

### <a name="can-i-accept-connections-from-within-public-azure-datacenters-when-service-endpoint-access-is-enabled-for-azure-cosmos-db"></a>Posso "Aceitar conexões de datacenters públicos do Azure" quando o acesso ao ponto de extremidade de serviço está habilitado para o Banco de Dados do Azure Cosmos?  
Isso é necessário somente quando você deseja que sua conta do Azure Cosmos DB seja acessada por outros serviços de terceiros do Azure primeiro, como Azure Data Factory, Azure Cognitive Search ou qualquer serviço implantado em determinada região do Azure.

## <a name="next-steps"></a>Próximas etapas

* Para configurar um firewall para o Azure Cosmos DB, consulte o artigo [Suporte ao firewall](how-to-configure-firewall.md).
