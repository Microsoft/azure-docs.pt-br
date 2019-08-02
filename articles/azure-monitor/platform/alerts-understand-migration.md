---
title: Entender como a ferramenta de migração voluntária funciona para alertas do Azure Monitor
description: Entender como funciona a ferramenta de migração de alertas e solucionar problemas.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 015000388c5629dbd8ed8833931a809ebd738bd6
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67295522"
---
# <a name="understand-how-the-migration-tool-works"></a>Entender como funciona a ferramenta de migração

Como [anunciado anteriormente](monitoring-classic-retirement.md), alertas clássicos no Azure Monitor estão sendo desativadas até 31 de agosto de 2019 (foi originalmente 30 de junho de 2019). Uma ferramenta de migração está disponível no portal do Azure para clientes que usam regras de alerta clássicas e que queiram disparar a migração em si.

Este artigo explica como funciona a ferramenta de migração voluntária. Ele também descreve soluções para alguns problemas comuns.

> [!NOTE]
> Devido a atraso na disponibilização da ferramenta de migração, a data de desativação para a migração de alertas clássicos tiver sido [estendido até 31 de agosto de 2019](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/) da data do dia 30 de junho de 2019 originalmente anunciada.

## <a name="classic-alert-rules-that-will-not-be-migrated"></a>Regras de alerta clássicas que não serão migradas

> [!IMPORTANT]
> Alertas do log de atividade (incluindo alertas de integridade do serviço) e alertas de Log não são afetados pela migração. A migração se aplica somente às regras de alerta clássicas descritas [aqui](monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform).

Embora a ferramenta possa migrar quase todos os [regras de alerta clássicas](monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform), há algumas exceções. As seguintes regras de alerta não serão migradas por meio da ferramenta (ou durante a migração automática a partir de setembro de 2019):

