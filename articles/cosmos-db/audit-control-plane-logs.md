---
title: Como auditar Azure Cosmos DB operações do plano de controle
description: Saiba como auditar as operações do plano de controle, como adicionar uma região, taxa de transferência de atualização, failover de região, adicionar uma VNet, etc. no Azure Cosmos DB
author: SnehaGunda
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/05/2020
ms.author: sngun
ms.openlocfilehash: 6f3e408343fc75d6587d1a67a0179edf13d56e36
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101658241"
---
# <a name="how-to-audit-azure-cosmos-db-control-plane-operations"></a>Como auditar Azure Cosmos DB operações do plano de controle
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

O plano de controle no Azure Cosmos DB é um serviço RESTful que permite que você execute um conjunto variado de operações na conta do Azure Cosmos. Ele expõe um modelo de recurso público (por exemplo: Database, Account) e várias operações para os usuários finais executarem ações no modelo de recurso. As operações do plano de controle incluem alterações no contêiner ou na conta do Azure Cosmos. Por exemplo, operações como criar uma conta do Azure Cosmos, adicionar uma região, atualizar taxa de transferência, failover de região, adicionar uma VNet etc. são algumas das operações de plano de controle. Este artigo explica como auditar as operações do plano de controle no Azure Cosmos DB. Você pode executar as operações do plano de controle em contas do Azure Cosmos usando CLI do Azure, PowerShell ou portal do Azure, enquanto para contêineres, use CLI do Azure ou PowerShell.

Veja a seguir alguns cenários de exemplo em que as operações do plano de controle de auditoria são úteis:

* Você deseja receber um alerta quando as regras de firewall para sua conta do Azure cosmos são modificadas. O alerta é necessário para localizar modificações não autorizadas em regras que regem a segurança de rede de sua conta do Azure Cosmos e tomar uma ação rápida.

* Você deseja receber um alerta se uma nova região for adicionada ou removida da sua conta do Azure Cosmos. Adicionar ou remover regiões tem implicações em requisitos de cobrança e da soberania de dados. Este alerta o ajudará a detectar uma adição acidental ou remoção de região em sua conta.

* Você deseja obter mais detalhes dos logs de diagnóstico sobre o que mudou. Por exemplo, uma VNet foi alterada.

## <a name="disable-key-based-metadata-write-access"></a>Desabilitar acesso de gravação de metadados com base em chave

