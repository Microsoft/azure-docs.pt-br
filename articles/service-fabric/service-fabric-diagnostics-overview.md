---
title: "Visão geral de diagnóstico e monitoramento do Azure Service Fabric| Microsoft Docs"
description: "Saiba mais sobre monitoramento e diagnóstico para clusters, aplicativos e serviços do Service Fabric do Azure."
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
ms.date: 07/17/2017
ms.author: dekapur
ms.openlocfilehash: 88f4a23f89a1c8fd88db1df3a7ff03ae5df64c0f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="monitoring-and-diagnostics-for-azure-service-fabric"></a>Monitoramento e diagnóstico no Azure Service Fabric

O monitoramento e o diagnóstico são essenciais para o desenvolvimento, os testes e a implantação de aplicativos e serviços em qualquer ambiente. As soluções do Service Fabric funcionam melhor quando você planeja e implementa o monitoramento e o diagnóstico que ajudam a garantir que aplicativos e serviços funcionem conforme o esperado em um ambiente de desenvolvimento local ou na produção.

As principais metas do monitoramento e diagnóstico são:
* Detectar e diagnosticar problemas de hardware e infraestrutura
* Detectar problemas de software e aplicativos e reduzir o tempo de inatividade do serviço
* Entender o consumo de recursos e ajudar a orientar as decisões de operações
* Otimizar o desempenho de aplicativos, serviços e infraestrutura
* Gerar informações de negócios e identificar áreas de aprimoramento


O fluxo de trabalho geral do monitoramento e diagnóstico consiste em três etapas:

1. **Geração de eventos**: isso inclui eventos (logs, rastreamentos, eventos personalizados) nos níveis de infraestrutura (cluster), plataforma e aplicativo/serviço
2. **Agregação de eventos**: os eventos gerados precisam ser coletados e agregados antes que possam ser exibidos
3. **Análise**: os eventos precisam ser visualizado e estar acessíveis em algum formato, para permitir a análise e a exibição conforme necessário

Estão disponíveis vários produtos que abrangem essas três áreas, e você é livre para escolher tecnologias diferentes para cada um. É importante garantir que as várias partes trabalhem juntas para fornecer uma solução de monitoramento de ponta a ponta para o cluster.

## <a name="event-generation"></a>Geração de evento

A primeira etapa no fluxo de trabalho de monitoramento e diagnóstico é a criação e a geração de eventos e logs. Esses eventos, logs e rastreamentos são gerados em dois níveis: a camada de plataforma (incluindo o cluster, os computadores ou as ações do Service Fabric) ou a camada de aplicativo (qualquer instrumentação adicionada aos aplicativos e serviços implantados no cluster). Os eventos em cada um desses níveis são personalizáveis, embora o Service Fabric forneça instrumentação por padrão.

Leia mais sobre [eventos no nível de plataforma](service-fabric-diagnostics-event-generation-infra.md) e [eventos no nível de aplicativo](service-fabric-diagnostics-event-generation-app.md) para entender o que é fornecido e como adicionar mais instrumentação.

Depois de tomar uma decisão sobre o provedor de log que deseja usar, você precisa garantir que os logs estejam sendo agregados e armazenados corretamente.

## <a name="event-aggregation"></a>Agregação de eventos

Para coletar os logs e eventos sendo gerados pelos aplicativos e o cluster, normalmente, é recomendável usar o [Diagnóstico do Azure](service-fabric-diagnostics-event-aggregation-wad.md) (mais semelhante à coleta de logs baseada em agente) ou o [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) (coleta de log no processo).

Coletar logs de aplicativo usando a extensão do Diagnóstico do Azure será uma boa opção para serviços do Service Fabric se o conjunto de origens e destinos de log não for alterado com frequência e se houver um mapeamento direto entre as origens e seus destinos. O motivo para isso é que a configuração de Diagnóstico do Azure ocorre na camada do Resource Manager. Portanto, para fazer alterações significativas na configuração, é necessário atualizar/reimplantar o cluster. Além disso, é utilizado da melhor forma para garantir que os logs sejam armazenados em algum lugar um pouco mais permanente, do qual possam ser acessados por várias plataformas de análise. Isso significa que ele acaba sendo menos eficiente como pipeline do que uma opção como o EventFlow.

