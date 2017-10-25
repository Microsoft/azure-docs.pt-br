---
title: Exibir dados do aplicativo do Azure Application Insights | Microsoft Docs
description: "Use a solução Conector do Application Insights para diagnosticar problemas de desempenho e entender o que os usuários fazem com seu aplicativo quando ele é monitorado com o Application Insights."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: 49280cad-3526-43e1-a365-c6a3bf66db52
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: banders
ms.openlocfilehash: fe6c003e095b25cf3ec3430fc68dcd399150b3ed
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="application-insights-connector-solution-preview-in-operations-management-suite-oms"></a>Solução Conector do Application Insights (Versão Prévia) no OMS (Operations Management Suite)

![Símbolo do Application Insights](./media/log-analytics-app-insights-connector/app-insights-connector-symbol.png)

A solução Conector do Application Insights ajuda você a diagnosticar problemas de desempenho e entender o que os usuários fazem com seu aplicativo quando ele é monitorado com o [Application Insights](../application-insights/app-insights-overview.md). Exibições da mesma telemetria de aplicativo que os desenvolvedores visualizam no Application Insights estão disponíveis no OMS. No entanto, ao integrar os aplicativos do Application Insights ao OMS, a visibilidade dos aplicativos aumenta, pois os dados do aplicativo e da operação ficam em um único lugar. Ter as mesmas exibições ajuda na colaboração com os desenvolvedores de aplicativos. As exibições comuns podem ajudar a reduzir o tempo para detectar e resolver problemas do aplicativo e da plataforma.

Ao usar a solução, você pode:

- Exibir todos os aplicativos do Application Insights em um único lugar, mesmo quando eles estiverem em assinaturas diferentes do Azure
- Correlacionar os dados da infraestrutura com os dados do aplicativo
- Visualizar os dados do aplicativo com perspectivas na pesquisa de logs
- Dinamizar dos dados do Log Analytics para o aplicativo do Application Insights no OMS e nos portais do Azure

## <a name="connected-sources"></a>Fontes conectadas

Ao contrário da maioria das outras soluções do Log Analytics, os dados não são coletados para o Conector do Application Insights por agentes. Todos os dados usados pela solução vêm diretamente do Azure.

| Fonte Conectada | Suportado | Descrição |
| --- | --- | --- |
| [Agentes do Windows](log-analytics-windows-agents.md) | Não | A solução não coleta informações de agentes do Windows. |
| [Agentes do Linux](log-analytics-linux-agents.md) | Não | A solução não coleta informações de agentes do Linux. |
| [Grupo de gerenciamento do SCOM](log-analytics-om-agents.md) | Não | A solução não coleta informações de agentes em um grupo de gerenciamento de SCOM conectado. |
| [Conta de armazenamento do Azure](log-analytics-azure-storage.md) | Não | A solução não coleta informações do armazenamento do Azure. |

## <a name="prerequisites"></a>Pré-requisitos

- Para acessar as informações do Conector do Application Insights, você deve ter uma assinatura do Azure
- É necessário ter, pelo menos, um recurso do Application Insights configurado.
- Você deve ser o proprietário ou colaborador do recurso do Application Insights.

## <a name="configuration"></a>Configuração

1. Habilite a solução Análise de Aplicativos Web do Azure no [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ApplicationInsights?tab=Overview) ou usando o processo descrito em [Adicionar soluções do Log Analytics por meio da Galeria de Soluções](log-analytics-add-solutions.md).
2. No portal do OMS, clique em **Configurações** &gt; **Dados** &gt; **Application Insights**.
3. Em **Selecionar uma assinatura**, selecione uma assinatura que tem os recursos do Application Insights e, em seguida, em **Nome do Aplicativo**, selecione um ou mais aplicativos.
4. Clique em **Salvar**.

Em aproximadamente 30 minutos, os dados ficarão disponíveis e o bloco do Application Insights será atualizado com os dados, como a seguinte imagem:

![Bloco do Application Insights](./media/log-analytics-app-insights-connector/app-insights-tile.png)

Outros pontos a serem considerados:

