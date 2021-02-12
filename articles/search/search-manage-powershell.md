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
ms.date: 02/11/2020
ms.openlocfilehash: d7b672b7e2c3004eba4a38bd659965b7dee24db6
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422477"
---
# <a name="manage-your-azure-cognitive-search-service-with-powershell"></a>Gerenciar o serviço de Pesquisa Cognitiva do Azure com o PowerShell
> [!div class="op_single_selector"]
> * [Portal](search-manage.md)
> * [PowerShell](search-manage-powershell.md)
> * [REST API](/rest/api/searchmanagement/)
> * [SDK .NET](/dotnet/api/microsoft.azure.management.search)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

Você pode executar os cmdlets e scripts do PowerShell no Windows, Linux ou no [Azure cloud Shell](../cloud-shell/overview.md) para criar e configurar o pesquisa cognitiva do Azure. O módulo **AZ. Search** estende [Azure PowerShell](/powershell/) com paridade total para as [APIs REST de gerenciamento de pesquisa](/rest/api/searchmanagement) e a capacidade de executar as seguintes tarefas:

> [!div class="checklist"]
> * [Listar serviços de pesquisa em uma assinatura](#list-search-services)
> * [Informações de serviço de retorno](#get-search-service-information)
> * [Criar ou excluir um serviço](#create-or-delete-a-service)
> * [Regenerar chaves de API de administrador](#regenerate-admin-keys)
> * [Criar ou excluir as chaves de API de consulta](#create-or-delete-query-keys)
> * [Escalar ou reduzir verticalmente com réplicas e partições](#scale-replicas-and-partitions)

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
ResourceId        : /subscriptions/<alpha-numeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
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
CommandType     Name                                Version    Source
-----------     ----                                -------    ------
Cmdlet          Get-AzSearchAdminKeyPair            0.7.1      Az.Search
Cmdlet          Get-AzSearchQueryKey                0.7.1      Az.Search
Cmdlet          Get-AzSearchService                 0.7.1      Az.Search
Cmdlet          New-AzSearchAdminKey                0.7.1      Az.Search
Cmdlet          New-AzSearchQueryKey                0.7.1      Az.Search
Cmdlet          New-AzSearchService                 0.7.1      Az.Search
Cmdlet          Remove-AzSearchQueryKey             0.7.1      Az.Search
Cmdlet          Remove-AzSearchService              0.7.1      Az.Search
Cmdlet          Set-AzSearchService                 0.7.1      Az.Search
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
New-AzSearchService -ResourceGroupName "demo-westus" -Name "my-demo-searchapp" -Sku "Standard" -Location "West US" -PartitionCount 3 -ReplicaCount 3
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

## <a name="next-steps"></a>Próximas etapas

Crie um [índice](search-what-is-an-index.md), [consulte um índice](search-query-overview.md) usando o portal, as APIs REST ou o SDK do .net.

* [Criar um índice da Pesquisa Cognitiva do Azure no portal do Azure](search-get-started-portal.md)
* [Configurar um indexador para carregar dados de outros serviços](search-indexer-overview.md)
* [Consultar um índice de Pesquisa Cognitiva do Azure usando o Search Explorer no portal do Azure](search-explorer.md)
* [Como usar o Azure Pesquisa Cognitiva no .NET](search-howto-dotnet-sdk.md)