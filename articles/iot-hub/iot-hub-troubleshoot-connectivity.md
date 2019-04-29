---
title: Diagnosticar e solucionar problemas de desconexões com o Hub do Azure IoT
description: Aprenda a diagnosticar e solucionar erros comuns com a conectividade de dispositivos do Azure IoT Hub
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/19/2018
ms.author: jlian
ms.openlocfilehash: a107689796c58b17c445e7a9cf7c6f0402ef6005
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61440109"
---
# <a name="detect-and-troubleshoot-disconnects-with-azure-iot-hub"></a>Detectar e solucionar problemas de desconexões com o Hub do Azure IoT

Problemas de conectividade para dispositivos IoT podem ser difíceis de solucionar porque há muitos possíveis pontos de falha. A lógica do aplicativo do dispositivo, as redes físicas, os protocolos, o hardware e o Azure IoT Hub podem causar problemas. Este artigo fornece recomendações sobre como detectar e solucionar problemas de conectividade de dispositivos do lado da nuvem (em oposição ao lado do dispositivo).

## <a name="get-alerts-and-error-logs"></a>Obtenha alertas e logs de erros

Use o Azure Monitor para obter alertas e gravar logs quando as conexões de dispositivo solta.

### <a name="turn-on-diagnostic-logs"></a>Ativar logs de diagnóstico

Para registrar erros e eventos de conexão do dispositivo, ative o diagnóstico do Hub IoT.

