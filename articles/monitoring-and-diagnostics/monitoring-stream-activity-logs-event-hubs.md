---
title: "Transmissão do Log de Atividades do Azure para os Hubs de Eventos | Microsoft Docs"
description: Saiba como transmitir o Log de Atividades do Azure para os Hubs de Eventos.
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ec4c2d2c-8907-484f-a910-712403a06829
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 6/06/2017
ms.author: johnkem
ms.openlocfilehash: 88c5701279f370914fac68872d67b02a7571748a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="stream-the-azure-activity-log-to-event-hubs"></a>Transmissão do Log de Atividades do Azure para os Hubs de Eventos
Os [**Logs de Atividade do Azure**](monitoring-overview-activity-logs.md) podem ser transmitidos quase em tempo real a qualquer aplicativo usando a opção interna "Exportar" no Portal, ou habilitando a ID da Regra de Barramento de Serviço em um Perfil de Log por meio de Cmdlets do Azure PowerShell ou da CLI do Azure.

## <a name="what-you-can-do-with-the-activity-log-and-event-hubs"></a>O que você pode fazer com o Log de Atividades e os Hubs de Eventos
Veja algumas maneiras de usar o recurso de streaming para o Log de Atividade:

* **Transmitir para sistemas de registro em log e telemetria de terceiros** – Ao longo do tempo, a transmissão de Hubs de Eventos se tornará o mecanismo para direcionar seus Logs de Atividade para SIEMs e soluções de análise de log de terceiros.
* **Compilar uma plataforma de registro em log e telemetria personalizada** – Se você já tiver uma plataforma de telemetria personalizada ou estiver pensando em criar uma, a natureza altamente escalonável de publicação-assinatura dos Hubs de Eventos permite a flexibilidade de ingestão de log de atividade. [Consulte o guia de Dan Rosanova sobre como usar os Hubs de Eventos em uma plataforma de telemetria de escala global.](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)

## <a name="enable-streaming-of-the-activity-log"></a>Habilitar o streaming do Log de Atividades
Você pode habilitar o streaming do Log de Atividades programaticamente ou por meio do portal. De qualquer forma, você escolhe um namespace de barramento de serviço e uma política de acesso compartilhado para esse namespace e um hub de eventos é criado no namespace quando ocorre o primeiro novo evento de Log de Atividades. Se você não tiver um Namespace do Barramento de Serviço, você precisará primeiro criar um. Se você tiver distribuído anteriormente eventos de Log de Atividades para esse namespace do barramento de serviço, o Hub de Eventos criado anteriormente será reutilizado. A política de acesso compartilhado define as permissões que o mecanismo de streaming tem. Atualmente, o streaming para um Hub de Eventos exige as **Gerenciar**, **Enviar** e **Escutar**. Você pode criar ou modificar políticas de acesso compartilhado do Namespace do Barramento de Serviço no portal clássico, na guia "Configurar" para seu Namespace de Barramento de Serviço. Para atualizar o perfil de registro do Log de Atividades a fim de incluir o streaming, o usuário fazendo a alteração deve ter a permissão ListKey nessa Regra de Autorização do Barramento de Serviço.

O namespace do barramento de serviço ou do hub de eventos não precisa estar na mesma assinatura que a assinatura que emite os logs, contanto que o usuário que define a configuração tenha acesso RBAC apropriado a ambas as assinaturas.

### <a name="via-azure-portal"></a>Via Portal do Azure
1. Navegue até a folha **Log de Atividades** usando o menu no lado esquerdo do portal.
   
    ![Navegue até o Log de Atividades no portal](./media/monitoring-overview-activity-logs/activity-logs-portal-navigate.png)
2. Clique no botão **Exportar** na parte superior da folha.
   
    ![Botão Exportar no portal](./media/monitoring-overview-activity-logs/activity-logs-portal-export.png)
3. Na folha que aparece, você pode selecionar as regiões para as quais você deseja realizar o streaming de eventos, e o Namespace do Barramento de Serviço no qual você gostaria de criar um Hub de Eventos para esses eventos de streaming.
   
    ![Folha Exportar Log de Atividades](./media/monitoring-overview-activity-logs/activity-logs-portal-export-blade.png)
4. Clique em **Salvar** para salvar as configurações. As configurações serão aplicadas imediatamente à sua assinatura.

### <a name="via-powershell-cmdlets"></a>Via Cmdlets do PowerShell
Se já existir um perfil de log, primeiro remova esse perfil.

1. Use `Get-AzureRmLogProfile` para identificar se já existe um perfil de log
2. Se existir, use `Remove-AzureRmLogProfile` para removê-lo.
3. Use `Set-AzureRmLogProfile` para criar um perfil:

```
Add-AzureRmLogProfile -Name my_log_profile -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus -RetentionInDays 90 -Categories Write,Delete,Action
```

A ID da Regra do Barramento de Serviço é uma cadeia de caracteres com este formato: {ID de recurso do barramento de serviço} /authorizationrules/ {nome da chave}, por exemplo 

### <a name="via-azure-cli"></a>Via CLI do Azure
Se já existir um perfil de log, primeiro remova esse perfil.

1. Use `azure insights logprofile list` para identificar se já existe um perfil de log
2. Se existir, use `azure insights logprofile delete` para removê-lo.
3. Use `azure insights logprofile add` para criar um perfil:

```
azure insights logprofile add --name my_log_profile --storageId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/my_storage --serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope --retentionInDays 90 –categories Write,Delete,Action
```

A ID da Regra do Barramento de Serviço é uma cadeia de caracteres com este formato: `{service bus resource ID}/authorizationrules/{key name}`.

## <a name="how-do-i-consume-the-log-data-from-event-hubs"></a>Como eu consumo os dados de log dos Hubs de Eventos?
[O esquema para o Log de Atividades está disponível aqui](monitoring-overview-activity-logs.md). Cada evento é uma matriz de blobs JSON chamada "registros".

## <a name="next-steps"></a>Próximas etapas
* [Arquivar o Log de Atividades em uma conta de armazenamento](monitoring-archive-activity-log.md)
* [Leia a visão geral do Log de Atividades do Azure](monitoring-overview-activity-logs.md)
* [Configurar um alerta com base em um evento do Log de Atividades](insights-auditlog-to-webhook-email.md)

