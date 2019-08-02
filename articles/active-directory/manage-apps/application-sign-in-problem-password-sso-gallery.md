---
title: Problema ao entrar no aplicativo da galeria do Azure AD configurado para SSO | Microsoft Docs
description: Como solucionar problemas com um aplicativo de galeria do Azure AD configurado para logon único com senha.
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: e9fd17d9e066be6a1abff5165436a09b8921184e
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68381299"
---
# <a name="sign-in-problems-with-an-azure-ad-gallery-app-configured-for-sso"></a>Problemas de conexão com um aplicativo da galeria do Azure AD configurado para SSO

O painel de acesso é um portal baseado na Web. Ele permite que os usuários que têm contas corporativas ou de estudante do Azure Active Directory (Azure AD) acessem aplicativos baseados em nuvem para os quais têm permissões. Os usuários que têm as edições do Azure AD também podem usar o grupo de autoatendimento e os recursos de gerenciamento de aplicativo por meio do painel de acesso.

O painel de acesso é separado da portal do Azure. Os usuários não precisam de uma assinatura do Azure para usar o painel de acesso.

Para usar o SSO (logon único) baseado em senha no painel de acesso, a extensão do painel de acesso deve ser instalada no navegador. A extensão é baixada automaticamente quando você seleciona um aplicativo configurado para SSO baseado em senha.

## <a name="browser-requirements-for-access-panel"></a>Requisitos de navegador para o painel de acesso

O painel de acesso requer um navegador com suporte para JavaScript e com CSS habilitado.

Os navegadores a seguir oferecem suporte ao SSO baseado em senha:

- Internet Explorer 8, 9, 10 e 11 no Windows 7 ou posterior

- Chrome no Windows 7 ou posterior ou no MacOS X ou posterior

- Firefox 26,0 ou posterior no Windows XP SP2 ou posterior ou no Mac OS X 10,6 ou posterior

>[!NOTE]
>A extensão de SSO baseada em senha torna-se disponível para o Microsoft Edge no Windows 10 quando o suporte para extensões de navegador foi adicionado ao Microsoft Edge.

## <a name="install-the-access-panel-browser-extension"></a>Instalar a extensão do navegador do painel de acesso

Siga estas etapas:

1. Abra o [painel de acesso](https://myapps.microsoft.com) em um navegador com suporte e entre como um usuário no Azure AD.

2. Selecione um aplicativo habilitado para SSO de senha no painel de acesso.

3. Quando for solicitado, selecione **instalar agora**.

4. Você será direcionado para um link de download baseado em seu navegador. Selecione **Adicionar** para instalar a extensão do navegador.

5. Se for solicitado, selecione **habilitar** ou **permitir**.

6. Após a instalação, reinicie o navegador.

7.  Entre no painel de acesso e veja se você pode iniciar seus aplicativos habilitados para SSO de senha.

Você também pode baixar diretamente as extensões do Chrome e do Firefox por meio destes links:

-   [Extensão do painel de acesso do Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Extensão do painel de acesso do Firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="set-up-a-group-policy-for-internet-explorer"></a>Configurar uma política de grupo para o Internet Explorer

Você pode configurar uma política de grupo que permite instalar remotamente a extensão do painel de acesso para o Internet Explorer nos computadores dos usuários.

Estes são os pré-requisitos:

-   [Active Directory Domain Services](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx) deve ser configurado e os computadores dos usuários devem ser ingressados em seu domínio.

-   Você tem a permissão "Editar configurações" para editar o objeto de Política de Grupo (GPO). Por padrão, os membros dos grupos de segurança a seguir têm essa permissão: Administradores de Domínio, Administradores da Empresa e Proprietários criadores de políticas de grupo. [Saiba mais](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx).

Para configurar a política de grupo e implantá-la para os usuários, consulte [como implantar a extensão do painel de acesso para o Internet Explorer usando a política de grupo](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-group-policy).

## <a name="troubleshoot-access-panel-in-internet-explorer"></a>Solucionar problemas do painel de acesso no Internet Explorer

Para acessar uma ferramenta de diagnóstico e instruções para configurar a extensão, consulte [solucionar problemas da extensão do painel de acesso para o Internet Explorer](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-troubleshooting).

## <a name="configure-password-sso-for-an-azure-ad-gallery-app"></a>Configurar o SSO de senha para um aplicativo da galeria do Azure AD

Para configurar um aplicativo na galeria do Azure AD, você precisa fazer o seguinte:

-   Adicionar o aplicativo da galeria do Azure AD
-   [Configurar o aplicativo para logon único com senha](#configure-the-app-for-password-sso)
-   [Atribuir usuários ao aplicativo](#assign-users-to-the-app)

### <a name="add-the-app-from-the-azure-ad-gallery"></a>Adicionar o aplicativo da galeria do Azure AD

Siga estas etapas:

1. Abra o [portal do Azure](https://portal.azure.com) e entre como um administrador global ou coadministrador.

2. Selecione **todos os serviços** na parte superior do painel de navegação no lado esquerdo para abrir a extensão do Azure AD.

3. Digite **Azure Active Directory** na caixa de pesquisa de filtro e, em seguida, selecione **Azure Active Directory**.

4. Selecione **aplicativos empresariais** no painel de navegação do Azure AD.

5. Selecione **Adicionar** no canto superior direito do painel **aplicativos empresariais** .

6. Na seção **Adicionar da Galeria** , digite o nome do aplicativo na caixa **Inserir um nome** .

7. Selecione o aplicativo que você deseja configurar para o SSO.

8. *Opcional:* Antes de adicionar o aplicativo, você pode alterar seu nome na caixa **nome** .

9. Clique em **Adicionar** para adicionar o aplicativo.

   Após um breve atraso, você poderá ver o painel de configuração do aplicativo.

### <a name="configure-the-app-for-password-sso"></a>Configurar o aplicativo para SSO de senha

Siga estas etapas:

1. Abra o [portal do Azure](https://portal.azure.com/) e entre como um administrador global ou coadministrador.

2. Selecione **todos os serviços** na parte superior do painel de navegação no lado esquerdo para abrir a extensão do Azure AD.

3. Digite **Azure Active Directory** na caixa de pesquisa de filtro e, em seguida, selecione **Azure Active Directory**.

4. Selecione **aplicativos empresariais** no painel de navegação do Azure AD.

5. Selecione **todos os aplicativos** para exibir uma lista de seus aplicativos.

   > [!NOTE]
   > Se você não vir o aplicativo desejado, use o controle de **filtro** na parte superior da **lista todos os aplicativos**. Defina a opção **Mostrar** como "todos os aplicativos".

6. Selecione o aplicativo que você deseja configurar para o SSO.

7. Depois que o aplicativo for carregado, selecione **logon único** no painel no lado esquerdo do aplicativo.

8. Selecione o modo de **logon baseado em senha** .

9. Atribua usuários ao aplicativo.

10. Você também pode fornecer credenciais para usuários. (Caso contrário, os usuários serão solicitados a inserir as credenciais na inicialização do aplicativo.) Para fazer isso, selecione as linhas dos usuários. Em seguida, selecione **Atualizar credenciais** e insira seus nomes de usuário e senhas.

### <a name="assign-users-to-the-app"></a>Atribuir usuários ao aplicativo

Para atribuir usuários diretamente a um aplicativo, siga estas etapas:

1. Abra o [portal do Azure](https://portal.azure.com/) e entre como um administrador global.

2. Selecione **todos os serviços** no problema de navegação no lado esquerdo para abrir a extensão do Azure AD.

3. Digite **Azure Active Directory** na caixa de pesquisa de filtro e, em seguida, selecione **Azure Active Directory**.

4. Selecione **aplicativos empresariais** no painel de navegação do Azure AD.

5. Selecione **todos os aplicativos** para exibir uma lista de seus aplicativos.

   > [!NOTE]
   > Se você não vir o aplicativo desejado, use o controle de **filtro** na parte superior da **lista todos os aplicativos**. Defina a opção **Mostrar** como "todos os aplicativos".

6. Na lista, selecione o aplicativo ao qual você deseja atribuir um usuário.

7. Depois que o aplicativo for carregado, selecione **usuários e grupos** no painel de navegação do aplicativo no lado esquerdo.

8. Selecione **Adicionar** na parte superior da lista **usuários e grupos** para abrir o painel **Adicionar atribuição** .

9. Selecione **usuários e grupos** no painel **Adicionar atribuição** .

10. Na caixa **Pesquisar por nome ou endereço de email** , digite o nome completo ou o endereço de email do usuário que você deseja atribuir.

11. Passe o mouse sobre o usuário na lista. Marque a caixa de seleção ao lado da foto ou do logotipo do perfil do usuário para adicionar esse usuário à lista **selecionada** .

12. *Opcional:* Para adicionar outro usuário, digite outro nome ou endereço de email na caixa **Pesquisar por nome ou endereço de email** e marque a caixa de seleção para adicionar esse usuário à lista **selecionada** .

13. Quando tiver terminado de selecionar os usuários, clique em **selecionar** para adicioná-los à lista de usuários e grupos que são atribuídos ao aplicativo.

14. *Opcional:* Clique em **selecionar função** no painel **Adicionar atribuição** para selecionar uma função a ser atribuída aos usuários que você selecionou.

15. Selecione **atribuir** para atribuir o aplicativo aos usuários selecionados.

    Após um breve atraso, os usuários poderão acessar esses aplicativos no painel de acesso.

## <a name="request-support"></a>Solicitar suporte 
Se você receber uma mensagem de erro ao configurar o SSO e atribuir usuários, abra um tíquete de suporte. Inclua o máximo possível das seguintes informações:

-   ID de erro de correlação
-   UPN (endereço de email de usuário)
-   TenantID
-   Tipo de navegador
-   Fuso horário e período de hora/hora em que o erro ocorreu
-   Rastreamentos do Fiddler

## <a name="next-steps"></a>Próximas etapas
[Fornecer logon único para seus aplicativos com Proxy de Aplicativo](application-proxy-configure-single-sign-on-with-kcd.md)
