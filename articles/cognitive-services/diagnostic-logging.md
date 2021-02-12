---
title: Registro de diagnóstico
titleSuffix: Azure Cognitive Services
description: Este guia fornece instruções passo a passo para habilitar o log de diagnóstico para um serviço de cognitiva do Azure. Esses logs fornecem dados avançados e frequentes sobre a operação de um recurso que são usados para identificação e depuração de problemas.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: article
ms.date: 06/14/2019
ms.author: erhopf
ms.openlocfilehash: e33e8fe6e626700790a3b62265c6889f06e0861b
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/08/2020
ms.locfileid: "94366597"
---
# <a name="enable-diagnostic-logging-for-azure-cognitive-services"></a>Habilitar o log de diagnóstico para serviços cognitivas do Azure

Este guia fornece instruções passo a passo para habilitar o log de diagnóstico para um serviço de cognitiva do Azure. Esses logs fornecem dados avançados e frequentes sobre a operação de um recurso que são usados para identificação e depuração de problemas. Antes de continuar, você deve ter uma conta do Azure com uma assinatura para pelo menos um serviço cognitiva, como [pesquisa na Web do Bing](./bing-web-search/overview.md), [serviços de fala](./speech-service/overview.md)ou [Luis](./luis/what-is-luis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para habilitar o log de diagnóstico, você precisará de algum lugar para armazenar os dados de log. Este tutorial usa o armazenamento do Azure e o Log Analytics.

* [Armazenamento do Azure](../azure-monitor/platform/resource-logs.md#send-to-azure-storage) – mantém os logs de diagnóstico para auditoria de política, análise estática ou backup. A conta de armazenamento não precisa estar na mesma assinatura que o recurso que emite os logs, contanto que o usuário que define a configuração tenha o acesso RBAC do Azure apropriado a ambas as assinaturas.
* [Log Analytics](../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace) -uma ferramenta de análise e pesquisa de logs flexível que permite a análise de logs brutos gerados por um recurso do Azure.

> [!NOTE]
> Opções de configuração adicionais estão disponíveis. Para saber mais, consulte [coletar e consumir dados de log dos recursos do Azure](../azure-monitor/platform/platform-logs-overview.md).

## <a name="enable-diagnostic-log-collection"></a>Habilitar coleta de log de diagnóstico  

Vamos começar habilitando o log de diagnóstico usando o portal do Azure.

> [!NOTE]
> Para habilitar esse recurso usando o PowerShell ou o CLI do Azure, use as instruções fornecidas em [coletar e consumir dados de log dos recursos do Azure](../azure-monitor/platform/platform-logs-overview.md).

1. Navegue até o Portal do Azure. Em seguida, localize e selecione um recurso de serviços cognitivas. Por exemplo, sua assinatura para Pesquisa na Web do Bing.   
2. Em seguida, no menu de navegação à esquerda, localize **monitoramento** e selecione **configurações de diagnóstico**. Esta tela contém todas as configurações de diagnóstico criadas anteriormente para esse recurso.
3. Se houver um recurso criado anteriormente que você gostaria de usar, você poderá selecioná-lo agora. Caso contrário, selecione **+ Adicionar configuração de diagnóstico**.
4. Insira um nome para a configuração. Em seguida, selecione **arquivar em uma conta de armazenamento** e **Enviar para o log Analytics**.
5. Quando for solicitado a configurar, selecione a conta de armazenamento e o espaço de trabalho do OMS que você gostaria de usar para armazenar os logs de diagnóstico. **Observação** : se você não tiver uma conta de armazenamento ou espaço de trabalho do OMS, siga os prompts para criar uma.
6. Selecione **auditoria** , **RequestResponse** e **biométricas**. Em seguida, defina o período de retenção para seus dados de log de diagnóstico. Se uma política de retenção for definida como zero, os eventos dessa categoria de log serão armazenados indefinidamente.
7. Clique em **Salvar**.

Pode levar até duas horas antes que os dados de log estejam disponíveis para consulta e análise. Portanto, não se preocupe se não vir nada imediatamente.

## <a name="view-and-export-diagnostic-data-from-azure-storage"></a>Exibir e exportar dados de diagnóstico do armazenamento do Azure

O armazenamento do Azure é uma solução de armazenamento de objetos robusta que é otimizada para armazenar grandes quantidades de dados não estruturados. Nesta seção, você aprenderá a consultar sua conta de armazenamento para obter as transações totais em um período de 30 dias e exportar os dados para o Excel.

1. No portal do Azure, localize o recurso de armazenamento do Azure que você criou na última seção.
2. No menu de navegação à esquerda, localize **monitoramento** e selecione **métricas**.
3. Use os menus suspensos disponíveis para configurar sua consulta. Para este exemplo, vamos definir o intervalo de tempo para os **últimos 30 dias** e a métrica para **Transaction**.
4. Quando a consulta for concluída, você verá uma visualização da transação nos últimos 30 dias. Para exportar esses dados, use o botão **exportar para o Excel** localizado na parte superior da página.

Saiba mais sobre o que você pode fazer com dados de diagnóstico no [armazenamento do Azure](../storage/blobs/storage-blobs-introduction.md).

## <a name="view-logs-in-log-analytics"></a>Exibir logs no Log Analytics

Siga estas instruções para explorar os dados do log Analytics para seu recurso.

1. Na portal do Azure, localize e selecione **log Analytics** no menu de navegação à esquerda.
2. Localize e selecione o recurso que você criou ao habilitar o diagnóstico.
3. Em **geral** , localize e selecione **logs**. Nessa página, você pode executar consultas em seus logs.

### <a name="sample-queries"></a>Consultas de exemplo

Aqui estão algumas consultas Kusto básicas que você pode usar para explorar os dados de log.

Execute esta consulta para todos os logs de diagnóstico dos serviços cognitivas do Azure por um período de tempo especificado:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
```

Execute esta consulta para ver os 10 logs mais recentes:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| take 10
```

Execute esta consulta para agrupar operações por **recurso** :

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES" |
summarize count() by Resource
```
Execute esta consulta para localizar o tempo médio necessário para executar uma operação:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize avg(DurationMs)
by OperationName
```

Execute esta consulta para exibir o volume de operações ao longo do tempo dividido por OperationName com contagens compartimentalizados para cada 10s.

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize count()
by bin(TimeGenerated, 10s), OperationName
| render areachart kind=unstacked
```

## <a name="next-steps"></a>Próximas etapas

* Para entender como habilitar o registro em log e também as métricas e as categorias de log com suporte nos vários serviços do Azure, leia a [visão geral das métricas](../azure-monitor/platform/data-platform.md) nos artigos Microsoft Azure e [visão geral dos logs de diagnóstico do Azure](../azure-monitor/platform/platform-logs-overview.md) .
* Leia estes artigos para saber mais sobre os hubs de eventos:
  * [O que são os Hubs de Eventos do Azure?](../event-hubs/event-hubs-about.md)
  * [Introdução aos Hubs de Eventos](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)
* Leia [Baixar métricas e logs de diagnósticos do Armazenamento do Microsoft Azure](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-blobs).
* Leia [entender as pesquisas de log nos logs de Azure monitor](../azure-monitor/log-query/log-query-overview.md).