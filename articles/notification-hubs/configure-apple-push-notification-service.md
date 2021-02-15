---
title: Configurar Apple Push Notification Service nos hubs de notificação do Azure | Microsoft Docs
description: Saiba como configurar um hub de notificação do Azure com configurações de Apple Push Notification Service (APNS).
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 06/22/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: 63c7e0c9569428b55420911f253deee52ce440cb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85255391"
---
# <a name="configure-apple-push-notification-service-settings-for-a-notification-hub-in-the-azure-portal"></a>Definir configurações de Apple Push Notification Service para um hub de notificação no portal do Azure

Este artigo mostra como definir as configurações de Apple Push Notification Service (APNS) para um hub de notificação do Azure usando o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Se você ainda não criou um hub de notificação, crie um agora. Para obter mais informações, consulte [Criar um hub de notificação do Azure no portal do Azure](create-notification-hub-portal.md).

## <a name="configure-apple-push-notification-service"></a>Configurar Apple Push Notification Service

O procedimento a seguir fornece as etapas para definir as configurações de Apple Push Notification Service (APNS) para um hub de notificação:

1. Na portal do Azure, na página **Hub de notificação** , selecione **Apple (APNS)** no menu à esquerda.

1. Para o **Modo de Autenticação**, selecione **Certificado** ou **Token**.

   - Se você selecionar **Certificado**:
      - Selecione o ícone de arquivo, e em seguida, o arquivo *.p12* que você deseja carregar.
      - Digite uma senha.
      - Selecione o modo **Sandbox**. Ou, para enviar notificação por push aos usuários que compraram seu aplicativo da loja, selecione o modo **Produção**.

     ![Captura de tela de uma configuração de certificado APN no portal do Azure](./media/configure-apple-push-notification-service/notification-hubs-apple-config-cert.png)

   - Se você selecionar **Token**:
      - Insira os valores de **Identificação da chave**, **ID do pacote**, **ID da equipe** e **Token**.
      - Selecione o modo **Sandbox**. Ou, para enviar notificação por push aos usuários que compraram seu aplicativo da loja, selecione o modo **Produção**.

     ![Captura de tela de uma configuração de token APNS no portal do Azure](./media/configure-apple-push-notification-service/notification-hubs-apple-config-token.png)

## <a name="next-steps"></a>Próximas etapas

Para obter um tutorial de instruções passo a passo para enviar notificações para dispositivos iOS, consulte o seguinte artigo: [enviar notificações por push para aplicativos Ios usando os hubs de notificação do Azure](ios-sdk-get-started.md).
