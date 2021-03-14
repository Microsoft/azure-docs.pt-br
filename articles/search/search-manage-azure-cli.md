---
title: CLI do Azure scripts usando o módulo de pesquisa AZ
titleSuffix: Azure Cognitive Search
description: Crie e configure um serviço de Pesquisa Cognitiva do Azure com o CLI do Azure. Você pode dimensionar um serviço para cima ou para baixo, gerenciar as chaves de API de administração e de consulta e consultar informações do sistema.
manager: luisca
author: DerekLegenzoff
ms.author: delegenz
ms.service: cognitive-search
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 02/17/2021
ms.openlocfilehash: ee6b0e1b745e86c72843af88c0f6d17f91512e15
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102176749"
---
# <a name="manage-your-azure-cognitive-search-service-with-the-azure-cli"></a>Gerenciar o serviço de Pesquisa Cognitiva do Azure com o CLI do Azure
> [!div class="op_single_selector"]
> * [Portal](search-manage.md)
> * [PowerShell](search-manage-powershell.md)
> * [CLI do Azure](search-manage-azure-cli.md)
> * [REST API](/rest/api/searchmanagement/)
> * [SDK .NET](/dotnet/api/microsoft.azure.management.search)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)

Você pode executar CLI do Azure comandos e scripts no Windows, no macOS, no Linux ou no [Azure cloud Shell](../cloud-shell/overview.md) para criar e configurar o pesquisa cognitiva do Azure. O módulo de [**pesquisa AZ**](/cli/azure/search) estende a [CLI do Azure](/cli/) com paridade total para as [APIs REST de gerenciamento de pesquisa](/rest/api/searchmanagement) e a capacidade de executar as seguintes tarefas:

