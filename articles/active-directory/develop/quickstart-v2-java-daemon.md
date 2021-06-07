---
title: 'Início rápido: Chamar o Microsoft Graph em um daemon Java | Azure'
titleSuffix: Microsoft identity platform
description: Neste guia de início rápido, você verá como um aplicativo Java pode obter um token de acesso e chamar uma API protegida pelo ponto de extremidade da plataforma de identidade da Microsoft usando a própria identidade do aplicativo
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 01/22/2021
ms.author: nacanuma
ms.custom: aaddev, scenarios:getting-started, languages:Java, devx-track-java
ms.openlocfilehash: 196b80a704b8a270a4cbb7d3505d5f9be1e23479
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99820306"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-from-a-java-console-app-using-apps-identity"></a>Início rápido: Adquirir um token e chamar a API do Microsoft Graph em um aplicativo de console Java usando a identidade do aplicativo

Neste guia de início rápido, você baixará e executará um exemplo de código que demonstra como um aplicativo Java pode obter um token de acesso usando a identidade do aplicativo para chamar a API do Microsoft Graph e exibir uma [lista de usuários](/graph/api/user-list) no diretório. O exemplo de código demonstra como um trabalho autônomo ou um serviço Windows pode ser executado com uma identidade de aplicativo, em vez de uma identidade do usuário. 

> [!div renderon="docs"]
> ![Mostra como o aplicativo de exemplo gerado por este início rápido funciona](media/quickstart-v2-java-daemon/java-console-daemon.svg)

## <a name="prerequisites"></a>Pré-requisitos

Para executar este exemplo, você precisa de:

- [JDK (Java Development Kit)](https://openjdk.java.net/) 8 ou posterior
- [Maven](https://maven.apache.org/)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registrar e baixar o aplicativo de início rápido

> [!div renderon="docs" class="sxs-lookup"]
>
> Você tem duas opções para iniciar o aplicativo de início rápido: Expressa (Opção 1 abaixo) e Manual (Opção 2)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opção 1: Registrar e configurar o aplicativo automaticamente e, em seguida, baixar seu exemplo de código
>
> 1. Acesse a experiência de início rápido do <a href="https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/JavaDaemonQuickstartPage/sourceType/docs" target="_blank">portal do Azure – Registros de aplicativo</a>.
> 1. Insira um nome para seu aplicativo e selecione **Registrar**.
> 1. Siga as instruções para baixar e configurar automaticamente o novo aplicativo com apenas um clique.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opção 2: Registrar e configurar manualmente o aplicativo e o exemplo de código

> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>Etapa 1: Registre seu aplicativo
> Para registrar seu aplicativo e adicionar as informações de registro do aplicativo à solução manualmente, siga estas etapas:
>
> 1. Entre no <a href="https://portal.azure.com/" target="_blank">portal do Azure</a>.
> 1. Se você tem acesso a vários locatários, use o filtro **Diretório + assinatura** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: no menu superior para selecionar o locatário no qual você deseja registrar um aplicativo.
> 1. Pesquise **Azure Active Directory** e selecione-o.
> 1. Em **Gerenciar**, selecione **Registros de aplicativo** > **Novo registro**.
> 1. Insira um **Nome** para seu aplicativo, por exemplo, `Daemon-console`. Os usuários do seu aplicativo podem ver esse nome e você pode alterá-lo mais tarde.
> 1. Selecione **Registrar**.
> 1. Em **Gerenciar**, selecione **Certificados e segredos**.
> 1. Em **Segredos do cliente**, selecione **Novo segredo do cliente**, insira um nome e selecione **Adicionar**. Registre o valor secreto em uma localização segura para uso em uma etapa posterior.
> 1. Em **Gerenciar**, selecione **Permissões de API** > **Adicionar uma permissão**. Selecione **Microsoft Graph**.
> 1. Selecione **Permissões de aplicativo**.
> 1. No nó **Usuário**, selecione **User.Read.All** e selecione **Adicionar permissões**.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="download-and-configure-the-quickstart-app"></a>Baixar e configurar o aplicativo de início rápido
>
> #### <a name="step-1-configure-the-application-in-azure-portal"></a>Etapa 1: Configurar o aplicativo no portal do Azure
> Para o exemplo de código deste início rápido funcionar, você precisará criar um segredo do cliente e adicionar a permissão de aplicativo **User.Read.All** da API do Graph.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Fazer essas alterações para mim]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurado](media/quickstart-v2-netcore-daemon/green-check.png) Seu aplicativo já está configurado com esses atributos.

