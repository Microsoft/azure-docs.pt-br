---
title: Taxa de transferência de provisionamento nos contêineres e bancos de dados do Azure Cosmos
description: Saiba como definir a taxa de transferência provisionada para os contêineres e bancos de dados do Azure Cosmos.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/25/2021
ms.openlocfilehash: 74addd691e3a6c42f48100292542cfd3563b5c3a
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98797574"
---
# <a name="introduction-to-provisioned-throughput-in-azure-cosmos-db"></a>Introdução à taxa de transferência provisionada do Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

O Azure Cosmos DB permite que você defina a taxa de transferência nos seus banco de dados e contêineres. Há dois tipos de taxa de transferência provisionada, padrão (manual) ou dimensionamento automático. Este artigo fornece uma visão geral de como funciona a taxa de transferência provisionada. 

Um banco de dados do Azure Cosmos é uma unidade de gerenciamento para um conjunto de contêineres. Um banco de dados é composto por um conjunto de contêineres independentes de esquema. Um contêiner do Azure Cosmos é a unidade de escalabilidade para taxa de transferência e armazenamento. Um contêiner é particionado horizontalmente em um conjunto de máquinas em uma região do Azure e é distribuído em todas as regiões do Azure associadas à sua conta do Azure Cosmos.

No Azure Cosmos DB, você pode provisionar a taxa de transferência em duas granularidades:
 
- Contêineres do Azure Cosmos
- Bancos de dados do Azure Cosmos

## <a name="set-throughput-on-a-container"></a>Definir taxa de transferência em um contêiner  

A taxa de transferência provisionada em um contêiner do Azure Cosmos é reservada exclusivamente para esse contêiner. O contêiner recebe a taxa de transferência provisionada o tempo todo. A taxa de transferência provisionada em um contêiner é respaldada financeiramente por SLAs. Para saber como configurar a taxa de transferência padrão (manual) em um contêiner, consulte [Provisionar taxa de transferência em um contêiner do Azure Cosmos](how-to-provision-container-throughput.md). Para saber como configurar a taxa de transferência de dimensionamento automático em um contêiner, consulte [Provisionar taxa de transferência de dimensionamento automático](how-to-provision-autoscale-throughput.md).

A configuração da taxa de transferência provisionada em um contêiner é a opção frequentemente utilizada. É possível dimensionar de forma elástica a taxa de transferência de um contêiner, provisionando qualquer quantidade de taxa de transferência usando [RUs ( Unidades de Solicitação)](request-units.md). 

A taxa de transferência provisionada para um contêiner é distribuída uniformemente entre suas partições físicas e, pressupondo que haja uma boa chave de partição que distribui as partições lógicas uniformemente entre as partições físicas, a taxa de transferência também é distribuída uniformemente entre todas as partições lógicas do contêiner. Não é possível especificar seletivamente a taxa de transferência para partições lógicas. Como uma ou mais partições lógicas de um contêiner são hospedadas por uma partição física, as partições físicas pertencem exclusivamente ao contêiner e dão suporte à taxa de transferência provisionada no contêiner. 

Se a carga de trabalho em execução em uma partição lógica consumir mais do que a taxa de transferência alocada para a partição física subjacente, será possível que suas operações sejam limitadas por taxa. O que é conhecido como uma _partição ativa_ ocorre quando uma partição lógica tem, desproporcionalmente, mais solicitações do que outros valores de chave de partição.

Quando ocorrer uma limitação da taxa, você poderá aumentar a taxa de transferência provisionada de todo o contêiner ou tentar novamente as operações. Você também deve se certificar de escolher uma chave de partição que distribua uniformemente o armazenamento e o volume de solicitação. Para obter mais informações sobre particionamento, consulte [particionamento e dimensionamento horizontal em Azure Cosmos DB](partitioning-overview.md).

Recomendamos que você configure a taxa de transferência na granularidade do contêiner quando desejar um desempenho previsível para o contêiner.

