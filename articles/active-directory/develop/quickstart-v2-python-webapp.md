---
title: 'Início Rápido: Adicionar a opção Entrar com a conta Microsoft a um aplicativo Web do Python | Azure'
titleSuffix: Microsoft identity platform
description: Neste guia de início rápido, saiba como um aplicativo Web do Python pode conectar usuários, obter um token de acesso da plataforma de identidade da Microsoft e chamar a API do Microsoft Graph.
services: active-directory
author: abhidnya13
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/25/2019
ms.author: abpati
ms.custom: aaddev, devx-track-python, scenarios:getting-started, languages:Python
ms.openlocfilehash: fb38140f09fc7c1eb2c40fc02e8c113cbc6f94a0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100103508"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-python-web-app"></a>Início Rápido: Adicionar a opção Entrar com a conta da Microsoft a um aplicativo Web do Python

Neste guia de início rápido, você baixará e executará um exemplo de código que demonstra como um aplicativo Web Python pode conectar usuários e obter um token de acesso para chamar a API do Microsoft Graph. Os usuários com uma conta Microsoft pessoal ou uma conta em qualquer organização do Azure AD (Azure Active Directory) podem entrar no aplicativo.

Confira [Como o exemplo funciona](#how-the-sample-works) para ver uma ilustração.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Python 2.7 e posterior](https://www.python.org/downloads/release/python-2713) ou [Python 3 e posterior](https://www.python.org/downloads/release/python-364/)
- [Flask](http://flask.pocoo.org/), [Flask-Session](https://pypi.org/project/Flask-Session/), [solicitações](https://requests.kennethreitz.org/en/master/)
- [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)

> [!div renderon="docs"]
>
> ## <a name="register-and-download-your-quickstart-app"></a>Registrar e baixar o aplicativo de início rápido
>
> Você tem duas opções para iniciar o aplicativo de início rápido: expresso (opção 1) ou manual (opção 2)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opção 1: Registrar e configurar o aplicativo automaticamente e, em seguida, baixar seu exemplo de código
>
> 1. Acesse a experiência de início rápido do <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/PythonQuickstartPage/sourceType/docs" target="_blank">portal do Azure – Registros de aplicativo</a>.
> 1. Insira um nome para seu aplicativo e selecione **Registrar**.
> 1. Siga as instruções para baixar e configurar automaticamente o novo aplicativo.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opção 2: Registrar e configurar manualmente o aplicativo e o exemplo de código
>
> #### <a name="step-1-register-your-application"></a>Etapa 1: Registre seu aplicativo
>
> Para registrar seu aplicativo e adicionar as informações de registro do aplicativo à solução manualmente, siga estas etapas:
>
> 1. Entre no <a href="https://portal.azure.com/" target="_blank">portal do Azure</a>.
> 1. Se você tem acesso a vários locatários, use o filtro **Diretório + assinatura** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: no menu superior para selecionar o locatário no qual você deseja registrar um aplicativo.
> 1. Em **Gerenciar**, selecione **Registros de aplicativo** > **Novo registro**.
> 1. Insira um **Nome** para seu aplicativo, por exemplo, `python-webapp`. Os usuários do seu aplicativo podem ver esse nome e você pode alterá-lo mais tarde.
> 1. Em **Tipos de conta com suporte**, selecione **Contas em qualquer diretório organizacional e contas pessoais da Microsoft**.
> 1. Selecione **Registrar**.
> 1. Na página **Visão geral** do aplicativo, anote o valor de **ID do aplicativo (cliente)** para uso posterior.
> 1. Em **Gerenciar**, selecione **Autenticação**.
> 1. Selecione **Adicionar uma plataforma** > **Web**.
> 1. Adicione `http://localhost:5000/getAToken` como **URIs de Redirecionamento**.
> 1. Selecione **Configurar**.
> 1. Em **Gerenciar**, selecione os **Certificados e segredos** e, na seção **Segredos do cliente**, selecione **Novo segredo do cliente**.
> 1. Digite uma descrição de chave (por exemplo, segredo do aplicativo); deixe a expiração padrão e selecione **Adicionar**.
> 1. Observe o **Valor** do **Segredo do cliente** para uso posterior.
> 1. Em **Gerenciar**, selecione **Permissões de API** > **Adicionar uma permissão**.
> 1. Verifique se a guia **APIs da Microsoft** está selecionada.
> 1. Na seção *APIs da Microsoft frequentemente utilizadas*, selecione **Microsoft Graph**.
> 1. Na seção **Permissões delegadas**, verifique se as permissões corretas estão marcadas: **User.ReadBasic.All**. Use a caixa de pesquisa, se necessário.
> 1. Selecione o botão **Adicionar permissões**.
>
> [!div class="sxs-lookup" renderon="portal"]
>
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Etapa 1: Configurar seu aplicativo no portal do Azure
>
> Para que o exemplo de código deste guia de início rápido funcione:
>
> 1. Adicione uma URL de resposta como `http://localhost:5000/getAToken`.
> 1. Criar um segredo do cliente.
> 1. Adicione a permissão delegada User.ReadBasic.All da API do Microsoft Graph.
>
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Fazer essas alterações para mim]()
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurado](media/quickstart-v2-aspnet-webapp/green-check.png) Seu aplicativo já está configurado com esse atributo

