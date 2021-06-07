---
title: Solução de problemas Azure Monitor gráficos de métricas
description: Solucionar problemas com a criação, personalização ou interpretação de gráficos de métricas
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: vitalyg
ms.openlocfilehash: bc1978f6ed0aa3e38f25fa03fc54ddc7d4efb9c3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102033326"
---
# <a name="troubleshooting-metrics-charts"></a>Solução de problemas nos gráficos de métrica

Use este artigo se você encontrar problemas com a criação, personalização ou interpretação de gráficos no Azure Metrics Explorer. Se você for novo nas métricas, saiba mais sobre como [começar a usar o Metrics Explorer](metrics-getting-started.md) e [recursos avançados do Metrics Explorer](../essentials/metrics-charts.md). Você também pode ver [exemplos](../essentials/metric-chart-samples.md) dos gráficos de métricas configurados.

## <a name="chart-shows-no-data"></a>O gráfico não mostra dados

Às vezes, os gráficos podem não mostrar nenhum dado depois de selecionar os recursos e as métricas corretos. Esse comportamento pode ser causado por vários dos seguintes motivos:

### <a name="microsoftinsights-resource-provider-isnt-registered-for-your-subscription"></a>O provedor de recursos Microsoft.Insights não foi registrado para a assinatura

A exploração de métricas precisa do provedor de recursos *Microsoft.Insights* registrado em sua assinatura. Em muitos casos, ele é registrado automaticamente (ou seja, depois de configurar uma regra de alerta, personalizar as configurações de diagnóstico de qualquer recurso ou configurar uma regra de dimensionamento automático). Se o provedor de recursos Microsoft. insights não estiver registrado, você deverá registrá-lo manualmente seguindo as etapas descritas em [provedores de recursos e tipos do Azure](../../azure-resource-manager/management/resource-providers-and-types.md).

**Solução:** Abra **assinaturas**, guia **provedores de recursos** e verifique se o *Microsoft. insights* está registrado para sua assinatura.

### <a name="you-dont-have-sufficient-access-rights-to-your-resource"></a>Você não tem direitos de acesso suficientes para seu recurso

