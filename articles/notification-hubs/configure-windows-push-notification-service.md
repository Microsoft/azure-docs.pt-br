---
title: Configurar o serviço de notificação por push do Windows nos hubs de notificação do Azure | Microsoft Docs
description: Saiba como definir as configurações do serviço de notificação por push do Windows para um hub de notificação do Azure.
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
ms.openlocfilehash: 62d73a23fff3bd15d873a579d451b2f91509b7fe
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87758723"
---
# <a name="configure-windows-push-notification-service-settings-in-the-azure-portal"></a>Definir as configurações do serviço de notificação por push do Windows no portal do Azure

Este artigo mostra como definir as configurações do WNS (serviço de notificação do Windows) para um hub de notificação do Azure usando o portal do Azure.  

## <a name="prerequisites"></a>Pré-requisitos

Se você ainda não criou um hub de notificação, crie um agora. Para obter mais informações, consulte [Criar um hub de notificação do Azure no portal do Azure](create-notification-hub-portal.md).

## <a name="configure-windows-push-notification-service-wns"></a>Configurar o serviço de notificação por push do Windows (WNS)

O procedimento a seguir descreve as etapas para definir as configurações de WNS (serviço de notificação por push do Windows) para um hub de notificação:

1. Na portal do Azure, na página **Hub de notificação** , selecione **Windows (WNS)** no menu à esquerda.
2. Insira valores para **SID do Pacote** e **Chave de Segurança**.
3. Clique em **Salvar**.

   ![Captura de tela que mostra as caixas SID do pacote e as chaves de segurança](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

## <a name="next-steps"></a>Próximas etapas

Para obter um tutorial de instruções passo a passo para o envio de notificações por push para Plataforma Universal do Windows aplicativos usando os hubs de notificação do Azure e o WNS (serviço de notificação por push do Windows), consulte [enviar notificações para aplicativos UWP usando os hubs de notificação do Azure](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).