A imagem a seguir mostra como uma partição física hospeda uma ou mais partições lógicas de um contêiner:

:::image type="content" source="./media/set-throughput/resource-partition.png" alt-text="Partição física que hospeda uma ou mais partições lógicas de um contêiner" border="false":::

## <a name="set-throughput-on-a-database"></a>Definir taxa de transferência em um banco de dados

Ao provisionar a taxa de transferência em um banco de dados do Azure Cosmos, a taxa de transferência será compartilhada entre todos os contêineres (denominados contêineres de banco de dados compartilhados) no banco de dados. Uma exceção é se você especificou uma taxa de transferência provisionada em contêineres específicos no banco de dados. O compartilhamento da taxa de transferência provisionada no banco de dados entre seus contêineres é análogo à hospedagem de um banco de dados em um cluster de computadores. Como todos os contêineres de um banco de dados compartilham os recursos disponíveis em um computador, evidentemente você não obtém o desempenho previsível em qualquer contêiner específico. Para saber como configurar a taxa de transferência provisionada em um banco de dados, consulte [Configurar taxa de transferência provisionada em um banco de dados do Azure Cosmos](how-to-provision-database-throughput.md). Para saber como configurar a taxa de transferência de dimensionamento automático em um banco de dados, consulte [Provisionar taxa de transferência de dimensionamento automático](how-to-provision-autoscale-throughput.md).

Como todos os contêineres do banco de dados compartilham a taxa de transferência provisionada, o Azure Cosmos DB não fornece nenhuma garantia de taxa de transferência previsível para um contêiner específico nesse banco de dados. A parte da taxa de transferência que um contêiner específico pode receber depende do seguinte:

* Número de contêineres.
* Escolha de chaves de partição para vários contêineres.
* Da distribuição da carga de trabalho entre várias partições lógicas dos contêineres. 

É recomendável que você configure a taxa de transferência em um banco de dados quando quiser compartilhar a taxa de transferência em vários contêineres, mas não quer dedicar a taxa de transferência a nenhum contêiner específico. 

Os exemplos a seguir demonstram onde é preferível provisionar a taxa de transferência no nível do banco de dados:

* Compartilhar a taxa de transferência provisionada de um banco de dados em um conjunto de contêineres é útil para um aplicativo multilocatário. Cada usuário pode ser representado por um contêiner do Azure Cosmos distinto.

* Compartilhar a taxa de transferência provisionada de um banco de dados em um conjunto de contêineres é útil quando você migra um banco de dados NoSQL, como MongoDB ou Cassandra, hospedado em um cluster de VMs ou de servidores físicos locais para o Azure Cosmos DB. Pense na taxa de transferência provisionada configurada no banco de dados do Azure Cosmos como um equivalente lógico, porém mais econômico e elástico, ao da capacidade de computação do cluster do MongoDB ou Cassandra.  

Todos os contêineres criados em um banco de dados com taxa de transferência provisionada devem ser criados com uma [chave de partição](partitioning-overview.md). A qualquer momento, a taxa de transferência alocada a um contêiner dentro de um banco de dados será distribuída entre todas as partições lógicas desse contêiner. Quando houver contêineres compartilhando a taxa de transferência provisionada configurada em um banco de dados, não será possível aplicar seletivamente a taxa de transferência a um contêiner específico ou a uma partição lógica. 

Se a carga de trabalho em uma partição lógica consumir mais do que a taxa de transferência alocada para uma partição lógica específica, suas operações serão limitadas por taxa. Quando ocorrer uma limitação da taxa, você poderá aumentar a taxa de transferência de todo o banco de dados ou tentar novamente as operações. Para saber mais sobre particionamento, confira [Partições lógicas](partitioning-overview.md).

Os contêineres em um banco de dados de produtividade compartilhado também compartilham a taxa de transferência (RU/s) alocada para esse banco de dados. Com a taxa de transferência padrão (manual) provisionada, você pode ter até 25 contêineres com um mínimo de 400 RU/s no banco de dados. Com a taxa de transferência provisionada de dimensionamento automático, você pode ter até 25 contêineres em um banco de dados com dimensionamento automático de 4000 RU/s (escalas entre 400-4000 RU/s).

