---
title: Enviar notificações por Push seguro com Hubs de Notificação do Azure
description: Saiba como enviar notificações por push seguro para um aplicativo Android do Azure. Exemplos de códigos escritos em Java e c#.
documentationcenter: android
keywords: Enviar notificação, notificações por push, mensagens por push, notificações por push do android
author: jwargo
manager: patniko
editor: spelluru
services: notification-hubs
ms.assetid: daf3de1c-f6a9-43c4-8165-a76bfaa70893
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: android
ms.devlang: java
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: 27536b0a3d7e0858a5660b4c7b33cb6679b5fbf1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60874540"
---
# <a name="sending-secure-push-notifications-with-azure-notification-hubs"></a>Enviar notificações por Push seguro com Hubs de Notificação do Azure

> [!div class="op_single_selector"]
> * [Windows Universal](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)

## <a name="overview"></a>Visão geral

> [!IMPORTANT]
> Para concluir este tutorial, você precisa ter uma conta ativa do Azure. Se não tiver uma conta, você poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-ios-get-started).

O suporte à notificação por push no Microsoft Azure permite que você acesse uma infraestrutura de envio de mensagem por push fácil de usar, multiplataforma e expansível que simplifica muito a implementação de notificações por push para aplicativos de consumidor e empresariais para plataformas móveis.

Devido a restrições regulatórias ou de segurança, às vezes, um aplicativo pode querer incluir algo na notificação que não pode ser transmitido por meio da infraestrutura de notificação por push padrão. Este tutorial descreve como obter a mesma experiência ao enviar informações confidenciais por meio de uma conexão segura e autenticada entre o dispositivo Android cliente e o back-end do aplicativo.

Em um nível superior, o fluxo é o seguinte:

1. O back-end do aplicativo:
   * Armazena uma carga segura no banco de dados de back-end.
   * Envia a ID dessa notificação ao dispositivo Android (nenhuma informação segura é enviada).
2. O dispositivo no aplicativo, ao receber a notificação:
   * O dispositivo Android entra em contato com o back-end solicitando a carga segura.
   * O aplicativo pode mostrar a carga como uma notificação no dispositivo.

É importante observar que no fluxo anterior (e neste tutorial), pressupomos que o dispositivo armazena um token de autenticação no armazenamento local depois que o usuário faz logon. Essa abordagem garante uma experiência perfeita, já que o dispositivo pode recuperar a carga de segurança da notificação usando este token. Se o seu aplicativo não armazenar tokens de autenticação no dispositivo, ou se esses tokens puderem expirar, o aplicativo do dispositivo, após receber a notificação por push, deve exibir uma notificação genérica solicitando que o usuário inicie o aplicativo. Dessa forma, o aplicativo autentica o usuário e mostra a carga de notificação.

Este tutorial mostra como enviar notificações por push seguro. Baseia-se no tutorial [Notify Users](notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md) (Notificar usuários), de modo que você deve concluir as etapas nesse tutorial, caso ainda não o tenha feito.

> [!NOTE]
> Este tutorial presume que você criou e configurou seu hub de notificação conforme descrito em [Introdução aos Hubs de Notificação (Android)](notification-hubs-android-push-notification-google-gcm-get-started.md).

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-android-project"></a>Modificar o projeto Android

Agora que você modificou o back-end do aplicativo para enviar apenas a *ID* de uma notificação por push, é preciso alterar o aplicativo Android para manipular essa notificação e retornar a chamada do back-end para recuperar a mensagem segura a ser exibida.
Para atingir essa meta, você deve se certificar de que seu aplicativo Android saiba como se autenticar com o back-end ao receber as notificações por push.

Agora, modifique o fluxo de *logon* para salvar o valor do cabeçalho de autenticação nas preferências compartilhadas de seu aplicativo. Mecanismos análogos podem ser usados para armazenar qualquer token de autenticação (por ex., tokens OAuth) que o aplicativo precisa usar sem solicitar as credenciais do usuário.

1. No projeto do aplicativo Android, adicione as seguintes constantes na parte superior da classe `MainActivity`:

    ```java
    public static final String NOTIFY_USERS_PROPERTIES = "NotifyUsersProperties";
    public static final String AUTHORIZATION_HEADER_PROPERTY = "AuthorizationHeader";
    ```
