---
title: incluir arquivo
description: incluir arquivo
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 09/11/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: ccb5e40738680181e7339b8652d029597c7d0bd4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "70935163"
---
1. Faça logon no [console do Firebase](https://firebase.google.com/console/). Crie um novo projeto do Firebase se você ainda não tiver um.
2. Depois de criar seu projeto, selecione **Adicionar Firebase ao seu aplicativo Android**. 

    ![Adicione o Firebase ao seu aplicativo Android](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. Siga estas etapas na página **Adicionar Firebase ao seu aplicativo Android**: 
    1. Para **Nome do pacote Android**, copie o valor de **applicationId** no arquivo build.gradle do aplicativo. Neste exemplo, é `com.fabrikam.fcmtutorial1app`. 

        ![Especificar o nome do pacote](./media/notification-hubs-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)
    2. Selecione **Registrar aplicativo**. 
4. Selecione **Baixar google-services.json**, salve o arquivo na pasta **app** do projeto e, em seguida, selecione **Avançar**. 

    ![Baixar google-services.json](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-service-button.png)
5. Faça as seguintes **alterações na configuração** do seu projeto no Android Studio. 
    1.  No arquivo project-level build.gradle (&lt;project&gt;/build.gradle), adicione a seguinte instrução à seção de **dependências**. 

        ```
        classpath 'com.google.gms:google-services:4.0.1'
        ```
    2. No arquivo build.gradle no nível do aplicativo (&lt;project&gt;/&lt;app-module&gt;/build.gradle), adicione as seguintes instruções à seção de **dependências**. 

        ```
        implementation 'com.google.firebase:firebase-core:16.0.8'
        implementation 'com.google.firebase:firebase-messaging:17.3.4'
        ```

    3. Adicione a seguinte linha ao final do arquivo build.gradle no nível do aplicativo após a seção de dependências. 

        ```
        apply plugin: 'com.google.gms.google-services'
        ```        
    4. Selecione **Sincronizar agora** na barra de ferramentas. 
 
        ![Alterações na configuração de build.gradle](./media/notification-hubs-enable-firebase-cloud-messaging/build-gradle-configurations.png)
6. Selecione **Avançar**. 
7. Selecione **Ignorar esta etapa**. 

    ![Ignorar a última etapa](./media/notification-hubs-enable-firebase-cloud-messaging/skip-this-step.png)
8. No console do Firebase, selecione a engrenagem para seu projeto. Em seguida, selecione **Configurações do Projeto**.

    ![Selecione Configurações do Projeto](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. Se você não tiver baixado o arquivo google-services.json para a pasta **aplicativo** do projeto do Android Studio, será possível fazer isso nesta página. 
5. Mude para a guia **Mensagens de Nuvem** na parte superior. 
6. Copie e salve a **Chave do servidor** para uso posterior. Use esse valor para configurar o hub.
