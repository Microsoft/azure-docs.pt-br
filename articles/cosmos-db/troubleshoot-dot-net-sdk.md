---
title: Diagnosticar e solucionar problemas ao usar o SDK do .NET do Azure Cosmos DB
description: Use recursos como o registro em log do lado do cliente e outras ferramentas de terceiros para identificar, diagnosticar e solucionar problemas do Azure Cosmos DB usando o SDK do .NET.
author: j82w
ms.service: cosmos-db
ms.date: 05/28/2019
ms.author: jawilley
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: b9511562b81f7ac0c1582897d703f4c5ccb89716
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67806392"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-cosmos-db-net-sdk"></a>Diagnosticar e solucionar problemas ao usar o SDK do .NET do Azure Cosmos DB
Este artigo aborda problemas comuns, as soluções alternativas, etapas de diagnóstico e ferramentas quando você usa o [SDK do .NET](sql-api-sdk-dotnet.md) com contas da API de SQL do Azure Cosmos DB.
O SDK do .NET fornece uma representação lógica do lado do cliente para acessar a API de SQL do Azure Cosmos DB. Este artigo descreve as ferramentas e as abordagens para ajudá-lo se você tiver algum problema.

## <a name="checklist-for-troubleshooting-issues"></a>Lista de verificação para solução de problemas:
Considere a seguinte lista de verificação antes de mover seu aplicativo para produção. Usando a lista de verificação impedirá que vários problemas comuns que você pode ver. Você pode diagnosticar rapidamente quando ocorrer um problema:

*   Use a versão mais recente [SDK](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/changelog.md). SDKs de visualização não devem ser usados para produção. Isso impedirá a atingir os problemas conhecidos que já foram corrigidos.
*   Examine [dicas de desempenho](performance-tips.md) e siga as práticas sugeridas. Isso ajudará a evitar o dimensionamento, latência e outros problemas de desempenho.
*   Habilite o registro em log do SDK para ajudá-lo a solucionar um problema. Habilitar o registro em log pode afetar o desempenho, portanto, é melhor para habilitá-lo somente quando a solução de problemas. Você pode habilitar os logs a seguir:
    *   [Registrar métricas](monitor-accounts.md) por meio do portal do Azure. Métricas do portal mostram a telemetria do Azure Cosmos DB, que é útil para determinar se o problema corresponde ao Azure Cosmos DB ou se ele for do lado do cliente.
    *   Log de [cadeia de caracteres de diagnóstico](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring?view=azure-dotnet) entre as respostas de operação de ponto.
    *   Log de [métricas de consulta SQL](sql-api-query-metrics.md) de todas as respostas de consulta 
    *   Execute a instalação para [registro em log do SDK]( https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/docs/documentdb-sdk_capture_etl.md)

