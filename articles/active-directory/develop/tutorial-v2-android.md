---
title: Introdução ao Android – Plataforma de identidade da Microsoft | Azure
description: Como um aplicativo do Android pode obter um token de acesso e chamar a API do Microsoft Graph ou APIs que exigem tokens de acesso por meio da plataforma de identidade da Microsoft.
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/09/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5bd19b6094d68277130916b5cda565ba9e633c59
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68334115"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-android-app"></a>Conectar usuários e chamar o Microsoft Graph em um aplicativo Android

Neste tutorial, você aprenderá como integrar um aplicativo Android à plataforma de identidade da Microsoft. Seu aplicativo conectará um usuário, obterá um token de acesso para chamar a API do Microsoft Graph e fará uma solicitação para a API do Microsoft Graph.  

Após concluir este guia, seu aplicativo aceitará conexões de contas Microsoft pessoais (incluindo outlook.com, live.com e outras) e contas corporativas ou de estudante de qualquer empresa ou organização que utilize o Azure Active Directory.

## <a name="how-this-tutorial-works"></a>Como este tutorial funciona

![Mostra como funciona o aplicativo de exemplo gerado por este tutorial](../../../includes/media/active-directory-develop-guidedsetup-android-intro/android-intro.svg)

O aplicativo deste tutorial conectará usuários e obterá dados no nome deles.  Esses dados serão acessados por meio de uma API protegida (API do Microsoft Graph) que exige autorização e é protegida pela plataforma de identidade da Microsoft.

Mais especificamente:

* Seu aplicativo conectará o usuário por meio de um navegador ou do Microsoft Authenticator e pelo Portal da Empresa do Intune.
* O usuário final aceitará as permissões solicitadas por seu aplicativo.
* Será emitido um token de acesso para seu aplicativo para a API do Microsoft Graph.
* O token de acesso será incluído na solicitação HTTP para API Web.
* Processe a resposta do Microsoft Graph.

