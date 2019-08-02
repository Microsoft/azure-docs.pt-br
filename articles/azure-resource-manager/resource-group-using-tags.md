---
title: Marcar recursos do Azure para organização lógica | Microsoft Docs
description: Mostra como aplicar marcas para organizar os recursos do Azure para cobrança e gerenciamento.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: tomfitz
ms.openlocfilehash: e18fc040249954ce7ea6a8a686e121a4b56fb54a
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68312131"
---
# <a name="use-tags-to-organize-your-azure-resources"></a>Usar marcações para organizar seus recursos do Azure

[!INCLUDE [resource-manager-governance-tags](../../includes/resource-manager-governance-tags.md)]

Para aplicar marcas a recursos, o usuário deve ter acesso de gravação a esse tipo de recurso. Para aplicar marcas a todos os tipos de recursos, use a função [Colaborador](../role-based-access-control/built-in-roles.md#contributor). Para aplicar marcas a apenas um tipo de recurso, use a função de colaborador para esse recurso. Por exemplo, para aplicar marcas a máquinas virtuais, use o [Colaborador da Máquina Virtual](../role-based-access-control/built-in-roles.md#virtual-machine-contributor).

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="policies"></a>Políticas

Você pode usar o [Azure Policy](../governance/policy/overview.md) para impor a marcação de regras e convenções. Ao criar uma política, você deve evitar o cenário de recursos implantados em sua assinatura que não são compatíveis com as marcas esperadas para a sua organização. Em vez de aplicar as marcas ou procurar os recursos que não são compatíveis manualmente, você pode criar uma política que aplica automaticamente as marcas necessárias durante a implantação. A seção a seguir mostra as políticas de exemplo para marcas.

[!INCLUDE [Tag policies](../../includes/azure-policy-samples-general-tags.md)]

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para conferir as marcas existentes para um *grupo de recursos*, use:

```azurepowershell-interactive
(Get-AzResourceGroup -Name examplegroup).Tags
```

Esse script retorna o seguinte formato:

```powershell
Name                           Value
----                           -----
Dept                           IT
Environment                    Test
```

Para conferir as marcas existentes para um *recurso que tem uma ID de recurso especificada*, use:

```azurepowershell-interactive
(Get-AzResource -ResourceId /subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>).Tags
```

Ou, para conferir as marcas existentes para um *recurso que tem um nome especificado, e um grupo de recursos*, use:

```azurepowershell-interactive
(Get-AzResource -ResourceName examplevnet -ResourceGroupName examplegroup).Tags
```

Para obter *grupos de recursos que têm uma marca específica*, use:

```azurepowershell-interactive
(Get-AzResourceGroup -Tag @{ Dept="Finance" }).ResourceGroupName
```

Para obter *recursos que têm uma marca específica*, use:

```azurepowershell-interactive
(Get-AzResource -Tag @{ Dept="Finance"}).Name
```

Para obter *recursos que têm um nome de marca específico*, use:

```azurepowershell-interactive
(Get-AzResource -TagName Dept).Name
```

Ao aplicar marcas a um recurso ou grupo de recursos, você pode substituir as marcas existentes nesse recurso ou grupo de recursos. Portanto, você deve usar uma abordagem diferente com base em se o recurso ou o grupo de recursos tem marcas existentes.

Para adicionar marcas a um *grupo de recursos sem marcas existentes*, use:

```azurepowershell-interactive
Set-AzResourceGroup -Name examplegroup -Tag @{ Dept="IT"; Environment="Test" }
```

Para adicionar marcas a um *grupo de recursos que tem marcas existentes*, recupere as marcas existentes, adicione a nova marca e reaplique as marcas:

```azurepowershell-interactive
$tags = (Get-AzResourceGroup -Name examplegroup).Tags
$tags.Add("Status", "Approved")
Set-AzResourceGroup -Tag $tags -Name examplegroup
```

Para adicionar marcas a um *recurso sem marcas existentes*, use:

```azurepowershell-interactive
$r = Get-AzResource -ResourceName examplevnet -ResourceGroupName examplegroup
Set-AzResource -Tag @{ Dept="IT"; Environment="Test" } -ResourceId $r.ResourceId -Force
```

Para adicionar marcas a um *grupo de recursos que tem marcas existentes*, use:

```azurepowershell-interactive
$r = Get-AzResource -ResourceName examplevnet -ResourceGroupName examplegroup
$r.Tags.Add("Status", "Approved")
Set-AzResource -Tag $r.Tags -ResourceId $r.ResourceId -Force
```

Para aplicar todas as marcas de um grupo de recursos a seus recursos e *não manter as marcas existentes nos recursos*, use o seguinte script:

```azurepowershell-interactive
$groups = Get-AzResourceGroup
foreach ($g in $groups)
{
    Get-AzResource -ResourceGroupName $g.ResourceGroupName | ForEach-Object {Set-AzResource -ResourceId $_.ResourceId -Tag $g.Tags -Force }
}
```

Para aplicar todas as marcas de um grupo de recursos a seus recursos e *manter as marcas existentes nos recursos que não são duplicatas*, use o seguinte script:

```azurepowershell-interactive
$group = Get-AzResourceGroup "examplegroup"
if ($null -ne $group.Tags) {
    $resources = Get-AzResource -ResourceGroupName $group.ResourceGroupName
    foreach ($r in $resources)
    {
        $resourcetags = (Get-AzResource -ResourceId $r.ResourceId).Tags
        if ($resourcetags)
        {
            foreach ($key in $group.Tags.Keys)
            {
                if (-not($resourcetags.ContainsKey($key)))
                {
                    $resourcetags.Add($key, $group.Tags[$key])
                }
            }
            Set-AzResource -Tag $resourcetags -ResourceId $r.ResourceId -Force
        }
        else
        {
            Set-AzResource -Tag $group.Tags -ResourceId $r.ResourceId -Force
        }
    }
}
```

Para remover todas as marcas, passe uma tabela de hash vazio:

```azurepowershell-interactive
Set-AzResourceGroup -Tag @{} -Name examplegroup
```

## <a name="azure-cli"></a>CLI do Azure

Para conferir as marcas existentes para um *grupo de recursos*, use:

```azurecli
az group show -n examplegroup --query tags
```

Esse script retorna o seguinte formato:

```json
{
  "Dept"        : "IT",
  "Environment" : "Test"
}
```

Ou, para conferir as marcas existentes para um *recurso que tem um nome, tipo e um grupo de recursos especificado*, use:

```azurecli
az resource show -n examplevnet -g examplegroup --resource-type "Microsoft.Network/virtualNetworks" --query tags
```

Ao executar um loop em uma coleção de recursos, talvez você queira exibir o recurso segundo sua ID. Um exemplo completo é exibido posteriormente neste artigo. Para conferir as marcas existentes para um *recurso que tem uma ID de recurso especificada*, use:

```azurecli
az resource show --id <resource-id> --query tags
```

Para obter grupos de recursos que têm uma marca específica, use `az group list`:

```azurecli
az group list --tag Dept=IT
```

Para obter todos os recursos que tem marca e valor específicos, use `az resource list`:

```azurecli
az resource list --tag Dept=Finance
```

Ao aplicar marcas a um recurso ou grupo de recursos, você pode substituir as marcas existentes nesse recurso ou grupo de recursos. Portanto, você deve usar uma abordagem diferente com base em se o recurso ou o grupo de recursos tem marcas existentes.

Para adicionar marcas a um *grupo de recursos sem marcas existentes*, use:

```azurecli
az group update -n examplegroup --set tags.Environment=Test tags.Dept=IT
```

Para adicionar marcas a um *recurso sem marcas existentes*, use:

```azurecli
az resource tag --tags Dept=IT Environment=Test -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

Para adicionar marcas a um recurso que já tem marcas, recupere as marcas existentes, reformate esse valor e reaplique as marcas novas e existentes:

```azurecli
jsonrtag=$(az resource show -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks" --query tags)
rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
az resource tag --tags $rt Project=Redesign -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

Para aplicar todas as marcas de um grupo de recursos a seus recursos e *não manter as marcas existentes nos recursos*, use o seguinte script:

```azurecli
groups=$(az group list --query [].name --output tsv)
for rg in $groups
do
  jsontag=$(az group show -n $rg --query tags)
  t=$(echo $jsontag | tr -d '"{},' | sed 's/: /=/g')
  r=$(az resource list -g $rg --query [].id --output tsv)
  for resid in $r
  do
    az resource tag --tags $t --id $resid
  done
done
```

Para aplicar todas as marcas de um grupo de recursos a seus recursos e *manter as marcas existentes nos recursos*, use o seguinte script:

```azurecli
groups=$(az group list --query [].name --output tsv)
for rg in $groups
do
  jsontag=$(az group show -n $rg --query tags)
  t=$(echo $jsontag | tr -d '"{},' | sed 's/: /=/g')
  r=$(az resource list -g $rg --query [].id --output tsv)
  for resid in $r
  do
    jsonrtag=$(az resource show --id $resid --query tags)
    rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
    az resource tag --tags $t$rt --id $resid
  done
done
```

## <a name="templates"></a>Modelos

Para marcar um recurso durante a implantação, adicione `tags` o elemento ao recurso que você está implantando. Forneça o nome e o valor da marca.

### <a name="apply-a-literal-value-to-the-tag-name"></a>Aplicar um valor literal ao nome da marca

O exemplo a seguir mostra uma conta de armazenamento com duas marcas (`Dept` e `Environment`) que são definidas como valores literais:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": {
                "Dept": "Finance",
                "Environment": "Production"
            },
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

Para definir uma marca para um valor DateTime, use a [função UtcNow](resource-group-template-functions-string.md#utcnow).

### <a name="apply-an-object-to-the-tag-element"></a>Aplicar um objeto ao elemento da marca

Você pode definir um parâmetro de objeto que armazena várias marcas e aplicar esse objeto para o elemento de marca. Cada propriedade no objeto se torna uma marca separada para o recurso. O exemplo a seguir tem um parâmetro chamado `tagValues` que é aplicado ao elemento de marca.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        },
        "tagValues": {
            "type": "object",
            "defaultValue": {
                "Dept": "Finance",
                "Environment": "Production"
            }
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": "[parameters('tagValues')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

### <a name="apply-a-json-string-to-the-tag-name"></a>Aplicar uma cadeia de caracteres JSON ao nome da marca

Para armazenar diversos valores em uma única marca, aplica uma cadeia de caracteres JSON que representa os valores. A cadeia de caracteres JSON inteira é armazenada como uma marca que não pode exceder 256 caracteres. O exemplo a seguir tem uma única marca denominada `CostCenter` que contém vários valores de uma cadeia de caracteres JSON:  

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": {
                "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
            },
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

### <a name="apply-tags-from-resource-group"></a>Aplicar marcas do grupo de recursos

Para aplicar marcas de um grupo de recursos a um recurso, use a função [resourcegroup](resource-group-template-functions-resource.md#resourcegroup) . Ao obter o valor da marca, use `tags.[tag-name]` a sintaxe em vez `tags.tag-name` da sintaxe, porque alguns caracteres não são analisados corretamente na notação de ponto.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": {
                "Dept": "[resourceGroup().tags['Dept']]",
                "Environment": "[resourceGroup().tags['Environment']]"
            },
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

## <a name="portal"></a>Portal

[!INCLUDE [resource-manager-tag-resource](../../includes/resource-manager-tag-resources.md)]

## <a name="rest-api"></a>API REST

O portal do Azure e o PowerShell usam a [API REST do Gerenciador de Recursos](https://docs.microsoft.com/rest/api/resources/) em segundo plano. Se você precisar integrar a marcação a outro ambiente, você pode obter marcas usando **GET** na ID do recurso e atualizar o conjunto de marcas usando uma chamada de **PATCH**.

## <a name="tags-and-billing"></a>Marcas e cobrança

Você pode usar marcas para agrupar os dados de cobrança. Por exemplo, se estiver executando várias VMs para organizações diferentes, use marcas para agrupar o uso por centro de custo. Você também pode usar marcas para categorizar os custos pelo ambiente de tempo de execução, como por exemplo, o uso de cobrança para VMs em execução no ambiente de produção.

Você pode recuperar as informações sobre as marcações por meio das [APIs RateCard e Uso de Recursos do Azure](../billing/billing-usage-rate-card-overview.md) ou do arquivo de uso CSV (com valores separados por vírgula). Você baixar o arquivo de uso do [Centro de Contas do Azure](https://account.azure.com/Subscriptions) ou do Portal do Azure. Para saber mais, confira [Baixar ou exibir sua fatura de cobrança e dados de uso diário do Azure](../billing/billing-download-azure-invoice-daily-usage-date.md). Ao baixar o arquivo de uso do Centro de Contas do Azure, selecione **Versão 2**. Para os serviços que dão suporte a marcas com cobrança, as marcas aparecerão na coluna **Marcas**.

Para operações de API REST, confira [Referência da API REST de cobrança do Azure](/rest/api/billing/).

## <a name="next-steps"></a>Próximas etapas

* Nem todos os tipos de recursos suportam tags. Para determinar se você pode aplicar uma tag a um tipo de recurso, consulte [Suporte a tags para recursos do Azure](tag-support.md).
* Para obter uma introdução ao uso do portal, confira [Usando o portal do Azure para gerenciar os recursos do Azure](manage-resource-groups-portal.md).  
