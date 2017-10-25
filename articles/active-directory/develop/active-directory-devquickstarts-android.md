---
title: "Introdução a Android no AD do Azure | Microsoft Docs"
description: Como compilar um aplicativo para Android que se integre ao Azure AD para entrada e que chame as APIs protegidas do Azure AD usando o OAuth.
services: active-directory
documentationcenter: android
author: danieldobalian
manager: mbaldwin
editor: 
ms.assetid: da1ee39f-89d3-4d36-96f1-4eabbc662343
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 01/07/2017
ms.author: dadobali
ms.custom: aaddev
ms.openlocfilehash: 746cad19093fd2a1ad23ddd9412394f8d9da331c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="integrate-azure-ad-into-an-android-app"></a>Integração do Azure AD a um aplicativo Android
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

> [!TIP]
> Experimente a versão de visualização de nosso novo [portal do desenvolvedor](https://identity.microsoft.com/Docs/Android), que ajudará você a executar o Azure AD em apenas alguns minutos. O portal do desenvolvedor orientará você pelo processo de registro de um aplicativo e integração do Azure AD em seu código. Quando terminar, você terá um aplicativo simples que pode autenticar os usuários em seu locatário e um back-end que pode aceitar tokens e executar a validação.
>
>

Se você estiver desenvolvendo um aplicativo da área de trabalho, o Azure AD (Azure Active Directory) simplificará a autenticação dos usuários com as próprias contas do Active Directory locais. Ele também permite que seu aplicativo consuma com segurança qualquer API da Web protegida pelo AD do Azure, como as APIs do Office 365 ou a API do Azure.

Para os clientes Android que precisam acessar recursos protegidos, o Azure AD fornece a ADAL (Biblioteca de Autenticação do Active Directory). A única finalidade da ADAL é tornar mais fácil para seu aplicativo obter tokens de acesso. Para demonstrar como isso é fácil, vamos compilar um aplicativo de lista de tarefas pendentes para Android que:

* Obtém tokens de acesso para chamar a API da lista de tarefas pendentes usando o [protocolo de autenticação OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx).
* Obtém a lista de tarefas pendentes de um usuário.
* Desconecta usuários.

Para começar, você precisa de um locatário do Azure AD no qual possa criar usuários e registrar um aplicativo. Se você ainda não tiver um locatário [saiba como obter um](active-directory-howto-tenant.md).

## <a name="step-1-download-and-run-the-nodejs-rest-api-todo-sample-server"></a>Etapa 1: Baixe e execute o servidor de exemplo API REST TODO do Node. js
O servidor de exemplo REST API TODO do Node.js é escrito especificamente para funcionar em nosso exemplo, a fim de criar uma API REST de tarefas pendentes de locatário único para o Azure AD. Este é um pré-requisito para o Início Rápido.

Para obter informações sobre como configurar isso, consulte nossos exemplos em [Serviço de API REST de exemplo para Node. js do Microsoft Azure Active Directory](active-directory-devquickstarts-webapi-nodejs.md).


## <a name="step-2-register-your-web-api-with-your-azure-ad-tenant"></a>Etapa 2: Registre sua API da Web com seu locatário do Azure AD
O Active Directory dá suporte à adição de dois tipos de aplicativos:

- APIs da Web que oferecem serviços a usuários
- Aplicativos (executados na Web ou em um dispositivo) que acessam essas APIs da Web

Nesta etapa, você está registrando a API da Web que está sendo executada localmente para testar este exemplo. Normalmente, essa API da Web é um serviço REST que oferece uma funcionalidade interessante a um aplicativo. O Azure AD pode ajudar a proteger qualquer ponto de extremidade.

Vamos supor que você esteja registrando a API REST TODO mencionada anteriormente. Porém, isso funciona para qualquer API da Web para a qual você queira proteção do Azure Active Directory.

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Na barra superior, clique em sua conta. Na lista **Diretório**, escolha o locatário do Azure AD no qual você quer registrar seu aplicativo.
3. Clique em **Mais Serviços** no painel esquerdo e selecione **Azure Active Directory**.
4. Clique em **Registros do aplicativo**e, em seguida, selecione **Adicionar**.
5. Insira um nome amigável para o aplicativo (por exemplo, **TodoListService**, selecione **Aplicativo Web e/ou API da Web** e clique em **Avançar**.
6. Para a URL de logon, insira a URL base para o exemplo. Por padrão, ela é `https://localhost:8080`.
7. Clique em **OK** para concluir o registro.
8. Ainda no Portal do Azure, acesse a página de aplicativo, encontre o valor da ID do aplicativo e copie-o. Você precisará dele mais tarde ao configurar o aplicativo.
9. Na página **Configurações** -> **Propriedades**, atualize o URI da ID do aplicativo – insira `https://<your_tenant_name>/TodoListService`. Substitua `<your_tenant_name>` pelo nome de seu locatário do Azure AD.

## <a name="step-3-register-the-sample-android-native-client-application"></a>Etapa 3: Registre o aplicativo cliente nativo para Android de exemplo
Registre seu aplicativo da Web neste exemplo. Isso permite que o aplicativo se comunique com a API da Web recém-registrada. O Azure AD não vai permitir que o aplicativo solicite a entrada a menos que ele esteja registrado. Isso faz parte da segurança do modelo.

Vamos supor que você esteja registrando o exemplo de aplicativo mencionado anteriormente. Porém, este procedimento funciona para qualquer aplicativo que você esteja desenvolvendo.

> [!NOTE]
> Talvez você se pergunte por que está colocando um aplicativo e uma API da Web em um locatário. Como você deve ter adivinhado, é possível compilar um aplicativo que acessa uma API externa que é registrada no Azure AD de outro locatário. Se você fizer isso, os clientes precisarão concordar com o uso da API no aplicativo. A biblioteca de autenticação do Active Directory para iOS se encarrega desse consentimento por você. Conforme exploramos recursos mais avançados, você verá que essa é uma parte importante do trabalho necessário para acessar o conjunto de APIs da Microsoft no Azure e Office, bem como em qualquer outro provedor de serviço. Por enquanto, como você registrou sua API da Web e seu aplicativo sob o mesmo locatário, você não verá solicitações de permissão. Isso normalmente acontece se você estiver desenvolvendo um aplicativo para uso exclusivo de sua própria empresa.

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Na barra superior, clique em sua conta. Na lista **Diretório**, escolha o locatário do Azure AD no qual você quer registrar seu aplicativo.
3. Clique em **Mais Serviços** no painel esquerdo e selecione **Azure Active Directory**.
4. Clique em **Registros do aplicativo**e, em seguida, selecione **Adicionar**.
5. Insira um nome amigável para o aplicativo (por exemplo, **TodoListClient-Android**), selecione **Aplicativo Cliente Nativo** e clique em **Avançar**.
6. Para o URI de redirecionamento, insira `http://TodoListClient`. Clique em **Concluir**.
7. Na página do aplicativo, encontre o valor da ID do aplicativo e copie-o. Você precisará dele mais tarde ao configurar o aplicativo.
8. Na página **Configurações**, escolha **Permissões Necessárias** e escolha **Adicionar**.  Localize e selecione TodoListService e adicione a permissão **Acessar TodoListService** em **Permissões Delegadas** e clique em **Concluído**.

Para compilar com o Maven, use o pom.xml de nível superior:

1. Clone esse repositório em um diretório de sua escolha:

  `$ git clone git@github.com:AzureADSamples/NativeClient-Android.git`  
2. Siga as etapas nos [pré-requisitos de configuração de seu ambiente Maven para Android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android/wiki/Setting-up-maven-environment-for-Android).
3. Configure o emulador com o SDK 19.
4. Vá para a pasta raiz onde você clonou o repositório.
5. Execute este comando: `mvn clean install`
6. Altere o diretório para o exemplo do Início Rápido: `cd samples\hello`
7. Execute este comando: `mvn android:deploy android:run`

   O aplicativo deve iniciar.
8. Insira as credenciais de usuário de teste para experimentar.

Pacotes JAR serão enviados juntamente com o pacote AAR.

## <a name="step-4-download-the-android-adal-and-add-it-to-your-eclipse-workspace"></a>Etapa 4: Baixe a ADAL para Android e adicione-a ao seu espaço de trabalho do Eclipse
Facilitamos para você ter várias opções para usar a ADAL em seu projeto para Android:

* Você pode usar o código-fonte para importar essa biblioteca no Eclipse e vinculá-la ao seu aplicativo.
* Se estiver usando o Android Studio, você poderá usar o formato de pacote AAR e fazer referência aos binários.

### <a name="option-1-source-zip"></a>Opção 1: zip de origem
Para baixar uma cópia do código-fonte, clique em **Baixar ZIP** no lado direito da página. Ou [baixe do GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-android/archive/v1.0.9.tar.gz).

### <a name="option-2-source-via-git"></a>Opção 2: Origem via Git
Para obter o código-fonte do SDK via Git, digite:

    git clone git@github.com:AzureAD/azure-activedirectory-library-for-android.git
    cd ./azure-activedirectory-library-for-android/src

### <a name="option-3-binaries-via-gradle"></a>Opção 3: Binários via Gradle
Você pode obter os binários do repositório central do Maven. O pacote AAR pode ser incluído da seguinte forma em seu projeto no Android Studio:

```gradle
repositories {
    mavenCentral()
    flatDir {
        dirs 'libs'
    }
    maven {
        url "YourLocalMavenRepoPath\\.m2\\repository"
    }
}
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile('com.microsoft.aad:adal:1.1.1') {
        exclude group: 'com.android.support'
    } // Recent version is 1.1.1
}
```

### <a name="option-4-aar-via-maven"></a>Opção 4: AAR via Maven
Se você estiver usando o plug-in M2Eclipse no Eclipse, especifique a dependência em seu arquivo pom.xml:

```xml
<dependency>
    <groupId>com.microsoft.aad</groupId>
    <artifactId>adal</artifactId>
    <version>1.1.1</version>
    <type>aar</type>
</dependency>
```


### <a name="option-5-jar-package-inside-the-libs-folder"></a>Opção 5: pacote JAR dentro da pasta de bibliotecas
Você pode obter o arquivo JAR do repositório Maven e colocá-lo na pasta **libs** em seu projeto. Você também precisa copiar os recursos necessários ao seu projeto, uma vez que os pacotes JAR não os incluem.

## <a name="step-5-add-references-to-android-adal-to-your-project"></a>Etapa 5: Adicione referências à ADAL do Android em seu projeto
1. Adicione uma referência ao seu projeto e especifique-a como uma biblioteca Android. Se você não tiver certeza sobre como fazer isso, saiba mais no [site do Android Studio](http://developer.android.com/tools/projects/projects-eclipse.html).
2. Adicione a dependência do projeto para depuração nas configurações de seu projeto.
3. Atualize o arquivo AndroidManifest.xml do seu projeto para incluir:

        <uses-permission android:name="android.permission.INTERNET" />
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
        <application
            android:allowBackup="true"
            android:debuggable="true"
            android:icon="@drawable/ic_launcher"
            android:label="@string/app_name"
            android:theme="@style/AppTheme" >

            <activity
                android:name="com.microsoft.aad.adal.AuthenticationActivity"
                android:label="@string/title_login_hello_app" >
            </activity>
            ....
        <application/>

4. Crie uma instância de AuthenticationContext na atividade principal. Os detalhes dessa chamada estão além do escopo deste tópico, mas você pode obter uma boa introdução com o [exemplo de cliente nativo para Android](https://github.com/AzureADSamples/NativeClient-Android). No exemplo a seguir, SharedPreferences é o cache padrão, e a Autoridade está na forma de `https://login.microsoftonline.com/yourtenant.onmicrosoft.com`:

    `mContext = new AuthenticationContext(MainActivity.this, authority, true); // mContext is a field in your activity`

5. Copie esse bloco de código para lidar com o final de AuthenticationActivity, após o usuário inserir as credenciais e receber o código de autorização:

        @Override
         protected void onActivityResult(int requestCode, int resultCode, Intent data) {
             super.onActivityResult(requestCode, resultCode, data);
             if (mContext != null) {
                mContext.onActivityResult(requestCode, resultCode, data);
             }
         }

6. Para solicitar um token, defina um retorno de chamada:

        private AuthenticationCallback<AuthenticationResult> callback = new AuthenticationCallback<AuthenticationResult>() {

            @Override
            public void onError(Exception exc) {
                if (exc instanceof AuthenticationException) {
                    textViewStatus.setText("Cancelled");
                    Log.d(TAG, "Cancelled");
                } else {
                    textViewStatus.setText("Authentication error:" + exc.getMessage());
                    Log.d(TAG, "Authentication error:" + exc.getMessage());
                }
            }

            @Override
            public void onSuccess(AuthenticationResult result) {
                mResult = result;

                if (result == null || result.getAccessToken() == null
                        || result.getAccessToken().isEmpty()) {
                    textViewStatus.setText("Token is empty");
                    Log.d(TAG, "Token is empty");
                } else {
                    // request is successful
                    Log.d(TAG, "Status:" + result.getStatus() + " Expired:"
                            + result.getExpiresOn().toString());
                    textViewStatus.setText(PASSED);
                }
            }
        };

7. Por fim, solicite um token usando esse retorno de chamada:

    `mContext.acquireToken(MainActivity.this, resource, clientId, redirect, user_loginhint, PromptBehavior.Auto, "",
                   callback);`

Confira uma explicação dos parâmetros:

* *resource* é obrigatório e é o recurso que você está tentando acessar.
* *clientid* é obrigatório e é proveniente do Azure AD.
* *RedirectUri* não é obrigatório para a chamada acquireToken. Configure-o como o nome de seu pacote.
* *PromptBehavior* ajuda a solicitar credenciais para ignorar o cache e o cookie.
* *callback* será chamado depois que o código de autorização for trocado por um token. Ele possui um objeto AuthenticationResult, que tem informações sobre o token de acesso, a data de vencimento e o token de ID.
* *acquireTokenSilent* é opcional. Você pode chamá-lo para manipular o cache e a atualização do token. Ele também fornece a versão de sincronização. Ele aceita *userid* como parâmetro.

        mContext.acquireTokenSilent(resource, clientid, userId, callback );

Usando este passo a passo, você deve ter o que precisa para se integrar com êxito com o Azure Active Directory. Para obter mais exemplos de como isso funciona, visite o repositório AzureADSamples/ no GitHub.

## <a name="important-information"></a>Informações importantes
### <a name="customization"></a>Personalização
Os recursos de seu aplicativo podem substituir os recursos de projeto da biblioteca. Isso acontece quando seu aplicativo está sendo compilado. Por esse motivo, você pode personalizar o layout de atividade de autenticação da maneira desejada. Lembre-se de manter a ID dos controles usados pela ADAL (WebView).

### <a name="broker"></a>Agente
O aplicativo do portal da empresa do Microsoft Intune fornece o componente do agente. A conta é criada no AccountManager. O tipo de conta é "com.microsoft.workaccount". AccountManager permite apenas uma única conta SSO. Ele cria o cookie do SSO para o usuário após a conclusão do desafio do dispositivo para um dos aplicativos.

A ADAL usa a conta de agente se houver uma conta de usuário criada nesse autenticador, e você optar por não ignorá-la. Você pode ignorar o usuário do agente com:

   `AuthenticationSettings.Instance.setSkipBroker(true);`

Você precisa registrar um redirectUri especial para uso do agente. RedirectUri está no formato `msauth://packagename/Base64UrlencodedSignature`. Você pode obter o RedirectUri para seu aplicativo usando o script brokerRedirectPrint.ps1 ou usar a chamada à API mContext.getBrokerRedirectUri. A assinatura está relacionada aos seus certificados de autenticação.

O modelo atual do agente é para um usuário. AuthenticationContext fornece o método de API para obter o usuário do agente.

   `String brokerAccount =  mContext.getBrokerUser(); //Broker user is returned if account is valid.`

O manifesto de seu aplicativo deve ter as seguintes permissões para usar contas do AccountManager. Para obter detalhes, consulte as [Informações do AccountManager no site do Android](http://developer.android.com/reference/android/accounts/AccountManager.html).

* GET_ACCOUNTS
* USE_CREDENTIALS
* MANAGE_ACCOUNTS

### <a name="authority-url-and-ad-fs"></a>URL da Autoridade e AD FS
O AD FS (Serviços de Federação do Active Directory) não é reconhecido como STS de produção, por isso, você precisa desativar a descoberta de instância e inserir false no construtor de AuthenticationContext.

A URL da autoridade precisa de uma instância STS e de um [nome de locatário](https://login.microsoftonline.com/yourtenant.onmicrosoft.com).

### <a name="querying-cache-items"></a>Consultar itens do cache
A ADAL fornece um cache padrão em SharedPreferences com algumas funções de consulta de cache simples. É possível obter o cache atual de AuthenticationContext usando:

    ITokenCacheStore cache = mContext.getCache();

Também será possível fornecer implementação ao cache, se desejar personalizá-lo.

    mContext = new AuthenticationContext(MainActivity.this, authority, true, yourCache);

### <a name="prompt-behavior"></a>Comportamento da solicitação
A ADAL fornece uma opção para especificar o comportamento da solicitação. PromptBehavior.Auto exibirá a interface do usuário se o token de atualização for inválido e as credenciais de usuário forem exigidas. PromptBehavior.Always ignorará o uso de cache e sempre mostrará a interface do usuário.

### <a name="silent-token-request-from-cache-and-refresh"></a>Solicitação de token silenciosa do cache e atualização
Uma solicitação de token silenciosa não usa o pop-up da interface do usuário e não exige uma atividade. Ele retorna um token do cache, se estiver disponível. Se o token tiver expirado, esse método tenta atualizá-lo. Se o token de atualização tiver expirado ou falhado, ele retornará AuthenticationException.

    Future<AuthenticationResult> result = mContext.acquireTokenSilent(resource, clientid, userId, callback );

Você também pode fazer uma chamada de sincronização com esse método. Você pode definir null para retorno de chamada ou usar acquireTokenSilentSync.

### <a name="diagnostics"></a>Diagnostics
Estas são as principais fontes de informações para diagnosticar problemas:

* Exceções
* Logs
* Rastreamentos de rede

Observe que as IDs de correlação são essenciais para o diagnóstico na biblioteca. Você pode definir suas IDs de correlação com base em cada solicitação se quiser correlacionar uma solicitação da ADAL com outras operações em seu código. Se você não definir uma ID de correlação, a ADAL gerará uma senha aleatória. Todas as mensagens de log e chamadas de rede serão carimbadas com a ID de correlação. A ID gerada automaticamente muda em cada solicitação.

#### <a name="exceptions"></a>Exceções
As exceções são o primeiro diagnóstico. Tentamos fornecer mensagens de erro úteis. Se você encontrar uma que não é útil, registre um problema e nos informe. Inclua informações do dispositivo, como modelo e número do SDK.

#### <a name="logs"></a>Logs
Você pode configurar a biblioteca para gerar mensagens de log que você pode usar para ajudar a diagnosticar problemas. Configure o registro de log fazendo a chamada a seguir para configurar um retorno de chamada que a ADAL usará para lidar com cada mensagem de log conforme elas são geradas.

    Logger.getInstance().setExternalLogger(new ILogger() {
        @Override
        public void Log(String tag, String message, String additionalMessage, LogLevel level, ADALError errorCode) {
        ...
        // You can write this to log file depending on level or error code.
        writeToLogFile(getApplicationContext(), tag +":" + message + "-" + additionalMessage);
        }
    }

As mensagens podem ser gravadas em um arquivo de log personalizado, conforme visto no código a seguir. Infelizmente, não há um modo padrão de obter os logs de um dispositivo. Há alguns serviços que podem ajudá-lo. Você pode também criar seus próprios métodos, como enviar o arquivo para um servidor.

    private syncronized void writeToLogFile(Context ctx, String msg) {
       File directory = ctx.getDir(ctx.getPackageName(), Context.MODE_PRIVATE);
       File logFile = new File(directory, "logfile");
       FileOutputStream outputStream = new FileOutputStream(logFile, true);
       OutputStreamWriter osw = new OutputStreamWriter(outputStream);
       osw.write(msg);
       osw.flush();
       osw.close();
    }

Estes são os níveis de log:
* Erro (exceções)
* Aviso (aviso)
* Informações (fins informativos)
* Detalhado (mais detalhes)

Defina o nível de log da seguinte maneira:

    Logger.getInstance().setLogLevel(Logger.LogLevel.Verbose);

 Todas as mensagens de log são enviadas para logcat, além de qualquer retorno de chamada de log personalizado.
Você pode obter um log para um arquivo do logcat da seguinte maneira:

    adb logcat > "C:\logmsg\logfile.txt"

 Para obter detalhes sobre comandos adb, consulte as [informações sobre o logcat no site da Android](https://developer.android.com/tools/debugging/debugging-log.html#startingLogcat).

#### <a name="network-traces"></a>Rastreamentos de rede
Você pode usar várias ferramentas para capturar o tráfego HTTP que a ADAL gera.  Isso é mais útil se você estiver familiarizado com o protocolo OAuth ou se você precisar fornecer informações de diagnóstico para a Microsoft ou outros canais de suporte.

Fiddler é a ferramenta de rastreamento de HTTP mais fácil. Use os links a seguir para configurá-la até o tráfego de rede da ADAL de registro correto. Para que uma ferramenta de rastreamento, como o Fiddler ou Charles, seja útil, você deve configurá-la para registrar o tráfego SSL não criptografado.  

> [!NOTE]
> Rastreamentos gerados desta forma podem conter informações altamente privilegiadas como tokens de acesso, nomes de usuário e senhas. Se você estiver usando contas de produção, não compartilhe esses rastreamentos com terceiros. Se precisar fornecer um rastreamento a alguém para obter suporte, reproduza o problema com uma conta temporária com nomes de usuário e senhas que você não se importa de compartilhar.

* Do site da Telerik: [Setting Up Fiddler For Android](http://docs.telerik.com/fiddler/configure-fiddler/tasks/ConfigureForAndroid) (Configuração o Fiddler para Android)
* Do GitHub: [Configure Fiddler Rules For ADAL](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki/How-to-listen-to-httpUrlConnection-in-Android-app-from-Fiddler) (Configurar regras do Fiddler para ADAL)

### <a name="dialog-mode"></a>Modo de caixa de diálogo
O método acquireToken sem atividade dá suporte a um prompt da caixa de diálogo.

### <a name="encryption"></a>Criptografia
A ADAL criptografa os tokens e os armazena em SharedPreferences por padrão. Você pode examinar a classe StorageHelper para ver os detalhes. O Android introduziu o Armazenamento de chaves privadas Android KeyStore 4.3 (API 18). A ADAL o utiliza para API18 e superior. Se você quiser usar a ADAL para versões anteriores do SDK, você precisa fornecer uma chave secreta em AuthenticationSettings.INSTANCE.setSecretKey.

### <a name="oauth2-bearer-challenge"></a>Desafio de portador do Oauth2
A classe AuthenticationParameters fornece funcionalidade para obter o authorization_uri do desafio de portador do OAuth2.

### <a name="session-cookies-in-webview"></a>Cookies de sessão no Webview
O Android WebView não limpa os cookies de sessão depois que o aplicativo é fechado. Você pode tratar disso usando este exemplo de código:

    CookieSyncManager.createInstance(getApplicationContext());
    CookieManager cookieManager = CookieManager.getInstance();
    cookieManager.removeSessionCookie();
    CookieSyncManager.getInstance().sync();

Para obter detalhes sobre cookies, consulte as [informações sobre CookieSyncManager no site do Android](http://developer.android.com/reference/android/webkit/CookieSyncManager.html).

### <a name="resource-overrides"></a>Substituições de recurso
A biblioteca ADAL inclui sequências de caracteres em inglês para mensagens ProgressDialog. Seu aplicativo deve substituí-las se você quiser cadeias de caracteres localizadas.

     <string name="app_loading">Loading...</string>
     <string name="broker_processing">Broker is processing</string>
     <string name="http_auth_dialog_username">Username</string>
     <string name="http_auth_dialog_password">Password</string>
     <string name="http_auth_dialog_title">Sign In</string>
     <string name="http_auth_dialog_login">Login</string>
     <string name="http_auth_dialog_cancel">Cancel</string>

### <a name="ntlm-dialog-box"></a>Caixa de diálogo NTLM
A ADAL versão 1.1.0 dá suporte à caixa de diálogo NTLM, que é processada por meio do evento onReceivedHttpAuthRequest do WebViewClient. Você pode personalizar o layout e as cadeias de caracteres da caixa de diálogo.

### <a name="cross-app-sso"></a>SSO entre aplicativos
Saiba [como habilitar o SSO entre aplicativos no Android usando a ADAL](active-directory-sso-android.md).  

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