Esta amostra usa a MSAL (Biblioteca de Autenticação da Microsoft) para Android para implementar a Autenticação: [com.microsoft.identity.client](https://javadoc.io/doc/com.microsoft.identity.client/msal).

 A MSAL automaticamente renovará tokens, fornecerá o SSO (logon único) entre outros aplicativos no dispositivo e ajudará a gerenciar as contas.

## <a name="prerequisites"></a>Pré-requisitos

* Este tutorial exige o Android Studio versão 16 ou posterior (19 e posterior é recomendado).

## <a name="create-a-project"></a>Criar um projeto

Este tutorial irá criar um novo projeto. Se, ao invés disso, você quiser baixar o tutorial concluído, [baixe o código](https://github.com/Azure-Samples/active-directory-android-native-v2/archive/master.zip).

1. Abra o Android Studio e selecione **Iniciar um novo projeto do Android Studio**.
2. Selecione **Atividade Básica** e, em seguida, **Avançar**.
3. Nome do seu aplicativo.
4. Guarde o nome do pacote. Você o inserirá mais tarde no portal do Azure.
5. Defina o **Nível mínimo da API** como **API 19** ou superior e clique em **Concluir**.
6. Na exibição do projeto, escolha **Projeto** na lista suspensa para exibir os arquivos do projeto que são e que não são a fonte, abra **app/build.gradle** e defina `targetSdkVersion` como `27`.

## <a name="register-your-application"></a>Registre seu aplicativo

1. Vá para o [Portal do Azure](https://aka.ms/MobileAppReg).
2. Abra a folha [Registros de aplicativo](https://ms.portal.azure.com/?feature.broker=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) e clique em **+Novo registro**.
3. Insira um **Nome** para seu aplicativo e, em seguida, sem definir um URI de Redirecionamento, clique em **Registrar**.
4. Na seção **Gerenciar** do painel que aparece, selecione **Autenticação** >  **+ Adicionar uma plataforma** > **Android**.
5. Insira o nome do pacote do seu projeto. Se você baixou o código, esse valor é `com.azuresamples.msalandroidapp`.
6. Na seção **Hash de assinatura** da página **Configurar seu aplicativo Android**, clique em **Gerar um hash de assinatura de desenvolvimento.** e copie o comando KeyTool para usar para sua plataforma.

   > [!Note]
   > KeyTool.exe é instalado como parte do JDK (Java Development Kit). Você também precisará instalar a ferramenta OpenSSL para executar o comando KeyTool.

7. Insira o **Hash de assinatura** gerado por KeyTool.
8. Clique em `Configure` e salve a **Configuração da MSAL** exibida na página **Configuração do Android** para que você possa inseri-la quando configurar o aplicativo mais tarde.  Clique em **Concluído**.

## <a name="build-your-app"></a>Compilar seu aplicativo

### <a name="add-your-app-registration"></a>Adicionar o registro do seu aplicativo

1. No painel do projeto do Android Studio, navegue até **app\src\main\res**.
2. Clique com o botão direito do mouse em **res** e escolha **Novo** > **Diretório**. Insira `raw` como o nome do novo diretório e clique em **OK**.
3. Em **app** > **src** > **res** > **raw**, crie um novo arquivo JSON chamado `auth_config.json` e cole a Configuração da MSAL que você salvou anteriormente. Confira [Configuração de MSAL para obter mais informações](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki/Configuring-your-app).
4. Em **app** > **src** > **main** > **AndroidManifest.xml**, adicione a atividade `BrowserTabActivity` abaixo. Essa entrada permite que a Microsoft faça uma chamada de retorno ao aplicativo após concluir a autenticação:

    ```xml
    <!--Intent filter to capture System Browser or Authenticator calling back to our app after sign-in-->
    <activity
        android:name="com.microsoft.identity.client.BrowserTabActivity">
        <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="msauth"
                android:host="Enter_the_Package_Name"
                android:path="/Enter_the_Signature_Hash" />
        </intent-filter>
    </activity>
    ```

    Substitua o nome do pacote que você registrou no portal do Azure para o valor `android:host=`.
    Substitua o hash da chave que você registrou no portal do Azure para o valor `android:path=`. O hash de assinatura não deve ser codificado por URL.

5. Dentro do **AndroidManifest.xml**, logo acima da tag `<application>`, adicione as seguintes permissões:

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    ```

### <a name="create-the-apps-ui"></a>Criar a interface do usuário do aplicativo

1. Na janela de projeto do Android Studio, navegue até **app** > **src** > **main** > **res** > **layout**, abra **activity_main.xml** e abra o modo de exibição de **Texto**.
2. Altere o layout da atividade, por exemplo: de `<androidx.coordinatorlayout.widget.CoordinatorLayout` para `<androidx.coordinatorlayout.widget.LinearLayout`.
3. Adicionar o propriedade `android:orientation="vertical"` ao nó `LinearLayout`.
4. Cole o seguinte código no nó `LinearLayout`, substituindo o conteúdo atual:

    ```xml
    <TextView
        android:text="Welcome, "
        android:textColor="#3f3f3f"
        android:textSize="50px"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginLeft="10dp"
        android:layout_marginTop="15dp"
        android:id="@+id/welcome"
        android:visibility="invisible"/>

    <Button
        android:id="@+id/callGraph"
        android:text="Call Microsoft Graph"
        android:textColor="#FFFFFF"
        android:background="#00a1f1"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="200dp"
        android:textAllCaps="false" />

    <TextView
        android:text="Getting Graph Data..."
        android:textColor="#3f3f3f"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="5dp"
        android:id="@+id/graphData"
        android:visibility="invisible"/>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="0dip"
        android:layout_weight="1"
        android:gravity="center|bottom"
        android:orientation="vertical" >

        <Button
            android:text="Sign Out"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_marginBottom="15dp"
            android:textColor="#FFFFFF"
            android:background="#00a1f1"
            android:textAllCaps="false"
            android:id="@+id/clearCache"
            android:visibility="invisible" />
    </LinearLayout>
    ```

### <a name="add-msal-to-your-project"></a>Adicionar MSAL ao seu projeto

1. Na janela do projeto do Android Studio, navegue até **app** > **src** > **gradle**.
2. Em **Dependências**, cole o seguinte:

    ```gradle  
    implementation 'com.android.volley:volley:1.1.1'
    implementation 'com.microsoft.identity.client:msal:0.3.+'
    ```

### <a name="use-msal"></a>Usar a MSAL

Agora, faça alterações dentro de `MainActivity.java` para adicionar e usar a MSAL em seu aplicativo.
Na janela do projeto do Android Studio, navegue até **app** > **src** > **main** > **java** > **com.example.msal** e abra `MainActivity.java`.

#### <a name="required-imports"></a>Importações necessárias

Adicione as seguintes importações próximo à parte superior de `MainActivity.java`:

```java
import android.app.Activity;
import android.content.Intent;
import android.util.Log;
import android.view.View;
import android.widget.Button;
import android.widget.TextView;
import android.widget.Toast;
import com.android.volley.*;
import com.android.volley.toolbox.JsonObjectRequest;
import com.android.volley.toolbox.Volley;
import org.json.JSONObject;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
import com.microsoft.identity.client.*;
import com.microsoft.identity.client.exception.*;
```

#### <a name="instantiate-msal"></a>Criar uma instância da MSAL

Na classe `MainActivity`, precisaremos criar uma instância da MSAL juntamente com algumas configurações sobre o que o aplicativo fará, incluindo os escopos e a API Web que desejamos acessar.

Copie as seguintes variáveis dentro da classe `MainActivity`:

```java
final static String SCOPES [] = {"https://graph.microsoft.com/User.Read"};
final static String MSGRAPH_URL = "https://graph.microsoft.com/v1.0/me";

/* UI & Debugging Variables */
private static final String TAG = MainActivity.class.getSimpleName();
Button callGraphButton;
Button signOutButton;

/* Azure AD Variables */
private PublicClientApplication sampleApp;
private IAuthenticationResult authResult;
```

Substitua o conteúdo de `onCreate()` pelo código a seguir para criar a instância da MSAL:

```java
super.onCreate(savedInstanceState);
setContentView(R.layout.activity_main);

callGraphButton = (Button) findViewById(R.id.callGraph);
signOutButton = (Button) findViewById(R.id.clearCache);

callGraphButton.setOnClickListener(new View.OnClickListener() {
    public void onClick(View v) {
        onCallGraphClicked();
    }
});

signOutButton.setOnClickListener(new View.OnClickListener() {
    public void onClick(View v) {
        onSignOutClicked();
    }
});

/* Configure your sample app and save state for this activity */
sampleApp = new PublicClientApplication(
        this.getApplicationContext(),
        R.raw.auth_config);

/* Attempt to get a user and acquireTokenSilent */
sampleApp.getAccounts(new PublicClientApplication.AccountsLoadedCallback() {
    @Override
    public void onAccountsLoaded(final List<IAccount> accounts) {
        if (!accounts.isEmpty()) {
            /* This sample doesn't support multi-account scenarios, use the first account */
            sampleApp.acquireTokenSilentAsync(SCOPES, accounts.get(0), getAuthSilentCallback());
        } else {
            /* No accounts */
        }
    }
});
```

O código acima tenta conectar silenciosamente os usuários quando eles abrem o aplicativo por meio de `getAccounts()` e, se bem-sucedidos, `acquireTokenSilentAsync()`.  Nas próximas seções, implementaremos o manipulador de retorno de chamada para o caso de não haver contas conectadas.

#### <a name="use-msal-to-get-tokens"></a>Usar a MSAL para obter tokens

Agora, podemos implementar a interface de usuário do aplicativo lógica de processamento e a obtenção de tokens interativamente por meio da MSAL.

A MSAL expõe dois métodos principais para obter tokens: `acquireTokenSilentAsync()` e `acquireToken()`.  

`acquireTokenSilentAsync()` conecta um usuário e obtém tokens sem qualquer interação do usuário se uma conta estiver presente. Se tiver êxito, a MSAL enviará os tokens para seu aplicativo, se falhar, ela irá gerar um `MsalUiRequiredException`.  Se essa exceção for gerada ou se você quiser que seu usuário tenha uma experiência de conexão interativa (credenciais, MFA ou outras políticas de acesso condicional podem ou não ser necessárias), use `acquireToken()`.  

`acquireToken()` exibe a interface do usuário ao tentar conectar o usuário e obter tokens. No entanto, ele pode usar cookies de sessão no navegador ou uma conta no Microsoft Authenticator para fornecer uma experiência de SSO interativo.

Crie os três métodos de interface do usuário a seguir dentro da classe `MainActivity`:

```java
/* Set the UI for successful token acquisition data */
private void updateSuccessUI() {
    callGraphButton.setVisibility(View.INVISIBLE);
    signOutButton.setVisibility(View.VISIBLE);
    findViewById(R.id.welcome).setVisibility(View.VISIBLE);
    ((TextView) findViewById(R.id.welcome)).setText("Welcome, " +
            authResult.getAccount().getUsername());
    findViewById(R.id.graphData).setVisibility(View.VISIBLE);
}

/* Set the UI for signed out account */
private void updateSignedOutUI() {
    callGraphButton.setVisibility(View.VISIBLE);
    signOutButton.setVisibility(View.INVISIBLE);
    findViewById(R.id.welcome).setVisibility(View.INVISIBLE);
    findViewById(R.id.graphData).setVisibility(View.INVISIBLE);
    ((TextView) findViewById(R.id.graphData)).setText("No Data");

    Toast.makeText(getBaseContext(), "Signed Out!", Toast.LENGTH_SHORT)
            .show();
}

/* Use MSAL to acquireToken for the end-user
 * Callback will call Graph api w/ access token & update UI
 */
private void onCallGraphClicked() {
    sampleApp.acquireToken(getActivity(), SCOPES, getAuthInteractiveCallback());
}
```

Adicione os seguintes métodos para obter a atividade atual e processar retornos de chamada silenciosos e interativos:

```java
public Activity getActivity() {
    return this;
}

/* Callback used in for silent acquireToken calls.
 * Looks if tokens are in the cache (refreshes if necessary and if we don't forceRefresh)
 * else errors that we need to do an interactive request.
 */
private AuthenticationCallback getAuthSilentCallback() {
    return new AuthenticationCallback() {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            /* Successfully got a token, call graph now */
            Log.d(TAG, "Successfully authenticated");

            /* Store the authResult */
            authResult = authenticationResult;

            /* call graph */
            callGraphAPI();

            /* update the UI to post call graph state */
            updateSuccessUI();
        }

        @Override
        public void onError(MsalException exception) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());

            if (exception instanceof MsalClientException) {
                /* Exception inside MSAL, more info inside the exception */
            } else if (exception instanceof MsalServiceException) {
                /* Exception when communicating with the STS, likely config issue */
            } else if (exception instanceof MsalUiRequiredException) {
                /* Tokens expired or no session, retry with interactive */
            }
        }

        @Override
        public void onCancel() {
            /* User cancelled the authentication */
            Log.d(TAG, "User cancelled login.");
        }
    };
}

/* Callback used for interactive request.  If succeeds we use the access
 * token to call the Microsoft Graph. Does not check cache
 */
private AuthenticationCallback getAuthInteractiveCallback() {
    return new AuthenticationCallback() {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            /* Successfully got a token, call graph now */
            Log.d(TAG, "Successfully authenticated");
            Log.d(TAG, "ID Token: " + authenticationResult.getIdToken());

            /* Store the auth result */
            authResult = authenticationResult;

            /* call graph */
            callGraphAPI();

            /* update the UI to post call graph state */
            updateSuccessUI();
        }

        @Override
        public void onError(MsalException exception) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());

            if (exception instanceof MsalClientException) {
                /* Exception inside MSAL, more info inside the exception */
            } else if (exception instanceof MsalServiceException) {
                /* Exception when communicating with the STS, likely config issue */
            }
        }

        @Override
        public void onCancel() {
            /* User cancelled the authentication */
            Log.d(TAG, "User cancelled login.");
        }
    };
}
```

#### <a name="use-msal-for-sign-out"></a>Usar a MSAL para saída

Em seguida, adicione o suporte para saída.

> [!Important]
> A saída com a MSAL remove todas as informações conhecidas sobre um usuário deste aplicativo, mas o usuário ainda terá uma sessão ativa em seu dispositivo. Se o usuário tentar entrar novamente, ele poderá ver a interface do usuário de entrada, mas talvez não precise inserir novamente suas credenciais, pois a sessão do dispositivo ainda estará ativa.

Para adicionar funcionalidade de saída, adicione o seguinte método dentro da classe `MainActivity`. Esse método percorre todas as contas e as remove:

```java
/* Clears an account's tokens from the cache.
 * Logically similar to "sign out" but only signs out of this app.
 * User will get interactive SSO if trying to sign back-in.
 */
