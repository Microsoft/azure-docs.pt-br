---
title: Importação em massa e atualizar dados no Azure Cosmos DB usando a biblioteca de executor em massa
description: Execute as operações em massa no Azure Cosmos DB por meio da importação em massa e APIs oferecidas pela biblioteca de executor em massa de atualização em massa.
author: tknandu
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: ramkris
ms.reviewer: sngun
ms.openlocfilehash: e0176af0ce77e9306f6f0031122f8ba8a7b27e61
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60894452"
---
# <a name="azure-cosmos-db-bulk-executor-library-overview"></a>Visão geral da biblioteca bulk executor do Azure Cosmos DB
 
O Azure Cosmos DB é um serviço de banco de dados rápido, flexível e distribuído globalmente que foi projetado para elasticidade de expansão para dar suporte a: 

* Grandes taxas de transferência de leitura e gravação (em milhões de operações por segundo).  
* Armazenamento de grandes volumes de transações e dados operacionais (centenas de terabytes ou mais) com latência previsível de milissegundo.  

A biblioteca de executor em massa ajuda você a aproveitar essa enorme produtividade e armazenamento. A biblioteca de executor permite que você realize operações em massa no Azure Cosmos DB através da importar em massa e APIs de atualização em massa. Você pode ler mais sobre os recursos da biblioteca bulk executor nas seções a seguir. 

> [!NOTE] 
> Atualmente, a biblioteca de executor em massa dará suporte à importação e operações de atualização e essa biblioteca dará suporte apenas para contas de API de SQL do Azure Cosmos DB e API do Gremlin.
 
## <a name="key-features-of-the-bulk-executor-library"></a>Os principais recursos da biblioteca bulk executor  
 
* Reduz significativamente os recursos de computação do lado do cliente necessários para saturar a taxa de transferência alocada para um contêiner. Um aplicativo de thread único que grava dados usando a API de importação em massa atinge 10 vezes maior taxa de transferência de gravação quando comparado a um aplicativo multithread que grava dados em paralelo ao saturar a CPU do computador do cliente.  

* Abstrai tarefas entediantes de escrever uma lógica de aplicativo para lidar com a limitação de solicitação, tempos limite de solicitação e outras exceções transitórias tratando-os com eficiência na biblioteca.  

* Fornece um mecanismo simplificado para aplicativos que executam operações em massa de expansão. Uma única instância de executor em massa em execução em uma VM do Azure pode consumir mais que 500 K RU/s e você pode obter uma maior taxa de transferência adicionando instâncias adicionais nas VMs do cliente individual.  
 
* Pode importar em massa mais de um terabyte de dados em uma hora usando uma arquitetura de expansão.  

* Pode atualizar em massa dados existentes nos contêineres do Azure Cosmos DB como patches. 
 
## <a name="how-does-the-bulk-executor-operate"></a>Como funciona o BulkExecutor? 

Quando uma operação em massa para importar ou atualizar documentos é disparada com um lote de entidades, eles são inicialmente embaralhados em segmentos correspondentes aos seus intervalos de chaves de partição do Azure Cosmos DB. Dentro de cada partição que corresponde a um intervalo de chave de partição, eles são divididos em minilotes e cada minilote atua como uma carga que é confirmada no lado do servidor. A biblioteca bulk executor criou otimizações para execução simultânea desses minilotes dentro e entre os intervalos de chave de partição. A imagem a seguir ilustra como bulk executor divide os dados de lotes em chaves de partição diferentes:  

![Arquitetura do BulkExecutor](./media/bulk-executor-overview/bulk-executor-architecture.png)

A biblioteca BulkExecutor certifica-se de utilizar ao máximo a taxa de transferência alocada para uma coleção. Ela usa um  [mecanismo de controle de congestionamento do estilo AIMD](https://tools.ietf.org/html/rfc5681) para cada intervalo de chave de partição do Azure Cosmos DB para tratar com eficiência a limitação e os tempos limite. 

## <a name="next-steps"></a>Próximas etapas 
  
* Saiba mais experimentando os aplicativos de exemplo que consumem a biblioteca BulkExecutor em [.NET](bulk-executor-dot-net.md) e [Java](bulk-executor-java.md).  
* Confira as informações e notas de versões do SDK de bulk executor no [.NET](sql-api-sdk-bulk-executor-dot-net.md) e [Java](sql-api-sdk-bulk-executor-java.md).
* A biblioteca BulkExecutor é integrada ao conector Cosmos DB Spark. Para obter mais informações, consulte o artigo [conector Spark do Azure Cosmos DB](spark-connector.md).  
* A biblioteca bulk executor também é integrada a uma nova versão do [conector do Azure Cosmos DB](https://aka.ms/bulkexecutor-adf-v2) para o Azure Data Factory copiar dados.