No Azure, o acesso a métricas é controlado pelo Azure [RBAC (controle de acesso baseado em função)](../../role-based-access-control/overview.md). Você deve ser um membro de [leitor de monitoramento](../../role-based-access-control/built-in-roles.md#monitoring-reader), [colaborador de monitoramento](../../role-based-access-control/built-in-roles.md#monitoring-contributor) ou [colaborador](../../role-based-access-control/built-in-roles.md#contributor) para explorar as métricas de qualquer recurso.

**Solução:** Verifique se você tem permissões suficientes para o recurso do qual você está explorando métricas.

### <a name="your-resource-didnt-emit-metrics-during-the-selected-time-range"></a>O recurso não emite métricas durante o intervalo de tempo selecionado

Alguns recursos não emitem suas métricas constantemente. Por exemplo, o Azure não coletará as métricas para máquinas virtuais interrompidas. Outros recursos podem emitir métricas somente quando ocorrer alguma condição. Por exemplo, uma métrica que mostra o tempo de processamento de uma transação requer pelo menos uma transação. Se não houver nenhuma transação no intervalo de tempo selecionado, o gráfico naturalmente estará vazio. Além disso, embora a maioria das métricas no Azure seja coletada a cada minuto, há algumas que são coletadas com menos frequência. Consulte a documentação de métrica para obter mais detalhes sobre a métrica que está tentando explorar.

**Solução:** Altere a hora do gráfico para um intervalo mais amplo. Você pode começar com os "últimos 30 dias" usando uma granularidade de tempo maior (ou contando com a opção "granularidade automática de tempo").

### <a name="you-picked-a-time-range-greater-than-30-days"></a>Você escolheu um intervalo de tempo maior que 30 dias

[A maioria das métricas no Azure é armazenada por 93 dias](../essentials/data-platform-metrics.md#retention-of-metrics). No entanto, você não pode consultar mais de 30 dias de dados em um único gráfico. Essa limitação não se aplica a [métricas baseadas em log](../app/pre-aggregated-metrics-log-metrics.md#log-based-metrics).

**Solução:** Se você vir um gráfico em branco ou o gráfico exibir apenas parte dos dados da métrica, verifique se a diferença entre as datas de início e término no seletor de hora não excede o intervalo de 30 dias.

### <a name="all-metric-values-were-outside-of-the-locked-y-axis-range"></a>Todos os valores de métrica estavam fora do intervalo do eixo y bloqueado

Por [bloqueio dos limites do eixo y do gráfico](../essentials/metrics-charts.md#locking-the-range-of-the-y-axis), você acidentalmente pode fazer com que a área de exibição de gráfico não mostre a linha do gráfico. Por exemplo, se o eixo y estiver bloqueado em um intervalo entre 0% e 50% e a métrica tiver um valor constante de 100%, a linha será sempre renderizada fora da área visível, fazendo com que o gráfico apareça em branco.

**Solução:** Verifique se os limites do eixo y do gráfico não estão bloqueados fora do intervalo dos valores de métrica. Se os limites do eixo y estiverem bloqueados, você talvez queira redefini-los temporariamente para garantir que os valores da métrica não fiquem fora do intervalo do gráfico. Bloquear o intervalo do eixo y não é recomendado com granularidade automática para que os gráficos com agregação **soma**, **mín** e **máx** porque seus valores serão alterados com granularidade redimensionando a janela do navegador ou indo de uma resolução de tela para outra. Alternar a granularidade pode deixar a área de exibição do gráfico vazia.

### <a name="you-are-looking-at-a-guest-os-metric-but-didnt-enable-azure-diagnostic-extension"></a>Você está olhando para uma métrica de SO convidado, mas não habilitou a extensão de diagnóstico do Azure

A coleta de métricas do **Sistema Operacional Convidado** requer a configuração da Extensão de Diagnóstico do Azure ou habilitá-la usando o painel **Configurações de Diagnóstico** para seu recurso.

**Solução:** Se Diagnóstico do Azure extensão estiver habilitada, mas você ainda não conseguir ver suas métricas, siga as etapas descritas no [Guia de solução de problemas de extensão diagnóstico do Azure](../agents/diagnostics-extension-troubleshooting.md#metric-data-doesnt-appear-in-the-azure-portal). Consulte também as etapas de solução de problemas para [não pode escolher o namespace e as métricas do SO convidado](#cannot-pick-guest-os-namespace-and-metrics)

## <a name="error-retrieving-data-message-on-dashboard"></a>Mensagem "erro ao recuperar dados" no painel

Esse problema pode ocorrer quando o painel foi criado com uma métrica que foi descontinuada e removida do Azure. Para verificar se é o caso, abra a guia **métricas** do recurso e verifique as métricas disponíveis no seletor de métrica. Se a métrica não for exibida, é porque ela foi removida do Azure. Geralmente, uma métrica é descontinuada em detrimento de outra melhor que fornece uma perspectiva semelhante sobre a integridade do recurso.

**Solução:** Atualize o bloco com falha selecionando uma métrica alternativa para seu gráfico no painel. Você pode [revisar uma lista das métricas disponíveis para os serviços do Azure](./metrics-supported.md).

## <a name="chart-shows-dashed-line"></a>O gráfico mostra a linha tracejada

Os gráficos de métricas do Azure usam o estilo de linha tracejado para indicar que há um valor ausente (também conhecido como "valor nulo") entre dois pontos de dados de intervalo de tempo conhecidos. Por exemplo, se no seletor de tempo você escolheu a granularidade de tempo "1 minuto", mas a métrica foi relatada às 07:26, 07:27, 07:29 e 07:30 (Observe uma lacuna de minuto entre o segundo e o terceiro pontos de dados), uma linha tracejada se conectará a 07:27 e 07:29 e uma linha sólida conectará todos os outros pontos de dados. A linha tracejada cai para zero quando a métrica usa a agregação **Count** e **sum** . Para as agregações **AVG**, **min** ou **Max** , a linha tracejada conecta dois pontos de dados conhecidos mais próximos. Além disso, quando os dados estão ausentes no lado mais à direita ou à esquerda do gráfico, a linha tracejada se expande na direção do ponto de dados ausente.
  ![Captura de tela que mostra como quando os dados estão ausentes no lado mais à direita ou à esquerda do gráfico, a linha tracejada se expande para a direção do ponto de dados ausente.](./media/metrics-troubleshoot/dashed-line.png)

**Solução:** Esse comportamento é por design. Ele é útil para identificar pontos de dados ausentes. O gráfico de linhas é uma opção superior para a visualização de tendências de métricas de alta densidade, mas pode ser difícil interpretar para as métricas com valores esparsos, especialmente quando valores corelacionados com o intervalo de tempo são importantes. A linha tracejada facilita a leitura desses gráficos, mas se seu gráfico ainda não estiver claro, considere a possibilidade de visualizar suas métricas em outro tipo de gráfico. Por exemplo, um gráfico de plotagem disperso para a mesma métrica claramente mostra cada intervalo de tempo apenas visualizando um ponto quando há um valor e ignorando o ponto de dados quando o valor está ausente: ![ captura de tela que realça a opção de menu gráfico de dispersão.](./media/metrics-troubleshoot/scatter-plot.png)

   > [!NOTE]
   > Se você ainda preferir um gráfico de linhas para sua métrica, mover o mouse sobre o gráfico pode ajudar a avaliar a granularidade de tempo ao realçar o ponto de dados no local do ponteiro do mouse.

## <a name="chart-shows-unexpected-drop-in-values"></a>O gráfico mostra uma queda inesperada de valores

Em muitos casos, a queda perceptível nos valores de métrica é um mal-entendido dos dados mostrados no gráfico. Você pode ser confundido por uma queda na somas ou contagens de quando o gráfico mostra os minutos mais recentes porque os últimos pontos de dados de métrica ainda não foram recebidos ou processados pelo Azure. Dependendo do serviço, a latência de métricas de processamento pode estar dentro de um intervalo de alguns minutos. Para gráficos mostrando um intervalo de tempo recente com uma granularidade de 1 ou 5 minutos, uma queda do valor nos últimos minutos torna-se mais perceptível: ![ captura de tela que mostra uma queda do valor nos últimos minutos.](./media/metrics-troubleshoot/unexpected-dip.png)

**Solução:** Esse comportamento é por design. Acreditamos que é benéfico mostrar os dados assim que eles forem recebidos, mesmo quando os dados forem *parciais* ou *incompletos*. Isso permite que você tire conclusões importantes mais cedo e inicie a investigação imediatamente. Por exemplo, em uma métrica que mostra o número de falhas, ver um valor parcial X informa que havia pelo menos X falhas em um determinado minuto. Você pode começar a investigar o problema imediatamente, em vez de esperar para ver a contagem exata de falhas que ocorreram nesse minuto, que podem não ser tão importantes. O gráfico será atualizado depois de recebermos todo o conjunto de dados mas, nesse momento, ele também poderá mostrar novos pontos de dados incompletos de minutos mais recentes.

## <a name="cannot-pick-guest-os-namespace-and-metrics"></a>Não é possível escolher o namespace e as métricas do SO convidado

Máquinas virtuais e conjuntos de dimensionamento de máquinas virtuais têm duas categorias de métricas: As métricas de **Host de Máquina Virtual** que são coletadas pelo ambiente de hospedagem do Azure e as métricas do **sistema operacional convidado (clássico)** que são coletadas pelo [agente de monitoramento](../agents/agents-overview.md) em execução em suas máquinas virtuais. O agente de monitoramento é instalado habilitando a [Extensão de Diagnóstico do Azure](../agents/diagnostics-extension-overview.md).

Por padrão, as métricas de sistema operacional convidado são armazenadas na conta de Armazenamento do Microsoft Azure, que você seleciona na guia **Configurações de Diagnóstico** do recurso. Se as métricas do sistema operacional convidado não forem coletadas ou se o explorador de métricas não consegue acessá-las, você só verá o namespace da métrica do **Host de Máquina Virtual**:

![imagem de métrica](./media/metrics-troubleshoot/vm.png)

**Solução:** Se você não vir o namespace e as métricas do **sistema operacional convidado (clássico)** no Metrics Explorer:

1. Confirme se a [Extensão de Diagnóstico do Azure](../agents/diagnostics-extension-overview.md) está habilitada e configurada para coletar métricas.
    > [!WARNING]
    > Não é possível usar o [Agente do Log Analytics](../agents/agents-overview.md#log-analytics-agent) (também conhecido como Microsoft Monitoring Agent ou "MMA") para enviar o **Sistema Operacional Convidado** para uma conta de armazenamento.

1. Verifique se o provedor de recursos **Microsoft. insights** está [registrado para sua assinatura](#microsoftinsights-resource-provider-isnt-registered-for-your-subscription).

1. Verifique se a conta de armazenamento não está protegida pelo firewall. O portal do Azure precisa acessar a conta de armazenamento para recuperar dados de métricas e plotar os gráficos.

1. Use [Gerenciador de armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/) para validar que as métricas estão fluindo para a conta de armazenamento. Se as métricas não estiverem sendo coletadas, siga o [Guia de solução de problemas da Extensão de Diagnóstico do Azure](../agents/diagnostics-extension-troubleshooting.md#metric-data-doesnt-appear-in-the-azure-portal).

## <a name="next-steps"></a>Próximas etapas

* [Saiba mais sobre como começar a usar o Gerenciador de métricas](metrics-getting-started.md)
* [Saiba mais sobre os recursos avançados do Gerenciador de métricas](../essentials/metrics-charts.md)
* [Veja uma lista das métricas disponíveis para os serviços do Azure](./metrics-supported.md)
* [Veja exemplos de gráficos configurados](../essentials/metric-chart-samples.md)