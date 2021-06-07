---
title: Configurar o registro em log para monitorar aplicativos lógicos na central de segurança do Azure
description: Monitore a integridade dos recursos dos aplicativos lógicos na central de segurança do Azure Configurando o log de diagnóstico.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 12/07/2020
ms.openlocfilehash: 42b33a5b96de7334f8310b040052c633342f5e05
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101712378"
---
# <a name="set-up-logging-to-monitor-logic-apps-in-azure-security-center"></a>Configurar o registro em log para monitorar aplicativos lógicos na central de segurança do Azure

Ao monitorar os recursos dos aplicativos lógicos na [central de segurança Microsoft Azure](../security-center/security-center-introduction.md), você pode [examinar se os aplicativos lógicos estão seguindo as políticas padrão](#view-logic-apps-health-status). O Azure mostra o status de integridade de um recurso de aplicativos lógicos depois de habilitar o registro em log e configurar corretamente o destino dos logs. Este artigo explica como configurar o log de diagnósticos e certificar-se de que todos os seus aplicativos lógicos são recursos íntegros.

> [!TIP]
> Para localizar o status atual do serviço de aplicativos lógicos, examine a [página de status do Azure](https://status.azure.com/), que lista o status de diferentes produtos e serviços em cada região disponível.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura, [crie uma conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar.
* Aplicativos lógicos existentes com [log de diagnóstico habilitado](#enable-diagnostic-logging).
* Um espaço de trabalho Log Analytics, que é necessário para habilitar o registro em log para seu aplicativo lógico. Se você não tiver um espaço de trabalho, primeiro [crie seu espaço de trabalho](../azure-monitor/logs/quick-create-workspace.md).

## <a name="enable-diagnostic-logging"></a>Habilitar registro em log de diagnóstico

Antes de poder exibir o status de integridade do recurso para seus aplicativos lógicos, primeiro você deve [Configurar o log de diagnóstico](monitor-logic-apps-log-analytics.md). Se você já tiver um espaço de trabalho Log Analytics, poderá habilitar o registro em log ao criar seu aplicativo lógico ou em aplicativos lógicos existentes.

> [!TIP]
> A recomendação padrão é habilitar os logs de diagnóstico para aplicativos lógicos. No entanto, você controla essa configuração para seus aplicativos lógicos. Ao habilitar os logs de diagnóstico para seus aplicativos lógicos, você pode usar as informações para ajudar a analisar incidentes de segurança.

### <a name="check-diagnostic-logging-setting"></a>Verificar a configuração de log de diagnóstico

Se você não tiver certeza se seus aplicativos lógicos têm o log de diagnóstico habilitado, você pode fazer check-in na central de segurança:

1. Entre no [portal do Azure](https://portal.azure.com).
1. Na barra de pesquisa, insira e selecione **central de segurança**.
1. No menu do painel da central de segurança, em **geral**, selecione **recomendações**.
1. Na tabela de sugestões de segurança, localize e selecione **habilitar auditoria e log** &gt; **logs de diagnóstico em aplicativos lógicos devem ser habilitados** na tabela de controles de segurança.
1. Na página recomendação, expanda a seção **etapas de correção** e examine as opções. Você pode habilitar o diagnóstico de aplicativos lógicos selecionando a **correção rápida!** ou seguindo as instruções de correção manual.

## <a name="view-logic-apps-health-status"></a>Exibir o status de integridade dos aplicativos lógicos

Depois de [habilitar o log de diagnóstico](#enable-diagnostic-logging), você poderá ver o status de integridade dos seus aplicativos lógicos na central de segurança.

1. Entre no [portal do Azure](https://portal.azure.com).
1. Na barra de pesquisa, insira e selecione **central de segurança**.
1. No menu do painel da central de segurança, em **geral**, selecione **inventário**.
1. Na página inventário, filtre sua lista de ativos para mostrar somente os recursos dos aplicativos lógicos. No menu página, selecione **tipos de recursos** &gt; **aplicativos lógicos**.

   O contador **recursos não íntegros** mostra o número de aplicativos lógicos que a central de segurança considera não íntegros.
1.  Na lista de recursos de aplicativos lógicos, examine a coluna **recomendações** . Para examinar os detalhes de integridade de um aplicativo lógico específico, selecione um nome de recurso ou selecione o botão de reticências (**...**) &gt; **Exibir recurso**.
1.  Para corrigir quaisquer possíveis problemas de integridade de recursos, siga as etapas listadas para seus aplicativos lógicos.

Se o log de diagnóstico já estiver habilitado, poderá haver um problema com o destino de seus logs. Examine [como corrigir problemas com diferentes destinos de log de diagnóstico](#fix-diagnostic-logging-for-logic-apps).

## <a name="fix-diagnostic-logging-for-logic-apps"></a>Corrigir o log de diagnóstico para aplicativos lógicos

Se seus [aplicativos lógicos estiverem listados como não íntegros na central de segurança](#view-logic-apps-health-status), abra seu aplicativo lógico na exibição de código na portal do Azure ou por meio do CLI do Azure. Em seguida, verifique a configuração de destino dos seus logs de diagnóstico: [Azure log Analytics](#log-analytics-and-event-hubs-destinations), [hubs de eventos do Azure](#log-analytics-and-event-hubs-destinations)ou [uma conta de armazenamento do Azure](#storage-account-destination).

### <a name="log-analytics-and-event-hubs-destinations"></a>Destinos de Log Analytics e de hubs de eventos

Se você usar Log Analytics ou hubs de eventos como o destino para seus logs de diagnóstico de aplicativos lógicos, verifique as configurações a seguir. 

1. Para confirmar que você habilitou os logs de diagnóstico, verifique se o campo configurações de diagnóstico `logs.enabled` está definido como `true` . 
1. Para confirmar que você não definiu uma conta de armazenamento como o destino, verifique se o `storageAccountId` campo está definido como `false` .

Por exemplo:

```json
"allOf": [
    {
        "field": "Microsoft.Insights/diagnosticSettings/logs.enabled",
        "equals": "true"
    },
    {
        "anyOf": [
            {
                "field": "Microsoft.Insights/diagnosticSettings/logs[*].retentionPolicy.enabled",
                "notEquals": "true"
            },
            {
                "field": "Microsoft.Insights/diagnosticSettings/storageAccountId",
                "exists": false
            }
        ]
    }
] 
```

### <a name="storage-account-destination"></a>Destino da conta de armazenamento

Se você usar uma conta de armazenamento como o destino para seus logs de diagnóstico de aplicativos lógicos, verifique as configurações a seguir.

1. Para confirmar que você habilitou os logs de diagnóstico, verifique se o campo configurações de diagnóstico `logs.enabled` está definido como `true` .
1. Para confirmar que você habilitou uma política de retenção para seus logs de diagnóstico, verifique se o `retentionPolicy.enabled` campo está definido como `true` .
1. Para confirmar que você definiu um tempo de retenção de 0-365 dias, verifique se o `retentionPolicy.days` campo está definido para um número inclusivamente entre 0 e 365.

```json
"allOf": [
    {
        "field": "Microsoft.Insights/diagnosticSettings/logs[*].retentionPolicy.enabled",
        "equals": "true"
    },
    {
        "anyOf": [
            {
                "field": "Microsoft.Insights/diagnosticSettings/logs[*].retentionPolicy.days",
                "equals": "0"
            },
            {
                "field": "Microsoft.Insights/diagnosticSettings/logs[*].retentionPolicy.days",
                "equals": "[parameters('requiredRetentionDays')]"
            }
          ]
    },
    {
        "field": "Microsoft.Insights/diagnosticSettings/logs.enabled",
        "equals": "true"
    }
]
```