---
title: Como monitorar a disponibilidade do cluster com os logs de Azure Monitor no HDInsight
description: Saiba como usar os logs de Azure Monitor para monitorar a integridade e a disponibilidade do cluster.
ms.service: hdinsight
ms.topic: how-to
ms.date: 08/12/2020
ms.openlocfilehash: d52cb1c5f3b1dd1b23adb39f2f65d0e66968e482
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946961"
---
# <a name="how-to-monitor-cluster-availability-with-azure-monitor-logs-in-hdinsight"></a>Como monitorar a disponibilidade do cluster com os logs de Azure Monitor no HDInsight

Os clusters HDInsight incluem a integração de logs de Azure Monitor, que fornece logs e métricas consultáveis, bem como alertas configuráveis. Este artigo mostra como usar Azure Monitor para monitorar o cluster.

## <a name="azure-monitor-logs-integration"></a>Integração de logs de Azure Monitor

Os logs de Azure Monitor permitem que os dados gerados por vários recursos, como clusters HDInsight, sejam coletados e agregados em um único local para alcançar uma experiência de monitoramento unificada.

Como pré-requisito, você precisará de um espaço de trabalho Log Analytics para armazenar os dados coletados. Se você ainda não criou uma, você pode seguir as instruções aqui: [criar um log Analytics espaço de trabalho](../azure-monitor/learn/quick-create-workspace.md).

## <a name="enable-hdinsight-azure-monitor-logs-integration"></a>Habilitar a integração de logs de Azure Monitor do HDInsight

Na página de recursos do cluster HDInsight no portal, selecione **Azure monitor**. Em seguida, selecione **habilitar** e selecione o espaço de trabalho log Analytics na lista suspensa.

![HDInsight Operations Management Suite](media/cluster-availability-monitor-logs/azure-portal-monitoring.png)

Por padrão, isso instala o agente do OMS em todos os nós de cluster, exceto os nós de borda. Como nenhum agente do OMS está instalado em nós de borda de cluster, não há telemetria em nós de borda presentes em Log Analytics por padrão.

## <a name="query-metrics-and-logs-tables"></a>Consultar tabelas de métricas e logs

Quando Azure Monitor integração de log estiver habilitada (isso pode levar alguns minutos), navegue até o recurso de **espaço de trabalho do log Analytics** e selecione **logs**.

![Log Analytics logs do espaço de trabalho](media/cluster-availability-monitor-logs/hdinsight-portal-logs.png)

Os logs listam várias consultas de exemplo, como:

| Nome da consulta                      | Descrição                                                               |
|---------------------------------|---------------------------------------------------------------------------|
| Disponibilidade de computadores hoje    | Gráfico o número de computadores que enviam logs, a cada hora                     |
| Listar pulsações                 | Listar todas as pulsações de computador da última hora                           |
| Última pulsação de cada computador | Mostrar a última pulsação enviada por cada computador                             |
| Computadores não disponíveis           | Listar todos os computadores conhecidos que não enviaram uma pulsação nas últimas 5 horas |
| Taxa de disponibilidade               | Calcular a taxa de disponibilidade de cada computador conectado                |

Por exemplo, execute a consulta de exemplo **taxa de disponibilidade** selecionando **executar** na consulta, conforme mostrado na captura de tela acima. Isso mostrará a taxa de disponibilidade de cada nó no cluster como uma porcentagem. Se você tiver habilitado vários clusters HDInsight para enviar métricas para o mesmo espaço de trabalho Log Analytics, verá a taxa de disponibilidade para todos os nós (excluindo nós de borda) nesses clusters exibidos.

![Consulta de exemplo ' taxa de disponibilidade ' de logs do espaço de trabalho Log Analytics](media/cluster-availability-monitor-logs/portal-availability-rate.png)

> [!NOTE]  
> A taxa de disponibilidade é medida em um período de 24 horas, portanto, o cluster precisará ser executado por pelo menos 24 horas antes de você ver taxas de disponibilidade precisas.

