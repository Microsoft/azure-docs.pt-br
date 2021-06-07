---
title: incluir arquivo
description: incluir arquivo
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 08/01/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 45bdd569741dc13181bcaf9e8587a02b3d02c621
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "68728846"
---
1. Faça logon no [console do Firebase](https://firebase.google.com/console/). Crie um novo projeto do Firebase se você ainda não tiver um.
2. Depois de criar seu projeto, selecione **Adicionar Firebase ao seu aplicativo Android**. 

    ![Adicione o Firebase ao seu aplicativo Android](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)

3. Siga estas etapas na página **Adicionar Firebase ao seu aplicativo Android**: 
    1. Para o **nome do pacote do Android**, insira um nome para o pacote. Por exemplo: `tutorials.tutoria1.xamarinfcmapp`. 

        ![Especificar o nome do pacote](./media/notification-hubs-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)
    2. Selecione **Registrar aplicativo**.  
    1. Selecione **Baixar google-services.json**. Em seguida, salve o arquivo na pasta do projeto e selecione **Avançar**. Se ainda não criou o projeto do Visual Studio, poderá realizar essa etapa depois de criar o projeto. 

        ![Baixar google-services.json](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-service-button.png)
    6. Selecione **Avançar**. 
    7. Selecione **Ignorar esta etapa**. 

        ![Ignorar a última etapa](./media/notification-hubs-enable-firebase-cloud-messaging/skip-this-step.png)
8. No console do Firebase, selecione a engrenagem para seu projeto. Em seguida, selecione **Configurações do Projeto**.

    ![Selecione Configurações do Projeto](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. Se você ainda não baixou o arquivo **google-services.json**, é possível fazer isso nesta página. 

    ![Baixar o google-services.json na guia Geral](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-services-json-general-page.png)
1. Mude para a guia **Mensagens de Nuvem** na parte superior. Copie e salve a **Chave do servidor** para uso posterior. Você usará esse valor para configurar o hub de notificação.

    ![Copiar a chave do servidor](./media/notification-hubs-enable-firebase-cloud-messaging/server-key.png)
