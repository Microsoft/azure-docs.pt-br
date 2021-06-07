---
title: Detecção Inteligente - anomalias de falha no Application Insights | Microsoft Docs
description: Alerta para alterações incomuns na taxa de solicitações com falha para seu aplicativo Web e fornece a análise de diagnóstico. Nenhuma configuração é necessária.
ms.topic: conceptual
ms.date: 12/18/2018
ms.reviewer: yalavi
ms.openlocfilehash: 0f4de3aaba4acf86df37048134089326196e87ff
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100587548"
---
# <a name="smart-detection---failure-anomalies"></a>Detecção Inteligente - anomalias de falha
O [Application Insights](./app-insights-overview.md) alertará você automaticamente, quase em tempo real, se seu aplicativo Web experimentar um aumento anormal de solicitações com falha. Ele detecta um aumento excepcional na taxa de solicitações de HTTP ou chamadas de dependência são relatadas como falha. Nas solicitações, as solicitações com falha geralmente têm códigos de resposta de 400 ou superior. Para ajudar você na triagem e no diagnóstico do problema, uma análise das características das falhas e dados relacionados do aplicativo são fornecidos nos detalhes do alerta. Também há links para portal do Application Insights, onde você pode obter um diagnóstico mais detalhado. O recurso não precisa de qualquer configuração, pois usa algoritmos de aprendizado de máquina para prever a taxa normal de falhas.

