---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 11/25/2018
ms.author: crdun
ms.openlocfilehash: deb94cab97bd9a402676cdc5c0239da8d07ed8b2
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172610"
---
O exemplo anterior mostrou um logon padrão, que requer que o cliente contate o provedor de identidade e o serviço de back-end do Azure sempre que o aplicativo for iniciado. Esse método é ineficiente e você pode ter problemas relacionados ao uso caso muitos clientes tentem iniciar o aplicativo simultaneamente. Uma abordagem melhor é armazenar em cache o token de autorização retornado pelo serviço do Azure e tentar usá-lo antes de usar um logon baseado em provedor.

> [!NOTE]
> Você pode armazenar em cache o token emitido pelo serviço de back-end do Azure usando tanto a autenticação gerenciada pelo cliente quanto a autenticação gerenciada pelo serviço. Este tutorial usa a autenticação gerenciada pelo serviço.
>
>

1. Abra o arquivo ToDoActivity.java e adicione as seguintes instruções de importação:

    ```java
    import android.content.Context;
    import android.content.SharedPreferences;
    import android.content.SharedPreferences.Editor;
    ```

2. Adicione os seguintes membros à classe `ToDoActivity` :

    ```java
    public static final String SHAREDPREFFILE = "temp";
    public static final String USERIDPREF = "uid";
    public static final String TOKENPREF = "tkn";
    ```

3. No arquivo ToDoActivity.java, adicione a seguinte definição para o método `cacheUserToken`.

    ```java
    private void cacheUserToken(MobileServiceUser user)
    {
        SharedPreferences prefs = getSharedPreferences(SHAREDPREFFILE, Context.MODE_PRIVATE);
        Editor editor = prefs.edit();
        editor.putString(USERIDPREF, user.getUserId());
        editor.putString(TOKENPREF, user.getAuthenticationToken());
        editor.commit();
    }
    ```

    Este método armazena a ID e o token do usuário em um arquivo preferencial marcado como privado. Isto deve proteger o acesso ao cache para que os outros aplicativos do dispositivo não tenham acesso ao token. A preferência é uma área restrita para o aplicativo. No entanto, se alguém obtiver acesso ao dispositivo, é possível que a pessoa ganhe acesso ao cache de token de outras formas.

   > [!NOTE]
   > Você pode proteger ainda mais o token com criptografia se o acesso do token a seus dados for considerado altamente confidencial e alguém puder acessar o dispositivo. No entanto, uma solução completamente segura está além do escopo deste tutorial e depende dos seus requisitos de segurança.
   >
   >

4. No arquivo ToDoActivity.java, adicione a seguinte definição para o método `loadUserTokenCache`.

    ```java
    private boolean loadUserTokenCache(MobileServiceClient client)
    {
        SharedPreferences prefs = getSharedPreferences(SHAREDPREFFILE, Context.MODE_PRIVATE);
        String userId = prefs.getString(USERIDPREF, null);
        if (userId == null)
            return false;
        String token = prefs.getString(TOKENPREF, null);
        if (token == null)
            return false;

        MobileServiceUser user = new MobileServiceUser(userId);
        user.setAuthenticationToken(token);
        client.setCurrentUser(user);

        return true;
    }
    ```

5. No arquivo *ToDoActivity.java*, substitua os métodos `authenticate` e `onActivityResult` pelos seguintes métodos, que usam um cache de token. Altere o provedor de logon se quiser usar uma conta que não seja do Google.

    ```java
    private void authenticate() {
        // We first try to load a token cache if one exists.
        if (loadUserTokenCache(mClient))
        {
            createTable();
        }
        // If we failed to load a token cache, sign in and create a token cache
        else
        {
            // Sign in using the Google provider.
            mClient.login(MobileServiceAuthenticationProvider.Google, "{url_scheme_of_your_app}", GOOGLE_LOGIN_REQUEST_CODE);
        }
    }

    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        // When request completes
        if (resultCode == RESULT_OK) {
            // Check the request code matches the one we send in the sign-in request
            if (requestCode == GOOGLE_LOGIN_REQUEST_CODE) {
                MobileServiceActivityResult result = mClient.onActivityResult(data);
                if (result.isLoggedIn()) {
                    // sign-in succeeded
                    createAndShowDialog(String.format("You are now signed in - %1$2s", mClient.getCurrentUser().getUserId()), "Success");
                    cacheUserToken(mClient.getCurrentUser());
                    createTable();
                } else {
                    // sign-in failed, check the error message
                    String errorMessage = result.getErrorMessage();
                    createAndShowDialog(errorMessage, "Error");
                }
            }
        }
    }
    ```

6. Compile o aplicativo e teste a autenticação usando uma conta válida. Execute pelo menos uma vez. Durante a primeira execução, você deverá ser solicitado a iniciar uma sessão e criar o cache do token. Depois disso, cada execução tentará carregar o cache de token para autenticação. Não deve ser solicitado que você inicie uma sessão.
