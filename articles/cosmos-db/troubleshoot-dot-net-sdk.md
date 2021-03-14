---
title: Diagnosticar e solucionar problemas ao usar o SDK . NET para Azure Cosmos DB
description: Use recursos como registro em log do lado do cliente e outras ferramentas de terceiros para identificar, diagnosticar e solucionar problemas de Azure Cosmos DB ao usar o SDK do .NET.
author: anfeldma-ms
ms.service: cosmos-db
ms.date: 03/05/2021
ms.author: anfeldma
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.custom: devx-track-dotnet
ms.openlocfilehash: 1f7548b355353eb77419f4d1760b40ba02eeddda
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102442189"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-cosmos-db-net-sdk"></a>Diagnosticar e solucionar problemas ao usar o SDK . NET para Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [SDK do Java v4](troubleshoot-java-sdk-v4-sql.md)
> * [SDK do Java Assíncrono v2](troubleshoot-java-async-sdk.md)
> * [.NET](troubleshoot-dot-net-sdk.md)
> 

Este artigo aborda problemas comuns, soluções alternativas, etapas de diagnóstico e ferramentas quando você usa o [SDK do .net](sql-api-sdk-dotnet.md) com Azure Cosmos DB contas da API do SQL.
O SDK do .NET fornece a representação lógica do lado do cliente para acessar a API do SQL do Azure Cosmos DB. Este artigo descreve as ferramentas e as abordagens para ajudá-lo se você tiver algum problema.

## <a name="checklist-for-troubleshooting-issues"></a>Lista de verificação para solucionar problemas

Considere a seguinte lista de verificação antes de mover seu aplicativo para produção. O uso da lista de verificação impedirá vários problemas comuns que você possa ver. Você também pode diagnosticar rapidamente quando ocorrer um problema:

*    Use o [SDK](sql-api-sdk-dotnet-standard.md)mais recente. Os SDKs de visualização não devem ser usados para produção. Isso evitará o pressionamento de problemas conhecidos que já foram corrigidos.
*    Examine [dicas de desempenho](performance-tips.md) e siga as práticas sugeridas. Isso ajudará a evitar o dimensionamento, latência e outros problemas de desempenho.
*    Habilite o log do SDK para ajudá-lo a solucionar um problema. Habilitar o registro em log pode afetar o desempenho para que seja melhor habilitá-lo somente ao solucionar problemas. Você pode habilitar os seguintes logs:
*    [Métricas de log](./monitor-cosmos-db.md) usando o portal do Azure. As métricas do portal mostram a telemetria do Azure Cosmos DB, o que é útil para determinar se o problema corresponde a Azure Cosmos DB ou se ele é do lado do cliente.
*    Registre a [cadeia de caracteres de diagnóstico](/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring) no SDK v2 ou [diagnóstico](/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics) no SDK v3 das respostas de operação de ponto.
*    Registrar as [métricas de consulta SQL](sql-api-query-metrics.md) de todas as respostas de consulta 
*    Siga a configuração para o [log do SDK]( https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/docs/documentdb-sdk_capture_etl.md)

