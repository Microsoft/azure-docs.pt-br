Os exemplos neste artigo exigem a versão 3.0 ou posterior do Azure PowerShell. Se você não tiver a versão 3.0 ou posterior, [atualize sua versão](/powershell/azureps-cmdlets-docs/) usando a Galeria do PowerShell ou o Web Platform Installer.

Para conferir as marcas existentes para um *grupo de recursos*, use:

```powershell
(Get-AzureRmResourceGroup -Name examplegroup).Tags
```

Esse script retorna o seguinte formato:

```powershell
Name                           Value
----                           -----
Dept                           IT
Environment                    Test
```

Para conferir as marcas existentes para um *recurso que tem uma ID de recurso especificada*, use:

```powershell
(Get-AzureRmResource -ResourceId {resource-id}).Tags
```

Ou, para conferir as marcas existentes para um *recurso que tem um nome especificado, e um grupo de recursos*, use:

```powershell
(Get-AzureRmResource -ResourceName examplevnet -ResourceGroupName examplegroup).Tags
```

Para obter *grupos de recursos que têm uma marca específica*, use:

```powershell
(Find-AzureRmResourceGroup -Tag @{ Dept="Finance" }).Name
```

Para obter *recursos que têm uma marca específica*, use:

```powershell
(Find-AzureRmResource -TagName Dept -TagValue Finance).Name
```

Ao aplicar marcas a um recurso ou grupo de recursos, você pode substituir as marcas existentes nesse recurso ou grupo de recursos. Portanto, você deve usar uma abordagem diferente com base em se o recurso ou o grupo de recursos tem marcas existentes.

Para adicionar marcas a um *grupo de recursos sem marcas existentes*, use:

```powershell
Set-AzureRmResourceGroup -Name examplegroup -Tag @{ Dept="IT"; Environment="Test" }
```

Para adicionar marcas a um *grupo de recursos que tem marcas existentes*, recupere as marcas existentes, adicione a nova marca e reaplique as marcas:

```powershell
$tags = (Get-AzureRmResourceGroup -Name examplegroup).Tags
$tags += @{Status="Approved"}
Set-AzureRmResourceGroup -Tag $tags -Name examplegroup
```

Para adicionar marcas a um *grupo de recursos sem marcas existentes*, use:

```powershell
$r = Get-AzureRmResource -ResourceName examplevnet -ResourceGroupName examplegroup
Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test" } -ResourceId $r.ResourceId -Force
```

Para adicionar marcas a um *grupo de recursos que tem marcas existentes*, use:

```powershell
$r = Get-AzureRmResource -ResourceName examplevnet -ResourceGroupName examplegroup
$r.tags += @{Status="Approved"}
Set-AzureRmResource -Tag $r.Tags -ResourceId $r.ResourceId -Force
```

Para aplicar todas as marcas de um grupo de recursos em seus recursos, e *não manter as marcas existentes nos recursos*, use o seguinte script:

```powershell
$groups = Get-AzureRmResourceGroup
foreach ($g in $groups)
{
    Find-AzureRmResource -ResourceGroupNameEquals $g.ResourceGroupName | ForEach-Object {Set-AzureRmResource -ResourceId $_.ResourceId -Tag $g.Tags -Force }
}
```

Para aplicar todas as marcas de um grupo de recursos em seus recursos, e *manter as marcas existentes nos recursos que não são duplicados*, use o seguinte script:

```powershell
$groups = Get-AzureRmResourceGroup
foreach ($g in $groups)
{
    if ($g.Tags -ne $null) {
        $resources = Find-AzureRmResource -ResourceGroupNameEquals $g.ResourceGroupName
        foreach ($r in $resources)
        {
            $resourcetags = (Get-AzureRmResource -ResourceId $r.ResourceId).Tags
            foreach ($key in $g.Tags.Keys)
            {
                if ($resourcetags.ContainsKey($key)) { $resourcetags.Remove($key) }
            }
            $resourcetags += $g.Tags
            Set-AzureRmResource -Tag $resourcetags -ResourceId $r.ResourceId -Force
        }
    }
}
```

Para remover todas as marcas, passe uma tabela de hash vazio:

```powershell
Set-AzureRmResourceGroup -Tag @{} -Name examplegroup
```
