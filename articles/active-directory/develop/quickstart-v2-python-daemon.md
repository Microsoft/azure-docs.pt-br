---
title: 'Início Rápido: Chamar o Microsoft Graph de um daemon do Python | Azure'
titleSuffix: Microsoft identity platform
description: Neste início rápido, você verá como um processo do Python pode obter um token de acesso e chamar uma API protegida pela plataforma de identidade da Microsoft usando a própria identidade do aplicativo
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/22/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, devx-track-python, scenarios:getting-started, languages:Python
ms.openlocfilehash: 734fad7d3f4fb7a2a816d9ad10fb6b15e2faf9e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99820391"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-from-a-python-console-app-using-apps-identity"></a>Início Rápido: Adquirir um token e chamar a API do Microsoft Graph de um aplicativo de console Python usando a identidade do aplicativo

Neste guia de início rápido, você baixará e executará um exemplo de código que demonstra como um aplicativo Python pode obter um token de acesso usando a identidade do aplicativo para chamar a API do Microsoft Graph e exibir uma [lista de usuários](/graph/api/user-list) no diretório. O exemplo de código demonstra como um trabalho autônomo ou um serviço Windows pode ser executado com uma identidade de aplicativo, em vez de uma identidade do usuário. 

> [!div renderon="docs"]
> ![Mostra como o aplicativo de exemplo gerado por este início rápido funciona](media/quickstart-v2-python-daemon/python-console-daemon.svg)

## <a name="prerequisites"></a>Pré-requisitos

Para executar este exemplo, você precisa de:

- [Python 2.7 e posterior](https://www.python.org/downloads/release/python-2713) ou [Python 3 e posterior](https://www.python.org/downloads/release/python-364/)
- [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registrar e baixar o aplicativo de início rápido

> [!div renderon="docs" class="sxs-lookup"]
>
> Você tem duas opções para iniciar o aplicativo de início rápido: Expressa (Opção 1 abaixo) e Manual (Opção 2)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opção 1: Registrar e configurar o aplicativo automaticamente e, em seguida, baixar seu exemplo de código
>
> 1. Acesse a experiência de início rápido do <a href="https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/PythonDaemonQuickstartPage/sourceType/docs" target="_blank">portal do Azure – Registros de aplicativo</a>.
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
> 1. Em **Gerenciar**, selecione **Certificados e Segredos**.
> 1. Em **Segredos do cliente**, selecione **Novo segredo do cliente**, insira um nome e selecione **Adicionar**. Registre o valor secreto em uma localização segura para uso em uma etapa posterior.
> 1. Em **Gerenciar**, selecione **Permissões de API** > **Adicionar uma permissão**. Selecione **Microsoft Graph**.
> 1. Selecione **Permissões de aplicativo**.
> 1. No nó **Usuário**, selecione **User.Read.All** e selecione **Adicionar permissões**.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="download-and-configure-the-quickstart-app"></a>Baixar e configurar o aplicativo de início rápido
>
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Etapa 1: Configurar seu aplicativo no portal do Azure
> Para que o exemplo de código deste guia de início rápido funcione, crie um segredo do cliente e adicione a permissão de aplicativo **User.Read.All** da API do Graph.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Fazer essas alterações para mim]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurado](media/quickstart-v2-netcore-daemon/green-check.png) Seu aplicativo já está configurado com esses atributos.

#### <a name="step-2-download-the-python-project"></a>Etapa 2: Baixar o projeto Python

