---
title: Monitorar e solucionar problemas de desconexões com o Hub IoT
description: Aprenda a monitorar e solucionar erros comuns com a conectividade de dispositivos do Hub IoT
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/06/2020
ms.author: jlian
ms.custom:
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
- 'Role: Technical Support'
- fasttrack-edit
- iot
ms.openlocfilehash: 8bd20e3c7207c75e87a2132fca89906885de2676
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100579262"
---
# <a name="monitor-diagnose-and-troubleshoot-disconnects-with-azure-iot-hub"></a>Monitorar, diagnosticar e solucionar problemas de desconexões com o Hub IoT

Problemas de conectividade para dispositivos IoT podem ser difíceis de solucionar porque há muitos possíveis pontos de falha. A lógica do aplicativo, as redes físicas, os protocolos, o hardware, o Hub IoT e outros serviços de nuvem podem causar problemas. Poder detectar e identificar a origem de um problema é crucial. No entanto, uma solução de IoT em escala pode ter milhares de dispositivos, portanto, não é viável verificar dispositivos individuais manualmente. O Hub IoT integra-se com dois serviços do Azure para ajudá-lo a:

* **Azure monitor** Para ajudá-lo a detectar, diagnosticar e solucionar esses problemas em escala, use os recursos de monitoramento que o Hub IoT fornece por meio de Azure Monitor. Isso inclui a configuração de alertas para disparar notificações e ações quando as desconexões ocorrem e configurar logs que podem ser usados para descobrir as condições que causaram desconexões.

* **Grade de eventos do Azure** Para a infraestrutura crítica e desconexões por dispositivo, use a grade de eventos do Azure para assinar eventos de conexão e desconexão do dispositivo emitidos pelo Hub IoT.

Em ambos os casos, esses recursos são limitados ao que o Hub IoT pode observar, por isso também recomendamos que você siga as práticas recomendadas de monitoramento para seus dispositivos e outros serviços do Azure.

## <a name="event-grid-vs-azure-monitor"></a>Grade de eventos versus Azure Monitor

A grade de eventos fornece uma solução de monitoramento de baixa latência por dispositivo que você pode usar para rastrear conexões de dispositivo para dispositivos e infraestruturas críticos. Azure Monitor fornece uma métrica, *dispositivos conectados*, que você pode usar para monitorar o número de dispositivos conectados ao seu hub IOT e disparar um alerta quando esse número cair abaixo de um limite estático.

Considere o seguinte ao decidir se deseja usar a grade de eventos ou Azure Monitor para um cenário específico:

* Latência de alerta: os eventos de conexão do Hub IoT são entregues muito mais rapidamente por meio da grade de eventos. Isso torna a grade de eventos uma opção melhor para cenários em que a notificação rápida é desejável.

* Notificações por dispositivo: a grade de eventos fornece a capacidade de controlar conexões e desconexões para dispositivos individuais. Isso torna a grade de eventos uma opção melhor para cenários em que você precisa monitorar as conexões para dispositivos críticos.

* Configuração leve: Azure Monitor alertas de métrica fornecem uma experiência de configuração leve que não exige a integração com outros serviços para fornecer notificações por email, SMS, voz e outras notificações.  Com a grade de eventos, você precisa integrar com outros serviços do Azure para entregar notificações. Ambos os serviços podem se integrar a outros serviços para disparar ações mais complexas.

Devido a seus recursos de baixa latência por dispositivo, para ambientes de produção, é altamente recomendável usar a grade de eventos para monitorar conexões. É claro que a escolha não é exclusiva, você pode usar os alertas de métrica Azure Monitor e a grade de eventos. Independentemente de sua escolha para o rastreamento de desconexões, você provavelmente usará Azure Monitor logs de recursos para ajudar a solucionar os motivos de desconexões de dispositivo inesperadas. As seções a seguir discutem cada uma dessas opções com mais detalhes.

## <a name="event-grid-monitor-device-connect-and-disconnect-events"></a>Grade de eventos: monitorar eventos de conexão e desconexão do dispositivo

