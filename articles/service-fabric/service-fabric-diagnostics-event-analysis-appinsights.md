---
title: "Análise de Evento do Azure Service Fabric com Application Insights | Microsoft Docs"
description: "Saiba mais sobre visualização e análise de eventos utilizando o Application Insights para o monitoramento e diagnóstico de clusters do Azure Service Fabric."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/26/2017
ms.author: dekapur
ms.openlocfilehash: 4085a607b800f4f4f155cdc266bc203b0858fd7c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="event-analysis-and-visualization-with-application-insights"></a>Visualização e análise de eventos com o Application Insights

O Azure Application Insights é uma plataforma extensível para monitoramento e diagnóstico de aplicativos. Inclui uma poderosa ferramenta de análise e consulta, visualizações e dashboard personalizável e outras opções, incluindo alertas automatizados. É a plataforma recomendada para monitoramento e diagnóstico para aplicativos e serviços do Service Fabric.

## <a name="setting-up-application-insights"></a>Configurando o Application Insights

### <a name="creating-an-ai-resource"></a>Criar um Recurso do AI

Para criar um recurso do AI, vá para o Azure Marketplace e pesquise por "Application Insights". Ele deve aparecer como a primeira solução (está na categoria "Web + Móvel"). Clique em **Criar** quando estiver diante do recurso certo (confirme se seu caminho corresponde à imagem abaixo).

![Novo recurso do Application Insights](media/service-fabric-diagnostics-event-analysis-appinsights/create-new-ai-resource.png)

Você precisará preencher algumas informações para fornecer o recurso corretamente. No campo *Tipo do Aplicativo* utilize "aplicativo Web ASP.NET" se você estiver utilizando qualquer um dos modelos de programação do Service Fabric ou publicando um aplicativo .NET no cluster. Utilize "Geral" se você estiver implantando contêineres e executáveis do convidado. Em geral, o padrão para usar o "aplicativo Web ASP.NET" para manter suas opções abertas no futuro. O nome é de acordo com sua preferência e tanto o grupo de recursos como a assinatura são pós-implantação alteráveis do recurso. É recomendável que seu recurso do AI esteja no mesmo grupo de recursos do seu cluster. Caso precise de mais informações, consulte [Criar um recurso do Application Insights](../application-insights/app-insights-create-new-resource.md)

É necessário a Chave de Instrumentação do AI para configurar o AI com sua ferramenta de agregação de eventos. Quando o recurso do AI for configurado (demora alguns minutos após a validação da implantação), navegue até ele e localize a seção **Propriedades**  na barra de navegação esquerda. Uma nova folha será aberta e exibirá uma *CHAVE DE INSTRUMENTAÇÃO*. Se for necessário alterar a assinatura ou o grupo de recursos do recurso, isso também poderá feito aqui.

### <a name="configuring-ai-with-wad"></a>Configurar o AI com WAD

Há duas maneiras primárias de enviar dados do WAD para o AI do Azure, o que é alcançado pela adição de um coletor do AI para a configuração WAD, conforme detalhado [neste artigo](../monitoring-and-diagnostics/azure-diagnostics-configure-application-insights.md).

#### <a name="add-an-ai-instrumentation-key-when-creating-a-cluster-in-azure-portal"></a>Adicione uma Chave de Instrumentação do AI ao criar um cluster no Portal do Azure

![Adicionar uma AIKey](media/service-fabric-diagnostics-event-analysis-appinsights/azure-enable-diagnostics.png)

Ao criar um cluster, se o Diagnóstico estiver ativado em "On", um campo opcional para inserir uma chave de Instrumentação do Application Insights será exibido. Se você colar a IKey do AI aqui, o coletor do AI será configurado automaticamente para você no modelo do Resource Manager que é utilizado para implantar seu cluster.

#### <a name="add-the-ai-sink-to-the-resource-manager-template"></a>Adicione o coletor do AI ao modelo do Resource Manager

No "WadCfg" do modelo do Resource Manager, adicione um "Coletor", incluindo as duas alterações a seguir:

1. Adicione a configuração do coletor:

    ```json
    "SinksConfig": {
        "Sink": [
            {
                "name": "applicationInsights",
                "ApplicationInsights": "***ADD INSTRUMENTATION KEY HERE***"
            }
        ]
    }

    ```

2. Inclua o Coletor na DiagnosticMonitorConfiguration, adicionando a seguinte linha em "DiagnosticMonitorConfiguration" do "WadCfg":

    ```json
    "sinks": "applicationInsights"
    ```

Nos dois trechos de código acima, o nome "applicationInsights" foi utilizado para descrever o coletor. Isso não é um requisito e, enquanto o nome do coletor estiver incluído em "coletores", você poderá definir o nome para qualquer cadeia de caracteres.

