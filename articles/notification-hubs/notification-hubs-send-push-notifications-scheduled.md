---
title: Como enviar notificações agendadas | Microsoft Docs
description: Este tópico descreve como usar as Notificações Agendadas com os Hubs de Notificação do Azure.
services: notification-hubs
documentationcenter: .net
keywords: notificações por push, notificação por push, agendando notificações por push
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 6b718c75-75dd-4c99-aee3-db1288235c1a
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: dotnet
ms.topic: article
origin.date: 04/14/2018
ms.date: 02/25/2019
ms.author: v-biyu
ms.openlocfilehash: af0de9e8c18644f4ae200f6546c0dd0a41320f9f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61457678"
---
# <a name="how-to-send-scheduled-notifications"></a>Como: Enviar notificações agendadas

Se você tiver um cenário no qual deseja enviar uma notificação em algum momento futuro, mas não tiver um maneira fácil de despertar seu código back-end para enviar a notificação. Os hubs de notificação do tipo Standard oferecem suporte a um recurso que permite a programação de notificações para sete dias no futuro.


## <a name="schedule-your-notifications"></a>Agendar notificações
Ao enviar uma notificação, basta usar a classe [`ScheduledNotification` ](https://msdn.microsoft.com/library/microsoft.azure.notificationhubs.schedulednotification.aspx) no SDK dos Hubs de Notificação, conforme mostrado no seguinte exemplo:

```c#
Notification notification = new AppleNotification("{\"aps\":{\"alert\":\"Happy birthday!\"}}");
var scheduled = await hub.ScheduleNotificationAsync(notification, new DateTime(2014, 7, 19, 0, 0, 0));
```

## <a name="cancel-scheduled-notifications"></a>Cancelar notificações agendadas
Além disso, você pode cancelar uma notificação previamente agendada usando essa notificationId:

```c#
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