#### <a name="step-2-download-the-java-project"></a>Etapa 2: Baixar o projeto Java

> [!div renderon="docs"]
> [Baixar o projeto de daemon Java](https://github.com/Azure-Samples/ms-identity-java-daemon/archive/master.zip)

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Baixe o exemplo de código](https://github.com/Azure-Samples/ms-identity-java-daemon/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`


> [!div renderon="docs"]
> #### <a name="step-3-configure-the-java-project"></a>Etapa 3: Configurar o projeto Java
>
> 1. Extraia o arquivo zip para uma pasta local mais próxima da raiz do disco, por exemplo, *C:\Azure-Samples*.
> 1. Procure a subpasta **msal-client-credential-secret**.
> 1. Edite *src\main\resources\application.properties* e substitua os valores dos campos `AUTHORITY`, `CLIENT_ID` e `SECRET` pelo seguinte snippet:
>
>    ```
>    AUTHORITY=https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/
>    CLIENT_ID=Enter_the_Application_Id_Here
>    SECRET=Enter_the_Client_Secret_Here
>    ```
>    Em que:
>    - `Enter_the_Application_Id_Here` - é a **ID do aplicativo (cliente)** que você registrou.
>    - `Enter_the_Tenant_Id_Here`: substitua esse valor pela **ID do Locatário** ou pelo **Nome do locatário** (por exemplo, contoso.microsoft.com).
>    - `Enter_the_Client_Secret_Here` – substitua esse valor pelo segredo do cliente criado na etapa 1.
>
> > [!TIP]
> > Para encontrar os valores de **ID do aplicativo (cliente)** , **ID de diretório (locatário)** , acesse a página **Visão Geral** do aplicativo no portal do Azure. Para gerar uma nova chave, acesse a página **Certificados e segredos**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-admin-consent"></a>Etapa 3: Consentimento do administrador

> [!div renderon="docs"]
> #### <a name="step-4-admin-consent"></a>Etapa 4: Consentimento do administrador

Se você tentar executar o aplicativo neste ponto, receberá o erro *HTTP 403 – Proibido*: `Insufficient privileges to complete the operation`. Esse erro acontece porque qualquer *permissão somente do aplicativo* exige o consentimento do administrador, ou seja, um administrador global do seu diretório precisa dar consentimento ao seu aplicativo. Selecione uma das opções abaixo, dependendo de sua função:

##### <a name="global-tenant-administrator"></a>Administrator de locatário global

> [!div renderon="docs"]
> Se você é um administrador de locatários global, acesse a página **Permissões de API** em **Registros de aplicativo** no portal do Azure e selecione **Fornecer o consentimento do administrador para {Nome do Locatário}** (em que {Nome do Locatário} é o nome do seu diretório).

> [!div renderon="portal" class="sxs-lookup"]
> Se você é um Administrador global, acesse a página **Permissões de API**, selecione **Fornecer o consentimento do administrador para Insira_o_Nome_do_Locatário_Aqui**.
> > [!div id="apipermissionspage"]
> > [Acesse a página Permissões de API]()

##### <a name="standard-user"></a>Usuário padrão

Se você for usuário padrão do seu locatário, precisará pedir que o administrador global dê consentimento do administrador para seu aplicativo. Para fazer isso, dê a seguinte URL ao administrador:

```url
https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
```

> [!div renderon="docs"]
>> Em que:
>> * `Enter_the_Tenant_Id_Here` – substitua esse valor pela **ID do locatário** ou pelo **Nome do locatário** (por exemplo, contoso.microsoft.com)
>> * `Enter_the_Application_Id_Here` - é a **ID do aplicativo (cliente)** que você registrou.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-4-run-the-application"></a>Etapa 4: Executar o aplicativo

> [!div renderon="docs"]
> #### <a name="step-5-run-the-application"></a>Etapa 5: Executar o aplicativo

Teste o exemplo diretamente executando o método principal de ClientCredentialGrant.java no IDE.

No shell ou na linha de comando:

```
$ mvn clean compile assembly:single
```

Isso vai gerar um arquivo msal-client-credential-secret-1.0.0.jar no diretório /targets. Execute-o usando o executável Java, conforme mostrado abaixo:

```
$ java -jar msal-client-credential-secret-1.0.0.jar
```

Após a execução, o aplicativo exibirá a lista de usuários no locatário configurado.


> [!IMPORTANT]
> Este aplicativo de início rápido usa um segredo do cliente para se identificar como cliente confidencial. Como o segredo do cliente é adicionado como texto sem formatação a seus arquivos de projeto, por motivos de segurança, é recomendável que você use um certificado, em vez de um segredo do cliente, antes de considerar o aplicativo como aplicativo de produção. Para obter mais informações sobre como usar um certificado, confira [estas instruções](https://github.com/Azure-Samples/ms-identity-java-daemon/tree/master/msal-client-credential-certificate) no mesmo repositório GitHub deste exemplo, mas na segunda pasta **msal-client-credential-certificate**.

## <a name="more-information"></a>Mais informações

### <a name="msal-java"></a>MSAL Java

[MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java) é a biblioteca usada para conectar usuários e solicitar tokens usados para acessar uma API protegida pela plataforma de identidade da Microsoft. Conforme descrito, este início rápido solicita tokens usando a identidade do próprio aplicativo, em vez de permissões delegadas. O fluxo de autenticação usado nesse caso é conhecido como *[fluxo OAuth de credenciais do cliente](v2-oauth2-client-creds-grant-flow.md)* . Para obter mais informações sobre como usar a MSAL Java com aplicativos daemon, confira [este artigo](scenario-daemon-overview.md).

Adicione a MSAL4J ao seu aplicativo usando o Maven ou o Gradle para gerenciar as dependências fazendo as alterações a seguir no arquivo pom.xml (Maven) ou build.gradle (Gradle) do aplicativo.

No pom.xml:

```XML
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>msal4j</artifactId>
    <version>1.0.0</version>
</dependency>
```

No build.gradle:

```$xslt
compile group: 'com.microsoft.azure', name: 'msal4j', version: '1.0.0'
```

### <a name="msal-initialization"></a>Inicialização da MSAL

Adicione uma referência à MSAL para Java incluindo o seguinte código ao início do arquivo no qual você usará a MSAL4J:

```Java
import com.microsoft.aad.msal4j.*;
```

Em seguida, inicialize a MSAL usando o seguinte código:

```Java
IClientCredential credential = ClientCredentialFactory.createFromSecret(CLIENT_SECRET);

ConfidentialClientApplication cca =
        ConfidentialClientApplication
                .builder(CLIENT_ID, credential)
                .authority(AUTHORITY)
                .build();
```

> | Em que: |Descrição |
> |---------|---------|
> | `CLIENT_SECRET` | É o segredo do cliente criado para o aplicativo no portal do Azure. |
> | `CLIENT_ID` | É a **ID do aplicativo (cliente)** relativa ao aplicativo registrado no portal do Azure. Você pode encontrar esse valor na página **Visão Geral** do aplicativo no portal do Azure. |
> | `AUTHORITY`    | O ponto de extremidade do STS para o usuário autenticar. Normalmente, `https://login.microsoftonline.com/{tenant}` para a nuvem pública, em que {tenant} é o nome do seu locatário ou ID do seu locatário.|

### <a name="requesting-tokens"></a>Solicitando tokens

Para solicitar um token usando a identidade do aplicativo, use o método `acquireToken`:

```Java
IAuthenticationResult result;
     try {
         SilentParameters silentParameters =
                 SilentParameters
                         .builder(SCOPE)
                         .build();

         // try to acquire token silently. This call will fail since the token cache does not
         // have a token for the application you are requesting an access token for
         result = cca.acquireTokenSilently(silentParameters).join();
     } catch (Exception ex) {
         if (ex.getCause() instanceof MsalException) {

             ClientCredentialParameters parameters =
                     ClientCredentialParameters
                             .builder(SCOPE)
                             .build();

             // Try to acquire a token. If successful, you should see
             // the token information printed out to console
             result = cca.acquireToken(parameters).join();
         } else {
             // Handle other exceptions accordingly
             throw ex;
         }
     }
     return result;
```

> |Em que:| Descrição |
> |---------|---------|
> | `SCOPE` | Contém os escopos solicitados. Para clientes confidenciais, ele deve usar um formato semelhante a `{Application ID URI}/.default` para indicar que os escopos solicitados são os estaticamente definidos no objeto de aplicativo definido no portal do Azure (no caso do Microsoft Graph, `{Application ID URI}` aponta para `https://graph.microsoft.com`). Para APIs Web personalizadas, o `{Application ID URI}` é definido na seção **Expor uma API** em **Registros de aplicativo** no portal do Azure.|

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre aplicativos daemon, confira a página de aterrissagem do cenário.

> [!div class="nextstepaction"]
> [Aplicativo daemon que chama APIs Web](scenario-daemon-overview.md)
