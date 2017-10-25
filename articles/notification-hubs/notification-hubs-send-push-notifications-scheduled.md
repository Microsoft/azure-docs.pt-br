---
title: "Como enviar notificações agendadas | Microsoft Docs"
description: "Este tópico descreve como usar as Notificações Agendadas com os Hubs de Notificação do Azure."
services: notification-hubs
documentationcenter: .net
keywords: "notificações por push, notificação por push, agendando notificações por push"
author: ysxu
manager: erikre
editor: 
ms.assetid: 6b718c75-75dd-4c99-aee3-db1288235c1a
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: dotnet
ms.topic: article
ms.date: 06/29/2016
ms.author: yuaxu
ms.openlocfilehash: efac6e1ecc00359f1622d380333140bc055c83e0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-send-scheduled-notifications"></a>Como enviar notificações agendadas
## <a name="overview"></a>Visão geral
Se você tiver um cenário no qual deseja enviar uma notificação em algum momento futuro, mas não tiver um maneira fácil de despertar seu código back-end para enviar a notificação. Os Hubs de Notificação do tipo Standard oferecem suporte a um recurso que permite a programação de notificações para sete dias no futuro.

Ao enviar uma notificação, simplesmente use a classe [ScheduledNotification](https://msdn.microsoft.com/library/microsoft.azure.notificationhubs.schedulednotification.aspx) no SDK dos Hubs de Notificação, conforme mostra o exemplo a seguir:

    Notification notification = new AppleNotification("{\"aps\":{\"alert\":\"Happy birthday!\"}}");
    var scheduled = await hub.ScheduleNotificationAsync(notification, new DateTime(2014, 7, 19, 0, 0, 0));

Além disso, você pode cancelar uma notificação previamente agendada usando essa notificationId:

    await hub.CancelNotificationAsync(scheduled.ScheduledNotificationId);

Não há limites para o número de notificações agendadas que você pode enviar.

