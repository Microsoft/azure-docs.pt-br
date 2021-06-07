---
title: Esquema de recurso baseado em workspace do Azure Monitor Application Insights
description: Saiba mais sobre a nova estrutura e o esquema de tabela para Azure Monitor Application Insights recursos baseados no espaço de trabalho.
ms.topic: conceptual
ms.date: 05/09/2020
ms.openlocfilehash: ef9d22cd2b45679928ee54778b2a521ea9ecab03
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100575601"
---
# <a name="workspace-based-resource-changes"></a>Alterações de recursos com base no espaço de trabalho

Antes da introdução dos [recursos de Application insights baseados em espaço de trabalho](create-workspace-resource.md), Application insights dados eram armazenados separados de outros dados de log no Azure monitor. Ambos são baseados no Azure Data Explorer e usam a mesma linguagem de consulta Kusto (KQL). Isso é descrito em [logs em Azure monitor](../logs/data-platform-logs.md).

Com os dados de recursos Application Insights baseados em espaço de trabalho são armazenados em um espaço de trabalho Log Analytics com outros dados de monitoramento e dados de aplicativos. Isso simplifica sua configuração, permitindo que você analise dados com mais facilidade em várias soluções e aproveite os recursos dos espaços de trabalho.

## <a name="table-structure"></a>Estrutura da tabela

| Nome da tabela herdada | Nome da nova tabela | Descrição |
|:---|:---|:---|
| availabilityResults | AppAvailabilityResults |  Dados de resumo dos testes de disponibilidade.|
| browserTimings | AppBrowserTimings | Dados sobre o desempenho do cliente, como o tempo necessário para processar os dados de entrada.|
| dependencies | AppDependencies | Chamadas do aplicativo para outros componentes (incluindo componentes externos) registrados por meio de TrackDependency() – por exemplo, chamadas para API REST, banco de dados ou sistema de arquivos.  |
| customEvents | AppEvents | Eventos personalizados criados pelo seu aplicativo. |
| customMetrics | AppMetrics | Métricas personalizadas criadas pelo seu aplicativo. |
| pageViews | AppPageViews| Dados sobre cada exibição do site com informações do navegador. |
| performanceCounters | AppPerformanceCounters | Medições de desempenho dos recursos de computação que dão suporte ao aplicativo, por exemplo, contadores de desempenho do Windows. |
| solicitações | AppRequests | Solicitações recebidas pelo seu aplicativo. Por exemplo, um registro de solicitação separado é registrado para cada solicitação HTTP que o aplicativo Web recebe.  |
| exceptions | AppSystemEvents | As exceções geradas pelo tempo de execução do aplicativo capturam as exceções do lado do servidor e do lado do cliente (navegadores). |
| traces | AppTraces | Logs detalhados (rastreamentos) emitidos por meio de estruturas de log/código do aplicativo gravados por meio de TrackTrace(). |

## <a name="table-schemas"></a>Esquemas de tabela

As seções a seguir mostram o mapeamento entre os nomes de propriedade clássica e os novos nomes de propriedade de Application Insights com base no espaço de trabalho.  Use essas informações para converter todas as consultas que usam tabelas herdadas.

A maioria das colunas tem o mesmo nome com maiúsculas e minúsculas diferentes. Como KQL diferencia maiúsculas de minúsculas, será necessário alterar cada nome de coluna junto com os nomes de tabela nas consultas existentes. Colunas com alterações além de maiúsculas e minúsculas são realçadas. Você ainda pode usar suas consultas Application Insights clássicas dentro do painel **logs** do recurso Application insights, mesmo que ele seja um recurso baseado em espaço de trabalho. Os novos nomes de propriedade são necessários para a consulta de dentro do contexto da experiência de Log Analytics espaço de trabalho.

### <a name="appavailabilityresults"></a>AppAvailabilityResults

Tabela herdada: disponibilidade

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|string|\_ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|ClientOS|string|
|client_StateOrProvince|string|ClientStateOrProvince|string|
|client_Type|string|Clientetype|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|AppRoleName|string|
|customDimensions|dinâmico|Propriedades|Dinâmico|
|customMeasurements|dinâmico|Medidas|Dinâmico|
|duration|real|DurationMs|real|
|`id`|string|`Id`|string|
|iKey|string|IKey|string|
|itemCount|INT|ItemCount|INT|
|itemId|string|\_ItemId|string|
|itemType|string|Type|String|
|local|string|Location|string|
|message|string|Mensagem|string|
|name|string|Nome|cadeia de caracteres|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperationParentId|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|performanceBucket|string|PerformanceBucket|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|tamanho|real|Tamanho|real|
|sucesso|string|Êxito|Bool|
| timestamp|DATETIME|TimeGenerated|DATETIME|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|UserId|string|