1. Entre no [Portal do Azure](https://portal.azure.com).

2. Navegue até seu hub IoT.

3. Selecionar **configurações de Diagnóstico**.

4. Selecione **Ativar diagnóstico**.

5. Habilitar log de **conexões** a serem coletados.

6. Para uma análise mais fácil, ative **Enviar para o Log Analytics** ([ver preço](https://azure.microsoft.com/pricing/details/log-analytics/)). Consulte o exemplo em [resolver erros de conectividade](#resolve-connectivity-errors).

   ![Configurações recomendadas](./media/iot-hub-troubleshoot-connectivity/diagnostic-settings-recommendation.png)

Para obter mais informações, consulte [monitorar a integridade do IoT Hub do Azure e diagnosticar problemas rapidamente](iot-hub-monitor-resource-health.md).

### <a name="set-up-alerts-for-the-connected-devices-count-metric"></a>Configurar alertas para a métrica de contagem de _dispositivos conectados_

Para obter alertas quando os dispositivos se desconectar, configurar alertas sobre o **(visualização) de dispositivos conectados** métrica.

1. Entre no [Portal do Azure](https://portal.azure.com).

2. Navegue até seu hub IoT.

3. Selecione **Alertas**.

4. Selecione **nova regra de alerta**.

5. Selecione **Adicionar condição**, em seguida, selecione "Conectado dispositivos (visualização)".

6. Conclua a configuração de seus limites desejados e opções de alerta por prompts a seguir.

Para saber mais, consulte [O que são alertas clássicos no Microsoft Azure?](../azure-monitor/platform/alerts-overview.md).

## <a name="resolve-connectivity-errors"></a>Resolver problemas de conectividade

Quando você ativa logs de diagnóstico e alertas para dispositivos conectados, você recebe alertas quando ocorrem erros. Esta seção descreve como resolver problemas comuns quando você recebe um alerta. As etapas a seguir pressupõem que você configurou os logs do Azure Monitor para seus logs de diagnóstico.

1. Vá para sua área de trabalho para **Log Analytics** no portal do Azure.

2. Selecione **Pesquisa de Logs**.

3. Para isolar logs de erros de conectividade do Hub IoT, insira a consulta a seguir e selecione **Executar**:

    ```
    search *
    | where ( Type == "AzureDiagnostics" and ResourceType == "IOTHUBS")
    | where ( Category == "Connections" and Level == "Error")
    ```

1. Se houver resultados, procure `OperationName`, `ResultType` (código de erro), e `ResultDescription` (mensagem de erro) para obter mais detalhes sobre o erro.

   ![Exemplo de log de erros](./media/iot-hub-troubleshoot-connectivity/diag-logs.png)

2. Use esta tabela para entender e resolver erros comuns.

    | Erro | Causa raiz | Resolução |
    |-------|------------|------------|
    | DeviceConnectionClosedRemotely 404104 | A conexão foi fechada pelo dispositivo, mas o Hub IoT não sabe por quê. Causas comuns incluem tempo limite de MQTT / AMQP e perda de conectividade com a Internet. | Certifique-se de que o dispositivo possa se conectar ao Hub IoT até [testar a conexão](tutorial-connectivity.md). Se a conexão estiver correta, mas o dispositivo desconectar de forma intermitente, certifique-se de implementar a lógica adequada do dispositivo keep alive para sua escolha de protocolo (MQTT / AMPQ). |
    | IoTHubUnauthorized 401003 | O IoT Hub não foi possível autenticar a conexão. | Certifique-se de que o SAS ou outro token de segurança usado não esteja expirado. Os [SDKs IoT do Azure](iot-hub-devguide-sdks.md) geram automaticamente tokens sem exigir configuração especial. |
    | LinkCreationConflict 409002 | Um dispositivo tem mais de uma conexão. Quando uma nova solicitação de conexão chega para um dispositivo, o Hub IoT fecha a anterior com esse erro. | No caso mais comum, um dispositivo detecta uma desconexão e tenta restabelecer a conexão, mas o Hub IoT ainda considera o dispositivo conectado. O Hub IoT fecha a conexão anterior e registra esse erro. Esse erro geralmente aparece como um efeito colateral de um problema diferente e transitório, portanto, procure outros erros nos logs para solucionar problemas adicionais. Caso contrário, certifique-se de emitir uma nova solicitação de conexão apenas se a conexão cair. |
    | ServerError 500001 | O Hub IoT encontrou um problema no lado do servidor. Muito provavelmente, o problema é transitório. Enquanto a equipe do Hub IoT trabalha muito para manter [o SLA](https://azure.microsoft.com/support/legal/sla/iot-hub/), pequenos subconjuntos de nós do IoT Hub podem ocasionalmente sofrer falhas transitórias. Quando o dispositivo tenta se conectar a um nó com problemas, você recebe esse erro. | Para atenuar a falha transitória, emita uma nova tentativa do dispositivo. Para [gerenciar automaticamente as novas tentativas](iot-hub-reliability-features-in-sdks.md#connection-and-retry), certifique-se de usar a versão mais recente dos [SDKs do Azure IoT](iot-hub-devguide-sdks.md).<br><br>Como prática recomendada no tratamento de falhas transitórias e novas tentativas, consulte [tratamento de falhas transitórias](/azure/architecture/best-practices/transient-faults).  <br><br>Se o problema persistir após várias tentativas, verifique [Resource Health](iot-hub-monitor-resource-health.md#use-azure-resource-health) e [Status do Azure](https://azure.microsoft.com/status/history/) para ver se o IoT Hub tem um problema conhecido. Se não há nenhum problema conhecido e o problema persistir, [entre em contato com o suporte](https://azure.microsoft.com/support/options/) para uma investigação adicional. |
    | GenericTimeout 500008 | O Hub IoT não pôde concluir a solicitação de conexão antes do tempo limite. Como um 500001 ServerError, esse erro provavelmente é transitório. | Siga as etapas de solução de problemas de um 500001 ServerError para a causa raiz e resolva esse erro.|

## <a name="other-steps-to-try"></a>Outras etapas para experimentar

Se as etapas anteriores não ajudarem, você pode tentar:

* Se você tiver acesso aos dispositivos problemáticos, seja física ou remotamente (como o SSH), siga o [guia de solução de problemas do lado do dispositivo](https://github.com/Azure/azure-iot-sdk-node/wiki/Troubleshooting-Guide-Devices) para continuar com a solução de problemas.

* Verifique se os seus dispositivos estão **Habilitados** no portal do Azure> seu hub IoT> dispositivos IoT.

* Obter ajuda sobre [Fórum do IoT Hub do Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=azureiothub), [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-iot-hub), ou [suporte do Azure](https://azure.microsoft.com/support/options/).

Para ajudar a melhorar a documentação para todos, deixe um comentário na seção de comentários abaixo, caso este guia não tenha ajudado.

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre como resolver problemas transitórios, consulte [Tratamento de falhas transitórias](/azure/architecture/best-practices/transient-faults).

* Para saber mais sobre o SDK do IoT do Azure e gerenciar repetições, consulte [Como gerenciar a conectividade e mensagens confiáveis usando SDKs de dispositivo do Hub IoT do Azure](iot-hub-reliability-features-in-sdks.md#connection-and-retry).
