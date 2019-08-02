---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 042aedf1a043cd89d74ff099554642d38a3c7dd3
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171669"
---
## <a name="what-is-table-storage"></a>O que é o Armazenamento de Tabelas
O Armazenamento de Tabelas do Microsoft Azure armazena grandes quantidades de dados estruturados. O serviço é um repositório de dados NoSQL que aceita chamadas autenticadas de dentro e de fora da nuvem do Azure. As tabelas do Azure são ideais para armazenar dados estruturados não relacionais. Os usos comuns do armazenamento de tabelas incluem:

* Armazenamento de TBs de dados estruturados capazes de atender a aplicativos de dimensionamento da Web
* Armazenando conjuntos de dados que não exigem junções complexas, chaves estrangeiras ou procedimentos armazenados e que podem ser desnormalizados para acesso rápido
* Consulta rápida de dados usando um índice clusterizado
* Acesso a dados usando o protocolo OData e consultas LINQ com bibliotecas WCF Data Service .NET

Você pode usar o armazenamento de tabelas para armazenar e consultar grandes conjuntos de dados estruturados não relacionais, e suas tabelas serão dimensionadas conforme a demanda aumentar.

## <a name="table-storage-concepts"></a>Conceitos de armazenamento de tabelas
O armazenamento de tabelas contém os seguintes componentes:

![Diagrama de componentes do Armazenamento de Tabelas][Table1]

* **Formato de URL:** Contas do Armazenamento de Tabelas do Azure usam este formato: `http://<storage account>.table.core.windows.net/<table>`

  As contas da API de tabela do Azure Cosmos DB usam este formato:`http://<storage account>.table.cosmosdb.azure.com/<table>`  

  Você pode endereçar as tabelas do Azure diretamente usando esse endereço com o protocolo OData. Para saber mais, veja [OData.org][OData.org].
* **Contas:** Todo o acesso ao Armazenamento do Azure ocorre por meio de uma conta de armazenamento. Consulte [Escalabilidade e Metas de Desempenho do Armazenamento do Azure](../articles/storage/common/storage-scalability-targets.md) para obter detalhes sobre a capacidade da conta de armazenamento. 

    Todo o acesso ao Azure Cosmos DB é feito por meio de uma conta da API de tabela. Consulte [Criar uma conta de API de tabela](../articles/cosmos-db/create-table-dotnet.md#create-a-database-account) para saber detalhes sobre a criação de uma conta da API de tabela.
* **Tabela**: Uma tabela é uma coleção de entidades. As tabelas não impõem um esquema nas entidades, o que significa que uma única tabela pode conter entidades com diferentes conjuntos de propriedades.  
* **Entidade**: Uma entidade é um conjunto de propriedades, semelhantes a uma linha do banco de dados. Uma entidade no Armazenamento do Azure pode ser de até 1MB de tamanho. Uma entidade no Azure Cosmos DB pode ser de até 2MB de tamanho.
* **Propriedades**: Uma propriedade é um par nome-valor. Cada entidade pode incluir até 252 propriedades para armazenar dados. Cada entidade possui também três propriedades do sistema que especificam uma chave de partição, uma chave de linha e um carimbo de hora. As entidades com a mesma chave de partição podem ser consultadas mais rapidamente e inseridas/atualizadas em operações atômicas. A chave de linha de uma entidade é seu identificador exclusivo dentro de uma partição.

Para obter detalhes sobre a nomenclatura de tabelas e propriedades, confira [Noções básicas sobre o modelo de dados do serviço Tabela](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model).

[Table1]: ./media/storage-table-concepts-include/table1.png
[OData.org]: http://www.odata.org/
