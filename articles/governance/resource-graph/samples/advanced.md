---
title: Exemplos de consulta avançada
description: Use o Azure Resource Graph para executar algumas consultas avançadas, incluindo o trabalho com colunas, a listagem das marcas usadas e a correspondência de recursos com expressões regulares.
ms.date: 03/23/2021
ms.topic: sample
ms.openlocfilehash: c6a140b0392affea252e05d63055232532305c75
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104949848"
---
# <a name="advanced-resource-graph-query-samples"></a>Exemplos de consultas avançadas do Resource Graph

A primeira etapa para consultas de reconhecimento com o Microsoft Azure Resource Graph é uma compreensão básica da [Linguagem de Consulta](../concepts/query-language.md). Se você já não estiver familiarizado com o [Azure Data Explorer](/azure/data-explorer/data-explorer-overview), é recomendável revisar os conceitos básicos para entender como compor solicitações para os recursos que está procurando.

Vamos percorrer as seguintes consultas avançadas:

- [Mostrar a versão de API para cada tipo de recurso](#apiversion)
- [Obter capacidade e tamanho do conjunto de dimensionamento de máquina virtual](#vmss-capacity)
- [Remover colunas dos resultados](#remove-column)
- [Listar todos os nomes de marca](#list-all-tags)
- [Máquinas virtuais correspondidas por regex](#vm-regex)
- [Listar Cosmos DB com locais de gravação específicos](#mvexpand-cosmosdb)
- [Cofres de chaves com o nome da assinatura](#join)
- [Listar Bancos de Dados SQL e seus pools elásticos](#join-sql)
- [Listar máquinas virtuais com seu adaptador de rede e IP público](#join-vmpip)
- [Listar todas as extensões instaladas em uma máquina virtual](#join-vmextension)
- [Localizar contas de armazenamento com uma tag específica no grupo de recursos](#join-findstoragetag)
- [Combinar resultados de duas consultas em um único resultado](#unionresults)
- [Resumir a máquina virtual pela propriedade estendida dos estados de energia](#vm-powerstate)
- [Contagem de atribuições de Configuração de Convidado que não estão em conformidade](#count-gcnoncompliant)
- [Consultar detalhes de relatórios de atribuição de Configuração de Convidado](#query-gcreports)
- [Localizar todos os motivos pelos quais um computador não está em conformidade para atribuições de Configuração de Convidado](#query-gcmachinedetails)

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a name="language-support"></a>Suporte ao idioma

A CLI do Azure (por meio de uma extensão) e o Azure PowerShell (por meio de um módulo) suportam o Gráfico de Recursos do Azure. Antes de executar qualquer uma das consultas a seguir, verifique se seu ambiente está preparado. Veja [CLI do Azure](../first-query-azurecli.md#add-the-resource-graph-extension) e [Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module) para obter as etapas instalar e validar o ambiente shell de escolha.

## <a name="show-resource-types-and-api-versions"></a><a name="apiversion"></a>Mostrar tipos de recursos e versões de API

O Resource Graph usa principalmente a versão mais recente que não é de versão prévia da API de um provedor de recursos para as propriedades de recurso de `GET` durante uma atualização. Em alguns casos, a versão da API usada foi substituída para fornecer propriedades mais atuais ou amplamente usadas nos resultados. A seguinte consulta fornece detalhes da versão da API usada para coletar as propriedades em cada tipo de recurso:

```kusto
Resources
| distinct type, apiVersion
| where isnotnull(apiVersion)
| order by type asc
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | distinct type, apiVersion | where isnotnull(apiVersion) | order by type asc"
```

# <a name="azure-powershell"></a>[PowerShell do Azure](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | distinct type, apiVersion | where isnotnull(apiVersion) | order by type asc"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Experimente usar esta consulta no Azure Resource Graph Explorer:

- Portal do Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20distinct%20type%2C%20apiVersion%0D%0A%7C%20where%20isnotnull%28apiVersion%29%0D%0A%7C%20order%20by%20type%20asc" target="_blank">portal.azure.com</a>
- Portal do Azure Government: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20distinct%20type%2C%20apiVersion%0D%0A%7C%20where%20isnotnull%28apiVersion%29%0D%0A%7C%20order%20by%20type%20asc" target="_blank">portal.azure.us</a>
- Portal do Azure China 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20distinct%20type%2C%20apiVersion%0D%0A%7C%20where%20isnotnull%28apiVersion%29%0D%0A%7C%20order%20by%20type%20asc" target="_blank">portal.azure.cn</a>

---

## <a name="get-virtual-machine-scale-set-capacity-and-size"></a><a name="vmss-capacity"></a>Obter capacidade e tamanho do conjunto de dimensionamento de máquina virtual

Essa consulta procura por recursos VMSS e obtém os diversos detalhes, incluindo o tamanho da máquina virtual e a capacidade do conjunto de dimensionamento. A consulta usa a função `toint()` para converter a capacidade em um número para que ela possa ser classificada. Por fim, as colunas são renomeadas para as propriedades nomeadas personalizadas.

```kusto
Resources
| where type=~ 'microsoft.compute/virtualmachinescalesets'
| where name contains 'contoso'
| project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name
| order by Capacity desc
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type=~ 'microsoft.compute/virtualmachinescalesets' | where name contains 'contoso' | project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name | order by Capacity desc"
```

# <a name="azure-powershell"></a>[PowerShell do Azure](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type=~ 'microsoft.compute/virtualmachinescalesets' | where name contains 'contoso' | project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name | order by Capacity desc"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Experimente usar esta consulta no Azure Resource Graph Explorer:

- Portal do Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%3D~%20%27microsoft.compute%2Fvirtualmachinescalesets%27%0D%0A%7C%20where%20name%20contains%20%27contoso%27%0D%0A%7C%20project%20subscriptionId%2C%20name%2C%20location%2C%20resourceGroup%2C%20Capacity%20%3D%20toint%28sku.capacity%29%2C%20Tier%20%3D%20sku.name%0D%0A%7C%20order%20by%20Capacity%20desc" target="_blank">portal.azure.com</a>
- Portal do Azure Government: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%3D~%20%27microsoft.compute%2Fvirtualmachinescalesets%27%0D%0A%7C%20where%20name%20contains%20%27contoso%27%0D%0A%7C%20project%20subscriptionId%2C%20name%2C%20location%2C%20resourceGroup%2C%20Capacity%20%3D%20toint%28sku.capacity%29%2C%20Tier%20%3D%20sku.name%0D%0A%7C%20order%20by%20Capacity%20desc" target="_blank">portal.azure.us</a>
- Portal do Azure China 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%3D~%20%27microsoft.compute%2Fvirtualmachinescalesets%27%0D%0A%7C%20where%20name%20contains%20%27contoso%27%0D%0A%7C%20project%20subscriptionId%2C%20name%2C%20location%2C%20resourceGroup%2C%20Capacity%20%3D%20toint%28sku.capacity%29%2C%20Tier%20%3D%20sku.name%0D%0A%7C%20order%20by%20Capacity%20desc" target="_blank">portal.azure.cn</a>

---

## <a name="remove-columns-from-results"></a><a name="remove-column"></a>Remover colunas dos resultados

A consulta a seguir usa `summarize` para contar recursos por assinatura, `join` para combiná-lo com detalhes da assinatura da tabela _ResourceContainers_ e, em seguida, `project-away` para remover algumas colunas.

```kusto
Resources
| summarize resourceCount=count() by subscriptionId
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| project-away subscriptionId, subscriptionId1
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | summarize resourceCount=count() by subscriptionId | join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId| project-away subscriptionId, subscriptionId1"
```

# <a name="azure-powershell"></a>[PowerShell do Azure](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | summarize resourceCount=count() by subscriptionId | join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId| project-away subscriptionId, subscriptionId1"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Experimente usar esta consulta no Azure Resource Graph Explorer:

- Portal do Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20summarize%20resourceCount%3Dcount%28%29%20by%20subscriptionId%0D%0A%7C%20join%20%28ResourceContainers%20%7C%20where%20type%3D%3D%27microsoft.resources%2Fsubscriptions%27%20%7C%20project%20SubName%3Dname%2C%20subscriptionId%29%20on%20subscriptionId%0D%0A%7C%20project-away%20subscriptionId%2C%20subscriptionId1" target="_blank">portal.azure.com</a>
- Portal do Azure Government: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20summarize%20resourceCount%3Dcount%28%29%20by%20subscriptionId%0D%0A%7C%20join%20%28ResourceContainers%20%7C%20where%20type%3D%3D%27microsoft.resources%2Fsubscriptions%27%20%7C%20project%20SubName%3Dname%2C%20subscriptionId%29%20on%20subscriptionId%0D%0A%7C%20project-away%20subscriptionId%2C%20subscriptionId1" target="_blank">portal.azure.us</a>
- Portal do Azure China 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20summarize%20resourceCount%3Dcount%28%29%20by%20subscriptionId%0D%0A%7C%20join%20%28ResourceContainers%20%7C%20where%20type%3D%3D%27microsoft.resources%2Fsubscriptions%27%20%7C%20project%20SubName%3Dname%2C%20subscriptionId%29%20on%20subscriptionId%0D%0A%7C%20project-away%20subscriptionId%2C%20subscriptionId1" target="_blank">portal.azure.cn</a>

---

## <a name="list-all-tag-names"></a><a name="list-all-tags"></a>Listar todos os nomes de marca

Essa consulta começa com a marca e cria um objeto JSON listando todos os nomes de marca exclusivos e seus tipos correspondentes.

```kusto
Resources
| project tags
| summarize buildschema(tags)
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | project tags | summarize buildschema(tags)"
```

# <a name="azure-powershell"></a>[PowerShell do Azure](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project tags | summarize buildschema(tags)"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Experimente usar esta consulta no Azure Resource Graph Explorer:

- Portal do Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20project%20tags%0D%0A%7C%20summarize%20buildschema%28tags%29" target="_blank">portal.azure.com</a>
- Portal do Azure Government: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20project%20tags%0D%0A%7C%20summarize%20buildschema%28tags%29" target="_blank">portal.azure.us</a>
- Portal do Azure China 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20project%20tags%0D%0A%7C%20summarize%20buildschema%28tags%29" target="_blank">portal.azure.cn</a>

---

## <a name="virtual-machines-matched-by-regex"></a><a name="vm-regex"></a>Máquinas virtuais correspondidas por regex

Essa consulta procura por máquinas virtuais que correspondem a um [expressão regular](/dotnet/standard/base-types/regular-expression-language-quick-reference) (conhecida como _regex_). O **corresponde ao regex \@** nos permite definir o regex para correspondência, que é `^Contoso(.*)[0-9]+$`.
Essa definição de regex é explicada como:

- `^` - a correspondência deve começar no início da cadeia de caracteres.
- `Contoso` - a cadeia de caracteres com diferenciação de maiúsculas e minúsculas.
- `(.*)` - uma correspondência de subexpressão:
  - `.` - corresponde a qualquer caractere único (exceto uma nova linha).
  - `*` - corresponde ao elemento anterior nenhuma ou mais vezes.
- `[0-9]` - correspondência de grupo de caracteres para os números de 0 a 9.
- `+` - corresponde ao elemento anterior uma ou mais vezes.
- `$` - a correspondência do elemento anterior deve ocorrer no final da cadeia de caracteres.

Após a correspondência por nome, a consulta projeta o nome e ordena por nome em ordem crescente.

```kusto
Resources
| where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$'
| project name
| order by name asc
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$' | project name | order by name asc"
```

# <a name="azure-powershell"></a>[PowerShell do Azure](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$' | project name | order by name asc"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Experimente usar esta consulta no Azure Resource Graph Explorer:

- Portal do Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27microsoft.compute%2Fvirtualmachines%27%20and%20name%20matches%20regex%20%40%27%5EContoso%28.%2A%29%5B0-9%5D%2B%24%27%0D%0A%7C%20project%20name%0D%0A%7C%20order%20by%20name%20asc" target="_blank">portal.azure.com</a>
- Portal do Azure Government: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27microsoft.compute%2Fvirtualmachines%27%20and%20name%20matches%20regex%20%40%27%5EContoso%28.%2A%29%5B0-9%5D%2B%24%27%0D%0A%7C%20project%20name%0D%0A%7C%20order%20by%20name%20asc" target="_blank">portal.azure.us</a>
- Portal do Azure China 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27microsoft.compute%2Fvirtualmachines%27%20and%20name%20matches%20regex%20%40%27%5EContoso%28.%2A%29%5B0-9%5D%2B%24%27%0D%0A%7C%20project%20name%0D%0A%7C%20order%20by%20name%20asc" target="_blank">portal.azure.cn</a>

---

## <a name="list-cosmos-db-with-specific-write-locations"></a><a name="mvexpand-cosmosdb"></a>Listar Cosmos DB com locais de gravação específicos

A consulta a seguir limita-se aos recursos do Cosmos DB, usa `mv-expand` para expandir o recipiente de propriedade para **properties.writeLocations** e projetar campos específicos e limitar os resultados ainda mais para os valores **properties.writeLocations.locationName** correspondentes a “Leste dos EUA” ou “Oeste dos EUA”.

```kusto
Resources
| where type =~ 'microsoft.documentdb/databaseaccounts'
| project id, name, writeLocations = (properties.writeLocations)
| mv-expand writeLocations
| project id, name, writeLocation = tostring(writeLocations.locationName)
| where writeLocation in ('East US', 'West US')
| summarize by id, name
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.documentdb/databaseaccounts' | project id, name, writeLocations = (properties.writeLocations) | mv-expand writeLocations | project id, name, writeLocation = tostring(writeLocations.locationName) | where writeLocation in ('East US', 'West US') | summarize by id, name"
```

# <a name="azure-powershell"></a>[PowerShell do Azure](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.documentdb/databaseaccounts' | project id, name, writeLocations = (properties.writeLocations) | mv-expand writeLocations | project id, name, writeLocation = tostring(writeLocations.locationName) | where writeLocation in ('East US', 'West US') | summarize by id, name"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Experimente usar esta consulta no Azure Resource Graph Explorer:

- Portal do Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27microsoft.documentdb%2Fdatabaseaccounts%27%0D%0A%7C%20project%20id%2C%20name%2C%20writeLocations%20%3D%20%28properties.writeLocations%29%0D%0A%7C%20mv-expand%20writeLocations%0D%0A%7C%20project%20id%2C%20name%2C%20writeLocation%20%3D%20tostring%28writeLocations.locationName%29%0D%0A%7C%20where%20writeLocation%20in%20%28%27East%20US%27%2C%20%27West%20US%27%29%0D%0A%7C%20summarize%20by%20id%2C%20name" target="_blank">portal.azure.com</a>
- Portal do Azure Government: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27microsoft.documentdb%2Fdatabaseaccounts%27%0D%0A%7C%20project%20id%2C%20name%2C%20writeLocations%20%3D%20%28properties.writeLocations%29%0D%0A%7C%20mv-expand%20writeLocations%0D%0A%7C%20project%20id%2C%20name%2C%20writeLocation%20%3D%20tostring%28writeLocations.locationName%29%0D%0A%7C%20where%20writeLocation%20in%20%28%27East%20US%27%2C%20%27West%20US%27%29%0D%0A%7C%20summarize%20by%20id%2C%20name" target="_blank">portal.azure.us</a>
- Portal do Azure China 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27microsoft.documentdb%2Fdatabaseaccounts%27%0D%0A%7C%20project%20id%2C%20name%2C%20writeLocations%20%3D%20%28properties.writeLocations%29%0D%0A%7C%20mv-expand%20writeLocations%0D%0A%7C%20project%20id%2C%20name%2C%20writeLocation%20%3D%20tostring%28writeLocations.locationName%29%0D%0A%7C%20where%20writeLocation%20in%20%28%27East%20US%27%2C%20%27West%20US%27%29%0D%0A%7C%20summarize%20by%20id%2C%20name" target="_blank">portal.azure.cn</a>

---

## <a name="key-vaults-with-subscription-name"></a><a name="join"></a>Cofres de chaves com o nome da assinatura

A consulta a seguir mostra um uso complexo de `join` com o **tipo** como _leftouter_. A consulta limita a tabela ingressada aos recursos de assinaturas e com `project` para incluir apenas o campo original _subscriptionId_ e o campo _name_ renomeado para _SubName_. A renomeação do campo evita que `join` o adicione como _name1_, uma vez que o campo já existe em _resources_. A tabela original é filtrada com `where` e o `project` a seguir inclui colunas das duas tabelas. O resultado da consulta é que todos os cofres de chaves exibem o tipo, o nome do cofre de chaves e o nome da assinatura em que ele está.

```kusto
Resources
| join kind=leftouter (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| where type == 'microsoft.keyvault/vaults'
| project type, name, SubName
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | join kind=leftouter (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId | where type == 'microsoft.keyvault/vaults' | project type, name, SubName"
```

# <a name="azure-powershell"></a>[PowerShell do Azure](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | join kind=leftouter (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId | where type == 'microsoft.keyvault/vaults' | project type, name, SubName"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Experimente usar esta consulta no Azure Resource Graph Explorer:

- Portal do Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20join%20kind%3Dleftouter%20%28ResourceContainers%20%7C%20where%20type%3D%3D%27microsoft.resources%2Fsubscriptions%27%20%7C%20project%20SubName%3Dname%2C%20subscriptionId%29%20on%20subscriptionId%0D%0A%7C%20where%20type%20%3D%3D%20%27microsoft.keyvault%2Fvaults%27%0D%0A%7C%20project%20type%2C%20name%2C%20SubName" target="_blank">portal.azure.com</a>
- Portal do Azure Government: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20join%20kind%3Dleftouter%20%28ResourceContainers%20%7C%20where%20type%3D%3D%27microsoft.resources%2Fsubscriptions%27%20%7C%20project%20SubName%3Dname%2C%20subscriptionId%29%20on%20subscriptionId%0D%0A%7C%20where%20type%20%3D%3D%20%27microsoft.keyvault%2Fvaults%27%0D%0A%7C%20project%20type%2C%20name%2C%20SubName" target="_blank">portal.azure.us</a>
- Portal do Azure China 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20join%20kind%3Dleftouter%20%28ResourceContainers%20%7C%20where%20type%3D%3D%27microsoft.resources%2Fsubscriptions%27%20%7C%20project%20SubName%3Dname%2C%20subscriptionId%29%20on%20subscriptionId%0D%0A%7C%20where%20type%20%3D%3D%20%27microsoft.keyvault%2Fvaults%27%0D%0A%7C%20project%20type%2C%20name%2C%20SubName" target="_blank">portal.azure.cn</a>

---

## <a name="list-sql-databases-and-their-elastic-pools"></a><a name="join-sql"></a>Listar Bancos de Dados SQL e seus pools elásticos

A consulta a seguir usará **leftouter** `join` para reunir recursos do Banco de Dados SQL e os pools elásticos relacionados deles, se tiverem algum.

```kusto
Resources
| where type =~ 'microsoft.sql/servers/databases'
| project databaseId = id, databaseName = name, elasticPoolId = tolower(tostring(properties.elasticPoolId))
| join kind=leftouter (
    Resources
    | where type =~ 'microsoft.sql/servers/elasticpools'
    | project elasticPoolId = tolower(id), elasticPoolName = name, elasticPoolState = properties.state)
on elasticPoolId
| project-away elasticPoolId1
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.sql/servers/databases' | project databaseId = id, databaseName = name, elasticPoolId = tolower(tostring(properties.elasticPoolId)) | join kind=leftouter ( Resources | where type =~ 'microsoft.sql/servers/elasticpools' | project elasticPoolId = tolower(id), elasticPoolName = name, elasticPoolState = properties.state) on elasticPoolId | project-away elasticPoolId1"
```

# <a name="azure-powershell"></a>[PowerShell do Azure](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.sql/servers/databases' | project databaseId = id, databaseName = name, elasticPoolId = tolower(tostring(properties.elasticPoolId)) | join kind=leftouter ( Resources | where type =~ 'microsoft.sql/servers/elasticpools' | project elasticPoolId = tolower(id), elasticPoolName = name, elasticPoolState = properties.state) on elasticPoolId | project-away elasticPoolId1"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Experimente usar esta consulta no Azure Resource Graph Explorer:

- Portal do Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27microsoft.sql%2Fservers%2Fdatabases%27%0D%0A%7C%20project%20databaseId%20%3D%20id%2C%20databaseName%20%3D%20name%2C%20elasticPoolId%20%3D%20tolower%28tostring%28properties.elasticPoolId%29%29%0D%0A%7C%20join%20kind%3Dleftouter%20%28%0D%0A%20%20%20%20Resources%0D%0A%20%20%20%20%7C%20where%20type%20%3D~%20%27microsoft.sql%2Fservers%2Felasticpools%27%0D%0A%20%20%20%20%7C%20project%20elasticPoolId%20%3D%20tolower%28id%29%2C%20elasticPoolName%20%3D%20name%2C%20elasticPoolState%20%3D%20properties.state%29%0D%0Aon%20elasticPoolId%0D%0A%7C%20project-away%20elasticPoolId1" target="_blank">portal.azure.com</a>
- Portal do Azure Government: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27microsoft.sql%2Fservers%2Fdatabases%27%0D%0A%7C%20project%20databaseId%20%3D%20id%2C%20databaseName%20%3D%20name%2C%20elasticPoolId%20%3D%20tolower%28tostring%28properties.elasticPoolId%29%29%0D%0A%7C%20join%20kind%3Dleftouter%20%28%0D%0A%20%20%20%20Resources%0D%0A%20%20%20%20%7C%20where%20type%20%3D~%20%27microsoft.sql%2Fservers%2Felasticpools%27%0D%0A%20%20%20%20%7C%20project%20elasticPoolId%20%3D%20tolower%28id%29%2C%20elasticPoolName%20%3D%20name%2C%20elasticPoolState%20%3D%20properties.state%29%0D%0Aon%20elasticPoolId%0D%0A%7C%20project-away%20elasticPoolId1" target="_blank">portal.azure.us</a>
- Portal do Azure China 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27microsoft.sql%2Fservers%2Fdatabases%27%0D%0A%7C%20project%20databaseId%20%3D%20id%2C%20databaseName%20%3D%20name%2C%20elasticPoolId%20%3D%20tolower%28tostring%28properties.elasticPoolId%29%29%0D%0A%7C%20join%20kind%3Dleftouter%20%28%0D%0A%20%20%20%20Resources%0D%0A%20%20%20%20%7C%20where%20type%20%3D~%20%27microsoft.sql%2Fservers%2Felasticpools%27%0D%0A%20%20%20%20%7C%20project%20elasticPoolId%20%3D%20tolower%28id%29%2C%20elasticPoolName%20%3D%20name%2C%20elasticPoolState%20%3D%20properties.state%29%0D%0Aon%20elasticPoolId%0D%0A%7C%20project-away%20elasticPoolId1" target="_blank">portal.azure.cn</a>

---

## <a name="list-virtual-machines-with-their-network-interface-and-public-ip"></a><a name="join-vmpip"></a>Listar máquinas virtuais com seu adaptador de rede e IP público

Essa consulta usa os comandos **leftouter** `join` para reunir máquinas virtuais criadas com o modelo de implantação do Resource Manager, os adaptadores de rede relacionados deles e quaisquer endereços IP públicos relacionados a esses adaptadores de rede.

```kusto
Resources
| where type =~ 'microsoft.compute/virtualmachines'
| extend nics=array_length(properties.networkProfile.networkInterfaces) 
| mv-expand nic=properties.networkProfile.networkInterfaces 
| where nics == 1 or nic.properties.primary =~ 'true' or isempty(nic) 
| project vmId = id, vmName = name, vmSize=tostring(properties.hardwareProfile.vmSize), nicId = tostring(nic.id) 
| join kind=leftouter (
    Resources
    | where type =~ 'microsoft.network/networkinterfaces'
    | extend ipConfigsCount=array_length(properties.ipConfigurations) 
    | mv-expand ipconfig=properties.ipConfigurations 
    | where ipConfigsCount == 1 or ipconfig.properties.primary =~ 'true'
    | project nicId = id, publicIpId = tostring(ipconfig.properties.publicIPAddress.id))
on nicId
| project-away nicId1
| summarize by vmId, vmName, vmSize, nicId, publicIpId
| join kind=leftouter (
    Resources
    | where type =~ 'microsoft.network/publicipaddresses'
    | project publicIpId = id, publicIpAddress = properties.ipAddress)
on publicIpId
| project-away publicIpId1
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.compute/virtualmachines' | extend nics=array_length(properties.networkProfile.networkInterfaces) | mv-expand nic=properties.networkProfile.networkInterfaces | where nics == 1 or nic.properties.primary =~ 'true' or isempty(nic) | project vmId = id, vmName = name, vmSize=tostring(properties.hardwareProfile.vmSize), nicId = tostring(nic.id) | join kind=leftouter ( Resources | where type =~ 'microsoft.network/networkinterfaces' | extend ipConfigsCount=array_length(properties.ipConfigurations) | mv-expand ipconfig=properties.ipConfigurations | where ipConfigsCount == 1 or ipconfig.properties.primary =~ 'true' | project nicId = id, publicIpId = tostring(ipconfig.properties.publicIPAddress.id)) on nicId | project-away nicId1 | summarize by vmId, vmName, vmSize, nicId, publicIpId | join kind=leftouter ( Resources | where type =~ 'microsoft.network/publicipaddresses' | project publicIpId = id, publicIpAddress = properties.ipAddress) on publicIpId | project-away publicIpId1"
```

# <a name="azure-powershell"></a>[PowerShell do Azure](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.compute/virtualmachines' | extend nics=array_length(properties.networkProfile.networkInterfaces) | mv-expand nic=properties.networkProfile.networkInterfaces | where nics == 1 or nic.properties.primary =~ 'true' or isempty(nic) | project vmId = id, vmName = name, vmSize=tostring(properties.hardwareProfile.vmSize), nicId = tostring(nic.id) | join kind=leftouter ( Resources | where type =~ 'microsoft.network/networkinterfaces' | extend ipConfigsCount=array_length(properties.ipConfigurations) | mv-expand ipconfig=properties.ipConfigurations | where ipConfigsCount == 1 or ipconfig.properties.primary =~ 'true' | project nicId = id, publicIpId = tostring(ipconfig.properties.publicIPAddress.id)) on nicId | project-away nicId1 | summarize by vmId, vmName, vmSize, nicId, publicIpId | join kind=leftouter ( Resources | where type =~ 'microsoft.network/publicipaddresses' | project publicIpId = id, publicIpAddress = properties.ipAddress) on publicIpId | project-away publicIpId1"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Experimente usar esta consulta no Azure Resource Graph Explorer:

- Portal do Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27microsoft.compute%2Fvirtualmachines%27%0D%0A%7C%20extend%20nics%3Darray_length%28properties.networkProfile.networkInterfaces%29%20%0D%0A%7C%20mv-expand%20nic%3Dproperties.networkProfile.networkInterfaces%20%0D%0A%7C%20where%20nics%20%3D%3D%201%20or%20nic.properties.primary%20%3D~%20%27true%27%20or%20isempty%28nic%29%20%0D%0A%7C%20project%20vmId%20%3D%20id%2C%20vmName%20%3D%20name%2C%20vmSize%3Dtostring%28properties.hardwareProfile.vmSize%29%2C%20nicId%20%3D%20tostring%28nic.id%29%20%0D%0A%7C%20join%20kind%3Dleftouter%20%28%0D%0A%20%20%20%20Resources%0D%0A%20%20%20%20%7C%20where%20type%20%3D~%20%27microsoft.network%2Fnetworkinterfaces%27%0D%0A%20%20%20%20%7C%20extend%20ipConfigsCount%3Darray_length%28properties.ipConfigurations%29%20%0D%0A%20%20%20%20%7C%20mv-expand%20ipconfig%3Dproperties.ipConfigurations%20%0D%0A%20%20%20%20%7C%20where%20ipConfigsCount%20%3D%3D%201%20or%20ipconfig.properties.primary%20%3D~%20%27true%27%0D%0A%20%20%20%20%7C%20project%20nicId%20%3D%20id%2C%20publicIpId%20%3D%20tostring%28ipconfig.properties.publicIPAddress.id%29%29%0D%0Aon%20nicId%0D%0A%7C%20project-away%20nicId1%0D%0A%7C%20summarize%20by%20vmId%2C%20vmName%2C%20vmSize%2C%20nicId%2C%20publicIpId%0D%0A%7C%20join%20kind%3Dleftouter%20%28%0D%0A%20%20%20%20Resources%0D%0A%20%20%20%20%7C%20where%20type%20%3D~%20%27microsoft.network%2Fpublicipaddresses%27%0D%0A%20%20%20%20%7C%20project%20publicIpId%20%3D%20id%2C%20publicIpAddress%20%3D%20properties.ipAddress%29%0D%0Aon%20publicIpId%0D%0A%7C%20project-away%20publicIpId1" target="_blank">portal.azure.com</a>
- Portal do Azure Government: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27microsoft.compute%2Fvirtualmachines%27%0D%0A%7C%20extend%20nics%3Darray_length%28properties.networkProfile.networkInterfaces%29%20%0D%0A%7C%20mv-expand%20nic%3Dproperties.networkProfile.networkInterfaces%20%0D%0A%7C%20where%20nics%20%3D%3D%201%20or%20nic.properties.primary%20%3D~%20%27true%27%20or%20isempty%28nic%29%20%0D%0A%7C%20project%20vmId%20%3D%20id%2C%20vmName%20%3D%20name%2C%20vmSize%3Dtostring%28properties.hardwareProfile.vmSize%29%2C%20nicId%20%3D%20tostring%28nic.id%29%20%0D%0A%7C%20join%20kind%3Dleftouter%20%28%0D%0A%20%20%20%20Resources%0D%0A%20%20%20%20%7C%20where%20type%20%3D~%20%27microsoft.network%2Fnetworkinterfaces%27%0D%0A%20%20%20%20%7C%20extend%20ipConfigsCount%3Darray_length%28properties.ipConfigurations%29%20%0D%0A%20%20%20%20%7C%20mv-expand%20ipconfig%3Dproperties.ipConfigurations%20%0D%0A%20%20%20%20%7C%20where%20ipConfigsCount%20%3D%3D%201%20or%20ipconfig.properties.primary%20%3D~%20%27true%27%0D%0A%20%20%20%20%7C%20project%20nicId%20%3D%20id%2C%20publicIpId%20%3D%20tostring%28ipconfig.properties.publicIPAddress.id%29%29%0D%0Aon%20nicId%0D%0A%7C%20project-away%20nicId1%0D%0A%7C%20summarize%20by%20vmId%2C%20vmName%2C%20vmSize%2C%20nicId%2C%20publicIpId%0D%0A%7C%20join%20kind%3Dleftouter%20%28%0D%0A%20%20%20%20Resources%0D%0A%20%20%20%20%7C%20where%20type%20%3D~%20%27microsoft.network%2Fpublicipaddresses%27%0D%0A%20%20%20%20%7C%20project%20publicIpId%20%3D%20id%2C%20publicIpAddress%20%3D%20properties.ipAddress%29%0D%0Aon%20publicIpId%0D%0A%7C%20project-away%20publicIpId1" target="_blank">portal.azure.us</a>
- Portal do Azure China 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27microsoft.compute%2Fvirtualmachines%27%0D%0A%7C%20extend%20nics%3Darray_length%28properties.networkProfile.networkInterfaces%29%20%0D%0A%7C%20mv-expand%20nic%3Dproperties.networkProfile.networkInterfaces%20%0D%0A%7C%20where%20nics%20%3D%3D%201%20or%20nic.properties.primary%20%3D~%20%27true%27%20or%20isempty%28nic%29%20%0D%0A%7C%20project%20vmId%20%3D%20id%2C%20vmName%20%3D%20name%2C%20vmSize%3Dtostring%28properties.hardwareProfile.vmSize%29%2C%20nicId%20%3D%20tostring%28nic.id%29%20%0D%0A%7C%20join%20kind%3Dleftouter%20%28%0D%0A%20%20%20%20Resources%0D%0A%20%20%20%20%7C%20where%20type%20%3D~%20%27microsoft.network%2Fnetworkinterfaces%27%0D%0A%20%20%20%20%7C%20extend%20ipConfigsCount%3Darray_length%28properties.ipConfigurations%29%20%0D%0A%20%20%20%20%7C%20mv-expand%20ipconfig%3Dproperties.ipConfigurations%20%0D%0A%20%20%20%20%7C%20where%20ipConfigsCount%20%3D%3D%201%20or%20ipconfig.properties.primary%20%3D~%20%27true%27%0D%0A%20%20%20%20%7C%20project%20nicId%20%3D%20id%2C%20publicIpId%20%3D%20tostring%28ipconfig.properties.publicIPAddress.id%29%29%0D%0Aon%20nicId%0D%0A%7C%20project-away%20nicId1%0D%0A%7C%20summarize%20by%20vmId%2C%20vmName%2C%20vmSize%2C%20nicId%2C%20publicIpId%0D%0A%7C%20join%20kind%3Dleftouter%20%28%0D%0A%20%20%20%20Resources%0D%0A%20%20%20%20%7C%20where%20type%20%3D~%20%27microsoft.network%2Fpublicipaddresses%27%0D%0A%20%20%20%20%7C%20project%20publicIpId%20%3D%20id%2C%20publicIpAddress%20%3D%20properties.ipAddress%29%0D%0Aon%20publicIpId%0D%0A%7C%20project-away%20publicIpId1" target="_blank">portal.azure.cn</a>

---

## <a name="list-all-extensions-installed-on-a-virtual-machine"></a><a name="join-vmextension"></a>Listar todas as extensões instaladas em uma máquina virtual

Primeiro, essa consulta usa `extend` no tipo de recurso das máquinas virtuais para obter a ID em letras maiúsculas (`toupper()`), obter o nome e o tipo do sistema operacional e obter o tamanho da máquina virtual.
Obter a ID do recurso em letras maiúsculas é uma boa maneira de se preparar para unir-se a outra propriedade. Em seguida, a consulta usa `join` com **kind** como _leftouter_ para obter as extensões de máquina virtual, fazendo a correspondência com um `substring` em letra maiúscula da ID da extensão. A parte da ID antes de "/extensions/\<ExtensionName\>" tem o mesmo formato que a ID da máquina virtual, portanto, usamos essa propriedade para o `join`. `summarize` é usado com `make_list` no nome da extensão da máquina virtual para combinar o nome de cada extensão em que _id_, _OSName_, _OSType_ e _VMSize_ são os mesmos em uma só propriedade de matriz. Por fim, nós fazemos o `order by` do _OSName_ em letras minúsculas com **asc**. Por padrão, `order by` é decrescente.

```kusto
Resources
| where type == 'microsoft.compute/virtualmachines'
| extend
    JoinID = toupper(id),
    OSName = tostring(properties.osProfile.computerName),
    OSType = tostring(properties.storageProfile.osDisk.osType),
    VMSize = tostring(properties.hardwareProfile.vmSize)
| join kind=leftouter(
    Resources
    | where type == 'microsoft.compute/virtualmachines/extensions'
    | extend 
        VMId = toupper(substring(id, 0, indexof(id, '/extensions'))),
        ExtensionName = name
) on $left.JoinID == $right.VMId
| summarize Extensions = make_list(ExtensionName) by id, OSName, OSType, VMSize
| order by tolower(OSName) asc
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type == 'microsoft.compute/virtualmachines' | extend JoinID = toupper(id), OSName = tostring(properties.osProfile.computerName), OSType = tostring(properties.storageProfile.osDisk.osType), VMSize = tostring(properties.hardwareProfile.vmSize) | join kind=leftouter( Resources | where type == 'microsoft.compute/virtualmachines/extensions' | extend VMId = toupper(substring(id, 0, indexof(id, '/extensions'))), ExtensionName = name ) on $left.JoinID == $right.VMId | summarize Extensions = make_list(ExtensionName) by id, OSName, OSType, VMSize | order by tolower(OSName) asc"
```

# <a name="azure-powershell"></a>[PowerShell do Azure](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type == 'microsoft.compute/virtualmachines' | extend JoinID = toupper(id), OSName = tostring(properties.osProfile.computerName), OSType = tostring(properties.storageProfile.osDisk.osType), VMSize = tostring(properties.hardwareProfile.vmSize) | join kind=leftouter( Resources | where type == 'microsoft.compute/virtualmachines/extensions' | extend VMId = toupper(substring(id, 0, indexof(id, '/extensions'))), ExtensionName = name ) on $left.JoinID == $right.VMId | summarize Extensions = make_list(ExtensionName) by id, OSName, OSType, VMSize | order by tolower(OSName) asc"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Experimente usar esta consulta no Azure Resource Graph Explorer:

- Portal do Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0A%7C%20where%20type%20%3D%3D%20'microsoft.compute%2Fvirtualmachines'%0A%7C%20extend%0A%20%20%20%20JoinID%20%3D%20toupper(id)%2C%0A%20%20%20%20OSName%20%3D%20tostring(properties.osProfile.computerName)%2C%0A%20%20%20%20OSType%20%3D%20tostring(properties.storageProfile.osDisk.osType)%2C%0A%20%20%20%20VMSize%20%3D%20tostring(properties.hardwareProfile.vmSize)%0A%7C%20join%20kind%3Dleftouter(%0A%20%20%20%20Resources%0A%20%20%20%20%7C%20where%20type%20%3D%3D%20'microsoft.compute%2Fvirtualmachines%2Fextensions'%0A%20%20%20%20%7C%20extend%20%0A%20%20%20%20%20%20%20%20VMId%20%3D%20toupper(substring(id%2C%200%2C%20indexof(id%2C%20'%2Fextensions')))%2C%0A%20%20%20%20%20%20%20%20ExtensionName%20%3D%20name%0A)%20on%20%24left.JoinID%20%3D%3D%20%24right.VMId%0A%7C%20summarize%20Extensions%20%3D%20make_list(ExtensionName)%20by%20id%2C%20OSName%2C%20OSType%2C%20VMSize%0A%7C%20order%20by%20tolower(OSName)%20asc" target="_blank">portal.azure.com</a>
- Portal do Azure Government: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0A%7C%20where%20type%20%3D%3D%20'microsoft.compute%2Fvirtualmachines'%0A%7C%20extend%0A%20%20%20%20JoinID%20%3D%20toupper(id)%2C%0A%20%20%20%20OSName%20%3D%20tostring(properties.osProfile.computerName)%2C%0A%20%20%20%20OSType%20%3D%20tostring(properties.storageProfile.osDisk.osType)%2C%0A%20%20%20%20VMSize%20%3D%20tostring(properties.hardwareProfile.vmSize)%0A%7C%20join%20kind%3Dleftouter(%0A%20%20%20%20Resources%0A%20%20%20%20%7C%20where%20type%20%3D%3D%20'microsoft.compute%2Fvirtualmachines%2Fextensions'%0A%20%20%20%20%7C%20extend%20%0A%20%20%20%20%20%20%20%20VMId%20%3D%20toupper(substring(id%2C%200%2C%20indexof(id%2C%20'%2Fextensions')))%2C%0A%20%20%20%20%20%20%20%20ExtensionName%20%3D%20name%0A)%20on%20%24left.JoinID%20%3D%3D%20%24right.VMId%0A%7C%20summarize%20Extensions%20%3D%20make_list(ExtensionName)%20by%20id%2C%20OSName%2C%20OSType%2C%20VMSize%0A%7C%20order%20by%20tolower(OSName)%20asc" target="_blank">portal.azure.us</a>
- Portal do Azure China 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0A%7C%20where%20type%20%3D%3D%20'microsoft.compute%2Fvirtualmachines'%0A%7C%20extend%0A%20%20%20%20JoinID%20%3D%20toupper(id)%2C%0A%20%20%20%20OSName%20%3D%20tostring(properties.osProfile.computerName)%2C%0A%20%20%20%20OSType%20%3D%20tostring(properties.storageProfile.osDisk.osType)%2C%0A%20%20%20%20VMSize%20%3D%20tostring(properties.hardwareProfile.vmSize)%0A%7C%20join%20kind%3Dleftouter(%0A%20%20%20%20Resources%0A%20%20%20%20%7C%20where%20type%20%3D%3D%20'microsoft.compute%2Fvirtualmachines%2Fextensions'%0A%20%20%20%20%7C%20extend%20%0A%20%20%20%20%20%20%20%20VMId%20%3D%20toupper(substring(id%2C%200%2C%20indexof(id%2C%20'%2Fextensions')))%2C%0A%20%20%20%20%20%20%20%20ExtensionName%20%3D%20name%0A)%20on%20%24left.JoinID%20%3D%3D%20%24right.VMId%0A%7C%20summarize%20Extensions%20%3D%20make_list(ExtensionName)%20by%20id%2C%20OSName%2C%20OSType%2C%20VMSize%0A%7C%20order%20by%20tolower(OSName)%20asc" target="_blank">portal.azure.cn</a>

---

## <a name="find-storage-accounts-with-a-specific-tag-on-the-resource-group"></a><a name="join-findstoragetag"></a>Localizar contas de armazenamento com uma tag específica no grupo de recursos

A consulta a seguir usa **inner** `join` para conectar contas de armazenamento a grupos de recursos que têm um nome e um valor de marca especificados que diferenciam maiúsculas de minúsculas.

```kusto
Resources
| where type =~ 'microsoft.storage/storageaccounts'
| join kind=inner (
    ResourceContainers
    | where type =~ 'microsoft.resources/subscriptions/resourcegroups'
    | where tags['Key1'] =~ 'Value1'
    | project subscriptionId, resourceGroup)
on subscriptionId, resourceGroup
| project-away subscriptionId1, resourceGroup1
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.storage/storageaccounts' | join kind=inner ( ResourceContainers | where type =~ 'microsoft.resources/subscriptions/resourcegroups' | where tags['Key1'] =~ 'Value1' | project subscriptionId, resourceGroup) on subscriptionId, resourceGroup | project-away subscriptionId1, resourceGroup1"
```

# <a name="azure-powershell"></a>[PowerShell do Azure](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.storage/storageaccounts' | join kind=inner ( ResourceContainers | where type =~ 'microsoft.resources/subscriptions/resourcegroups' | where tags['Key1'] =~ 'Value1' | project subscriptionId, resourceGroup) on subscriptionId, resourceGroup | project-away subscriptionId1, resourceGroup1"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Experimente usar esta consulta no Azure Resource Graph Explorer:

- Portal do Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27microsoft.storage%2Fstorageaccounts%27%0D%0A%7C%20join%20kind%3Dinner%20%28%0D%0A%20%20%20%20ResourceContainers%0D%0A%20%20%20%20%7C%20where%20type%20%3D~%20%27microsoft.resources%2Fsubscriptions%2Fresourcegroups%27%0D%0A%20%20%20%20%7C%20where%20tags%5B%27Key1%27%5D%20%3D~%20%27Value1%27%0D%0A%20%20%20%20%7C%20project%20subscriptionId%2C%20resourceGroup%29%0D%0Aon%20subscriptionId%2C%20resourceGroup%0D%0A%7C%20project-away%20subscriptionId1%2C%20resourceGroup1" target="_blank">portal.azure.com</a>
- Portal do Azure Government: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27microsoft.storage%2Fstorageaccounts%27%0D%0A%7C%20join%20kind%3Dinner%20%28%0D%0A%20%20%20%20ResourceContainers%0D%0A%20%20%20%20%7C%20where%20type%20%3D~%20%27microsoft.resources%2Fsubscriptions%2Fresourcegroups%27%0D%0A%20%20%20%20%7C%20where%20tags%5B%27Key1%27%5D%20%3D~%20%27Value1%27%0D%0A%20%20%20%20%7C%20project%20subscriptionId%2C%20resourceGroup%29%0D%0Aon%20subscriptionId%2C%20resourceGroup%0D%0A%7C%20project-away%20subscriptionId1%2C%20resourceGroup1" target="_blank">portal.azure.us</a>
- Portal do Azure China 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27microsoft.storage%2Fstorageaccounts%27%0D%0A%7C%20join%20kind%3Dinner%20%28%0D%0A%20%20%20%20ResourceContainers%0D%0A%20%20%20%20%7C%20where%20type%20%3D~%20%27microsoft.resources%2Fsubscriptions%2Fresourcegroups%27%0D%0A%20%20%20%20%7C%20where%20tags%5B%27Key1%27%5D%20%3D~%20%27Value1%27%0D%0A%20%20%20%20%7C%20project%20subscriptionId%2C%20resourceGroup%29%0D%0Aon%20subscriptionId%2C%20resourceGroup%0D%0A%7C%20project-away%20subscriptionId1%2C%20resourceGroup1" target="_blank">portal.azure.cn</a>

---

Se for necessário procurar um nome e um valor de marca que não diferenciam maiúsculas de minúsculas, use `mv-expand` com o parâmetro **bagexpansion**. Essa consulta usa mais cota do que a consulta anterior, portanto, use `mv-expand` somente se necessário.

```kusto
Resources
| where type =~ 'microsoft.storage/storageaccounts'
| join kind=inner (
    ResourceContainers
    | where type =~ 'microsoft.resources/subscriptions/resourcegroups'
    | mv-expand bagexpansion=array tags
    | where isnotempty(tags)
    | where tags[0] =~ 'key1' and tags[1] =~ 'value1'
    | project subscriptionId, resourceGroup)
on subscriptionId, resourceGroup
| project-away subscriptionId1, resourceGroup1
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.storage/storageaccounts' | join kind=inner ( ResourceContainers | where type =~ 'microsoft.resources/subscriptions/resourcegroups' | mv-expand bagexpansion=array tags | where isnotempty(tags) | where tags[0] =~ 'key1' and tags[1] =~ 'value1' | project subscriptionId, resourceGroup) on subscriptionId, resourceGroup | project-away subscriptionId1, resourceGroup1"
```

# <a name="azure-powershell"></a>[PowerShell do Azure](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.storage/storageaccounts' | join kind=inner ( ResourceContainers | where type =~ 'microsoft.resources/subscriptions/resourcegroups' | mv-expand bagexpansion=array tags | where isnotempty(tags) | where tags[0] =~ 'key1' and tags[1] =~ 'value1' | project subscriptionId, resourceGroup) on subscriptionId, resourceGroup | project-away subscriptionId1, resourceGroup1"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Experimente usar esta consulta no Azure Resource Graph Explorer:

- Portal do Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27microsoft.storage%2Fstorageaccounts%27%0D%0A%7C%20join%20kind%3Dinner%20%28%0D%0A%20%20%20%20ResourceContainers%0D%0A%20%20%20%20%7C%20where%20type%20%3D~%20%27microsoft.resources%2Fsubscriptions%2Fresourcegroups%27%0D%0A%20%20%20%20%7C%20mv-expand%20bagexpansion%3Darray%20tags%0D%0A%20%20%20%20%7C%20where%20isnotempty%28tags%29%0D%0A%20%20%20%20%7C%20where%20tags%5B0%5D%20%3D~%20%27key1%27%20and%20tags%5B1%5D%20%3D~%20%27value1%27%0D%0A%20%20%20%20%7C%20project%20subscriptionId%2C%20resourceGroup%29%0D%0Aon%20subscriptionId%2C%20resourceGroup%0D%0A%7C%20project-away%20subscriptionId1%2C%20resourceGroup1" target="_blank">portal.azure.com</a>
- Portal do Azure Government: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27microsoft.storage%2Fstorageaccounts%27%0D%0A%7C%20join%20kind%3Dinner%20%28%0D%0A%20%20%20%20ResourceContainers%0D%0A%20%20%20%20%7C%20where%20type%20%3D~%20%27microsoft.resources%2Fsubscriptions%2Fresourcegroups%27%0D%0A%20%20%20%20%7C%20mv-expand%20bagexpansion%3Darray%20tags%0D%0A%20%20%20%20%7C%20where%20isnotempty%28tags%29%0D%0A%20%20%20%20%7C%20where%20tags%5B0%5D%20%3D~%20%27key1%27%20and%20tags%5B1%5D%20%3D~%20%27value1%27%0D%0A%20%20%20%20%7C%20project%20subscriptionId%2C%20resourceGroup%29%0D%0Aon%20subscriptionId%2C%20resourceGroup%0D%0A%7C%20project-away%20subscriptionId1%2C%20resourceGroup1" target="_blank">portal.azure.us</a>
- Portal do Azure China 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27microsoft.storage%2Fstorageaccounts%27%0D%0A%7C%20join%20kind%3Dinner%20%28%0D%0A%20%20%20%20ResourceContainers%0D%0A%20%20%20%20%7C%20where%20type%20%3D~%20%27microsoft.resources%2Fsubscriptions%2Fresourcegroups%27%0D%0A%20%20%20%20%7C%20mv-expand%20bagexpansion%3Darray%20tags%0D%0A%20%20%20%20%7C%20where%20isnotempty%28tags%29%0D%0A%20%20%20%20%7C%20where%20tags%5B0%5D%20%3D~%20%27key1%27%20and%20tags%5B1%5D%20%3D~%20%27value1%27%0D%0A%20%20%20%20%7C%20project%20subscriptionId%2C%20resourceGroup%29%0D%0Aon%20subscriptionId%2C%20resourceGroup%0D%0A%7C%20project-away%20subscriptionId1%2C%20resourceGroup1" target="_blank">portal.azure.cn</a>

---

## <a name="combine-results-from-two-queries-into-a-single-result"></a><a name="unionresults"></a>Combinar resultados de duas consultas em um único resultado

A consulta a seguir usa `union` para obter resultados da tabela _ResourceContainers_ e adicioná-los aos resultados da tabela _Recursos_.

```kusto
ResourceContainers
| where type=='microsoft.resources/subscriptions/resourcegroups' | project name, type  | limit 5
| union  (Resources | project name, type | limit 5)
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "ResourceContainers | where type=='microsoft.resources/subscriptions/resourcegroups' | project name, type  | limit 5 | union  (Resources | project name, type | limit 5)"
```

# <a name="azure-powershell"></a>[PowerShell do Azure](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "ResourceContainers | where type=='microsoft.resources/subscriptions/resourcegroups' | project name, type  | limit 5 | union  (Resources | project name, type | limit 5)"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Experimente usar esta consulta no Azure Resource Graph Explorer:

- Portal do Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ResourceContainers%0D%0A%7C%20where%20type%3D%3D%27microsoft.resources%2Fsubscriptions%2Fresourcegroups%27%20%7C%20project%20name%2C%20type%20%20%7C%20limit%205%0D%0A%7C%20union%20%20%28Resources%20%7C%20project%20name%2C%20type%20%7C%20limit%205%29" target="_blank">portal.azure.com</a>
- Portal do Azure Government: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ResourceContainers%0D%0A%7C%20where%20type%3D%3D%27microsoft.resources%2Fsubscriptions%2Fresourcegroups%27%20%7C%20project%20name%2C%20type%20%20%7C%20limit%205%0D%0A%7C%20union%20%20%28Resources%20%7C%20project%20name%2C%20type%20%7C%20limit%205%29" target="_blank">portal.azure.us</a>
- Portal do Azure China 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ResourceContainers%0D%0A%7C%20where%20type%3D%3D%27microsoft.resources%2Fsubscriptions%2Fresourcegroups%27%20%7C%20project%20name%2C%20type%20%20%7C%20limit%205%0D%0A%7C%20union%20%20%28Resources%20%7C%20project%20name%2C%20type%20%7C%20limit%205%29" target="_blank">portal.azure.cn</a>

---

## <a name="summarize-virtual-machine-by-the-power-states-extended-property"></a><a name="vm-powerstate"></a>Resumir a máquina virtual pela propriedade estendida dos estados de energia

Essa consulta usa as [propriedades estendidas](../concepts/query-language.md#extended-properties) em máquinas virtuais para resumir por estados de energia.


```kusto
Resources
| where type == 'microsoft.compute/virtualmachines'
| summarize count() by tostring(properties.extended.instanceView.powerState.code)
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type == 'microsoft.compute/virtualmachines' | summarize count() by tostring(properties.extended.instanceView.powerState.code)"
```

# <a name="azure-powershell"></a>[PowerShell do Azure](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type == 'microsoft.compute/virtualmachines' | summarize count() by tostring(properties.extended.instanceView.powerState.code)"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Experimente usar esta consulta no Azure Resource Graph Explorer:

- Portal do Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D%3D%20%27microsoft.compute%2Fvirtualmachines%27%20%7C%20summarize%20count%28%29%20by%20tostring%28properties.extended.instanceView.powerState.code%29" target="_blank">portal.azure.com</a>
- Portal do Azure Government: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D%3D%20%27microsoft.compute%2Fvirtualmachines%27%20%7C%20summarize%20count%28%29%20by%20tostring%28properties.extended.instanceView.powerState.code%29" target="_blank">portal.azure.us</a>
- Portal do Azure China 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D%3D%20%27microsoft.compute%2Fvirtualmachines%27%20%7C%20summarize%20count%28%29%20by%20tostring%28properties.extended.instanceView.powerState.code%29" target="_blank">portal.azure.cn</a>

---

## <a name="count-of-non-compliant-guest-configuration-assignments"></a><a name="count-gcnoncompliant"></a>Contagem de atribuições de Configuração de Convidado que não estão em conformidade

Exibe uma contagem de computadores que não estão em conformidade por [motivo de atribuição de Configuração de Convidado](../../policy/how-to/determine-non-compliance.md#compliance-details-for-guest-configuration). Limita os resultados aos 100 primeiros para o desempenho.

```kusto
GuestConfigurationResources
| extend vmid = split(properties.targetResourceId,'/')
| where properties.complianceStatus == 'NonCompliant'
| mvexpand properties.latestAssignmentReport.resources
| mvexpand properties_latestAssignmentReport_resources.reasons
| project machine = tostring(vmid[(-1)]),
    type = tostring(vmid[(-3)]),
    name,
    status = tostring(properties.complianceStatus),
    resource = tostring(properties_latestAssignmentReport_resources.resourceId),
    phrase = tostring(properties_latestAssignmentReport_resources_reasons.phrase)
| summarize count() by resource, name
| order by count_
| limit 100
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "GuestConfigurationResources | extend vmid = split(properties.targetResourceId,'/') | where properties.complianceStatus == 'NonCompliant' | mvexpand properties.latestAssignmentReport.resources | mvexpand properties_latestAssignmentReport_resources.reasons | project machine = tostring(vmid[(-1)]), type = tostring(vmid[(-3)]), name, status = tostring(properties.complianceStatus), resource = tostring(properties_latestAssignmentReport_resources.resourceId), phrase = tostring(properties_latestAssignmentReport_resources_reasons.phrase) | summarize count() by resource, name | order by count_ | limit 100"
```

# <a name="azure-powershell"></a>[PowerShell do Azure](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "GuestConfigurationResources | extend vmid = split(properties.targetResourceId,'/') | where properties.complianceStatus == 'NonCompliant' | mvexpand properties.latestAssignmentReport.resources | mvexpand properties_latestAssignmentReport_resources.reasons | project machine = tostring(vmid[(-1)]), type = tostring(vmid[(-3)]), name, status = tostring(properties.complianceStatus), resource = tostring(properties_latestAssignmentReport_resources.resourceId), phrase = tostring(properties_latestAssignmentReport_resources_reasons.phrase) | summarize count() by resource, name | order by count_ | limit 100"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Experimente usar esta consulta no Azure Resource Graph Explorer:

- Portal do Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/GuestConfigurationResources%20%7C%20extend%20vmid%20%3D%20split(properties.targetResourceId%2C%22%2F%22)%20%7C%20where%20properties.complianceStatus%20%3D%3D%20'NonCompliant'%20%7C%20mvexpand%20properties.latestAssignmentReport.resources%20%7C%20mvexpand%20properties_latestAssignmentReport_resources.reasons%20%7C%20project%20machine%20%3D%20tostring(vmid%5B(-1)%5D)%2C%20type%20%3D%20tostring(vmid%5B(-3)%5D)%2C%20name%2C%20status%20%3D%20tostring(properties.complianceStatus)%2C%20resource%20%3D%20tostring(properties_latestAssignmentReport_resources.resourceId)%2C%20phrase%20%3D%20tostring(properties_latestAssignmentReport_resources_reasons.phrase)%20%7C%20summarize%20count()%20by%20resource%2C%20name%20%7C%20order%20by%20count_%20%7C%20limit%20100" target="_blank">portal.azure.com</a>

---

## <a name="query-details-of-guest-configuration-assignment-reports"></a><a name="query-gcreports"></a>Consultar detalhes de relatórios de atribuição de Configuração de Convidado

Exiba o relatório dos detalhes do [motivo de atribuição de Configuração de Convidado](../../policy/how-to/determine-non-compliance.md#compliance-details-for-guest-configuration). No exemplo a seguir, a consulta retorna apenas os resultados em que o nome da Atribuição de Convidado é `installed_application_linux` e a saída contém a cadeia de caracteres `Python` para listar todos os computadores Linux em que um pacote está instalado, incluindo o nome **Python**. Para consultar a conformidade de todos os computadores para uma atribuição específica, remova a segunda cláusula `where`.

```kusto
GuestConfigurationResources
| extend vmid = split(properties.targetResourceId,'/')
| mvexpand properties.latestAssignmentReport.resources
| mvexpand properties_latestAssignmentReport_resources.reasons
| where name in ('installed_application_linux')
| where properties_latestAssignmentReport_resources_reasons.phrase contains 'Python'
| project machine = tostring(vmid[(-1)]),
    type = tostring(vmid[(-3)]),
    name,
    status = tostring(properties.complianceStatus),
    resource = tostring(properties_latestAssignmentReport_resources.resourceId),
    phrase = tostring(properties_latestAssignmentReport_resources_reasons.phrase)
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "GuestConfigurationResources | extend vmid = split(properties.targetResourceId,'/') | mvexpand properties.latestAssignmentReport.resources | mvexpand properties_latestAssignmentReport_resources.reasons | where name in ('installed_application_linux') | where properties_latestAssignmentReport_resources_reasons.phrase contains 'Python' | project machine = tostring(vmid[(-1)]), type = tostring(vmid[(-3)]), name, status = tostring(properties.complianceStatus), resource = tostring(properties_latestAssignmentReport_resources.resourceId), phrase = tostring (properties_latestAssignmentReport_resources_reasons.phrase)"
```

# <a name="azure-powershell"></a>[PowerShell do Azure](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "GuestConfigurationResources | extend vmid = split(properties.targetResourceId,'/') | mvexpand properties.latestAssignmentReport.resources | mvexpand properties_latestAssignmentReport_resources.reasons | where name in ('installed_application_linux') | where properties_latestAssignmentReport_resources_reasons.phrase contains 'Python' | project machine = tostring(vmid[(-1)]), type = tostring(vmid[(-3)]), name, status = tostring(properties.complianceStatus), resource = tostring(properties_latestAssignmentReport_resources.resourceId), phrase = tostring (properties_latestAssignmentReport_resources_reasons.phrase)"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Experimente usar esta consulta no Azure Resource Graph Explorer:

- Portal do Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/GuestConfigurationResources%20%7C%20extend%20vmid%20%3D%20split(properties.targetResourceId%2C'%2F')%20%7C%20mvexpand%20properties.latestAssignmentReport.resources%20%7C%20mvexpand%20properties_latestAssignmentReport_resources.reasons%20%7C%20where%20name%20in%20('installed_application_linux')%20%7C%20where%20properties_latestAssignmentReport_resources_reasons.phrase%20contains%20'Python'%20%7C%20project%20machine%20%3D%20tostring(vmid%5B(-1)%5D)%2C%20type%20%3D%20tostring(vmid%5B(-3)%5D)%2C%20name%2C%20status%20%3D%20tostring(properties.complianceStatus)%2C%20resource%20%3D%20tostring(properties_latestAssignmentReport_resources.resourceId)%2C%20phrase%20%3D%20tostring%20(properties_latestAssignmentReport_resources_reasons.phrase)" target="_blank">portal.azure.com</a>

---

## <a name="find-all-reasons-a-machine-is-non-compliant-for-guest-configuration-assignments"></a><a name="query-gcmachinedetails"></a>Localizar todos os motivos pelos quais um computador não está em conformidade para atribuições de Configuração de Convidado

Exiba todos os [motivos de atribuição de Configuração de Convidado](../../policy/how-to/determine-non-compliance.md#compliance-details-for-guest-configuration) para um computador específico. Remova a primeira cláusula `where` para também incluir auditorias em que o computador está em conformidade.

```kusto
GuestConfigurationResources
| where properties.complianceStatus == 'NonCompliant'
| extend vmid = split(properties.targetResourceId,'/')
| mvexpand properties.latestAssignmentReport.resources
| mvexpand properties_latestAssignmentReport_resources.reasons
| extend machine = tostring(vmid[(-1)])
| where machine == 'MACHINENAME'
| project phrase = tostring(properties_latestAssignmentReport_resources_reasons.phrase),
    resource = tostring(properties_latestAssignmentReport_resources.resourceId),
    name,
    machine,
    resourceGroup,
    subscriptionId
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "GuestConfigurationResources | where properties.complianceStatus == 'NonCompliant' | extend vmid = split(properties.targetResourceId,'/') | mvexpand properties.latestAssignmentReport.resources | mvexpand properties_latestAssignmentReport_resources.reasons | extend machine = tostring(vmid[(-1)]) | where machine == 'MACHINENAME' | project phrase = tostring(properties_latestAssignmentReport_resources_reasons.phrase), resource = tostring(properties_latestAssignmentReport_resources.resourceId), name, machine, resourceGroup, subscriptionId"
```

# <a name="azure-powershell"></a>[PowerShell do Azure](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "GuestConfigurationResources | where properties.complianceStatus == 'NonCompliant' | extend vmid = split(properties.targetResourceId,'/') | mvexpand properties.latestAssignmentReport.resources | mvexpand properties_latestAssignmentReport_resources.reasons | extend machine = tostring(vmid[(-1)]) | where machine == 'MACHINENAME' | project phrase = tostring(properties_latestAssignmentReport_resources_reasons.phrase), resource = tostring(properties_latestAssignmentReport_resources.resourceId), name, machine, resourceGroup, subscriptionId"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Experimente usar esta consulta no Azure Resource Graph Explorer:

- Portal do Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/GuestConfigurationResources%20%7C%20where%20properties.complianceStatus%20%3D%3D%20'NonCompliant'%20%7C%20extend%20vmid%20%3D%20split(properties.targetResourceId%2C'%2F')%20%7C%20mvexpand%20properties.latestAssignmentReport.resources%20%7C%20mvexpand%20properties_latestAssignmentReport_resources.reasons%20%7C%20extend%20machine%20%3D%20tostring(vmid%5B(-1)%5D)%20%7C%20where%20machine%20%3D%3D%20'MACHINENAME'%20%7C%20project%20phrase%20%3D%20tostring(properties_latestAssignmentReport_resources_reasons.phrase)%2C%20resource%20%3D%20tostring(properties_latestAssignmentReport_resources.resourceId)%2C%20name%2C%20machine%2C%20resourceGroup%2C%20subscriptionId" target="_blank">portal.azure.com</a>

## <a name="next-steps"></a>Próximas etapas

- Veja exemplos de [Consultas iniciais](starter.md).
- Saiba mais sobre a [linguagem de consulta](../concepts/query-language.md).
- Saiba mais sobre como [explorar recursos](../concepts/explore-resources.md).