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
ms.date: 04/10/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: c4dc3d422e18d9ee41bf16ac3e6f22c3d7e466d7
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172098"
---
## <a name="register-your-application"></a>Registre seu aplicativo

Você pode registrar seu aplicativo de duas maneiras.

### <a name="option-1-express-mode"></a>Opção 1: Modo Expresso

Registre rapidamente seu aplicativo fazendo o seguinte:
1. Vá para o [portal do Azure – Registro de Aplicativo](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/WinDesktopQuickstartPage/sourceType/docs).
1. Insira um nome para seu aplicativo e selecione **Registrar**.
1. Siga as instruções para baixar e configurar automaticamente o novo aplicativo com apenas um clique.

### <a name="option-2-advanced-mode"></a>Opção 2: Modo Avançado

Para registrar seu aplicativo e adicionar suas informações de registro de aplicativo à sua solução, faça o seguinte:
1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. Se sua conta fornecer acesso a mais de um locatário, selecione sua conta no canto superior direito e defina sua sessão do portal para o locatário desejado do Azure AD.
1. Navegue até a página [Registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) da plataforma de identidade da Microsoft para desenvolvedores.
1. Selecione **Novo registro**.
   - Na seção **Nome**, insira um nome de aplicativo relevante que será exibido aos usuários do aplicativo, por exemplo, `Win-App-calling-MsGraph`.
   - Na seção **Tipos de conta com suporte**, selecione **Contas em qualquer diretório organizacional e contas pessoais da Microsoft (por exemplo, Skype, Xbox, Outlook.com)** .
   - Selecione **Registrar** para criar o aplicativo.
1. Na lista de páginas para o aplicativo, selecione **Autenticação**.
   1. Na seção **URIs de Redirecionamento** na lista de URIs de Redirecionamento:
   1. Na coluna **TIPO**, selecione **Cliente público (dispositivo móvel e desktop)** .
   1. Insira `urn:ietf:wg:oauth:2.0:oob` na coluna **URI DE REDIRECIONAMENTO**.
1. Clique em **Salvar**.
1. Vá para o Visual Studio, abra o arquivo *App.xaml.cs* e, depois, substitua `Enter_the_Application_Id_here` pela ID do aplicativo que você acabou de registrar e copiar.

    ```csharp
    private static string ClientId = "Enter_the_Application_Id_here";
    ```