Dê uma olhada na seção [Problemas comuns e soluções alternativas](#common-issues-workarounds) neste artigo.

Verifique a [seção de problemas do GitHub](https://github.com/Azure/azure-cosmos-dotnet-v2/issues) monitorado ativamente. Verifique se você encontrar algum problema semelhante com uma solução alternativa já arquivada. Se você não encontrar uma solução, então, execute um problema do GitHub. Você pode abrir um tique de suporte para problemas urgentes.


## <a name="common-issues-and-workarounds"></a><a name="common-issues-workarounds"></a>Problemas comuns e soluções alternativas

### <a name="general-suggestions"></a>Sugestões gerais
* Execute seu aplicativo na mesma região do Azure que sua conta de Azure Cosmos DB, sempre que possível. 
* Você pode encontrar problemas de conectividade/disponibilidade devido à falta de recursos no computador cliente. É recomendável monitorar a utilização da CPU em nós que executam o cliente Azure Cosmos DB e escalar verticalmente se eles estiverem sendo executados com alta carga.

### <a name="check-the-portal-metrics"></a>Verificar as métricas do portal
Verificar as [métricas do portal](./monitor-cosmos-db.md) ajudará a determinar se é um problema do lado do cliente ou se há um problema com o serviço. Por exemplo, se as métricas contiverem uma alta taxa de solicitações limitadas por taxa (código de status HTTP 429), o que significa que a solicitação está sendo limitada e, em seguida, verifique a seção [taxa de solicitação muito grande](troubleshoot-request-rate-too-large.md) . 

## <a name="retry-logic"></a>Lógica de repetição <a id="retry-logics"></a>
O SDL do Cosmos DB em qualquer falha de E/S tentará repetir a operação com falha se a nova tentativa no SDK for viável. Ter uma nova tentativa em vigor para qualquer falha é uma boa prática, mas o tratamento e a repetição de falhas de gravação é necessário. É recomendável usar o SDK mais recente, pois a lógica de repetição está sendo continuamente aprimorada.

1. As falhas de leitura e consulta de e/s serão repetidas pelo SDK sem identificando-las ao usuário final.
2. As gravações (criar, Upsert, substituir, excluir) são "não" idempotentes e, portanto, o SDK nem sempre pode repetir as operações de gravação com falha. É necessário que a lógica do aplicativo do usuário manipule a falha e tente novamente.
3. A [disponibilidade do SDK de solução de problemas](troubleshoot-sdk-availability.md) explica as repetições para contas de Cosmos DB de várias regiões.

## <a name="common-error-status-codes"></a>Códigos de status de erro comuns <a id="error-codes"></a>

| Código de status | Descrição | 
|----------|-------------|
| 400 | Solicitação inadequada (depende da mensagem de erro)| 
| 401 | [Não autorizado](troubleshoot-unauthorized.md) | 
| 403 | [Proibido](troubleshoot-forbidden.md) |
| 404 | [Recurso não encontrado](troubleshoot-not-found.md) |
| 408 | [Tempo limite da solicitação expirado](troubleshoot-dot-net-sdk-request-timeout.md) |
| 409 | Falha de conflito é quando a ID fornecida para um recurso em uma operação de gravação foi realizada por um recurso existente. Use outra ID para o recurso para resolver esse problema, pois a ID deve ser exclusiva dentro de todos os documentos com o mesmo valor de chave de partição. |
| 410 | Exceções desexistentes (falha transitória que não deve violar o SLA) |
| 412 | A falha de pré-condição é onde a operação especificou uma eTag que é diferente da versão disponível no servidor. É um erro de simultaneidade otimista. Repita a solicitação depois de ler a versão mais recente do recurso e atualizar o eTag na solicitação.
| 413 | [Entidade de solicitação muito grande](concepts-limits.md#per-item-limits) |
| 429 | [Número excessivo de solicitações](troubleshoot-request-rate-too-large.md) |
| 449 | Erro transitório que ocorre apenas em operações de gravação e é seguro tentar novamente |
| 500 | A operação falhou devido a um erro de serviço inesperado. Entre em contato com o suporte. Confira como arquivar um [problema de suporte do Azure](https://aka.ms/azure-support). |
| 503 | [Serviço indisponível](troubleshoot-service-unavailable.md) | 

### <a name="azure-snat-pat-port-exhaustion"></a><a name="snat"></a>Esgotamento da porta SNAT (PAT) do Azure

Se seu aplicativo for implantado em [máquinas virtuais do Azure sem um endereço IP público](../load-balancer/load-balancer-outbound-connections.md), por padrão, [as portas SNAT do Azure](../load-balancer/load-balancer-outbound-connections.md#preallocatedports) estabelecerão conexões com qualquer ponto de extremidade fora de sua VM. O número de conexões permitidas da VM para o ponto de extremidade do Azure Cosmos DB é limitado pela [configuração do Azure SNAT](../load-balancer/load-balancer-outbound-connections.md#preallocatedports). Essa situação pode levar à limitação de conexão, fechamento de conexão ou aos [tempos limite de solicitação](troubleshoot-dot-net-sdk-request-timeout.md)mencionados acima.

 As portas SNAT do Azure são usadas somente quando sua VM tem um endereço IP privado está se conectando a um endereço IP público. Há duas soluções alternativas para evitar a limitação de SNAT do Azure (desde que você já esteja usando uma única instância de cliente em todo o aplicativo):

* Adicione seu ponto de extremidade de serviço do Azure Cosmos DB para a sub-rede da sua rede virtual de Máquinas Virtuais do Azure. Para saber mais, consulte [pontos de extremidade de serviço de Rede Virtual do Microsoft Azure](../virtual-network/virtual-network-service-endpoints-overview.md). 

    Quando o ponto de extremidade for habilitado, as solicitações não são mais enviadas de um IP público para o Azure Cosmos DB. Em vez disso, a rede virtual e a identidade de sub-rede são enviadas. Essa alteração poderá resultar em quedas de firewall se apenas IPs públicos forem permitidos. Se você usar um firewall, quando você habilitar o ponto de extremidade de serviço, adicione uma sub-rede para o firewall usando as [ACLs de Rede Virtual](/previous-versions/azure/virtual-network/virtual-networks-acl).
* Atribua um [IP público à sua VM do Azure](../load-balancer/troubleshoot-outbound-connection.md#assignilpip).

### <a name="high-network-latency"></a><a name="high-network-latency"></a>Alta latência de rede
Alta latência de rede pode ser identificada usando a [cadeia de caracteres de diagnóstico](/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring) no SDK v2 ou [diagnóstico](/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics#Microsoft_Azure_Cosmos_ResponseMessage_Diagnostics) no SDK v3.

Se nenhum [tempo limite](troubleshoot-dot-net-sdk-request-timeout.md) estiver presente e o diagnóstico mostrar solicitações únicas em que a alta latência é evidente.

# <a name="v3-sdk"></a>[SDK DO V3](#tab/diagnostics-v3)

O diagnóstico pode ser obtido de qualquer `ResponseMessage` , `ItemResponse` , `FeedResponse` ou `CosmosException` pela `Diagnostics` Propriedade:

```csharp
ItemResponse<MyItem> response = await container.CreateItemAsync<MyItem>(item);
Console.WriteLine(response.Diagnostics.ToString());
```

As interações de rede no diagnóstico serão, por exemplo:

```json
{
    "name": "Microsoft.Azure.Documents.ServerStoreModel Transport Request",
    "id": "0e026cca-15d3-4cf6-bb07-48be02e1e82e",
    "component": "Transport",
    "start time": "12: 58: 20: 032",
    "duration in milliseconds": 1638.5957
}
```

Onde o `duration in milliseconds` mostraria a latência.

# <a name="v2-sdk"></a>[V2 SDK](#tab/diagnostics-v2)

Os diagnósticos estão disponíveis quando o cliente é configurado no [modo direto](sql-sdk-connection-modes.md), por meio da `RequestDiagnosticsString` Propriedade:

```csharp
ResourceResponse<Document> response = await client.ReadDocumentAsync(documentLink, new RequestOptions() { PartitionKey = new PartitionKey(partitionKey) });
Console.WriteLine(response.RequestDiagnosticsString);
```

E a latência seria sobre a diferença entre `ResponseTime` e `RequestStartTime` :

```bash
RequestStartTime: 2020-03-09T22:44:49.5373624Z, RequestEndTime: 2020-03-09T22:44:49.9279906Z,  Number of regions attempted:1
ResponseTime: 2020-03-09T22:44:49.9279906Z, StoreResult: StorePhysicalAddress: rntbd://..., ...
```
--- 

Essa latência pode ter várias causas:

* Seu aplicativo não está em execução na mesma região que sua conta de Azure Cosmos DB.
* A configuração do [PreferredLocations](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations) ou do [ApplicationRegion](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationregion) está incorreta e está tentando se conectar a uma região diferente na qual seu aplicativo está sendo executado no momento.
* Pode haver um afunilamento na interface de rede devido ao alto tráfego. Se o aplicativo estiver em execução em máquinas virtuais do Azure, há possíveis soluções alternativas:
    * Considere usar uma [máquina virtual com rede acelerada habilitada](../virtual-network/create-vm-accelerated-networking-powershell.md).
    * Habilite [a rede acelerada em uma máquina virtual existente](../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms).
    * Considere o uso de uma [máquina virtual de extremidade superior](../virtual-machines/sizes.md).

### <a name="common-query-issues"></a>Problemas comuns de consulta

As [métricas de consulta](sql-api-query-metrics.md) ajudarão a determinar onde a consulta está sendo gasta na maior parte do tempo. A partir das métricas de consulta, você pode ver quanto ele está sendo gasto no back-end em vez do cliente. Saiba mais sobre como [solucionar problemas de desempenho de consulta](troubleshoot-query-performance.md).

* Se a consulta de back-end retornar rapidamente e passar um tempo grande no cliente, verifique a carga no computador. É provável que não haja recursos suficientes e o SDK esteja aguardando que os recursos estejam disponíveis para lidar com a resposta.
* Se a consulta de back-end estiver lenta, tente [otimizar a consulta](troubleshoot-query-performance.md) e examinar a política de [indexação](index-overview.md) atual

    > [!NOTE]
    > Para aprimorar o desempenho, recomendamos o processamento de host do Windows de 64 bits. O SDK do SQL inclui um ServiceInterop.dll nativo para analisar e otimizar as consultas localmente. O ServiceInterop.dll tem suporte apenas na plataforma Windows x64. Para Linux e outras plataformas sem suporte em que o ServiceInterop.dll não está disponível, uma chamada de rede adicional será feita ao gateway para obter a consulta otimizada.

Se você encontrar o seguinte erro: `Unable to load DLL 'Microsoft.Azure.Cosmos.ServiceInterop.dll' or one of its dependencies:` e estiver usando o Windows, deverá atualizar para a versão mais recente do Windows.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre as diretrizes de desempenho para o [.net v3](performance-tips-dotnet-sdk-v3-sql.md) e o [.net v2](performance-tips.md)
* Saiba mais sobre os [SDKs do Java baseados em reator](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-pattern-guide.md)

 <!--Anchors-->
[Common issues and workarounds]: #common-issues-workarounds
[Enable client SDK logging]: #logging
[Azure SNAT (PAT) port exhaustion]: #snat
[Production check list]: #production-check-list