Antes de auditar as operações do plano de controle no Azure Cosmos DB, desabilite o acesso de gravação de metadados baseado em chave em sua conta. Quando o acesso de gravação de metadados com base em chave está desabilitado, os clientes que se conectam à conta do Azure Cosmos por meio de chaves de conta são impedidos de acessar Você pode desabilitar o acesso de gravação definindo a `disableKeyBasedMetadataWriteAccess` propriedade como true. Depois de definir essa propriedade, as alterações em qualquer recurso podem ocorrer de um usuário com a função e as credenciais adequadas do Azure. Para saber mais sobre como definir essa propriedade, consulte o artigo [impedindo alterações de SDKs](role-based-access-control.md#prevent-sdk-changes) . 

Depois que o `disableKeyBasedMetadataWriteAccess` for ativado, se os clientes baseados em SDK executarem operações de criação ou atualização, um erro *"operação ' Post ' no recurso ' ContainerNameorDatabaseName ' não será permitido por meio de Azure Cosmos DB ponto de extremidade* será retornado. Você precisa ativar o acesso a essas operações para sua conta ou executar as operações de criação/atualização por meio de Azure Resource Manager, CLI do Azure ou Azure PowerShell. Para alternar de volta, defina disableKeyBasedMetadataWriteAccess como **false** usando CLI do Azure conforme descrito no artigo [impedindo alterações do SDK do cosmos](role-based-access-control.md#prevent-sdk-changes) . Certifique-se de alterar o valor de `disableKeyBasedMetadataWriteAccess` para falso em vez de true.

Considere os seguintes pontos ao desativar o acesso de gravação de metadados:

* Avalie e verifique se seus aplicativos não fazem chamadas de metadados que alteram os recursos acima (por exemplo, criar coleção, atualizar taxa de transferência,...) usando o SDK ou as chaves de conta.

* Atualmente, o portal do Azure usa chaves de conta para operações de metadados e, portanto, essas operações serão bloqueadas. Como alternativa, use as implantações de modelo CLI do Azure, SDKs ou Resource Manager para executar essas operações.

## <a name="enable-diagnostic-logs-for-control-plane-operations"></a>Habilitar logs de diagnóstico para operações de plano de controle

Você pode habilitar os logs de diagnóstico para operações de plano de controle usando o portal do Azure. Depois de habilitar, os logs de diagnóstico registrarão a operação como um par de eventos de início e de conclusão com detalhes relevantes. Por exemplo, *RegionFailoverStart* e *RegionFailoverComplete* concluirão o evento de failover de região.

Use as etapas a seguir para habilitar o registro em log nas operações do plano de controle:

1. Entre no [portal do Azure](https://portal.azure.com) e navegue até sua conta do Azure Cosmos.

1. Abra o painel **configurações de diagnóstico** , forneça um **nome** para os logs a serem criados.

1. Selecione **ControlPlaneRequests** para tipo de log e selecione a opção **Enviar para log Analytics** .

Você também pode armazenar os logs em uma conta de armazenamento ou transmitir para um hub de eventos. Este artigo mostra como enviar logs para o log Analytics e, em seguida, consultá-los. Depois de habilitar, levará alguns minutos para que os logs de diagnóstico entrem em vigor. Todas as operações do plano de controle executadas após esse ponto podem ser controladas. A captura de tela a seguir mostra como habilitar os logs do plano de controle:

:::image type="content" source="./media/audit-control-plane-logs/enable-control-plane-requests-logs.png" alt-text="Habilitar log de solicitações de plano de controle":::

## <a name="view-the-control-plane-operations"></a>Exibir as operações do plano de controle

Depois de ativar o registro em log, use as seguintes etapas para rastrear as operações para uma conta específica:

1. Entre no [portal do Azure](https://portal.azure.com).

1. Abra a guia **Monitor** na navegação esquerda e, em seguida, selecione o painel **logs** . Ele abre uma interface do usuário na qual você pode facilmente executar consultas com essa conta específica no escopo. Execute a consulta a seguir para exibir os logs do plano de controle:

   ```kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="ControlPlaneRequests"
   | where TimeGenerated >= ago(1h)
   ```

As capturas de tela a seguir capturam logs quando um nível de consistência é alterado para uma conta do Azure Cosmos:

:::image type="content" source="./media/audit-control-plane-logs/add-ip-filter-logs.png" alt-text="Controlar os logs de plano quando uma VNet for adicionada":::

As capturas de tela a seguir capturam logs quando o keyspace ou uma tabela de uma conta Cassandra são criados e quando a taxa de transferência é atualizada. Os logs do plano de controle para operações de criação e atualização no banco de dados e o contêiner são registrados separadamente, conforme mostrado na seguinte captura de tela:

:::image type="content" source="./media/audit-control-plane-logs/throughput-update-logs.png" alt-text="Controlar os logs de plano quando a taxa de transferência for atualizada":::

## <a name="identify-the-identity-associated-to-a-specific-operation"></a>Identificar a identidade associada a uma operação específica

Se você quiser depurar ainda mais, poderá identificar uma operação específica no log de **atividades** usando a ID da atividade ou o carimbo de data/hora da operação. O carimbo de data/hora é usado para alguns clientes do Resource Manager em que a ID da atividade não é aprovada explicitamente. O log de atividades fornece detalhes sobre a identidade com a qual a operação foi iniciada. A captura de tela a seguir mostra como usar a ID da atividade e localizar as operações associadas a ela no log de atividades:

:::image type="content" source="./media/audit-control-plane-logs/find-operations-with-activity-id.png" alt-text="Usar a ID da atividade e localizar as operações":::

## <a name="control-plane-operations-for-azure-cosmos-account"></a>Operações do plano de controle para a conta do Azure Cosmos

A seguir estão as operações do plano de controle disponíveis no nível da conta. A maioria das operações é controlada no nível da conta. Essas operações estão disponíveis como métricas no Azure monitor:

* Região adicionada
* Região removida
* Conta excluída
* Falha na região
* Conta criada
* Rede virtual excluída
* Configurações de rede da conta atualizadas
* Configurações de replicação de conta atualizadas
* Chaves de conta atualizadas
* Configurações de backup da conta atualizadas
* Configurações de diagnóstico da conta atualizadas

## <a name="control-plane-operations-for-database-or-containers"></a>Operações de plano de controle para banco de dados ou contêineres

A seguir estão as operações do plano de controle disponíveis no nível do banco de dados e do contêiner. Essas operações estão disponíveis como métricas no Azure monitor:

* Banco de dados SQL criado
* Banco de dados SQL atualizado
* Produtividade do banco de dados SQL atualizada
* Banco de dados SQL excluído
* Contêiner SQL criado
* Contêiner SQL atualizado
* Produtividade do contêiner SQL atualizada
* Contêiner SQL excluído
* Keyspace Cassandra criado
* Cassandra keyspace atualizado
* Taxa de transferência de keyspace Cassandra atualizada
* Cassandra keyspace excluído
* Tabela Cassandra criada
* Tabela Cassandra atualizada
* Taxa de transferência de tabela Cassandra atualizada
* Tabela Cassandra excluída
* Banco de dados Gremlin criado
* Banco de dados Gremlin atualizado
* Taxa de transferência do banco de dados Gremlin atualizada
* Banco de dados Gremlin excluído
* Grafo Gremlin criado
* Grafo Gremlin atualizado
* Taxa de transferência do grafo Gremlin atualizada
* Grafo Gremlin excluído
* Banco de dados Mongo criado
* Banco de dados Mongo atualizado
* Taxa de transferência do banco de dados Mongo atualizada
* Banco de dados Mongo excluído
* Coleção Mongo criada
* Coleção Mongo atualizada
* Taxa de transferência de coleção Mongo atualizada
* Coleção Mongo excluída
* Tabela do azuretable criada
* Tabela do azuretable atualizada
* Taxa de transferência de tabela do azuretable atualizada
* Tabela do azuretable excluída

## <a name="diagnostic-log-operations"></a>Operações de log de diagnóstico

A seguir estão os nomes de operação nos logs de diagnóstico para operações diferentes:

* RegionAddStart, RegionAddComplete
* RegionRemoveStart, RegionRemoveComplete
* AccountDeleteStart, AccountDeleteComplete
* RegionFailoverStart, RegionFailoverComplete
* AccountCreateStart, AccountCreateComplete
* AccountUpdateStart, AccountUpdateComplete
* VirtualNetworkDeleteStart, VirtualNetworkDeleteComplete
* DiagnosticLogUpdateStart, DiagnosticLogUpdateComplete

Para operações específicas de API, a operação é nomeada com o seguinte formato:

* ApiKind + ApiKindResourceType + OperationType
* ApiKind + ApiKindResourceType + "taxa de transferência" + OperationType

**Exemplo** 

* CassandraKeyspacesCreate
* CassandraKeyspacesUpdate
* CassandraKeyspacesThroughputUpdate
* SqlContainersUpdate

A propriedade *ResourceDetails* contém o corpo do recurso inteiro como uma carga de solicitação e contém todas as propriedades solicitadas para atualização

## <a name="diagnostic-log-queries-for-control-plane-operations"></a>Consultas de log de diagnóstico para operações de plano de controle

Veja a seguir alguns exemplos para obter os logs de diagnóstico para operações de plano de controle:

```kusto
AzureDiagnostics 
| where Category startswith "ControlPlane"
| where OperationName contains "Update"
| project httpstatusCode_s, statusCode_s, OperationName, resourceDetails_s, activityId_g
```

```kusto
AzureDiagnostics 
| where Category =="ControlPlaneRequests"
| where TimeGenerated >= todatetime('2020-05-14T17:37:09.563Z')
| project TimeGenerated, OperationName, apiKind_s, apiKindResourceType_s, operationType_s, resourceDetails_s
```

```kusto
AzureDiagnostics
| where Category == "ControlPlaneRequests"
| where OperationName startswith "SqlContainersUpdate"
```

```kusto
AzureDiagnostics
| where Category == "ControlPlaneRequests"
| where OperationName startswith "SqlContainersThroughputUpdate"
```

Consulte para obter a ActivityId e o chamador que iniciou a operação de exclusão do contêiner:

```kusto
(AzureDiagnostics
| where Category == "ControlPlaneRequests"
| where OperationName == "SqlContainersDelete"
| where TimeGenerated >= todatetime('9/3/2020, 5:30:29.300 PM')
| summarize by activityId_g )
| join (
AzureActivity
| parse HTTPRequest with * "clientRequestId\": \"" activityId_g "\"" * 
| summarize by Caller, HTTPRequest, activityId_g)
on activityId_g
| project Caller, activityId_g
```

Consulta para obter atualizações de índice ou TTL. Em seguida, você pode comparar a saída dessa consulta com uma atualização anterior para ver a alteração no índice ou TTL.

```Kusto
AzureDiagnostics
| where Category =="ControlPlaneRequests"
| where  OperationName == "SqlContainersUpdate"
| project resourceDetails_s
```

**der**

```json
{id:skewed,indexingPolicy:{automatic:true,indexingMode:consistent,includedPaths:[{path:/*,indexes:[]}],excludedPaths:[{path:/_etag/?}],compositeIndexes:[],spatialIndexes:[]},partitionKey:{paths:[/pk],kind:Hash},defaultTtl:1000000,uniqueKeyPolicy:{uniqueKeys:[]},conflictResolutionPolicy:{mode:LastWriterWins,conflictResolutionPath:/_ts,conflictResolutionProcedure:}
```

## <a name="next-steps"></a>Próximas etapas

* [Explorar o Azure Monitor para o Azure Cosmos DB](../azure-monitor/insights/cosmosdb-insights-overview.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json)
* [Monitorar e depurar com métricas no Azure Cosmos DB](use-metrics.md)
