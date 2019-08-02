---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 2cdc6ea01e6c3555740102f319d0f4e8e4fc1c22
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172530"
---
## <a name="register-your-application"></a>Registre seu aplicativo

Para registrar seu aplicativo e adicionar suas informações de registro de aplicativo à sua solução, você tem duas opções:

### <a name="option-1-express-mode"></a>Opção 1: Modo Expresso

Registre rapidamente seu aplicativo fazendo o seguinte:

1. Acesse o novo painel do [portal do Azure – Registros de aplicativo](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs).
1. Insira um nome para o aplicativo e clique em **Registrar**.
1. Siga as instruções para baixar e configurar automaticamente o novo aplicativo com apenas um clique.

### <a name="option-2-advanced-mode"></a>Opção 2: Modo Avançado

Para registrar seu aplicativo e adicionar as informações de registro do aplicativo à solução manualmente, siga estas etapas:

1. Acesse o Visual Studio e:
   1. no Gerenciador de Soluções, selecione o projeto e examine a janela Propriedades (se uma janela Propriedades não for exibida, pressione F4).
   1. Altere habilitado para SSL `True`.
   1. Clique com botão direito no projeto no Visual Studio e, em seguida, escolha **propriedades**e o **Web** guia. No *servidores* seção alteração a *Url do projeto* para ser a URL de SSL.
   1. Copie a URL do SSL. Você adicionará essa URL à lista de URLs de Redirecionamento na lista de URLs de Redirecionamento do Portal de Registro na próxima etapa:<br/><br/>![Propriedades do projeto](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. Se sua conta fornecer acesso a mais de um locatário, selecione sua conta no canto superior direito e defina sua sessão do portal para o locatário desejado do Azure AD.
1. Navegue até a página [Registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) da plataforma de identidade da Microsoft para desenvolvedores.
1. Selecione **Novo registro**.
1. Quando a página **Registrar um aplicativo** for exibida, insira as informações de registro do aplicativo:
   1. Na seção **Nome**, insira um nome de aplicativo relevante que será exibido aos usuários do aplicativo, por exemplo, `ASPNET-Tutorial`.
   1. Adicione a URL do SSL copiada do Visual Studio na etapa 1 (por exemplo, `https://localhost:44368/`) à **URL de Resposta** e clique em **Registrar**.
1. Selecione o menu **Autenticação** menu, defina **Tokens de ID** em **Concessão Implícita**e selecione **Salvar**.
1. Adicione o seguinte em `web.config` localizado na pasta raiz, na seção `configuration\appSettings`:

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="redirectUri" value="Enter_the_Redirect_URL_here" />
    <add key="Tenant" value="common" />
    <add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" />
    ```

1. Substitua `ClientId` pelo ID do aplicativo que você acabou de registrar.
1. Substitua `redirectUri` com a URL do SSL de seu projeto.