- Só é possível vincular os aplicativos do Application Insights a um único espaço de trabalho do OMS.
- Só é possível vincular os [recursos do Application Insights Standard ou Premium](https://azure.microsoft.com/pricing/details/application-insights) ao Log Analytics do OMS. No entanto, você pode usar a camada Gratuita do Log Analytics.

## <a name="management-packs"></a>Pacotes de gerenciamento

Essa solução não instala nenhum pacote de gerenciamento nos grupos de gerenciamento conectados.

## <a name="use-the-solution"></a>Usar a solução

As próximas seções descrevem como você pode usar as folhas mostradas no painel do Application Insights para exibir e interagir com os dados de seus aplicativos.

### <a name="view-application-insights-connector-information"></a>Exibir informações do Conector do Application Insights

Clique no bloco **Application Insights** para abrir o painel **Application Insights** para ver as folhas a seguir.

![Painel do Application Insights](./media/log-analytics-app-insights-connector/app-insights-dash01.png)

![Painel do Application Insights](./media/log-analytics-app-insights-connector/app-insights-dash02.png)

O painel inclui as folhas mostradas na tabela. Cada folha lista os 10 principais itens que correspondem aos critérios da folha para o escopo e o intervalo de tempo especificados. É possível executar uma pesquisa de logs que retorna todos os registros ao clicar em **Ver todos** na parte inferior da folha ou ao clicar no cabeçalho da folha.

[!include[log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

| **Coluna** | **Descrição** |
| --- | --- |
| Aplicativos – Número de aplicativos | Mostra o número de aplicativos nos recursos do Aplicativo. Também lista os nomes de aplicativo e para cada um, a contagem de registros do aplicativo. Clique no número para executar uma pesquisa de logs para <code>Type=ApplicationInsights &#124; measure sum(SampledCount) by ApplicationName</code> <br><br>  Clique em um nome de aplicativo para executar uma pesquisa de logs no aplicativo, que mostra registros de aplicativos por host, registros por tipo de telemetria e todos os dados por tipo (com base no último dia). |
| Volume de Dados – hosts que enviam os dados | Mostra o número de hosts do computador que estão enviando dados. Também lista os hosts do computador e a contagem de registros de cada host. Clique no número para executar uma pesquisa de logs para <code>Type=ApplicationInsights &#124; measure sum(SampledCount) by Host</code> <br><br> Clique em um nome do computador para executar uma pesquisa de logs no host, que mostra registros de aplicativos por host, registros por tipo de telemetria e todos os dados por tipo (com base no último dia). |
| Disponibilidade – resultados do teste na Web | Mostra um gráfico de rosca dos resultados de teste na Web, indicando aprovação ou reprovação. Clique no gráfico para executar uma pesquisa de logs para <code>Type=ApplicationInsights TelemetryType=Availability &#124; measure sum(SampledCount) by AvailabilityResult</code> <br><br> Os resultados mostram o número de aprovações e reprovações de todos os testes. Ela mostra todos os aplicativos Web com o tráfego para o último minuto. Clique em um nome de aplicativo para exibir uma pesquisa de logs, mostrando os detalhes dos testes na Web com falha. |
| Solicitações do Servidor – solicitações por hora | Mostra um gráfico de linhas das solicitações do servidor por hora para vários aplicativos. Focalize uma linha no gráfico para ver os três primeiros aplicativos que receberam solicitações em um ponto no tempo. Também mostra uma lista dos aplicativos que receberam solicitações e o número de solicitações para o período selecionado. <br><br>Clique no gráfico para executar uma pesquisa de logs para <code>Type=ApplicationInsights TelemetryType=Request &#124; measure sum(SampledCount) by ApplicationName interval 1hour</code>, que mostra um gráfico de linhas mais detalhado das solicitações do servidor por hora para vários aplicativos. <br><br> Clique em um aplicativo na lista para executar uma pesquisa de logs para <code>Type=ApplicationInsights  ApplicationName=yourapplicationname  TelemetryType=Request</code>, que mostra uma lista de solicitações, gráficos das solicitações ao longo do tempo e duração da solicitação e uma lista de códigos de resposta da solicitação.   |
| Falhas – solicitações com falha por hora | Mostra um gráfico de linhas das solicitações de aplicativo com falha por hora. Focalize o gráfico para ver os três primeiros aplicativos com solicitações com falha em um ponto no tempo. Também mostra uma lista de aplicativos com o número de solicitações com falha para cada um. Clique no gráfico para executar uma pesquisa de logs para <code>Type=ApplicationInsights TelemetryType=Request  RequestSuccess = false &#124; measure sum(SampledCount) by ApplicationName interval 1hour</code>, que mostra um gráfico de linhas mais detalhado das solicitações de aplicativo com falha. <br><br>Clique em um item na lista para executar uma pesquisa de logs para <code>Type=ApplicationInsights ApplicationName=yourapplicationname TelemetryType=Request  RequestSuccess=false</code>, que mostra as solicitações com falha, gráficos das solicitações com falha ao longo do tempo e duração da solicitação e uma lista de códigos de resposta da solicitação. |
| Exceções – exceções por hora | Mostra um gráfico de linhas das exceções por hora. Focalize o gráfico para ver os três primeiros aplicativos com exceções com falha em um ponto no tempo. Também mostra uma lista de aplicativos com o número de exceções com falha para cada um. Clique no gráfico para executar uma pesquisa de logs para <code>Type=ApplicationInsights TelemetryType=Exception &#124; measure sum(SampledCount) by ApplicationName interval 1hour</code>, que mostra um gráfico de link mais detalhado das exceções. <br><br>Clique em um item na lista para executar uma pesquisa de logs para <code>Type=ApplicationInsights  ApplicationName=yourapplicationname TelemetryType=Exception</code>, que mostra uma lista de exceções, gráficos das exceções ao longo do tempo e solicitações com falha e uma lista dos tipos de exceção.  |

### <a name="view-the-application-insights-perspective-with-log-search"></a>Exibir a perspectiva do Application Insights com a pesquisa de logs

Quando você clica em um item no painel, você vê uma perspectiva do Application Insights mostrada na pesquisa. A perspectiva fornece uma visualização estendida, com base no tipo de telemetria selecionado. Portanto, o conteúdo de visualização muda para tipos diferentes de telemetria.

Quando você clica em qualquer lugar na folha Aplicativos, você vê a perspectiva de **Aplicativos** padrão.

![Perspectiva de Aplicativos do Application Insights](./media/log-analytics-app-insights-connector/applications-blade-drill-search.png)

A perspectiva mostra uma visão geral do aplicativo selecionado.

A folha **Disponibilidade** mostra uma exibição de perspectiva diferente na qual você pode ver os resultados de teste na Web e as solicitações com falha relacionadas.

![Perspectiva de Disponibilidade do Application Insights](./media/log-analytics-app-insights-connector/availability-blade-drill-search.png)

Quando você clica em qualquer lugar nas folhas **Solicitações do Servidor** ou **Falhas**, os componentes da perspectiva mudam para dar a você uma visualização relacionada às solicitações.

![Folha Falhas do Application Insights](./media/log-analytics-app-insights-connector/server-requests-failures-drill-search.png)

Quando você clica em qualquer lugar na folha **Exceções**, você vê uma visualização adaptada às exceções.

![Folha Exceções do Application Insights](./media/log-analytics-app-insights-connector/exceptions-blade-drill-search.png)

Independentemente de você clicar em algo no painel **Conector do Application Insights**, na própria página **Pesquisa**, qualquer consulta que retorna dados do Application Insights mostra a perspectiva do Application Insights. Por exemplo, se você estiver exibindo dados do Application Insights, uma consulta **&#42;** também mostrará a guia de perspectiva como a seguinte imagem:

![Application Insights ](./media/log-analytics-app-insights-connector/app-insights-search.png)

Os componentes da perspectiva são atualizados, dependendo da consulta de pesquisa. Isso significa que você pode filtrar os resultados usando qualquer campo de pesquisa que fornece a capacidade de ver os dados de:

- Todos os aplicativos
- Um único aplicativo selecionado
- Um grupo de aplicativos

### <a name="pivot-to-an-app-in-the-azure-portal"></a>Dinamizar para um aplicativo no portal do Azure

As folhas do Conector do Application Insights foram projetadas para permitir que você dinamiza para o aplicativo selecionado do Application Insights *quando usar o portal do OMS*. Use a solução como uma plataforma de monitoramento de alto nível que ajuda você a solucionar problemas de um aplicativo. Quando você encontrar um problema potencial em um de seus aplicativos conectados, faça uma busca detalhada na pesquisa do OMS ou dinamize diretamente para o aplicativo do Application Insights.

Para dinamizar, clique nas reticências (**...**) exibidas ao final de cada linha e selecione **Abrir no Application Insights**.

>[!NOTE]
>A opção **Abrir no Application Insights** não está disponível no portal do Azure.

![Abrir no Application Insights](./media/log-analytics-app-insights-connector/open-in-app-insights.png)

### <a name="sample-corrected-data"></a>Dados corrigido por amostra

O Application Insights fornece a *[correção de amostragem](../application-insights/app-insights-sampling.md)* para ajudar a reduzir o tráfego de telemetria. Quando você habilita a amostragem no aplicativo do Application Insights, você obtém um número reduzido de entradas armazenadas no Application Insights e no OMS. Embora a consistência dos dados seja preservada na página e nas perspectivas do **Conector do Application Insights**, você deverá corrigir manualmente os dados amostrados das consultas personalizadas.

Este é um exemplo de correção de amostragem em uma consulta da pesquisa de logs:

```
Type=ApplicationInsights | measure sum(SampledCount) by TelemetryType
```

O campo **Contagem Amostrada** está presente em todas as entradas e mostra o número de pontos de dados que a entrada representa. Se você ativar a amostragem no aplicativo do Application Insights, a **Contagem Amostrada** será maior que 1. Para contar o número real de entradas geradas pelo aplicativo, some os campos **Contagem Amostrada**.

A amostragem afeta somente o número total de entradas geradas pelo aplicativo. Não é necessário corrigir a amostragem em campos de métrica como **RequestDuration** ou **AvailabilityDuration**, porque esses campos mostram a média das entradas representadas.

## <a name="input-data"></a>Dados de entrada

A solução recebe os seguintes tipos de telemetria de dados dos aplicativos conectados do Application Insights:

- Disponibilidade
- Exceções
- Solicitações
- Exibições de página – para que o espaço de trabalho receba exibições de página, você deve configurar os aplicativos para coletar essas informações. Para obter mais informações, consulte [PageViews](../application-insights/app-insights-api-custom-events-metrics.md#page-views).
- Eventos personalizados – para que o espaço de trabalho receba eventos personalizados, você deve configurar os aplicativos para coletar essas informações. Para obter mais informações, consulte [TrackEvent](../application-insights/app-insights-api-custom-events-metrics.md#trackevent).

Os dados são recebidos pelo OMS por meio do Application Insights assim que estiverem disponíveis.

## <a name="output-data"></a>Dados de saída

Um registro com um *tipo* de *ApplicationInsights* é criado para cada tipo de dados de entrada. Os registros do Application Insights têm as propriedades mostradas nas seguintes seções:

### <a name="generic-fields"></a>Campos genéricos

| Propriedade | Descrição |
| --- | --- |
| Tipo | ApplicationInsights |
| ClientIP |   |
| TimeGenerated | Hora do registro |
| ApplicationId | Chave de instrumentação do aplicativo do Application Insights |
| ApplicationName | Nome do aplicativo do Application Insights |
| RoleInstance | ID do host do servidor |
| DeviceType | Dispositivo de cliente |
| ScreenResolution |   |
| Continente | Continente de origem da solicitação |
| País | País de origem da solicitação |
| Província | Província, Estado ou localidade de origem da solicitação |
| City | Cidade de origem da solicitação |
| isSynthetic | Indica se a solicitação foi criada por um usuário ou por um método automatizado. True = gerado pelo usuário ou false = método automatizado |
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
| Tipo | ApplicationInsights |
| TelemetryType | Solicitação |
| ResponseCode | Resposta HTTP enviada ao cliente |
| RequestSuccess | Indica êxito ou falha. Verdadeiro ou falso. |
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

## <a name="next-steps"></a>Próximas etapas

- Use a [Pesquisa de Logs](log-analytics-log-searches.md) para exibir informações detalhadas dos aplicativos do Application Insights.