### <a name="appbrowsertimings"></a>AppBrowserTimings

Tabela herdada: browserTimings

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|string|\_ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|ClientOS|string|
|client_StateOrProvince|string|ClientStateOrProvince|string|
|client_Type|string|Clientetype|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|AppRoleName|string|
|customDimensions|dinâmico|Propriedades|Dinâmico|
|customMeasurements|dinâmico|Medidas|Dinâmico|
|iKey|string|IKey|string|
|itemCount|INT|ItemCount|INT|
|itemId|string|\_ItemId|string|
|itemType|string|Type|string|
|name|string|Nome|DATETIME|
|networkDuration|real|NetworkDurationMs|real|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperationParentId|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|performanceBucket|string|PerformanceBucket|string|
|processingDuration|real|ProcessingDurationMs|real|
|receiveDuration|real|ReceiveDurationMs|real|
|sdkVersion|string|SdkVersion|string|
|sendDuration|real|SendDurationMs|real|
|session_Id|string|SessionId|string|
| timestamp|DATETIME|TimeGenerated|DATETIME|
|totalDuration|real|TotalDurationMs|real|
|url|string|Url|string|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|UserId|string|

### <a name="appdependencies"></a>AppDependencies

Tabela herdada: dependências

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|string|\_ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|ClientOS|string|
|client_StateOrProvince|string|ClientStateOrProvince|string|
|client_Type|string|Clientetype|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|AppRoleName|string|
|customDimensions|dinâmico|Propriedades|Dinâmico|
|customMeasurements|dinâmico|Medidas|Dinâmico|
|data|string|Dados|string|
|duration|real|DurationMs|real|
|`id`|string|`Id`|string|
|iKey|string|IKey|string|
|itemCount|INT|ItemCount|INT|
|itemId|string|\_ItemId|string|
|itemType|string|Type|String|
|name|string|Nome|cadeia de caracteres|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperationParentId|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|performanceBucket|string|PerformanceBucket|string|
|resultCode|string|ResultCode|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|sucesso|string|Êxito|Bool|
|destino|string|Destino|string|
| timestamp|DATETIME|TimeGenerated|DATETIME|
|type|string|DependencyType|string|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|UserId|string|

### <a name="appevents"></a>AppEvents

Tabela herdada: customEvents

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|string|\_ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|ClientOS|string|
|client_StateOrProvince|string|ClientStateOrProvince|string|
|client_Type|string|Clientetype|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|AppRoleName|string|
|customDimensions|dinâmico|Propriedades|Dinâmico|
|customMeasurements|dinâmico|Medidas|Dinâmico|
|iKey|string|IKey|string|
|itemCount|INT|ItemCount|INT|
|itemId|string|\_ItemId|string|
|itemType|string|Type|string|
|name|string|Nome|cadeia de caracteres|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperationParentId|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
| timestamp|DATETIME|TimeGenerated|DATETIME|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|UserId|string|

### <a name="appmetrics"></a>AppMetrics

Tabela herdada: customMetrics

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|string|\_ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|ClientOS|string|
|client_StateOrProvince|string|ClientStateOrProvince|string|
|client_Type|string|Clientetype|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|AppRoleName|string|
|customDimensions|dinâmico|Propriedades|Dinâmico|
|iKey|string|IKey|string|
|itemId|string|\_ItemId|string|
|itemType|string|Type|string|
|name|string|Nome|cadeia de caracteres|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperationParentId|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
| timestamp|DATETIME|TimeGenerated|DATETIME|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|UserId|string|
|valor|real|removido||
|valueCount|INT|ValueCount|INT|
|valueMax|real|ValueMax|real|
|valueMin|real|ValueMin|real|
|valueStdDev|real|ValueStdDev|real|
|Valores|real|Valores|real|

### <a name="apppageviews"></a>AppPageViews

