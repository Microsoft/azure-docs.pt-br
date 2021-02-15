---
title: incluir arquivo
description: incluir arquivo
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 02/20/2019
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: f3cab4909937bbf5ccb0f72b194b08810c0487c2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "84792006"
---
### <a name="enable-logging-with-diagnostics-settings"></a>Habilitar registro em log com as configurações de diagnóstico

[!INCLUDE [updated-for-az](./updated-for-az.md)]

1. Entre no [Portal do Azure](https://portal.azure.com) e navegue até o Hub IoT.

2. Selecionar **configurações de Diagnóstico**.

3. Selecione **Ativar diagnóstico**.

   ![Ativar diagnóstico](./media/iot-hub-diagnostics-settings/turnondiagnostics.png)

4. Nomeie as configurações de diagnóstico.

5. Escolha para onde você deseja enviar os logs. Você pode selecionar qualquer combinação das três opções abaixo:

   * Arquivar em uma conta de armazenamento
   * Transmitir para um hub de eventos
   * Enviar para o Log Analytics

6. Escolha as operações que você deseja monitorar e habilite os logs para essas operações. As operações que podem ser informadas em configurações de diagnóstico são:

   * conexões
   * Telemetria de dispositivo
   * Mensagens da nuvem para o dispositivo
   * Operações de identidade do dispositivo
   * Carregamentos de arquivos
   * Roteamento de mensagem
   * Operações de dispositivo gêmeo para nuvem
   * Operações de nuvem gêmea para dispositivo
   * Operações de gêmeos
   * Operações de trabalho
   * Métodos diretos  
   * Rastreamento distribuído (versão prévia)
   * Configurações
   * Fluxos de dispositivo
   * Métricas do dispositivo

6. Salve as novas configurações. 

Se você deseja ativar as configurações de diagnóstico com o PowerShell, use o seguinte código:

```azurepowershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName <subscription that includes your IoT Hub>
Set-AzDiagnosticSetting -ResourceId <your resource Id> -ServiceBusRuleId <your service bus rule Id> -Enabled $true
```

As novas configurações terão efeito em aproximadamente 10 minutos. Depois disso, os logs aparecerão no destino de arquivamento configurado, na folha **Configurações de diagnóstico**. Para obter mais informações sobre como configurar o diagnóstico, consulte [coletar e consumir dados de log dos recursos do Azure](../articles/azure-monitor/platform/platform-logs-overview.md).
