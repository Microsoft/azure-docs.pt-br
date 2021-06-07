---
title: Migrar monitoramento de operações do Hub IoT do Azure para logs de recursos do Hub IoT no Azure Monitor | Microsoft Docs
description: Como atualizar o Hub IoT do Azure para usar Azure Monitor em vez do monitoramento de operações para monitorar o status das operações no Hub IoT em tempo real.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: kgremban
ms.openlocfilehash: 48b646881b12047b28490999a96326f6076af2c1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100591847"
---
# <a name="migrate-your-iot-hub-from-operations-monitoring-to-azure-monitor-resource-logs"></a>Migrar o Hub IoT do monitoramento de operações para Azure Monitor logs de recursos

Os clientes que usam o [monitoramento de operações](iot-hub-operations-monitoring.md) para acompanhar o status das operações no Hub IOT podem migrar esse fluxo de trabalho para [Azure monitor logs de recursos](../azure-monitor/essentials/platform-logs-overview.md), um recurso do Azure monitor. Os logs de recursos fornecem informações de diagnóstico no nível do recurso para muitos serviços do Azure.

**A funcionalidade de monitoramento de operações do Hub IOT foi preterida** e foi removida do Portal. Este artigo fornece etapas para mover suas cargas de trabalho do monitoramento de operações para Azure Monitor logs de recursos. Para saber mais sobre a linha do tempo de substituição, confira [Monitorar suas soluções de IoT do Azure com o Azure Monitor e o Azure Resource Health](https://azure.microsoft.com/blog/monitor-your-azure-iot-solutions-with-azure-monitor-and-azure-resource-health/).

## <a name="update-iot-hub"></a>Atualizar Hub IoT

Para atualizar o Hub IoT no portal do Azure, primeiro crie uma configuração de diagnóstico e desative o monitoramento de operações.  

### <a name="create-a--diagnostic-setting"></a>Criar uma configuração de diagnóstico

1. Entre no [Portal do Azure](https://portal.azure.com) e navegue até o Hub IoT.

1. No painel esquerdo, em **monitoramento**, selecione **configurações de diagnóstico**. Em seguida, selecione **Adicionar configuração de diagnóstico**.

   :::image type="content" source="media/iot-hub-migrate-to-diagnostics-settings/open-diagnostic-settings.png" alt-text="Captura de tela que realça as Configurações do diagnóstico na seção Monitoramento.":::

1. No painel **configuração de diagnóstico** , dê um nome à configuração de diagnóstico.

1. Em **detalhes da categoria**, selecione as categorias para as operações que você deseja monitorar. Para obter mais informações sobre as categorias de operações disponíveis com o Hub IoT, consulte [logs de recursos](monitor-iot-hub-reference.md#resource-logs).

1. Em **detalhes de destino**, escolha onde você deseja enviar os logs. Você pode selecionar qualquer combinação desses destinos:

   * Arquivar em uma conta de armazenamento
   * Transmitir para um hub de eventos
   * Enviar para logs de Azure Monitor por meio de um espaço de trabalho Log Analytics

   A captura de tela a seguir mostra uma configuração de diagnóstico que roteia as operações nas categorias conexões e telemetria de dispositivo para um espaço de trabalho Log Analytics:

   :::image type="content" source="media/iot-hub-migrate-to-diagnostics-settings/add-diagnostic-setting.png" alt-text="Captura de tela mostrando uma configuração de diagnóstico concluída.":::

1. Clique em **Salvar** para salvar as configurações.

As novas configurações terão efeito em aproximadamente 10 minutos. Depois disso, os logs aparecem no destino configurado. Para obter mais informações sobre como configurar o diagnóstico, consulte [coletar e consumir dados de log dos recursos do Azure](../azure-monitor/essentials/platform-logs-overview.md).

Para obter informações mais detalhadas sobre como criar configurações de diagnóstico, incluindo com o PowerShell e o CLI do Azure, consulte [configurações de diagnóstico](../azure-monitor/essentials/diagnostic-settings.md) na documentação do Azure monitor.

### <a name="turn-off-operations-monitoring"></a>Desligue o monitoramento de operações

> [!NOTE]
> A partir de 11 de março de 2019, o recurso de monitoramento de operações é removido da interface de portal do Azure do Hub IoT. As etapas abaixo não se aplicam mais. Para migrar, certifique-se de que as categorias corretas sejam roteadas para um destino com uma configuração de diagnóstico Azure Monitor acima.

Depois de testar as novas configurações de diagnóstico em seu fluxo de trabalho, você pode desativar o recurso de monitoramento de operações. 

1. No menu do Hub IoT, selecione **Monitoramento de operações**.

2. Em cada categoria de monitoramento, selecione **Nenhum**.

3. Salvar alterações no monitoramento de operações.

## <a name="update-applications-that-use-operations-monitoring"></a>Atualizar os aplicativos que usam o monitoramento de operações

Os esquemas para monitoramento de operações e logs de recursos variam um pouco. É importante que você atualize os aplicativos que usam o monitoramento de operações hoje para mapear para o esquema usado pelos logs de recursos.

Além disso, os logs de recursos do Hub IoT oferecem cinco novas categorias para acompanhamento. Depois de atualizar aplicativos para o esquema existente, adicione as novas categorias também:

* Operações de dispositivo gêmeo para nuvem
* Operações de nuvem gêmea para dispositivo
* Consultas de gêmeos
* Operações de trabalhos
* Métodos diretos

Para obter as estruturas de esquema específicas, consulte [logs de recursos](monitor-iot-hub-reference.md#resource-logs).

## <a name="monitoring-device-connect-and-disconnect-events-with-low-latency"></a>Monitoramento de dispositivo se conectar e desconectar eventos com baixa latência

Para monitorar eventos de conexão e desconexão do dispositivo em produção, é recomendável assinar o [evento **dispositivo desconectado**](iot-hub-event-grid.md#event-types) na grade de eventos para obter alertas e monitorar o estado da conexão do dispositivo. Use este [tutorial](iot-hub-how-to-order-connection-state-events.md) para saber como integrar os eventos Dispositivo Conectado e Dispositivo Desconectado do Hub IoT em sua solução de IoT.

## <a name="next-steps"></a>Próximas etapas

[Monitorar o Hub IoT](monitor-iot-hub.md)