---
title: Expressões de marca e roteamento
description: Este tópico explica as expressões de marca e roteamento para hubs de notificação do Azure.
services: notification-hubs
documentationcenter: .net
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 0fffb3bb-8ed8-4e0f-89e8-0de24a47f644
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2019
ms.author: jowargo
ms.openlocfilehash: 31a22aabc7b0f1d51a673ef8642037103badcc02
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61457750"
---
# <a name="routing-and-tag-expressions"></a>Expressões de marca e roteamento

## <a name="overview"></a>Visão geral

As expressões de marcas permitem que você direcione conjuntos específicos de dispositivos, ou mais especificamente, registros, ao enviar uma notificação por push por meio de Hubs de notificação.

## <a name="targeting-specific-registrations"></a>Como direcionar registros específicos

A única maneira de direcionar registros de notificações específicos é associar marcas a eles e, então, direcionar essas marcas. Conforme discutido em [Gerenciamento de registro](notification-hubs-push-notification-registration-management.md), para receber notificações por push, um aplicativo precisa registrar um dispositivo em um hub de notificação. Quando um registro é criado em um hub de notificação, o back-end do aplicativo pode enviar notificações por push para ele. O back-end do aplicativo pode escolher os registros para direcionar uma notificação específica das seguintes maneiras:

1. **Difusão**: todos os registros no hub de notificação recebem a notificação.
2. **Marca**: todos os registros que contêm a marca especificada recebem a notificação.
3. **Expressão de marca**: todos os registros cujos conjuntos de marcas correspondem à expressão especificada recebem a notificação.

## <a name="tags"></a>Marcas

Uma marca pode ser qualquer cadeia de caracteres com até 120 caracteres que contenha caracteres alfanuméricos e os seguintes caracteres não alfanuméricos: ‘_’, ‘@’, ‘#’, ‘.’, ‘:’, ‘-’. O exemplo a seguir mostra um aplicativo do qual você pode receber notificações de aviso sobre grupos musicais específicos. Nesse cenário, uma maneira simples de direcionar as notificações é rotular os registros com marcas que representem as diferentes bandas, como na seguinte imagem:

![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags.png)

Nesta figura, a mensagem marcada como **Beatles** atinge somente o tablet que estiver registrado com a marca **Beatles**.

Para mais informações sobre a criação de registros para marcas, consulte [Gerenciamento de registro](notification-hubs-push-notification-registration-management.md).

Você pode enviar notificações para marcas usando os métodos de notificações de envio da classe `Microsoft.Azure.NotificationHubs.NotificationHubClient` no SDK dos [Hubs de Notificação do Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/). Você também pode usar o Node. js ou as APIs de REST de notificações por push.  Aqui está um exemplo usando o SDK.

```csharp
Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;

// Windows 8.1 / Windows Phone 8.1
var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
"You requested a Beatles notification</text></binding></visual></toast>";
outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, "Beatles");

// Windows 10
toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
"You requested a Wailers notification</text></binding></visual></toast>";
outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, "Wailers");
```

As marcas não precisam ser pré-configuradas e podem se referir a vários conceitos específicos do aplicativo. Por exemplo, os usuários deste aplicativo de exemplo podem comentar sobre as bandas e desejar receber notificações do sistema, não apenas dos comentários sobre suas bandas favoritas, mas também de todos os comentários dos seus amigos, independentemente da banda sobre a qual eles estão comentando. A figura a seguir mostra um exemplo desse cenário:

![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags2.png)

Nesta imagem, Alice está interessada em atualizações dos Beatles e Fábio está interessado em atualizações dos Wailers. Fábio também está interessado nos comentários de Carlos e Carlos está interessado nos Wailers. Quando uma notificação é enviada para o comentário de Carlos sobre os Beatles, Alice e Fábio o recebem.

Embora você possa codificar vários interesses em marcas (por exemplo, “band_Beatles” ou “follows_Charlie”), as marcas são sequências de caracteres simples e não propriedades com valores. Um registro é marcado somente na presença ou ausência de uma marca específica.

Para obter um tutorial passo a passo completo sobre como usar marcas para enviar para grupos de interesse, consulte as [Últimas notícias](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md).

> [!NOTE]
> Os Hubs de Notificação do Azure dão suporte a um máximo de 60 marcas por registro.

## <a name="using-tags-to-target-users"></a>Como usar marcas para usuários de destino

Outra maneira de usar marcas é identificar todos os dispositivos de um usuário específico. Os registros podem ser marcados com uma marca que contenha uma ID de usuário, como na seguinte imagem:

![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags3.png)

Nesse cenário, a mensagem marcada com uid: Alice obtém todos os registros marcados com “uid:Alice”; portanto, todos os dispositivos de Alice.

## <a name="tag-expressions"></a>Expressões de marca

Há casos em que uma notificação tem como destino um conjunto de registros identificados não por uma única marca, mas por uma expressão booliana nas marcas.

Considere um aplicativo de esportes que envia um lembrete para todos em Boston sobre um jogo entre o Red Sox e o Cardinals. Se o aplicativo cliente registrar marcas sobre interesse em times e localização, a notificação deverá ser direcionada para todos em Boston interessados no Red Sox ou no Cardinals. Essa condição pode ser expressada com a seguinte expressão booliana:

```csharp
(follows_RedSox || follows_Cardinals) && location_Boston
```

![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags4.png)

As expressões de marca podem conter todos os operadores boolianos, como E (&&), OU (||) e NÃO (!). Eles também podem conter parênteses. As expressões de marca são limitadas a 20 marcas se contiverem apenas ORs; caso contrário, elas são limitadas a seis marcas.

Aqui está um exemplo de envio de notificações com expressões de marca usando o SDK.

```csharp
Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;

String userTag = "(location_Boston && !follows_Cardinals)";

// Windows 8.1 / Windows Phone 8.1
var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
"You want info on the Red Sox</text></binding></visual></toast>";
outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

// Windows 10
toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
"You want info on the Red Sox</text></binding></visual></toast>";
outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);
```
