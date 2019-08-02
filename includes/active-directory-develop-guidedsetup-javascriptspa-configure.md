---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 050bae64a62e90bdb74c93948109e255b69d7518
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133813"
---
## <a name="register-your-application"></a>Registre seu aplicativo

1. Entre no [Portal do Azure](https://portal.azure.com/).

1. Se sua conta fornecer acesso a mais de um locatário, selecione a conta na parte superior direita e defina sua sessão do portal para o locatário Azure Active Directory que deseja usar.
1. Vá até a página [Registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) da plataforma de identidade da Microsoft para desenvolvedores.
1. Quando a página **Registrar um aplicativo** aparecer, insira um nome para o seu aplicativo.
1. Em **Tipos de conta com suporte**, selecione **Contas em qualquer diretório organizacional e contas pessoais da Microsoft**.
1. Na seção **Redirect URI**, selecione a plataforma **Web** na lista suspensa e defina o valor para o URL do aplicativo com base em seu servidor da web. 

   Para obter informações sobre como definir e obter a URL de redirecionamento no Visual Studio e no Node.js, confira as duas próximas seções.

1. Selecione **Registrar**.
1. Na página **Visão geral** do aplicativo, anote o valor de **ID do aplicativo (cliente)** para uso posterior.
1. Este início rápido requer que o [fluxo de concessão implícita](../articles/active-directory/develop/v2-oauth2-implicit-grant-flow.md) seja ativado. No painel esquerdo do aplicativo registrado, selecione **Autenticação**.
1. Em **Configurações avançadas**, em **Concessão implícita**, marque as caixas de seleção **Tokens de ID** e **Tokens de Acesso**. Os tokens de ID e tokens de acesso são necessários porque esse aplicativo precisa conectar usuários e chamar uma API.
1. Clique em **Salvar**.

> #### <a name="set-a-redirect-url-for-nodejs"></a>Configurar a URL de redirecionamento para o Node.js
> Para o Node.js, você pode definir a porta do servidor Web no arquivo *server.js*. Este tutorial usa a porta 30662 para referência, mas você pode usar qualquer outra porta disponível. 
>
> Para configurar uma URL de redirecionamento nas informações de registro do aplicativo, volte para o painel **Registro de Aplicativo** e siga um destes procedimentos:
>
> - Defina *`http://localhost:30662/`* como a **URL de Redirecionamento**.
> - Se você estiver usando uma porta TCP personalizada, use *`http://localhost:<port>/`* (onde *\<porta>* é o número de porta TCP personalizada).
>
> #### <a name="set-a-redirect-url-for-visual-studio"></a>Configurar a URL de redirecionamento para o Visual Studio
> Para obter a URL de redirecionamento para o Visual Studio, faça o seguinte:
> 1. No **Gerenciador de Soluções**, selecione o projeto.
>
>    A janela **Propriedades** é aberta. Se não abrir, pressione **F4**.
>
>    ![A janela de propriedades do projeto JavaScriptSPA](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)
>
> 1. Copie o valor da **URL**.
 
> 1. Volte para o painel **Registro de Aplicativo** e cole o valor copiado como uma **URL de Redirecionamento**.

#### <a name="configure-your-javascript-spa"></a>Configurar o JavaScript SPA

1. No arquivo *index.html* criado durante a configuração do projeto, inclua as informações de registro do aplicativo. Na parte superior do arquivo, dentro das marcações `<script></script>`, adicione o seguinte código:

    ```javascript
    var msalConfig = {
        auth: {
            clientId: "<Enter_the_Application_Id_here>",
            authority: "https://login.microsoftonline.com/<Enter_the_Tenant_info_here>"
        },
        cache: {
            cacheLocation: "localStorage",
            storeAuthStateInCookie: true
        }
    };
    ```

    Em que:
    - *\<Insira_Informações_da_Conta_com_Suporte_Aqui>* é a **ID do Aplicativo (cliente)** para o aplicativo que você registrou.
    - *\<Insira_as_informações_de_Locatário_aqui>* é definido para uma das seguintes opções:
       - Se o seu aplicativo é compatível com *Contas neste diretório organizacional*, substitua esse valor pela **ID do locatário** ou pelo **Nome do locatário** (por exemplo, *Contoso.microsoft.com*).
       - Se o aplicativo for compatível com as *contas em qualquer diretório organizacional*, substitua esse valor por **organizações**.
       - Se o seu aplicativo for compatível com as *contas em qualquer diretório organizacional e contas pessoais da Microsoft*, substitua esse valor por **comum**. Para restringir o suporte a *contas pessoais da Microsoft*, substitua esse valor por **consumidores**.
