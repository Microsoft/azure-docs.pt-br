---
title: Configurar o Google firebase Cloud Messaging nos hubs de notificação do Azure | Microsoft Docs
description: Saiba como configurar um hub de notificação do Azure com as configurações de mensagens de nuvem do Google firebase.
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 08/04/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: 0da103c11e2412108535ca322917632f5d95559d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96003558"
---
# <a name="configure-google-firebase-settings-for-a-notification-hub-in-the-azure-portal"></a>Definir configurações do Google firebase para um hub de notificação no portal do Azure

Este artigo mostra como configurar as configurações do FCM (Google firebase Cloud Messaging) para um hub de notificação do Azure usando o portal do Azure.  

## <a name="prerequisites"></a>Pré-requisitos

Se você ainda não criou um hub de notificação, crie um agora. Para obter mais informações, consulte [Criar um hub de notificação do Azure no portal do Azure](create-notification-hub-portal.md).

## <a name="configure-google-firebase-cloud-messaging-fcm"></a>Configurar o Google firebase Cloud Messaging (FCM)

O procedimento a seguir descreve as etapas para configurar as configurações do FCM (Google firebase Cloud Messaging) para um hub de notificação:

1. Na portal do Azure, na página **Hub de notificação** , selecione **Google (GCM/FCM)** no menu à esquerda.
2. Cole a **chave de API** do projeto do FCM salva anteriormente.
3. Clique em **Salvar**.

   ![Captura de tela que mostra como configurar os Hubs de Notificação para Google FCM](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)

## <a name="next-steps"></a>Próximas etapas

Para obter um tutorial de instruções passo a passo para enviar notificações para dispositivos Android usando os hubs de notificação do Azure e o Google firebase Cloud Messaging, consulte [enviar notificações por push para dispositivos Android usando hubs de notificação e o Google FCM](notification-hubs-android-push-notification-google-fcm-get-started.md).
