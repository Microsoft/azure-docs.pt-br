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
ms.openlocfilehash: cd202c98ed605209f5600965ecdb6c0b4c03c17e
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509138"
---
1. No Android Studio, selecione **Ferramentas** no menu e selecione **Gerenciador do SDK**. 
2. Selecione a versão de destino do SDK do Android que é usada em seu projeto. Em seguida, selecione **Mostrar detalhes do pacote**. 

    ![Gerenciador do SDK do Android – selecionar versão de destino](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-sdk-manager.png)
3. Selecione **APIs do Google**, caso ainda não estejam instaladas.

    ![Gerenciador do SDK do Android – APIs do Google selecionadas](./media/notification-hubs-android-studio-add-google-play-services/googole-apis-selected.png)
4. Alterne para a guia **SDK Tools**. Caso ainda não tenha instalado o Google Play Services, selecione **Google Play Services**, conforme mostrado na imagem a seguir. Em seguida, selecione **Aplicar** para instalar. Anote o caminho do SDK, a ser usado em uma etapa posterior.

    ![Gerenciador do SDK do Android – Google Play Services selecionado](./media/notification-hubs-android-studio-add-google-play-services/google-play-services-selected.png)
3. Se a caixa de diálogo **Confirmar Alteração** for exibida, selecione **OK**. O Instalador de Componente instalará os componentes solicitados. Selecione **Concluir** depois que os componentes forem instalados.
4. Selecione **OK** para fechar a caixa de diálogo **Configurações para Novos Projetos**.  
5. Abra o arquivo build.gradle no diretório do **aplicativo** e, em seguida, adicione a seguinte linha em `dependencies`. 

    ```gradle
    implementation 'com.google.android.gms:play-services-gcm:16.0.0'
    ```
5. Selecione o ícone **Sincronizar Agora** na barra de ferramentas.

    ![Sincronização com o Gradle](./media/notification-hubs-android-studio-add-google-play-services/gradle-sync.png)
1. Abra o arquivo AndroidManifest.xml e adicione a seguinte marca ao *aplicativo*.

    ```xml
    <meta-data android:name="com.google.android.gms.version"
         android:value="@integer/google_play_services_version" />
    ```