private void onSignOutClicked() {
    /* Attempt to get a user and acquireTokenSilent
     * If this fails we do an interactive request
     */
    sampleApp.getAccounts(new PublicClientApplication.AccountsLoadedCallback() {
        @Override
        public void onAccountsLoaded(final List<IAccount> accounts) {

            if (accounts.isEmpty()) {
                /* No accounts to remove */

            } else {
                for (final IAccount account : accounts) {
                    sampleApp.removeAccount(
                            account,
                            new PublicClientApplication.AccountsRemovedCallback() {
                        @Override
                        public void onAccountsRemoved(Boolean isSuccess) {
                            if (isSuccess) {
                                /* successfully removed account */
                            } else {
                                /* failed to remove account */
                            }
                        }
                    });
                }
            }

            updateSignedOutUI();
        }
    });
}
```

#### <a name="call-the-microsoft-graph-api"></a>Chamar a API do Microsoft Graph

Após recebermos um token, podemos fazer uma solicitação para a [API do Microsoft Graph](https://graph.microsoft.com). O token de acesso estará dentro de `AuthenticationResult` dentro do método `onSuccess()` do retorno de chamada de autenticação. Para criar uma solicitação autorizada, seu aplicativo precisará adicionar o token de acesso ao cabeçalho HTTP:

| chave de cabeçalho    | value                 |
| ------------- | --------------------- |
| Autorização | Bearer \<access-token> |

Adicione os dois métodos a seguir dentro da classe `MainActivity` para o grafo de chamadas e atualize a interface do usuário:

```java
/* Use Volley to make an HTTP request to the /me endpoint from MS Graph using an access token */
private void callGraphAPI() {
    Log.d(TAG, "Starting volley request to graph");

    /* Make sure we have a token to send to graph */
    if (authResult.getAccessToken() == null) {return;}

    RequestQueue queue = Volley.newRequestQueue(this);
    JSONObject parameters = new JSONObject();

    try {
        parameters.put("key", "value");
    } catch (Exception e) {
        Log.d(TAG, "Failed to put parameters: " + e.toString());
    }
    JsonObjectRequest request = new JsonObjectRequest(Request.Method.GET, MSGRAPH_URL,
            parameters,new Response.Listener<JSONObject>() {
        @Override
        public void onResponse(JSONObject response) {
            /* Successfully called graph, process data and send to UI */
            Log.d(TAG, "Response: " + response.toString());

            updateGraphUI(response);
        }
    }, new Response.ErrorListener() {
        @Override
        public void onErrorResponse(VolleyError error) {
            Log.d(TAG, "Error: " + error.toString());
        }
    }) {
        @Override
        public Map<String, String> getHeaders() {
            Map<String, String> headers = new HashMap<>();
            headers.put("Authorization", "Bearer " + authResult.getAccessToken());
            return headers;
        }
    };

    Log.d(TAG, "Adding HTTP GET to Queue, Request: " + request.toString());

    request.setRetryPolicy(new DefaultRetryPolicy(
            3000,
            DefaultRetryPolicy.DEFAULT_MAX_RETRIES,
            DefaultRetryPolicy.DEFAULT_BACKOFF_MULT));
    queue.add(request);
}

