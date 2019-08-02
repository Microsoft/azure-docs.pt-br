---
title: Solução de problemas de gráficos de métricas do Azure Monitor
description: Solucionar os problemas com a criação, personalização ou interpretação de gráficos de métricas
author: vgorbenko
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: 73ef5cc00b5154dbdbc92911d17740c7d13038ec
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341975"
---
# <a name="troubleshooting-metrics-charts"></a>Solucionando problemas de gráficos de métricas

Use este artigo se você enfrentar problemas com a criação, personalização ou interpretação de gráficos no Gerenciador de métricas do Azure. Se você estiver familiarizado com as métricas, que saiba mais sobre [guia de Introdução do metrics explorer](metrics-getting-started.md) e [recursos avançados do Gerenciador de métricas](metrics-charts.md). Você também pode ver [exemplos](metric-chart-samples.md) dos gráficos de métrica configurados.

## <a name="cant-find-your-resource-to-select-it"></a>Não é possível localizar o recurso para selecioná-lo

Você clicou na **selecione um recurso** botão, mas não vir seu recurso na caixa de diálogo de seletor de recurso.

**Solução:** Gerenciador de métricas exige que você selecione as assinaturas e grupos de recursos antes de listagem de recursos disponíveis. Se você não vir seu recurso:

1. Certifique-se de que você tenha selecionado a assinatura correta na **assinatura** lista suspensa. Se sua assinatura não estiver listada, clique no **diretório + assinatura configurações** e adicionar uma assinatura com o recurso.

1. Certifique-se de que você selecionou o grupo de recursos correto.
    > [!WARNING]
    > Para obter melhor desempenho, quando você abrir o Gerenciador de métricas, o **grupo de recursos** suspenso não tem nenhum grupo de recursos previamente selecionado. Você deve escolher pelo menos um grupo antes que você possa ver todos os recursos.

## <a name="chart-shows-no-data"></a>Gráfico não mostra nenhum dado

Às vezes, os gráficos não podem mostrar nenhum dado depois de selecionar as métricas e os recursos corretos. Esse comportamento pode ser causado por vários dos seguintes motivos:

### <a name="microsoftinsights-resource-provider-isnt-registered-for-your-subscription"></a>Provedor de recursos Microsoft. Insights não está registrado para sua assinatura

Explorando métricas requer *Microsoft. Insights* registrado do provedor de recursos em sua assinatura. Em muitos casos, ele é registrado automaticamente (ou seja, depois de configurar uma regra de alerta, personalizar as configurações de diagnóstico para qualquer recurso ou configurar uma regra de dimensionamento automático). Se o provedor de recursos Microsoft. Insights não estiver registrado, você deve registrá-lo manualmente seguindo as etapas descritas em [provedores de recursos do Azure e tipos](../../azure-resource-manager/resource-manager-supported-services.md).

**Solução:** Abra **inscrições**, **provedores de recursos** guia e, em seguida, verifique *Microsoft. Insights* está registrado para sua assinatura.

### <a name="you-dont-have-sufficient-access-rights-to-your-resource"></a>Você não tem direitos de acesso suficientes para seu recurso

