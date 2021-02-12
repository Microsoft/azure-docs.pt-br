---
title: Solucionando problemas de alertas de métrica do Azure
description: Problemas comuns com Azure Monitor alertas de métrica e possíveis soluções.
author: harelbr
ms.author: harelbr
ms.topic: troubleshooting
ms.date: 01/21/2021
ms.subservice: alerts
ms.openlocfilehash: f7425e1cf34348b7742b739ef5440a5cb0355077
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98942093"
---
# <a name="troubleshooting-problems-in-azure-monitor-metric-alerts"></a>Solucionando problemas em alertas de métrica do Azure Monitor 

Este artigo aborda problemas comuns em Azure Monitor [alertas de métrica](alerts-metric-overview.md) e como solucioná-los.

Azure Monitor Alertas proativamente notificam você quando condições importantes são encontradas nos dados de monitoramento. Eles permitem que você identifique e resolva problemas antes que os usuários do seu sistema os percebam. Para obter mais informações sobre alertas, consulte [visão geral de alertas no Microsoft Azure](alerts-overview.md).

## <a name="metric-alert-should-have-fired-but-didnt"></a>O alerta de métrica deve ter sido acionado, mas não 

Se você acredita que um alerta de métrica deve ter sido acionado, mas não foi acionado e não foi encontrado no portal do Azure, tente as seguintes etapas:

