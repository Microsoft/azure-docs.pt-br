---
title: Monitorar visualmente o Azure Data Factory
description: Saiba como monitorar visualmente data factories do Azure
services: data-factory
documentationcenter: ''
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: f2bf1b1a16660b4e80bc0acc5c6e4201b452098f
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96497904"
---
# <a name="visually-monitor-azure-data-factory"></a>Monitorar visualmente o Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Depois de criar e publicar um pipeline no Azure Data Factory, você pode associá-lo a um gatilho ou iniciar manualmente uma execução ad hoc. Você pode monitorar todas as execuções de pipeline nativamente na experiência de usuário do Azure Data Factory. Para abrir a experiência de monitoramento, selecione o bloco **Monitorar e gerenciar** na folha de data factory do [portal do Azure](https://portal.azure.com/). Se você já estiver na UX do ADF, clique no ícone **Monitorar** na barra lateral esquerda.

Por padrão, todas as execuções de data factory são exibidas no fuso horário local do navegador. Se você alterar o fuso horário e todos os campos de data e hora serão encaixados no fuso horário selecionado.

## <a name="monitor-pipeline-runs"></a>Monitorar execuções de pipeline

O modo de exibição de monitoramento padrão é a lista de execuções de pipeline disparadas no período de tempo selecionado. Você pode alterar o intervalo de tempo e filtrar por status, nome do pipeline ou anotação. Passe o mouse sobre a execução do pipeline específico para obter ações específicas de execução, como executar novamente e o relatório de consumo.

![Visualização de lista para monitoramento de execuções de pipeline](media/monitor-visually/pipeline-runs.png)

A grade de execução de pipeline contém as seguintes colunas:

| **Nome da coluna** | **Descrição** |
| --- | --- |
| Nome do Pipeline | Nome do pipeline |
| Início da Execução | Data e hora de início da execução do pipeline (MM/DD/AAAA HH:MM:SS AM/PM) |
| Término da execução | Data e hora de término da execução do pipeline (MM/DD/AAAA, HH: MM: SS AM/PM) |
| Duration | Duração da execução (HH:MM:SS) |
| Disparado por | O nome do gatilho que iniciou o pipeline |
| Status | **Falha**, **Bem-sucedida**, **Em andamento**, **Cancelado** ou **Na fila** |
| Anotações | Marcas filtráveis associadas a um pipeline  |
| Parâmetros | Parâmetros de execução de pipeline (pares de nome/valor) |
| Erro | Se o pipeline falhou, o erro de execução |
| ID da execução | ID da execução de pipeline |

Você precisará selecionar manualmente o botão **Atualizar** para atualizar a lista de execuções do pipeline e atividades. No momento, não há suporte para atualização automática.

![Botão Atualizar](media/monitor-visually/refresh.png)

Para exibir os resultados de uma execução de depuração, selecione a guia **depurar** .

![Selecione o ícone Exibir execuções de depuração ativas](media/iterative-development-debugging/view-debug-runs.png)

## <a name="monitor-activity-runs"></a>Monitorar execuções de atividade

Para obter uma exibição detalhada das execuções de atividade individuais de uma execução de pipeline específica, clique no nome do pipeline.

![Exibir execuções de atividade](media/monitor-visually/view-activity-runs.png)

A exibição de lista mostra as execuções de atividade que correspondem a cada execução de pipeline. Focalize a execução da atividade específica para obter informações específicas da execução, como a entrada JSON, a saída JSON e experiências detalhadas de monitoramento específicas da atividade.

![Há informações sobre SalesAnalyticsMLPipeline, seguidas por uma lista de execuções de atividade.](media/monitor-visually/activity-runs.png)

