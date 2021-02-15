---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/27/2019
ms.author: tamram
ms.openlocfilehash: 9a60c624b181a1efd2f6deebd349daa82214a8a4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "67171597"
---
<!--created by Robin Shahan to go in the articles for table storage w/powershell.
    There is one for Azure Table Storage and one for Azure Cosmos DB Table API -->

## <a name="managing-table-entities"></a>Gerenciando entidades de tabela

Agora que você tem uma tabela, vamos examinar como gerenciar entidades ou linhas, na tabela. 

As entidades podem ter até 255 Propriedades, incluindo três propriedades do sistema: **PartitionKey**, **RowKey**e **timestamp**. Você é responsável por inserir e atualizar os valores de **PartitionKey** e **RowKey**. O servidor gerencia o valor do **carimbo de data/hora**, que não pode ser modificado. Juntas, **PartitionKey** e **RowKey** identificam exclusivamente cada entidade dentro de uma tabela.

* **PartitionKey**: determina a partição em que a entidade está armazenada.
* **RowKey**: identifica exclusivamente a entidade dentro da partição.

Você pode definir até 252 propriedades personalizadas para uma entidade. 

### <a name="add-table-entities"></a>Adicionar entidades de tabela

Adicione entidades a uma tabela usando **Add-AzTableRow**. Esses exemplos usam chaves de partição com valores `partition1` e `partition2` chaves de linha iguais a abreviações de estado. As propriedades em cada entidade são `username` e `userid` . 

```powershell
$partitionKey1 = "partition1"
$partitionKey2 = "partition2"

# add four rows 
Add-AzTableRow `
    -table $cloudTable `
    -partitionKey $partitionKey1 `
    -rowKey ("CA") -property @{"username"="Chris";"userid"=1}

Add-AzTableRow `
    -table $cloudTable `
    -partitionKey $partitionKey2 `
    -rowKey ("NM") -property @{"username"="Jessie";"userid"=2}

Add-AzTableRow `
    -table $cloudTable `
    -partitionKey $partitionKey1 `
    -rowKey ("WA") -property @{"username"="Christine";"userid"=3}

Add-AzTableRow `
    -table $cloudTable `
    -partitionKey $partitionKey2 `
    -rowKey ("TX") -property @{"username"="Steven";"userid"=4}
```

### <a name="query-the-table-entities"></a>Consultar as entidades de tabela

Você pode consultar as entidades em uma tabela usando o comando **Get-AzTableRow** .

> [!NOTE]
> Os cmdlets **Get-AzureStorageTableRowAll**, **Get-AzureStorageTableRowByPartitionKey**, **Get-AzureStorageTableRowByColumnName**e **Get-AzureStorageTableRowByCustomFilter** são preteridos e serão removidos em uma atualização de versão futura.

#### <a name="retrieve-all-entities"></a>Recuperar todas as entidades

```powershell
Get-AzTableRow -table $cloudTable | ft
```

Este comando gera resultados semelhantes à seguinte tabela:

| userid | Nome de Usuário | partition | rowkey |
|----|---------|---------------|----|
| 1 | Chris | partition1 | AC |
| 3 | Christine | partition1 | WA |
| 2 | Jessie | partition2 | NM |
| 4 | Steven | partition2 | TX |

#### <a name="retrieve-entities-for-a-specific-partition"></a>Recuperar entidades de uma partição específica

```powershell
Get-AzTableRow -table $cloudTable -partitionKey $partitionKey1 | ft
```

Os resultados são parecidos com a seguinte tabela:

| userid | Nome de Usuário | partition | rowkey |
|----|---------|---------------|----|
| 1 | Chris | partition1 | AC |
| 3 | Christine | partition1 | WA |

#### <a name="retrieve-entities-for-a-specific-value-in-a-specific-column"></a>Recuperar entidades de um valor específico em uma coluna específica

```powershell
Get-AzTableRow -table $cloudTable `
    -columnName "username" `
    -value "Chris" `
    -operator Equal
```

Essa consulta recupera um registro.

|field|value|
|----|----|
| userid | 1 |
| Nome de Usuário | Chris |
| PartitionKey | partition1 |
| RowKey      | AC |

#### <a name="retrieve-entities-using-a-custom-filter"></a>Recuperar entidades usando um filtro personalizado 

```powershell
Get-AzTableRow `
    -table $cloudTable `
    -customFilter "(userid eq 1)"
```

Essa consulta recupera um registro.

|field|value|
|----|----|
| userid | 1 |
| Nome de Usuário | Chris |
| PartitionKey | partition1 |
| RowKey      | AC |

### <a name="updating-entities"></a>Atualizando entidades 

Há três etapas para a atualização de entidades. Primeiro, recupere a entidade para alterar. Em segundo lugar, faça a alteração. Em terceiro lugar, confirme a alteração usando **Update-AzTableRow**.

Atualize a entidade com o nome de usuário = “Jessie” para ter o nome de usuário = “Jessie2”. Este exemplo também mostra outra maneira de criar um filtro personalizado usando tipos .NET.

```powershell
# Create a filter and get the entity to be updated.
[string]$filter = `
    [Microsoft.Azure.Cosmos.Table.TableQuery]::GenerateFilterCondition("username",`
    [Microsoft.Azure.Cosmos.Table.QueryComparisons]::Equal,"Jessie")
$user = Get-AzTableRow `
    -table $cloudTable `
    -customFilter $filter

# Change the entity.
$user.username = "Jessie2"

# To commit the change, pipe the updated record into the update cmdlet.
$user | Update-AzTableRow -table $cloudTable

# To see the new record, query the table.
Get-AzTableRow -table $cloudTable `
    -customFilter "(username eq 'Jessie2')"
```

Os resultados mostram o registro Jessie2.

|field|value|
|----|----|
| userid | 2 |
| Nome de Usuário | Jessie2 |
| PartitionKey | partition2 |
| RowKey      | NM |

### <a name="deleting-table-entities"></a>Excluindo entidades de tabela

Você pode excluir uma entidade ou todas as entidades da tabela.

#### <a name="deleting-one-entity"></a>Excluindo uma entidade

Para excluir uma única entidade, obtenha uma referência a essa entidade e redirecione-a para **Remove-AzTableRow**.

```powershell
# Set filter.
[string]$filter = `
  [Microsoft.Azure.Cosmos.Table.TableQuery]::GenerateFilterCondition("username",`
  [Microsoft.Azure.Cosmos.Table.QueryComparisons]::Equal,"Jessie2")

# Retrieve entity to be deleted, then pipe it into the remove cmdlet.
$userToDelete = Get-AzTableRow `
    -table $cloudTable `
    -customFilter $filter
$userToDelete | Remove-AzTableRow -table $cloudTable

# Retrieve entities from table and see that Jessie2 has been deleted.
Get-AzTableRow -table $cloudTable | ft
```

#### <a name="delete-all-entities-in-the-table"></a>Excluir todas as entidades da tabela

Para excluir todas as entidades na tabela, você as recupera e redireciona os resultados para o cmdlet de remoção. 

```powershell
# Get all rows and pipe the result into the remove cmdlet.
Get-AzTableRow `
    -table $cloudTable | Remove-AzTableRow -table $cloudTable 

# List entities in the table (there won't be any).
Get-AzTableRow -table $cloudTable | ft
```