> [!div renderon="docs"]
> [Baixe o projeto do daemon do Python](https://github.com/Azure-Samples/ms-identity-python-daemon/archive/master.zip)

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Baixe o exemplo de código](https://github.com/Azure-Samples/ms-identity-python-daemon/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`


> [!div renderon="docs"]
> #### <a name="step-3-configure-the-python-project"></a>Etapa 3: Configurar o projeto Python
>
> 1. Extraia o arquivo zip para uma pasta local mais próxima da raiz do disco, por exemplo, **C:\Azure-Samples**.
> 1. Procure a subpasta **1-Call-MsGraph-WithSecret**.
> 1. Edite **parameters.json** e substitua os valores dos campos `authority`, `client_id` e `secret` pelo seguinte trecho:
>
>    ```json
>    "authority": "https://login.microsoftonline.com/Enter_the_Tenant_Id_Here",
>    "client_id": "Enter_the_Application_Id_Here",
>    "secret": "Enter_the_Client_Secret_Here"
>    ```
>    Em que:
>    - `Enter_the_Application_Id_Here` - é a **ID do aplicativo (cliente)** que você registrou.
>    - `Enter_the_Tenant_Id_Here` – substitua esse valor pela **ID do locatário** ou pelo **Nome do locatário** (por exemplo, contoso.microsoft.com)
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

Se você é um usuário padrão do seu locatário, solicite ao Administrador global que forneça consentimento do administrador para seu aplicativo. Para fazer isso, dê a seguinte URL ao administrador:

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

Você precisará instalar as dependências deste exemplo uma só vez.

```console
pip install -r requirements.txt
```

Em seguida, execute o aplicativo via prompt de comando ou console:

```console
python confidential_client_secret_sample.py parameters.json
```

Você deve ver no console a saída de algum fragmento JSON que representa uma lista de usuários em seu diretório do Azure AD.

> [!IMPORTANT]
> Este aplicativo de início rápido usa um segredo do cliente para se identificar como cliente confidencial. Como o segredo do cliente é adicionado como texto sem formatação a seus arquivos de projeto, por motivos de segurança, é recomendável que você use um certificado, em vez de um segredo do cliente, antes de considerar o aplicativo como aplicativo de produção. Para obter mais informações sobre como usar um certificado, confira [estas instruções](https://github.com/Azure-Samples/ms-identity-python-daemon/blob/master/2-Call-MsGraph-WithCertificate/README.md) no mesmo repositório GitHub deste exemplo, mas na segunda pasta **2-Call-MsGraph-WithCertificate**.

## <a name="more-information"></a>Mais informações

### <a name="msal-python"></a>MSAL Python

[MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python) é a biblioteca usada para conectar usuários e solicitar tokens usados para acessar uma API protegida pela plataforma de identidade da Microsoft. Conforme descrito, este início rápido solicita tokens usando a identidade do próprio aplicativo, em vez de permissões delegadas. O fluxo de autenticação usado nesse caso é conhecido como *[fluxo OAuth de credenciais do cliente](v2-oauth2-client-creds-grant-flow.md)* . Para obter mais informações sobre como usar a MSAL Python com aplicativos daemon, consulte [este artigo](scenario-daemon-overview.md).

 Instale a MSAL Python executando o seguinte comando de pip.

```powershell
pip install msal
```

### <a name="msal-initialization"></a>Inicialização da MSAL

Você pode adicionar a referência da MSAL adicionando o seguinte código:

```Python
import msal
```

Em seguida, inicialize a MSAL usando o seguinte código:

```Python
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential=config["secret"])
```

> | Em que: |Descrição |
> |---------|---------|
> | `config["secret"]` | É o segredo do cliente criado para o aplicativo no portal do Azure. |
> | `config["client_id"]` | É a **ID do aplicativo (cliente)** relativa ao aplicativo registrado no portal do Azure. Você pode encontrar esse valor na página **Visão Geral** do aplicativo no portal do Azure. |
> | `config["authority"]`    | O ponto de extremidade do STS para o usuário autenticar. Normalmente, `https://login.microsoftonline.com/{tenant}` para a nuvem pública, em que {tenant} é o nome do seu locatário ou ID do seu locatário.|

Para obter mais informações, confira a [documentação de referência do `ConfidentialClientApplication`](https://msal-python.readthedocs.io/en/latest/#confidentialclientapplication).

### <a name="requesting-tokens"></a>Solicitando tokens

Para solicitar um token usando a identidade do aplicativo, use o método `AcquireTokenForClient`:

```Python
result = None
result = app.acquire_token_silent(config["scope"], account=None)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    result = app.acquire_token_for_client(scopes=config["scope"])
```

> |Em que:| Descrição |
> |---------|---------|
> | `config["scope"]` | Contém os escopos solicitados. Para clientes confidenciais, ele deve usar um formato semelhante a `{Application ID URI}/.default` para indicar que os escopos solicitados são os estaticamente definidos no objeto de aplicativo definido no portal do Azure (no caso do Microsoft Graph, `{Application ID URI}` aponta para `https://graph.microsoft.com`). Para APIs Web personalizadas, o `{Application ID URI}` é definido na seção **Expor uma API** em **Registros de aplicativo** no portal do Azure.|

Para obter mais informações, confira a [documentação de referência do `AcquireTokenForClient`](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_for_client).

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre aplicativos daemon, confira a página de aterrissagem do cenário.

> [!div class="nextstepaction"]
> [Aplicativo daemon que chama APIs Web](scenario-daemon-overview.md)
