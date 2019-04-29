---
title: Taxa de transferência de provisionamento nos contêineres e bancos de dados do Azure Cosmos
description: Saiba como definir a taxa de transferência provisionada para os contêineres e bancos de dados do Azure Cosmos.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/31/2019
ms.author: rimman
ms.openlocfilehash: ed3a171e60c078975de7003cdf599f0bac62c402
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61330275"
---
# <a name="provision-throughput-on-containers-and-databases"></a>Aprovisionar a taxa de transferência para contêineres e bancos de dados

Um banco de dados do Azure Cosmos é uma unidade de gerenciamento para um conjunto de contêineres. Um banco de dados é composto por um conjunto de contêineres independentes de esquema. Um contêiner do Azure Cosmos é a unidade de escalabilidade para taxa de transferência e armazenamento. Um contêiner é particionado horizontalmente em um conjunto de máquinas em uma região do Azure e é distribuído em todas as regiões do Azure associadas à sua conta do Azure Cosmos.

Com o Azure Cosmos DB, você pode provisionar a taxa de transferência em dois granularidades:
 
- Contêineres do Azure Cosmos
- Bancos de dados do Azure Cosmos

## <a name="set-throughput-on-a-container"></a>Definir taxa de transferência em um contêiner  

A taxa de transferência provisionada em um contêiner do Azure Cosmos é exclusivamente reservada para o contêiner. O contêiner recebe a taxa de transferência provisionada o tempo todo. A taxa de transferência provisionada em um contêiner é respaldada financeiramente por SLAs. Para saber como configurar a taxa de transferência em um contêiner, consulte [provisionar a produtividade em um contêiner do Azure Cosmos](how-to-provision-container-throughput.md).

Taxa de transferência provisionada em um contêiner de configuração é a opção usada com mais frequência. Você pode dimensionar elasticamente a taxa de transferência para um contêiner por meio do provisionamento qualquer quantidade de taxa de transferência usando [unidades de solicitação (RUs)](request-units.md). 

A taxa de transferência provisionada em um contêiner do Azure Cosmos é distribuída uniformemente em todas as partições lógicas do contêiner. Seletivamente não é possível especificar a taxa de transferência para partições lógicas. Como uma ou mais partições lógicas de um contêiner são hospedadas por uma partição física, as partições físicas pertencem exclusivamente ao contêiner e dão suporte à taxa de transferência provisionada no contêiner. 

Se a carga de trabalho em execução em uma partição lógica consome mais do que a taxa de transferência que foi alocada para essa partição lógica, as operações de ter sua taxa limitada. Quando ocorre a limitação de taxa, você pode aumentar a taxa de transferência provisionada para o contêiner inteiro ou repita as operações. Para saber mais sobre particionamento, confira [Partições lógicas](partition-data.md).

É recomendável configurar a taxa de transferência na granularidade do contêiner quando você quiser desempenho garantido para o contêiner.

A imagem a seguir mostra como uma partição física hospeda uma ou mais partições lógicas de um contêiner:

![Partição física](./media/set-throughput/resource-partition.png)

## <a name="set-throughput-on-a-database"></a>Definir taxa de transferência em um banco de dados

Ao provisionar a taxa de transferência em um banco de dados do Azure Cosmos, a taxa de transferência será compartilhada entre todos os contêineres no banco de dados. Uma exceção é se você especificou uma taxa de transferência provisionada em contêineres específicos no banco de dados. A taxa de transferência provisionada de nível de banco de dados entre seus contêineres de compartilhamento é análogo à hospedagem de um banco de dados em um cluster de computadores. Como todos os contêineres de um banco de dados compartilham os recursos disponíveis em um computador, evidentemente você não obtém o desempenho previsível em qualquer contêiner específico. Para saber como configurar a taxa de transferência provisionada em um banco de dados, consulte [taxa de transferência provisionada em um banco de dados Cosmos do Azure configurar](how-to-provision-database-throughput.md).

Taxa de transferência de configuração em um banco de dados Cosmos do Azure garante que você receba a taxa de transferência provisionada para esse banco de dados o tempo todo. Como todos os contêineres do banco de dados compartilham a taxa de transferência provisionada, o Azure Cosmos DB não fornece nenhuma garantia de taxa de transferência previsível para um contêiner específico nesse banco de dados. A parte da taxa de transferência que um contêiner específico pode receber depende do seguinte:

* Número de contêineres.
* Escolha de chaves de partição para vários contêineres.
* Da distribuição da carga de trabalho entre várias partições lógicas dos contêineres. 

É recomendável que você configure a taxa de transferência em um banco de dados quando quiser compartilhar a taxa de transferência em vários contêineres, mas não quer dedicar a taxa de transferência a nenhum contêiner específico. 

Os exemplos a seguir demonstram onde é preferível provisionar a taxa de transferência no nível do banco de dados:

* Compartilhar a taxa de transferência provisionada de um banco de dados em um conjunto de contêineres é útil para um aplicativo multilocatário. Cada usuário pode ser representado por um contêiner do Azure Cosmos distinto.

* Compartilhar a taxa de transferência provisionada de um banco de dados em um conjunto de contêineres é útil quando você migra um banco de dados NoSQL, como MongoDB ou Cassandra, hospedado em um cluster de VMs ou de servidores físicos locais para o Azure Cosmos DB. Pense na taxa de transferência provisionada configurada no banco de dados do Azure Cosmos como um equivalente lógico, porém mais econômico e elástico, ao da capacidade de computação do cluster do MongoDB ou Cassandra.  