> [!div class="checklist"]
> * [Listar serviços de pesquisa em uma assinatura](#list-search-services)
> * [Informações de serviço de retorno](#get-search-service-information)
> * [Criar ou excluir um serviço](#create-or-delete-a-service)
> * [Criar um serviço com um ponto de extremidade privado](#create-a-service-with-a-private-endpoint)
> * [Regenerar chaves de API de administrador](#regenerate-admin-keys)
> * [Criar ou excluir as chaves de API de consulta](#create-or-delete-query-keys)
> * [Escalar ou reduzir verticalmente com réplicas e partições](#scale-replicas-and-partitions)
> * [Criar um recurso de link privado compartilhado](#create-a-shared-private-link-resource)

Ocasionalmente, as perguntas são feitas sobre tarefas que *não* estão na lista acima. No momento, você não pode usar o módulo de **pesquisa AZ** ou a API REST de gerenciamento para alterar um nome de servidor, região ou camada. Os recursos dedicados são alocados quando um serviço é criado. Assim, alterar o hardware subjacente (local ou tipo de nó) requer um novo serviço. Da mesma forma, não há ferramentas ou APIs para transferir conteúdo, como um índice, de um serviço para outro.

Dentro de um serviço, a criação e o gerenciamento de conteúdo são por meio de [serviço de pesquisa API REST](/rest/api/searchservice/) ou [SDK do .net](/dotnet/api/overview/azure/search.documents-readme). Embora não haja nenhum comando do PowerShell dedicado para o conteúdo, você pode escrever scripts que chamam as APIs REST ou .NET para criar e carregar índices.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

<a name="list-search-services"></a>

## <a name="list-services-in-a-subscription"></a>Listar serviços em uma assinatura

Os comandos a seguir são de [**AZ Resource**](/cli/azure/resource), retornando informações sobre recursos e serviços existentes já provisionados em sua assinatura. Se você não souber quantos serviços de pesquisa já foram criados, esses comandos retornarão essas informações, poupando uma viagem ao Portal.

O primeiro comando retorna todos os serviços de pesquisa.

```azurecli-interactive
az resource list --resource-type Microsoft.Search/searchServices --output table
```

Na lista de serviços, retorne informações sobre um recurso específico.

```azurecli-interactive
az resource list --name <service-name>
```

## <a name="list-all-az-search-commands"></a>Listar todos os comandos de pesquisa AZ

Você pode exibir informações sobre os subgrupos e comandos disponíveis no [**AZ Search**](/cli/azure/search) de dentro da CLI. Como alternativa, você pode examinar a [documentação](/cli/azure/search).

Para exibir os subgrupos disponíveis no `az search` , execute o comando a seguir.

```azurecli-interactive
az search --help
```

A resposta deve ser semelhante à saída a seguir.

```
Group
    az search : Manage Azure Search services, admin keys and query keys.
        WARNING: This command group is in preview and under development. Reference and support
        levels: https://aka.ms/CLI_refstatus
Subgroups:
    admin-key                    : Manage Azure Search admin keys.
    private-endpoint-connection  : Manage Azure Search private endpoint connections.
    private-link-resource        : Manage Azure Search private link resources.
    query-key                    : Manage Azure Search query keys.
    service                      : Manage Azure Search services.
    shared-private-link-resource : Manage Azure Search shared private link resources.

For more specific examples, use: az find "az search"
```

Em cada subgrupo, vários comandos estão disponíveis. Você pode ver os comandos disponíveis para o `service` subgrupo executando a linha a seguir.

```azurecli-interactive
az search service --help
```

Você também pode ver os argumentos disponíveis para um comando específico.

```azurecli-interactive
az search service create --help
```

## <a name="get-search-service-information"></a>Obter informações do serviço de pesquisa

Se você souber o grupo de recursos que contém o serviço de pesquisa, execute [**AZ Search Service show**](/cli/azure/search/service#az_search_service_show) para retornar a definição de serviço, incluindo o nome, a região, a camada e as contagens de réplica e partição.

```azurecli-interactive
az search service show --name <service-name> --resource-group <resource-group-name>
```

## <a name="create-or-delete-a-service"></a>Criar ou excluir um serviço

Para [criar um novo serviço de pesquisa](search-create-service-portal.md), use o comando [**AZ Search Service Create**](/cli/azure/search/service#az_search_service_show) .

```azurecli-interactive
az search service create \
    --name <service-name> \
    --resource-group <resource-group-name> \
    --sku Standard \
    --partition-count 1 \
    --replica-count 1
``` 

Os resultados devem ser semelhantes à seguinte saída:

```
{
  "hostingMode": "default",
  "id": "/subscriptions/<alphanumeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp",
  "identity": null,
  "location": "West US",
  "name": "my-demo-searchapp",
  "networkRuleSet": {
    "bypass": "None",
    "ipRules": []
  },
  "partitionCount": 1,
  "privateEndpointConnections": [],
  "provisioningState": "succeeded",
  "publicNetworkAccess": "Enabled",
  "replicaCount": 1,
  "resourceGroup": "demo-westus",
  "sharedPrivateLinkResources": [],
  "sku": {
    "name": "standard"
  },
  "status": "running",
  "statusDetails": "",
  "tags": null,
  "type": "Microsoft.Search/searchServices"
}
```

### <a name="create-a-service-with-ip-rules"></a>Criar um serviço com regras de IP

Dependendo dos seus requisitos de segurança, talvez você queira criar um serviço de pesquisa com um [Firewall IP configurado](service-configure-firewall.md). Para fazer isso, passe os endereços IP públicos (v4) ou os intervalos CIDR para o `ip-rules` argumento, conforme mostrado abaixo. As regras devem ser separadas por uma vírgula ( `,` ) ou ponto e vírgula ( `;` ).

```azurecli-interactive
az search service create \
    --name <service-name> \
    --resource-group <resource-group-name> \
    --sku Standard \
    --partition-count 1 \
    --replica-count 1 \
    --ip-rules "55.5.63.73;52.228.215.197;101.37.221.205"
```

### <a name="create-a-service-with-a-system-assigned-managed-identity"></a>Criar um serviço com uma identidade gerenciada atribuída ao sistema

Em alguns casos, como ao [usar a identidade gerenciada para se conectar a uma fonte de dados](search-howto-managed-identities-storage.md), será necessário ativar a [identidade gerenciada atribuída ao sistema](../active-directory/managed-identities-azure-resources/overview.md). Isso é feito adicionando `--identity-type SystemAssigned` ao comando.

```azurecli-interactive
az search service create \
    --name <service-name> \
    --resource-group <resource-group-name> \
    --sku Standard \
    --partition-count 1 \
    --replica-count 1 \
    --identity-type SystemAssigned
```

## <a name="create-a-service-with-a-private-endpoint"></a>Criar um serviço com um ponto de extremidade privado

[Pontos de extremidade privados](../private-link/private-endpoint-overview.md) para o Azure pesquisa cognitiva permitem que um cliente em uma rede virtual acesse dados com segurança em um índice de pesquisa por um [link privado](../private-link/private-link-overview.md). O ponto de extremidade privado usa um endereço IP do [espaço de endereço de rede virtual](../virtual-network/private-ip-addresses.md) para o serviço de pesquisa. O tráfego de rede entre o cliente e o serviço de pesquisa atravessa a rede virtual e um link privado na rede de backbone da Microsoft, eliminando a exposição da Internet pública. Para obter mais detalhes, consulte a documentação sobre como [criar um ponto de extremidade privado para o Azure pesquisa cognitiva](service-create-private-endpoint.md)

O exemplo a seguir mostra como criar um serviço de pesquisa com um ponto de extremidade privado.

Primeiro, implante um serviço de pesquisa com `PublicNetworkAccess` definido como `Disabled` .

```azurecli-interactive
az search service create \
    --name <service-name> \
    --resource-group <resource-group-name> \
    --sku Standard \
    --partition-count 1 \
    --replica-count 1 \
    --public-access Disabled
```

Em seguida, crie uma rede virtual e o ponto de extremidade privado.

```azurecli-interactive
# Create the virtual network
az network vnet create \
    --resource-group <resource-group-name> \
    --location "West US" \
    --name <virtual-network-name> \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name <subnet-name> \
    --subnet-prefixes 10.1.0.0/24

# Update the subnet to disable private endpoint network policies
az network vnet subnet update \
    --name <subnet-name> \
    --resource-group <resource-group-name> \
    --vnet-name <virtual-network-name> \
    --disable-private-endpoint-network-policies true

# Get the id of the search service
id=$(az search service show \
    --resource-group <resource-group-name> \
    --name <service-name> \
    --query [id] \
    --output tsv)

# Create the private endpoint
az network private-endpoint create \
    --name <private-endpoint-name> \
    --resource-group <resource-group-name> \
    --vnet-name <virtual-network-name> \
    --subnet <subnet-name> \
    --private-connection-resource-id $id \
    --group-id searchService \
    --connection-name <private-link-connection-name>  
```

Por fim, crie uma zona DNS privada. 

```azurecli-interactive
## Create private DNS zone
az network private-dns zone create \
    --resource-group <resource-group-name> \
    --name "privatelink.search.windows.net"

## Create DNS network link
az network private-dns link vnet create \
    --resource-group <resource-group-name> \
    --zone-name "privatelink.search.windows.net" \
    --name "myLink" \
    --virtual-network <virtual-network-name> \
    --registration-enabled false

## Create DNS zone group
az network private-endpoint dns-zone-group create \
   --resource-group <resource-group-name>\
   --endpoint-name <private-endpoint-name> \
   --name "myZoneGroup" \
   --private-dns-zone "privatelink.search.windows.net" \
   --zone-name "searchServiceZone"
```

Para obter mais informações sobre como criar pontos de extremidade privados no PowerShell, consulte o [início rápido do link privado](https://docs.microsoft.com/azure/private-link/create-private-endpoint-cli)

### <a name="manage-private-endpoint-connections"></a>Gerenciar conexões de ponto de extremidade privado

Além de criar uma conexão de ponto de extremidade privada, você também pode `show` , `update` e `delete` a conexão.

Para recuperar uma conexão de ponto de extremidade privado e ver seu status, use [**AZ Search Private-Endpoint-Connection show**](/cli/azure/search/private-endpoint-connection#az_search_private_endpoint_connection_show).

```azurecli-interactive
az search private-endpoint-connection show \
    --name <pe-connection-name> \
    --service-name <search-service-name> \
    --resource-group <resource-group-name> 
```

Para atualizar a conexão, use [**AZ Search Private-Endpoint-Update Connection**](/cli/azure/search/private-endpoint-connection#az_search_private_endpoint_connection_update). O exemplo a seguir define uma conexão de ponto de extremidade privada como rejeitada:

```azurecli-interactive
az search private-endpoint-connection show \
    --name <pe-connection-name> \
    --service-name <search-service-name> \
    --resource-group <resource-group-name> 
    --status Rejected \
    --description "Rejected" \
    --actions-required "Please fix XYZ"
```

Para excluir a conexão de ponto de extremidade particular, use [**AZ Search Private-Endpoint-Connection Delete**](/cli/azure/search/private-endpoint-connection#az_search_private_endpoint_connection_delete).

```azurecli-interactive
az search private-endpoint-connection delete \
    --name <pe-connection-name> \
    --service-name <search-service-name> \
    --resource-group <resource-group-name> 
```

## <a name="regenerate-admin-keys"></a>Regenerar chaves de administrador

Para sobrepor [as chaves da API](search-security-api-keys.md)de administração, use [**AZ Search Admin-Key Renew**](/cli/azure/search/admin-key#az_search_admin_key_renew). Duas chaves de administração são criadas com cada serviço para acesso autenticado. As chaves são necessárias em cada solicitação. Ambas as chaves de administração são funcionalmente equivalentes, concedendo acesso de gravação total a um serviço de pesquisa com a capacidade de recuperar qualquer informação ou criar e excluir qualquer objeto. Existem duas chaves para que você possa usar uma enquanto substitui a outra. 

Você só pode gerar um de cada vez, especificado como a `primary` `secondary` chave ou. Para o serviço ininterrupto, lembre-se de atualizar todo o código do cliente para usar uma chave secundária ao reverter a chave primária. Evite alterar as chaves enquanto as operações estiverem em trânsito.

Como você deve esperar, se você regenerar chaves sem Atualizar o código do cliente, as solicitações que usam a chave antiga falharão. A regeneração de todas as novas chaves não o bloqueia permanentemente de seu serviço e você ainda pode acessar o serviço por meio do Portal. Depois de regenerar chaves primárias e secundárias, você pode atualizar o código do cliente para usar as novas chaves e as operações serão retomadas de acordo.

Os valores para as chaves de API são gerados pelo serviço. Você não pode fornecer uma chave personalizada para uso do Azure Pesquisa Cognitiva. Da mesma forma, não há nenhum nome definido pelo usuário para as chaves de API de administração. As referências à chave são cadeias de caracteres fixas, `primary` ou `secondary` . 

```azurecli-interactive
az search admin-key renew \
    --resource-group <resource-group-name> \
    --service-name <search-service-name> \
    --key-kind primary
```

Os resultados devem ser semelhantes à saída a seguir. Ambas as chaves são retornadas, mesmo que você altere apenas uma por vez.

```   
{
  "primaryKey": <alphanumeric-guid>,
  "secondaryKey": <alphanumeric-guid>  
}
```

## <a name="create-or-delete-query-keys"></a>Criar ou excluir chaves de consulta

Para criar [chaves de API](search-security-api-keys.md) de consulta para acesso somente leitura de aplicativos cliente a um índice de pesquisa cognitiva do Azure, use [**AZ Search Query-Key Create**](/cli/azure/search/query-key#az_search_query_key_create). As chaves de consulta são usadas para autenticar em um índice específico com a finalidade de recuperar os resultados da pesquisa. As chaves de consulta não concedem acesso somente leitura a outros itens no serviço, como um índice, uma fonte de dados ou um indexador.

Você não pode fornecer uma chave para uso do Azure Pesquisa Cognitiva. As chaves de API são geradas pelo serviço.

```azurecli-interactive
az search query-key create \
    --name myQueryKey \
    --resource-group <resource-group-name> \
    --service-name <search-service-name>
```

## <a name="scale-replicas-and-partitions"></a>Dimensionar réplicas e partições

Para [aumentar ou diminuir réplicas e partições,](search-capacity-planning.md) use [**AZ Search Service Update**](/cli/azure/search/service#az_search_service_update). O aumento de réplicas ou partições adiciona à sua fatura, que tem encargos fixos e variáveis. Se você tiver uma necessidade temporária de poder de processamento adicional, poderá aumentar as réplicas e as partições para lidar com a carga de trabalho. A área de monitoramento na página do portal de visão geral tem blocos de latência de consulta, consultas por segundo e limitação, indicando se a capacidade atual é adequada.

Pode levar algum tempo para adicionar ou remover a origem. Os ajustes na capacidade ocorrem em segundo plano, permitindo que as cargas de trabalho existentes continuem. A capacidade adicional é usada para solicitações de entrada assim que ela estiver pronta, sem necessidade de configuração adicional. 

A remoção da capacidade pode causar interrupções. Parar todos os trabalhos de indexação e indexador antes de reduzir a capacidade é recomendado para evitar solicitações ignoradas. Se isso não for viável, você pode considerar reduzir a capacidade incrementalmente, uma réplica e uma partição de cada vez, até que os novos níveis de destino sejam atingidos.

Depois de enviar o comando, não é possível encerrá-lo no centro. Você precisará aguardar até que o comando seja concluído antes de revisar as contagens.

```azurecli-interactive
az search service update \
    --name <service-name> \
    --resource-group <resource-group-name> \
    --partition-count 6 \
    --replica-count 6
```

Além de atualizar as contagens de réplica e partição, você também pode atualizar `ip-rules` , `public-access` e `identity-type` .

## <a name="create-a-shared-private-link-resource"></a>Criar um recurso de link privado compartilhado

Pontos de extremidade privados de recursos protegidos que são criados por meio de APIs de Pesquisa Cognitiva do Azure são chamados de *recursos de link privado compartilhado*. Isso ocorre porque você está "compartilhando" o acesso a um recurso, como uma conta de armazenamento, que foi integrado com o [serviço de link privado do Azure](https://azure.microsoft.com/services/private-link/).

Se você estiver usando um indexador para indexar dados no Azure Pesquisa Cognitiva e sua fonte de dados estiver em uma rede privada, você poderá criar uma [conexão de ponto de extremidade privada](../private-link/private-endpoint-overview.md) de saída para alcançar os dados.

Uma lista completa dos recursos do Azure para os quais você pode criar pontos de extremidade privados de saída do Azure Pesquisa Cognitiva pode ser encontrada [aqui](search-indexer-howto-access-private.md#shared-private-link-resources-management-apis) junto com os valores de **ID de grupo** relacionados.

Para criar o recurso de link privado compartilhado, use [**AZ Search Shared-particular-link-Resource Create**](/cli/azure/search/shared-private-link-resource#az_search_shared_private_link_resource_list). Tenha em mente que algumas configurações podem ser necessárias para a fonte de dados antes de executar esse comando.

```azurecli-interactive
az search shared-private-link-resource create \
    --name <spl-name> \
    --service-name <search-service-name> \
    --resource-group <resource-group-name> \
    --group-id blob \
    --resource-id "/subscriptions/<alphanumeric-subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/myBlobStorage"  \
    --request-message "Please approve" 
```


Para recuperar os recursos de link privado compartilhado e exibir seu status, use [**AZ Search Shared-Private-link-Resource List**](/cli/azure/search/shared-private-link-resource#az_search_shared_private_link_resource_list).

```azurecli-interactive
az search shared-private-link-resource list \
    --service-name <search-service-name> \
    --resource-group <resource-group-name> 
```

Você precisará aprovar a conexão com o comando a seguir antes que ele possa ser usado. A ID da conexão de ponto de extremidade particular precisará ser recuperada do recurso filho. Nesse caso, obtemos a ID de conexão do AZ Storage.

```azurecli-interactive
id = (az storage account show -n myBlobStorage --query "privateEndpointConnections[0].id")

az network private-endpoint-connection approve --id $id
```

Para excluir o recurso de link privado compartilhado, use [**AZ Search Shared-particular-link-Resource Delete**](/cli/azure/search/shared-private-link-resource#az_search_shared_private_link_resource_delete).

```azurecli-interactive
az search shared-private-link-resource delete \
    --name <spl-name> \
    --service-name <search-service-name> \
    --resource-group <resource-group-name> 
```

Para obter detalhes completos sobre como configurar recursos de link privado compartilhado, consulte a documentação sobre como [fazer conexões de indexador por meio de um ponto de extremidade privado](search-indexer-howto-access-private.md).

## <a name="next-steps"></a>Próximas etapas

Crie um [índice](search-what-is-an-index.md), [consulte um índice](search-query-overview.md) usando o portal, as APIs REST ou o SDK do .net.

* [Criar um índice da Pesquisa Cognitiva do Azure no portal do Azure](search-get-started-portal.md)
* [Configurar um indexador para carregar dados de outros serviços](search-indexer-overview.md)
* [Consultar um índice de Pesquisa Cognitiva do Azure usando o Search Explorer no portal do Azure](search-explorer.md)
* [Como usar o Azure Pesquisa Cognitiva no .NET](search-howto-dotnet-sdk.md)
