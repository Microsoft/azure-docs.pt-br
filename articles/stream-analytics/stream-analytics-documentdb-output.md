---
title: "Saída JSON para o Stream Analytics | Microsoft Docs"
description: "Saiba como o Stream Analytics pode direcionar o Azure Cosmos DB para uma saída em JSON, para arquivamento de dados e consultas de baixa latência em dados JSON não estruturados."
keywords: "Saída em JSON"
documentationcenter: 
services: stream-analytics,documentdb
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 5d2a61a6-0dbf-4f1b-80af-60a80eb25dd1
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: samacha
ms.openlocfilehash: cc80b0080c806541362a1ef2d71b95862bd51ca2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="target-azure-cosmos-db-for-json-output-from-stream-analytics"></a>Direcionar o Azure Cosmos DB para uma saída em JSON no Stream Analytics
O Stream Analytics pode direcionar o [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) para uma saída em JSON, possibilitando o arquivamento de dados e consultas de baixa latência em dados JSON não estruturados. Este documento aborda algumas práticas recomendadas para implementar essa configuração.

Para aqueles que não estão familiarizados com o Cosmos DB, consulte [Roteiro de aprendizagem do Azure Cosmos DB](https://azure.microsoft.com/documentation/learning-paths/documentdb/) para obter uma introdução. 

Observação: atualmente, não há suporte para coleções do Cosmos DB baseados na API do Mongo DB. 

## <a name="basics-of-cosmos-db-as-an-output-target"></a>Conceitos básicos do Cosmos DB como um destino de saída
A saída do Azure Cosmos DB no Stream Analytics permite a gravação dos resultados do processamento de fluxo como uma saída em JSON nas coleções do Cosmos DB. O Stream Analytics não cria coleções em seu banco de dados, em vez disso, ele exige a criação delas antecipadamente. Isso serve para que os custos de cobrança das coleções de Cosmos DB sejam transparentes e para que você possa ajustar o desempenho, a consistência e a capacidade de suas coleções diretamente com as [APIs do Cosmos DB](https://msdn.microsoft.com/library/azure/dn781481.aspx). Recomendamos o uso de um Banco de Dados do Cosmos DB por trabalho de streaming, a fim de separar logicamente as coleções de um trabalho de streaming.

Algumas das opções de coleção do Cosmos DB são detalhadas abaixo.

## <a name="tune-consistency-availability-and-latency"></a>Ajustar a consistência, a disponibilidade e a latência
Para atender aos requisitos do aplicativo, o Cosmos DB permite o ajuste do banco de dados e das coleções, além de compensações entre consistência, disponibilidade e latência. Dependendo dos níveis de consistência de leitura exigidos pelo seu cenário em relação à latência de leitura e de gravação, você poderá escolher um nível de consistência em sua conta de banco de dados. Também por padrão, o Cosmos DB habilita a indexação síncrona em cada operação CRUD da coleção. Essa é outra opção útil para controlar o desempenho de leitura/gravação no Cosmos DB. Para saber mais sobre esse tópico, confira o artigo [Alterar os níveis de consistência do banco de dados e de consulta](../documentdb/documentdb-consistency-levels.md) .

## <a name="upserts-from-stream-analytics"></a>Inserções e atualizações a partir do Stream Analytics
A integração do Stream Analytics ao Cosmos DB permite inserir ou atualizar registros na coleção do Cosmos DB de acordo com determinada coluna de ID do Documento. Isso também é chamado de *Upsert*.

O Stream Analytics utiliza uma abordagem Upsert otimista, na qual as atualizações são feitas somente quando a inserção falha devido a um conflito de ID do Documento. Essa atualização é executada pelo Stream Analytics como um PATCH. Assim, é possível realizar atualizações parciais no documento, ou seja, a adição de novas propriedades ou a substituição de uma propriedade existente é executada de forma incremental. Observe que as alterações nos valores de propriedades de matriz em seu documento JSON resultam na substituição de toda a matriz, ou seja, a matriz não é mesclada.

## <a name="data-partitioning-in-cosmos-db"></a>Particionamento de dados no Cosmos DB
As [coleções particionadas](../cosmos-db/partition-data.md) do Cosmos DB são a abordagem recomendada para particionar os dados. 

Para coleções individuais do Cosmos DB, o Stream Analytics ainda permite particionar os dados de acordo com os padrões de consulta e as necessidades de desempenho do aplicativo. Cada coleção pode conter até 10 GB de dados (máximo) e, atualmente, não há uma maneira de escalar verticalmente (ou estourar) uma coleção. Para dimensionamento, o Stream Analytics permite que você grave várias coleções com um determinado prefixo (veja os detalhes de uso abaixo). O Stream Analytics usa a estratégia de [Resolvedor de Partição Hash](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.hashpartitionresolver.aspx) consistente com base na coluna PartitionKey fornecida pelo usuário para particionar seus registros de saída. O número de coleções com o prefixo especificado na hora de início do trabalho de streaming é usado como a contagem de partições de saída, nas quais o trabalho grava em paralelo (Coleções do Cosmos DB = Partições de Saída). Para uma coleção individual com indexação lenta que realiza apenas inserções, é possível esperar uma produtividade de gravação de cerca de 0,4 MB/s. O uso de várias coleções permite alcançar maior produtividade e maior capacidade.

Se você pretender aumentar a contagem de partições no futuro, talvez seja necessário interromper seu trabalho, reparticionar os dados de suas coleções existentes em novas coleções e reiniciar o trabalho do Stream Analytics. Incluiremos mais detalhes sobre como usar PartitionResolver e o novo particionamento, junto com um exemplo de código, em uma próxima postagem. O artigo [Particionamento e escala no Cosmos DB](../documentdb/documentdb-partition-data.md) também apresenta detalhes sobre isso.

## <a name="cosmos-db-settings-for-json-output"></a>Configurações do Cosmos DB para saída em JSON
A criação do Cosmos DB como uma saída no Stream Analytics gera uma solicitação de informações, conforme mostrado abaixo. Esta seção fornece uma explicação da definição de propriedades.

Coleção particionada | Várias coleções de “partição única”
---|---
![tela de saída do stream analytics do banco de dados de documentos](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png) |  ![tela de saída do stream analytics do banco de dados de documentos](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-2.png)


  
> [!NOTE]
> O cenário **Várias coleções de “partição única”** requer uma chave de partição e é uma configuração com suporte. 

* **Alias de Saída** : um alias para se referir a essa saída em sua consulta ASA  
* **Nome da Conta** – o nome ou o URI do ponto de extremidade da conta do Cosmos DB.  
* **Chave de Conta** – a chave de acesso compartilhado da conta do Cosmos DB.  
* **Banco de Dados** – o nome do banco de dados do Cosmos DB.  
* **Padrão do Nome de Coleção** – O nome da coleção ou seu padrão que será usado para as coleções. O formato de nome da coleção pode ser construído com o token {partição} opcional, em que as partições começam em 0. A seguir estão as entradas válidas de exemplo:  
  1\) MyCollection – uma coleção denominada “MyCollection” deve existir.  
  2\) MyCollection{partition} – estas coleções devem existir – "MyCollection0”, “MyCollection1”, “MyCollection2” e assim por diante.  
* **Chave de Partição** — opcional. Isso só será necessário se você estiver usando um token {partition} no seu padrão de nome de coleção. O nome do campo nos eventos de saída usado para especificar a chave para o particionamento de saída em várias coleções. Para uma saída de coleção única, nenhuma coluna de saída arbitrária pode ser usada, por exemplo, PartitionId.  
* **ID do Documento** : opcional. O nome do campo em eventos de saída usado para especificar a chave primária que serve de base para as operações de inserção ou atualização.  
