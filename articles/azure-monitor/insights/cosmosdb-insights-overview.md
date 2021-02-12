---
title: Monitorar o Azure Cosmos DB com o Azure Monitor para Cosmos DB | Microsoft Docs
description: Este artigo descreve o recurso Azure Monitor para Cosmos DB, que fornece aos proprietários do Cosmos DB uma rápida compreensão dos problemas de desempenho e utilização com as contas do CosmosDB deles.
author: lgayhardt
ms.author: lagayhar
ms.topic: conceptual
ms.date: 05/11/2020
ms.openlocfilehash: 878ae21cfbe55565c4a20064d937d1447f96ce7a
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98209892"
---
# <a name="explore-azure-monitor-for-azure-cosmos-db"></a>Explorar o Azure Monitor para o Azure Cosmos DB

O Azure Monitor para Azure Cosmos DB fornece uma exibição do desempenho geral, das falhas, da capacidade e da integridade operacional de todos os seus recursos de Azure Cosmos DB em uma experiência interativa unificada. Este artigo o ajudará a entender os benefícios dessa nova experiência de monitoramento e como você pode modificar e adaptar a experiência para atender às necessidades exclusivas de sua organização.   

## <a name="introduction"></a>Introdução

Antes de mergulhar na experiência, você deve entender como ela apresenta e visualiza informações. 

Ela oferece:

* **Perspectiva em escala** de seus recursos do Azure Cosmos DB em todas as suas assinaturas em um único local, com a capacidade de definir o escopo seletivamente apenas com as assinaturas e os recursos que você está interessado em avaliar.

* **Análise de busca detalhada** de um recurso específico do Azure CosmosDB para ajudar a diagnosticar problemas ou executar uma análise detalhada por categoria – utilização, falhas, capacidade e operações. A seleção de qualquer uma dessas opções fornece uma visão detalhada das métricas do Azure Cosmos DB relevantes.  

* **Personalizável** – essa experiência é criada sobre os modelos de pasta de trabalho do Azure Monitor, permitindo que você altere quais métricas são exibidas, modifique ou defina os limites que se alinham com seus limites e, em seguida, salve em uma pasta de trabalho personalizada. Os gráficos nas pastas de trabalho podem ser fixados a painéis do Azure.  

Esse recurso não exige que você habilite nem configure nada, essas métricas do Azure Cosmos DB são coletadas por padrão.

>[!NOTE]
>Não há nenhum encargo para acessar esse recurso e você será cobrado somente pelos recursos essenciais Azure Monitor que você configurar ou habilitar, conforme descrito na página [Detalhes de preços de Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).

## <a name="view-utilization-and-performance-metrics-for-azure-cosmos-db"></a>Exibir métricas de desempenho e utilização para o Azure Cosmos DB

Para exibir a utilização e o desempenho de suas contas de armazenamento em todas as suas assinaturas, execute as etapas a seguir.