1. **Configuração** -examine a configuração da regra de alerta de métrica para certificar-se de que ela está configurada corretamente:
    - Verifique se o **tipo de agregação** e a **granularidade de agregação (período)** estão configurados como esperado. O **tipo de agregação** determina como os valores de métrica são agregados (Saiba mais [aqui](./metrics-aggregation-explained.md#aggregation-types)), e a **granularidade de agregação (período)** controla o quanto a avaliação agrega os valores de métrica cada vez que a regra de alerta é executada.
    -  Verifique se o **valor** ou a **Sensibilidade** do limite estão configurados conforme o esperado.
    - Para uma regra de alerta que usa limites dinâmicos, verifique se as configurações avançadas estão definidas, uma vez que o **número de violações** pode filtrar alertas e **ignorar dados antes que** o possa afetar a forma como os limites são calculados.

       > [!NOTE] 
       > Os limites dinâmicos exigem pelo menos 3 dias e 30 amostras de métricas antes de ficarem ativos.

2. Disparado **mas sem notificação** -examine a [lista de alertas acionados](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/alertsV2) para ver se você pode localizar o alerta acionado. Se você puder ver o alerta na lista, mas tiver um problema com algumas de suas ações ou notificações, consulte mais informações [aqui](./alerts-troubleshoot.md#action-or-notification-on-my-alert-did-not-work-as-expected).

3. **Já ativo** -Verifique se já há um alerta acionado na série temporal de métricas para a qual você esperava receber um alerta. Os alertas de métricas são com estado, o que significa que, uma vez que um alerta é acionado em uma série temporal de métrica específica, alertas adicionais nessa série temporal não serão acionados até que o problema não seja mais observado. Essa opção de design reduz o ruído. O alerta é resolvido automaticamente quando a condição de alerta não é atendida por três avaliações consecutivas.

4. **Dimensões usadas** -se você tiver selecionado alguns [valores de dimensão para uma métrica](./alerts-metric-overview.md#using-dimensions), a regra de alerta monitorará cada série temporal de métrica individual (conforme definido pela combinação de valores de dimensão) para uma violação de limite. Para monitorar também a série temporal da métrica agregada (sem nenhuma dimensão selecionada), configure uma regra de alerta adicional na métrica sem selecionar dimensões.

5. **Agregação e granularidade de tempo** – se você estiver visualizando a métrica usando [gráficos de métricas](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/metrics), verifique se:
    * A **agregação** selecionada no gráfico de métrica é igual ao **tipo de agregação** em sua regra de alerta
    * A **granularidade de tempo** selecionada é igual à granularidade de **agregação (período)** em sua regra de alerta (e não definida como ' automática ')

## <a name="metric-alert-fired-when-it-shouldnt-have"></a>Alerta de métrica acionado quando não deveria

Se você acredita que seu alerta de métrica não deveria ter sido acionado, mas fez isso, as etapas a seguir podem ajudar a resolver o problema.

1. Examine a [lista de alertas acionados](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/alertsV2) para localizar o alerta acionado e clique para exibir seus detalhes. Examine as informações fornecidas sob **por que o alerta foi acionado?** para ver o gráfico de métrica, o **valor de métrica** e o **valor de limite** no momento em que o alerta foi disparado.

    > [!NOTE] 
    > Se você estiver usando um tipo de condição de limites dinâmicos e considerar que os limites usados não estavam corretos, forneça comentários usando o ícone de rosto triste. Esses comentários afetarão a pesquisa de algoritmos do Machine Learning e ajudarão a melhorar as detecções futuras.

2. Se você tiver selecionado vários valores de dimensão para uma métrica, o alerta será disparado quando **qualquer** uma das séries temporais de métricas (conforme definido pela combinação de valores de dimensão) violar o limite. Para obter mais informações sobre como usar dimensões em alertas de métrica, consulte [aqui](./alerts-metric-overview.md#using-dimensions).

3. Examine a configuração da regra de alerta para certificar-se de que ela está configurada corretamente:
    - Verifique se o **tipo de agregação**, a **granularidade de agregação (período)** e o **valor de limite** ou a **sensibilidade** estão configurados como esperado
    - Para uma regra de alerta que usa limites dinâmicos, verifique se as configurações avançadas estão definidas, uma vez que o **número de violações** pode filtrar alertas e **ignorar dados antes que** o possa afetar a forma como os limites são calculados

   > [!NOTE]
   > Os limites dinâmicos exigem pelo menos 3 dias e 30 amostras de métricas antes de ficarem ativos.

4. Se você estiver visualizando a métrica usando o [gráfico de métricas](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/metrics), verifique se:
    - A **agregação** selecionada no gráfico de métrica é igual ao **tipo de agregação** em sua regra de alerta
    - A **granularidade de tempo** selecionada é igual à granularidade de **agregação (período)** em sua regra de alerta (e não definida como ' automática ')

5. Se o alerta for disparado enquanto já tiver acionado alertas que monitoram os mesmos critérios (que não são resolvidos), verifique se a regra de alerta foi configurada com a propriedade *Automitigate* definida como **false** (essa propriedade só pode ser configurada por meio de REST/PowerShell/CLI. portanto, verifique o script usado para implantar a regra de alerta). Nesse caso, a regra de alerta não resolve automaticamente os alertas acionados e não requer que um alerta acionado seja resolvido antes de ser acionado novamente.


## <a name="cant-find-the-metric-to-alert-on---virtual-machines-guest-metrics"></a>Não é possível encontrar a métrica para alertar sobre as métricas de convidado de máquinas virtuais

Para alertar sobre as métricas do sistema operacional convidado de máquinas virtuais (por exemplo: memória, espaço em disco), verifique se você instalou o agente necessário para coletar esses dados para Azure Monitor métricas:
- [Para VMs do Windows](./collect-custom-metrics-guestos-resource-manager-vm.md)
- [Para VMs do Linux](./collect-custom-metrics-linux-telegraf.md)

Para obter mais informações sobre como coletar dados do sistema operacional convidado de uma máquina virtual, consulte [aqui](../insights/monitor-vm-azure.md#guest-operating-system).

> [!NOTE] 
> Se você configurou as métricas de convidado a serem enviadas para um espaço de trabalho Log Analytics, as métricas serão exibidas no recurso de espaço de trabalho Log Analytics e começarão a mostrar dados **somente** após a criação de uma regra de alerta que as monitore. Para isso, siga as etapas para [configurar um alerta de métrica para os logs](./alerts-metric-logs.md#configuring-metric-alert-for-logs).

> [!NOTE] 
> O monitoramento de uma métrica de convidado para várias máquinas virtuais com uma única regra de alerta não tem suporte dos alertas de métrica no momento. Você pode conseguir isso com uma [regra de alerta de log](./alerts-unified-log.md). Para fazer isso, verifique se as métricas de convidado são coletadas para um espaço de trabalho Log Analytics e crie uma regra de alerta de log no espaço de trabalho.

## <a name="cant-find-the-metric-to-alert-on"></a>Não é possível encontrar a métrica para alertar

Se você quiser criar um alerta em uma métrica específica, mas não puder vê-la ao criar uma regra de alerta, verifique o seguinte:
- Se você não conseguir ver nenhuma métrica para o recurso, [verifique se o tipo de recurso é compatível com alertas de métrica](./alerts-metric-near-real-time.md).
- Se você conseguir ver algumas métricas para o recurso, mas não conseguir encontrar uma métrica específica, [verifique se essa métrica está disponível](./metrics-supported.md)e, em caso afirmativo, veja a descrição da métrica para verificar se ela está disponível apenas em versões ou edições específicas do recurso.
- Se a métrica não estiver disponível para o recurso, ela poderá estar disponível nos logs de recursos e poderá ser monitorada usando alertas de log. Veja aqui para obter mais informações sobre como [coletar e analisar logs de recursos de um recurso do Azure](../learn/tutorial-resource-logs.md).

## <a name="cant-find-the-metric-dimension-to-alert-on"></a>Não é possível localizar a dimensão métrica para alertar

Se você estiver procurando alertar sobre [valores de dimensão específicos de uma métrica](./alerts-metric-overview.md#using-dimensions), mas não puder encontrar esses valores, observe o seguinte:

1. Pode levar alguns minutos para que os valores de dimensão apareçam na lista **Valores de dimensão**
1. Os valores de dimensão exibidos se baseiam nos dados de métrica coletados no último dia
1. Se o valor da dimensão ainda não for emitido ou não for mostrado, você poderá usar a opção ' adicionar valor personalizado ' para adicionar um valor de dimensão personalizado
1. Se você quiser alertar sobre todos os valores possíveis de uma dimensão (incluindo valores futuros), escolha a opção ' selecionar todos os valores atuais e futuros '

## <a name="metric-alert-rules-still-defined-on-a-deleted-resource"></a>Regras de alerta de métrica ainda definidas em um recurso excluído 

Ao excluir um recurso do Azure, as regras associadas de alerta de métrica não são excluídas automaticamente. Para excluir as regras de alerta associadas a um recurso que foi excluído:

1. Abra o grupo de recursos no qual o recurso excluído foi definido
1. Na lista exibindo os recursos, marque a caixa de seleção **Mostrar tipos ocultos**
1. Filtrar a lista por Tipo == **microsoft.insights/metricalerts**
1. Selecione as regras de alerta relevantes e selecione **excluir**

## <a name="make-metric-alerts-occur-every-time-my-condition-is-met"></a>Os alertas de métrica ocorrem toda vez que minha condição é atendida

Os alertas de métricas são monitorados por padrão e, portanto, alertas adicionais não são acionados se já houver um alerta acionado em uma determinada série temporal. Se você quiser tornar uma regra de alerta de métrica específica sem monitoração de estado e receber um alerta sobre cada avaliação em que a condição de alerta é atendida, crie a regra de alerta programaticamente (por exemplo, por meio do [Resource Manager](./alerts-metric-create-templates.md), do [PowerShell](/powershell/module/az.monitor/), do [REST](/rest/api/monitor/metricalerts/createorupdate), da [CLI](/cli/azure/monitor/metrics/alert)) e defina a propriedade *automitigate* como ' false '.

> [!NOTE] 
> Fazer uma regra de alerta de métrica sem monitoração de estado impede que os alertas disparados se tornem resolvidos, mesmo depois que a condição não for mais satisfeita, os alertas disparados permanecerão em um estado disparado até o período de retenção

## <a name="define-an-alert-rule-on-a-custom-metric-that-isnt-emitted-yet"></a>Definir uma regra de alerta em uma métrica personalizada que ainda não foi emitida

Ao criar uma regra de alerta de métrica, o nome da métrica é validado em relação à [API de definições de métrica](/rest/api/monitor/metricdefinitions/list) para verificar se ela existe. Em alguns casos, você gostaria de criar uma regra de alerta em uma métrica personalizada mesmo antes de ela ser emitida. Por exemplo, ao criar (usando um modelo do Resource Manager) um recurso Application Insights que emitirá uma métrica personalizada, juntamente com uma regra de alerta que monitora essa métrica.

Para evitar a falha da implantação ao tentar validar as definições da métrica personalizada, você pode usar o parâmetro *skipMetricValidation* na seção critérios da regra de alerta, o que fará com que a validação da métrica seja ignorada. Consulte o exemplo abaixo para saber como usar esse parâmetro em um modelo do Resource Manager. Para obter mais informações, consulte os [exemplos de modelo completos do Resource Manager para criar regras de alerta de métrica](./alerts-metric-create-templates.md).

```json
"criteria": {
    "odata.type": "Microsoft.Azure.Monitor.SingleResourceMultipleMetricCriteria",
        "allOf": [
            {
                "name" : "condition1",
                "metricName": "myCustomMetric",
                "metricNamespace": "myCustomMetricNamespace",
                "dimensions":[],
                "operator": "GreaterThan",
                "threshold" : 10,
                "timeAggregation": "Average",
                "skipMetricValidation": true
            }
        ]
    }
```

## <a name="export-the-azure-resource-manager-template-of-a-metric-alert-rule-via-the-azure-portal"></a>Exportar o modelo de Azure Resource Manager de uma regra de alerta de métrica por meio do portal do Azure

Exportar o modelo do Resource Manager de uma regra de alerta de métrica ajuda a entender sua sintaxe e propriedades JSON e pode ser usado para automatizar implantações futuras.
1. Navegue até a seção **grupos de recursos** no portal e selecione o grupo de recursos que contém a regra.
2. Na seção visão geral, marque a caixa de seleção **Mostrar tipos ocultos** .
3. No filtro de **tipo** , selecione *Microsoft. insights/metricalerts*.
4. Selecione a regra de alerta relevante para exibir seus detalhes.
5. Em **configurações**, selecione **Exportar modelo**.

## <a name="metric-alert-rules-quota-too-small"></a>Cota de regras de alerta de métrica muito pequena

O número permitido de regras de alerta de métrica por assinatura está sujeito aos [limites de cota](../service-limits.md).

Se você atingiu o limite de cota, as etapas a seguir podem ajudar a resolver o problema:
1. Tente excluir ou desabilitar as regras de alerta de métricas que não são mais usadas.

2. Alterne para o uso de regras de alerta de métrica que monitoram vários recursos. Com esse recurso, uma única regra de alerta pode monitorar vários recursos usando apenas uma regra de alerta contada em relação à cota. Para obter mais informações sobre esse recurso e os tipos de recursos com suporte, consulte [aqui](./alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor).

3. Se você precisar que o limite de cota seja aumentado, abra uma solicitação de suporte e forneça as seguintes informações:

    - ID (s) de assinatura para as quais o limite de cota precisa ser aumentado
    - Tipo de recurso para o aumento de cota: **alertas de métrica** ou **alertas de métrica (clássico)**
    - Limite de cota solicitado

## <a name="check-total-number-of-metric-alert-rules"></a>Verificar o número total de regras de alerta de métrica

Para verificar o uso atual de regras de alerta de métrica, siga as etapas abaixo.

### <a name="from-the-azure-portal"></a>No portal do Azure

1. Na página **Alertas**, clique em **Gerenciar Regras de Alerta**
2. Filtrar para a assinatura relevante usando o controle suspenso de **assinatura**
3. Certifique-se de não filtrar para um grupo de recursos, tipo de recurso ou recurso específico
4. No controle suspenso **tipo de sinal** , selecione **métricas**
5. Verifique se o controle DropDown de **status** está definido como **habilitado**
6. O número total de regras de alerta de métricas é exibido acima da lista de regras de alerta

### <a name="from-api"></a>Na API

- PowerShell - [Get-AzMetricAlertRuleV2](/powershell/module/az.monitor/get-azmetricalertrulev2)
- API REST - [Lista por assinatura](/rest/api/monitor/metricalerts/listbysubscription)
- CLI do Azure - [Lista de alerta de métricas do monitor az](/cli/azure/monitor/metrics/alert#az-monitor-metrics-alert-list)

## <a name="managing-alert-rules-using-resource-manager-templates-rest-api-powershell-or-azure-cli"></a>Gerenciando regras de alerta usando modelos do Resource Manager, API REST, PowerShell ou CLI do Azure

Se você estiver executando problemas para criar, atualizar, recuperar ou excluir alertas de métrica usando modelos do Resource Manager, a API REST, o PowerShell ou a CLI (interface de linha de comando) do Azure, as etapas a seguir podem ajudar a resolver o problema.

### <a name="resource-manager-templates"></a>Modelos do Gerenciador de Recursos

- Examine a lista de [erros comuns de implantação do Azure](../../azure-resource-manager/templates/common-deployment-errors.md) e solucione os problemas conforme apropriado
- Consulte os [exemplos de Azure Resource Manager do modelo de alertas de métrica](./alerts-metric-create-templates.md) para garantir que você está passando todos os parâmetros corretamente

### <a name="rest-api"></a>API REST

Examine o [guia da API REST](/rest/api/monitor/metricalerts/) para verificar se você está passando todos os parâmetros corretamente

### <a name="powershell"></a>PowerShell

Verifique se você está usando os cmdlets do PowerShell corretos para alertas de métrica:

- Os cmdlets do PowerShell para alertas de métrica estão disponíveis no [módulo AZ.Monitor](/powershell/module/az.monitor/)
- Certifique-se de usar os cmdlets que terminam com ' v2 ' para novos alertas de métrica (não clássicos) (por exemplo, [Add-AzMetricAlertRuleV2](/powershell/module/az.monitor/add-azmetricalertrulev2))

### <a name="azure-cli"></a>CLI do Azure

Verifique se você está usando os comandos da CLI corretos para alertas de métrica:

- Os comandos da CLI para alertas de métrica começam com `az monitor metrics alert`. Examine a [referência da CLI do Azure](/cli/azure/monitor/metrics/alert) para saber mais sobre a sintaxe.
- Você pode consultar um [exemplo que mostra como usar a CLI de alerta de métrica](./alerts-metric.md#with-azure-cli)
- Para alertar sobre uma métrica personalizada, lembre-se de prefixar o nome da métrica com o namespace de métrica relevante: NAMESPACE.METRIC

### <a name="general"></a>Geral

- Se você estiver recebendo um `Metric not found` erro:

   - Para uma métrica de plataforma: Verifique se você está usando o nome da **métrica** na [página Azure monitor métricas com suporte](./metrics-supported.md)e não o **nome de exibição da métrica**

   - Para uma métrica personalizada: Verifique se a métrica já está sendo emitida (não é possível criar uma regra de alerta em uma métrica personalizada que ainda não exista) e que você está fornecendo o namespace da métrica personalizada (consulte um exemplo de modelo do Resource Manager [aqui](./alerts-metric-create-templates.md#template-for-a-static-threshold-metric-alert-that-monitors-a-custom-metric))

- Se você estiver criando [alertas de métricas em logs](./alerts-metric-logs.md), verifique se as dependências apropriadas estão incluídas. Confira o [modelo de exemplo](./alerts-metric-logs.md#resource-template-for-metric-alerts-for-logs).

- Se você estiver criando uma regra de alerta que contenha vários critérios, observe as seguintes restrições:

   - Só é possível selecionar um valor por dimensão dentro de cada critério
   - Não é possível usar "\*" como um valor de dimensão
   - Quando as métricas que são configuradas em critérios diferentes dão suporte à mesma dimensão, um valor de dimensão configurado deve ser definido explicitamente da mesma forma para todas essas métricas (consulte um exemplo de modelo do Resource Manager [aqui](./alerts-metric-create-templates.md#template-for-a-static-threshold-metric-alert-that-monitors-multiple-criteria))


## <a name="no-permissions-to-create-metric-alert-rules"></a>Não há permissões para criar regras de alerta de métrica

Para criar uma regra de alerta de métrica, você precisará ter as seguintes permissões:

- Permissão de leitura no recurso de destino da regra de alerta
- Permissão de gravação no grupo de recursos no qual a regra de alerta é criada (se você estiver criando a regra de alerta do portal do Azure, a regra de alerta será criada por padrão no mesmo grupo de recursos em que reside o recurso de destino)
- Permissão de leitura em qualquer grupo de ações associado à regra de alerta (se aplicável)


## <a name="naming-restrictions-for-metric-alert-rules"></a>Restrições de nomenclatura para regras de alerta de métrica

Considere as seguintes restrições para nomes de regra de alerta de métrica:

- Nomes de regra de alerta de métrica não podem ser alterados (renomeados) depois de criados
- Os nomes de regra de alerta de métrica devem ser exclusivos em um grupo de recursos
- Os nomes de regra de alerta de métrica não podem conter os seguintes caracteres: * # & +:  < > ? @ % { } \ / 
- Os nomes de regra de alerta de métrica não podem terminar com um espaço ou um ponto

> [!NOTE] 
> Se o nome da regra de alerta contiver caracteres que não são alfabéticos ou numéricos (por exemplo: espaços, sinais de pontuação ou símbolos), esses caracteres poderão ser codificados por URL quando forem recuperados por determinados clientes.

## <a name="restrictions-when-using-dimensions-in-a-metric-alert-rule-with-multiple-conditions"></a>Restrições ao usar dimensões em uma regra de alerta de métrica com várias condições

Os alertas de métricas dão suporte a alertas em métricas multidimensionais, bem como suporte para definir várias condições (até 5 condições por regra de alerta).

Considere as seguintes restrições ao usar dimensões em uma regra de alerta que contenha várias condições:
- Você só pode selecionar um valor por dimensão dentro de cada condição.
- Você não pode usar a opção "selecionar todos os valores atuais e futuros" (selecione \* ).
- Quando as métricas que são configuradas em condições diferentes dão suporte à mesma dimensão, um valor de dimensão configurado deve ser definido explicitamente da mesma forma para todas essas métricas (nas condições relevantes).
Por exemplo:
    - Considere uma regra de alerta de métrica que é definida em uma conta de armazenamento e monitora duas condições:
        * Total de **transações** > 5
        * Média de **SuccessE2ELatency** > 250 MS
    - Eu gostaria de atualizar a primeira condição e monitorar apenas as transações em que a dimensão **ApiName** é igual a *"getBlob"*
    - Como as métricas de **Transações** e **SuccessE2ELatency** dão suporte a uma dimensão **ApiName** , precisarei atualizar ambas as condições e fazer com que ambas especifiquem a dimensão **ApiName** com um valor *"getBlob"* .

## <a name="setting-the-alert-rules-period-and-frequency"></a>Definindo o período e a frequência da regra de alerta

É recomendável escolher uma *granularidade de agregação (período)* maior do que a *frequência de avaliação*, para reduzir a probabilidade de falta da primeira avaliação da série temporal adicionada nos seguintes casos:
-   Regra de alerta de métrica que monitora várias dimensões – quando uma nova combinação de valor de dimensão é adicionada
-   Regra de alerta de métrica que monitora vários recursos – quando um novo recurso é adicionado ao escopo
-   Regra de alerta de métrica que monitora uma métrica que não é emitida continuamente (métrica esparsa) – quando a métrica é emitida após um período de mais de 24 horas em que ela não foi emitida

## <a name="the-dynamic-thresholds-borders-dont-seem-to-fit-the-data"></a>As bordas de limites dinâmicos não parecem se ajustar aos dados

Se o comportamento de uma métrica foi alterado recentemente, as alterações não serão necessariamente refletidas nas bordas de limite dinâmico (limites superiores e inferiores) imediatamente, pois elas são calculadas com base nos dados de métrica dos últimos 10 dias. Ao exibir as bordas de limite dinâmico para uma determinada métrica, certifique-se de examinar a tendência da métrica na última semana e não apenas por horas ou dias recentes.

## <a name="why-is-weekly-seasonality-not-detected-by-dynamic-thresholds"></a>Por que a sazonalidade semanal não é detectada por limites dinâmicos?

Para identificar sazonalidade semanalmente, o modelo de limites dinâmicos requer pelo menos três semanas de dados históricos. Quando dados históricos suficientes estiverem disponíveis, qualquer sazonalidade semanal que exista nos dados da métrica será identificada e o modelo será ajustado de acordo. 

## <a name="dynamic-thresholds-shows-a-negative-lower-bound-for-a-metric-even-though-the-metric-always-has-positive-values"></a>Os limites dinâmicos mostram um limite inferior negativo para uma métrica, embora a métrica sempre tenha valores positivos

Quando uma métrica exibe a flutuação grande, os limites dinâmicos criarão um modelo mais amplo em torno dos valores de métrica, o que pode fazer com que a borda inferior esteja abaixo de zero. Especificamente, isso pode acontecer nos seguintes casos:
1. A sensibilidade está definida como baixa 
2. Os valores medianos estão próximos de zero
3. A métrica exibe um comportamento irregular com alta variação (há picos ou DIPs nos dados)

Quando o limite inferior tem um valor negativo, isso significa que é plausível que a métrica alcance um valor zero, dado o comportamento irregular da métrica. Você pode considerar a escolha de uma maior sensibilidade ou uma *granularidade de agregação maior (período)* para tornar o modelo menos sensível, ou usando a opção *ignorar dados antes* de excluir um irregulaity recente dos dados históricos usados para criar o modelo.

## <a name="next-steps"></a>Próximas etapas

- Para obter informações gerais sobre solução de problemas de alertas e notificações, consulte [Solucionando problemas em alertas de Azure monitor](alerts-troubleshoot.md).