Esse recurso funciona com qualquer aplicativo Web, hospedado na nuvem ou em seus próprios servidores, que gere dados de dependência ou de solicitação de aplicativo. Por exemplo, se você tiver uma função de trabalho que chame [TrackRequest ()](./api-custom-events-metrics.md#trackrequest) ou [TrackDependency ()](./api-custom-events-metrics.md#trackdependency).

Após a configuração do [Application Insights para seu projeto](./app-insights-overview.md), e se o aplicativo gerar certa quantidade mínima de dados, a Detecção Inteligente de anomalias de falha levará 24 horas para compreender o comportamento normal do aplicativo antes que ele seja ligado e possa enviar alertas.

Veja a seguir um exemplo de alerta:

:::image type="content" source="./media/proactive-failure-diagnostics/013.png" alt-text="Alerta de detecção inteligente de exemplo mostrando a análise de cluster em relação a falhas." lightbox="./media/proactive-failure-diagnostics/013.png":::

Os detalhes do alerta informarão:

* A taxa de falhas em comparação com o comportamento normal do aplicativo.
* Quantos usuários são afetados - então você sabe o quanto se preocupar.
* Um padrão característico associado às falhas. Neste exemplo, há um código de resposta específico, nome da solicitação (operação) e versão do aplicativo. Isso diz a você imediatamente onde começar a procurar em seu código. Outras possibilidades poderiam ser um navegador ou um sistema operacional cliente específico.
* A exceção, os rastreamentos de log e as falhas de dependência (bancos de dados ou outros componentes externos) que parecem estar associados às falhas caracterizadas.
* Vincula diretamente às pesquisas relevantes nos dados do Application Insights.

## <a name="benefits-of-smart-detection"></a>Benefícios da Detecção Inteligente
Os [alertas de métrica](../alerts/alerts-log.md) comuns mostram que pode haver um problema. Mas a Detecção Inteligente inicia o trabalho de diagnóstico para você, executando grande parte da análise que, de outra forma, você teria de fazer por conta própria. Você obtém os resultados empacotados organizadamente, o que ajuda a chegar rapidamente à raiz do problema.

## <a name="how-it-works"></a>Como ele funciona
A Detecção Inteligente monitora os dados recebidos do aplicativo, especialmente as taxas de falha. Essa regra calcula o número de solicitações para o qual o `Successful request` propriedade for false, e o número de dependência chamadas para o qual o `Successful call` propriedade é false. Para as solicitações, por padrão, `Successful request == (resultCode < 400)` (a menos que você tenha escrito o código personalizado para [filtrar](./api-filtering-sampling.md#filtering) ou gerar suas próprias chamadas [TrackRequest](./api-custom-events-metrics.md#trackrequest)). 

O desempenho do seu aplicativo tem um padrão típico de comportamento. Algumas solicitações ou chamadas de dependência serão mais propensas a falhas do que outras; a taxa geral de falha poderá aumentar à medida que a carga crescer. A Detecção Inteligente usa aprendizado de máquina para encontrar essas anomalias.

Conforme os dados entram no Application Insights vindos do aplicativo Web, a Detecção Inteligente compara o comportamento atual com os padrões vistos nos últimos dias. Se for observado um aumento anormal na taxa de falha em comparação com o desempenho anterior, uma análise será disparada.

Quando um alerta é gerado, o serviço realiza uma análise de cluster na solicitação com falha a fim de tentar identificar um padrão de valores que caracterize as falhas. 

No exemplo acima, a análise descobriu que a maioria das falhas são sobre um código de resultado específico, nome de solicitação, host da URL do servidor e instância de função. 

Quando seu serviço conta com essas chamadas, o analisador procura uma exceção e uma falha de dependência associadas às solicitações identificadas no cluster, junto com um exemplo de qualquer log de rastreamento associado a essas solicitações.

A análise resultante é enviada como um alerta, a menos que você tenha configurado para isso não acontecer.

Assim como acontece com os [alertas definidos manualmente](../alerts/alerts-log.md), você pode inspecionar o estado do alerta acionado, que poderá ser resolvido se o problema for corrigido. Configure as regras de alerta na página Alertas do recurso do Application Insights. Mas, ao contrário de outros alertas, você não precisa configurar a Detecção Inteligente. Se quiser, você pode desabilitá-lo ou alterar o endereço de email de destino.

### <a name="alert-logic-details"></a>Detalhes da lógica de alerta

Os alertas são acionados por nosso algoritmo patenteado de machine learning, portanto, não podemos compartilhar os detalhes exatos da implementação. Dito isto, sabemos que muitas vezes é preciso saber mais sobre a forma como a lógica subjacente funciona. Os principais fatores avaliados para determinar se um alerta deve ser acionado são: 

* Análise da porcentagem da falha de solicitações/dependências em uma janela de tempo com acumulação de 20 minutos.
* Uma comparação da porcentagem de falha dos últimos 20 minutos em relação à taxa nos últimos 40 minutos e nos últimos sete dias e desvios significativos que excedam X vezes esse desvio padrão.
* O uso de um limite adaptável para a porcentagem de falha mínima, que varia com base no volume de solicitações/dependências do aplicativo.
* Há uma lógica que poderá resolver automaticamente a condição do monitor do alerta acionado, se o problema não for mais detectado por 8-24 horas.
  Observação: no design atual. uma notificação ou ação não será enviada quando um alerta de detecção inteligente for resolvido. Você pode verificar se um alerta de detecção inteligente foi resolvido no portal do Azure.

## <a name="configure-alerts"></a>Configurar alertas

Você pode desabilitar a regra de alerta de Detecção Inteligente no portal ou usando o Azure Resource Manager ([consulte o exemplo de modelo](./proactive-arm-config.md)).

Essa regra de alerta é criada com um [grupo de ações](../alerts/action-groups.md) associado, chamado "Detecção Inteligente do Application Insights", que contém ações de email e webhook e pode ser estendido para disparar ações adicionais quando o alerta for acionado.

> [!NOTE]
> As notificações por email enviadas por essa regra de alerta agora, por padrão, são enviadas aos usuários associados a funções de leitor de monitoramento e colaborador de monitoramento da assinatura. Saiba mais sobre esse assunto [aqui](./proactive-email-notification.md).
> As notificações enviadas por essa regra de alerta seguem o [esquema de alerta comum](../alerts/alerts-common-schema.md).
>

Abra a página Alertas. As regras de alerta de anomalias de falha são incluídas com todos os alertas que você definiu manualmente e é possível ver se está em estado de alerta no momento.

:::image type="content" source="./media/proactive-failure-diagnostics/021.png" alt-text="Na página Application Insights recurso, clique no bloco alertas e, em seguida, gerenciar regras de alerta." lightbox="./media/proactive-failure-diagnostics/021.png":::

Clique no alerta para configurá-lo.

:::image type="content" source="./media/proactive-failure-diagnostics/032.png" alt-text="Tela de configuração da regra." lightbox="./media/proactive-failure-diagnostics/032.png":::

Você pode desabilitar ou excluir uma regra de alerta de anomalias de falha, mas não pode criar outra no mesmo recurso do Application Insights.

## <a name="example-of-failure-anomalies-alert-webhook-payload"></a>Exemplo de conteúdo do webhook do alerta de anomalias de falha

```json
{
    "properties": {
        "essentials": {
            "severity": "Sev3",
            "signalType": "Log",
            "alertState": "New",
            "monitorCondition": "Resolved",
            "monitorService": "Smart Detector",
            "targetResource": "/subscriptions/4f9b81be-fa32-4f96-aeb3-fc5c3f678df9/resourcegroups/test-group/providers/microsoft.insights/components/test-rule",
            "targetResourceName": "test-rule",
            "targetResourceGroup": "test-group",
            "targetResourceType": "microsoft.insights/components",
            "sourceCreatedId": "1a0a5b6436a9b2a13377f5c89a3477855276f8208982e0f167697a2b45fcbb3e",
            "alertRule": "/subscriptions/4f9b81be-fa32-4f96-aeb3-fc5c3f678df9/resourcegroups/test-group/providers/microsoft.alertsmanagement/smartdetectoralertrules/failure anomalies - test-rule",
            "startDateTime": "2019-10-30T17:52:32.5802978Z",
            "lastModifiedDateTime": "2019-10-30T18:25:23.1072443Z",
            "monitorConditionResolvedDateTime": "2019-10-30T18:25:26.4440603Z",
            "lastModifiedUserName": "System",
            "actionStatus": {
                "isSuppressed": false
            },
            "description": "Failure Anomalies notifies you of an unusual rise in the rate of failed HTTP requests or dependency calls."
        },
        "context": {
            "DetectionSummary": "An abnormal rise in failed request rate",
            "FormattedOccurenceTime": "2019-10-30T17:50:00Z",
            "DetectedFailureRate": "50.0% (200/400 requests)",
            "NormalFailureRate": "0.0% (over the last 30 minutes)",
            "FailureRateChart": [
                [
                    "2019-10-30T05:20:00Z",
                    0
                ],
                [
                    "2019-10-30T05:40:00Z",
                    100
                ],
                [
                    "2019-10-30T06:00:00Z",
                    0
                ],
                [
                    "2019-10-30T06:20:00Z",
                    0
                ],
                [
                    "2019-10-30T06:40:00Z",
                    100
                ],
                [
                    "2019-10-30T07:00:00Z",
                    0
                ],
                [
                    "2019-10-30T07:20:00Z",
                    0
                ],
                [
                    "2019-10-30T07:40:00Z",
                    100
                ],
                [
                    "2019-10-30T08:00:00Z",
                    0
                ],
                [
                    "2019-10-30T08:20:00Z",
                    0
                ],
                [
                    "2019-10-30T08:40:00Z",
                    100
                ],
                [
                    "2019-10-30T17:00:00Z",
                    0
                ],
                [
                    "2019-10-30T17:20:00Z",
                    0
                ],
                [
                    "2019-10-30T09:00:00Z",
                    0
                ],
                [
                    "2019-10-30T09:20:00Z",
                    0
                ],
                [
                    "2019-10-30T09:40:00Z",
                    100
                ],
                [
                    "2019-10-30T10:00:00Z",
                    0
                ],
                [
                    "2019-10-30T10:20:00Z",
                    0
                ],
                [
                    "2019-10-30T10:40:00Z",
                    100
                ],
                [
                    "2019-10-30T11:00:00Z",
                    0
                ],
                [
                    "2019-10-30T11:20:00Z",
                    0
                ],
                [
                    "2019-10-30T11:40:00Z",
                    100
                ],
                [
                    "2019-10-30T12:00:00Z",
                    0
                ],
                [
                    "2019-10-30T12:20:00Z",
                    0
                ],
                [
                    "2019-10-30T12:40:00Z",
                    100
                ],
                [
                    "2019-10-30T13:00:00Z",
                    0
                ],
                [
                    "2019-10-30T13:20:00Z",
                    0
                ],
                [
                    "2019-10-30T13:40:00Z",
                    100
                ],
                [
                    "2019-10-30T14:00:00Z",
                    0
                ],
                [
                    "2019-10-30T14:20:00Z",
                    0
                ],
                [
                    "2019-10-30T14:40:00Z",
                    100
                ],
                [
                    "2019-10-30T15:00:00Z",
                    0
                ],
                [
                    "2019-10-30T15:20:00Z",
                    0
                ],
                [
                    "2019-10-30T15:40:00Z",
                    100
                ],
                [
                    "2019-10-30T16:00:00Z",
                    0
                ],
                [
                    "2019-10-30T16:20:00Z",
                    0
                ],
                [
                    "2019-10-30T16:40:00Z",
                    100
                ],
                [
                    "2019-10-30T17:30:00Z",
                    50
                ]
            ],
            "ArmSystemEventsRequest": "/subscriptions/4f9b81be-fa32-4f96-aeb3-fc5c3f678df9/resourceGroups/test-group/providers/microsoft.insights/components/test-rule/query?query=%0d%0a++++++++++++++++systemEvents%0d%0a++++++++++++++++%7c+where+timestamp+%3e%3d+datetime(%272019-10-30T17%3a20%3a00.0000000Z%27)+%0d%0a++++++++++++++++%7c+where+itemType+%3d%3d+%27systemEvent%27+and+name+%3d%3d+%27ProactiveDetectionInsight%27+%0d%0a++++++++++++++++%7c+where+dimensions.InsightType+in+(%275%27%2c+%277%27)+%0d%0a++++++++++++++++%7c+where+dimensions.InsightDocumentId+%3d%3d+%27718fb0c3-425b-4185-be33-4311dfb4deeb%27+%0d%0a++++++++++++++++%7c+project+dimensions.InsightOneClassTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightExceptionCorrelationTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightDependencyCorrelationTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightRequestCorrelationTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightTraceCorrelationTable%0d%0a++++++++++++&api-version=2018-04-20",
            "LinksTable": [
                {
                    "Link": "<a href=\"https://portal.azure.com/#blade/AppInsightsExtension/ProactiveDetectionFeedBlade/ComponentId/{\"SubscriptionId\":\"4f9b81be-fa32-4f96-aeb3-fc5c3f678df9\",\"ResourceGroup\":\"test-group\",\"Name\":\"test-rule\"}/SelectedItemGroup/718fb0c3-425b-4185-be33-4311dfb4deeb/SelectedItemTime/2019-10-30T17:50:00Z/InsightType/5\" target=\"_blank\">View full details in Application Insights</a>"
                }
            ],
            "SmartDetectorId": "FailureAnomaliesDetector",
            "SmartDetectorName": "Failure Anomalies",
            "AnalysisTimestamp": "2019-10-30T17:52:32.5802978Z"
        },
        "egressConfig": {
            "displayConfig": [
                {
                    "rootJsonNode": null,
                    "sectionName": null,
                    "displayControls": [
                        {
                            "property": "DetectionSummary",
                            "displayName": "What was detected?",
                            "type": "Text",
                            "isOptional": false,
                            "isPropertySerialized": false
                        },
                        {
                            "property": "FormattedOccurenceTime",
                            "displayName": "When did this occur?",
                            "type": "Text",
                            "isOptional": false,
                            "isPropertySerialized": false
                        },
                        {
                            "property": "DetectedFailureRate",
                            "displayName": "Detected failure rate",
                            "type": "Text",
                            "isOptional": false,
                            "isPropertySerialized": false
                        },
                        {
                            "property": "NormalFailureRate",
                            "displayName": "Normal failure rate",
                            "type": "Text",
                            "isOptional": false,
                            "isPropertySerialized": false
                        },
                        {
                            "chartType": "Line",
                            "xAxisType": "Date",
                            "yAxisType": "Percentage",
                            "xAxisName": "",
                            "yAxisName": "",
                            "property": "FailureRateChart",
                            "displayName": "Failure rate over last 12 hours",
                            "type": "Chart",
                            "isOptional": false,
                            "isPropertySerialized": false
                        },
                        {
                            "defaultLoad": true,
                            "displayConfig": [
                                {
                                    "rootJsonNode": null,
                                    "sectionName": null,
                                    "displayControls": [
                                        {
                                            "showHeader": false,
                                            "columns": [
                                                {
                                                    "property": "Name",
                                                    "displayName": "Name"
                                                },
                                                {
                                                    "property": "Value",
                                                    "displayName": "Value"
                                                }
                                            ],
                                            "property": "tables[0].rows[0][0]",
                                            "displayName": "All of the failed requests had these characteristics:",
                                            "type": "Table",
                                            "isOptional": false,
                                            "isPropertySerialized": true
                                        }
                                    ]
                                }
                            ],
                            "property": "ArmSystemEventsRequest",
                            "displayName": "",
                            "type": "ARMRequest",
                            "isOptional": false,
                            "isPropertySerialized": false
                        },
                        {
                            "showHeader": false,
                            "columns": [
                                {
                                    "property": "Link",
                                    "displayName": "Link"
                                }
                            ],
                            "property": "LinksTable",
                            "displayName": "Links",
                            "type": "Table",
                            "isOptional": false,
                            "isPropertySerialized": false
                        }
                    ]
                }
            ]
        }
    },
    "id": "/subscriptions/4f9b81be-fa32-4f96-aeb3-fc5c3f678df9/resourcegroups/test-group/providers/microsoft.insights/components/test-rule/providers/Microsoft.AlertsManagement/alerts/7daf8739-ca8a-4562-b69a-ff28db4ba0a5",
    "type": "Microsoft.AlertsManagement/alerts",
    "name": "Failure Anomalies - test-rule"
}
```

## <a name="triage-and-diagnose-an-alert"></a>Triagem e diagnóstico de um alerta

Um alerta indica a detecção de um aumento anormal na taxa de solicitações com falha. É provável que haja algum problema com seu aplicativo ou seu ambiente.

Para investigar melhor, clique na opção “Exibir os detalhes completos no Application Insights”. Os links dessa página levarão você diretamente a uma [página de pesquisa](./diagnostic-search.md) filtrada para mostrar solicitações, exceções, dependências ou rastreamentos relevantes. 

Você também pode abrir o [portal do Azure](https://portal.azure.com), navegar até o recurso Application Insights do seu aplicativo e abrir a página Falhas.

Clicar em “Diagnosticar falhas” ajudará você a obter mais detalhes e a resolver o problema.

:::image type="content" source="./media/proactive-failure-diagnostics/051.png" alt-text="Pesquisa de diagnóstico." lightbox="./media/proactive-failure-diagnostics/051.png#lightbox":::

Você pode decidir a urgência do problema com base na porcentagem de solicitações e no número de usuários afetados. No exemplo anterior, a taxa de falha de 78,5% é comparada a uma taxa normal de 2,2% e indica que está acontecendo alguma coisa errada. Por outro lado, somente 46 usuários foram afetados. Se fosse seu aplicativo, você poderia avaliar a gravidade.

Em muitos casos, você poderá diagnosticar o problema rapidamente pelo nome da solicitação, exceção, falha de dependência e pelos dados de rastreamento fornecidos.

Neste exemplo, houve uma exceção do banco de dados SQL devido ao limite de solicitação ser atingido.

:::image type="content" source="./media/proactive-failure-diagnostics/052.png" alt-text="Detalhes da solicitação com falha." lightbox="./media/proactive-failure-diagnostics/052.png":::

## <a name="review-recent-alerts"></a>Exame dos alertas recentes

Clique em **Alertas** na página de recursos do Application Insights para obter os alertas acionados mais recentes:

:::image type="content" source="./media/proactive-failure-diagnostics/070.png" alt-text="Resumo de alertas." lightbox="./media/proactive-failure-diagnostics/070.png":::

## <a name="whats-the-difference-"></a>Qual é a diferença...
A Detecção Inteligente de anomalias de falha complementa outros recursos distintos, mas parecidos, do Application Insights.

* os [alertas de métrica](../alerts/alerts-log.md) são definidos por você e podem monitorar uma ampla gama de métricas, como ocupação de CPU, taxas de solicitação, tempos de carregamento de página e assim por diante. Você pode usá-los para receber um aviso, por exemplo, se precisar adicionar mais recursos. Por outro lado, a Detecção Inteligente de anomalias de falha cobre um pequeno grupo de métricas essenciais (atualmente, apenas a taxa de solicitações com falha), projetadas para notificar você quase em tempo real quando a taxa de solicitações com falha de seu aplicativo Web aumenta, em comparação com o comportamento normal do aplicativo Web. Diferentemente dos alertas de métrica, a Detecção Inteligente define e atualiza automaticamente os limites nas alterações de resposta do comportamento. A Detecção Inteligente também inicia o trabalho de diagnóstico para você, economizando tempo na resolução de problemas.

* A [Detecção Inteligente de anomalias de desempenho](proactive-performance-diagnostics.md) também usa a inteligência de máquina para descobrir padrões incomuns em suas métricas, sem qualquer necessidade de configuração da sua parte. Mas diferentemente da Detecção Inteligente de anomalias de falha, a finalidade da Detecção Inteligente de anomalias de desempenho é encontrar segmentos de sua coleção de uso com mau serviço, por exemplo, por páginas específicas em um tipo específico de navegador. A análise é realizada diariamente e, se qualquer resultado for encontrado, provavelmente será muito menos urgente do que um alerta. Por outro lado, a análise de anomalias de falha é feita continuamente com os dados recebidos do aplicativo. Você receberá uma notificação em questão de minutos se as taxas de falha do servidor forem maiores do que o esperado.

## <a name="if-you-receive-a-smart-detection-alert"></a>Se você receber um alerta de Detecção Inteligente
*Por que eu recebei esse alerta?*

* Foi detectado um aumento anormal de solicitações com falha em comparação à linha de base normal do período anterior. Após a análise das falhas e dos dados do aplicativo associados, acreditamos que há um problema que você deve examinar.

*A notificação significa que, definitivamente, tenho um problema?*

* Tentamos alertar sobre a interrupção do aplicativo, ou sobre sua degradação, mas só você poderá compreender totalmente a semântica e o impacto no aplicativo ou nos usuários.

*Então, você está vendo os dados do meu aplicativo?*

* Não. O serviço é totalmente automático. Somente você recebe as notificações. Os dados são [privados](./data-retention-privacy.md).

*É necessário assinar este alerta?*

* Não. Todos os aplicativos que enviam dados de solicitação têm a regra de alerta de Detecção Inteligente.

*Posso cancelar a assinatura ou ter as notificações enviadas para meus colegas em vez disso?*

* Sim, em Regras de alerta, clique na regra de Detecção Inteligente para configurá-la. Você pode desabilitar o alerta ou alterar os destinatários do alerta.

*Perdi o email. Onde posso encontrar as notificações no portal?*

* Nos Logs de atividade. No Azure, abra o recurso Application Insights de seu aplicativo e selecione Logs de atividade.

*Alguns dos alertas são sobre problemas conhecidos e não quero recebê-los.*

* Você pode usar o recurso de supressão de [regras de ação de alerta](../alerts/alerts-action-rules.md).

## <a name="next-steps"></a>Próximas etapas
Estas ferramentas de diagnóstico ajudam você a inspecionar os dados do seu aplicativo:

* [Metrics explorer](../essentials/metrics-charts.md)
* [Gerenciador de pesquisa](./diagnostic-search.md)
* [Analytics - linguagem de consulta poderosa](../logs/log-analytics-tutorial.md)

As detecções inteligentes são automáticas. Mas talvez você queira configurar alguns outros alertas?

* [Alertas de métrica configurados manualmente](../alerts/alerts-log.md)
* [Testes de disponibilidade na Web](./monitor-web-app-availability.md)