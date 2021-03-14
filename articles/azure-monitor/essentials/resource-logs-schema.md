---
title: Serviços e esquemas com suporte dos Logs de Recursos do Azure
description: Entenda os serviços com suporte e o esquema de eventos para logs de recursos do Azure.
ms.topic: reference
ms.date: 09/01/2020
ms.openlocfilehash: 661cba2d78dbb176bb04e6831cf8e045a285535e
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102033088"
---
# <a name="common-and-service-specific-schema-for-azure-resource-logs"></a>Esquema comum e específico de serviço para logs de recursos do Azure

> [!NOTE]
> Os logs de recursos eram anteriormente conhecidos como logs de diagnóstico. O nome foi alterado em outubro de 2019, pois os tipos de logs coletados por Azure Monitor foram deslocados para incluir mais do que apenas o recurso do Azure. Além disso, a lista de categorias de log de recursos que você pode coletar costumava ser listada neste artigo. Elas foram movidas para [categorias de log de recursos](resource-logs-categories.md). 

[Azure monitor logs de recursos](../essentials/platform-logs-overview.md) são logs emitidos pelos serviços do Azure que descrevem a operação desses serviços ou recursos. Todos os logs de recursos disponíveis por meio de Azure Monitor compartilham um esquema comum de nível superior, com flexibilidade para cada serviço para emitir propriedades exclusivas para seus próprios eventos.

Uma combinação do tipo de recurso (disponível na propriedade `resourceId`) e da `category` identifica exclusivamente um esquema. Este artigo descreve o esquema de nível superior para logs de recursos e links para o Schemata para cada serviço.


## <a name="top-level-common-schema"></a>Esquema comum de nível superior

| Name | Obrigatório/Opcional | Descrição |
|---|---|---|
| time | Obrigatório | O carimbo de data/hora (UTC) do evento. |
| resourceId | Obrigatório | A ID do recurso que emitiu o evento. Para serviços de locatário, isso é o /tenants/tenant-id/providers/provider-name do formulário. |
| tenantId | Necessário para os logs de locatário | A ID de locatário do que esse evento está vinculado ao locatário do Active Directory. Essa propriedade só é usada para logs de nível de locatário, ele não aparece nos logs de nível do recurso. |
| operationName | Obrigatório | O nome da operação representada por esse evento. Se o evento representar uma operação RBAC do Azure, esse será o nome da operação RBAC do Azure (por exemplo, Microsoft. Storage/storageAccounts/blobservices/BLOBs/Read). Normalmente modeladas na forma de uma operação do Resource Manager, mesmo que não sejam as operações do Resource Manager documentadas reais (`Microsoft.<providerName>/<resourceType>/<subtype>/<Write/Read/Delete/Action>`) |
| operationVersion | Opcional | A versão de API associada à operação, se a operationName tiver sido executada usando uma API (por exemplo, `http://myservice.windowsazure.net/object?api-version=2016-06-01` ). Se não houver nenhuma API que corresponde a essa operação, a versão representará a versão dessa operação, caso as propriedades associadas à operação sejam alteradas no futuro. |
| category | Obrigatório | A categoria de log do evento. Categoria é a granularidade na qual você pode habilitar ou desabilitar os logs em determinado recurso. As propriedades exibidas no blob de propriedades de um evento são as mesmas em uma categoria de log e um tipo de recurso específicos. As categorias de log típicas são "auditoria" "operacional" "execução" e "solicitação". |
| resultType | Opcional | O status do evento. Os valores típicos incluem Iniciado, Em Andamento, Com Êxito, Com Falha, Ativo e Resolvido. |
| resultSignature | Opcional | O substatus do evento. Se essa operação corresponder a uma chamada à API REST, esse campo será o código de status HTTP da chamada REST correspondente. |
| resultDescription | Opcional | A descrição de texto estático dessa operação, por exemplo, "obter arquivo de armazenamento". |
| durationMs | Opcional | A duração da operação em milissegundos. |
| callerIpAddress | Opcional | O endereço IP do chamador, caso a operação corresponda a uma chamada à API proveniente de uma entidade com um endereço IP disponível publicamente. |
| correlationId | Opcional | Um GUID usado para agrupar um conjunto de eventos relacionados. Normalmente, se dois eventos tiverem a mesma operationName, mas dois status diferentes (por exemplo, "iniciado" e "êxito"), eles compartilharão a mesma ID de correlação. Isso também pode representar outras relações entre os eventos. |
| identidade | Opcional | Um blob JSON que descreve a identidade do usuário ou do aplicativo que realizou a operação. Normalmente, esse campo inclui a autorização e o token de declarações/JWT do Active Directory. |
| Nível | Opcional | O nível de severidade do evento. Precisa ser Informativo, Aviso, Erro ou Crítico. |
| local | Opcional | A região do recurso que emite o evento, por exemplo, "leste dos EUA" ou "sul da França" |
| properties | Opcional | As propriedades estendidas relacionadas a essa categoria específica de eventos. Todas as propriedades personalizadas/exclusivas devem ser colocadas dentro dessa "parte B" do esquema. |

## <a name="service-specific-schemas"></a>Esquemas específicos do serviço

O esquema para logs de recursos varia dependendo do recurso e da categoria de log. Esta lista mostra serviços que disponibilizam logs de recursos e links para o serviço e o esquema específico de categoria, quando disponíveis. Essa lista está mudando o tempo todo à medida que novos serviços são adicionados, portanto, se você não vir o que precisa abaixo, use um mecanismo de pesquisa para descobrir documentação adicional. Fique à vontade para abrir um problema do GitHub neste artigo para que possamos atualizá-lo.

