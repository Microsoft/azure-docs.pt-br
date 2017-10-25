---
title: Como monitorar uma conta de Armazenamento do Azure | Microsoft Docs
description: Saiba como monitorar uma conta de armazenamento no Azure usando o portal do Azure.
services: storage
documentationcenter: 
author: tamram
manager: timlt
editor: tysonn
ms.assetid: b83cba7b-4627-4ba7-b5d0-f1039fe30e78
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: tamram
ms.openlocfilehash: 8c566978531ceb9db9aa7c9e2ba3ab83b2dc67cb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-a-storage-account-in-the-azure-portal"></a>Monitorar uma conta de armazenamento no portal do Azure

[Análise de Armazenamento do Azure](../storage-analytics.md) fornece métricas para todos os serviços de armazenamento e logs para blobs, filas e tabelas. Você pode usar o [portal do Azure](https://portal.azure.com) de configurar quais métricas e logs são registrados para sua conta e configurar gráficos que fornecem representações visuais dos dados de métricas.

> [!NOTE]
> Há custos associados ao exame de dados de monitoramento no portal do Azure. Para obter mais informações, consulte [Análise de armazenamento e cobrança](/rest/api/storageservices/Storage-Analytics-and-Billing).
>
> O Arquivos do Azure atualmente dá suporte às métricas de Análise de Armazenamento, mas ainda não dá suporte ao registro em log.
>
> As contas de armazenamento com um tipo de replicação de armazenamento com redundância de zona (ZRS) não têm métricas ou funcionalidade de log habilitadas no momento.
> 
> Para um guia aprofundado sobre como usar a Análise de Armazenamento e outras ferramentas para identificar, diagnosticar e solucionar problemas relacionados ao Armazenamento do Azure, consulte [Monitorar, diagnosticar e solucionar problemas do Armazenamento do Microsoft Azure](../storage-monitoring-diagnosing-troubleshooting.md).
>

## <a name="configure-monitoring-for-a-storage-account"></a>Configurar o monitoramento para uma conta de armazenamento

1. No [portal do Azure](https://portal.azure.com), selecione **Contas de armazenamento** e o nome de conta de armazenamento para abrir o painel de conta.
1. Selecione **Diagnóstico** na seção **MONITORAMENTO** da folha de menu.

    ![Opções de monitoramento](./media/storage-monitor-storage-account/stg-enable-metrics-00.png)

1. Selecione o **tipo** de dados de métrica para cada **serviço** que você deseja monitorar e a **política de retenção** para os dados. Você também pode desabilitar o monitoramento definindo **Status** como **Desativado**.

    ![Opções de monitoramento](./media/storage-monitor-storage-account/stg-enable-metrics-01.png)

   Há dois tipos de métricas que você pode habilitar para cada serviço. Ambas são habilitadas por padrão para novas contas de armazenamento:

   * **Agregação**: coleta métricas como percentuais de entrada/saída, disponibilidade, latência e sucesso. Essas métricas são agregadas para os serviços de blob, fila, tabela e arquivo.
   * **Por API**: além das métricas agregadas, coleta o mesmo conjunto de métricas para cada operação de armazenamento na API de serviço de Armazenamento do Azure.

   Para definir a política de retenção de dados, mova o controle deslizante **Retenção (dias)** ou insira o número de dias de dados devem ser retidos, de 1 a 365. O padrão para novas contas de armazenamento é de sete dias. Se não desejar definir uma política de retenção, digite zero. Se não houver nenhuma política de retenção, cabe a você excluir os dados de monitoramento.

   > [!WARNING]
   > Você é cobrado quando exclui manualmente dados de métricas. Dados de análise obsoletos (dados anteriores à política de retenção) são excluídos pelo sistema sem custo. É recomendável configurar uma política de retenção com base no tempo pelo qual você deseja manter os dados de análise de armazenamento para sua conta. Confira [Quais são os encargos quando você habilita as métricas de armazenamento?](../common/storage-enable-and-view-metrics.md#what-charges-do-you-incur-when-you-enable-storage-metrics) para obter mais informações.
   >

1. Ao concluir a configuração de monitoramento, selecione **Salvar**.

Um conjunto de métricas padrão é exibido em gráficos na folha da conta de armazenamento, bem como nas folhas de serviço individuais (blob, fila, tabela e arquivo). Depois que você habilita métricas para um serviço, pode levar até uma hora para que os dados apareçam nos gráficos. Você pode selecionar **Editar** em qualquer gráfico de métricas para [configurar quais métricas](#how-to-customize-metrics-charts) são exibidas no gráfico.

Você pode desabilitar a coleta de métricas e o registro em log definindo **Status** como **Desativado**.

> [!NOTE]
> O Armazenamento do Azure usa o [armazenamento de tabelas](../common/storage-introduction.md#table-storage) para armazenar as métricas para sua conta de armazenamento e armazena as métricas em tabelas em sua conta. Para obter mais informações, confira: [Como as métricas são armazenadas](../common/storage-analytics.md#how-metrics-are-stored).
>

## <a name="customize-metrics-charts"></a>Personalizar gráficos de métricas

Use o procedimento a seguir para escolher quais métricas de armazenamento devem ser exibidas em um gráfico de métricas. 

1. Comece exibindo um gráfico de métricas de armazenamento no portal do Azure. Você pode encontrar gráficos na **folha da conta de armazenamento** e na folha **Métricas** de um serviço individual (blob, fila, tabela, arquivo).

   Neste exemplo, trabalhamos com o gráfico a seguir, que aparece na **folha de conta de armazenamento**:

   ![Seleção de gráfico no portal do Azure](./media/storage-monitor-storage-account/stg-customize-chart-00.png)

1. Em seguida, clique em qualquer lugar no gráfico para abrir a folha **Métrica**. Selecione **Editar gráfico** para abrir a folha **Editar Gráfico**.

   ![Editar botão de gráfico na folha de gráfico](./media/storage-monitor-storage-account/stg-customize-chart-01.png)

1. Na folha **Editar Gráfico**, selecione o **Intervalo de Tempo** das métricas para exibir no gráfico e o **service** (blob, fila, tabela, arquivo) cujas métricas você deseja exibir. Aqui, selecionamos a exibição das métricas da semana passada para o serviço de blob:

   ![Intervalo de tempo e seleção de serviço na folha Editar Gráfico](./media/storage-monitor-storage-account/stg-customize-chart-02.png)

1. Selecione as **métricas** individuais que deseja exibir no gráfico e clique em **OK**. Por exemplo, aqui, optamos por exibir as métricas *ContainerCount* e *ObjectCount*:

   ![Seleção de métrica individual na folha Editar Gráfico](./media/storage-monitor-storage-account/stg-customize-chart-03.png)

As configurações de gráfico não afetam a coleta, a agregação ou o armazenamento de dados de monitoramento na conta de armazenamento, somente a exibição de dados de métricas.

### <a name="metrics-availability-in-charts"></a>Disponibilidade de métricas em gráficos

A lista de métricas disponíveis é alterada com base em qual serviço você escolheu na lista suspensa e qual tipo de unidade do gráfico você está editando. Por exemplo, você só poderá selecionar as métricas de percentual como *PercentNetworkError* e *PercentThrottlingError* se estiver editando um gráfico que exiba as unidades em percentual:

![Gráfico de percentual de erro de solicitação no portal do Azure](./media/storage-monitor-storage-account/stg-customize-chart-04.png)

### <a name="metrics-resolution"></a>Resolução de métricas

As métricas que você selecionou em Diagnósticos determinam a resolução das métricas que estão disponíveis para sua conta:

* O monitoramento **Agregado** fornece métricas como percentuais de entrada/saída, disponibilidade, latência e sucesso. Essas métricas são agregadas dos serviços de blob, tabela, arquivo e fila.
* **Por API** fornece resolução mais detalhada, com métricas disponíveis para operações de armazenamento individuais, além de agregações de nível de serviço.

## <a name="configure-metrics-alerts"></a>Configurar alertas de métricas

Você pode criar alertas para notificá-lo quando os limites forem atingidos para métricas de recursos de armazenamento.

1. Para abrir a **folha de Regras de alerta**, role para baixo até a seção **MONITORAMENTO** da **folha de Menu** e selecione **Regras de alerta**.
1. Selecione **Adicionar alerta** para abrir a folha **Adicionar uma regra de alerta**
1. Selecione um **Recurso** (blob, arquivo, fila, tabela) na lista suspensa e digite um **Nome** e **Descrição** para a nova regra de alerta.
1. Selecione a **Métrica** para a qual você deseja adicionar um alerta, uma **Condição** de alerta e um **Limite**. O tipo de unidade de limite é alterado dependendo da métrica que você escolheu. Por exemplo, "count" é o tipo de unidade para *ContainerCount*, enquanto a unidade para a métrica *PercentNetworkError* é um percentual.
1. Selecione o **Período**. As métricas que atingiram ou excederam o limite dentro do período disparam um alerta.
1. (Opcional) Configurar notificações de **Email** e **Webhook**. Para obter mais informações sobre webhooks, confira [Configurar um webhook em um alerta de métrica do Azure](../../monitoring-and-diagnostics/insights-webhooks-alerts.md). Se você não configurar notificações por email ou webhook, serão exibidos alertas somente no portal do Azure.

![Folha 'Adicionar uma regra de alerta' no portal do Azure](./media/storage-monitor-storage-account/stg-alert-rules-01.png)

## <a name="add-metrics-charts-to-the-portal-dashboard"></a>Adicionar gráficos de métricas para o painel do portal

Você pode adicionar gráficos de métricas do Armazenamento do Azure para qualquer uma de suas contas de armazenamento no painel do portal.

1. Clique para selecionar **Editar painel** ao exibir o painel no [portal do Azure](https://portal.azure.com).
1. Na **Galeria de Blocos**, selecione **Localizar blocos por** > **Tipo**.
1. Selecione **Tipo** > **Contas de armazenamento**.
1. Em **Recursos**, selecione a conta de armazenamento cujas métricas deseja adicionar ao painel.
1. Selecione **Categorias** > **Monitoramento**.
1. Arraste e solte o bloco do gráfico no painel para a métrica que você deseja exibir. Repita para todas as métricas que deseja exibir no painel. Na imagem a seguir, o gráfico "Blobs - total de solicitações" está realçado como um exemplo, mas todos os gráficos estão disponíveis para posicionamento em seu painel.

   ![Galeria de blocos no portal do Azure](./media/storage-monitor-storage-account/stg-customize-dashboard-01.png)
1. Selecione **Personalização concluída** na parte superior do painel quando terminar a adição de gráficos.

Depois de adicionar gráficos ao painel, você pode personalizá-los conforme descrito em [Personalizar gráficos de métricas](#how-to-customize-metrics-charts).

## <a name="configure-logging"></a>Configurar o registro em log

Você pode instruir o Armazenamento do Azure a salvar logs de diagnóstico para ler, gravar e excluir solicitações para os serviços de fila, blob e tabela. A política de retenção de dados que você define também se aplica a esses logs.

> [!NOTE]
> O Arquivos do Azure atualmente dá suporte às métricas de Análise de Armazenamento, mas ainda não dá suporte ao registro em log.
>

1. No [portal do Azure](https://portal.azure.com), selecione **Contas de armazenamento** e o nome da conta de armazenamento para abrir a folha de conta de armazenamento.
1. Selecione **Diagnóstico** na seção **MONITORAMENTO** da folha de menu.

    ![Item do menu de diagnóstico em MONITORAMENTO no portal do Azure.](./media/storage-monitor-storage-account/stg-enable-metrics-00.png)
    
1. Verifique se **Status** está definido como **Ativado**e selecione os **Serviços** para os quais deseja habilitar os logs.

    ![Configure os logs no portal do Azure.](./media/storage-monitor-storage-account/stg-enable-logging-01.png)
1. Clique em **Salvar**.

Os logs de diagnóstico são salvos em um contêiner de blob denominado $logs em sua conta de armazenamento. Você pode exibir os dados de log usando um gerenciador de armazenamento como o [Gerenciador de Armazenamento da Microsoft](http://storageexplorer.com) ou de forma programática, usando a biblioteca de cliente de armazenamento ou o PowerShell.

Para obter informações sobre como acessar o contêiner $logs, confira [Habilitando o log de armazenamento e acessando dados de log](/rest/api/storageservices/enabling-storage-logging-and-accessing-log-data).

## <a name="next-steps"></a>Próximas etapas

* Encontre mais detalhes sobre [métricas, logs e cobrança](../storage-analytics.md) para Análise de Armazenamento.
* [Habilitar métricas do Armazenamento do Azure e exibir dados de métricas](../storage-enable-and-view-metrics.md) usando o PowerShell e de forma programática com C#.
