---
title: Tutorial – Criar um gráfico de métricas no Azure Monitor
description: Saiba como criar um gráfico de métricas com o Metrics Explorer do Azure.
author: bwren
ms.author: bwren
ms.topic: tutorial
ms.date: 03/09/2020
ms.openlocfilehash: 9923bfcbd439ef5e20e44e9c397199fe06f40b43
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102032901"
---
# <a name="tutorial-create-a-metrics-chart-in-azure-monitor"></a>Tutorial: Criar um gráfico de métricas no Azure Monitor
O Metrics Explorer é um recurso do Azure Monitor no portal do Azure que permite criar gráficos com base em valores de métrica, correlacionar tendências visualmente e investigar picos e vales nos valores das métricas. Use o Metrics Explorer para investigar a integridade e o uso dos seus recursos do Azure ou para plotar gráficos de métricas personalizadas. 

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Selecionar uma métrica segundo a qual deseja plotar um gráfico
> * Realizar diferentes agregações de valores de métricas
> * Modificar o intervalo de tempo e a granularidade do gráfico

Veja a seguir um vídeo que mostra um cenário mais extensivo do que o procedimento descrito neste artigo. Se você é novo nas métricas, sugerimos que você leia este artigo primeiro e, em seguida, veja o vídeo para obter mais especificações. 

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4qO59]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisa de um recurso do Azure para monitorar. Você poderá usar qualquer recurso da sua assinatura do Azure que seja compatível com métricas. Para determinar se um recurso é compatível com métricas, acesse seu menu no portal do Azure e verifique se há a opção **Métricas** na seção **Monitoramento** do menu.


## <a name="log-in-to-azure"></a>Fazer logon no Azure
Faça logon no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="open-metrics-explorer-and-select-a-scope"></a>Abrir o Metrics Explorer e selecionar um escopo
Você pode abrir o Metrics Explorer pelo menu do Azure Monitor ou pelo menu de um recurso no portal do Azure. As métricas de todos os recursos estarão disponíveis independentemente da opção que você usar. 

1. Selecione **Métricas** no menu do **Azure Monitor** ou na seção **Monitoramento** do menu de um recurso.

1. Selecione o **Escopo**, que é o recurso cujas métricas deseja visualizar. O escopo já estará preenchido se você tiver aberto o Metrics Explorer pelo menu de um recurso. Para saber mais sobre os vários recursos do seletor de escopo de recurso, visite [este artigo](../essentials/metrics-charts.md#resource-scope-picker).

    ![Selecionar um escopo](media/tutorial-metrics-explorer/scope-picker.png)

2. Selecione um **Namespace** se o escopo tiver mais de um. O namespace é apenas uma maneira de organizar as métricas para que você possa encontrá-las com facilidade. Por exemplo, as contas de armazenamento têm namespaces separados para armazenar métricas de arquivos, tabelas, blobs e filas. Muitos tipos de recursos têm apenas um namespace.

3. Selecione uma métrica em uma lista de métricas disponíveis para o escopo e o namespace selecionados.

    ![Selecione uma métrica](media/tutorial-metrics-explorer/metric-picker.png)

4. Opcionalmente, altere a **Agregação** da métrica. Isso define como os valores da métrica serão agregados ao longo da granularidade de tempo para o gráfico. Por exemplo, se a granularidade de tempo for definida como 15 minutos e a agregação for definida como soma, então cada ponto do gráfico representará a soma de todos os valores coletados em cada período de 15 minutos.

    ![A captura de tela mostra um gráfico intitulado Entrada – Soma para contosoretailweb.](media/tutorial-metrics-explorer/chart.png)

5. Use o botão **Adicionar métrica** e repita essas etapas se quiser ver várias métricas plotadas no mesmo gráfico. Para vários gráficos em uma única exibição, selecione o botão **Novo gráfico**.

## <a name="select-a-time-range-and-granularity"></a>Selecione um intervalo de tempo e uma granularidade

Por padrão, o gráfico mostra as últimas 24 horas de dados de métricas. Use o seletor de tempo para alterar o **intervalo de tempo** do gráfico ou a **granularidade de tempo** que define o intervalo de tempo de cada ponto de dados. O gráfico usa a agregação especificada para calcular todos os valores de amostra ao longo da granularidade de tempo especificada.

![Alterar o painel de intervalo de tempo](media/tutorial-metrics-explorer/time-picker.png)


Use o de **pincel de tempo** para investigar uma área de interesse do gráfico, como um pico ou um vale. Coloque o ponteiro do mouse no início da área, clique com o botão esquerdo do mouse e mantenha pressionado, arraste para o outro lado da área e solte o botão. O gráfico será ampliado naquele intervalo de tempo. 

![Pincel de tempo](media/tutorial-metrics-explorer/time-brush.png)

## <a name="apply-dimension-filters-and-splitting"></a>Aplicar filtros de dimensão e divisão
Consulte as referências a seguir para recursos avançados que permitem executar análises adicionais em suas métricas e identificar possíveis exceções em seus dados.

- A [filtragem](../essentials/metrics-charts.md#filters) permite que você escolha quais valores de dimensão serão incluídos no gráfico. Por exemplo, talvez você queira mostrar apenas solicitações bem-sucedidas ao representar graficamente a métrica *tempo de resposta do servidor*. 

- A [divisão](../essentials/metrics-charts.md#apply-splitting) controla se o gráfico exibe linhas separadas para cada valor de uma dimensão ou se agrega os valores em uma única linha. Por exemplo, talvez você queira ver uma única linha para um tempo médio de resposta em todas as instâncias de servidores ou pode querer linhas separadas para cada servidor. 

Confira [exemplos de gráficos](../essentials/metric-chart-samples.md) que têm a filtragem e a divisão aplicadas.

## <a name="advanced-chart-settings"></a>Configurações de gráfico avançadas

Você pode personalizar o estilo do gráfico, o título e modificar as configurações avançadas do gráfico. Quando terminar a personalização, fixe-a em um painel para salvar seu trabalho. Você também pode configurar alertas de métricas. Confira [Recursos avançados do Azure Metrics Explorer](../essentials/metrics-charts.md#locking the-range-of-the-y-axis) para saber mais sobre esses e outros recursos avançados do Azure Monitor Metrics Explorer.


## <a name="next-steps"></a>Próximas etapas
Agora que você aprendeu como trabalhar com métricas no Azure Monitor, saiba como usar as métricas para enviar alertas proativos.

> [!div class="nextstepaction"]
> [Criar, exibir e gerenciar alertas de métrica usando o Azure Monitor](../essentials/metrics-charts.md#alert-rules)