Todos os contêineres de criado dentro de um banco de dados com produtividade provisionada devem ser criados com uma [chave de partição](partition-data.md). A qualquer momento, a taxa de transferência alocada a um contêiner dentro de um banco de dados será distribuída entre todas as partições lógicas desse contêiner. Quando você tem os contêineres que compartilham a taxa de transferência provisionada, configurada em um banco de dados, é possível aplicar seletivamente a taxa de transferência para um contêiner específico ou uma partição lógica. 

Se a carga de trabalho em uma partição lógica consumir mais do que a taxa de transferência alocada para uma partição lógica específica, suas operações serão limitadas por taxa. Quando ocorre a limitação de taxa, você pode aumentar a taxa de transferência para o banco de dados inteiro ou repita as operações. Para saber mais sobre particionamento, confira [Partições lógicas](partition-data.md).

Várias partições lógicas que pertencem a diferentes contêineres que compartilham a taxa de transferência provisionada para um banco de dados podem ser hospedadas em uma única partição física. Embora sempre tem como escopo uma única partição lógica de um contêiner em uma partição física, *"L"* partições lógicas entre *"C"* contêineres que compartilham a taxa de transferência provisionada de um banco de dados podem ser mapeado e hospedado no *"R"* partições físicas. 

A imagem a seguir mostra como uma partição física pode hospedar uma ou mais partições lógicas que pertencem a contêineres diferentes dentro de um banco de dados:

![Partição física](./media/set-throughput/resource-partition2.png)

## <a name="set-throughput-on-a-database-and-a-container"></a>Definir taxa de transferência em um banco de dados e um contêiner

É possível combinar os dois modelos. É permitido provisionar a taxa de transferência tanto no banco de dados como no contêiner. O exemplo a seguir mostra como provisionar a taxa de transferência em um banco de dados do Azure Cosmos e em um contêiner:

* Você pode criar um banco de dados Cosmos do Azure denominado *Z* com produtividade provisionada da *"K"* RUs. 
* Em seguida, cria cinco contêineres nomeados *um*, *B*, *C*, *1!d*, e *E* no banco de dados.
* Você pode configurar explicitamente *"P"* RUs da produtividade provisionada no contêiner chamado *B*.
* O *"K"* RUs taxa de transferência é compartilhada entre os contêineres de quatro *um*, *C*, *1!d*, e *E*. A quantidade exata produtividade disponível para *um*, *C*, *1!d*, ou *E* varia de acordo. Não há SLAs para taxa de transferência de cada contêiner individual.
* O contêiner nomeado *B* é garantia de obter o *"P"* taxa de transferência de RUs o tempo todo. É respaldado por SLAs.

## <a name="update-throughput-on-a-database-or-a-container"></a>Taxa de transferência de atualização em um banco de dados ou um contêiner

Depois de criar um contêiner do Cosmos do Azure ou um banco de dados, você pode atualizar a taxa de transferência provisionada. Não há nenhum limite na taxa de transferência provisionada máxima que você pode configurar o banco de dados ou o contêiner. Taxa de transferência provisionada mínima depende dos seguintes fatores: 

* O tamanho máximo dos dados armazenados no contêiner
* A taxa de transferência máxima que provisionar no contêiner
* O número máximo de contêineres do Cosmos do Azure que você nunca cria um banco de dados com taxa de transferência compartilhada. 

Você pode recuperar a taxa de transferência mínima de um contêiner ou um banco de dados programaticamente usando os SDKs ou exibir o valor no portal do Azure. Ao usar o SDK do .NET, o [DocumentClient.ReplaceOfferAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient.replaceofferasync?view=azure-dotnet) método permite que você dimensione o valor de taxa de transferência provisionada. Ao usar o SDK do Java, o [RequestOptions.setOfferThroughput](sql-api-java-samples.md#offer-examples) método permite que você dimensione o valor de taxa de transferência provisionada. 

Ao usar o SDK do .NET, o [DocumentClient.ReadOfferAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient.readofferasync?view=azure-dotnet) método permite que você recupere a taxa de transferência mínima de um contêiner ou um banco de dados. 

Você pode dimensionar a taxa de transferência provisionada de um contêiner ou um banco de dados a qualquer momento. 

## <a name="comparison-of-models"></a>Comparação de modelos

|**Parâmetro**  |**Taxa de transferência provisionada em um banco de dados**  |**Taxa de transferência provisionada em um contêiner**|
|---------|---------|---------|
|Mínimo de RUs |400 (após os primeiros quatro contêineres, cada contêiner adicional irá exigir um mínimo de 100 RUs por segundo.) |400|
|Mínimo de RUs por contêiner|100|400|
|Mínimo de RUs necessários para consumir 1 GB de armazenamento|40|40|
|Máximo de RUs|Ilimitado, no banco de dados.|Ilimitado, no contêiner.|
|RUs atribuídas ou disponíveis para um contêiner específico|Sem garantias. RUs atribuídas a um determinado contêiner dependem das propriedades. As propriedades podem ser a escolha de chaves de partição de contêineres que compartilham a taxa de transferência, a distribuição da carga de trabalho e o número de contêineres. |Todas as RUs configuradas no contêiner são reservadas exclusivamente ao contêiner.|
|Armazenamento máximo para um contêiner|Ilimitado.|Ilimitado.|
|Taxa de transferência máxima por partição lógica de um contêiner|10 mil RUs|10 mil RUs|
|Taxa de transferência máxima (dados + índice) por partição lógica de um contêiner|10 GB|10 GB|

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [partições lógicas](partition-data.md).
* Saiba como [provisionar taxa de transferência em um contêiner do Azure Cosmos](how-to-provision-container-throughput.md).
* Saiba como [provisionar taxa de transferência em um banco de dados do Azure Cosmos DB](how-to-provision-database-throughput.md).

