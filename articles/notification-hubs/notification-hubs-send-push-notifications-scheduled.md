---
title: Como enviar notificações agendadas | Microsoft Docs
description: Este tópico descreve como usar as Notificações Agendadas com os Hubs de Notificação do Azure.
services: notification-hubs
documentationcenter: .net
keywords: notificações por push, notificação por push, agendando notificações por push
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 6b718c75-75dd-4c99-aee3-db1288235c1a
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: dotnet
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 5d27ad1f15e9f46f0bc67e62b79606828efb85e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102453148"
---
# <a name="how-to-send-scheduled-notifications"></a>Como enviar notificações agendadas

Se você tiver um cenário no qual deseja enviar uma notificação em algum momento futuro, mas não tiver um maneira fácil de despertar seu código back-end para enviar a notificação. Os hubs de notificação do tipo Standard oferecem suporte a um recurso que permite a programação de notificações para sete dias no futuro.


## <a name="schedule-your-notifications"></a>Agendar notificações
Ao enviar uma notificação, basta usar a classe [`ScheduledNotification`](/dotnet/api/microsoft.azure.notificationhubs.schedulednotification#microsoft_azure_notificationhubs_schedulednotification) no SDK dos Hubs de Notificação, conforme mostrado no seguinte exemplo:

```csharp
Notification notification = new AppleNotification("{\"aps\":{\"alert\":\"Happy birthday!\"}}");
var scheduled = await hub.ScheduleNotificationAsync(notification, new DateTime(2014, 7, 19, 0, 0, 0));
```

## <a name="cancel-scheduled-notifications"></a>Cancelar notificações agendadas
Além disso, você pode cancelar uma notificação previamente agendada usando essa notificationId:

```csharp
await hub.CancelNotificationAsync(scheduled.ScheduledNotificationId);
```

Não há limites para o número de notificações agendadas que você pode enviar.

## <a name="next-steps"></a>Próximas etapas

Consulte os seguintes tutoriais:

 - [Notificações por push para todos os dispositivos registrados](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
 - [Notificações por push para especificar dispositivos](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md)
 - [Notificações localizadas por push](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
 - [Enviar notificações por push para usuários específicos](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) 
 - [Notificações por push com base no local](notification-hubs-push-bing-spatial-data-geofencing-notification.md)
