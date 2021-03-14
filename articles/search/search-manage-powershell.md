---
title: Scripts do PowerShell usando o módulo AZ. Search
titleSuffix: Azure Cognitive Search
description: Criar e configurar um serviço de Pesquisa Cognitiva do Azure com o PowerShell. Você pode dimensionar um serviço para cima ou para baixo, gerenciar as chaves de API de administração e de consulta e consultar informações do sistema.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: powershell
ms.topic: conceptual
ms.date: 02/09/2021
ms.openlocfilehash: 111424ab3a3bd6020e70a08ddf2c494996f6f0ea
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101676677"
---
# <a name="manage-your-azure-cognitive-search-service-with-powershell"></a>Gerenciar o serviço de Pesquisa Cognitiva do Azure com o PowerShell
> [!div class="op_single_selector"]
> * [Portal](search-manage.md)
> * [PowerShell](search-manage-powershell.md)
> * [CLI do Azure](search-manage-azure-cli.md)
> * [REST API](/rest/api/searchmanagement/)
> * [SDK .NET](/dotnet/api/microsoft.azure.management.search)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)

Você pode executar os cmdlets e scripts do PowerShell no Windows, Linux ou no [Azure cloud Shell](../cloud-shell/overview.md) para criar e configurar o pesquisa cognitiva do Azure. O módulo **AZ. Search** estende [Azure PowerShell](/powershell/) com paridade total para as [APIs REST de gerenciamento de pesquisa](/rest/api/searchmanagement) e a capacidade de executar as seguintes tarefas:

> [!div class="checklist"]
> * [Listar serviços de pesquisa em uma assinatura](#list-search-services)
> * [Informações de serviço de retorno](#get-search-service-information)
> * [Criar ou excluir um serviço](#create-or-delete-a-service)
> * [Criar um serviço com um ponto de extremidade privado](#create-a-service-with-a-private-endpoint)
> * [Regenerar chaves de API de administrador](#regenerate-admin-keys)
> * [Criar ou excluir as chaves de API de consulta](#create-or-delete-query-keys)
> * [Escalar ou reduzir verticalmente com réplicas e partições](#scale-replicas-and-partitions)
> * [Criar um recurso de link privado compartilhado](#create-a-shared-private-link-resource)

Ocasionalmente, as perguntas são feitas sobre tarefas que *não* estão na lista acima. No momento, você não pode usar o módulo **AZ. Search** ou a API REST de gerenciamento para alterar um nome de servidor, região ou camada. Os recursos dedicados são alocados quando um serviço é criado. Assim, alterar o hardware subjacente (local ou tipo de nó) requer um novo serviço. Da mesma forma, não há ferramentas ou APIs para transferir conteúdo, como um índice, de um serviço para outro.

Dentro de um serviço, a criação e o gerenciamento de conteúdo são por meio de [serviço de pesquisa API REST](/rest/api/searchservice/) ou [SDK do .net](/dotnet/api/overview/azure/search.documents-readme). Embora não haja nenhum comando do PowerShell dedicado para o conteúdo, você pode escrever o script do PowerShell que chama as APIs REST ou .NET para criar e carregar índices.

<a name="check-versions-and-load"></a>

## <a name="check-versions-and-load-modules"></a>Verificar versões e carregar módulos

Os exemplos neste artigo são interativos e exigem permissões elevadas. Azure PowerShell (o módulo **AZ** ) deve ser instalado. Para obter mais informações, consulte [Instalar o Azure PowerShell](/powershell/azure/).

### <a name="powershell-version-check-51-or-later"></a>Verificação de versão do PowerShell (5,1 ou posterior)

O PowerShell local deve ser 5,1 ou posterior em qualquer sistema operacional com suporte.

```azurepowershell-interactive
$PSVersionTable.PSVersion
```

### <a name="load-azure-powershell"></a>Carregar Azure PowerShell

Se você não tiver certeza se **AZ** está instalado, execute o comando a seguir como uma etapa de verificação. 

```azurepowershell-interactive
Get-InstalledModule -Name Az
```

Alguns sistemas não carregam módulos automaticamente. Se você receber um erro no comando anterior, tente carregar o módulo e, se isso falhar, volte para as instruções de instalação para ver se você perdeu uma etapa.

```azurepowershell-interactive
Import-Module -Name Az
```

### <a name="connect-to-azure-with-a-browser-sign-in-token"></a>Conectar-se ao Azure com um token de entrada do navegador

Você pode usar suas credenciais de logon do portal para se conectar a uma assinatura no PowerShell. Como alternativa, você pode [autenticar de forma não interativa com uma entidade de serviço](../active-directory/develop/howto-authenticate-service-principal-powershell.md).

```azurepowershell-interactive
Connect-AzAccount
```

Se você mantiver várias assinaturas do Azure, defina sua assinatura do Azure. Para ver uma lista de suas assinaturas atuais, execute este comando.

```azurepowershell-interactive
Get-AzSubscription | sort SubscriptionName | Select SubscriptionName
```

Para especificar a assinatura, execute o comando a seguir. No exemplo a seguir, o nome da assinatura é `ContosoSubscription`.

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName ContosoSubscription
```

<a name="list-search-services"></a>

## <a name="list-services-in-a-subscription"></a>Listar serviços em uma assinatura

Os comandos a seguir são de [**AZ. Resources**](/powershell/module/az.resources), retornando informações sobre os recursos e serviços existentes já provisionados em sua assinatura. Se você não souber quantos serviços de pesquisa já foram criados, esses comandos retornarão essas informações, poupando uma viagem ao Portal.

O primeiro comando retorna todos os serviços de pesquisa.

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Search/searchServices | ft
```

Na lista de serviços, retorne informações sobre um recurso específico.

```azurepowershell-interactive
Get-AzResource -ResourceName <service-name>
```

Os resultados devem ser semelhantes à saída a seguir.

```
Name              : my-demo-searchapp
ResourceGroupName : demo-westus
ResourceType      : Microsoft.Search/searchServices
Location          : westus
ResourceId        : /subscriptions/<alphanumeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="import-azsearch"></a>Importar AZ. Search

Os comandos de [**AZ. Search**](/powershell/module/az.search) não estarão disponíveis até que você carregue o módulo.

```azurepowershell-interactive
Install-Module -Name Az.Search
```

### <a name="list-all-azsearch-commands"></a>Listar todos os comandos AZ. Search

Como uma etapa de verificação, retorne uma lista de comandos fornecidos no módulo.

```azurepowershell-interactive
Get-Command -Module Az.Search
```

Os resultados devem ser semelhantes à saída a seguir.

```
CommandType     Name                                               Version    Source                                                                
-----------     ----                                               -------    ------                                                                
Cmdlet          Get-AzSearchAdminKeyPair                           0.8.0      Az.Search                                                             
Cmdlet          Get-AzSearchPrivateEndpointConnection              0.8.0      Az.Search                                                             
Cmdlet          Get-AzSearchPrivateLinkResource                    0.8.0      Az.Search                                                             
Cmdlet          Get-AzSearchQueryKey                               0.8.0      Az.Search                                                             
Cmdlet          Get-AzSearchService                                0.8.0      Az.Search                                                             
Cmdlet          Get-AzSearchSharedPrivateLinkResource              0.8.0      Az.Search                                                             
Cmdlet          New-AzSearchAdminKey                               0.8.0      Az.Search                                                             
Cmdlet          New-AzSearchQueryKey                               0.8.0      Az.Search                                                             
Cmdlet          New-AzSearchService                                0.8.0      Az.Search                                                             
Cmdlet          New-AzSearchSharedPrivateLinkResource              0.8.0      Az.Search                                                             
Cmdlet          Remove-AzSearchPrivateEndpointConnection           0.8.0      Az.Search                                                             
Cmdlet          Remove-AzSearchQueryKey                            0.8.0      Az.Search                                                             
Cmdlet          Remove-AzSearchService                             0.8.0      Az.Search                                                             
Cmdlet          Remove-AzSearchSharedPrivateLinkResource           0.8.0      Az.Search                                                             
Cmdlet          Set-AzSearchPrivateEndpointConnection              0.8.0      Az.Search                                                             
Cmdlet          Set-AzSearchService                                0.8.0      Az.Search                                                             
Cmdlet          Set-AzSearchSharedPrivateLinkResource              0.8.0      Az.Search   
```

Se você tiver uma versão mais antiga do pacote, atualize o módulo para obter a funcionalidade mais recente.

```azurepowershell-interactive
Update-Module -Name Az.Search
```

## <a name="get-search-service-information"></a>Obter informações do serviço de pesquisa

Depois que **AZ. Search** for importado e você souber que o grupo de recursos que contém o serviço de pesquisa, execute [Get-AzSearchService](/powershell/module/az.search/get-azsearchservice) para retornar a definição de serviço, incluindo o nome, a região, a camada e as contagens de réplica e partição.

```azurepowershell-interactive
Get-AzSearchService -ResourceGroupName <resource-group-name>
```

Os resultados devem ser semelhantes à saída a seguir.

```
Name              : my-demo-searchapp
ResourceGroupName : demo-westus
ResourceType      : Microsoft.Search/searchServices
Location          : West US
Sku               : Standard
ReplicaCount      : 1
PartitionCount    : 1
HostingMode       : Default
ResourceId        : /subscriptions/<alphanumeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="create-or-delete-a-service"></a>Criar ou excluir um serviço

[**New-AzSearchService**](/powershell/module/az.search/new-azsearchadminkey) é usado para [criar um novo serviço de pesquisa](search-create-service-portal.md).

```azurepowershell-interactive
New-AzSearchService -ResourceGroupName <resource-group-name> -Name <search-service-name> -Sku "Standard" -Location "West US" -PartitionCount 3 -ReplicaCount 3 -HostingMode Default
``` 
Os resultados devem ser semelhantes à saída a seguir.

```
ResourceGroupName : demo-westus
Name              : my-demo-searchapp
Id                : /subscriptions/<alphanumeric-subscription-ID>/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
Location          : West US
Sku               : Standard
ReplicaCount      : 3
PartitionCount    : 3
HostingMode       : Default
Tags
```     

### <a name="create-a-service-with-ip-rules"></a>Criar um serviço com regras de IP

Dependendo dos seus requisitos de segurança, talvez você queira criar um serviço de pesquisa com um [Firewall IP configurado](service-configure-firewall.md). Para fazer isso, primeiro defina as regras de IP e, em seguida, passe-as para o `IPRuleList` parâmetro, conforme mostrado abaixo.

```azurepowershell-interactive
$ipRules = @([pscustomobject]@{Value="55.5.63.73"},
        [pscustomobject]@{Value="52.228.215.197"},
        [pscustomobject]@{Value="101.37.221.205"})

 New-AzSearchService -ResourceGroupName <resource-group-name> `
                      -Name <search-service-name> `
                      -Sku Standard `
                      -Location "West US" `
                      -PartitionCount 3 -ReplicaCount 3 `
                      -HostingMode Default `
                      -IPRuleList $ipRules
```

### <a name="create-a-service-with-a-system-assigned-managed-identity"></a>Criar um serviço com uma identidade gerenciada atribuída ao sistema

Em alguns casos, como ao [usar a identidade gerenciada para se conectar a uma fonte de dados](search-howto-managed-identities-storage.md), será necessário ativar a [identidade gerenciada atribuída ao sistema](../active-directory/managed-identities-azure-resources/overview.md). Isso é feito adicionando `-IdentityType SystemAssigned` ao comando.

```azurepowershell-interactive
New-AzSearchService -ResourceGroupName <resource-group-name> `
                      -Name <search-service-name> `
                      -Sku Standard `
                      -Location "West US" `
                      -PartitionCount 3 -ReplicaCount 3 `
                      -HostingMode Default `
                      -IdentityType SystemAssigned
```

## <a name="create-a-service-with-a-private-endpoint"></a>Criar um serviço com um ponto de extremidade privado

[Pontos de extremidade privados](../private-link/private-endpoint-overview.md) para o Azure pesquisa cognitiva permitem que um cliente em uma rede virtual acesse dados com segurança em um índice de pesquisa por um [link privado](../private-link/private-link-overview.md). O ponto de extremidade privado usa um endereço IP do [espaço de endereço de rede virtual](../virtual-network/private-ip-addresses.md) para o serviço de pesquisa. O tráfego de rede entre o cliente e o serviço de pesquisa atravessa a rede virtual e um link privado na rede de backbone da Microsoft, eliminando a exposição da Internet pública. Para obter mais detalhes, consulte a documentação sobre como [criar um ponto de extremidade privado para o Azure pesquisa cognitiva](service-create-private-endpoint.md)

O exemplo a seguir mostra como criar um serviço de pesquisa com um ponto de extremidade privado.

Primeiro, implante um serviço de pesquisa com `PublicNetworkAccess` definido como `Disabled` .

```azurepowershell-interactive
$searchService = New-AzSearchService `
    -ResourceGroupName <resource-group-name> `
    -Name <search-service-name> `
    -Sku Standard `
    -Location "West US" `
    -PartitionCount 1 -ReplicaCount 1 `
    -HostingMode Default `
    -PublicNetworkAccess Disabled
```

Em seguida, crie uma rede virtual, uma conexão de rede privada e o ponto de extremidade privado.

```azurepowershell-interactive
# Create the subnet
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
    -Name <subnet-name> `
    -AddressPrefix 10.1.0.0/24 `
    -PrivateEndpointNetworkPolicies Disabled 

# Create the virtual network
$virtualNetwork = New-AzVirtualNetwork `
    -ResourceGroupName <resource-group-name> `
    -Location "West US" `
    -Name <virtual-network-name> `
    -AddressPrefix 10.1.0.0/16 `
    -Subnet $subnetConfig

# Create the private network connection
$privateLinkConnection = New-AzPrivateLinkServiceConnection `
    -Name <private-link-name> `
    -PrivateLinkServiceId $searchService.Id `
    -GroupId searchService

# Create the private endpoint
$privateEndpoint = New-AzPrivateEndpoint `
    -Name <private-endpoint-name> `
    -ResourceGroupName <resource-group-name> `
    -Location "West US" `
    -Subnet $virtualNetwork.subnets[0] `
    -PrivateLinkServiceConnection $privateLinkConnection
```

Por fim, crie uma zona DNS privada. 

```azurepowershell-interactive
## Create private dns zone
$zone = New-AzPrivateDnsZone `
    -ResourceGroupName <resource-group-name> `
    -Name "privatelink.search.windows.net"

## Create dns network link
$link = New-AzPrivateDnsVirtualNetworkLink `
    -ResourceGroupName <resource-group-name> `
    -ZoneName "privatelink.search.windows.net" `
    -Name "myLink" `
    -VirtualNetworkId $virtualNetwork.Id

## Create DNS configuration 
$config = New-AzPrivateDnsZoneConfig `
    -Name "privatelink.search.windows.net" `
    -PrivateDnsZoneId $zone.ResourceId

## Create DNS zone group
New-AzPrivateDnsZoneGroup `
    -ResourceGroupName <resource-group-name> `
    -PrivateEndpointName <private-endpoint-name> `
    -Name 'myZoneGroup' `
    -PrivateDnsZoneConfig $config
```

Para obter mais detalhes sobre a criação de pontos de extremidade privados no PowerShell, consulte o [início rápido do link privado](../private-link/create-private-endpoint-powershell.md)

### <a name="manage-private-endpoint-connections"></a>Gerenciar conexões de ponto de extremidade privado

Além de criar uma conexão de ponto de extremidade privada, você também pode `Get` , `Set` e `Remove` a conexão.

[Get-AzSearchPrivateEndpointConnection](/powershell/module/az.search/Get-AzSearchPrivateEndpointConnection) é usado para recuperar uma conexão de ponto de extremidade privada e para ver seu status.

```azurepowershell-interactive
Get-AzSearchPrivateEndpointConnection -ResourceGroupName <resource-group-name> -ServiceName <search-service-name>
```

[Set-AzSearchPrivateEndpointConnection](/powershell/module/az.search/Set-AzSearchPrivateEndpointConnection) é usado para atualizar a conexão. O exemplo a seguir define uma conexão de ponto de extremidade privada como rejeitada:

```azurepowershell-interactive
Set-AzSearchPrivateEndpointConnection -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -Name <pe-connection-name> -Status Rejected  -Description "Rejected"
```

[Remove-AzSearchPrivateEndpointConnection](/powershell/module/az.search/Remove-AzSearchPrivateEndpointConnection) é usado para excluir a conexão de ponto de extremidade privada.

```azurepowershell-interactive
 Remove-AzSearchPrivateEndpointConnection -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -Name <pe-connection-name>
```

## <a name="regenerate-admin-keys"></a>Regenerar chaves de administrador

[**New-AzSearchAdminKey**](/powershell/module/az.search/new-azsearchadminkey) é usado para sobrepor chaves de [API](search-security-api-keys.md)de administração. Duas chaves de administração são criadas com cada serviço para acesso autenticado. As chaves são necessárias em cada solicitação. Ambas as chaves de administração são funcionalmente equivalentes, concedendo acesso de gravação total a um serviço de pesquisa com a capacidade de recuperar qualquer informação ou criar e excluir qualquer objeto. Existem duas chaves para que você possa usar uma enquanto substitui a outra. 

Você só pode gerar um de cada vez, especificado como a `primary` `secondary` chave ou. Para o serviço ininterrupto, lembre-se de atualizar todo o código do cliente para usar uma chave secundária ao reverter a chave primária. Evite alterar as chaves enquanto as operações estiverem em trânsito.

Como você deve esperar, se você regenerar chaves sem Atualizar o código do cliente, as solicitações que usam a chave antiga falharão. A regeneração de todas as novas chaves não o bloqueia permanentemente de seu serviço e você ainda pode acessar o serviço por meio do Portal. Depois de regenerar chaves primárias e secundárias, você pode atualizar o código do cliente para usar as novas chaves e as operações serão retomadas de acordo.

Os valores para as chaves de API são gerados pelo serviço. Você não pode fornecer uma chave personalizada para uso do Azure Pesquisa Cognitiva. Da mesma forma, não há nenhum nome definido pelo usuário para as chaves de API de administração. As referências à chave são cadeias de caracteres fixas, `primary` ou `secondary` . 

```azurepowershell-interactive
New-AzSearchAdminKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -KeyKind Primary
```

Os resultados devem ser semelhantes à saída a seguir. Ambas as chaves são retornadas, mesmo que você altere apenas uma por vez.

```
Primary                    Secondary
-------                    ---------
<alphanumeric-guid>        <alphanumeric-guid>  
```

## <a name="create-or-delete-query-keys"></a>Criar ou excluir chaves de consulta

[**New-AzSearchQueryKey**](/powershell/module/az.search/new-azsearchquerykey) é usado para criar [chaves de API](search-security-api-keys.md) de consulta para acesso somente leitura de aplicativos cliente a um índice de pesquisa cognitiva do Azure. As chaves de consulta são usadas para autenticar em um índice específico com a finalidade de recuperar os resultados da pesquisa. As chaves de consulta não concedem acesso somente leitura a outros itens no serviço, como um índice, uma fonte de dados ou um indexador.

Você não pode fornecer uma chave para uso do Azure Pesquisa Cognitiva. As chaves de API são geradas pelo serviço.

```azurepowershell-interactive
New-AzSearchQueryKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -Name <query-key-name> 
```

## <a name="scale-replicas-and-partitions"></a>Dimensionar réplicas e partições

[**Set-AzSearchService**](/powershell/module/az.search/set-azsearchservice) é usado para [aumentar ou diminuir réplicas e partições](search-capacity-planning.md) para reajustar recursos faturáveis dentro de seu serviço. O aumento de réplicas ou partições adiciona à sua fatura, que tem encargos fixos e variáveis. Se você tiver uma necessidade temporária de poder de processamento adicional, poderá aumentar as réplicas e as partições para lidar com a carga de trabalho. A área de monitoramento na página do portal de visão geral tem blocos de latência de consulta, consultas por segundo e limitação, indicando se a capacidade atual é adequada.

Pode levar algum tempo para adicionar ou remover a origem. Os ajustes na capacidade ocorrem em segundo plano, permitindo que as cargas de trabalho existentes continuem. A capacidade adicional é usada para solicitações de entrada assim que ela estiver pronta, sem necessidade de configuração adicional. 

A remoção da capacidade pode causar interrupções. Parar todos os trabalhos de indexação e indexador antes de reduzir a capacidade é recomendado para evitar solicitações ignoradas. Se isso não for viável, você pode considerar reduzir a capacidade incrementalmente, uma réplica e uma partição de cada vez, até que os novos níveis de destino sejam atingidos.

Depois de enviar o comando, não é possível encerrá-lo no centro. Você precisará aguardar até que o comando seja concluído antes de revisar as contagens.

```azurepowershell-interactive
Set-AzSearchService -ResourceGroupName <resource-group-name> -Name <search-service-name> -PartitionCount 6 -ReplicaCount 6
```

Os resultados devem ser semelhantes à saída a seguir.

```
ResourceGroupName : demo-westus
Name              : my-demo-searchapp
Location          : West US
Sku               : Standard
ReplicaCount      : 6
PartitionCount    : 6
HostingMode       : Default
Id                : /subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="create-a-shared-private-link-resource"></a>Criar um recurso de link privado compartilhado

Pontos de extremidade privados de recursos protegidos que são criados por meio de APIs de Pesquisa Cognitiva do Azure são chamados de *recursos de link privado compartilhado*. Isso ocorre porque você está "compartilhando" o acesso a um recurso, como uma conta de armazenamento, que foi integrado com o [serviço de link privado do Azure](https://azure.microsoft.com/services/private-link/).

Se você estiver usando um indexador para indexar dados no Azure Pesquisa Cognitiva e sua fonte de dados estiver em uma rede privada, você poderá criar uma [conexão de ponto de extremidade privada](../private-link/private-endpoint-overview.md) de saída para alcançar os dados.

Uma lista completa dos recursos do Azure para os quais você pode criar pontos de extremidade privados de saída do Azure Pesquisa Cognitiva pode ser encontrada [aqui](search-indexer-howto-access-private.md#shared-private-link-resources-management-apis) junto com os valores de **ID de grupo** relacionados.

[New-AzSearchSharedPrivateLinkResource](/powershell/module/az.search/New-AzSearchSharedPrivateLinkResource) é usado para criar o recurso de link privado compartilhado. Tenha em mente que algumas configurações podem ser necessárias para a fonte de dados antes de executar esse comando.

```azurepowershell-interactive
New-AzSearchSharedPrivateLinkResource -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -Name <spl-name> -PrivateLinkResourceId /subscriptions/<alphanumeric-subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/myBlobStorage -GroupId <group-id> -RequestMessage "Please approve" 
```

O [Get-AzSearchSharedPrivateLinkResource](/powershell/module/az.search/Get-AzSearchSharedPrivateLinkResource) permite que você recupere os recursos de link privado compartilhado e exiba seu status.

```azurepowershell-interactive
Get-AzSearchSharedPrivateLinkResource -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -Name <spl-name>
```

Você precisará aprovar a conexão com o comando a seguir antes que ele possa ser usado.

```azurepowershell-interactive
Approve-AzPrivateEndpointConnection `
    -Name <spl-name> `
    -ServiceName <search-service-name> `
    -ResourceGroupName <resource-group-name> `
    -Description = "Approved"
```

[Remove-AzSearchSharedPrivateLinkResource](/powershell/module/az.search/Remove-AzSearchSharedPrivateLinkResource) é usado para excluir o recurso de link privado compartilhado.

```azurepowershell-interactive
$job = Remove-AzSearchSharedPrivateLinkResource -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -Name <spl-name> -Force -AsJob

$job | Get-Job
```

Para obter detalhes completos sobre como configurar recursos de link privado compartilhado, consulte a documentação sobre como [fazer conexões de indexador por meio de um ponto de extremidade privado](search-indexer-howto-access-private.md).

## <a name="next-steps"></a>Próximas etapas

Crie um [índice](search-what-is-an-index.md), [consulte um índice](search-query-overview.md) usando o portal, as APIs REST ou o SDK do .net.

* [Criar um índice da Pesquisa Cognitiva do Azure no portal do Azure](search-get-started-portal.md)
* [Configurar um indexador para carregar dados de outros serviços](search-indexer-overview.md)
* [Consultar um índice de Pesquisa Cognitiva do Azure usando o Search Explorer no portal do Azure](search-explorer.md)
* [Como usar o Azure Pesquisa Cognitiva no .NET](search-howto-dotnet-sdk.md)