- Regras de alerta clássicas nas métricas de convidado de máquina virtual (Windows e Linux). Consulte a [orientações para recriar essas regras de alerta em novos alertas de métrica](#guest-metrics-on-virtual-machines) mais adiante neste artigo.
- Regras de alerta clássicas nas métricas de armazenamento clássico. Consulte a [orientação para monitoramento de suas contas de armazenamento clássico](https://azure.microsoft.com/blog/modernize-alerting-using-arm-storage-accounts/).
- Regras de alerta clássicas em algumas métricas da conta de armazenamento. Ver [detalhes](#storage-account-metrics) mais adiante neste artigo.
- Regras de alerta clássicas em algumas métricas de Cosmos DB. Detalhes serão adicionados em uma atualização futura.

Se sua assinatura tiver tais regras clássicas, você deverá migrá-los manualmente. Porque não podemos fornecer uma migração automática, quaisquer alertas de métrica clássicas, existentes desses tipos continuarão a funcionar até de 2020 de junho. Essa extensão oferece-lhe tempo para mover para novos alertas. No entanto, nenhum novo alerta clássica pode ser criada depois de agosto de 2019.

> [!NOTE]
> Além das exceções listadas acima, se suas regras de alerta clássicas são inválidas ou seja, estão em uma [preterido métricas](#classic-alert-rules-on-deprecated-metrics) ou recursos que foram excluídos, eles não serão migrados durante a migração voluntária. Qualquer tais inválidas clássicas regras de alerta serão excluídas quando ocorre a migração automática.

### <a name="guest-metrics-on-virtual-machines"></a>Métricas de convidado em máquinas virtuais

Antes de criar novos alertas de métrica em métricas de convidado, as métricas de convidado devem ser enviadas para o armazenamento do Azure Monitor métricas personalizadas. Siga estas instruções para habilitar o coletor do Azure Monitor em configurações de diagnóstico:

- [Habilitando métricas de convidado para VMs do Windows](collect-custom-metrics-guestos-resource-manager-vm.md)
- [Habilitando métricas de convidado para VMs do Linux](collect-custom-metrics-linux-telegraf.md)

Depois de concluir essas etapas, você pode criar novos alertas de métrica em métricas de convidado. E, depois de criar novos alertas de métrica, você pode excluir alertas clássicos.

### <a name="storage-account-metrics"></a>Métricas da conta de armazenamento

Todos os alertas clássicos em contas de armazenamento podem ser migrados, exceto alertas sobre essas métricas:

- PercentAuthorizationError
- PercentClientOtherError
- PercentNetworkError
- PercentClientOtherError
- PercentSuccess
- PercentThrottlingError
- PercentTimeoutError
- AnonymousThrottlingError
- SASThrottlingError
- ThrottlingError

Alerta clássica regras nas métricas de porcentagem devem ser migradas com base em [o mapeamento entre as métricas de armazenamento novas e antigas](https://docs.microsoft.com/azure/storage/common/storage-metrics-migration#metrics-mapping-between-old-metrics-and-new-metrics). Os limites precisará ser modificada de maneira apropriada porque a nova métrica disponível é absoluto.

Regras de alerta clássicas no AnonymousThrottlingError, SASThrottlingError e ThrottlingError devem ser divididas em dois novos alertas porque não há nenhuma métrica combinada que fornece a mesma funcionalidade. Os limites precisará ser adaptado adequadamente.

### <a name="classic-alert-rules-on-deprecated-metrics"></a>Regras de alerta clássicas nas métricas preteridas

Essas são regras de alerta clássicas nas métricas que anteriormente tinham suporte, mas, eventualmente, foram preteridas. Uma pequena porcentagem de clientes pode ter regras de alerta clássicas inválidas nessas métricas. Como essas regras de alerta são inválidas, eles não serão migrados.

| Tipo de recurso| Métricas preteridas |
|-------------|----------------- |
| Microsoft.DBforMySQL/servers | compute_consumption_percent, compute_limit |
| Microsoft.DBforPostgreSQL/servers | compute_consumption_percent, compute_limit |
| Microsoft.Network/publicIPAddresses | defaultddostriggerrate |
| Microsoft.SQL/servers/databases | service_level_objective, storage_limit, storage_used, limitação, dtu_consumption_percent, storage_used |
| Microsoft.Web/hostingEnvironments/multirolepools | averagememoryworkingset |
| Microsoft.Web/hostingEnvironments/workerpools | bytesreceived, httpqueuelength |

## <a name="how-equivalent-new-alert-rules-and-action-groups-are-created"></a>Como o equivalentes novas regras de alerta e os grupos de ação são criados

A ferramenta de migração converte suas regras de alerta clássicas equivalentes novas regras de alerta e grupos de ação. Para a maioria das regras de alerta clássico equivalentes novas regras de alerta estão na mesma métrica com as mesmas propriedades como `windowSize` e `aggregationType`. No entanto, há algumas regras são em métricas que tem uma métrica diferente, o equivalente no novo sistema de alerta clássico. Os seguintes princípios se aplicam à migração de alertas clássicos, a menos que especificado na seção a seguir:

- **Frequência**: Define a frequência com que uma regra de alerta clássica ou nova verifica a condição. O `frequency` nas regras de alerta clássicas não estava configurável pelo usuário e sempre durante 5 minutos para todos os tipos de recursos, exceto os componentes do Application Insights para o qual ela era 1 minuto. Portanto, frequência de regras equivalentes é definida como 5 minutos e 1 minuto também, respectivamente.
- **Tipo de agregação**: Define como a métrica é agregada durante a janela de interesse. O `aggregationType` também é o mesmo entre alertas clássicos e novos alertas para a maioria das métricas. Em alguns casos, uma vez que a métrica é diferente entre alertas clássicos e novos alertas equivalentes `aggregationType` ou o `primary Aggregation Type` definido para a métrica é usada.
- **Unidades**: Propriedade da métrica na qual o alerta foi criado. Algumas métricas equivalentes tem unidades diferentes. O limite é ajustado corretamente, conforme necessário. Por exemplo, se a métrica original tem segundos como unidades, mas equivalente nova métrica tem milissegundos como unidades, o limite original é multiplicado por 1000 para garantir que o mesmo comportamento.
- **Tamanho da janela**: Define a janela durante o qual métrica os dados são agregados para comparar com o limite. Para o standard `windowSize` valores como 5mins, 15 min, 30mins, 1 hora, três horas, 6 horas, 12 horas, 1 dia, não há nenhuma alteração foi feita para a nova regra de alerta equivalente. Para outros valores, o mais próximo possível `windowSize` é escolhido para ser usado. Para a maioria dos clientes, não há nenhum impacto com essa alteração. Para uma pequena porcentagem de clientes, pode haver a necessidade de ajustar o limite para obter o mesmo comportamento exato.

Nas seções a seguir, fornecemos detalhes de como as métricas que tem uma métrica diferente, o equivalente no novo sistema. Qualquer métrica que permanece a mesma clássicas e novas regras de alerta não está listada. Você pode encontrar uma lista de métricas com suporte no novo sistema [aqui](metrics-supported.md).

### <a name="microsoftstorageaccountsservices"></a>Microsoft.StorageAccounts/services

Para serviços de conta de armazenamento, como blob, tabela, arquivo e fila, as métricas a seguir são mapeadas para as métricas equivalentes conforme mostrado abaixo:

| Métrica de alertas clássicos | Métrica equivalente em novos alertas | Comentários|
|--------------------------|---------------------------------|---------|
| AnonymousAuthorizationError| Métrica de transações com dimensões "ResponseType" = "AuthorizationError" e "Autenticação" = "Anônimo"| |
| AnonymousClientOtherError | Métrica de transações com dimensões "ResponseType" = "ClientOtherError" e "Autenticação" = "Anônimo" | |
| AnonymousClientTimeOutError| Métrica de transações com dimensões "ResponseType" = "ClientTimeOutError" e "Autenticação" = "Anônimo" | |
| AnonymousNetworkError | Métrica de transações com dimensões "ResponseType" = "NetworkError" e "Autenticação" = "Anônimo" | |
| AnonymousServerOtherError | Métrica de transações com dimensões "ResponseType" = "ServerOtherError" e "Autenticação" = "Anônimo" | |
| AnonymousServerTimeOutError | Métrica de transações com dimensões "ResponseType" = "ServerTimeOutError" e "Autenticação" = "Anônimo" | |
| AnonymousSuccess | Métrica de transações com dimensões "ResponseType" = "Success" e "Autenticação" = "Anônimo" | |
| AuthorizationError | Métrica de transações com dimensões "ResponseType" = "AuthorizationError" | |
| AverageE2ELatency | SuccessE2ELatency | |
| AverageServerLatency | SuccessServerLatency | |
| Capacity | BlobCapacity | Use `aggregationType` 'Médio', em vez de 'último'. Métrica se aplica somente aos serviços de Blob |
| ClientOtherError | Métrica de transações com dimensões "ResponseType" = "ClientOtherError"  | |
| ClientTimeoutError | Métrica de transações com dimensões "ResponseType" = "ClientTimeOutError" | |
| ContainerCount | ContainerCount | Use `aggregationType` 'Médio', em vez de 'último'. Métrica se aplica somente aos serviços de Blob |
| NetworkError | Métrica de transações com dimensões "ResponseType" = "NetworkError" | |
| ObjectCount | BlobCount| Use `aggregationType` 'Médio', em vez de 'último'. Métrica se aplica somente aos serviços de Blob |
| SASAuthorizationError | Métrica de transações com dimensões "ResponseType" = "AuthorizationError" e "Autenticação" = "SAS" | |
| SASClientOtherError | Métrica de transações com dimensões "ResponseType" = "ClientOtherError" e "Autenticação" = "SAS" | |
| SASClientTimeOutError | Métrica de transações com dimensões "ResponseType" = "ClientTimeOutError" e "Autenticação" = "SAS" | |
| SASNetworkError | Métrica de transações com dimensões "ResponseType" = "NetworkError" e "Autenticação" = "SAS" | |
| SASServerOtherError | Métrica de transações com dimensões "ResponseType" = "ServerOtherError" e "Autenticação" = "SAS" | |
| SASServerTimeOutError | Métrica de transações com dimensões "ResponseType" = "ServerTimeOutError" e "Autenticação" = "SAS" | |
| SASSuccess | Métrica de transações com dimensões "ResponseType" = "Success" e "Autenticação" = "SAS" | |
| ServerOtherError | Métrica de transações com dimensões "ResponseType" = "ServerOtherError" | |
| ServerTimeOutError | Métrica de transações com dimensões "ResponseType" = "ServerTimeOutError"  | |
| Êxito | Métrica de transações com dimensões "ResponseType" = "Êxito" | |
| TotalBillableRequests| Transações | |
| TotalEgress | Saída | |
| TotalIngress | Entrada | |
| TotalRequests | Transações | |

### <a name="microsoftinsightscomponents"></a>Microsoft.insights/components

Para o Application Insights equivalentes métricas são mostradas conforme abaixo:

| Métrica de alertas clássicos | Métrica equivalente em novos alertas | Comentários|
|--------------------------|---------------------------------|---------|
| availability.availabilityMetric.value | availabilityResults/availabilityPercentage|   |
| availability.durationMetric.value | availabilityResults/duration| Multiplique limite original por 1000 como unidades de métrica clássica são em segundos e novos estão em milissegundos.  |
| basicExceptionBrowser.count | exceptions/browser|  Use `aggregationType` 'count' em vez de 'sum'. |
| basicExceptionServer.count | exceptions/server| Use `aggregationType` 'count' em vez de 'sum'.  |
| clientPerformance.clientProcess.value | browserTimings/processingDuration| Multiplique limite original por 1000 como unidades de métrica clássica são em segundos e novos estão em milissegundos.  |
| clientPerformance.networkConnection.value | browserTimings/networkDuration|  Multiplique limite original por 1000 como unidades de métrica clássica são em segundos e novos estão em milissegundos. |
| clientPerformance.receiveRequest.value | browserTimings/receiveDuration| Multiplique limite original por 1000 como unidades de métrica clássica são em segundos e novos estão em milissegundos.  |
| clientPerformance.sendRequest.value | browserTimings/sendDuration| Multiplique limite original por 1000 como unidades de métrica clássica são em segundos e novos estão em milissegundos.  |
| clientPerformance.total.value | browserTimings/totalDuration| Multiplique limite original por 1000 como unidades de métrica clássica são em segundos e novos estão em milissegundos.  |
| performanceCounter.available_bytes.value | performanceCounters/memoryAvailableBytes|   |
| performanceCounter.io_data_bytes_per_sec.value | performanceCounters/processIOBytesPerSecond|   |
| performanceCounter.number_of_exceps_thrown_per_sec.value | performanceCounters/exceptionsPerSecond|   |
| performanceCounter.percentage_processor_time_normalized.value | performanceCounters/processCpuPercentage|   |
| performanceCounter.percentage_processor_time.value | performanceCounters/processCpuPercentage| Limite precisará ser adequadamente modificadas como métrica original era entre todos os núcleos e nova métrica é normalizada para um núcleo. Ferramenta de migração não altera os limites.  |
| performanceCounter.percentage_processor_total.value | performanceCounters/processorCpuPercentage|   |
| performanceCounter.process_private_bytes.value | performanceCounters/processPrivateBytes|   |
| performanceCounter.request_execution_time.value | performanceCounters/requestExecutionTime|   |
| performanceCounter.requests_in_application_queue.value | performanceCounters/requestsInQueue|   |
| performanceCounter.requests_per_sec.value | performanceCounters/requestsPerSecond|   |
| request.duration | requests/duration| Multiplique limite original por 1000 como unidades de métrica clássica são em segundos e novos estão em milissegundos.  |
| request.rate | / taxa de solicitações|   |
| requestFailed.count | requests/failed| Use `aggregationType` 'count' em vez de 'sum'.   |
| view.count | pageViews/count| Use `aggregationType` 'count' em vez de 'sum'.   |

### <a name="how-equivalent-action-groups-are-created"></a>Como os grupos de ação equivalentes são criados

Alerta clássico tinham de regras de email, webhook, ações de runbook e o aplicativo de lógica vinculadas ao alerta da regra em si. Novas regras de alerta usam grupos de ações que podem ser reutilizados em várias regras de alerta. A ferramenta de migração cria um único grupo de ação para ações mesmos, independentemente de quantas regras de alerta estiver usando a ação. Grupos de ação criados pela ferramenta de migração usam o formato de nomenclatura 'Migrated_AG *'.

## <a name="rollout-phases"></a>Fases de distribuição

A ferramenta de migração está distribuindo em fases para clientes que usam regras de alerta clássicas. Os proprietários da assinatura receberá um email quando a assinatura está pronta para ser migrada por meio da ferramenta.

> [!NOTE]
> Como a ferramenta está sendo revertida em fases, você poderá ver que algumas de suas assinaturas ainda não estiver prontas para ser migrada durante as fases iniciais.

A maioria das assinaturas está marcada como prontos para a migração. Somente as assinaturas que têm alertas clássicos nos seguintes tipos de recursos são ainda não está prontas para a migração.

- Microsoft.classicCompute/domainNames/slots/roles
- Microsoft.documentDB/databases
- Microsoft.insights/components

## <a name="who-can-trigger-the-migration"></a>Quem pode disparar a migração?

Qualquer usuário que tenha a função interna de Colaborador de monitoramento no nível da assinatura pode disparar a migração. Os usuários que têm uma função personalizada com as seguintes permissões também podem disparar a migração:

- */leitura
- Microsoft.Insights/actiongroups/*
- Microsoft.Insights/AlertRules/*
- Microsoft.Insights/metricAlerts/*

> [!NOTE]
> Além de ter acima permissões, sua assinatura Além disso deve ser registrada com o provedor de recursos Microsoft.AlertsManagement. Isso é necessário para migrar com êxito alertas de anomalias de falha no Application Insights. 

## <a name="common-problems-and-remedies"></a>Problemas comuns e soluções

Depois que você [disparar a migração](alerts-using-migration-tool.md), você receberá os endereços que você forneceu para notificá-lo que a migração foi concluída ou se nenhuma ação é necessária no email. Esta seção descreve alguns problemas comuns e como lidar com eles.

### <a name="validation-failed"></a>Falha na validação

Devido a algumas alterações recentes a regras de alerta clássicas em sua assinatura, a assinatura não pode ser migrada. Esse problema é temporário. Você pode reiniciar a migração, depois que o status de migração move de volta **prontos para a migração** em alguns dias.

### <a name="policy-or-scope-lock-preventing-us-from-migrating-your-rules"></a>Impedindo a migrar suas regras de bloqueio de política ou escopo

Como parte da migração, serão criados novos alertas de métrica e novos grupos de ação e, em seguida, as regras de alerta clássicas serão excluídas. No entanto, há uma política ou o escopo de bloqueio impedindo a criação de recursos. Dependendo do escopo ou a política de bloqueio, algumas ou todas as regras não pôde ser migradas. Você pode resolver esse problema removendo o bloqueio de escopo ou a política temporariamente e disparar a migração novamente.

## <a name="next-steps"></a>Próximas etapas

- [Como usar a ferramenta de migração](alerts-using-migration-tool.md)
- [Preparar para a migração](alerts-prepare-migration.md)