Atualmente, os logs do cluster aparecerão como rastreamentos no visualizador de logs do AI. Como a maioria dos rastreamentos provenientes da plataforma é do nível “Informativo”, considere também a alteração da configuração do coletor para enviar somente logs do tipo “Crítico” ou “Erro”. Isso pode ser feito adicionando "Canais" ao seu coletor, conforme demonstrado [neste artigo](../monitoring-and-diagnostics/azure-diagnostics-configure-application-insights.md).

>[!NOTE]
>Se você utilizar IKey do AI incorretamente no portal ou no modelo do Resource Manager, será necessário alterar manualmente a chave e atualizar o cluster/reimplementá-la. 

### <a name="configuring-ai-with-eventflow"></a>Configurar AI com EventFlow

Se você estiver utilizando EventFlow para agregar eventos, certifique-se de importar o pacote `Microsoft.Diagnostics.EventFlow.Output.ApplicationInsights`NuGet. O seguinte deve ser incluído na seção *saídas* do *eventFlowConfig.json*:

```json
"outputs": [
    {
        "type": "ApplicationInsights",
        // (replace the following value with your AI resource's instrumentation key)
        "instrumentationKey": "00000000-0000-0000-0000-000000000000"
    }
]
```

Certifique-se de fazer as alterações necessárias nos filtros, assim como incluir quaisquer outras entradas (em conjunto com os respectivos pacotes NuGet).

## <a name="aisdk"></a>AI.SDK

Em geral, é recomendável utilizar EventFlow e WAD como soluções de agregação porque permitem uma abordagem mais modular para diagnósticos e monitoramento, ou seja, se você deseja alterar suas saídas do EventFlow isso não requer nenhuma alteração na sua instrumentação real, apenas uma modificação simples do seu arquivo de configuração. No entanto, se você decidir investir na utilização do Application Insights e não é provável que mude para uma plataforma diferente, será necessário procurar usar o novo SDK do AI para agregar eventos e enviá-los ao AI. Isso significa que não será mais necessário configurar o EventFlow para enviar os dados para o AI, mas, em vez disso, irá instalar o pacote do NuGet Service Fabric do Application Insight. Detalhes sobre o pacote podem ser encontrados [aqui](https://github.com/Microsoft/ApplicationInsights-ServiceFabric).

[O suporte do Application Insights para Microsserviços e Contêineres](https://azure.microsoft.com/app-insights-microservices/) apresenta alguns dos novos recursos que estão sendo trabalhados (atualmente ainda em versão beta), os quais permitem que você tenha opções de monitoramento prontas para uso com o AI. Isso inclui o acompanhamento de dependência (utilizado na construção de um AppMap de todos os seus serviços e aplicativos em um cluster e a comunicação entre eles) e uma melhor correlação de rastreamentos provenientes de seus serviços (ajuda a identificar melhor um problema no fluxo de trabalho de um aplicativo ou serviço).

Se você estiver desenvolvendo em .NET e provavelmente estará usando alguns dos modelos de programação do Service Fabric, e disposto a utilizar o AI como sua plataforma para visualizar e analisar dados de eventos e logs, então, é recomendável que você vá via a rota SDK do AI como seu fluxo de trabalho de diagnóstico e monitoramento. Leia [este](../application-insights/app-insights-asp-net-more.md) e [este](../application-insights/app-insights-asp-net-trace-logs.md) para começar a utilizar o AI para coletar e exibir seus logs.

## <a name="navigating-the-ai-resource-in-azure-portal"></a>Navegar pelo recurso do AI no Portal do Azure

Depois de configurar o AI como saída para seus eventos e logs, as informações deverão começar a aparecer no recurso do AI em alguns minutos. Navegue até o recurso do AI, o qual o direcionará para o dashboard de recurso do AI. Clique em **Pesquisar** na barra de tarefas do AI para visualizar os últimos rastreamentos recebidos e poder filtrar através deles.

*Metrics Explorer* é uma ferramenta útil para criar painéis personalizados com base em métricas que seus aplicativos, serviços e clusters podem estar reportando. Consulte [Exploring Metrics no Application Insights](../application-insights/app-insights-metrics-explorer.md) para configurar alguns gráficos com base nos dados que você está coletando.

Ao clicar em **Análise** você será direcionado para o portal de Análise do Application Insights, onde poderá consultar eventos e rastreamentos com maior escopo e opcionalidade. Leia mais sobre isso em [Análise no Application Insights](../application-insights/app-insights-analytics.md).

## <a name="next-steps"></a>Próximas etapas

* [Configurar alertas no AI](../application-insights/app-insights-alerts.md) para ser notificado sobre mudanças no desempenho ou uso
* [Detecção Inteligente no Application Insights](../application-insights/app-insights-proactive-diagnostics.md) realiza uma análise pró-ativa da telemetria enviada ao AI para avisá-lo sobre possíveis problemas de desempenho