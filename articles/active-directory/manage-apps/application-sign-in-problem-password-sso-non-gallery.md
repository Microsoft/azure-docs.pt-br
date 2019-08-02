---
title: SSO (logon único) baseado em senha no painel de acesso | Microsoft Docs
description: Discute áreas problemáticas que fornecem orientação para solucionar problemas relacionados à entrada em aplicativos da galeria do Azure AD configurados para logon único com senha.
services: active-directory
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
ms.openlocfilehash: 9ca192c28757df189e531aee0ba2d8da288ba7e6
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68381228"
---
# <a name="problems-signing-in-to-an-azure-ad-gallery-application-configured-for-password-single-sign-on"></a>Problemas ao entrar em um aplicativo na Galeria do Azure AD configurado para logon único com senha

O Painel de Acesso é um portal baseado na Web que permite a um usuário que tenha uma conta corporativa ou de estudante no Azure Active Directory (Azure AD) exibir e iniciar aplicativos baseados em nuvem para os quais o administrador do Azure AD concedeu acesso. Um usuário com as edições do Azure AD também pode usar os recursos de gerenciamento de grupo de autoatendimento e aplicativo por meio do Painel de Acesso. O Painel de Acesso é separado do Portal do Azure e não exige que os usuários tenham uma assinatura do Azure.

Para usar o SSO (logon único) baseado em senha no Painel de Acesso, a extensão do Painel de Acesso deve estar instalada no navegador do usuário. Essa extensão é baixada automaticamente quando um usuário seleciona um aplicativo configurado para SSO baseado em senha.

## <a name="meeting-browser-requirements-for-the-access-panel"></a>Atender aos requisitos de navegador para o Painel de Acesso

O Painel de Acesso exige um navegador com suporte para JavaScript e CSS habilitado. Para usar o SSO (logon único) baseado em senha no Painel de Acesso, a extensão do Painel de Acesso deve estar instalada no navegador do usuário. Essa extensão é baixada automaticamente quando um usuário seleciona um aplicativo configurado para SSO baseado em senha.

Para SSO baseado em senha, os navegadores do usuário final podem ser:

-   Internet Explorer 8, 9, 10, 11 - no Windows 7 ou posterior

-   Chrome – No Windows 7 ou posterior e no MacOS X ou posterior

-   Firefox 26.0 ou posterior, no Windows XP SP2 ou posterior e no Mac OS X 10.6 ou posterior

>[!NOTE]
>A extensão de SSO baseada em senha será disponibilizada para o Microsoft Edge no Windows 10 quando as extensões de navegador tiverem suporte no Microsoft Edge.
>
>

## <a name="how-to-install-the-access-panel-browser-extension"></a>Como instalar a extensão do Navegador do Painel de Acesso

Para instalar a extensão do Navegador do Painel de Acesso, siga as etapas a seguir:

1.  Abra o [Painel de Acesso](https://myapps.microsoft.com) em um dos navegadores compatíveis e entre como um **usuário** no Azure AD.

2.  Clique no **aplicativo de SSO com senha** no Painel de Acesso.

3.  No prompt solicitando a instalação do software, selecione **Instalar Agora**.

4.  Com base no seu navegador, você será direcionado para o link de download. **Adicione** a extensão ao seu navegador.

5.  Se o navegador solicitar, selecione como **Habilitar** ou **Permitir** a extensão.

6.  Quando estiver instalado, **reinicie** a sessão do navegador.

7.  Entrar no Painel de Acesso e verificar se é possível **iniciar** os aplicativos de SSO de senha

Também é possível baixar a extensão para Chrome e Firefox diretamente pelos links abaixo:

-   [Extensão do Painel de Acesso do Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Extensão do Painel de Acesso do Firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="setting-up-a-group-policy-for-internet-explorer"></a>Configurar uma política de grupo para o Internet Explorer

É possível configurar uma política de grupo que permita instalar remotamente a extensão do Painel de Acesso para o Internet Explorer nos computadores dos usuários.

Os pré-requisitos incluem:

-   Você configurou os [Serviços de Domínio do Active Directory](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx)e os computadores dos usuários ingressaram no domínio.

-   Você deve ter a permissão "Editar configurações" para editar o GPO (Objeto de Política de Grupo). Por padrão, os membros dos grupos de segurança a seguir têm essa permissão: Administradores de Domínio, Administradores da Empresa e Proprietários criadores de políticas de grupo. [Saiba mais](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx).

Siga o tutorial [Como Implantar a Extensão do Painel de Acesso para o Internet Explorer usando Política de Grupo](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-group-policy) para obter instruções passo a passo sobre como configurar política de grupo e implantá-la nos usuários.

## <a name="troubleshoot-the-access-panel-in-internet-explorer"></a>Solucionar problemas do Painel de Acesso no Internet Explorer

Siga o guia [Solucionar problemas da Extensão do Painel de Acesso para o Internet Explorer](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-troubleshooting) para acessar uma ferramenta de diagnóstico e as instruções passo a passo sobre como configurar a extensão para o IE.

## <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Como configurar o logon único com senha para um aplicativo inexistente na galeria

Para configurar um aplicativo da galeria do Azure AD será necessário:

-   [Adicionar um aplicativo inexistente na galeria](#add-a-non-gallery-application)

-   [Configurar o aplicativo para logon único com senha](#configure-the-application-for-password-single-sign-on)

-   [Atribuir usuários ao aplicativo](#assign-users-to-the-application)

### <a name="add-a-non-gallery-application"></a>Adicionar um aplicativo inexistente na galeria

Para adicionar um aplicativo da galeria do Azure AD, siga as etapas abaixo:

1.  Abra o [Portal do Azure](https://portal.azure.com) e entre como um **Administrador Global** ou **Coadministrador**

2.  Abra a **Extensão do Azure Active Directory** clicando em **Todos os serviços** na parte superior do menu de navegação esquerdo principal.

3.  Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4.  clique em **Aplicativos Empresariais** no menu de navegação esquerdo do Azure Active Directory.

5.  clique no botão **Adicionar** no canto superior direito do painel **Aplicativos Empresariais**.

6.  clique em **Aplicativo inexistente na galeria.**

7.  Insira o nome do aplicativo na caixa de texto **Nome**. Selecione **Adicionar.**

Após um curto período de tempo, você poderá ver o painel de configuração do aplicativo.

### <a name="configure-the-application-for-password-single-sign-on"></a>Configurar o aplicativo para logon único com senha

Para configurar o logon único para um aplicativo, siga as etapas abaixo:

1. Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como um **Administrador Global** ou **Coadministrador.**

2. Abra a **Extensão do Azure Active Directory** clicando em **Todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4. clique em **Aplicativos Empresariais** no menu de navegação esquerdo do Azure Active Directory.

5. clique em **Todos os Aplicativos** para exibir uma lista com todos os seus aplicativos.

   * Se não vir o aplicativo desejado, use o controle **Filtro** na parte superior da **Lista com Todos os Aplicativos** e defina a opção **Mostrar** como **Todos os Aplicativos.**

6. Selecione o aplicativo para o qual deseja configurar o logon único

7. Após o carregamento do aplicativo, clique em **Logon único** no menu de navegação esquerdo do aplicativo.

8. Selecione o modo **Logon baseado em senha.**

9. Insira a **URL de Logon**. Trata-se da URL em que os usuários inserem o nome de usuário e senha para entrar. Verifique se os campos de entrada estão visíveis na URL.

10. Atribuir usuários a um aplicativo.

11. Além disso, também é possível fornecer credenciais em nome do usuário selecionando as linhas dos usuários, clicando em **Atualizar Credenciais** e digitando o nome de usuário e a senha em nome dos usuários. Caso contrário, os usuários serão solicitados a inserir as próprias credenciais na inicialização.

### <a name="assign-users-to-the-application"></a>Atribuir usuários ao aplicativo

Para atribuir um ou mais usuários diretamente a um aplicativo, siga as etapas abaixo:

1. Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como um **Administrador Global.**

2. Abra a **Extensão do Azure Active Directory** clicando em **Todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4. clique em **Aplicativos Empresariais** no menu de navegação esquerdo do Azure Active Directory.

5. clique em **Todos os Aplicativos** para exibir uma lista com todos os seus aplicativos.

   * Se não vir o aplicativo desejado, use o controle **Filtro** na parte superior da **Lista com Todos os Aplicativos** e defina a opção **Mostrar** como **Todos os Aplicativos.**

6. Na lista, selecione o aplicativo ao qual deseja atribuir um usuário.

7. Após o carregamento do aplicativo, clique em **Usuários e Grupos** no menu de navegação esquerdo do aplicativo.

8. Clique no botão **Adicionar** na parte superior da lista **Usuários e Grupos** para abrir o painel **Adicionar Atribuição**.

9. Clique no seletor **Usuários e grupos** do painel **Adicionar Atribuição**.

10. Digite o **nome completo** ou o **endereço de email** do usuário que você deseja atribuir na caixa de pesquisa **Pesquisar por nome ou endereço de email**.

11. Passe o mouse sobre o **usuário** na lista para mostrar uma **caixa de seleção**. Clique na caixa de seleção ao lado do logotipo ou da foto de perfil do usuário para adicioná-lo à lista **Selecionado**.

12. **Opcional:** Caso queira **adicionar mais de um usuário**, digite outro **nome completo** ou **endereço de email** na caixa de pesquisa **Pesquisar por nome ou endereço de email** e clique na caixa de seleção para adicionar esse usuário à lista **Selecionado**.

13. Ao concluir a seleção dos usuários, clique no botão **Selecionar** para adicioná-los à lista de usuários e grupos a serem atribuídos ao aplicativo.

14. **Opcional:** clique no seletor **Selecionar Função** no painel **Adicionar Atribuição** para selecionar uma função que será atribuída aos usuários selecionados.

15. Clique no botão **Atribuir** para atribuir o aplicativo aos usuários selecionados.

Após um breve período, os usuários selecionados poderão iniciar esses aplicativos no Painel de Acesso.

## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>Se essas etapas de solução de problemas não resolverem o problema

Abra um tíquete de suporte com as informações a seguir, se estiverem disponíveis:

-   ID de erro de correlação

-   UPN (endereço de email de usuário)

-   TenantID

-   Tipo de navegador

-   Fuso horário e hora/cronograma durante o erro

-   Rastreamentos do Fiddler

## <a name="next-steps"></a>Próximas etapas
[Fornecer logon único para seus aplicativos com Proxy de Aplicativo](application-proxy-configure-single-sign-on-with-kcd.md)

