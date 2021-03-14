---
title: Exibir dados do aplicativo do Azure Application Insights | Microsoft Docs
description: Use a solução Conector do Application Insights para diagnosticar problemas de desempenho e entender o que os usuários fazem com seu aplicativo quando ele é monitorado com o Application Insights.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/13/2019
ms.openlocfilehash: c21ed461280dfa617c852fe6c18c30e5d697b704
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102031269"
---
# <a name="application-insights-connector-management-solution-deprecated"></a>Solução de gerenciamento do Conector do Application Insights (preterida)

![Símbolo do Application Insights](./media/app-insights-connector/app-insights-connector-symbol.png)

>[!NOTE]
> Com o suporte de [consultas de recursos cruzados](../logs/cross-workspace-query.md), a solução de gerenciamento do Conector do Application Insights não é mais necessária. Ela foi preterida e removida do Azure Marketplace, juntamente com o portal do OMS que foi oficialmente preterido em 15 de janeiro de 2019 para a nuvem comercial do Azure. Ele será desativado em 30 de março de 2019 para a nuvem Azure US Government.
>
>As conexões existentes continuarão a funcionar até 30 de junho de 2019.  Com o preterimento do portal OMS, não há como configurar nem remover conexões do portal. Confira [Remover o conector com o PowerShell](#removing-the-connector-with-powershell) abaixo para obter um script que usa o PowerShell para remover as conexões existentes.
>
>Para obter orientação sobre como consultar os dados de log do Application Insights para vários aplicativos, confira [Unificar múltiplos recursos do Application Insights do Azure Monitor](./unify-app-resource-data.md). Para obter mais informações sobre a desativação do portal do OMS, confira [Portal do OMS mudando para o Azure](./oms-portal-transition.md).
>
> 

A solução Conector do Application Insights ajuda você a diagnosticar problemas de desempenho e entender o que os usuários fazem com seu aplicativo quando ele é monitorado com o [Application Insights](../app/app-insights-overview.md). Exibições da mesma telemetria de aplicativo que os desenvolvedores visualizam no Application Insights estão disponíveis no Log Analytics. No entanto, ao integrar os aplicativos do Application Insights com o Log Analytics, a visibilidade dos aplicativos aumenta, pois os dados do aplicativo e da operação ficam em um único lugar. Ter as mesmas exibições ajuda na colaboração com os desenvolvedores de aplicativos. As exibições comuns podem ajudar a reduzir o tempo para detectar e resolver problemas do aplicativo e da plataforma.

Ao usar a solução, você pode:

- Exibir todos os aplicativos do Application Insights em um único lugar, mesmo quando eles estiverem em assinaturas diferentes do Azure
- Correlacionar os dados da infraestrutura com os dados do aplicativo
- Visualizar os dados do aplicativo com perspectivas na pesquisa de logs
- Dinamizar dos dados do Log Analytics para o aplicativo do Application Insights no Portal do Azure


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="connected-sources"></a>Fontes conectadas

Ao contrário da maioria das outras soluções do Log Analytics, os dados não são coletados para o Conector do Application Insights por agentes. Todos os dados usados pela solução vêm diretamente do Azure.

| Fonte Conectada | Com suporte | Descrição |
| --- | --- | --- |
| [Agentes do Windows](./../agents/agent-windows.md) | Não | A solução não coleta informações de agentes do Windows. |
| [Agentes do Linux](../vm/quick-collect-linux-computer.md) | Não | A solução não coleta informações de agentes do Linux. |
| [Grupo de gerenciamento do SCOM](../agents/om-agents.md) | Não | A solução não coleta informações de agentes em um grupo de gerenciamento de SCOM conectado. |
| [Conta de Armazenamento do Azure](../essentials/resource-logs.md#send-to-log-analytics-workspace) | Não | A solução não coleta informações do armazenamento do Azure. |

## <a name="prerequisites"></a>Pré-requisitos

- Para acessar as informações do Conector do Application Insights, você deve ter uma assinatura do Azure
- É necessário ter, pelo menos, um recurso do Application Insights configurado.
- Você deve ser o proprietário ou colaborador do recurso do Application Insights.

## <a name="configuration"></a>Configuração

1. Habilite a solução Análise de Aplicativos Web do Azure no [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps) ou usando o processo descrito em [Adicionar soluções do Log Analytics por meio da Galeria de Soluções](../insights/solutions.md).
2. Navegue até o [Portal do Azure](https://portal.azure.com). Selecione **Todos os serviços** para abrir o Application Insights. Em seguida, procure Application Insights. 
3. Em **Assinaturas**, selecione uma assinatura que tem os recursos do Application Insights e, em seguida, em **Nome**, selecione um ou mais aplicativos.
4. Clique em **Save** (Salvar).

Em aproximadamente 30 minutos, os dados ficarão disponíveis e o bloco do Application Insights será atualizado com os dados, como a seguinte imagem:

![Bloco do Application Insights](./media/app-insights-connector/app-insights-tile.png)

Outros pontos a serem considerados:

- Você pode vincular aplicativos do Application Insights somente a um único espaço de trabalho do Log Analytics.
- Só é possível vincular os [recursos do Application Insights Básico ou Enterprise](https://azure.microsoft.com/pricing/details/application-insights) ao Log Analytics. No entanto, você pode usar a camada Gratuita do Log Analytics.

## <a name="management-packs"></a>Pacotes de gerenciamento

Essa solução não instala nenhum pacote de gerenciamento nos grupos de gerenciamento conectados.

## <a name="use-the-solution"></a>Usar a solução

As próximas seções descrevem como você pode usar as folhas mostradas no painel do Application Insights para exibir e interagir com os dados de seus aplicativos.

### <a name="view-application-insights-connector-information"></a>Exibir informações do Conector do Application Insights

Clique no bloco **Application Insights** para abrir o painel **Application Insights** para ver as folhas a seguir.

![Captura de tela do painel de Application Insights mostrando as folhas para aplicativos, volume de dados e disponibilidade.](./media/app-insights-connector/app-insights-dash01.png)

![Captura de tela do painel de Application Insights mostrando as folhas para solicitações de servidor, falhas e exceções.](./media/app-insights-connector/app-insights-dash02.png)

O painel inclui as folhas mostradas na tabela. Cada folha lista os 10 principais itens que correspondem aos critérios da folha para o escopo e o intervalo de tempo especificados. É possível executar uma pesquisa de logs que retorna todos os registros ao clicar em **Ver todos** na parte inferior da folha ou ao clicar no cabeçalho da folha.


| **Coluna** | **Descrição** |
| --- | --- |
| Aplicativos – Número de aplicativos | Mostra o número de aplicativos nos recursos do Aplicativo. Também lista os nomes de aplicativo e para cada um, a contagem de registros do aplicativo. Clique no número para executar uma pesquisa de logs para <code>ApplicationInsights &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName</code> <br><br>  Clique em um nome de aplicativo para executar uma pesquisa de logs no aplicativo, que mostra registros de aplicativos por host, registros por tipo de telemetria e todos os dados por tipo (com base no último dia). |
| Volume de Dados – hosts que enviam os dados | Mostra o número de hosts do computador que estão enviando dados. Também lista os hosts do computador e a contagem de registros de cada host. Clique no número para executar uma pesquisa de logs para <code>ApplicationInsights &#124; summarize AggregatedValue = sum(SampledCount) by Host</code> <br><br> Clique em um nome do computador para executar uma pesquisa de logs no host, que mostra registros de aplicativos por host, registros por tipo de telemetria e todos os dados por tipo (com base no último dia). |
| Disponibilidade – resultados do teste na Web | Mostra um gráfico de rosca dos resultados de teste na Web, indicando aprovação ou reprovação. Clique no gráfico para executar uma pesquisa de logs para <code>ApplicationInsights &#124; where TelemetryType == "Availability" &#124; summarize AggregatedValue = sum(SampledCount) by AvailabilityResult</code> <br><br> Os resultados mostram o número de aprovações e reprovações de todos os testes. Ela mostra todos os aplicativos Web com o tráfego para o último minuto. Clique em um nome de aplicativo para exibir uma pesquisa de logs, mostrando os detalhes dos testes na Web com falha. |
| Solicitações do Servidor – solicitações por hora | Mostra um gráfico de linhas das solicitações do servidor por hora para vários aplicativos. Focalize uma linha no gráfico para ver os três primeiros aplicativos que receberam solicitações em um ponto no tempo. Também mostra uma lista dos aplicativos que receberam solicitações e o número de solicitações para o período selecionado. <br><br>Clique no grafo para executar uma pesquisa de logs para <code>ApplicationInsights &#124; where TelemetryType == "Request" &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code>, que mostra um grafo de linhas mais detalhado das solicitações do servidor por hora para vários aplicativos. <br><br> Clique em um aplicativo na lista para executar uma pesquisa de logs para <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true</code>, que mostra uma lista de solicitações, gráficos das solicitações ao longo do tempo e duração da solicitação e uma lista de códigos de resposta da solicitação.   |
| Falhas – solicitações com falha por hora | Mostra um gráfico de linhas das solicitações de aplicativo com falha por hora. Focalize o gráfico para ver os três primeiros aplicativos com solicitações com falha em um ponto no tempo. Também mostra uma lista de aplicativos com o número de solicitações com falha para cada um. Clique no gráfico para executar uma pesquisa de logs para <code>ApplicationInsights &#124; where TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code>, que mostra um gráfico de linhas mais detalhado das solicitações de aplicativo com falha. <br><br>Clique em um item na lista para executar uma pesquisa de logs para <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true</code>, que mostra as solicitações com falha, gráficos das solicitações com falha ao longo do tempo e duração da solicitação e uma lista de códigos de resposta da solicitação. |
| Exceções – exceções por hora | Mostra um gráfico de linhas das exceções por hora. Focalize o gráfico para ver os três primeiros aplicativos com exceções com falha em um ponto no tempo. Também mostra uma lista de aplicativos com o número de exceções com falha para cada um. Clique no gráfico para executar uma pesquisa de logs para <code>ApplicationInsights &#124; where TelemetryType == "Exception" &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code>, que mostra um gráfico de link mais detalhado das exceções. <br><br>Clique em um item na lista para executar uma pesquisa de logs para <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Exception"</code>, que mostra uma lista de exceções, gráficos das exceções ao longo do tempo e solicitações com falha e uma lista dos tipos de exceção.  |

### <a name="view-the-application-insights-perspective-with-log-search"></a>Exibir a perspectiva do Application Insights com a pesquisa de logs

Quando você clica em um item no painel, você vê uma perspectiva do Application Insights mostrada na pesquisa. A perspectiva fornece uma visualização estendida, com base no tipo de telemetria selecionado. Portanto, o conteúdo de visualização muda para tipos diferentes de telemetria.

Quando você clica em qualquer lugar na folha Aplicativos, você vê a perspectiva de **Aplicativos** padrão.

![Perspectiva de Aplicativos do Application Insights](./media/app-insights-connector/applications-blade-drill-search.png)

A perspectiva mostra uma visão geral do aplicativo selecionado.

A folha **Disponibilidade** mostra uma exibição de perspectiva diferente na qual você pode ver os resultados de teste na Web e as solicitações com falha relacionadas.

![Perspectiva de Disponibilidade do Application Insights](./media/app-insights-connector/availability-blade-drill-search.png)

Quando você clica em qualquer lugar nas folhas **Solicitações do Servidor** ou **Falhas**, os componentes da perspectiva mudam para dar a você uma visualização relacionada às solicitações.

![Folha Falhas do Application Insights](./media/app-insights-connector/server-requests-failures-drill-search.png)

Quando você clica em qualquer lugar na folha **Exceções**, você vê uma visualização adaptada às exceções.

![Folha Exceções do Application Insights](./media/app-insights-connector/exceptions-blade-drill-search.png)

Independentemente de você clicar em algo no painel **Conector do Application Insights**, na própria página **Pesquisa**, qualquer consulta que retorna dados do Application Insights mostra a perspectiva do Application Insights. Por exemplo, se você estiver exibindo dados do Application Insights, uma consulta **&#42;** também mostrará a guia de perspectiva como a seguinte imagem:

![Application Insights](./media/app-insights-connector/app-insights-search.png)

Os componentes da perspectiva são atualizados, dependendo da consulta de pesquisa. Isso significa que você pode filtrar os resultados usando qualquer campo de pesquisa que fornece a capacidade de ver os dados de:

- Todos os aplicativos
- Um único aplicativo selecionado
- Um grupo de aplicativos

### <a name="pivot-to-an-app-in-the-azure-portal"></a>Dinamizar para um aplicativo no portal do Azure

As folhas do Conector do Application Insights foram projetadas para permitir que você dinamiza para o aplicativo selecionado do Application Insights *quando usar o portal do Azure*. Use a solução como uma plataforma de monitoramento de alto nível que ajuda você a solucionar problemas de um aplicativo. Ao encontrar um possível problema em um de seus aplicativos conectados, você pode fazer uma busca detalhada na pesquisa do Log Analytics ou dinamizar diretamente até o aplicativo do Application Insights.

Para dinamizar, clique nas reticências (**...**) exibidas ao final de cada linha e selecione **Abrir no Application Insights**.

>[!NOTE]
>A opção **Abrir no Application Insights** não está disponível no portal do Azure.

![Abrir no Application Insights](./media/app-insights-connector/open-in-app-insights.png)

### <a name="sample-corrected-data"></a>Dados corrigido por amostra

O Application Insights fornece a *[correção de amostragem](../app/sampling.md)* para ajudar a reduzir o tráfego de telemetria. Ao habilitar a amostragem no aplicativo do Application Insights, você obtém um número reduzido de entradas armazenadas no Application Insights e no Log Analytics. Embora a consistência dos dados seja preservada na página e nas perspectivas do **Conector do Application Insights**, você deverá corrigir manualmente os dados amostrados das consultas personalizadas.

Este é um exemplo de correção de amostragem em uma consulta da pesquisa de logs:

```
ApplicationInsights | summarize AggregatedValue = sum(SampledCount) by TelemetryType
```

O campo **Contagem Amostrada** está presente em todas as entradas e mostra o número de pontos de dados que a entrada representa. Se você ativar a amostragem no aplicativo do Application Insights, a **Contagem Amostrada** será maior que 1. Para contar o número real de entradas geradas pelo aplicativo, some os campos **Contagem Amostrada**.

A amostragem afeta somente o número total de entradas geradas pelo aplicativo. Não é necessário corrigir a amostragem em campos de métrica como **RequestDuration** ou **AvailabilityDuration**, porque esses campos mostram a média das entradas representadas.

## <a name="input-data"></a>Dados de entrada

A solução recebe os seguintes tipos de telemetria de dados dos aplicativos conectados do Application Insights:

- Disponibilidade
- Exceções
- Requests
- Exibições de página – para que o workspace receba exibições de página, você deve configurar os aplicativos para coletar essas informações. Para obter mais informações, consulte [PageViews](../app/api-custom-events-metrics.md#page-views).
- Eventos personalizados – para que o workspace receba eventos personalizados, você deve configurar os aplicativos para coletar essas informações. Para obter mais informações, consulte [TrackEvent](../app/api-custom-events-metrics.md#trackevent).

Os dados provenientes do Application Insights são recebidos pelo Log Analytics assim que estiverem disponíveis.

## <a name="output-data"></a>Dados de saída

Um registro com um *tipo* de *ApplicationInsights* é criado para cada tipo de dados de entrada. Os registros do Application Insights têm as propriedades mostradas nas seguintes seções:

### <a name="generic-fields"></a>Campos genéricos

| Propriedade | Descrição |
| --- | --- |
| Type | ApplicationInsights |
| ClientIP |   |
| TimeGenerated | Hora do registro |
| ApplicationId | Chave de instrumentação do aplicativo do Application Insights |
| ApplicationName | Nome do aplicativo do Application Insights |
| RoleInstance | ID do host do servidor |
| DeviceType | Dispositivo de cliente |
| ScreenResolution |   |
| Continente | Continente de origem da solicitação |
| País | País/região em que a solicitação foi originada |
| Província | Província, Estado ou localidade de origem da solicitação |
| City | Cidade de origem da solicitação |
| isSynthetic | Indica se a solicitação foi criada por um usuário ou por um método automatizado. True = método automatizado ou false = gerado pelo usuário |
| SamplingRate | Porcentagem de telemetria gerada pelo SDK enviado ao portal. Intervalo 0.0-100.0. |
| SampledCount | 100/(SamplingRate). Por exemplo, 4 =&gt; 25% |
| IsAuthenticated | Verdadeiro ou falso |
| OperationID | Itens que têm a mesma ID de operação são mostrados como Itens Relacionados no portal. Normalmente, a ID da solicitação |
| ParentOperationID | ID da operação pai |
| OperationName |   |
| SessionId | GUID para identificar exclusivamente a sessão em que a solicitação foi criada |
| SourceSystem | ApplicationInsights |

### <a name="availability-specific-fields"></a>Campos específicos à disponibilidade

| Propriedade | Descrição |
| --- | --- |
| TelemetryType | Disponibilidade |
| AvailabilityTestName | Nome do teste na Web |
| AvailabilityRunLocation | Origem geográfica da solicitação HTTP |
| AvailabilityResult | Indica o resultado de êxito do teste na Web |
| AvailabilityMessage | A mensagem anexada ao teste na Web |
| AvailabilityCount | 100/(Taxa de Amostragem). Por exemplo, 4 =&gt; 25% |
| DataSizeMetricValue | 1.0 ou 0.0 |
| DataSizeMetricCount | 100/(Taxa de Amostragem). Por exemplo, 4 =&gt; 25% |
| AvailabilityDuration | Tempo, em milissegundos, da duração do teste na Web |
| AvailabilityDurationCount | 100/(Taxa de Amostragem). Por exemplo, 4 =&gt; 25% |
| AvailabilityValue |   |
| AvailabilityMetricCount |   |
| AvailabilityTestId | GUID exclusivo para o teste na Web |
| AvailabilityTimestamp | Carimbo de data/hora preciso do teste de disponibilidade |
| AvailabilityDurationMin | Para registros da amostra, esse campo mostra a duração mínima (em milissegundos) do teste na Web nos pontos de dados representados |
| AvailabilityDurationMax | Para registros da amostra, esse campo mostra a duração máxima (em milissegundos) do teste na Web nos pontos de dados representados |
| AvailabilityDurationStdDev | Para registros da amostra, esse campo mostra o desvio padrão entre todas as durações (em milissegundos) do teste na Web nos pontos de dados representados |
| AvailabilityMin |   |
| AvailabilityMax |   |
| AvailabilityStdDev | &nbsp;  |

### <a name="exception-specific-fields"></a>Campos específicos à exceção

| Tipo | ApplicationInsights |
| --- | --- |
| TelemetryType | Exceção |
| ExceptionType | Tipo de exceção |
| ExceptionMethod | O método que cria a exceção |
| ExceptionAssembly | O assembly inclui a estrutura e a versão, bem como o token de chave pública |
| ExceptionGroup | Tipo de exceção |
| ExceptionHandledAt | Indica o nível que tratou a exceção |
| ExceptionCount | 100/(Taxa de Amostragem). Por exemplo, 4 =&gt; 25% |
| ExceptionMessage | Mensagem da exceção |
| ExceptionStack | Pilha completa da exceção |
| ExceptionHasStack | Verdadeiro, se a exceção tiver uma pilha |



### <a name="request-specific-fields"></a>Campos específicos à solicitação

| Propriedade | Descrição |
| --- | --- |
| Type | ApplicationInsights |
| TelemetryType | Solicitação |
| ResponseCode | Resposta HTTP enviada ao cliente |
| RequestSuccess | Indica êxito ou falha. True ou false. |
| RequestID | ID para identificar exclusivamente a solicitação |
| RequestName | GET/POST + URL base |
| RequestDuration | Tempo, em segundos, da duração da solicitação |
| URL | URL da solicitação, não incluindo host |
| Host | Host do servidor Web |
| URLBase | URL completa da solicitação |
| ApplicationProtocol | Tipo de protocolo usado pelo aplicativo |
| RequestCount | 100/(Taxa de Amostragem). Por exemplo, 4 =&gt; 25% |
| RequestDurationCount | 100/(Taxa de Amostragem). Por exemplo, 4 =&gt; 25% |
| RequestDurationMin | Para registros da amostra, esse campo mostra a duração mínima (em milissegundos) da solicitação nos pontos de dados representados. |
| RequestDurationMax | Para registros da amostra, esse campo mostra a duração máxima (em milissegundos) da solicitação nos pontos de dados representados |
| RequestDurationStdDev | Para registros da amostra, esse campo mostra o desvio padrão entre todas as durações (em milissegundos) da solicitação nos pontos de dados representados |

## <a name="sample-log-searches"></a>Pesquisas de log de exemplo

Essa solução não tem um conjunto de pesquisas de logs de exemplo mostrado no painel. No entanto, consultas da pesquisa de logs de exemplo com descrições são mostradas na seção [Exibir informações do Conector do Application Insights](#view-application-insights-connector-information).

## <a name="removing-the-connector-with-powershell"></a>Remover o conector com PowerShell
Com o preterimento do portal OMS, não há como configurar nem remover conexões do portal. É possível remover as conexões existentes com o seguinte script do PowerShell. Você deve ser o proprietário ou colaborador do workspace e o leitor do recurso Application Insights para executar esta operação.

```powershell
$Subscription_app = "App Subscription Name"
$ResourceGroup_app = "App ResourceGroup"
$Application = "Application Name"
$Subscription_workspace = "Workspace Subscription Name"
$ResourceGroup_workspace = "Workspace ResourceGroup"
$Workspace = "Workspace Name"

Connect-AzAccount
Set-AzContext -SubscriptionId $Subscription_app
$AIApp = Get-AzApplicationInsights -ResourceGroupName $ResourceGroup_app -Name $Application 
Set-AzContext -SubscriptionId $Subscription_workspace
Remove-AzOperationalInsightsDataSource -WorkspaceName $Workspace -ResourceGroupName $ResourceGroup_workspace -Name $AIApp.Id
```

É possível recuperar uma lista de aplicativos usando o seguinte script do PowerShell que invoca uma chamada à API REST. 

```powershell
Connect-AzAccount
$Tenant = "TenantId"
$Subscription_workspace = "Workspace Subscription Name"
$ResourceGroup_workspace = "Workspace ResourceGroup"
$Workspace = "Workspace Name"
$AccessToken = "AAD Authentication Token" 

Set-AzContext -SubscriptionId $Subscription_workspace
$LAWorkspace = Get-AzOperationalInsightsWorkspace -ResourceGroupName $ResourceGroup_workspace -Name $Workspace

$Headers = @{
    "Authorization" = "Bearer $($AccessToken)"
    "x-ms-client-tenant-id" = $Tenant
}

$Connections = Invoke-RestMethod -Method "GET" -Uri "https://management.azure.com$($LAWorkspace.ResourceId)/dataSources/?%24filter=kind%20eq%20'ApplicationInsights'&api-version=2015-11-01-preview" -Headers $Headers
$ConnectionsJson = $Connections | ConvertTo-Json
```
Este script requer um token de autenticação de portador para autenticação no Azure Active Directory. Uma maneira de recuperar esse token é usando um artigo no [site de documentação da API REST](/rest/api/loganalytics/datasources/createorupdate). Clique em **Experimentar** e faça logon na sua assinatura do Azure. É possível copiar o token de portador em **Solicitar versão prévia** conforme mostrado na imagem a seguir.


![Token de portador](media/app-insights-connector/bearer-token.png)


Também é possível recuperar uma lista de aplicativos usando uma consulta de log:

```Kusto
ApplicationInsights | summarize by ApplicationName
```

## <a name="next-steps"></a>Próximas etapas

- Use a [Pesquisa de Logs](./log-query-overview.md) para exibir informações detalhadas dos aplicativos do Application Insights.