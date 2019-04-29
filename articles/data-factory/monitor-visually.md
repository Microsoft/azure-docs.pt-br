---
title: Monitorar visualmente as data factories do Azure | Microsoft Docs
description: Saiba como monitorar visualmente data factories do Azure
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: shlo
ms.openlocfilehash: df684860cd3d1b6a002a300682ca4c6398461ba6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60716674"
---
# <a name="visually-monitor-azure-data-factories"></a>Monitorar visualmente data factories do Azure
O Azure Data Factory é um serviço de integração de dados baseado em nuvem que permite que você crie fluxos de trabalho controlados por dados na nuvem para orquestrar e automatizar a movimentação e a transformação de dados. Usando o Azure Data Factory, você pode criar e agendar fluxos de trabalho orientados a dados (chamados de pipelines) que podem ingerir dados de repositórios de dados diferentes, processar/transformr os dados usando serviços de computação como o Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics e Azure Machine Learning e publicar os dados de saída em repositórios de dados como o SQL Data Warehouse do Azure para consumo pelos aplicativos de business intelligence (BI).

Neste início rápido, você aprenderá a monitorar visualmente os pipelines do Data Factory sem escrever uma única linha de código.

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="monitor-data-factory-pipelines"></a>Monitorar pipelines de Data Factory

Monitore execuções de atividade e pipeline com uma simples interface de exibição de lista. Todas as execuções são exibidas no fuso horário local do navegador. Você pode alterar o fuso horário e todos os campos de data e hora serão encaixados no fuso horário selecionado.  