O uso do [EventFlow](https://github.com/Azure/diagnostics-eventflow) permite que os serviços enviem logs diretamente para uma plataforma de análise e visualização e/ou para o armazenamento. Outras bibliotecas (ILogger, Serilog etc.) que podem ser usadas para a mesma finalidade, mas EventFlow tem a vantagem de ter foi projetado especificamente para coleta de log em processo e para dar suporte a serviços de Service Fabric. Isso tende a ter várias vantagens possíveis:

* Fácil configuração e implantação
    * A configuração de coleta de dados de diagnóstico é apenas uma parte da configuração do serviço. É fácil sempre mantê-la "sincronizada" com o restante do aplicativo
    * A configuração por aplicativo ou por serviço é facilmente realizável
    * Para a configuração de destinos de dados por meio do EventFlow, basta adicionar o pacote do NuGet apropriado e alterar o arquivo *eventFlowConfig.json*
* Flexibilidade
    * O aplicativo pode enviar os dados sempre que eles forem necessários, desde que exista uma biblioteca de cliente que ofereça suporte ao sistema de armazenamento de dados de destino. Novos destinos podem ser adicionados conforme desejado
    * Regras de captura complexa, filtragem e agregação de dados podem ser implementadas
* Acesso a dados de aplicativos internos e contexto
    * O subsistema de diagnóstico em execução dentro do processo de serviço/do aplicativo pode facilmente aumentar os rastreamentos com informações contextuais

Algo a observar é que essas duas opções não são mutuamente exclusivas. Embora seja possível realizar um trabalho semelhante com o uso de uma ou outra, também pode fazer sentido configurar ambas. Na maioria das situações, a combinação de um agente à coleta no processo pode levar a um fluxo de trabalho de monitoramento mais confiável. A extensão do Diagnóstico do Azure (agente) pode ser seu caminho escolhido para os logs no nível de plataforma, embora você possa usar o EventFlow (coleção em processo) para os logs no nível de aplicativo. Após descobrir o que funciona melhor para você, é hora de pensar em como deseja que os dados sejam exibidos e analisados.

## <a name="event-analysis"></a>Análise de eventos

Existem várias plataformas excelentes no mercado para análise e visualização de dados de monitoramento e diagnóstico. Os dois que recomendamos são [OMS](service-fabric-diagnostics-event-analysis-oms.md) e [Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md), devido à sua melhor integração ao Service Fabric. Porém, você também deve examinar o [Elastic Stack](https://www.elastic.co/products) (especialmente se estiver considerando a execução de um cluster em um ambiente offline), o [Splunk](https://www.splunk.com/) ou qualquer outra plataforma de sua preferência.

Os principais pontos para qualquer plataforma que você escolher devem incluir seu grau de experiência com a interface do usuário e as opções de consulta, a capacidade de visualizar dados e criar painéis facilmente legíveis e as ferramentas adicionais que eles oferecem para aprimorar o monitoramento, como alertas automatizados.

Além da plataforma que você escolhe, ao configurar um cluster do Service Fabric como um recurso do Azure, você também obtém acesso ao monitorando do Azure pronto para uso para máquinas, o que podem ser útil para monitoramento específico de desempenho e métricas.

### <a name="azure-monitor"></a>Azure Monitor

Você pode usar [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md) para monitorar muitos dos recursos do Azure em que um cluster de Service Fabric é criado. Um conjunto de métricas para o [conjunto de dimensionamento de máquinas virtuais](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets) e para [máquinas virtuais](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesetsvirtualmachines) individuais é automaticamente coletado e exibido no portal do Azure. Para exibir as informações coletadas, no portal do Azure, selecione o grupo de recursos que contém o cluster de Service Fabric. Em seguida, selecione o conjunto de dimensionamento de máquinas virtuais que você deseja exibir. No **monitoramento** seção, selecione **métricas** para exibir um gráfico dos valores.

![Exibição de informações de métrica coletadas no portal do Azure](media/service-fabric-diagnostics-overview/azure-monitoring-metrics.png)

Para personalizar os gráficos, siga as instruções em [métricas no Microsoft Azure](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md). Você também pode criar alertas com base nessas métricas, conforme descrito em [criar alertas no Azure Monitor para serviços do Azure](../monitoring-and-diagnostics/insights-alerts-portal.md). Você pode enviar alertas para um serviço de notificação usando webhooks, conforme descrito em [Configurar um webhook em um alerta de métrica Azure](../monitoring-and-diagnostics/insights-webhooks-alerts.md). O Azure Monitor oferece suporte a apenas uma assinatura. Se você precisar monitorar várias assinaturas ou se precisar de recursos adicionais, o [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/), parte do Microsoft Operations Management Suite, fornece uma solução de gerenciamento de TI holística para infraestruturas local e baseada em nuvem. Você pode rotear dados do Azure Monitor diretamente para o Log Analytics para que você possa ver as métricas e logs de todo o ambiente em um único lugar.

## <a name="next-steps"></a>Próximas etapas

### <a name="watchdogs"></a>Watchdogs

Um watchdog é um serviço separado que pode inspecionar a integridade e a carga entre serviços e informar a integridade de qualquer coisa na hierarquia de modelo de integridade. Isso pode ajudar a evitar erros que não seriam detectados com base no modo de exibição de um único serviço. Os watchdogs também são um bom local para hospedar código que executa ações corretivas sem interação do usuário (por exemplo, limpar arquivos de log no armazenamento em determinados intervalos de tempo). Você pode encontrar uma implementação de serviço de watchdog de exemplo [aqui](https://github.com/Azure-Samples/service-fabric-watchdog-service).

Introdução ao modo de geração de eventos e logs no [nível de plataforma](service-fabric-diagnostics-event-generation-infra.md) e no [nível de aplicativo](service-fabric-diagnostics-event-generation-app.md).