/* Sets the graph response */
private void updateGraphUI(JSONObject graphResponse) {
    TextView graphText = findViewById(R.id.graphData);
    graphText.setText(graphResponse.toString());
}
```

#### <a name="multi-account-applications"></a>Aplicativos de várias contas

Este aplicativo foi criado para um cenário de conta única. A MSAL também dá suporte a cenários de várias contas, mas requer algum trabalho adicional dos aplicativos. Você precisará criar a interface do usuário para ajudar a selecionar qual conta deseja usar para cada ação que requer tokens do usuário. Como alternativa, seu aplicativo pode implementar uma heurística para selecionar qual conta usar por meio do método `getAccounts()`.

## <a name="test-your-app"></a>Testar seu aplicativo

### <a name="run-locally"></a>Executar localmente

Compilar e implantar o aplicativo em um emulador ou dispositivo de teste. Você deve conseguir conectar e obter tokens para o Azure AD ou contas Microsoft pessoais.

Após entrar, este aplicativo exibirá os dados retornados do ponto de extremidade `/me` do Microsoft Graph.

### <a name="consent"></a>Consentimento

Na primeira vez que um usuário entrar no seu aplicativo, ele será solicitado pela identidade da Microsoft a consentir com as permissões solicitadas.  Embora a maioria dos usuários seja capaz de dar seu consentimento, alguns locatários do Azure AD desabilitaram o consentimento do usuário, que exige que os administradores consintam em nome de todos os usuários. Para dar suporte a esse cenário, registre os escopos do seu aplicativo no portal do Azure.

## <a name="get-help"></a>Obter ajuda

Visite [Ajuda e suporte](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options) se você tiver algum problema com este tutorial ou com a plataforma de identidade da Microsoft.

Ajude-nos a melhorar a plataforma de identidade da Microsoft. Deixe sua opinião respondendo a uma breve pesquisa de duas perguntas.

> [!div class="nextstepaction"]
> [Pesquisa da plataforma de identidade da Microsoft](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)