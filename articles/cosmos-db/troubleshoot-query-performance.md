---
title: Diagnosticar e solucionar problemas de consulta ao usar o Azure Cosmos DB
description: Saiba como identificar, diagnosticar e solucionar problemas de consulta SQL do Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 07/10/2019
ms.author: girobins
ms.subservice: cosmosdb-sql
ms.reviewer: sngun
ms.openlocfilehash: 079e8677febfe6683d4f0e60a0e7ba6b06ea549d
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67835829"
---
# <a name="troubleshoot-query-performance-for-azure-cosmos-db"></a>Solucionar problemas de desempenho de consulta para o Azure Cosmos DB
Este artigo aborda como identificar, diagnosticar e solucionar problemas de consulta SQL do Azure Cosmos DB. Para obter o desempenho ideal para consultas do Azure Cosmos DB, siga as etapas de solução de problemas abaixo. 

## <a name="collocate-clients-in-same-azure-region"></a>Colocar os clientes na mesma região do Azure 
A menor latência possível é alcançada garantindo que o aplicativo de chamada está localizado na mesma região do Azure do ponto de extremidade do Azure Cosmos DB provisionado. Para obter uma lista de regiões disponíveis, consulte [regiões do Azure](https://azure.microsoft.com/global-infrastructure/regions/#services) artigo.

## <a name="check-consistency-level"></a>Verificar o nível de consistência
[Nível de consistência](consistency-levels.md) pode afetar o desempenho e os encargos. Verifique se que o nível de consistência é apropriado para o cenário dado. Para obter mais detalhes, consulte [escolhendo o nível de consistência](consistency-levels-choosing.md).

## <a name="log-query-metrics"></a>Métricas de consulta do log
Use `QueryMetrics` para solucionar problemas de consultas lentas ou caras. 

  * Definir `FeedOptions.PopulateQueryMetrics = true` ter `QueryMetrics` na resposta.
  * `QueryMetrics` classe tem uma sobrecarga `.ToString()` função pode ser invocada para obter a representação de cadeia de caracteres de `QueryMetrics`. 
  * As métricas podem ser utilizadas para derivar as informações a seguir, entre outros: 
  
      * Se qualquer componente específico do pipeline de consulta levou anormalmente longo para ser concluída (em ordem de centenas de milissegundos ou mais). 

          * Examinar `TotalExecutionTime`.
          * Se o `TotalExecutionTime` da consulta é menor que o tempo de execução de ponta a ponta e, em seguida, o tempo é gasto no lado do cliente ou de rede. Verifique uma segunda vez que o cliente e a região do Azure são colocados.
      
      * Se havia falsos positivos nos documentos analisados (se a contagem de documentos de saída é muito menor do que o contagem de documentos recuperados).  

          * Examinar `Index Utilization`.
          * `Index Utilization` = (Número de retornado documentos / carregado do número de documentos)
          * Se o número de documentos retornados é muito menor do que o número de carregado, falsos positivos estão sendo analisados.
          * Limite o número de documentos que estão sendo recuperados com filtros mais estreitos.  

      * Como individuais ida e volta dos (consulte a `Partition Execution Timeline` da representação de cadeia de caracteres de `QueryMetrics`). 
      * Se a consulta consumiu encargo de solicitação alta. 

Para obter mais detalhes, consulte [como obter métricas de execução de consulta SQL](profile-sql-api-query.md) artigo.
      
## <a name="tune-query-feed-options-parameters"></a>Ajustar parâmetros de opções do Feed de consulta 
Desempenho de consulta pode ser ajustado por meio da solicitação [opções do Feed](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.feedoptions?view=azure-dotnet) parâmetros. Tente definir as opções abaixo:

  * Definir `MaxDegreeOfParallelism` primeiro-1 e, em seguida, compare o desempenho entre valores diferentes. 
  * Definir `MaxBufferedItemCount` primeiro-1 e, em seguida, compare o desempenho entre valores diferentes. 
  * Definir `MaxItemCount` como -1.

Ao comparar o desempenho de valores diferentes, tente valores como 2, 4, 8, 16 e outras pessoas.
 
## <a name="read-all-results-from-continuations"></a>Ler todos os resultados de continuações
Se você achar que você não estiver obtendo todos os resultados, certifique-se esvaziar a continuação totalmente. Em outras palavras, mantenha os resultados de leitura, enquanto o token de continuação tem mais documentos para produzir.

Drenagem totalmente pode ser feito com qualquer um dos seguintes padrões:

  * Continue resultados de processamento enquanto continuação não está vazia.
  * Continue o processamento enquanto a consulta tiver mais resultados. 

    ```csharp
    // using AsDocumentQuery you get access to whether or not the query HasMoreResults
    // If it does, just call ExecuteNextAsync until there are no more results
    // No need to supply a continuation token here as the server keeps track of progress
    var query = client.CreateDocumentQuery<Family>(collectionLink, options).AsDocumentQuery();
    while (query.HasMoreResults)
    {
        foreach (Family family in await query.ExecuteNextAsync())
        {
            families.Add(family);
        }
    }
    ```

## <a name="choose-system-functions-that-utilize-index"></a>Escolher funções de sistema que utilizam o índice
Se a expressão pode ser convertida em um intervalo de valores de cadeia de caracteres, em seguida, ele pode utilizar o índice; Caso contrário, ele não é possível. 

Aqui está a lista de funções de cadeia de caracteres que podem utilizar o índice: 
    
  * STARTSWITH(str_expr, str_expr) 
  * LEFT(str_expr, num_expr) = str_expr 
  * SUBSTRING(str_expr, num_expr, num_expr) = str_expr, but only if first num_expr is 0 
    
    Aqui estão alguns exemplos de consulta: 
    
    ```sql

    -- If there is a range index on r.name, STARTSWITH will utilize the index while ENDSWITH won't 
    SELECT * 
    FROM c 
    WHERE STARTSWITH(c.name, 'J') AND ENDSWITH(c.name, 'n')

    ```
    
    ```sql

    -- LEFT will utilize the index while RIGHT won't 
    SELECT * 
    FROM c 
    WHERE LEFT(c.name, 2) = 'Jo' AND RIGHT(c.name, 2) = 'hn'

    ```

  * Evite funções do sistema no filtro (ou a cláusula WHERE) que não são atendidas pelo índice. Alguns exemplos de tais funções de sistema incluem Contains, superior, inferior.
  * Quando possível, codifique consultas para usar um filtro na chave de partição.
  * Para alcançar alto desempenho consultas evitar chamar maiuscula/minúscula no filtro. Em vez disso, normalize maiusculas e minúsculas dos valores após a inserção. Para cada um dos valores de inserir o valor com maiusculas e minúsculas desejado ou inserir o valor original e o valor com o uso de maiusculas e minúsculas desejado. 

    Por exemplo:
    
    ```sql

    SELECT * FROM c WHERE UPPER(c.name) = "JOE"

    ```
    
    Nesse caso, o repositório "JOE" em letras maiusculas ou armazenar o valor original de "Joe" e "JOE". 
    
    Se as maiusculas e minúsculas de dados JSON são normalizadas, a consulta se torna:
    
    ```sql

    SELECT * FROM c WHERE c.name = "JOE"

    ```

    A segunda consulta será mais eficaz, pois ele não requer a execução de transformações em cada um dos valores para comparar os valores para "JOE".

Para a função de sistema mais detalhes, consulte [funções de sistema](sql-query-system-functions.md) artigo.

## <a name="check-indexing-policy"></a>Verificar a política de indexação
Para verificar se o atual [política de indexação](index-policy.md) é ideal:

  * Certifique-se de que todos os caminhos JSON usados em consultas que estão incluídos na política de indexação para leituras mais rápidas.
  * Exclua caminhos não usados em consultas para obter mais gravações de alto desempenho.

Para obter mais detalhes, consulte [como para gerenciar política de indexação](how-to-manage-indexing-policy.md) artigo.

## <a name="spatial-data-check-ordering-of-points"></a>Dados espaciais: Verifique a ordem dos pontos
Os pontos em um Polígono devem ser especificados no sentido anti-horário. Um Polígono especificado no sentido horário representa o inverso da região dentro dele.

## <a name="optimize-join-expressions"></a>Otimizar expressões de junção
`JOIN` expressões podem expandir para vários produtos grandes. Quando possível, a consulta em uma pesquisa menor espaço por meio de um filtro mais estreito.

Subconsultas com vários valores podem otimizar `JOIN` expressões por push predicados após cada expressão select-muitos em vez de após todas as junções cruzadas no `WHERE` cláusula. Para obter um exemplo detalhado, consulte [otimizar Join a expressões](https://docs.microsoft.com/azure/cosmos-db/sql-query-subquery#optimize-join-expressions) artigo.

## <a name="optimize-order-by-expressions"></a>Otimizar expressões ORDER BY 
`ORDER BY` desempenho de consulta poderá ser afetado se os campos são esparsos ou não incluídos na política de índice.

  * Para campos esparsos, como tempo, reduzir o espaço de pesquisa tanto quanto possível com filtros. 
  * Propriedade única `ORDER BY`, incluir a propriedade na política de índice. 
  * Para a propriedade de vários `ORDER BY` expressões, definir um [índice composto](https://docs.microsoft.com/azure/cosmos-db/index-policy#composite-indexes) em campos que estão sendo classificados.  

## <a name="many-large-documents-being-loaded-and-processed"></a>Muitos documentos grandes que está sendo carregado e processado
O tempo e o RUs necessárias por uma consulta não são apenas dependentes do tamanho da resposta, eles também são dependentes do trabalho é feito pelo pipeline de processamento de consulta. Tempo e RUs aumentam proporcionalmente com a quantidade de trabalho realizado pelo pipeline de processamento de consulta inteira. Mais trabalho é executado para documentos grandes, assim, mais tempo e RUs são necessárias para carregar e processar documentos grandes.

## <a name="low-provisioned-throughput"></a>Baixa taxa de transferência provisionada
Verifique se a taxa de transferência provisionada pode lidar com a carga de trabalho. Aumente o orçamento de RU para coleções afetados.

## <a name="try-upgrading-to-the-latest-sdk-version"></a>Tente atualizar para a versão mais recente do SDK
Para determinar o SDK mais recente, consulte [Download do SDK e notas de versão](sql-api-sdk-dotnet.md) artigo.

## <a name="next-steps"></a>Próximas etapas
Consulte documentos abaixo sobre como medir RUs por consulta, obter estatísticas de execução para ajustar suas consultas e muito mais:

* [Obter métricas de execução de consulta SQL usando o SDK do .NET](profile-sql-api-query.md)
* [Ajustando o desempenho da consulta com o Azure Cosmos DB](sql-api-sql-query-metrics.md)
* [Dicas de desempenho do SDK do .NET](performance-tips.md)