Dê uma olhada na seção [Problemas comuns e soluções alternativas](#common-issues-workarounds) neste artigo.

Verifique as [seção de problemas do GitHub](https://github.com/Azure/azure-cosmos-dotnet-v2/issues) que está sendo ativamente monitorado. Verifique se você encontrar algum problema semelhante com uma solução alternativa já arquivada. Se você não encontrar uma solução, em seguida, registre um problema do GitHub. Você pode abrir um tique de suporte para problemas urgentes.


## <a name="common-issues-workarounds"></a>Problemas comuns e soluções alternativas

### <a name="general-suggestions"></a>Sugestões gerais
* Execute seu aplicativo na mesma região do Azure com sua conta do Azure Cosmos DB, sempre que possível. 
* Você pode ter problemas de conectividade/disponibilidade devido à falta de recursos no computador cliente. Recomendamos monitorar a utilização da CPU em nós que executam o cliente do Azure Cosmos DB e escalar vertical/horizontalmente se elas estão em execução com carga elevada.

### <a name="check-the-portal-metrics"></a>Verifique as métricas do portal
Verificando a [métricas do portal](monitor-accounts.md) ajudará a determinar se é um problema do lado do cliente ou se houver um problema com o serviço. Por exemplo se as métricas contêm uma alta taxa de solicitações de taxa limitada (código de status HTTP 429) que significa que a solicitação está sendo limitada, em seguida, verificar a [taxa de solicitação muito grande] seção. 

### <a name="request-timeouts"></a>Tempos limite de solicitações
RequestTimeout geralmente ocorre ao usar o Direct/TCP, mas pode ocorrer no modo de Gateway. Essas são as causas conhecidas comuns e sugestões sobre como corrigir o problema.

* Utilização da CPU é alta, que irá fazer com que a latência de e/ou tempos limite da solicitação. O cliente pode escalar verticalmente a máquina host para dar a ele mais recursos ou a carga pode ser distribuída em mais computadores.
* Soquete / disponibilidade da porta pode ser baixa. Ao usar os SDKs do .NET anterior à versão 2.0, os clientes em execução no Azure podem atingir o [Esgotamento da porta SNAT (PAT) do Azure]. Este exemplo de por que é recomendável sempre executar a versão mais recente do SDK.
* Criando várias instâncias de DocumentClient pode levar à contenção de conexão e problemas de tempo limite. Siga as [dicas de desempenho](performance-tips.md)e use uma única instância de DocumentClient em todo o processo.
* Os usuários veem, às vezes, tempos limite de latência ou a solicitação com privilégios elevados, porque suas coleções são provisionadas de forma insuficientemente, o back-end restringe as solicitações e o cliente tentará novamente internamente sem identificando isso ao chamador. Verifique as [métricas do portal](monitor-accounts.md).
* O Azure Cosmos DB distribui a produtividade provisionada uniformemente entre partições físicas. Verifique as métricas do portal para ver se a carga de trabalho está encontrando um hot [chave de partição](partition-data.md). Isso fará com que a produtividade agregada consumida (RU/s) para ser parece estar sob o RUs provisionado, mas uma única partição consumida taxa de transferência (RU/s) excederá a taxa de transferência provisionada. 
* Além disso, o SDK 2.0 adiciona semântica de canal para conexões direct/TCP. Uma conexão de TCP é usado para várias solicitações ao mesmo tempo. Isso pode levar a problemas de dois em casos específicos:
    * Um alto grau de simultaneidade pode levar à contenção no canal.
    * Grandes solicitações ou respostas podem levar ao bloqueio do cabeçalho de linha no canal e aumentam a contenção, mesmo com um nível relativamente baixo de simultaneidade.
    * Se o caso de ficar em qualquer uma dessas duas categorias (ou se houver suspeita de alta utilização da CPU), essas são as possíveis atenuações:
        * Tente dimensionar o aplicativo vertical/horizontalmente.
        * Além disso, os logs do SDK podem ser capturados por meio [ouvinte de rastreamento](https://github.com/Azure/azure-cosmosdb-dotnet/blob/master/docs/documentdb-sdk_capture_etl.md) para obter mais detalhes.

### <a name="connection-throttling"></a>Limitação de conexão
A limitação de Conexão pode ocorrer devido a um limite de conexão em um computador host. Anteriores ao 2.0, os clientes em execução no Azure podem atingir o [Esgotamento da porta SNAT (PAT) do Azure].

### <a name="snat"></a>Esgotamento da porta SNAT (PAT) do Azure

Se o seu aplicativo for desenvolvido nas Máquinas Virtuais do Microsoft Azure sem um endereço IP público, por padrão as [portas do Azure SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports) estabelecem conexões a qualquer ponto de extremidade fora da sua VM. O número de conexões permitidas da VM para o ponto de extremidade do Azure Cosmos DB é limitado pela [configuração do Azure SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports).

 As portas Azure SNAT são usadas somente quando sua VM do Azure tiver um endereço IP privado e um processo da VM tenta estabelecer uma conexão com um endereço IP público. Há duas soluções alternativas para evitar a limitação do Azure SNAT:

* Adicione seu ponto de extremidade de serviço do Azure Cosmos DB para a sub-rede da sua rede virtual de Máquinas Virtuais do Azure. Para saber mais, consulte [pontos de extremidade de serviço de Rede Virtual do Microsoft Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview). 

    Quando o ponto de extremidade for habilitado, as solicitações não são mais enviadas de um IP público para o Azure Cosmos DB. Em vez disso, a rede virtual e a identidade de sub-rede são enviadas. Essa alteração poderá resultar em quedas de firewall se apenas IPs públicos forem permitidos. Se você usar um firewall, quando você habilitar o ponto de extremidade de serviço, adicione uma sub-rede para o firewall usando as [ACLs de Rede Virtual](https://docs.microsoft.com/azure/virtual-network/virtual-networks-acl).
* Atribua um IP público à sua VM do Azure.

### <a name="http-proxy"></a>Proxy HTTP
Se você usar um proxy HTTP, certifique-se que pode suportar o número de conexões configuradas no SDK `ConnectionPolicy`.
Caso contrário, você enfrentará problemas de conexão.

### Taxa de solicitação muito grande<a name="request-rate-too-large"></a>
Muito grande de taxa de solicitação ou o código de erro 429 indica que as solicitações estão sendo limitadas, porque a taxa de transferência (RU/s) consumida excedeu a taxa de transferência provisionada. O SDK automaticamente tentará solicitações com base em especificado [política de repetição](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.retryoptions?view=azure-dotnet). Se você receber essa falha com frequência, considere aumentar a taxa de transferência da coleção. Verifique as [métricas do portal](use-metrics.md) para ver se você estiver recebendo 429 erros. Examine sua [chave de partição](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey) para garantir que ele resulta em uma distribuição uniforme de volume de armazenamento e a solicitação. 

### <a name="slow-query-performance"></a>Desempenho de consultas lentas
O [consultar métricas](sql-api-query-metrics.md) ajudará a determinar onde a consulta passa a maior parte do tempo. As métricas de consulta, você pode ver quanto ela está sendo gasto no vs back-end do cliente.
* Se a consulta de back-end retorna rapidamente e gasta um tempo grande no cliente Verifique a carga no computador. É provável que não há recursos suficientes e o SDK está aguardando recursos estejam disponíveis para tratar da resposta.
* Se a consulta de back-end estiver lenta, tente [otimizar a consulta](optimize-cost-queries.md) e observando atual [política de indexação](index-overview.md) 

 <!--Anchors-->
[Common issues and workarounds]: #common-issues-workarounds
[Enable client SDK logging]: #logging
[Taxa de solicitação muito grande]: #request-rate-too-large
[Request Timeouts]: #request-timeouts
[Esgotamento da porta SNAT (PAT) do Azure]: #snat
[Production check list]: #production-check-list