| Serviço | Esquema e Documentos |
| --- | --- |
| Azure Active Directory | [Visão geral](../../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md), esquema de [log de auditoria](../../active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema.md) e [esquemas de entradas](../../active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md) |
| Serviços de análise | [Log de diagnóstico da instalação do Azure Analysis Services](../../analysis-services/analysis-services-logging.md) |
| Gerenciamento de API | [Logs de recursos de gerenciamento de API](../../api-management/api-management-howto-use-azure-monitor.md#resource-logs) |
| Serviço de Aplicativo | [Logs do serviço de aplicativo](../../app-service/troubleshoot-diagnostic-logs.md)
| Gateways do Aplicativo |[Registro em log do gateway de aplicativo](../../application-gateway/application-gateway-diagnostics.md) |
| Automação do Azure |[Análise de log para automação do Azure](../../automation/automation-manage-send-joblogs-log-analytics.md) |
| Lote do Azure |[Log do lote do Azure](../../batch/batch-diagnostics.md) |
| Serviços Cognitivos | [Registro em log para serviços cognitivas do Azure](../../cognitive-services/diagnostic-logging.md) |
| Registro de Contêiner | [Registro em log do registro de contêiner do Azure](../../container-registry/container-registry-diagnostics-audit-logs.md) |
| Rede de Distribuição de Conteúdo | [Logs do Azure para CDN](../../cdn/cdn-azure-diagnostic-logs.md) |
| CosmosDB | [Registro em log do Azure Cosmos DB](../../cosmos-db/monitor-cosmos-db.md) |
| Data Factory | [Monitorar data factories usando o Azure Monitor](../../data-factory/monitor-using-azure-monitor.md) |
| Data Lake Analytics |[Acessando logs para Azure Data Lake Analytics](../../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Data Lake Store |[Acessando logs para Azure Data Lake Store](../../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Azure Data Explorer | [Logs de Data Explorer do Azure](/azure/data-explorer/using-diagnostic-logs) |
| Banco de Dados do Azure para MySQL | [Banco de dados do Azure para logs de Diagnóstico do MySQL](../../mysql/concepts-server-logs.md#diagnostic-logs) |
| Banco de Dados do Azure para PostgreSQL | [Logs do banco de dados do Azure para PostgreSQL](../../postgresql/concepts-server-logs.md#resource-logs) |
| Azure Databricks | [Log de diagnóstico no Azure Databricks](/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs) |
| Gêmeos Digitais do Azure | [Configurar o diagnóstico de gêmeos digital do Azure](../../digital-twins/troubleshoot-diagnostics.md#log-schemas)
| Hubs de Eventos |[Logs de hubs de eventos do Azure](../../event-hubs/event-hubs-diagnostic-logs.md) |
| ExpressRoute | Esquema não disponível. |
| Firewall do Azure | Esquema não disponível. |
| Front Door | [Registro em log da porta frontal](../../frontdoor/front-door-diagnostics.md) |
| Hub IoT | [Operações do Hub IoT](../../iot-hub/monitor-iot-hub-reference.md#resource-logs) |
| Key Vault |[Logs do Cofre da Chave do Azure](../../key-vault/general/logging.md) |
| Serviço de Kubernetes |[Log de kubernetes do Azure](../../aks/view-control-plane-logs.md#log-event-schema) |
| Load Balancer |[Análise de log para Balanceador de Carga do Azure](../../load-balancer/load-balancer-monitor-log.md) |
| Aplicativos Lógicos |[Esquema de acompanhamento personalizado dos Aplicativos Lógicos B2B](../../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Grupos de segurança de rede |[Log Analytics para grupos de segurança de rede (NSGs)](../../virtual-network/virtual-network-nsg-manage-log.md) |
| Proteção contra DDoS | [Registro em log para o padrão de proteção contra DDoS do Azure](../../ddos-protection/diagnostic-logging.md#log-schemas) |
| Power BI dedicado | [Registro em log para Power BI Embedded no Azure](/power-bi/developer/azure-pbie-diag-logs) |
| Serviços de Recuperação | [Modelo de dados para o backup do Azure](../../backup/backup-azure-reports-data-model.md)|
| Pesquisar |[Habilitação e uso da análise de tráfego de pesquisa](../../search/search-traffic-analytics.md) |
| Barramento de Serviço |[Logs do barramento de serviço do Azure](../../service-bus-messaging/service-bus-diagnostic-logs.md) |
| Banco de Dados SQL | [Log do banco de dados SQL do Azure](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md) |
| Stream Analytics |[Logs de trabalho](../../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Gerenciador de Tráfego | [Esquema de log do Gerenciador de tráfego](../../traffic-manager/traffic-manager-diagnostic-logs.md) |
| Redes Virtuais | Esquema não disponível. |
| Gateways de Rede Virtual | Esquema não disponível. |



## <a name="next-steps"></a>Próximas etapas

* [Consulte as categorias de log de recursos que você pode coletar](resource-logs-categories.md)
* [Saiba mais sobre os logs de recursos](../essentials/platform-logs-overview.md)
* [Transmitir logs de recurso de recurso para **hubs de eventos**](./resource-logs.md#send-to-azure-event-hubs)
* [Alterar as configurações de diagnóstico do log de recursos usando a API REST do Azure Monitor](/rest/api/monitor/diagnosticsettings)
* [Analisar logs do Armazenamento do Azure com o Log Analytics](./resource-logs.md#send-to-log-analytics-workspace)