#### <a name="step-2-download-your-project"></a>Etapa 2: Baixar o seu projeto
> [!div renderon="docs"]
> [Baixe o exemplo de código](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> Baixe o projeto e extraia o arquivo zip para uma pasta local mais próxima da pasta raiz, por exemplo, **C:\Azure-Samples**
> [!div class="sxs-lookup" renderon="portal" id="autoupdate" class="nextstepaction"]
> [Baixe o exemplo de código](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-application"></a>Etapa 3: Configurar o aplicativo
>
> 1. Extraia o arquivo zip para uma pasta local mais próxima da pasta raiz, por exemplo, **C:\Azure-Samples**
> 1. Se você usar um ambiente de desenvolvimento integrado, abra a amostra em seu IDE favorito (opcional).
> 1. Abra o arquivo **app_config.py**, que pode ser encontrado na pasta raiz, e substitua-o pelo seguinte snippet de código:
>
> ```python
> CLIENT_ID = "Enter_the_Application_Id_here"
> CLIENT_SECRET = "Enter_the_Client_Secret_Here"
> AUTHORITY = "https://login.microsoftonline.com/Enter_the_Tenant_Name_Here"
> ```
> Em que:
>
> - `Enter_the_Application_Id_here`: é a ID do aplicativo que você registrou.
> - `Enter_the_Client_Secret_Here` – é o **Segredo do Cliente** criado em **Certificados e Segredos** para o aplicativo registrado.
> - `Enter_the_Tenant_Name_Here` – é o valor da **ID do Diretório (locatário)** do aplicativo que você registrou.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-run-the-code-sample"></a>Etapa 3: Executar o exemplo de código

> [!div renderon="docs"]
> #### <a name="step-4-run-the-code-sample"></a>Etapa 4: Executar o exemplo de código

1. Você precisará instalar a biblioteca MSAL Python, a estrutura Flask, o Flask-Sessions para gerenciamento de sessão do servidor e solicitações usando o PIP da seguinte maneira:

    ```Shell
    pip install -r requirements.txt
    ```

2. Execute app.py no shell ou na linha de comando:

    ```Shell
    python app.py
    ```
   > [!IMPORTANT]
   > Este aplicativo de início rápido usa um segredo do cliente para se identificar como cliente confidencial. Como o segredo do cliente é adicionado como texto sem formatação a seus arquivos de projeto, por motivos de segurança, é recomendável que você use um certificado, em vez de um segredo do cliente, antes de considerar o aplicativo como aplicativo de produção. Para saber mais sobre como usar um certificado, confira [estas instruções](./active-directory-certificate-credentials.md).

## <a name="more-information"></a>Mais informações

### <a name="how-the-sample-works"></a>Como o exemplo funciona
![Mostra como o aplicativo de exemplo gerado por este início rápido funciona](media/quickstart-v2-python-webapp/python-quickstart.svg)

### <a name="getting-msal"></a>Como obter o MSAL
A MSAL é a biblioteca usada para conectar usuários e solicitar tokens usados para acessar uma API protegida pela plataforma de identidade da Microsoft.
Você pode adicionar a MSAL Python ao seu aplicativo usando o PIP.

```Shell
pip install msal
```

### <a name="msal-initialization"></a>Inicialização da MSAL
Adicione a referência à MSAL Python adicionando o seguinte código ao início do arquivo no qual você usará a MSAL:

```Python
import msal
```

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre os aplicativos Web que se conectam aos usuários em nossa série de cenários de várias partes.

> [!div class="nextstepaction"]
> [Cenário: Aplicativo Web que conecta usuários](scenario-web-app-sign-user-overview.md)
