---
title: Crie contêineres do Cosmos do Azure com a chave de partição grande usando o portal do Azure e vários SDKs.
description: Saiba como criar um contêiner no Azure Cosmos DB com a chave de partição grande usando o portal do Azure e SDKs diferentes.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/03/2019
ms.author: mjbrown
ms.openlocfilehash: bd1697378e5db0432d181f9f688ccc2468b306e7
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/04/2019
ms.locfileid: "67566016"
---
# <a name="create-containers-with-large-partition-key"></a>Criar contêineres com a chave de partição grande

O Azure Cosmos DB usa o esquema de particionamento baseado em hash para alcançar a escala horizontal de dados. Todos os contêineres do Azure Cosmos criados antes de 3 de maio de 2019 usam uma função de hash que calcula o hash com base no que os primeiros 100 bytes da chave de partição. Se houver várias chaves de partição que têm os mesmos primeiros 100 bytes, em seguida, essas partições lógicas são consideradas como a mesma partição lógica pelo serviço. Isso pode levar a problemas como a cota de tamanho de partição que está sendo incorreto e os índices exclusivos que estão sendo aplicados entre as chaves de partição. Chaves de partição grande são introduzidas para resolver esse problema. O Azure Cosmos DB agora dá suporte a chaves de partição grande com valores de até 2 KB.

Até 2 KB de chaves de partição grande chaves têm suporte usando a funcionalidade de uma versão aprimorada do que a função de hash, que pode gerar um hash exclusivo de partição grande. Esta versão de hash também é recomendado para cenários com cardinalidade de chave de partição alta, independentemente do tamanho da chave de partição. Uma cardinalidade de chave de partição é definida como o número de partições lógicas exclusivos, por exemplo, na ordem partições lógicas de ~ 30000 em um contêiner. Este artigo descreve como criar um contêiner com uma chave de partição grande usando o portal do Azure e SDKs diferentes. 

## <a name="create-a-large-partition-key-net-sdk-v2"></a>Criar uma chave de partição grande (.Net SDK V2)

Para criar um contêiner com uma chave de partição grande usando o SDK do .NET, especifique o `PartitionKeyDefinitionVersion.V2` propriedade. O exemplo a seguir mostra como especificar a propriedade da versão dentro do objeto PartitionKeyDefinition e defini-lo como PartitionKeyDefinitionVersion.V2.

```csharp
DocumentCollection collection = await newClient.CreateDocumentCollectionAsync(
database,
     new DocumentCollection
        {
           Id = Guid.NewGuid().ToString(),
           PartitionKey = new PartitionKeyDefinition
           {
             Paths = new Collection<string> {"/longpartitionkey" },
             Version = PartitionKeyDefinitionVersion.V2
           }
         },
      new RequestOptions { OfferThroughput = 400 });
```

## <a name="create-a-large-partition-key-azure-portal"></a>Criar uma chave de partição grande (portal do Azure) 

Para criar uma chave de partição grande, enquanto você cria um novo contêiner usando o portal do Azure, verifique as **minha chave de partição é maior que 100 bytes** opção. Por padrão, todos os novos contêineres aceitados usando as chaves de partição grande. Se você não precisar de chaves de partição grande ou se você tiver aplicativos em execução na versão de SDKs mais antigo que 1.18, desmarque a caixa de seleção.

![Criar chaves de partição grande usando o portal do Azure](./media/large-partition-keys/large-partition-key-with-portal.png)

## <a name="create-a-large-partition-key-powershell"></a>Criar uma chave de partição grande (PowerShell)

Para criar um contêiner com uma chave de partição grande usando o PowerShell, inclua `"version" = 2` para o `partitionKey` objeto.

```azurepowershell-interactive
# Create a Cosmos SQL API container with large partition key support (version 2)
$resourceGroupName = "myResourceGroup"
$containerName = "mycosmosaccount" + "/sql/" + "myDatabase" + "/" + "myContainer"

# Container with large partition key support (version = 2)
$containerProperties = @{
  "resource"=@{
    "id"=$containerName;
    "partitionKey"=@{
        "paths"=@("/myPartitionKey");
        "kind"="Hash";
        "version" = 2
    };
    "indexingPolicy"=@{
        "indexingMode"="Consistent";
        "includedPaths"= @(@{
            "path"="/*"
        });
        "excludedPaths"= @(@{
            "path"="/myPathToNotIndex/*"
        })
    }
  }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $containerName -PropertyObject $containerProperties
```

## <a name="supported-sdk-versions"></a>Versões com suporte SDK

As chaves de partição grande são compatíveis com as seguintes versões mínimas de SDKs:

|Tipo SDK  | Versão mínima   |
|---------|---------|
|.Net     |    1.18     |
|Sincronização de Java     |   2.4.0      |
|Async Java   |  2.5.0        |
| API REST | versão maior do que `2017-05-03` usando o `x-ms-version` cabeçalho de solicitação.|

Atualmente, é possível usar contêineres com a chave de partição grande dentro no Power BI e aplicativos lógicos do Azure. Você pode usar contêineres sem uma chave de partição grande desses aplicativos.

## <a name="next-steps"></a>Próximas etapas

* [Particionamento no Azure Cosmos DB](partitioning-overview.md)
* [Unidades de Solicitação no Azure Cosmos DB](request-units.md)
* [Provisionar a taxa de transferência para contêineres e bancos de dados](set-throughput.md)
* [Como trabalhar com a conta do Azure Cosmos](account-overview.md)