2. Ainda na classe `MainActivity`, atualize o método `getAuthorizationHeader()` para conter o seguinte código:

    ```java
    private String getAuthorizationHeader() throws UnsupportedEncodingException {
        EditText username = (EditText) findViewById(R.id.usernameText);
        EditText password = (EditText) findViewById(R.id.passwordText);
        String basicAuthHeader = username.getText().toString()+":"+password.getText().toString();
        basicAuthHeader = Base64.encodeToString(basicAuthHeader.getBytes("UTF-8"), Base64.NO_WRAP);

        SharedPreferences sp = getSharedPreferences(NOTIFY_USERS_PROPERTIES, Context.MODE_PRIVATE);
        sp.edit().putString(AUTHORIZATION_HEADER_PROPERTY, basicAuthHeader).commit();

        return basicAuthHeader;
    }
    ```
3. Adicione as seguintes instruções `import` na parte superior do arquivo `MainActivity`:

    ```java
    import android.content.SharedPreferences;
    ```

Agora, altere o manipulador que é chamado quando a notificação é recebida.

1. Na classe `MyHandler`, altere o método `OnReceive()` para conter:

    ```java
    public void onReceive(Context context, Bundle bundle) {
        ctx = context;
        String secureMessageId = bundle.getString("secureId");
        retrieveNotification(secureMessageId);
    }
    ```
2. Em seguida, adicione o método `retrieveNotification()`, substituindo o espaço reservado `{back-end endpoint}` pelo ponto de extremidade do back-end obtido ao implantar seu back-end:

    ```java
    private void retrieveNotification(final String secureMessageId) {
        SharedPreferences sp = ctx.getSharedPreferences(MainActivity.NOTIFY_USERS_PROPERTIES, Context.MODE_PRIVATE);
        final String authorizationHeader = sp.getString(MainActivity.AUTHORIZATION_HEADER_PROPERTY, null);

        new AsyncTask<Object, Object, Object>() {
            @Override
            protected Object doInBackground(Object... params) {
                try {
                    HttpUriRequest request = new HttpGet("{back-end endpoint}/api/notifications/"+secureMessageId);
                    request.addHeader("Authorization", "Basic "+authorizationHeader);
                    HttpResponse response = new DefaultHttpClient().execute(request);
                    if (response.getStatusLine().getStatusCode() != HttpStatus.SC_OK) {
                        Log.e("MainActivity", "Error retrieving secure notification" + response.getStatusLine().getStatusCode());
                        throw new RuntimeException("Error retrieving secure notification");
                    }
                    String secureNotificationJSON = EntityUtils.toString(response.getEntity());
                    JSONObject secureNotification = new JSONObject(secureNotificationJSON);
                    sendNotification(secureNotification.getString("Payload"));
                } catch (Exception e) {
                    Log.e("MainActivity", "Failed to retrieve secure notification - " + e.getMessage());
                    return e;
                }
                return null;
            }
        }.execute(null, null, null);
    }
    ```

Esse método chama o back-end do seu aplicativo para recuperar o conteúdo da notificação usando as credenciais armazenadas nas preferências compartilhadas e o exibe como uma notificação normal. Para o usuário do aplicativo, a notificação tem exatamente a mesma aparência que qualquer outra notificação por push.

É preferível manipular os casos de propriedade de cabeçalho de autenticação ausente ou de rejeição por meio do back-end. A manipulação específica desses casos depende em grande parte da experiência do usuário-alvo. Uma opção é exibir uma notificação com um aviso genérico para que o usuário realize a autenticação para recuperar a notificação real.

## <a name="run-the-application"></a>Executar o aplicativo

Para executar o aplicativo, faça as seguintes ações:

1. Certifique-se de que o **AppBackend** esteja implantado no Azure. Se estiver usando o Visual Studio, execute o aplicativo da API Web **AppBackend** . Uma página da Web do ASP.NET é exibida.
2. No Eclipse, execute o aplicativo em um dispositivo Android físico ou no emulador.
3. Na interface do usuário do aplicativo Android, insira um nome de usuário e senha. Pode ser qualquer cadeia de caracteres, mas devem ter o mesmo valor.
4. Na interface do usuário do aplicativo Android, clique em **Logon**. Em seguida, clique em **Enviar push**.