| **Nome da coluna** | **Descrição** |
| --- | --- |
| Nome da atividade | Nome da atividade no pipeline |
| Tipo de atividade | Tipo de atividade, como **Copiar**, **ExecuteDataFlow** ou **AzureMLExecutePipeline** |
| Ações | Ícones que permitem que você veja informações de entrada JSON, informações de saída JSON ou experiências detalhadas de monitoramento específicas da atividade | 
| Início da Execução | Data e hora de início da execução da atividade (MM/DD/AAAA HH:MM:SS AM/PM) |
| Duration | Duração da execução (HH:MM:SS) |
| Status | **Falha**, **Sucesso**, **Em andamento** ou **Cancelado** |
| Integration Runtime | Em qual Integration Runtime a atividade foi executada |
| Propriedades do Usuário | Propriedades definidas pelo usuário da atividade |
| Erro | Se a atividade falhou, o erro de execução |
| ID da execução | ID da execução de atividade |

Se uma atividade falhar, você poderá ver a mensagem de erro detalhada clicando no ícone na coluna erro. 

![Uma notificação é exibida com detalhes do erro, incluindo código de erro, tipo de falha e detalhes do erro.](media/monitor-visually/activity-run-error.png)

### <a name="promote-user-properties-to-monitor"></a>Promover as propriedades de usuário para monitorá-las

Promove qualquer propriedade de atividade do pipeline como uma propriedade do usuário para que ela se torne uma entidade que você monitorar. Por exemplo, você pode promover as propriedades de **Fonte** e **Destino** da atividade de Cópia do pipeline como propriedades do usuário.

> [!NOTE]
> É possível promover no máximo cinco propriedades de atividade do pipeline como propriedades do usuário.

![Criar propriedades do usuário](media/monitor-visually/promote-user-properties.png)

Após criar as propriedades do usuário, você pode monitorá-las nas exibições de lista de monitoramento.

![Adicionar colunas de propriedades de usuário à lista de execuções de atividade](media/monitor-visually/choose-user-properties.png)

 Se a fonte da atividade de Cópia for um nome de tabela, você poderá monitorar o nome de tabela de origem como uma coluna na exibição de lista de execuções de atividade.

![Lista de execuções de atividade com colunas de propriedades de usuário](media/monitor-visually/view-user-properties.png)

## <a name="rerun-pipelines-and-activities"></a>Executar pipelines e atividades novamente

Para executar novamente um pipeline que foi executado anteriormente desde o início, passe o mouse sobre a execução do pipeline específico e selecione executar **novamente**. Se você selecionar vários pipelines, poderá usar o botão **executar novamente** para executar todos eles.

![Executar novamente um pipeline](media/monitor-visually/rerun-pipeline.png)

Se você quiser executar novamente a partir de um ponto específico, poderá fazer isso na exibição execuções de atividade. Selecione a atividade da qual você deseja iniciar e selecione **executar novamente da atividade**. 

![Executar novamente uma execução de atividade](media/monitor-visually/rerun-activity.png)

### <a name="rerun-from-failed-activity"></a>Executar novamente a partir da atividade com falha

Se uma atividade falhar, expirar ou for cancelada, você poderá executar novamente o pipeline da atividade com falha selecionando **Executar novamente a partir da atividade com falha**.

![Executar novamente a atividade com falha](media/monitor-visually/rerun-failed-activity.png)

### <a name="view-rerun-history"></a>Exibir o histórico de novas execuções

Exiba o histórico de novas execuções para todas as execuções de pipeline na exibição de lista.

![Exibir histórico](media/monitor-visually/rerun-history-1.png)

Exiba também o histórico de novas execuções para uma execução de pipeline específica.

![Exibir o histórico para uma execução de pipeline](media/monitor-visually/view-rerun-history.png)

## <a name="monitor-consumption"></a>Monitorar o consumo

Você pode ver os recursos consumidos por uma execução de pipeline clicando no ícone de consumo ao lado da execução. 

![Captura de tela que mostra onde você pode ver os recursos consumidos por um pipeline.](media/monitor-visually/monitor-consumption-1.png)