1. Entre no [portal do Azure](https://portal.azure.com).

2. Pesquise **Monitor** e selecione **Monitor**.

    ![Caixa de pesquisa com a palavra "Monitor" e uma lista suspensa com os dizeres "Monitor" de Serviços com uma imagem semelhante a um velocímetro](./media/cosmosdb-insights-overview/search-monitor.png)

3. Selecione **Cosmos DB**.

    ![Captura de tela da pasta de trabalho de visão geral do Cosmos DB](./media/cosmosdb-insights-overview/cosmos-db.png)

### <a name="overview"></a>Visão geral

Em **Visão geral**, a tabela exibe métricas interativas do Azure Cosmos DB. Você pode filtrar os resultados com base nas opções selecionadas nas seguintes listas suspensas:

* **Assinaturas** – somente assinaturas que têm um recurso do Azure Cosmos DB são listadas.  

* **Cosmos DB** – você pode selecionar apenas um recurso do Azure Cosmos DB, um subconjunto deles ou todos.

* **Intervalo de tempo** – por padrão, exibe as últimas quatro horas de informações com base nas seleções correspondentes feitas.

O bloco contador sob as listas suspensas acumula o número total de recursos do Azure Cosmos DB que estão nas assinaturas selecionadas. Há codificação de cor condicional ou mapas de calor para colunas na pasta de trabalho que relatam métricas de transação. A cor mais intensa tem o valor mais alto e uma cor mais clara é baseada nos valores mais baixos. 

A seleção de uma seta suspensa ao lado de um dos recursos do Azure Cosmos DB revelará uma análise das métricas de desempenho no nível de contêiner de banco de dados individual:

![Lista suspensa expandida revelando contêineres de banco de dados individuais e o detalhamento de desempenho associado](./media/cosmosdb-insights-overview/container-view.png)

Selecionar o nome do recurso de Azure Cosmos DB realçado em azul levará você para a **Visão geral** padrão para a conta de Azure Cosmos DB associada. 

### <a name="failures"></a>Falhas

Selecione **Falhas** na parte superior da página e a parte **Falhas** do modelo da pasta de trabalho é aberta. Ele mostra o total de solicitações com a distribuição de respostas que compõem essas solicitações:

![Captura de tela de falhas com divisão por tipo de solicitação HTTP](./media/cosmosdb-insights-overview/failures.png)

| Código |  Descrição       | 
|-----------|:--------------------|
| `200 OK`  | Uma das seguintes operações REST foi bem-sucedida: </br>– GET em um recurso. </br> – PUT em um recurso. </br> – POST em um recurso. </br> – POST em um recurso de procedimento armazenado para executar o procedimento armazenado.|
| `201 Created` | Uma operação POST para criar um recurso foi bem-sucedida. |
| `404 Not Found` | A operação está tentando atuar em um recurso que não existe mais. Por exemplo, o recurso pode já ter sido excluído. |

Para obter uma lista completa de códigos de status, consulte o [artigo de código de status HTTP do Azure Cosmos DB](/rest/api/cosmos-db/http-status-codes-for-cosmosdb).

### <a name="capacity"></a>Capacity

Selecione **Capacidade** na parte superior da página e a parte **Capacidade** do modelo da pasta de trabalho é aberta. Ele mostra quantos documentos você tem, o crescimento do documento ao longo do tempo, o uso de dados e a quantidade total de armazenamento disponível que você deixou.  Isso pode ser usado para ajudar a identificar possíveis problemas de armazenamento e de utilização de dados.

![Pasta de trabalho de capacidade](./media/cosmosdb-insights-overview/capacity.png) 

Assim como na pasta de trabalho de visão geral, selecionar a lista suspensa ao lado de um recurso de Azure Cosmos DB na coluna **Assinatura** revelará um detalhamento dos contêineres individuais que compõem o banco de dados.

### <a name="operations"></a>Operações 

Selecione **Operações** na parte superior da página e a parte **Operações** do modelo da pasta de trabalho é aberta. Ele oferece a capacidade de ver suas solicitações divididas pelo tipo de solicitações feitas. 

Portanto, no exemplo abaixo, você verá que `eastus-billingint` está predominantemente recebendo solicitações de leitura, mas com um pequeno número de solicitações upsert e create. Já `westeurope-billingint` é somente leitura de uma perspectiva de solicitação, pelo menos nas últimas quatro horas definidas atualmente como escopo da pasta de trabalho por meio do parâmetro de intervalo de tempo dela.

![Pasta de trabalho de operações](./media/cosmosdb-insights-overview/operation.png) 

## <a name="pin-export-and-expand"></a>Fixar, exportar e expandir

Você pode fixar qualquer uma das seções de métrica em um [Painel do Azure](../../azure-portal/azure-portal-dashboards.md) selecionando o ícone de alfinete na parte superior direita da seção.

![Exemplo de fixação no painel na seção de métrica](./media/cosmosdb-insights-overview/pin.png)

Para exportar os dados para o formato do Excel, selecione o ícone de seta para baixo à esquerda do ícone de alfinete.

![Exportar ícone de pasta de trabalho](./media/cosmosdb-insights-overview/export.png)

Para expandir ou recolher todas as exibições suspensas na pasta de trabalho, selecione o ícone de expansão à esquerda do ícone de exportação:

![Ícone expandir pasta de trabalho](./media/cosmosdb-insights-overview/expand.png)

## <a name="customize-azure-monitor-for-azure-cosmos-db"></a>Personalizar o Azure Monitor para o Azure Cosmos DB

Já que essa experiência foi criada sobre os modelos de pasta de trabalho Azure Monitor, você tem a capacidade de **Personalizar** > **Editar** e **Salvar** uma cópia da sua versão modificada em uma pasta de trabalho personalizada. 

![Personalizar barra](./media/cosmosdb-insights-overview/customize.png)

As pastas de trabalho são salvas em um grupo de recursos, seja na seção **Meus Relatórios**, que é particular a você, ou na seção **Relatórios Compartilhados**, que é acessível a todos com acesso ao grupo de recursos. Depois de salvar a pasta de trabalho personalizada, você precisa ir para a galeria de pastas de trabalho para iniciá-la.

![Iniciar galeria de pastas de trabalho na barra de comandos](./media/cosmosdb-insights-overview/gallery.png)

## <a name="troubleshooting"></a>Solução de problemas

Para obter diretrizes de solução de problemas, consulte o artigo dedicado de solução de [problemas](troubleshoot-workbooks.md)com base na pasta de trabalho.

## <a name="next-steps"></a>Próximas etapas

* Configure [alertas de métrica](../platform/alerts-metric.md) e [notificações de integridade do serviço](../../service-health/alerts-activity-log-service-notifications-portal.md) para configurar alertas automatizados a fim de auxiliar na detecção de problemas.

* Conheça os cenários aos quais as pastas de trabalho foram projetadas para dar suporte, como criar relatórios, personalizar relatórios existentes e muito mais, examinando [Criar relatórios interativos com pastas de trabalho do Azure Monitor](../platform/workbooks-overview.md).
