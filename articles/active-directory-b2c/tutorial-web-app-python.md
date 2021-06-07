---
title: 'Tutorial: Habilitar a autenticação em um aplicativo Web do Python'
titleSuffix: Azure AD B2C
description: Neste tutorial, saiba como usar o Azure Active Directory B2C para fornecer o logon do usuário para um aplicativo Web do Python Flask.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 06/12/2020
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.custom: devx-track-python
ms.openlocfilehash: 56e92a698446bd417c74820d6da662ad9ee55c77
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100555687"
---
# <a name="tutorial-enable-authentication-in-a-python-web-application-with-azure-ad-b2c"></a>Tutorial: Habilitar a autenticação em um aplicativo Web do Python com o Azure AD B2C

Este tutorial mostra como usar o Azure AD B2C (Azure Active Directory B2C) para inscrever e conectar usuários em um aplicativo Web do Python Flask.

Neste tutorial:

> [!div class="checklist"]
> * Adicione uma URL de resposta a um aplicativo registrado em seu locatário do Azure AD B2C
> * Baixe um exemplo de código do GitHub
> * Modifique o código do aplicativo de exemplo para funcionar com seu locatário
> * Inscreva-se usando seu fluxo de inscrição/entrada de usuário

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Você precisará dos seguintes recursos do Azure AD B2C em vigor antes de continuar com as etapas deste tutorial:

* [Locatário do Azure AD B2C](tutorial-create-tenant.md)
* [Aplicativo registrado](tutorial-register-applications.md) em seu locatário e a *ID do Aplicativo (cliente)* e o *segredo do cliente* desse aplicativo
* [Fluxos dos usuários criados](tutorial-create-user-flows.md) em seu locatário

Além disso, você precisará do seguinte no ambiente de desenvolvimento local:

* [Visual Studio Code](https://code.visualstudio.com/) ou outro editor de código
* [Python](https://nodejs.org/en/download/) 2.7+ ou 3+

## <a name="add-a-redirect-uri"></a>Adicionar um URI de redirecionamento

No segundo tutorial concluído como parte dos pré-requisitos, você registrou um aplicativo Web no Azure AD B2C. Para habilitar a comunicação com o exemplo de código neste tutorial, adicione uma URL de resposta (também chamada de URI de redirecionamento) ao registro de aplicativo.

Para atualizar um aplicativo no locatário do Azure AD B2C, você pode usar a nossa nova experiência unificada de **Registros de aplicativos** ou a nossa experiência herdada **Aplicativos (Herdado)** . [Saiba mais sobre a nova experiência](./app-registrations-training-guide.md).

#### <a name="app-registrations"></a>[Registros de aplicativo](#tab/app-reg-ga/)

1. Entre no [portal do Azure](https://portal.azure.com).
1. Selecione o filtro **Diretório + assinatura** no menu superior e, em seguida, selecione o diretório que contém o locatário do Azure AD B2C.
1. No menu à esquerda, selecione **Azure AD B2C**. Ou selecione **Todos os serviços** e pesquise e selecione **Azure AD B2C**.
1. Selecione **Registros de aplicativo**, selecione a guia **Aplicativos Próprios** e, em seguida, selecione o aplicativo *webapp1*.
1. Em **Gerenciar**, selecione **Autenticação**.
1. Em **Web**, selecione o link **Adicionar URI** e digite `http://localhost:5000/getAToken` na caixa de texto.
1. Clique em **Salvar**.

#### <a name="applications-legacy"></a>[Aplicativos (Herdado)](#tab/applications-legacy/)

1. Entre no [portal do Azure](https://portal.azure.com).
1. Verifique se você está usando o diretório que contém o locatário do Azure AD B2C selecionando o filtro **Diretório + assinatura** no menu superior e escolhendo o diretório que contém o locatário.
1. Selecione **Todos os serviços** no canto superior esquerdo do portal do Azure, pesquise pelo **Azure AD B2C** e selecione-o.
1. Selecione **Aplicativos (Herdado)** e, em seguida, selecione o aplicativo *webapp1*.
1. Em **URL de resposta**, adicione `http://localhost:5000/getAToken`.
1. Clique em **Salvar**.
* * *

## <a name="get-the-sample-code"></a>Obter o código de amostra

Neste tutorial, você configurará um exemplo de código baixado do GitHub para funcionar com o locatário do B2C. A amostra descreve como um aplicativo Web do Python Flask pode usar o Azure AD B2C para a inscrição e a entrada de usuário.

[Baixe um arquivo morto .zip](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip) ou clone o [exemplo de código do repositório](https://github.com/Azure-Samples/ms-identity-python-webapp) do GitHub.

```console
git clone https://github.com/Azure-Samples/ms-identity-python-webapp.git
```

## <a name="update-the-sample"></a>Atualizar a amostra

Depois de obter o exemplo, configure o aplicativo para usar seu registro de aplicativo, fluxos de usuário e locatário do Azure AD B2C.

No diretório raiz do projeto:

1. Renomeie o arquivo *app_config.py* para *app_config.py.OLD*
1. Renomeie o *app_config_b2c.py* para *app_config.py*

Atualize o *app_config.py* renomeado recentemente com valores para seu locatário de Azure AD B2C e o registro de aplicativo que você criou como parte dos pré-requisitos.

1. Abra o arquivo *app_config.py* em seu editor.
1. Atualize o valor `b2c_tenant` com o nome do seu locatário do Azure AD B2C, por exemplo, *contosob2c*.
1. Atualize todos os valores de `*_user_flow` para corresponderem aos nomes dos fluxos de usuário que você criou como parte dos pré-requisitos.
1. Atualize o valor de `CLIENT_ID` com a **ID do Aplicativo (cliente)** do aplicativo Web que você registrou como parte dos pré-requisitos.
1. Atualize o valor de `CLIENT_SECRET` com o valor do **segredo do cliente** criado nos pré-requisitos. Para aumentar a segurança, considere armazená-lo em uma **variável de ambiente**, conforme recomendado nos comentários.

A seção superior de *app_config.py* agora deve ser semelhante ao seguinte snippet de código:

```python
import os

b2c_tenant = "contosob2c"
signupsignin_user_flow = "B2C_1_signupsignin1"
editprofile_user_flow = "B2C_1_profileediting1"
resetpassword_user_flow = "B2C_1_passwordreset1"
authority_template = "https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{user_flow}"

CLIENT_ID = "11111111-1111-1111-1111-111111111111" # Application (client) ID of app registration

CLIENT_SECRET = "22222222-2222-2222-2222-222222222222" # Placeholder - for use ONLY during testing.
# In a production app, we recommend you use a more secure method of storing your secret,
# like Azure Key Vault. Or, use an environment variable as described in Flask's documentation:
# https://flask.palletsprojects.com/en/1.1.x/config/#configuring-from-environment-variables
# CLIENT_SECRET = os.getenv("CLIENT_SECRET")
# if not CLIENT_SECRET:
#     raise ValueError("Need to define CLIENT_SECRET environment variable")
```

> [!WARNING]
> Conforme observado nos comentários do snippet de código, recomendamos que você **não armazene segredos em texto não criptografado** no código do aplicativo. A variável codificada é usada no exemplo de código *apenas por questão de conveniência*. Considere a possibilidade de usar uma variável de ambiente ou um repositório secreto como o Azure Key Vault.

## <a name="run-the-sample"></a>Execute o exemplo

1. No seu console ou terminal, alterne para o diretório que contém o exemplo. Por exemplo:

    ```console
    cd ms-identity-python-webapp
    ```
1. Execute os comandos a seguir para instalar os pacotes necessários do PyPi e executar o aplicativo Web em seu computador local:

    ```console
    pip install -r requirements.txt
    flask run --host localhost --port 5000
    ```

    A janela do console exibe o número da porta do aplicativo executado localmente:

    ```console
     * Serving Flask app "app" (lazy loading)
     * Environment: production
       WARNING: This is a development server. Do not use it in a production deployment.
       Use a production WSGI server instead.
     * Debug mode: off
     * Running on http://localhost:5000/ (Press CTRL+C to quit)
    ```

1. Navegue até `http://localhost:5000` para ver o aplicativo Web em execução no computador local.

    :::image type="content" source="media/tutorial-web-app-python/python-flask-web-app-01.png" alt-text="Navegador da Web mostrando o aplicativo Web do Python Flask em execução localmente":::

### <a name="sign-up-using-an-email-address"></a>Criar conta usando um endereço de email

Este aplicativo de exemplo é compatível com inscrição, entrada e redefinição de senha. Neste tutorial, você se inscreve usando um endereço de email.

1. Selecione **Entrar** para iniciar o fluxo de usuário *B2C_1_signupsignin1* especificado em uma etapa anterior.
1. O Azure AD B2C apresenta uma página de entrada que inclui um link de inscrição. Como você ainda não tem uma conta, selecione o link **Inscrever-se agora**.
1. O fluxo de trabalho de inscrição apresenta uma página para coletar e verificar a identidade do usuário usando um endereço de email. O fluxo de trabalho de inscrição também coleta a senha do usuário e os atributos solicitados definidos no fluxo de usuário.

    Use um endereço de email válido e valide-o usando o código de verificação. Defina uma senha. Insira valores para os atributos necessários.

    :::image type="content" source="media/tutorial-web-app-python/python-flask-web-app-02.png" alt-text="Página de inscrição exibida pelo fluxo de usuário do Azure AD B2C":::

1. Selecione **Criar** para criar uma conta local no diretório do Azure AD B2C.

Quando você seleciona **Criar**, o aplicativo mostra o nome do usuário conectado.

:::image type="content" source="media/tutorial-web-app-python/python-flask-web-app-03.png" alt-text="Navegador da Web mostrando um aplicativo Web do Python Flask com o usuário conectado":::

Se você quiser testar a entrada, selecione o link **Sair** e, em seguida, selecione **Entrar** e entre com o endereço de email e a senha que você inseriu quando se inscreveu.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você configurou um aplicativo Web do Python Flask para trabalhar com um fluxo de usuário em seu locatário do Azure AD B2C para fornecer capacidade de inscrição e entrada. Você concluiu estas etapas:

> [!div class="checklist"]
> * Adicionou uma URL de resposta a um aplicativo registrado em seu locatário do Azure AD B2C
> * Baixou um exemplo de código do GitHub
> * Modificou o código do aplicativo de exemplo para funcionar com seu locatário
> * Inscreveu-se usando o fluxo de inscrição/entrada de usuário

Em seguida, saiba como personalizar a interface do usuário das páginas de fluxo de usuário exibidas para seus usuários pelo Azure AD B2C:

> [!div class="nextstepaction"]
> [Personalizar a interface das experiências do usuário no Azure AD B2C >](customize-ui.md)