Você pode fixar essa tabela em um painel compartilhado clicando em **fixar** no canto superior direito. Se você não tiver nenhum Dashboard compartilhado gravável, poderá ver como criar um aqui: [criar e compartilhar painéis no portal do Azure](../azure-portal/azure-portal-dashboards.md#publish-and-share-a-dashboard).

## <a name="azure-monitor-alerts"></a>Alertas do Azure Monitor

Você também pode configurar Azure Monitor alertas que serão disparados quando o valor de uma métrica ou os resultados de uma consulta atenderem a determinadas condições. Por exemplo, vamos criar um alerta para enviar um email quando um ou mais nós não enviar uma pulsação em 5 horas (ou seja, presume-se que não esteja disponível).

Em **logs**, execute a consulta de exemplo **computadores indisponíveis** selecionando **executar** na consulta, conforme mostrado abaixo.

![Log Analytics do espaço de trabalho registra o exemplo de "computadores não disponíveis"](media/cluster-availability-monitor-logs/portal-unavailable-computers.png)

Se todos os nós estiverem disponíveis, essa consulta deverá retornar nenhum resultado por enquanto. Clique em **nova regra de alerta** para começar a configurar o alerta para esta consulta.

![Nova regra de alerta do Log Analytics Workspace](media/cluster-availability-monitor-logs/portal-logs-new-alert-rule.png)

Há três componentes para um alerta: o *recurso* para o qual criar a regra (o log Analytics espaço de trabalho nesse caso), a *condição* para disparar o alerta e os *grupos de ações* que determinam o que acontecerá quando o alerta for disparado.
Clique no **título da condição**, conforme mostrado abaixo, para concluir a configuração da lógica de sinal.

![Alerta do portal criar condição de regra](media/cluster-availability-monitor-logs/portal-condition-title.png)

Isso abrirá **Configurar lógica de sinal**.

Defina a seção de **lógica de alerta** da seguinte maneira:

*Com base em: número de resultados, condição: maior que, limite: 0.*

Como essa consulta só retorna nós indisponíveis como resultados, se o número de resultados for maior que 0, o alerta deverá ser acionado.

Na seção **avaliado com base em** , defina o **período** e a **frequência** com base na frequência com que você deseja verificar se há nós indisponíveis.

Para fins deste alerta, você deseja verificar o **período = frequência.** Mais informações sobre o período, a frequência e outros parâmetros de alerta podem ser encontrados [aqui](../azure-monitor/platform/alerts-unified-log.md#alert-logic-definition).

Selecione **concluído** quando tiver terminado de configurar a lógica de sinal.

![Regra de alerta configura a lógica do sinal](media/cluster-availability-monitor-logs/portal-configure-signal-logic.png)

Se você ainda não tiver um grupo de ações existente, clique em **criar novo** na seção **grupos de ação** .

![A regra de alerta cria um novo grupo de ação](media/cluster-availability-monitor-logs/portal-create-new-action-group.png)

Isso abrirá **Adicionar grupo de ação**. Escolha um **nome de grupo de ação**, **nome curto**, **assinatura** e **grupo de recursos.** Na seção **ações** , escolha um **nome de ação** e selecione **email/SMS/Push/voz** como o **tipo de ação.**

> [!NOTE]
> Há várias outras ações que um alerta pode disparar além de um email/SMS/Push/voz, como um Azure functions, LogicApp, webhook, ITSM e runbook de automação. [Saiba Mais.](../azure-monitor/platform/action-groups.md#action-specific-information)

Isso abrirá **email/SMS/Push/Voice**. Escolha um **nome** para o destinatário, **marque** a caixa **email** e digite um endereço de email para o qual você deseja que o alerta seja enviado. Selecione **OK** em  **email/SMS/Push/voz** e, em seguida, em **Adicionar grupo de ação** para concluir a configuração do grupo de ação.

![Regra de alerta cria Adicionar grupo de ação](media/cluster-availability-monitor-logs/portal-add-action-group.png)

Depois que essas folhas forem fechadas, você deverá ver o grupo de ações listado na seção **grupos de ações** . Por fim, conclua a seção **detalhes do alerta** digitando um nome e uma **Descrição** da **regra de alerta** e escolhendo uma **severidade**. Clique em **criar regra de alerta** para concluir.

![O portal cria a regra de alerta concluir](media/cluster-availability-monitor-logs/portal-create-alert-rule-finish.png)

> [!TIP]
> A capacidade de especificar a **gravidade** é uma ferramenta poderosa que pode ser usada durante a criação de vários alertas. Por exemplo, você pode criar um alerta para gerar um aviso (Sev 1) se um único nó de cabeçalho ficar inativo e outro alerta que gere crítico (Sev 0) no caso improvável de ambos os nós de cabeçalho ficarem inativos.

Quando a condição desse alerta for atendida, o alerta será acionado e você receberá um email com os detalhes do alerta como este:

![Exemplo de email de alerta Azure Monitor](media/cluster-availability-monitor-logs/portal-oms-alert-email.png)

Você também pode exibir todos os alertas que foram disparados, agrupados por severidade, acessando **alertas** em seu **espaço de trabalho do log Analytics**.

![Log Analytics alertas de espaço de trabalho](media/cluster-availability-monitor-logs/hdi-portal-oms-alerts.png)

Selecionar em um agrupamento de severidade (ou seja **, Sev 1,** como realçado acima) mostrará os registros de todos os alertas dessa severidade que foram disparados da seguinte maneira:

![Log Analytics espaço de trabalho Sev um alerta](media/cluster-availability-monitor-logs/portal-oms-alerts-sev1.png)

## <a name="next-steps"></a>Próximas etapas

* [Disponibilidade do cluster – Apache Ambari](./hdinsight-cluster-availability.md)
* [Usar logs de Azure Monitor](hdinsight-hadoop-oms-log-analytics-tutorial.md)
