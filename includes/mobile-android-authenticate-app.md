---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 11/25/2018
ms.author: crdun
ms.openlocfilehash: eded2d6a9f2c270a2b3ccca296277b0a016733fd
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172609"
---
1. Abra o projeto no Android Studio.

2. No **Gerenciador de Projetos**, no Android Studio, abra o arquivo `ToDoActivity.java` e adicione as seguintes instruções de importação:

    ```java
    import java.util.concurrent.ExecutionException;
    import java.util.concurrent.atomic.AtomicBoolean;

    import android.content.Context;
    import android.content.SharedPreferences;
    import android.content.SharedPreferences.Editor;

    import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceAuthenticationProvider;
    import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceUser;
    ```

3. Adicione o método a seguir à classe **ToDoActivity** :

    ```java
    // You can choose any unique number here to differentiate auth providers from each other. Note this is the same code at login() and onActivityResult().
    public static final int GOOGLE_LOGIN_REQUEST_CODE = 1;

    private void authenticate() {
        // Sign in using the Google provider.
        mClient.login(MobileServiceAuthenticationProvider.Google, "{url_scheme_of_your_app}", GOOGLE_LOGIN_REQUEST_CODE);
    }

    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        // When request completes
        if (resultCode == RESULT_OK) {
            // Check the request code matches the one we send in the login request
            if (requestCode == GOOGLE_LOGIN_REQUEST_CODE) {
                MobileServiceActivityResult result = mClient.onActivityResult(data);
                if (result.isLoggedIn()) {
                    // sign-in succeeded
                    createAndShowDialog(String.format("You are now signed in - %1$2s", mClient.getCurrentUser().getUserId()), "Success");
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

    Esse código cria um novo método para manipular o processo de autenticação. Um diálogo exibe a ID do usuário autenticado. Você só pode continuar em uma autenticação bem-sucedida.

    > [!NOTE]
    > Se você estiver usando um provedor de identidade diferente do Google, altere o valor passado para o **login** método a um dos seguintes valores: _MicrosoftAccount_, _Facebook_, _Twitter_, ou _windowsazureactivedirectory_.

4. No método **OnCreate**, adicione a linha de código a seguir após o código que cria uma instância do objeto `MobileServiceClient`.

    ```java
    authenticate();
    ```

    Essa chamada inicia o processo de autenticação.

5. Mova o código restante após `authenticate();` no método **OnCreate** para um novo método **CreateTable**:

    ```java
    private void createTable() {

        // Get the table instance to use.
        mToDoTable = mClient.getTable(ToDoItem.class);

        mTextNewToDo = (EditText) findViewById(R.id.textNewToDo);

        // Create an adapter to bind the items with the view.
        mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
        ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
        listViewToDo.setAdapter(mAdapter);

        // Load the items from Azure.
        refreshItemsFromTable();
    }
    ```

6. Para garantir que o redirecionamento funcione conforme o esperado, adicione o seguinte snippet de `RedirectUrlActivity` a `AndroidManifest.xml`:

    ```xml
    <activity android:name="com.microsoft.windowsazure.mobileservices.authentication.RedirectUrlActivity">
        <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="{url_scheme_of_your_app}"
                android:host="easyauth.callback"/>
        </intent-filter>
    </activity>
    ```

7. Adicione `redirectUriScheme` a `build.gradle` do seu aplicativo Android.

    ```gradle
    android {
        buildTypes {
            release {
                // ...
                manifestPlaceholders = ['redirectUriScheme': '{url_scheme_of_your_app}://easyauth.callback']
            }
            debug {
                // ...
                manifestPlaceholders = ['redirectUriScheme': '{url_scheme_of_your_app}://easyauth.callback']
            }
        }
    }
    ```

8. Adicione `com.android.support:customtabs:23.0.1` às dependências em seu `build.gradle`:

    ```gradle
    dependencies {
        // ...
        compile 'com.android.support:customtabs:23.0.1'
    }
    ```

9. No menu **Executar**, clique em **Executar aplicativo** para iniciar o aplicativo e entrar com seu provedor de identidade escolhido.

> [!WARNING]
> O esquema de URL mencionado diferencia maiúsculas de minúsculas. Certifique-se de que todas as ocorrências de `{url_scheme_of_you_app}` a mesma formatação, maiúsculas ou minúsculas.

Ao entrar com êxito, o aplicativo deve ser executado sem erros e você deve ser capaz de consultar o serviço de back-end e fazer atualizações nos dados.
