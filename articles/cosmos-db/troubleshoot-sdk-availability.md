---
title: Diagnosticar e solucionar problemas de disponibilidade de SDKs do Azure Cosmos em ambientes multiregiãois
description: Saiba tudo sobre o comportamento de disponibilidade do SDK do Azure Cosmos ao operar em ambientes de várias regiões.
author: ealsur
ms.service: cosmos-db
ms.date: 02/18/2021
ms.author: maquaran
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 0720eb01920e39a9bee27e4d00d97acba55b0ad5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101661419"
---
# <a name="diagnose-and-troubleshoot-the-availability-of-azure-cosmos-sdks-in-multiregional-environments"></a>Diagnosticar e solucionar problemas de disponibilidade de SDKs do Azure Cosmos em ambientes multiregiãois
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Este artigo descreve o comportamento da versão mais recente dos SDKs do Azure Cosmos quando você vê um problema de conectividade para uma determinada região ou quando ocorre um failover de região.

Todos os SDKs do Azure Cosmos oferecem uma opção para personalizar a preferência regional. As seguintes propriedades são usadas em diferentes SDKs:

* A propriedade [ConnectionPolicy. PreferredLocations](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations) no SDK do .net v2.
* As propriedades [CosmosClientOptions. ApplicationRegion](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationregion) ou [CosmosClientOptions. ApplicationPreferredRegions](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationpreferredregions) no SDK do .net v3.
* O método [CosmosClientBuilder. preferredRegions](/java/api/com.azure.cosmos.cosmosclientbuilder.preferredregions) no SDK do Java v4.
* O parâmetro [CosmosClient.preferred_locations](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient) no SDK do Python.
* O parâmetro [CosmosClientOptions. ConnectionPolicy. preferredLocations](/javascript/api/@azure/cosmos/connectionpolicy#preferredlocations) no SDK js.

Quando você definir a preferência regional, o cliente se conectará a uma região, conforme mencionado na tabela a seguir:

|Tipo de conta |Leituras |Gravações |
|------------------------|--|--|
| Região de gravação única | Região preferencial | Região primária  |
| Várias regiões de gravação | Região preferencial | Região preferencial  |

Se você **não definir uma região preferida**, o cliente SDK usa como padrão a região primária:

|Tipo de conta |Leituras |Gravações |
|------------------------|--|--|
| Região de gravação única | Região primária | Região primária |
| Várias regiões de gravação | Região primária  | Região primária  |

> [!NOTE]
> A região primária refere-se à primeira região na [lista de regiões da conta do Azure Cosmos](distribute-data-globally.md).
> Se os valores especificados como preferência regional não corresponderem a nenhuma região do Azure existente, eles serão ignorados. Se eles corresponderem a uma região existente, mas a conta não for replicada para ela, o cliente se conectará à próxima região preferida que corresponda ou à região primária.

> [!WARNING]
> A lógica de failover e disponibilidade descrita neste documento pode ser desabilitada na configuração do cliente, o que não é recomendável, a menos que o aplicativo do usuário trate os erros de disponibilidade em si. Isso pode ser feito por:
>
> * Definindo a propriedade [ConnectionPolicy. EnableEndpointRediscovery](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.enableendpointdiscovery) no SDK do .net v2 como false.
> * Definindo a propriedade [CosmosClientOptions. LimitToEndpoint](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.limittoendpoint) no SDK do .net V3 como true.
> * Definindo o método [CosmosClientBuilder. endpointDiscoveryEnabled](/java/api/com.azure.cosmos.cosmosclientbuilder.endpointdiscoveryenabled) no SDK do Java v4 como false.
> * Definindo o parâmetro [CosmosClient.enable_endpoint_discovery](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient) no SDK do Python como false.
> * Definindo o parâmetro [CosmosClientOptions. ConnectionPolicy. enableEndpointDiscovery](/javascript/api/@azure/cosmos/connectionpolicy#enableEndpointDiscovery) no SDK js como false.

Em circunstâncias normais, o cliente do SDK se conectará à região preferida (se uma preferência regional estiver definida) ou à região primária (se nenhuma preferência estiver definida) e as operações serão limitadas a essa região, a menos que qualquer um dos cenários a seguir ocorra.

Nesses casos, o cliente que usa o SDK do cosmos do Azure expõe logs e inclui as informações de repetição como parte das **informações de diagnóstico da operação**:

* A propriedade *RequestDiagnosticsString* em respostas no SDK do .net v2.
* A propriedade de *diagnóstico* em respostas e exceções no SDK do .net v3.
* O método *getDiagnostics ()* em respostas e exceções no SDK do Java V4.

Ao determinar a próxima região em ordem de preferência, o cliente SDK usará a lista região da conta, priorizando as regiões preferenciais (se houver).

Para obter detalhes abrangentes sobre as garantias de SLA durante esses eventos, consulte os [SLAs para disponibilidade](high-availability.md#slas-for-availability).

## <a name="removing-a-region-from-the-account"></a><a id="remove-region"></a>Removendo uma região da conta

Quando você remove uma região de uma conta do Azure Cosmos, qualquer cliente SDK que usa ativamente a conta detectará a remoção da região por meio de um código de resposta de back-end. O cliente marca o ponto de extremidade regional como indisponível. O cliente tenta novamente a operação atual e todas as operações futuras são roteadas permanentemente para a próxima região em ordem de preferência. Caso a lista de preferências tenha apenas uma entrada (ou estava vazia), mas a conta tenha outras regiões disponíveis, ela será roteada para a próxima região na lista de contas.

## <a name="adding-a-region-to-an-account"></a>Adicionando uma região a uma conta

A cada 5 minutos, o cliente do SDK do cosmos do Azure lê a configuração da conta e atualiza as regiões das quais está ciente.

Se você remover uma região e posteriormente adicioná-la de volta à conta, se a região adicionada tiver uma ordem de preferência regional maior na configuração do SDK do que a região conectada atual, o SDK voltará a usar essa região de forma permanente. Depois que a região adicionada for detectada, todas as solicitações futuras serão direcionadas para ela.

Se você configurar o cliente para se conectar preferencialmente a uma região que a conta do Azure Cosmos não tem, a região preferida será ignorada. Se você adicionar essa região posteriormente, o cliente a detectará e será alternado permanentemente para essa região.

## <a name="fail-over-the-write-region-in-a-single-write-region-account"></a><a id="manual-failover-single-region"></a>Fazer failover da região de gravação em uma única conta de região de gravação

Se você iniciar um failover da região de gravação atual, a próxima solicitação de gravação falhará com uma resposta de back-end conhecida. Quando essa resposta for detectada, o cliente consultará a conta para aprender a nova região de gravação e prosseguirá para repetir a operação atual e roteará permanentemente todas as operações de gravação futuras para a nova região.

## <a name="regional-outage"></a>Interrupção regional

Se a conta for uma região de gravação única e a interrupção regional ocorrer durante uma operação de gravação, o comportamento será semelhante a um [failover manual](#manual-failover-single-region). Para solicitações de leitura ou várias contas de regiões de gravação, o comportamento é semelhante à [remoção de uma região](#remove-region).

## <a name="session-consistency-guarantees"></a>Garantias de consistência de sessão

Ao usar a [consistência de sessão](consistency-levels.md#guarantees-associated-with-consistency-levels), o cliente precisa garantir que ele possa ler suas próprias gravações. Em contas de região de gravação única em que a preferência de região de leitura é diferente da região de gravação, pode haver casos em que o usuário emite uma gravação e, ao fazer uma leitura de uma região local, a região local ainda não recebeu a replicação de dados (velocidade de restrição de luz). Nesses casos, o SDK detecta a falha específica na operação de leitura e repete a leitura na região primária para garantir a consistência da sessão.

## <a name="transient-connectivity-issues-on-tcp-protocol"></a>Problemas de conectividade transitórios no protocolo TCP

Em cenários em que o cliente SDK do Azure Cosmos está configurado para usar o protocolo TCP, para uma determinada solicitação, pode haver situações em que as condições de rede estejam temporariamente afetando a comunicação com um ponto de extremidade específico. Essas condições de rede temporárias podem surgir como tempos limite de TCP e erros de serviço indisponível (HTTP 503). O cliente tentará novamente a solicitação localmente no mesmo ponto de extremidade por alguns segundos antes de identificando o erro.

Se o usuário tiver configurado uma lista de regiões preferenciais com mais de uma região e a conta do Azure Cosmos for várias regiões de gravação ou uma única região de gravação e a operação for uma solicitação de leitura, o cliente detectará a falha local e tentará novamente realizar a operação única na próxima região da lista de preferências.

## <a name="next-steps"></a>Próximas etapas

* Examine os [SLAs de disponibilidade](high-availability.md#slas-for-availability).
* Usar o [SDK do .net](sql-api-sdk-dotnet-standard.md) mais recente
* Usar o [SDK do Java](sql-api-sdk-java-v4.md) mais recente
* Usar o [SDK do Python](sql-api-sdk-python.md) mais recente
* Usar o último [SDK do node](sql-api-sdk-node.md)