> [!NOTE]
> Em fevereiro de 2020, apresentamos uma alteração que permite que você tenha um máximo de 25 contêineres em um banco de dados de taxa de transferência compartilhado, o que permite melhor o compartilhamento de taxa de transferência entre os contêineres. Após os 25 primeiros contêineres, você poderá adicionar mais contêineres ao banco de dados somente se eles estiverem [provisionados com taxa de transferência dedicada](#set-throughput-on-a-database-and-a-container), que é separada da produtividade compartilhada do banco de dados.<br>
Se sua conta de Azure Cosmos DB já contiver um banco de dados de taxa de transferência compartilhado com >= 25 contêineres, a conta e todas as outras contas na mesma assinatura do Azure serão isentas dessa alteração. [Entre em contato com o suporte ao produto](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se você tiver comentários ou dúvidas. 

Se suas cargas de trabalho envolvem excluir e recriar todas as coleções em um banco de dados, é recomendável descartar o banco de dados vazio e recriar um novo banco de dados antes da criação da coleção. A imagem a seguir mostra como uma partição física pode hospedar uma ou mais partições lógicas que pertencem a contêineres diferentes dentro de um banco de dados:

:::image type="content" source="./media/set-throughput/resource-partition2.png" alt-text="Partição física que hospeda uma ou mais partições lógicas que pertencem a contêineres diferentes " border="false":::

## <a name="set-throughput-on-a-database-and-a-container"></a>Definir taxa de transferência em um banco de dados e um contêiner

É possível combinar os dois modelos. É permitido provisionar a taxa de transferência tanto no banco de dados como no contêiner. O exemplo a seguir mostra como provisionar a taxa de transferência provisionada padrão (manual) em um banco de dados do Azure Cosmos e em um contêiner:

* Você pode criar um banco de dados do Azure Cosmos *Z* com a taxa de transferência provisionada padrão (manual) de RUs *"K"* . 
* Em seguida, crie cinco contêineres chamados *A*, *B*, *C*, *D* e *E* no banco de dados. Ao criar o contêiner B, habilite a opção **Provisionar taxa de transferência dedicada para esse contêiner** e configure explicitamente RUs *"P"* de taxa de transferência provisionada neste contêiner. Você pode configurar a taxa de transferência compartilhada e dedicada somente ao criar o banco de dados e o contêiner. 

   :::image type="content" source="./media/set-throughput/coll-level-throughput.png" alt-text="Definir a taxa de transferência no nível do contêiner":::

* O *"K"* a taxa de transferência de RUs é compartilhada entre os quatro contêineres *A*, *C*, *D* e *E*. A quantidade exata de taxa de transferência disponível para *A*, *C*, *D* ou *E* varia. Não há SLAs para taxa de transferência de cada contêiner individual.
* O contêiner nomeado *B* tem a garantia de obter a taxa de transferência de RUs *"P"* o tempo todo. É respaldado por SLAs.

> [!NOTE]
> Um contêiner com taxa de transferência provisionada não pode ser convertido para o contêiner de banco de dados compartilhado. Por outro lado, um contêiner de banco de dados compartilhado não pode ser convertido para ter uma taxa de transferência dedicada.

## <a name="update-throughput-on-a-database-or-a-container"></a>Atualizar a taxa de transferência em um banco de dados ou um contêiner

Depois de criar um contêiner Cosmos do Azure ou um banco de dados, você pode atualizar a taxa de transferência provisionada. Não há limite para a taxa de transferência máxima provisionada que você pode configurar no banco de dados ou no contêiner.

### <a name="current-provisioned-throughput"></a><a id="current-provisioned-throughput"></a> Taxa de transferência provisionada atual

Você pode recuperar a taxa de transferência provisionada de um contêiner ou de um banco de dados no portal do Azure ou usando os SDKs:

* [Container. ReadThroughputAsync](/dotnet/api/microsoft.azure.cosmos.container.readthroughputasync?view=azure-dotnet&preserve-view=true) no SDK do .net.
* [CosmosContainer. readThroughput](/java/api/com.azure.cosmos.cosmosasynccontainer.readthroughput?view=azure-java-stable&preserve-view=true) no SDK do Java.

A resposta desses métodos também contém a [taxa de transferência mínima provisionada](concepts-limits.md#storage-and-database-operations) para o contêiner ou banco de dados:

* [ThroughputResponse. MinThroughput](/dotnet/api/microsoft.azure.cosmos.throughputresponse.minthroughput?view=azure-dotnet&preserve-view=true) no SDK do .net.
* [ThroughputResponse. getMinThroughput ()](/java/api/com.azure.cosmos.models.throughputresponse.getminthroughput?view=azure-java-stable&preserve-view=true) no SDK do Java.

O mínimo de RU/s real pode variar dependendo da configuração da sua conta. Mas, em geral, é o máximo de:

* 400 RU/s 
* Armazenamento atual em GB * 10 RU/s (essa restrição pode ser reduzida em alguns casos, consulte nosso [programa de alto armazenamento/baixa taxa de transferência](#high-storage-low-throughput-program))
* RU/s mais alto provisionado no banco de dados ou contêiner/100

### <a name="changing-the-provisioned-throughput"></a>Alterando a taxa de transferência provisionada

Você pode dimensionar a taxa de transferência provisionada de um contêiner ou de um banco de dados por meio do portal do Azure ou usando os SDKs:

* [Container. ReplaceThroughputAsync](/dotnet/api/microsoft.azure.cosmos.container.replacethroughputasync?view=azure-dotnet&preserve-view=true) no SDK do .net.
* [CosmosContainer. replaceThroughput](/java/api/com.azure.cosmos.cosmosasynccontainer.replacethroughput?view=azure-java-stable&preserve-view=true) no SDK do Java.

Se você estiver **reduzindo a taxa de transferência provisionada**, poderá fazer isso até o [mínimo](#current-provisioned-throughput).

Se você estiver **aumentando a taxa de transferência provisionada**, na maioria das vezes, a operação será instantânea. No entanto, há casos em que a operação pode levar mais tempo devido às tarefas do sistema para provisionar os recursos necessários. Nesse caso, uma tentativa de modificar a taxa de transferência provisionada enquanto essa operação está em andamento produzirá uma resposta HTTP 423 com uma mensagem de erro explicando que outra operação de dimensionamento está em andamento.

> [!NOTE]
> Se você estiver planejando uma carga de trabalho de ingestão muito grande que exigirá um grande aumento na taxa de transferência provisionada, tenha em mente que a operação de dimensionamento não tem SLA e, como mencionado no parágrafo anterior, pode levar muito tempo quando o aumento for grande. Talvez você queira planejar com antecedência e iniciar o dimensionamento antes que a carga de trabalho comece e use os métodos abaixo para verificar o progresso.

Você pode verificar o progresso do dimensionamento programaticamente lendo a [taxa de transferência provisionada atual](#current-provisioned-throughput) e usando:

* [ThroughputResponse. IsReplacePending](/dotnet/api/microsoft.azure.cosmos.throughputresponse.isreplacepending?view=azure-dotnet&preserve-view=true) no SDK do .net.
* [ThroughputResponse. isReplacePending ()](/java/api/com.azure.cosmos.models.throughputresponse.isreplacepending?view=azure-java-stable&preserve-view=true) no SDK do Java.

Você pode usar [Azure monitor métricas](monitor-cosmos-db.md#view-operation-level-metrics-for-azure-cosmos-db) para exibir o histórico de taxa de transferência provisionada (ru/s) e o armazenamento em um recurso.

## <a name="high-storage--low-throughput-program"></a><a id="high-storage-low-throughput-program"></a> Alto programa de armazenamento/baixa taxa de transferência

Conforme descrito na seção [atual de taxa de transferência provisionada](#current-provisioned-throughput) acima, a taxa de transferência mínima que você pode provisionar em um contêiner ou banco de dados depende de vários fatores. Uma delas é a quantidade de dados atualmente armazenados, pois Azure Cosmos DB impõe uma taxa de transferência mínima de 10 RU/s por GB de armazenamento.

Isso pode ser uma preocupação em situações em que você precisa armazenar grandes quantidades de dados, mas têm requisitos de baixa taxa de transferência em comparação. Para acomodar melhor esses cenários, Azure Cosmos DB introduziu um **programa de "alto armazenamento/baixa taxa de transferência"** que diminui a restrição de ru/s por GB em contas qualificadas.

Para participar deste programa e avaliar sua qualificação completa, tudo o que você precisa fazer é preencher [essa pesquisa](https://customervoice.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRzBPrdEMjvxPuDm8fCLUtXpUREdDU0pCR0lVVFY5T1lRVEhWNUZITUJGMC4u). Em seguida, a equipe de Azure Cosmos DB acompanhará e continuará a integração.

## <a name="comparison-of-models"></a>Comparação de modelos
Esta tabela mostra uma comparação entre a taxa de transferência padrão de provisionamento (manual) em um banco de dados em relação a um contêiner. 

|**Parâmetro**  |**Taxa de transferência padrão (manual) em um banco de dados**  |**Taxa de transferência padrão (manual) em um contêiner**|**Taxa de transferência de dimensionamento automático em um banco de dados** | **Taxa de transferência de dimensionamento automático em um contêiner**|
|---------|---------|---------|---------|---------|
|Ponto de entrada (mínimo de RU/s) |400 RU/s. Pode ter até 25 contêineres com no mínimo RU/s por contêiner.</li> |400| Dimensionamento automático entre 400 e 4000 RU/s. Pode ter até 25 contêineres com no mínimo RU/s por contêiner.</li> | Dimensionamento automático entre 400 e 4000 RU/s.|
|Mínimo de RUs por contêiner|--|400|--|Dimensionamento automático entre 400 e 4000 RU/s|
|Máximo de RUs|Ilimitado, no banco de dados.|Ilimitado, no contêiner.|Ilimitado, no banco de dados.|Ilimitado, no contêiner.
|RUs atribuídas ou disponíveis para um contêiner específico|Sem garantias. RUs atribuídas a um determinado contêiner dependem das propriedades. As propriedades podem ser a escolha de chaves de partição de contêineres que compartilham a taxa de transferência, a distribuição da carga de trabalho e o número de contêineres. |Todas as RUs configuradas no contêiner são reservadas exclusivamente ao contêiner.|Sem garantias. RUs atribuídas a um determinado contêiner dependem das propriedades. As propriedades podem ser a escolha de chaves de partição de contêineres que compartilham a taxa de transferência, a distribuição da carga de trabalho e o número de contêineres. |Todas as RUs configuradas no contêiner são reservadas exclusivamente ao contêiner.|
|Armazenamento máximo para um contêiner|Ilimitado.|Ilimitado|Ilimitado|Ilimitado|
|Taxa de transferência máxima por partição lógica de um contêiner|10 K RU/s|10 K RU/s|10 K RU/s|10 K RU/s|
|Taxa de transferência máxima (dados + índice) por partição lógica de um contêiner|20 GB|20 GB|20 GB|20 GB|

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [partições lógicas](partitioning-overview.md).
* Saiba como [provisionar a taxa de transferência padrão (manual) em um contêiner do Azure Cosmos DB](how-to-provision-container-throughput.md).
* Saiba como [provisionar a taxa de transferência padrão (manual) em um banco de dados do Azure Cosmos DB](how-to-provision-database-throughput.md).
* Saiba como [provisionar taxa de transferência de dimensionamento automático em um banco de dados ou contêiner do Azure Cosmos](how-to-provision-autoscale-throughput.md).
