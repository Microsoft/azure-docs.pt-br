---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 02/05/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: fef6122eceda213fb6353ada53033d0d1e27fd7e
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509119"
---
1. Faça logon no [console do Firebase](https://firebase.google.com/console/). Crie um novo projeto do Firebase se você ainda não tiver um.
2. Depois de criar seu projeto, selecione **Adicionar Firebase ao seu aplicativo Android**. 

    ![Adicione o Firebase ao seu aplicativo Android](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. Na página **Adicionar Firebase ao seu aplicativo Android**, siga estas etapas: 
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
    2. No arquivo build.gradle no nível do aplicativo (&lt;project&gt;/&lt;app-module&gt;/build.gradle), adicione a seguinte instrução à seção de **dependências**. 

        ```
        implementation 'com.google.firebase:firebase-core:16.0.1'
        ```

    3. Adicione a seguinte linha ao final do arquivo build.gradle no nível do aplicativo após a seção de dependências. 

        ```
        apply plugin: 'com.google.gms.google-services'
        ```        
    4. Selecione **Sincronizar agora** na barra de ferramentas. 
 
        ![Alterações na configuração de build.gradle](./media/notification-hubs-enable-firebase-cloud-messaging/build-gradle-configurations.png)
6. Selecione **Avançar**. 
7. Selecione **Ignore esta etapa**. 

    ![Ignorar a última etapa](./media/notification-hubs-enable-firebase-cloud-messaging/skip-this-step.png)
8. No console do Firebase, selecione a engrenagem para seu projeto. Em seguida, selecione **Configurações do Projeto**.

    ![Selecione Configurações do Projeto](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. Se você não tiver baixado o arquivo google-services.json para a pasta **aplicativo** do projeto do Android Studio, será possível fazer isso nesta página. 
5. Mude para a guia **Mensagens de Nuvem** na parte superior. 
6. Copie e salve a **Chave do servidor** para uso posterior. Use esse valor para configurar o hub.