Para monitorar eventos de conexão e desconexão do dispositivo em produção, é recomendável inscrever-se nos [eventos **DeviceConnected** e **DeviceDisconnected**](iot-hub-event-grid.md#event-types) na grade de eventos para disparar alertas e monitorar o estado da conexão do dispositivo. A grade de eventos fornece uma latência de eventos muito menor do que Azure Monitor, e você pode monitorar por dispositivo, em vez de para o número total de dispositivos conectados. Esses fatores tornam a grade de eventos o método preferencial para monitorar dispositivos críticos e a infraestrutura.

Quando você usa a grade de eventos para monitorar ou disparar alertas em desconexões de dispositivo, não se esqueça de criar uma maneira de filtrar as desconexões periódicas devido à renovação de token SAS em dispositivos que usam os SDKs do IoT do Azure. Para saber mais, confira [comportamento de desconexão de dispositivo MQTT com SDKs de IOT do Azure](#mqtt-device-disconnect-behavior-with-azure-iot-sdks).

Explore os tópicos a seguir para saber mais sobre como monitorar eventos de conexão de dispositivo com a grade de eventos:

* Para obter uma visão geral do uso da grade de eventos com o Hub IoT, consulte [reagir aos eventos do Hub IOT com a grade de eventos](iot-hub-event-grid.md). Preste atenção especial à seção [limitações para dispositivo conectado e eventos desconectados do dispositivo](iot-hub-event-grid.md#limitations-for-device-connected-and-device-disconnected-events) .

* Para obter um tutorial sobre como ordenar eventos de conexão do dispositivo, consulte [solicitar eventos de conexão do dispositivo do Hub IOT do Azure usando Azure Cosmos DB](iot-hub-how-to-order-connection-state-events.md).

* Para obter um tutorial sobre como enviar notificações por email, consulte [enviar notificações por email sobre eventos do Hub IOT do Azure usando a grade de eventos e aplicativos lógicos](../event-grid/publish-iot-hub-events-to-logic-apps.md) na documentação da grade de eventos.

## <a name="azure-monitor-route-connection-events-to-logs"></a>Azure Monitor: rotear eventos de conexão para logs

O Hub IoT emite continuamente logs de recursos para várias categorias de operações. No entanto, para coletar esses dados de log, você precisa criar uma configuração de diagnóstico para circulá-lo para um destino no qual ele pode ser analisado ou arquivado. Um desses destinos é Azure Monitor logs por meio de um espaço de trabalho Log Analytics ([consulte preços](https://azure.microsoft.com/pricing/details/log-analytics/)), onde você pode analisar os dados usando consultas Kusto.

A categoria de [conexões de logs de recursos](monitor-iot-hub-reference.md#connections) do Hub IOT emite operações e erros que têm a ver com conexões de dispositivo. A captura de tela a seguir mostra uma configuração de diagnóstico para rotear esses logs para um espaço de trabalho Log Analytics:

:::image type="content" source="media/iot-hub-troubleshoot-connectivity/create-diagnostic-setting.png" alt-text="Configuração recomendada para enviar logs de conectividade para Log Analytics espaço de trabalho.":::

É recomendável criar uma configuração de diagnóstico o mais cedo possível depois de criar o Hub IoT, porque, embora o Hub IoT sempre emita logs de recursos, eles não são coletados pelo Azure Monitor até que você os encaminhe para um destino.

Para saber mais sobre os logs de roteamento para um destino, consulte [coleta e roteamento](monitor-iot-hub.md#collection-and-routing). Para obter instruções detalhadas sobre como criar uma configuração de diagnóstico, consulte o [tutorial usar métricas e logs](tutorial-use-metrics-and-diags.md).

## <a name="azure-monitor-set-up-metric-alerts-for-device-disconnect-at-scale"></a>Azure Monitor: configurar alertas de métrica para desconexão do dispositivo em escala

Você pode configurar alertas com base nas métricas de plataforma emitidas pelo Hub IoT. Com os alertas de métricas, você pode notificar as pessoas de que uma condição de interesse ocorreu e também disparar ações que podem responder a essa condição automaticamente.

A métrica [*dispositivos conectados (visualização)*](monitor-iot-hub-reference.md#device-metrics) informa quantos dispositivos estão conectados ao Hub IOT. Você pode criar alertas para disparar se essa métrica cair abaixo de um valor limite:

:::image type="content" source="media/iot-hub-troubleshoot-connectivity/configure-alert-logic.png" alt-text="Configurações de lógica de alerta para métrica de dispositivos conectados.":::

Você pode usar as regras de alerta de métrica para monitorar anomalias de desconexão de dispositivo em escala. Ou seja, quando um número significativo de dispositivos é desconectado inesperadamente. Quando tal ocorrência é detectada, você pode examinar os logs para ajudar a solucionar o problema. Para monitorar desconexões por dispositivo e desconexões para dispositivos críticos; no entanto, você deve usar a grade de eventos. A grade de eventos também fornece uma experiência mais em tempo real do que as métricas do Azure.

Para saber mais sobre alertas com o Hub IoT, confira [alertas em monitorar Hub IOT](monitor-iot-hub.md#alerts). Para obter um passo a passo de criação de alertas no Hub IoT, consulte o [tutorial usar métricas e logs](tutorial-use-metrics-and-diags.md). Para obter uma visão geral mais detalhada dos alertas, consulte [visão geral dos alertas no Microsoft Azure](../azure-monitor/alerts/alerts-overview.md) na documentação do Azure monitor.

## <a name="azure-monitor-use-logs-to-resolve-connectivity-errors"></a>Azure Monitor: usar logs para resolver erros de conectividade

Ao detectar desconexões de dispositivo, seja com Azure Monitor alertas de métrica ou com a grade de eventos, você pode usar logs para ajudar a solucionar o motivo. Esta seção descreve como procurar problemas comuns nos logs de Azure Monitor. As etapas a seguir pressupõem que você já criou uma [configuração de diagnóstico](#azure-monitor-route-connection-events-to-logs) para enviar logs de conexões do Hub IOT a um espaço de trabalho log Analytics.

Depois de criar uma configuração de diagnóstico para rotear logs de recursos do Hub IoT para Azure Monitor logs, siga estas etapas para exibir os logs no portal do Azure.

1. Navegue até o Hub IoT em [portal do Azure](https://portal.azure.com).

1. Em **monitoramento** no painel esquerdo do Hub IOT, selecione **logs**.

1. Para isolar os logs de erro de conectividade do Hub IoT, insira a seguinte consulta no editor de consultas e, em seguida, selecione **executar**:

    ```kusto
    AzureDiagnostics
    | where ( ResourceType == "IOTHUBS" and Category == "Connections" and Level == "Error")
    ```

1. Se houver resultados, procure `OperationName`, `ResultType` (código de erro), e `ResultDescription` (mensagem de erro) para obter mais detalhes sobre o erro.

   ![Exemplo de log de erros](./media/iot-hub-troubleshoot-connectivity/diag-logs.png)

Depois de identificar o erro, siga os guias de resolução do problema para obter ajuda com os erros mais comuns:

* [400027 ConnectionForcefullyClosedOnNewConnection](iot-hub-troubleshoot-error-400027-connectionforcefullyclosedonnewconnection.md)

* [DeviceConnectionClosedRemotely 404104](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md)

* [IoTHubUnauthorized 401003](iot-hub-troubleshoot-error-401003-iothubunauthorized.md)

* [LinkCreationConflict 409002](iot-hub-troubleshoot-error-409002-linkcreationconflict.md)

* [500001 ServerError](iot-hub-troubleshoot-error-500xxx-internal-errors.md)

* [500008 GenericTimeout](iot-hub-troubleshoot-error-500xxx-internal-errors.md)

## <a name="mqtt-device-disconnect-behavior-with-azure-iot-sdks"></a>Comportamento de desconexão de dispositivo MQTT com SDKs de IoT do Azure

Os SDKs do dispositivo IoT do Azure desconectam do Hub IoT e, em seguida, se reconectam quando renovam tokens SAS no protocolo MQTT (e MQTT sobre WebSockets). Nos logs, isso aparece como um dispositivo informativo desconectado e eventos de conexão às vezes acompanhados por eventos de erro.

Por padrão, o tempo de vida do token é de 60 minutos para todos os SDKs; no entanto, ele pode ser alterado por desenvolvedores em alguns dos SDKs. A tabela a seguir resume a vida útil do token, a renovação de token e o comportamento de renovação de token para cada um dos SDKs:

| . | Vida útil do token | Renovação de token | Comportamento de renovação |
|-----|----------|---------------------|---------|
| .NET | 60 minutos, configurável | 85% de vida útil, configurável | O SDK se conecta e se desconecta no tempo de vida do token, além de um período de carência de 10 minutos. Eventos informativos e erros gerados em logs. |
| Java | 60 minutos, configurável | 85% do ciclo de vida, não configurável | O SDK se conecta e se desconecta no tempo de vida do token, além de um período de carência de 10 minutos. Eventos informativos e erros gerados em logs. |
| Node.js | 60 minutos, configurável | configurável | O SDK se conecta e se desconecta na renovação do token. Somente eventos informativos são gerados em logs. |
| Python | 60 minutos, não configurável | -- | O SDK se conecta e se desconecta no tempo de vida do token. |

As capturas de tela a seguir mostram o comportamento de renovação de token em logs de Azure Monitor para diferentes SDKs. A vida útil do token e o limite de renovação foram alterados de seus padrões, conforme observado.

* SDK do dispositivo .NET com uma duração de token de 1200 segundo (20 minutos) e renovação definida como acontecer às 90% do tempo de vida. desconexões acontecem a cada 30 minutos:

    :::image type="content" source="media/iot-hub-troubleshoot-connectivity/net-mqtt.png" alt-text="Comportamento de erro para renovação de token sobre MQTT em logs de Azure Monitor com o SDK do .NET.":::

* SDK do Java com uma duração de token de 300 segundo (5 minutos) e o padrão 85% da renovação do ciclo de vida. Desconexões acontecem a cada 15 minutos:

    :::image type="content" source="media/iot-hub-troubleshoot-connectivity/java-mqtt.png" alt-text="Comportamento de erro para renovação de token sobre MQTT em logs de Azure Monitor com o SDK do Java.":::

* O node SDK com uma duração de token de 300 segundos (5 minutos) e a renovação de token definida como acontecem em 3 minutos. Desconexões acontecem na renovação do token. Além disso, não há erros, somente eventos de conexão/desconexão informativos são emitidos:

    :::image type="content" source="media/iot-hub-troubleshoot-connectivity/node-mqtt.png" alt-text="Comportamento de erro para renovação de token sobre MQTT em logs de Azure Monitor com o node SDK.":::

A consulta a seguir foi usada para coletar os resultados. A consulta extrai o nome do SDK e a versão do recipiente de propriedades; para saber mais, confira [versão do SDK em logs do Hub IOT](monitor-iot-hub.md#sdk-version-in-iot-hub-logs).

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
| where Category == "Connections"
| extend parsed_json = parse_json(properties_s)
| extend SDKVersion = tostring(parsed_json.sdkVersion) , DeviceId = tostring(parsed_json.deviceId) , Protocol =  tostring(parsed_json.protocol)
| distinct TimeGenerated, OperationName, Level, ResultType, ResultDescription, DeviceId, Protocol, SDKVersion

```

Como desenvolvedor ou operador de soluções de IoT, você precisa estar ciente desse comportamento para interpretar eventos de conexão/desconexão e erros relacionados em logs. Se você quiser alterar a vida útil do token ou o comportamento de renovação para dispositivos, verifique se o dispositivo implementa uma configuração de dispositivo de troca ou um método de dispositivo que torna isso possível.

Se você estiver monitorando conexões de dispositivo com o Hub de eventos, certifique-se de criar uma maneira de filtrar as desconexões periódicas devido à renovação de token SAS; por exemplo, não disparar ações com base em desconexões, desde que o evento de desconexão seja seguido por um evento de conexão dentro de um determinado período de tempo.

> [!NOTE]
> O Hub IoT oferece suporte apenas a uma conexão MQTT ativa por dispositivo. Qualquer nova conexão MQTT em nome da mesma ID de dispositivo faz com que o Hub IoT descarte a conexão existente.
>
> 400027 ConnectionForcefullyClosedOnNewConnection serão registrados nos logs do Hub IoT

## <a name="i-tried-the-steps-but-they-didnt-work"></a>Tentei as etapas, mas elas não funcionaram

Se as etapas anteriores não ajudarem, tente:

* Se você tiver acesso aos dispositivos problemáticos, seja física ou remotamente (como o SSH), siga o [guia de solução de problemas do lado do dispositivo](https://github.com/Azure/azure-iot-sdk-node/wiki/Troubleshooting-Guide-Devices) para continuar com a solução de problemas.

* Verifique se os seus dispositivos estão **Habilitados** no portal do Azure> seu hub IoT> dispositivos IoT.

* Se o dispositivo usar o protocolo MQTT, verifique se a porta 8883 está aberta. Para saber mais, veja [Conectar-se ao Hub IoT (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

* Obtenha ajuda na [Página de P e R da Microsoft para o Hub IoT](/answers/topics/azure-iot-hub.html), no [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-iot-hub) ou no [Suporte do Azure](https://azure.microsoft.com/support/options/).

Para ajudar a melhorar a documentação para todos, deixe um comentário na seção de comentários abaixo, caso este guia não tenha ajudado.

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre como resolver problemas transitórios, consulte [Tratamento de falhas transitórias](/azure/architecture/best-practices/transient-faults).

* Para saber mais sobre o SDK do IoT do Azure e gerenciar repetições, consulte [Como gerenciar a conectividade e mensagens confiáveis usando SDKs de dispositivo do Hub IoT do Azure](iot-hub-reliability-features-in-sdks.md#connection-and-retry).