---
title: Enviar notificações por push para o Android usando os Hubs de Notificação do Azure e o SDK do Firebase versão 1.0.0-preview1
description: Neste tutorial, você aprenderá a usar os Hubs de Notificação do Azure e o Google Firebase Cloud Messaging para enviar notificações por push para dispositivos Android (versão 1.0.0-preview1).
author: sethmanheim
ms.author: sethm
ms.date: 5/28/2020
ms.topic: tutorial
ms.service: notification-hubs
ms.reviewer: thsomasu
ms.lastreviewed: 05/27/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 091ada7f099aca17152fc806e61dce6ab979852e
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106055154"
---
# <a name="tutorial-send-push-notifications-to-android-devices-using-firebase-sdk-version-100-preview1"></a>Tutorial: Enviar notificações por push para dispositivos Android usando o SDK do Firebase versão 1.0.0-preview1

Este tutorial mostra como usar os Hubs de Notificação do Azure e a versão atualizada do SDK do FCM (Firebase Cloud Messaging) (versão 1.0.0-preview1) para enviar notificações por push para um aplicativo Android. Neste tutorial, você criará um aplicativo do Android em branco que receberá notificações por push por meio do FCM (Firebase Cloud Messaging).

Baixe o código completo deste tutorial no [GitHub](https://github.com/Azure/azure-notificationhubs-android/tree/v1-preview/notification-hubs-test-app-refresh).

Este tutorial cobre as seguintes etapas:

- Criar um projeto do Android Studio.
- Criar um projeto do Firebase que ofereça suporte ao Firebase Cloud Messaging.
- Criar um hub de notificação.
- Conectar seu aplicativo ao hub.
- Testar o aplicativo.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisa ter uma conta ativa do Azure. Se não tiver uma conta, você poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/free/).

Você também precisará dos seguintes itens:

- Recomendamos usar a última versão do [Android Studio](https://go.microsoft.com/fwlink/?LinkId=389797).
- O suporte mínimo é o nível da API 19.

## <a name="create-an-android-studio-project"></a>Criar um projeto do Android Studio

A primeira etapa é criar um projeto no Android Studio:

1. Inicie o Android Studio.

2. Selecione **Arquivo**, escolha **Novo** e **Novo Projeto**.

3. Na página **Escolher o projeto**, selecione **Atividade vazia** e, em seguida, selecione **Avançar**.

4. Na página **Configurar o projeto**, faça o seguinte:
   1. Insira um nome para o aplicativo.
   2. Especifique um local para salvar os arquivos do projeto.
   3. Selecione **Concluir**.

   :::image type="content" source="media/android-sdk/configure-project.png" alt-text="Configurar o projeto":::

## <a name="create-a-firebase-project-that-supports-fcm"></a>Crie um projeto do Firebase que ofereça suporte ao FCM

1. Faça logon no [console do Firebase](https://firebase.google.com/console/). Crie um novo projeto do Firebase se você ainda não tiver um.

2. Depois de criar seu projeto, selecione **Adicionar Firebase ao seu aplicativo Android**.

   :::image type="content" source="media/android-sdk/get-started.png" alt-text="Adicionar Firebase":::

3. Na página **Adicionar o Firebase ao seu aplicativo Android**, faça o seguinte:

   1. Em **Nome do pacote Android**, copie o valor da **applicationId** no arquivo **build.gradle** do aplicativo. Neste exemplo, é `com.fabrikam.fcmtutorial1app`.

      :::image type="content" source="media/android-sdk/specify-package-name-fcm-settings.png" alt-text="Especificar o nome do pacote":::

   2. Selecione **Registrar aplicativo**.

4. Selecione **Baixar google-services.json**, salve o arquivo na pasta **app** do projeto e, em seguida, selecione **Avançar**.

   :::image type="content" source="media/android-sdk/download-google-service-button.png" alt-text="Baixar o serviço do Google":::

5. No console do Firebase, selecione a engrenagem para seu projeto. Em seguida, selecione **Configurações do Projeto**.

   :::image type="content" source="media/android-sdk/notification-hubs-firebase-console-project-settings.png" alt-text="Configurações do projeto":::

6. Se você não tiver baixado o arquivo **google-services.json** para a pasta **aplicativo** do projeto do Android Studio, será possível fazer isso nesta página.

7. Alterne para a guia **Cloud Messaging**.

8. Copie e salve a **Chave do servidor** para uso posterior. Use esse valor para configurar o hub.

## <a name="configure-a-notification-hub"></a>Configurar um hub de notificação

1. Entre no [portal do Azure](https://portal.azure.com/).

2. Selecione **Todos os serviços** no menu à esquerda e **Hubs de Notificação** na seção **Móvel**. Selecione o ícone de estrela ao lado do nome do serviço para adicionar o serviço na seção **FAVORITOS** no menu à esquerda. Depois de adicionar os **Hubs de Notificação** a **FAVORITOS**, selecione-o no menu à esquerda.

3. Na página **Hubs de Notificação**, selecione **Adicionar** na barra de ferramentas.

   :::image type="content" source="media/android-sdk/add-hub.png" alt-text="Adicionar hub":::

4. Na página **Hubs de Notificação**, faça o seguinte:

   1. Insira um nome em **Hub de Notificação**.

   2. Insira um nome em **Criar um namespace**. Um namespace contém um ou mais hubs.

   3. Selecione um valor na lista suspensa **Localização**. Esse valor especifica a localização na qual você deseja criar o hub.

   4. Selecione um grupo de recursos existente em **Grupo de Recursos** ou crie um.

   5. Selecione **Criar**.

      :::image type="content" source="media/android-sdk/create-hub.png" alt-text="Criar o hub":::

5. Selecione **Notificações** (ícone de sino) e selecione **Acessar recurso**. Atualize também a lista na página **Hubs de Notificação** e, em seguida, selecione seu hub.

   :::image type="content" source="media/android-sdk/notification-hubs.png" alt-text="Selecionar o hub":::

6. Selecione **Políticas de Acesso** na lista. Observe que duas cadeias de conexão estão disponíveis. Você precisará delas para manipular notificações por push mais tarde.

   :::image type="content" source="media/android-sdk/access-policies.png" alt-text="Políticas de acesso":::

   > [!IMPORTANT]
   > Não use a política **DefaultFullSharedAccessSignature** no aplicativo. Essa política deverá ser usada somente no back-end do aplicativo.

## <a name="configure-firebase-cloud-messaging-settings-for-the-hub"></a>Definir as configurações do Firebase Cloud Messaging para o hub

1. No painel esquerdo, em **Configurações**, selecione **Google (GCM/FCM)** .

2. Insira a **chave do servidor** do projeto do FCM, salva anteriormente.

3. Na barra de ferramentas, selecione **Salvar**.

   :::image type="content" source="media/android-sdk/fcm-server-key.png" alt-text="Chave do servidor":::

4. O portal do Azure exibirá uma mensagem informando que o hub foi atualizado com êxito. O botão **Salvar** está desabilitado.

O hub de notificação agora está configurado para trabalhar com o Firebase Cloud Messaging. Você também tem as cadeias de caracteres de conexão que são necessárias para enviar notificações para um dispositivo e registrar um aplicativo para receber notificações.

## <a name="connect-your-app-to-the-notification-hub"></a>Conectar seu aplicativo ao hub de notificação

### <a name="add-google-play-services-to-the-project"></a>Adicionar serviços do Google Play ao projeto

1. No Android Studio, selecione **Ferramentas** no menu e selecione **Gerenciador do SDK**.

2. Selecione a versão de destino do SDK do Android que é usada em seu projeto. Em seguida, selecione **Mostrar detalhes do pacote**.

   :::image type="content" source="media/android-sdk/notification-hubs-android-studio-sdk-manager.png" alt-text="Gerenciador de SDK":::

3. Selecione **APIs do Google**, caso ainda não estejam instaladas.

   :::image type="content" source="media/android-sdk/google-apis-selected.png" alt-text="APIs":::

4. Alterne para a guia **SDK Tools**. Caso ainda não tenha instalado o Google Play Services, selecione **Google Play Services**, conforme mostrado na imagem a seguir. Em seguida, selecione **Aplicar** para instalar. Anote o caminho do SDK, a ser usado em uma etapa posterior.

   :::image type="content" source="media/android-sdk/google-play-services-selected.png" alt-text="Play Services":::

5. Se a caixa de diálogo **Confirmar Alteração** for exibida, selecione **OK**. O instalador de componentes instalará os componentes solicitados. Selecione **Concluir** depois que os componentes forem instalados.

6. Selecione **OK** para fechar a caixa de diálogo **Configurações para Novos Projetos**.

### <a name="add-azure-notification-hubs-libraries"></a>Adicionar bibliotecas de Hubs de Notificação do Azure

1. No arquivo **build.gradle** do aplicativo, adicione as seguintes linhas à seção de dependências:

   ```gradle
   implementation 'com.microsoft.azure:notification-hubs-android-sdk-fcm:1.1.4'
   implementation 'androidx.appcompat:appcompat:1.0.0'
   ```

2. Adicione o seguinte repositório após a seção de dependências:

   ```gradle
   repositories {
      maven {
         url "https://dl.bintray.com/microsoftazuremobile/SDK"
      }
   }
   ```

### <a name="add-google-firebase-support"></a>Adicionar suporte ao Google Firebase

1. Adicione o plug-in a seguir ao final do arquivo, caso ele ainda não esteja lá.

   ```gradle
   apply plugin: 'com.google.gms.google-services'
   ```

2. Selecione **Sincronizar Agora** na barra de ferramentas.

### <a name="add-code"></a>Incluir código

1. Crie um objeto **NotificationHubListener**, que processa a interceptação das mensagens dos Hubs de Notificação do Azure.

   ```java
   public class CustomNotificationListener implements NotificationListener {

      @override
      public void onNotificationReceived(Context context, RemoteMessage message) {
    
         /* The following notification properties are available. */
         Notification notification = message.getNotification();
         String title = notification.getTitle();
         String body = notification.getBody();
         Map<String, String> data = message.getData();
    
         if (message != null) {
            Log.d(TAG, "Message Notification Title: " + title);
            Log.d(TAG, "Message Notification Body: " + message);
         }

         if (data != null) {
             for (Map.Entry<String, String> entry : data.entrySet()) {
                 Log.d(TAG, "key, " + entry.getKey() + " value " + entry.getValue());
             }
         }
      }
   }
   ```

2. No método `OnCreate` da classe `MainActivity`, adicione o seguinte código para começar o processo de inicialização dos Hubs de Notificação quando a atividade for criada:

   ```java
   @Override
   protected void onCreate(Bundle savedInstanceState) {

      super.onCreate(savedInstanceState);
      setContentView(R.layout.activity\_main);
      NotificationHub.setListener(new CustomNotificationListener());
      NotificationHub.start(this.getApplication(), “Connection-String”, "Hub Name");

   }
   ```

3. No Android Studio, na barra de menus, selecione **Compilar** e **Recompilar Projeto** para verificar se não há erros no código. Se você receber um erro sobre o ícone **ic_launcher**, remova a seguinte instrução do arquivo AndroidManifest.xml:

   ```xml
   android:icon="@mipmap/ic_launcher"
   ```

4. Verifique se você tem um dispositivo virtual para executar o aplicativo. Se não tiver um, adicione da seguinte maneira:

   1. :::image type="content" source="media/android-sdk/open-device-manager.png" alt-text="Gerenciador de Dispositivos":::
   2. :::image type="content" source="media/android-sdk/your-virtual-devices.png" alt-text="Dispositivos virtuais":::
   3. Execute o aplicativo no dispositivo selecionado e confirme se ele foi registrado com êxito no hub.

      :::image type="content" source="media/android-sdk/device-registration.png" alt-text="Registro de dispositivos":::

      > [!NOTE]
      > O registro poderá falhar na primeira inicialização até que o método `onTokenRefresh()` do serviço de ID da instância seja chamado. Uma atualização deverá iniciar um registro bem-sucedido no hub de notificação.

## <a name="send-a-test-notification"></a>Enviar uma notificação de teste

Envie notificações por push ao hub de notificações por no [portal do Azure](https://portal.azure.com/) da seguinte maneira:

1. No portal do Azure, na página do hub de notificações do hub, selecione **Envio de Teste** na seção **Solução de Problemas**.

2. Em **Plataformas**, selecione **Android**.

3. Selecione **Enviar**. Você ainda não verá a notificação no dispositivo Android porque não executou o aplicativo móvel nele. Depois de executar o aplicativo móvel, selecione o botão **Enviar** novamente para ver a mensagem de notificação.

4. Confira o resultado da operação na lista na parte inferior da página do portal.

   :::image type="content" source="media/android-sdk/notification-hubs-test-send.png" alt-text="Enviar notificação de teste":::

5. Você verá a mensagem de notificação no dispositivo.

As notificações por push normalmente são enviadas em um serviço de back-end como Aplicativos Móveis ou ASP.NET usando uma biblioteca compatível. Se uma biblioteca não estiver disponível para o seu back-end, você também poderá usar a API REST diretamente para enviar mensagens de notificação.

## <a name="run-the-mobile-app-on-emulator"></a>Executar o aplicativo móvel no emulador

Antes de testar notificações por push em um emulador, verifique se a imagem do emulador dá suporte ao nível de API do Google que você escolheu para o aplicativo. Se a imagem não der suporte às APIs nativas do Google, você poderá receber a exceção **SERVICE_NOT_AVAILABLE**.

Além disso, verifique se você adicionou a conta do Google ao emulador em execução em **Configurações** > **Contas**. Caso contrário, suas tentativas de se registrar no FCM poderão resultar na exceção **AUTHENTICATION_FAILED**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você usou o Firebase Cloud Messaging para difundir notificações a todos os dispositivos Android que estavam registrados no serviço. Para saber como enviar notificações por push a dispositivos específicos, avance ao seguinte tutorial:

> [!div class="nextstepaction"]
>[Tutorial: Enviar notificações para usuários específicos](push-notifications-android-specific-users-firebase-cloud-messaging.md)

Esta é uma lista de alguns outros tutoriais sobre como enviar notificações:

- Aplicativos Móveis do Azure: para obter um exemplo de como enviar notificações de um back-end de Aplicativos Móveis integrado aos Hubs de Notificação, confira [Adicionar notificações por push ao seu aplicativo iOS](/previous-versions/azure/app-service-mobile/app-service-mobile-ios-get-started-push).

- ASP.NET: [use os Hubs de Notificação para enviar notificações por push aos usuários](notification-hubs-aspnet-backend-ios-apple-apns-notification.md).

- SDK do Java para Hubs de Notificação do Azure: confira [Como usar os Hubs de Notificação do Java](notification-hubs-java-push-notification-tutorial.md) para enviar notificações do Java. Isso foi testado no Eclipse para desenvolvimento no Android.

- PHP: [como usar Hubs de Notificação do PHP](notification-hubs-php-push-notification-tutorial.md).