No Azure, o acesso às métricas é controlado pelas [controle de acesso baseado em função (RBAC)](../../role-based-access-control/overview.md). Você deve ser um membro da [leitor de monitoramento](../../role-based-access-control/built-in-roles.md#monitoring-reader), [Colaborador de monitoramento](../../role-based-access-control/built-in-roles.md#monitoring-contributor), ou [Colaborador](../../role-based-access-control/built-in-roles.md#contributor) para explorar as métricas para qualquer recurso.

**Solução:** Certifique-se de que você tenha permissões suficientes para o recurso do qual você está explorando métricas.

### <a name="your-resource-didnt-emit-metrics-during-the-selected-time-range"></a>O recurso não emite métricas durante o intervalo de tempo selecionado

Alguns recursos não emitem constantemente a suas métricas. Por exemplo, o Azure não coletará as métricas para máquinas virtuais interrompidas. Outros recursos podem emitir suas métricas somente quando ocorre alguma condição. Por exemplo, uma métrica que mostra o tempo de processamento de uma transação requer pelo menos uma transação. Se não houver nenhuma transação no intervalo de tempo selecionado, o gráfico naturalmente estará vazio. Além disso, embora a maioria das métricas no Azure é coletada a cada minuto, há alguns que são coletados com menos frequência. Consulte a documentação de métrica para obter mais detalhes sobre a métrica que você está tentando explorar.

**Solução:** Altere a hora do gráfico para uma gama mais ampla. Você pode iniciar a partir de "Últimos 30 dias" usando uma maior granularidade de tempo (ou contar com a opção "granularidade de hora automática").

### <a name="you-picked-a-time-range-greater-than-30-days"></a>Você escolheu um intervalo de tempo maior que 30 dias

[A maioria das métricas no Azure são armazenados para 93 dias](data-platform-metrics.md#retention-of-metrics). No entanto, você pode consultar apenas não mais de 30 dias de dados em um único gráfico. Essa limitação não se aplica a [métricas baseadas em log](../app/pre-aggregated-metrics-log-metrics.md#log-based-metrics).

**Solução:** Se você ver um gráfico em branco ou seu gráfico exibe apenas a parte de dados de métrica, verifique se que a diferença entre o início - e -datas de término no seletor de tempo não excede o intervalo de 30 dias.

### <a name="all-metric-values-were-outside-of-the-locked-y-axis-range"></a>Todos os valores de métrica foram fora do intervalo do eixo y bloqueado

Por [bloqueio dos limites do eixo y do gráfico](metrics-charts.md#lock-boundaries-of-chart-y-axis), você acidentalmente pode tornar a área de exibição de gráfico não mostrar a linha do gráfico. Por exemplo, se o eixo y está bloqueado para um intervalo entre 0% e 50% e a métrica tem um valor constante de 100%, a linha é sempre renderizada fora da área visível, fazendo com que o gráfico aparecem em branco.

**Solução:** Verifique se que os limites do eixo y do gráfico não são bloqueados fora do intervalo dos valores de métrica. Se os limites do eixo y são bloqueados, você talvez queira temporariamente redefini-las para garantir que a métrica de valores não ficam fora do intervalo do gráfico. Bloqueio de intervalo do eixo y não é recomendado com granularidade de automática para que os gráficos com **soma**, **min**, e **max** agregação porque seus valores são alterados com granularidade redimensionando a janela do navegador ou indo de resolução de uma tela para outra. Granularidade de comutação pode deixar a área de exibição de seu gráfico vazio.

### <a name="you-are-looking-at-a-guest-os-metric-but-didnt-enable-azure-diagnostic-extension"></a>Você está vendo uma métrica de sistema operacional convidado, mas não tiver habilitado a extensão de diagnóstico do Azure

Coleção de **SO convidado** métricas requerem a configuração da extensão de diagnóstico do Azure ou habilitá-la usando o **configurações de diagnóstico** painel para seu recurso.

**Solução:** Se a extensão de diagnóstico do Azure está habilitada, mas ainda não puder ver suas métricas, siga as etapas descritas em [guia de solução de problemas da extensão de diagnóstico do Azure](diagnostics-extension-troubleshooting.md#metric-data-doesnt-appear-in-the-azure-portal). Consulte também as etapas de solução de problemas para [não é possível escolher o namespace do sistema operacional convidado e métricas](metrics-troubleshoot.md#cannot-pick-guest-os-namespace-and-metrics)

## <a name="error-retrieving-data-message-on-dashboard"></a>Mensagem "Erro ao recuperar dados" no painel

Esse problema pode ocorrer quando o painel foi criado com uma métrica que foi preterida e removida do Azure mais tarde. Para verificar o que é o caso, abra o **métricas** guia de recursos e as métricas disponíveis no seletor de métrica de seleção. Se a métrica não for exibida, a métrica foi removida do Azure. Geralmente, quando uma métrica foi preterida, é uma métrica melhor nova que fornece uma perspectiva semelhante sobre a integridade do recurso.

**Solução:** Atualize o bloco de falha por uma métrica alternativa para o gráfico no painel de separação. Você pode [examine uma lista de métricas disponíveis para serviços do Azure](metrics-supported.md).

## <a name="chart-shows-dashed-line"></a>Gráfico mostra a linha tracejada

Os gráficos de métricas do Azure usam o estilo de linha tracejada para indicar que há um valor ausente (também conhecido como "valor nulo") entre o intervalo de tempo conhecido dois pontos de dados. Por exemplo, se no seletor de tempo você escolheu a granularidade de tempo "minuto", mas a métrica foi relatada no 07:26, 07:27, 07:29 e 07:30 (Observe um intervalo de minutos entre pontos de dados do segundo e terceiro), uma linha tracejada conectará 07:27 e 07:29 e irão se conectar a uma linha sólida todos os outros pontos de dados. Os descartes de linha tracejada para zero quando a métrica utiliza **contagem** e **soma** agregação. Para o **avg**, **min** ou **max** agregações, a linha tracejada conecta dois pontos de dados conhecidos mais próximos. Além disso, quando os dados estão ausentes no lado mais à direita ou esquerdo do gráfico, a linha tracejada se expande para a direção do ponto de dados ausente.
  ![imagem de métrica](./media/metrics-troubleshoot/missing-data-point-line-chart.png)

**Solução:** Este comportamento ocorre por design. Ele é útil para identificar pontos de dados ausentes. O gráfico de linhas é uma opção superior para a visualização de tendências de métricas de alta densidade, mas pode ser difícil de interpretar para as métricas com valores esparsos, especialmente quando corelating valores com o intervalo de tempo é importante. A linha tracejada torna mais fácil a leitura destes gráficos, mas se seu gráfico não está claro ainda, considere a possibilidade de visualizar suas métricas com outro tipo de gráfico. Por exemplo, um gráfico disperso para a mesma métrica mostra claramente cada intervalo de agregação por apenas visualizar um ponto quando há um valor e ignorando os dados de ponto completamente quando o valor está ausente: ![imagem de métrica](./media/metrics-troubleshoot/missing-data-point-scatter-chart.png)

   > [!NOTE]
   > Se você ainda preferir um gráfico de linhas para sua métrica, mover o mouse sobre o gráfico pode ajudar a avaliar a granularidade de tempo, realçando o ponto de dados no local do ponteiro do mouse.

## <a name="chart-shows-unexpected-drop-in-values"></a>Gráfico mostra soltar inesperado em valores

Em muitos casos, a queda perceptível nos valores de métrica é um mal-entendido dados mostrados no gráfico. Você pode se confundir por uma queda na somas ou contagens de quando o gráfico mostra os minutos mais recentes, porque os últimos pontos de dados de métrica ainda não foram recebidos ou processados pelo Azure. Dependendo do serviço, a latência de métricas de processamento pode ser dentro do intervalo de alguns minutos. Para gráficos mostrando um intervalo de tempo recente com uma granularidade de 1 a 5 minutos, uma queda do valor sobre nos últimos minutos se torna mais perceptível: ![imagem de métrica](./media/metrics-troubleshoot/drop-in-values.png)

**Solução:** Este comportamento ocorre por design. Acreditamos que é benéfico mostrando os dados assim que elas são recebidas, mesmo quando os dados estiverem *parcial* ou *incompleta*. Isso permite que você faça mais cedo conclusão importante e iniciar imediatamente a investigação. Por exemplo, para uma métrica que mostra o número de falhas, ver um valor parcial X informa que havia pelo menos X falhas em um determinado minuto. Você pode começar a investigar o problema imediatamente, em vez de esperar para ver a contagem exata de falhas que ocorreram nesse minuto, o que pode não ser tão importante. O gráfico será atualizado depois de recebermos todo o conjunto de dados, mas nesse momento, ele também pode mostrar novos pontos de dados incompletos de minutos mais recentes.

## <a name="cannot-pick-guest-os-namespace-and-metrics"></a>Não é possível escolher as métricas e o namespace de sistema operacional convidado

As máquinas virtuais e conjuntos de dimensionamento de máquina virtual tem duas categorias de métricas: **Host de máquina virtual** métricas que são coletadas pelo ambiente de hospedagem do Azure, e **sistema operacional convidado** métricas que são coletadas pelo [agente de monitoramento](agents-overview.md) em execução em suas máquinas virtuais. Instalar o agente de monitoramento, permitindo [extensão de diagnóstico do Azure](diagnostics-extension-overview.md).

Por padrão, as métricas de SO convidado são armazenadas na conta de armazenamento do Azure, que você escolher o **configurações de diagnóstico** guia do recurso. Se as métricas do sistema operacional convidado não são coletadas ou do metrics explorer não pode acessá-los, você só verá os **Host de máquina Virtual** namespace da métrica:

![imagem de métrica](./media/metrics-troubleshoot/cannot-pick-guest-os-namespace.png)

**Solução:** Se você não vir **sistema operacional convidado** namespace e métricas no metrics explorer:

1. Confirme [extensão de diagnóstico do Azure](diagnostics-extension-overview.md) está habilitado e configurado para coletar métricas.
    > [!WARNING]
    > Não é possível usar [agente do Log Analytics](agents-overview.md#log-analytics-agent) (também conhecido como o Microsoft Monitoring Agent, ou "MMA") para enviar **SO convidado** em uma conta de armazenamento.

1. Verifique se que a conta de armazenamento não está protegida pelo firewall.

1. Use o [Gerenciador de armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/) para validar que as métricas estão fluindo para a conta de armazenamento. Se as métricas não são coletadas, execute as [guia de solução de problemas da extensão de diagnóstico do Azure](diagnostics-extension-troubleshooting.md#metric-data-doesnt-appear-in-the-azure-portal).

## <a name="next-steps"></a>Próximas etapas

* [Saiba mais sobre a introdução ao Gerenciador de métricas](metrics-getting-started.md)
* [Saiba mais sobre os recursos avançados do Metrics Explorer](metrics-charts.md)
* [Ver uma lista de métricas disponíveis para serviços do Azure](metrics-supported.md)
* [Consulte exemplos de gráficos configurados](metric-chart-samples.md)