1. Iniciar o navegador da Web **Microsoft Edge** ou **Google Chrome**. Atualmente, a interface do usuário do Data Factory tem suporte apenas nos navegadores da Web Microsoft Edge e Google Chrome.
2. Faça logon no [Portal do Azure](https://portal.azure.com/).
3. Navegue até a folha do data factory criada no portal do Azure e clique na peça 'Monitorar e Gerenciar' para iniciar a experiência de monitaramento visual do Data Factory.

## <a name="monitor-pipeline-runs"></a>Monitorar execuções de pipeline
Exibição de lista mostrando cada execução de pipeline para seus pipelines do data factory v2. Colunas incluídas:

| **Nome da Coluna** | **Descrição** |
| --- | --- |
| Nome do Pipeline | Nome do pipeline. |
| Ações | Ação única disponível para exibir execuções de atividade. |
| Início da Execução | Data e hora de início da execução do pipeline (MM/DD/AAAA HH:MM:SS AM/PM) |
| Duration | Duração da execução (HH:MM:SS) |
| Disparado por | Gatilho manual, Gatilho de agenda |
| Status | Falha, Sucesso, Em Andamento |
| parâmetros | Parâmetros de execução de pipeline (pares de nome, valor) |
| Erro | Erro de execução de pipeline (if/any) |
| ID da execução | ID da execução de pipeline |

![Monitorar execuções de pipeline](media/monitor-visually/pipeline-runs.png)

## <a name="monitor-activity-runs"></a>Monitorar execuções de atividade
Exibição de lista mostrando execuções de atividade correspondentes a cada execução de pipeline. Clique no ícone **'Execuções de Atividade'** na coluna **'Ações'** para exibir execuções de atividade para cada execução de pipeline. Colunas incluídas:

| **Nome da Coluna** | **Descrição** |
| --- | --- |
| Nome da atividade | Nome da atividade no pipeline. |
| Tipo de atividade | Tipo de atividade como o Copy, HDInsightSpark, HDInsightHive etc. |
| Início da Execução | Data hora de início de execução da atividade (MM/DD/AAAA HH: MM: SS AM/PM) |
| Duration | Duração da execução (HH:MM:SS) |
| Status | Falha, Sucesso, Em Andamento |
| Entrada | Matriz JSON que descreve as entradas de atividade |
| Saída | Matriz JSON que descreve as saídas de atividade |
| Erro | Erro de execução da atividade (if/any) |

![Monitorar execuções de atividade](media/monitor-visually/activity-runs.png)

> [!IMPORTANT]
> Você precisará clicar no ícone **'Atualizar'** na parte superior para atualizar a lista de execuções do pipeline e atividades. No momento, não há suporte para atualização automática.

![Atualizar](media/monitor-visually/refresh.png)

## <a name="select-a-data-factory-to-monitor"></a>Selecione um data factory a ser monitorado
Passe o cursor do mouse sobre o ícone **Data Factory** no canto superior esquerdo. Clique no ícone de 'Seta' para ver uma lista de assinaturas e data factories do Azure que você pode monitorar.

![Selecionar o data factory](media/monitor-visually/select-datafactory.png)

## <a name="configure-the-list-view"></a>Configurar a exibição de lista

### <a name="apply-rich-ordering-and-filtering"></a>Aplicar ordenação e filtragem avançadas

Ordene as execuções do pipeline em ordem decrescente/crescente com Início da Execução e filtre as execuções de pipeline pelas seguintes colunas:

| **Nome da Coluna** | **Descrição** |
| --- | --- |
| Nome do Pipeline | Nome do pipeline. As opções incluem filtros rápidos para 'últimas 24 horas', 'Última semana', 'Últimos 30 dias' ou selecione uma data e hora personalizadas. |
| Início da Execução | Data hora de início da execução do pipeline |
| Status da execução | O filtro é executado por status - Sucesso, Falha, Em andamento |

![Filter](media/monitor-visually/filter.png)

### <a name="add-or-remove-columns"></a>Adicionar ou remover colunas
Clique com o botão direito do mouse no cabeçalho da exibição de lista e escolha as colunas que você deseja que apareçam na exibição de lista

![Colunas](media/monitor-visually/columns.png)

### <a name="adjust-column-widths"></a>Ajustar as larguras de colunas
Aumente e diminua as larguras das colunas na exibição de lista simplesmente passando o cursor do mouse sobre o cabeçalho da coluna

## <a name="promote-user-properties-to-monitor"></a>Promover as propriedades de usuário para monitorá-las

É possível promover qualquer propriedade de atividade do pipeline como uma propriedade do usuário para que ela se torne uma entidade que você pode monitorar. Por exemplo, você pode promover as propriedades de **Fonte** e **Destino** da atividade de Cópia do pipeline como propriedades do usuário. Também é possível selecionar **Gerar Automaticamente** para gerar as propriedades de **Fonte** e **Destino** para uma atividade de Cópia.

![Criar propriedades do usuário](media/monitor-visually/monitor-user-properties-image1.png)

> [!NOTE]
> É possível promover no máximo cinco propriedades de atividade do pipeline como propriedades do usuário.

Após criar as propriedades do usuário, você pode monitorá-los nas exibições de lista de monitoramento. Se a fonte da atividade de Cópia for um nome de tabela, você poderá monitorar o nome de tabela de origem como uma coluna na exibição de lista de execuções de atividade.

![Lista de execuções de atividade sem propriedades de usuário](media/monitor-visually/monitor-user-properties-image2.png)

![Adicionar colunas de propriedades de usuário à lista de execuções de atividade](media/monitor-visually/monitor-user-properties-image3.png)

![Lista de execuções de atividade com colunas de propriedades de usuário](media/monitor-visually/monitor-user-properties-image4.png)

## <a name="rerun-activities-inside-a-pipeline"></a>Executar atividades novamente em um pipeline

Agora você pode executar atividades novamente em um pipeline. Clique em **Exibir execuções de atividade** e selecione a atividade no pipeline de cujo ponto você deseja executar o pipeline novamente.

![Exibir execuções de atividade](media/monitor-visually/rerun-activities-image1.png)

![Selecionar uma execução de atividade](media/monitor-visually/rerun-activities-image2.png)

### <a name="view-rerun-history"></a>Exibir o histórico de novas execuções

Exiba o histórico de novas execuções para todas as execuções de pipeline na exibição de lista.

![Exibir histórico](media/monitor-visually/rerun-history-image1.png)

Exiba também o histórico de novas execuções para uma execução de pipeline específica.

![Exibir o histórico para uma execução de pipeline](media/monitor-visually/rerun-history-image2.png)

## <a name="guided-tours"></a>Passeios Guiados
Clique em 'Ícone Informações' no canto inferior esquerdo e clique em 'Passeios Guiados' para obter instruções passo a passo sobre como monitorar as execuções de atividade e o pipeline.

![Passeios guiados](media/monitor-visually/guided-tours.png)

## <a name="feedback"></a>Comentários
Clique no ícone 'Comentários' para fazer comentários em vários recursos ou problemas que você possa estar enfrentando.

![Comentários](media/monitor-visually/feedback.png)

## <a name="alerts"></a>Alertas

Você pode gerar alertas em métricas com suporte no Data Factory. Selecione **Monitor -> Alertas e Métricas** na página do Monitor do Data Factory para começar.

![](media/monitor-visually/alerts01.png)

Para uma introdução de sete minutos e uma demonstração desse recurso, assista ao vídeo a seguir:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Monitor-your-Azure-Data-Factory-pipelines-proactively-with-alerts/player]

### <a name="create-alerts"></a>Criar alertas

1.  Clique em **Nova regra de alerta**  para criar um novo alerta.

    ![](media/monitor-visually/alerts02.png)

1.  Especifique o nome da regra e selecione o alerta **Gravidade**.

    ![](media/monitor-visually/alerts03.png)

1.  Selecione os critérios de alerta.

    ![](media/monitor-visually/alerts04.png)

    ![](media/monitor-visually/alerts05.png)

1.  Configure o alerta de log. Crie um alerta para a métrica selecionada para todos os pipelines e atividades correspondentes. Também é possível selecionar um tipo de atividade em particular, o nome da atividade, o nome do pipeline ou um tipo de falha.

    ![](media/monitor-visually/alerts06.png)

1.  Configure as notificações de alerta de **Email/SMS/Push/voz**. Crie ou escolha um existente **grupo de ação** para as notificações de alerta.

    ![](media/monitor-visually/alerts07.png)

    ![](media/monitor-visually/alerts08.png)

1.  Criar a regra de alerta.

    ![](media/monitor-visually/alerts09.png)

## <a name="next-steps"></a>Próximas etapas

Consulte o artigo [Monitorar e gerenciar os pipelines programaticamente](https://docs.microsoft.com/azure/data-factory/monitor-programmatically) para saber mais sobre o monitoramento e o gerenciamento de pipelines.