Clicar no ícone abre um relatório de consumo dos recursos usados por essa execução de pipeline. 

![Monitorar o consumo](media/monitor-visually/monitor-consumption-2.png)

Você pode conectar esses valores à [calculadora de preços do Azure](https://azure.microsoft.com/pricing/details/data-factory/) para estimar o custo da execução do pipeline. Para obter mais informações sobre preços de Azure Data Factory, consulte [Noções básicas sobre preços](pricing-concepts.md).

> [!NOTE]
> Esses valores retornados pela calculadora de preços são uma estimativa. Eles não refletem o valor exato que você será cobrado por Azure Data Factory 

## <a name="gantt-views"></a>Exibições de Gantt

Um gráfico de Gantt é uma exibição que permite que você veja o histórico de execução ao longo de um intervalo de tempo. Ao alternar para um modo de exibição de Gantt, você verá todas as execuções de pipeline agrupadas por nome exibidas como barras em relação ao tempo que a execução levou. Você também pode agrupar por anotações/marcas que você criou em seu pipeline. O modo de exibição Gantt também está disponível no nível de execução da atividade.

![Exemplo de um gráfico de Gantt](media/monitor-visually/select-gantt.png)

O comprimento da barra informa a duração do pipeline. Você também pode selecionar a barra para ver mais detalhes.

![Duração do gráfico de Gantt](media/monitor-visually/view-gantt-run.png)

## <a name="alerts"></a>Alertas

Você pode gerar alertas em métricas com suporte no Data Factory. Selecione o **Monitor** > **Alertas e métricas** na página do Monitor do Data Factory para começar a usar.

![Página Monitorar Data Factory](media/monitor-visually/start-page.png)

Para uma introdução de sete minutos e uma demonstração desse recurso, assista ao vídeo a seguir:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Monitor-your-Azure-Data-Factory-pipelines-proactively-with-alerts/player]

### <a name="create-alerts"></a>Criar alertas

1.  Selecione **Nova regra de alerta** para criar um novo alerta.

    ![Botão Nova regra de alerta](media/monitor-visually/new-alerts.png)

1.  Especifique o nome da regra e selecione a gravidade do alerta.

    ![Caixas para nome da regra e gravidade](media/monitor-visually/name-and-severity.png)

1.  Selecione os critérios de alerta.

    ![Caixa para os critérios de destino](media/monitor-visually/add-criteria-1.png)

    ![Captura de tela que mostra onde você seleciona uma métrica para configurar a condição de alerta.](media/monitor-visually/add-criteria-2.png)

    ![Lista de critérios](media/monitor-visually/add-criteria-3.png)

    Você pode criar alertas em várias métricas, incluindo aquelas para contagem/tamanho de entidade do ADF, execuções de atividade/pipeline/gatilho, execução de CPU de Integration Runtime (IR)/memória/contagem de nós/fila, bem como para execuções de pacote SSIS e operações de início/parada do SSIS.

1.  Configure a lógica de alerta. Crie um alerta para a métrica selecionada para todos os pipelines e atividades correspondentes. Também é possível selecionar um tipo de atividade em particular, o nome da atividade, o nome do pipeline ou um tipo de falha.

    ![Opções para configurar a lógica de alerta](media/monitor-visually/alert-logic.png)

1.  Configure as notificações de email, SMS, push e voz para o alerta. Crie um grupo de ações ou escolha um existente para as notificações de alerta.

    ![Opções para configurar notificações para alertas](media/monitor-visually/configure-notification-1.png)

    ![Opções para adicionar uma notificação](media/monitor-visually/configure-notification-2.png)

1.  Criar a regra de alerta.

    ![Opções para criar uma regra de alerta](media/monitor-visually/create-alert-rule.png)

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o monitoramento e o gerenciamento de pipelines, veja o artigo [Monitorar e gerenciar os pipelines programaticamente](./monitor-programmatically.md).