Tabela herdada: pageViews

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|string|\_ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|ClientOS|string|
|client_StateOrProvince|string|ClientStateOrProvince|string|
|client_Type|string|Clientetype|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|AppRoleName|string|
|customDimensions|dinâmico|Propriedades|Dinâmico|
|customMeasurements|dinâmico|Medidas|Dinâmico|
|duration|real|DurationMs|real|
|`id`|string|`Id`|string|
|iKey|string|IKey|string|
|itemCount|INT|ItemCount|INT|
|itemId|string|\_ItemId|string|
|itemType|string|Type|String|
|name|string|Nome|cadeia de caracteres|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperationParentId|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|performanceBucket|string|PerformanceBucket|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
| timestamp|DATETIME|TimeGenerated|DATETIME|
|url|string|Url|string|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|UserId|string|

### <a name="appperformancecounters"></a>AppPerformanceCounters

Tabela herdada: performanceCounters

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|string|\_ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|category|string|Categoria|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|ClientOS|string|
|client_StateOrProvince|string|ClientStateOrProvince|string|
|client_Type|string|Clientetype|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|AppRoleName|string|
|contador|string|removido||
|customDimensions|dinâmico|Propriedades|Dinâmico|
|iKey|string|IKey|string|
|instance|string|Instância|string|
|itemId|string|\_ItemId|string|
|itemType|string|Type|string|
|name|string|Nome|cadeia de caracteres|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperationParentId|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
| timestamp|DATETIME|TimeGenerated|DATETIME|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|UserId|string|
|valor|real|Valor|real|

### <a name="apprequests"></a>AppRequests

Tabela herdada: solicitações

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|string|\_ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|ClientOS|string|
|client_StateOrProvince|string|ClientStateOrProvince|string|
|client_Type|string|Clientetype|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|AppRoleName|string|
|customDimensions|dinâmico|Propriedades|Dinâmico|
|customMeasurements|dinâmico|Medidas|Dinâmico|
|duration|real|DurationMs|Real|
|`id`|string|`Id`|String|
|iKey|string|IKey|string|
|itemCount|INT|ItemCount|INT|
|itemId|string|\_ItemId|string|
|itemType|string|Type|String|
|name|string|Nome|String|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperationParentId|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|performanceBucket|string|PerformanceBucket|String|
|resultCode|string|ResultCode|String|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|source|string|Fonte|String|
|sucesso|string|Êxito|Bool|
| timestamp|DATETIME|TimeGenerated|DATETIME|
|url|string|Url|String|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|UserId|string|

### <a name="appsystemevents"></a>AppSystemEvents

Tabela herdada: exceções

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|string|\_ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|assembly|string|Assembly|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|ClientOS|string|
|client_StateOrProvince|string|ClientStateOrProvince|string|
|client_Type|string|Clientetype|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|AppRoleName|string|
|customDimensions|dinâmico|Propriedades|dinâmico|
|customMeasurements|dinâmico|Medidas|dinâmico|
|detalhes|dinâmico|Detalhes|dinâmico|
|handledAt|string|HandledAt|string|
|iKey|string|IKey|string|
|innermostAssembly|string|InnermostAssembly|string|
|innermostMessage|string|InnermostMessage|string|
|innermostMethod|string|InnermostMethod|string|
|mais interno|string|Mais interno|string|
|itemCount|INT|ItemCount|INT|
|itemId|string|\_ItemId|string|
|itemType|string|Type|string|
|message|string|Mensagem|string|
|method|string|Método|string|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperationParentId|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|outerAssembly|string|OuterAssembly|string|
|outerMessage|string|OuterMessage|string|
|outerMethod|string|OuterMethod|string|
|outertype|string|Outertype|string|
|problemid|string|Problemid|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|Nível|INT|SeverityLevel|INT|
| timestamp|DATETIME|TimeGenerated|DATETIME|
|type|string|ExceptionType|string|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|UserId|string|

### <a name="apptraces"></a>AppTraces

Tabela herdada: rastreamentos

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|string|\_ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|ClientOS|string|
|client_StateOrProvince|string|ClientStateOrProvince|string|
|client_Type|string|Clientetype|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|AppRoleName|string|
|customDimensions|dinâmico|Propriedades|dinâmico|
|customMeasurements|dinâmico|Medidas|dinâmico|
|iKey|string|IKey|string|
|itemCount|INT|ItemCount|INT|
|itemId|string|\_ItemId|string|
|itemType|string|Type|string|
|message|string|Mensagem|string|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperationParentId|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|Nível|INT|SeverityLevel|INT|
| timestamp|DATETIME|TimeGenerated|DATETIME|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|UserId|string|

## <a name="next-steps"></a>Próximas etapas

* [Explorar métricas](../essentials/metrics-charts.md)
* [Escrever consultas do Analytics](../logs/log-query-overview.md)

