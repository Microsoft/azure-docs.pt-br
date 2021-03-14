---
title: Azure Monitor para o cache do Azure para Redis | Microsoft Docs
description: Este artigo descreve o recurso Azure Monitor para Cache do Azure para Redis, que fornece aos proprietários do cache uma rápida compreensão dos problemas de desempenho e utilização.
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 09/10/2020
ms.openlocfilehash: fee454073c50b9542e140576ef0629a39b8f4294
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100573463"
---
# <a name="explore-azure-monitor-for-azure-cache-for-redis"></a>Explorar Azure Monitor para o cache do Azure para Redis

Para todos os seus recursos do cache do Azure para Redis, Azure Monitor para o cache do Azure para Redis fornece uma exibição unificada e interativa de:

- Desempenho geral
- Falhas
- Capacity
- Integridade operacional

Este artigo ajuda você a entender os benefícios dessa nova experiência de monitoramento. Ele também mostra como modificar e adaptar a experiência para atender às necessidades exclusivas de sua organização.

## <a name="introduction"></a>Introdução

Antes de iniciar a experiência, você deve entender como Azure Monitor para o Cache do Azure para Redis apresenta as informações visualmente.

Ela oferece:

- **Na perspectiva da escala** dos recursos do Cache do Azure para Redis em apenas um local, em todas as suas assinaturas. Você pode fazer o escopo seletivo somente para as assinaturas e os recursos que deseja avaliar.

- **Análise de busca detalhada** de um recurso específico do Cache do Azure para Redis. Você pode diagnosticar problemas e ver a análise detalhada de utilização, falhas, capacidade e operações. Selecione qualquer uma dessas categorias para ter uma visão detalhada das informações relevantes.  

- A **personalização** dessa experiência, que é criada sobre modelos de pasta de trabalho do Azure Monitor. A experiência permite alterar quais métricas são exibidas e modificar ou definir limites que se alinham com os seus. Você pode salvar as alterações em uma pasta de trabalho personalizada e, em seguida, fixar os gráficos da pasta de trabalho a painéis do Azure.

Esse recurso não exige que você habilite nem configure nada. As informações do Cache do Azure para Redis são coletadas por padrão.

>[!NOTE]
>Não há nenhum encargo para acessar esse recurso. Você será cobrado somente pelos recursos essenciais do Azure Monitor que você configurar ou habilitar, conforme descrito na página [Detalhes de preços do Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).

## <a name="view-utilization-and-performance-metrics-for-azure-cache-for-redis"></a>Exibir métricas de desempenho e utilização para o Cache do Azure para Redis

Para exibir a utilização e o desempenho de suas contas de armazenamento em todas as suas assinaturas, execute as seguintes etapas:

1. Entre no [portal do Azure](https://portal.azure.com).

1. Pesquise **Monitor** e selecione **Monitor**.

    ![Caixa de pesquisa com a palavra "Monitor" e os resultados da pesquisa de serviços com a palavra "Monitor" e um símbolo de velocímetro](./media/cosmosdb-insights-overview/search-monitor.png)

1. Selecione **cache do Azure para Redis**. Se essa opção não estiver presente, selecione **Mais** > **Cache do Azure para Redis**.

### <a name="overview"></a>Visão geral

Em **Visão geral**, a tabela exibe métricas interativas do Cache do Azure para Redis. Você pode filtrar os resultados com base nas opções selecionadas nas seguintes listas suspensas:

- **Assinaturas**: somente assinaturas que têm um recurso do Cache do Azure para Redis são listadas.  

- **Cache do Azure para Redis**: você pode selecionar todos, um subconjunto ou um único cache do recurso do Cache do Azure para Redis.

- **Intervalo de Tempo**: por padrão, exibe as últimas 24 horas de informações com base nas seleções correspondentes.

Há um bloco de contador sob as listas suspensas. O bloco mostra o número total de recursos do Cache do Azure para Redis nas assinaturas selecionadas. Os códigos de cores condicionais ou mapas de calor para colunas da pasta de trabalho relatam métricas de transação. A cor mais profunda representa o valor mais alto. Cores mais claras representam valores mais baixos.

A seleção de uma seta de lista suspensa ao lado de um dos recursos do Cache do Azure para Redis revela uma análise das métricas de desempenho no nível de recurso individual.

![Captura da experiência de Visão geral](./media/redis-cache-insights-overview/overview.png)

Ao selecionar o nome do recurso do Cache do Azure para Redis realçado em azul, você vê a tabela **Visão geral** padrão para a conta associada. Ela mostra estas colunas:

- **Memória Usada**
- **Percentual de memória utilizada**
- **Carga do Servidor**
- **Linha do Tempo de Carga do Servidor**
- **CPU**
- **Clientes Conectados**
- **Perdas no Cache**
- **Erros (Máx.)**

### <a name="operations"></a>Operações

Quando você seleciona **Operações** na parte superior da página, a tabela **Operações** do modelo da pasta de trabalho é aberto. Ela mostra estas colunas:

- **Total de Operações**
- **Linha do Tempo de Total de Operações**
- **Operações por Segundo**
- **Gets**
- **Sets**

![Captura da experiência de operações](./media/redis-cache-insights-overview/operations.png)

### <a name="usage"></a>Uso

Quando você seleciona **Uso** na parte superior da página, a parte **Uso** do modelo da pasta de trabalho é aberta. Ela mostra estas colunas:

- **Cache Lido**
- **Linha do Tempo de Leitura do Cache**
- **Gravação no Cache**
- **Ocorrências no Cache**
- **Perdas no Cache**

![Captura de tela da experiência de uso](./media/redis-cache-insights-overview/usage.png)

### <a name="failures"></a>Falhas

Quando você seleciona **Falhas** na parte superior da página, a tabela **Falhas** do modelo da pasta de trabalho é aberta. Ela mostra estas colunas:

- **Total de Erros**
- **Failover/Erros**
- **UnresponsiveClient/Erros**
- **RDB/Erros**
- **AOF/Erros**
- **Exportação/Erros**
- **Perda de dados/Erros**
- **Importação/Erros**

![Captura de tela de falhas com uma divisão por tipo de solicitação HTTP](./media/redis-cache-insights-overview/failures.png)

### <a name="metric-definitions"></a>Definições de Métrica

Para obter uma lista completa das definições de métrica que compõem essas pastas de trabalho, confira o [artigo sobre métricas disponíveis e intervalos de relatório](../../azure-cache-for-redis/cache-how-to-monitor.md#available-metrics-and-reporting-intervals).

## <a name="view-from-an-azure-cache-for-redis-resource"></a>Exibição de um recurso do Cache do Azure para Redis

Para acessar o Azure Monitor para o Cache do Azure para Redis diretamente de um recurso individual:

1. Na portal do Azure, selecione Cache do Azure para Redis.

2. Na lista, escolha um recurso individual de Cache do Azure para Redis. Na seção monitoramento, escolha insights.

    ![Captura de tela das opções de menu com as palavras "insights" realçadas em uma caixa vermelha](./media/redis-cache-insights-overview/insights.png)

Essas exibições também podem ser acessadas selecionando o nome de um recurso do Cache do Azure para Redis na pasta de trabalho do nível do Azure Monitor.

### <a name="resource-level-overview"></a>Visão geral no nível do recurso

Na pasta de trabalho **Visão geral** para o Cache do Azure para Redis, são mostradas várias métricas de desempenho que lhe oferecem acesso a:

- Gráficos de desempenho interativos que mostram os detalhes mais fundamentais relacionados ao desempenho do Cache do Azure para Redis.

- Blocos de status e de métricas realçando o desempenho de fragmentos, o número total de clientes conectados e a latência geral.

![Captura de tela do painel de visão geral exibindo informações sobre desempenho da CPU, memória usada, clientes conectados, erros, chaves expiradas e chaves removidas](./media/redis-cache-insights-overview/resource-overview.png)

Selecionar qualquer uma das outras guias para **Desempenho** ou **Operações** abre as respectivas pastas de trabalho.

### <a name="resource-level-performance"></a>Desempenho no nível do recurso

![Captura de tela dos gráficos de desempenho do recurso](./media/redis-cache-insights-overview/resource-performance.png)

### <a name="resource-level-operations"></a>Operações no nível do recurso

![Captura de tela de gráficos de operações de recurso](./media/redis-cache-insights-overview/resource-operations.png)

## <a name="pin-export-and-expand"></a>Fixar, exportar e expandir

Para fixar qualquer seção de métrica em um [painel do Azure](../../azure-portal/azure-portal-dashboards.md), selecione o símbolo de alfinete na parte superior direita da seção.

![Uma seção de métrica com o símbolo de alfinete realçado](./media/cosmosdb-insights-overview/pin.png)

Para exportar os dados para um formato do Excel, selecione o símbolo de seta para baixo à esquerda do símbolo de alfinete.

![Um símbolo de exportação/pasta de trabalho realçado](./media/cosmosdb-insights-overview/export.png)

Para expandir ou recolher todas as exibições em uma pasta de trabalho, selecione o símbolo de expansão à esquerda do símbolo de exportação.

![Um símbolo de expansão/pasta de trabalho realçado](./media/cosmosdb-insights-overview/expand.png)

## <a name="customize-azure-monitor-for-azure-cache-for-redis"></a>Personalizar Azure Monitor para o cache do Azure para Redis

Já que essa experiência foi criada sobre os modelos de pasta de trabalho Azure Monitor, você tem a capacidade de **Personalizar** > **Editar** > **Salvar** uma cópia da sua versão modificada em uma pasta de trabalho personalizada.

![Uma barra de comandos com Personalizar realçado](./media/cosmosdb-insights-overview/customize.png)

As pastas de trabalho são salvas em um grupo de recursos na seção **Meus Relatórios** ou na seção **Relatórios Compartilhados**. **Meus Relatórios** está disponível apenas para você. **Relatórios Compartilhados** está disponível para todos com acesso ao grupo de recursos.

Depois de salvar uma pasta de trabalho personalizada, acesse a galeria de pastas de trabalho para abri-la.

![Uma barra de comandos com Galeria realçado](./media/cosmosdb-insights-overview/gallery.png)

## <a name="troubleshooting"></a>Solução de problemas

Para obter diretrizes de solução de problemas, consulte o artigo dedicado de solução de [problemas](troubleshoot-workbooks.md)com base na pasta de trabalho.

## <a name="next-steps"></a>Próximas etapas

* Configure [alertas de métrica](../alerts/alerts-metric.md) e [notificações de integridade do serviço](../../service-health/alerts-activity-log-service-notifications-portal.md) para configurar alertas automatizados que auxiliam na detecção de problemas.

* Conheça os cenários compatíveis com as pastas de trabalho, como criar ou personalizar relatórios e muito mais, examinando [Criar relatórios interativos com pastas de trabalho do Azure Monitor](../visualize/workbooks-overview.md).
